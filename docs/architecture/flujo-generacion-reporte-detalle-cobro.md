# Ecosistema Vida Grupo - Flujo: Generación de Reporte de Detalle de Cobro 🔄

## 📋 **Introducción**

### Descripción del Flujo

El flujo de **Generación de Reporte de Detalle de Cobro** es un proceso crítico que genera reportes detallados con información granular de cada asegurado dentro de una factura colectiva de pólizas de Vida Grupo. Este flujo orquesta la construcción asíncrona de archivos masivos que pueden contener desde miles hasta millones de registros, utilizando un patrón de work queues programadas para procesar grandes volúmenes de datos de forma eficiente sin impactar el rendimiento del sistema.

El proceso inicia cuando una aplicación externa (o BillingCenter) solicita la generación del reporte mediante una llamada REST. El sistema registra la solicitud y delega el procesamiento real a 4 work queues especializadas que ejecutan de forma programada y distribuida: consulta de datos de Guidewire, construcción de registros del detalle, envío a Azure Massive Download API, y notificación vía RabbitMQ a aplicaciones consumidoras.

### Scope del Documento

**Enfoque Principal**: Documentación técnica completa del flujo de trabajo end-to-end de generación de reportes de detalle de cobro  
**Audiencia**: Desarrolladores, Arquitectos, Analistas de Negocio, Equipos de Soporte  
**Última Actualización**: Octubre 28, 2025

### Componentes Involucrados

| Componente                        | Tecnología                    | Puerto/Contexto                              | Responsabilidad                                                    |
| --------------------------------- | ----------------------------- | -------------------------------------------- | ------------------------------------------------------------------ |
| **Aplicación Cliente**            | REST Client                   | Aplicaciones externas / BillingCenter        | Solicitar generación y consultar estado de reportes               |
| **MicroIntegradorReportesVG**     | Java 17 + Apache Camel 3.20   | `:9000` - `/v1/he/invoices/.../chargedetail` | Orquestar generación asíncrona mediante work queues programadas    |
| **WorkQueue 1**                   | Quartz Scheduler + Camel      | Cron: `0 0/10 * * * ?` (cada 10 min)         | Consultar datos de BillingCenter y PolicyCenter                    |
| **WorkQueue 2**                   | Quartz Scheduler + Camel      | Cron: `0 0/2 * * * ?` (cada 2 min)           | Construir registros de detalle de cobro y persistir                |
| **WorkQueue 3**                   | Quartz Scheduler + Camel      | Cron: `0 0/5 * * * ?` (cada 5 min)           | Enviar bloques a Azure, cerrar archivo, notificar vía RabbitMQ     |
| **WorkQueue 4**                   | Quartz Scheduler + Camel      | Cron: `0 0 0 1/1 * ? *` (diario a medianoche)| Limpiar registros antiguos de tablas de control                    |
| **Oracle DB - Control**           | Oracle Database               | Esquema: `ADM_VIDAGRUPOREPORTES`             | Persistir estado de generación y registros de detalle              |
| **Oracle DB - Guidewire**         | Oracle Database               | Esquemas: `BC` (BillingCenter), `PC` (Policy)| Proveer datos de facturas, pólizas y asegurados                    |
| **Azure Massive Download API**    | Azure PaaS                    | `https://...suramericana.com/massive-download` | Gestionar construcción y almacenamiento de archivos masivos       |
| **RabbitMQ**                      | RabbitMQ 3.x                  | `msglab.suramericana.com.co:5672`            | Mensajería asíncrona para notificar cambios de estado              |

---

## 🔄 **Diagramas de Secuencia**

### 1. Flujo Principal: Solicitud de Generación de Reporte (POST)

```mermaid
sequenceDiagram
    participant Cliente as Aplicación Cliente<br/>(REST Client)
    participant API as RestApiRoute<br/>(Netty HTTP :9000)
    participant Gen as GenerateDetailChargeProcessor<br/>(Camel Processor)
    participant DB_CTL as Oracle DB Control<br/>(ADM_VIDAGRUPOREPORTES)

    Note over Cliente,DB_CTL: FASE 1: SOLICITUD DE GENERACIÓN

    Cliente->>API: POST /v1/he/invoices/{invoiceNumber}/chargedetail/report
    activate API
    Note right of Cliente: Headers:<br/>- autorizacion: password

    API->>Gen: procesarSolicitudGeneracion(invoiceNumber)
    activate Gen

    Gen->>Gen: validarInvoiceNumber(invoiceNumber)
    
    alt InvoiceNumber válido
        Gen->>DB_CTL: INSERT INTO CHARGE_DETAIL_PRINCIPAL<br/>(invoice_number, status=1, created_date)
        activate DB_CTL
        DB_CTL-->>Gen: Registro creado exitosamente
        deactivate DB_CTL
        
        Gen-->>API: ResponseEntity(200 OK)
        deactivate Gen
        API-->>Cliente: HTTP 200 OK<br/>"Solicitud registrada"
        deactivate API
        
        Note over Cliente,DB_CTL: El procesamiento continuará<br/>de forma asíncrona mediante<br/>Work Queues programadas

    else InvoiceNumber inválido o error
        Gen->>Gen: manejarError(exception)
        Gen-->>API: ResponseEntity(500 Internal Error)
        API-->>Cliente: HTTP 500 Internal Server Error
    end
```

### 2. Flujo Asíncrono: WorkQueue 1 - Consulta de Datos de Guidewire

```mermaid
sequenceDiagram
    participant Scheduler as Quartz Scheduler<br/>(Cron: cada 10 min)
    participant WQ1 as WorkQueue1Route<br/>(Camel Route)
    participant Proc1 as WorkQueue1Processors<br/>(Lógica de negocio)
    participant DB_CTL as Oracle DB Control<br/>(Tablas de control)
    participant DB_BC as Oracle DB BillingCenter<br/>(Esquema BC)
    participant DB_PC as Oracle DB PolicyCenter<br/>(Esquema PC)

    Note over Scheduler,DB_PC: FASE 2: CONSULTA DE DATOS (WORKQUEUE 1)

    Scheduler->>WQ1: Trigger scheduled execution<br/>(cron: 0 0/10 * * * ?)
    activate WQ1

    WQ1->>DB_CTL: SELECT * FROM CHARGE_DETAIL_PRINCIPAL<br/>WHERE status = 1<br/>LIMIT 1
    activate DB_CTL
    DB_CTL-->>WQ1: Registro con invoice_number
    deactivate DB_CTL

    alt Registro encontrado con status=1
        WQ1->>Proc1: procesarConsultaDatos(invoice_number)
        activate Proc1

        Proc1->>DB_CTL: UPDATE CHARGE_DETAIL_PRINCIPAL<br/>SET status = 2 (PROCESANDO)
        activate DB_CTL
        DB_CTL-->>Proc1: Actualización exitosa
        deactivate DB_CTL

        Note over Proc1,DB_PC: Consultar datos de BillingCenter

        Proc1->>DB_BC: SELECT bc_invoice.*, bc_invoiceitem.*,<br/>bc_policyperiod.*, bc_accountlocation.*<br/>FROM bc_invoice<br/>WHERE invoice_number = :invoiceNumber
        activate DB_BC
        DB_BC-->>Proc1: Datos de factura colectiva
        deactivate DB_BC

        Note over Proc1,DB_PC: Consultar datos de PolicyCenter

        Proc1->>DB_PC: SELECT pc_policy.*, pc_policyline.*,<br/>pc_grouppersonnel.*, pc_coverage.*<br/>FROM pc_policy p<br/>JOIN pc_policyperiod pp ON...<br/>WHERE policy_number IN (...)
        activate DB_PC
        DB_PC-->>Proc1: Datos de pólizas y asegurados
        deactivate DB_PC

        Proc1->>Proc1: consolidarDatosGuidewire()

        Proc1->>DB_CTL: UPDATE CHARGE_DETAIL_PRINCIPAL<br/>SET status = 3 (DATOS CONSULTADOS),<br/>data_json = :consolidatedData
        activate DB_CTL
        DB_CTL-->>Proc1: Datos persistidos
        deactivate DB_CTL

        Proc1-->>WQ1: Procesamiento completado
        deactivate Proc1

    else No hay registros con status=1
        WQ1->>WQ1: Log: "No hay solicitudes pendientes"
    end

    deactivate WQ1

    Note over Scheduler,DB_PC: WorkQueue 1 finalizó.<br/>WorkQueue 2 procesará en su próxima ejecución
```

### 3. Flujo Asíncrono: WorkQueue 2 - Construcción de Registros de Detalle

```mermaid
sequenceDiagram
    participant Scheduler as Quartz Scheduler<br/>(Cron: cada 2 min)
    participant WQ2 as WorkQueue2Route<br/>(Camel Route)
    participant Proc2 as WorkQueue2Processors<br/>(Construcción registros)
    participant Provider as CoverageProvider<br/>(Reglas de coberturas)
    participant DB_CTL as Oracle DB Control<br/>(Tablas de control)

    Note over Scheduler,DB_CTL: FASE 3: CONSTRUCCIÓN DE REGISTROS (WORKQUEUE 2)

    Scheduler->>WQ2: Trigger scheduled execution<br/>(cron: 0 0/2 * * * ?)
    activate WQ2

    WQ2->>DB_CTL: SELECT * FROM CHARGE_DETAIL_PRINCIPAL<br/>WHERE status = 3<br/>LIMIT 1
    activate DB_CTL
    DB_CTL-->>WQ2: Registro con datos consolidados
    deactivate DB_CTL

    alt Registro encontrado con status=3
        WQ2->>Proc2: construirRegistrosDetalle(invoice_number)
        activate Proc2

        Proc2->>DB_CTL: UPDATE CHARGE_DETAIL_PRINCIPAL<br/>SET status = 4 (CONSTRUYENDO REGISTROS)
        activate DB_CTL
        DB_CTL-->>Proc2: Actualización exitosa
        deactivate DB_CTL

        Proc2->>Proc2: parsearDatosConsolidados(data_json)

        loop Para cada asegurado en la póliza colectiva
            Proc2->>Provider: obtenerReglasCobertura(product_code, coverage_type)
            activate Provider
            Provider-->>Proc2: Reglas de cobertura aplicables
            deactivate Provider

            Proc2->>Proc2: aplicarReglasNegocio(asegurado, coberturas)
            Proc2->>Proc2: calcularValoresDetalle(primas, coberturas)

            Note over Proc2,DB_CTL: Construir registro de detalle<br/>con ~50 campos por asegurado

            Proc2->>Proc2: construirRegistroDetalle(asegurado, valores)
        end

        Note over Proc2,DB_CTL: Persistir lote de registros<br/>usando INSERT-SELECT optimizado

        Proc2->>DB_CTL: INSERT INTO CHARGE_DETAIL_ITEMS<br/>(invoice_number, insured_id, coverage_data, ...)<br/>VALUES (batch insert - 2,000 a 10,000 registros)
        activate DB_CTL
        DB_CTL-->>Proc2: Registros insertados: {count}
        deactivate DB_CTL

        Proc2->>DB_CTL: UPDATE CHARGE_DETAIL_PRINCIPAL<br/>SET status = 5 (REGISTROS CONSTRUIDOS),<br/>total_records = :count
        activate DB_CTL
        DB_CTL-->>Proc2: Actualización exitosa
        deactivate DB_CTL

        Proc2-->>WQ2: Construcción completada
        deactivate Proc2

    else No hay registros con status=3
        WQ2->>WQ2: Log: "No hay datos listos para construcción"
    end

    deactivate WQ2

    Note over Scheduler,DB_CTL: WorkQueue 2 finalizó.<br/>WorkQueue 3 enviará a Azure en su próxima ejecución
```

### 4. Flujo Asíncrono: WorkQueue 3 - Envío a Azure y Notificación RabbitMQ

```mermaid
sequenceDiagram
    participant Scheduler as Quartz Scheduler<br/>(Cron: cada 5 min)
    participant WQ3 as WorkQueue3Route<br/>(Camel Route)
    participant Proc3 as WorkQueue3Processors<br/>(Envío Azure + RabbitMQ)
    participant DB_CTL as Oracle DB Control<br/>(Tablas de control)
    participant Azure as Azure Massive Download API<br/>(REST API)
    participant MQ as RabbitMQ<br/>(Exchange + Queue)

    Note over Scheduler,MQ: FASE 4: ENVÍO A AZURE Y NOTIFICACIÓN (WORKQUEUE 3)

    Scheduler->>WQ3: Trigger scheduled execution<br/>(cron: 0 0/5 * * * ?)
    activate WQ3

    WQ3->>DB_CTL: SELECT * FROM CHARGE_DETAIL_PRINCIPAL<br/>WHERE status = 5<br/>LIMIT 1
    activate DB_CTL
    DB_CTL-->>WQ3: Registro listo para envío
    deactivate DB_CTL

    alt Registro encontrado con status=5
        WQ3->>Proc3: enviarAzureYNotificar(invoice_number)
        activate Proc3

        Proc3->>DB_CTL: UPDATE CHARGE_DETAIL_PRINCIPAL<br/>SET status = 6 (ENVIANDO A AZURE)
        activate DB_CTL
        DB_CTL-->>Proc3: Actualización exitosa
        deactivate DB_CTL

        Note over Proc3,Azure: PASO 1: Enviar cabecera del archivo

        Proc3->>Azure: POST /massive-download/api/v1/files/header<br/>Body: { fileName, totalRecords, ... }
        activate Azure
        Azure-->>Proc3: HTTP 200 OK { fileId: "ABC123" }
        deactivate Azure

        Note over Proc3,Azure: PASO 2: Enviar bloques de datos<br/>(chunks de 5,000 registros)

        loop Para cada bloque de registros (paginado)
            Proc3->>DB_CTL: SELECT * FROM CHARGE_DETAIL_ITEMS<br/>WHERE invoice_number = :invoiceNumber<br/>LIMIT 5000 OFFSET :offset
            activate DB_CTL
            DB_CTL-->>Proc3: Bloque de 5,000 registros
            deactivate DB_CTL

            Proc3->>Proc3: formatearRegistrosCSV(registros)

            Proc3->>Azure: POST /massive-download/api/v1/files/{fileId}/blocks<br/>Body: { blockData: "CSV content", blockNumber }
            activate Azure
            Azure-->>Proc3: HTTP 200 OK { blockId }
            deactivate Azure
        end

        Note over Proc3,Azure: PASO 3: Cerrar archivo

        Proc3->>Azure: POST /massive-download/api/v1/files/{fileId}/close<br/>Body: { totalBlocks }
        activate Azure
        Azure-->>Proc3: HTTP 200 OK { downloadUrl }
        deactivate Azure

        Proc3->>DB_CTL: UPDATE CHARGE_DETAIL_PRINCIPAL<br/>SET status = 7 (ARCHIVO DISPONIBLE),<br/>download_url = :downloadUrl,<br/>azure_file_id = :fileId
        activate DB_CTL
        DB_CTL-->>Proc3: Actualización exitosa
        deactivate DB_CTL

        Note over Proc3,MQ: PASO 4: Notificar vía RabbitMQ

        Proc3->>Proc3: construirEventoRabbitMQ(invoice_number, status, url)

        Proc3->>MQ: Publish to Exchange<br/>Exchange: sura.seguros.vidagrupo.chargedetail.ex<br/>Routing Key: sura.seguros.vidagrupo.chargedetail.risk<br/>Payload: { invoiceNumber, status: 7, downloadUrl }
        activate MQ
        MQ-->>Proc3: Message published successfully
        deactivate MQ

        Proc3-->>WQ3: Envío y notificación completados
        deactivate Proc3

    else No hay registros con status=5
        WQ3->>WQ3: Log: "No hay registros listos para Azure"
    end

    deactivate WQ3

    Note over Scheduler,MQ: WorkQueue 3 finalizó.<br/>El reporte está disponible para descarga
```

### 5. Flujo de Consulta y Descarga de Reporte (GET)

```mermaid
sequenceDiagram
    participant Cliente as Aplicación Cliente<br/>(REST Client)
    participant API as RestApiRoute<br/>(Netty HTTP :9000)
    participant Get as GetDetailChargeProcessor<br/>(Camel Processor)
    participant DB_CTL as Oracle DB Control<br/>(ADM_VIDAGRUPOREPORTES)
    participant Azure as Azure Massive Download API<br/>(REST API)

    Note over Cliente,Azure: FLUJO DE CONSULTA Y DESCARGA

    Cliente->>API: GET /v1/he/invoices/{invoiceNumber}/chargedetail/report
    activate API
    Note right of Cliente: Headers:<br/>- autorizacion: password

    API->>Get: consultarYDescargarReporte(invoiceNumber)
    activate Get

    Get->>DB_CTL: SELECT * FROM CHARGE_DETAIL_PRINCIPAL<br/>WHERE invoice_number = :invoiceNumber
    activate DB_CTL
    DB_CTL-->>Get: Registro de reporte
    deactivate DB_CTL

    alt Reporte disponible (status = 7)
        Get->>Get: extraerDownloadUrl(registro)

        Get->>Azure: GET {downloadUrl}
        activate Azure
        Azure-->>Get: HTTP 200 OK<br/>Body: archivo CSV (stream)
        deactivate Azure

        Get-->>API: ResponseEntity(200 OK, fileStream)
        deactivate Get
        API-->>Cliente: HTTP 200 OK<br/>Content-Type: text/csv<br/>Body: archivo de reporte
        deactivate API

    else Reporte en procesamiento (status < 7)
        Get-->>API: ResponseEntity(404 Not Found)<br/>Message: "Reporte en proceso"
        API-->>Cliente: HTTP 404 Not Found<br/>"El reporte aún se está generando"

    else Reporte no encontrado
        Get-->>API: ResponseEntity(404 Not Found)<br/>Message: "Reporte no existe"
        API-->>Cliente: HTTP 404 Not Found<br/>"No existe solicitud para esta factura"

    else Error en Azure o BD
        Get->>Get: manejarError(exception)
        Get-->>API: ResponseEntity(500 Internal Error)
        API-->>Cliente: HTTP 500 Internal Server Error
    end
```

### 6. Flujo de Manejo de Errores

```mermaid
sequenceDiagram
    participant WQ as WorkQueue Route<br/>(Cualquier WQ 1-3)
    participant Proc as WorkQueue Processor<br/>(Lógica de negocio)
    participant DB_CTL as Oracle DB Control<br/>(Tablas de control)
    participant Common as CommonRoute<br/>(Error Handler)
    participant Log as Splunk Logging<br/>(Observabilidad)

    Note over WQ,Log: ESCENARIO: ERROR EN PROCESAMIENTO

    WQ->>Proc: procesarWorkQueue(invoice_number)
    activate Proc

    Proc->>DB_CTL: ejecutarOperacionBD()
    activate DB_CTL

    alt Error de Conexión a BD
        DB_CTL-->>Proc: SQLException: Connection timeout
        deactivate DB_CTL

        Proc->>Proc: capturarExcepcion(SQLException)
        Proc->>DB_CTL: UPDATE CHARGE_DETAIL_PRINCIPAL<br/>SET status = -1 (ERROR),<br/>error_message = :errorMsg
        activate DB_CTL
        DB_CTL-->>Proc: Actualización exitosa
        deactivate DB_CTL

        Proc->>Common: onException(SQLException)
        activate Common
        Common->>Log: logError(SQLException, context)
        activate Log
        Log-->>Common: Log enviado a Splunk
        deactivate Log
        Common-->>Proc: Error manejado
        deactivate Common

        Proc-->>WQ: Error manejado, continuar
        deactivate Proc

    else Error de Azure API
        Proc->>Azure: POST /massive-download/api/v1/files/header
        activate Azure
        Azure-->>Proc: HTTP 500 Internal Server Error
        deactivate Azure

        Proc->>Proc: implementarReintento()

        alt Reintento Exitoso (max 3 reintentos)
            Proc->>Azure: POST /massive-download/api/v1/files/header<br/>(Intento 2)
            activate Azure
            Azure-->>Proc: HTTP 200 OK
            deactivate Azure

            Proc->>DB_CTL: UPDATE CHARGE_DETAIL_PRINCIPAL<br/>SET status = 6 (continuar procesamiento)
            Proc-->>WQ: Procesamiento exitoso tras reintento

        else Reintento Fallido
            Proc->>DB_CTL: UPDATE CHARGE_DETAIL_PRINCIPAL<br/>SET status = -2 (ERROR AZURE),<br/>error_message = "Azure API unavailable after 3 retries"
            activate DB_CTL
            DB_CTL-->>Proc: Error registrado
            deactivate DB_CTL

            Proc->>Log: logCriticalError("Azure API unreachable", invoice_number)
            activate Log
            Log-->>Proc: Alerta enviada
            deactivate Log

            Proc-->>WQ: Error crítico registrado
        end

    else Timeout en Procesamiento
        Note over Proc,DB_CTL: Timeout después de 30 minutos<br/>de procesamiento continuo

        Proc->>Proc: detectarTimeout()
        Proc->>DB_CTL: UPDATE CHARGE_DETAIL_PRINCIPAL<br/>SET status = -3 (TIMEOUT),<br/>error_message = "Procesamiento excedió 30 min"
        activate DB_CTL
        DB_CTL-->>Proc: Timeout registrado
        deactivate DB_CTL

        Proc->>Log: logWarning("Timeout en procesamiento", invoice_number)
        activate Log
        Log-->>Proc: Warning registrado
        deactivate Log

        Note over WQ,Log: Requiere intervención manual<br/>o ajuste de configuración

        Proc-->>WQ: Timeout manejado
    end
```

---

## 📊 **Estados y Transiciones**

### Diagrama de Estados del Flujo

```mermaid
stateDiagram-v2
    [*] --> Solicitado: POST /chargedetail/report
    Solicitado --> Procesando: WorkQueue 1 inicia
    Procesando --> DatosConsultados: Consulta BD exitosa
    DatosConsultados --> ConstruyendoRegistros: WorkQueue 2 inicia
    ConstruyendoRegistros --> RegistrosConstruidos: Inserción exitosa
    RegistrosConstruidos --> EnviandoAzure: WorkQueue 3 inicia
    EnviandoAzure --> ArchivoDisponible: Azure API responde OK
    ArchivoDisponible --> [*]: GET /chargedetail/report

    Solicitado --> Error: Validación falla
    Procesando --> Error: SQLException
    DatosConsultados --> Error: Datos inconsistentes
    ConstruyendoRegistros --> Error: BD timeout
    RegistrosConstruidos --> ErrorAzure: Azure API falla
    EnviandoAzure --> ErrorAzure: Azure API timeout

    Error --> Reintentando: Usuario reintenta
    ErrorAzure --> Reintentando: Reintento automático (max 3)
    Reintentando --> Procesando: Reinicia desde último estado válido

    Error --> [*]: Usuario cancela
    ErrorAzure --> [*]: Excedió reintentos

    note right of Solicitado
        Status: 1
        Estado inicial tras POST
    end note

    note right of Procesando
        Status: 2
        WorkQueue 1 consultando BD
    end note

    note right of DatosConsultados
        Status: 3
        Datos consolidados en data_json
    end note

    note right of ConstruyendoRegistros
        Status: 4
        WorkQueue 2 construyendo registros
    end note

    note right of RegistrosConstruidos
        Status: 5
        Registros en CHARGE_DETAIL_ITEMS
    end note

    note right of EnviandoAzure
        Status: 6
        WorkQueue 3 enviando a Azure
    end note

    note right of ArchivoDisponible
        Status: 7
        Archivo listo para descarga
    end note

    note right of Error
        Status: -1
        Error general (BD, validación)
    end note

    note right of ErrorAzure
        Status: -2
        Error específico de Azure API
    end note
```

### Tabla de Estados

| Estado                    | Código | Descripción                                              | WorkQueue Responsable | Siguiente Estado Esperado   |
| ------------------------- | ------ | -------------------------------------------------------- | --------------------- | --------------------------- |
| **Solicitado**            | 1      | Solicitud registrada, pendiente de procesamiento         | -                     | Procesando (2)              |
| **Procesando**            | 2      | Consultando datos de BillingCenter y PolicyCenter        | WorkQueue 1           | DatosConsultados (3)        |
| **DatosConsultados**      | 3      | Datos consolidados persistidos en data_json              | WorkQueue 1           | ConstruyendoRegistros (4)   |
| **ConstruyendoRegistros** | 4      | Construyendo registros de detalle de cobro               | WorkQueue 2           | RegistrosConstruidos (5)    |
| **RegistrosConstruidos**  | 5      | Registros insertados en CHARGE_DETAIL_ITEMS              | WorkQueue 2           | EnviandoAzure (6)           |
| **EnviandoAzure**         | 6      | Enviando bloques y cerrando archivo en Azure             | WorkQueue 3           | ArchivoDisponible (7)       |
| **ArchivoDisponible**     | 7      | Archivo listo para descarga, notificación enviada        | WorkQueue 3           | Final (descarga exitosa)    |
| **Error**                 | -1     | Error general (BD, validación, timeout)                  | Cualquiera            | Reintento manual            |
| **ErrorAzure**            | -2     | Error específico de Azure API (tras reintentos)          | WorkQueue 3           | Reintento manual/escalación |
| **Timeout**               | -3     | Procesamiento excedió tiempo máximo (30 min)             | Cualquiera            | Revisión manual             |

---

## 📋 **Configuración y Parámetros**

### Configuración del Flujo

| Parámetro                              | Valor                   | Descripción                                           | Impacto si se Cambia                                          |
| -------------------------------------- | ----------------------- | ----------------------------------------------------- | ------------------------------------------------------------- |
| `workqueue1.cron`                      | `0 0/10 * * * ?`        | Frecuencia de ejecución de WorkQueue 1 (cada 10 min) | Mayor frecuencia aumenta carga de BD Guidewire                |
| `workqueue2.cron`                      | `0 0/2 * * * ?`         | Frecuencia de ejecución de WorkQueue 2 (cada 2 min)  | Mayor frecuencia aumenta carga de BD de control               |
| `workqueue3.cron`                      | `0 0/5 * * * ?`         | Frecuencia de ejecución de WorkQueue 3 (cada 5 min)  | Mayor frecuencia aumenta llamadas a Azure API                 |
| `workqueue4.cron`                      | `0 0 0 1/1 * ? *`       | Frecuencia de limpieza (diario a medianoche)         | Cambiar para ajustar retención de registros históricos        |
| `batch.size.insert`                    | `2000 - 10000`          | Tamaño de lote para inserción de registros           | Muy alto puede causar OutOfMemory, muy bajo es ineficiente    |
| `azure.block.size`                     | `5000`                  | Registros por bloque enviado a Azure                 | Bloques muy grandes pueden causar timeout en Azure API        |
| `azure.api.timeout`                    | `60000 ms`              | Timeout para llamadas a Azure Massive Download       | Reducir puede causar falsos errores en archivos muy grandes   |
| `processing.timeout`                   | `30 minutos`            | Tiempo máximo de procesamiento por WorkQueue         | Ajustar según volumen de registros esperado                   |
| `retry.max.attempts`                   | `3`                     | Número máximo de reintentos ante error de Azure      | Aumentar puede incrementar latencia total en caso de falla    |
| `oracle.jdbc.maxTotal`                 | `10`                    | Máximo de conexiones en pool de BD                   | Aumentar si hay contención de conexiones                      |
| `oracle.jdbc.maxConnLifetimeMillis`    | `18300000 ms` (5.08 hr) | Tiempo de vida máximo de conexión                    | Reducir si hay problemas de conexiones stale                  |

### Message Queues Utilizadas

| Cola                                              | Exchange                                      | Routing Key                                     | TTL      | Propósito                                                      |
| ------------------------------------------------- | --------------------------------------------- | ----------------------------------------------- | -------- | -------------------------------------------------------------- |
| `sura.seguros.vidagrupo.chargedetail.risk.qu`     | `sura.seguros.vidagrupo.chargedetail.ex`      | `sura.seguros.vidagrupo.chargedetail.risk`      | No limit | Publicar eventos de cambio de estado (status 7) a consumidores |
| `sura.seguros.vidagrupo.chargedetail.risk.reply.qu` | `sura.seguros.vidagrupo.chargedetail.ex`    | `sura.seguros.vidagrupo.chargedetail.risk.reply`| No limit | Recibir confirmaciones de aplicaciones consumidoras            |

**Configuración de Virtual Host:**
- **Virtual Host**: `seguros.vh`
- **Exchange Type**: `topic` (permite routing patterns)
- **Durable**: `true` (mensajes persisten tras restart de RabbitMQ)
- **Auto-delete**: `false`

---

## 🔧 **Métricas y Monitoreo**

### Puntos Críticos de Medición

| Métrica                                  | Componente                 | Umbral Esperado          | Acción si se Excede                                        |
| ---------------------------------------- | -------------------------- | ------------------------ | ---------------------------------------------------------- |
| **Tiempo total de generación**          | End-to-end                 | < 30 minutos             | Revisar volumen de registros, optimizar consultas SQL      |
| **Tiempo de consulta BD Guidewire**      | WorkQueue 1                | < 5 minutos              | Optimizar índices en BC/PC, revisar plan de ejecución SQL  |
| **Throughput de inserción**              | WorkQueue 2                | > 5000 registros/min     | Aumentar batch size, revisar contención de BD              |
| **Latencia de Azure API**                | WorkQueue 3                | < 10 segundos por bloque | Contactar soporte Azure, verificar conectividad de red     |
| **Tasa de errores Azure**                | WorkQueue 3                | < 1% de bloques          | Revisar logs, escalar con soporte Azure                    |
| **Mensajes pendientes en RabbitMQ**      | Cola risk.qu               | < 100 mensajes           | Verificar que consumidores estén activos                   |
| **Conexiones activas de BD**             | Pool de conexiones Oracle  | < 8 (de 10 max)          | Revisar leaks de conexiones, aumentar maxTotal             |
| **Uso de memoria JVM**                   | MicroIntegradorReportesVG  | < 70% de heap            | Aumentar Xmx, revisar garbage collection, optimizar código |

### Logs Críticos a Monitorear

| Componente                | Archivo Log / Destino        | Patrón a Buscar                               | Severidad |
| ------------------------- | ---------------------------- | --------------------------------------------- | --------- |
| **WorkQueue 1**           | Splunk (index=vida_grupo)    | `ERROR.*SQLException.*WorkQueue1`             | ERROR     |
| **WorkQueue 2**           | Splunk (index=vida_grupo)    | `ERROR.*OutOfMemoryError.*WorkQueue2`         | CRITICAL  |
| **WorkQueue 3**           | Splunk (index=vida_grupo)    | `ERROR.*Azure.*5\d{2}` (Azure API 5xx)        | ERROR     |
| **Azure Integration**     | Splunk (index=vida_grupo)    | `WARN.*retry attempt.*Azure`                  | WARN      |
| **RabbitMQ Publisher**    | Splunk (index=vida_grupo)    | `ERROR.*RabbitMQ.*connection failed`          | ERROR     |
| **Common Error Handler**  | Splunk (index=vida_grupo)    | `FATAL.*unhandled exception`                  | CRITICAL  |

**Query de Splunk de ejemplo:**

```spl
index=vida_grupo source="microintegrador-reportes" 
| search "WorkQueue" AND "ERROR"
| stats count by workqueue_type, error_type
| sort -count
```

---

## 🧪 **Escenarios de Prueba**

### Casos de Prueba Críticos

#### TC001: Flujo Exitoso Completo - Factura con 10,000 Asegurados

```gherkin
Scenario: Generación exitosa de reporte de detalle de cobro para factura colectiva
  Given una factura colectiva "FAC2025001234" con 10,000 asegurados
  And todas las dependencias externas (BD, Azure, RabbitMQ) están disponibles
  When la aplicación cliente envía POST /v1/he/invoices/FAC2025001234/chargedetail/report
  Then el sistema responde HTTP 200 OK inmediatamente
  And un registro con status=1 se crea en CHARGE_DETAIL_PRINCIPAL
  And WorkQueue 1 procesa la solicitud en su próxima ejecución (max 10 min)
  And WorkQueue 2 construye 10,000 registros en CHARGE_DETAIL_ITEMS
  And WorkQueue 3 envía 2 bloques de 5,000 registros a Azure
  And WorkQueue 3 publica evento a RabbitMQ con status=7
  And el archivo está disponible para descarga en < 30 minutos
  When la aplicación cliente envía GET /v1/he/invoices/FAC2025001234/chargedetail/report
  Then el sistema responde HTTP 200 OK con el archivo CSV
  And el archivo contiene exactamente 10,000 líneas de detalle
```

#### TC002: Manejo de Error Crítico - Azure API No Disponible

```gherkin
Scenario: Manejo de error cuando Azure Massive Download API está caída
  Given una factura colectiva "FAC2025005678" procesada hasta status=5
  And WorkQueue 3 está intentando enviar a Azure
  And Azure Massive Download API retorna HTTP 503 Service Unavailable
  When WorkQueue 3 ejecuta su cron de 5 minutos
  Then el sistema implementa 3 reintentos con backoff exponencial
  And cada reintento espera 2, 4, 8 segundos respectivamente
  And si los 3 reintentos fallan
  Then el sistema actualiza CHARGE_DETAIL_PRINCIPAL con status=-2
  And registra error_message "Azure API unavailable after 3 retries"
  And envía log de severidad ERROR a Splunk
  And NO publica evento a RabbitMQ
  When un administrador revisa el estado
  Then puede identificar la factura en estado de error
  And puede reintentar manualmente o escalar con soporte Azure
```

#### TC003: Timeout en Procesamiento - Factura con 1 Millón de Asegurados

```gherkin
Scenario: Manejo de timeout en procesamiento de factura masiva
  Given una factura colectiva "FAC2025999999" con 1,000,000 de asegurados
  And el procesamiento de WorkQueue 2 excede 30 minutos
  When el sistema detecta que el procesamiento ha superado el timeout
  Then actualiza CHARGE_DETAIL_PRINCIPAL con status=-3 (TIMEOUT)
  And registra error_message "Procesamiento excedió 30 min"
  And envía log de severidad WARN a Splunk con detalles del invoice
  And detiene el procesamiento actual de WorkQueue 2
  When un administrador revisa el registro
  Then puede ajustar la configuración processing.timeout a 60 minutos
  Or puede partir el procesamiento en múltiples solicitudes más pequeñas
```

#### TC004: Consulta de Reporte en Proceso

```gherkin
Scenario: Cliente consulta reporte que aún se está generando
  Given una factura colectiva "FAC2025001111" con status=4 (construyendo registros)
  And el procesamiento total aún no ha completado
  When la aplicación cliente envía GET /v1/he/invoices/FAC2025001111/chargedetail/report
  Then el sistema responde HTTP 404 Not Found
  And el mensaje indica "El reporte aún se está generando. Estado actual: Construyendo registros"
  And el cliente debe implementar polling cada 2-5 minutos
  And eventualmente cuando status=7, el GET retornará HTTP 200 con el archivo
```

#### TC005: Validación de Consistencia de Datos

```gherkin
Scenario: Validación de que los datos del reporte coinciden con Guidewire
  Given una factura colectiva "FAC2025002222" procesada completamente
  And el archivo de detalle de cobro está disponible
  When se ejecuta una auditoría de datos
  Then cada registro del archivo debe tener un asegurado correspondiente en PolicyCenter
  And las sumas de primas en el archivo deben coincidir con BillingCenter
  And todas las coberturas listadas deben existir en la configuración de productos
  And los valores asegurados deben cumplir con las tablas actuariales
  And no debe haber duplicados de asegurados en el archivo
```

---

## 🔍 **Troubleshooting**

### Problemas Comunes y Soluciones

#### Error: "SQLException: Connection timeout to Oracle DB"

**Causa**: Pool de conexiones agotado o BD Guidewire no disponible  
**Diagnóstico**:

```bash
# Verificar estado del pool de conexiones
kubectl logs deployment/microintegrador-reportes-vidagrupo | grep "Connection pool"

# Verificar conectividad a BD
sqlplus ADM_VIDAGRUPOREPORTES/<password>@//srcsbdl07.suramericana.com.co:1537/LABGWDWH

# Verificar conexiones activas
SELECT COUNT(*) FROM v$session WHERE username = 'ADM_VIDAGRUPOREPORTES';
```

**Solución**:  
1. Aumentar `oracle.jdbc.maxTotal` de 10 a 20 en configuración
2. Reducir `oracle.jdbc.maxConnLifetimeMillis` si hay conexiones stale
3. Verificar que no haya leaks de conexiones en el código (usar try-with-resources)
4. Contactar DBA si la BD Guidewire está saturada

---

#### Error: "Azure Massive Download API returning HTTP 500"

**Causa**: Azure API experimentando problemas internos o rate limiting  
**Diagnóstico**:

```bash
# Revisar logs de WorkQueue 3
kubectl logs deployment/microintegrador-reportes-vidagrupo | grep "Azure" | grep "500"

# Verificar reintentos
kubectl logs deployment/microintegrador-reportes-vidagrupo | grep "retry attempt"

# Query en Splunk
index=vida_grupo source="microintegrador-reportes" "Azure" "500"
| stats count by error_message
```

**Solución**:  
1. Revisar dashboard de Azure APIM para verificar estado de la API
2. Verificar si se está excediendo rate limit de Azure (revisar headers de respuesta)
3. Implementar backoff exponencial más agresivo (aumentar tiempos de espera)
4. Contactar soporte de Azure con detalles de requests fallidos
5. Como workaround temporal, reintentar manualmente la factura en error (actualizar status a 5)

---

#### Error: "OutOfMemoryError in WorkQueue 2"

**Causa**: Batch size muy grande para construcción de registros  
**Diagnóstico**:

```bash
# Revisar uso de memoria JVM
kubectl top pod -l app=microintegrador-reportes-vidagrupo

# Revisar heap dump (si está configurado)
jmap -heap <pod-name>

# Revisar batch size actual
kubectl logs deployment/microintegrador-reportes-vidagrupo | grep "batch.size.insert"
```

**Solución**:  
1. Reducir `batch.size.insert` de 10,000 a 5,000 o 2,000
2. Aumentar memoria JVM: `-Xmx4g -Xms2g` en Dockerfile o deployment
3. Revisar si hay acumulación de objetos en memoria (posible leak)
4. Implementar procesamiento por páginas más pequeñas
5. Optimizar uso de memoria en `WorkQueue2Processors` (liberar objetos grandes)

---

#### Error: "RabbitMQ connection failed - Authentication failed"

**Causa**: Credenciales incorrectas o usuario sin permisos en virtual host  
**Diagnóstico**:

```bash
# Verificar credenciales en configuración
kubectl get secret microintegrador-reportes-config -o yaml | grep "rabbitmq"

# Verificar conectividad a RabbitMQ
telnet msglab.suramericana.com.co 5672

# Revisar logs de conexión
kubectl logs deployment/microintegrador-reportes-vidagrupo | grep "RabbitMQ"
```

**Solución**:  
1. Verificar credenciales de `spring.rabbitmq.producer.username` y `.password`
2. Verificar permisos del usuario en RabbitMQ Management Console
3. Confirmar que el usuario tiene acceso al virtual host `seguros.vh`
4. Re-generar credenciales si es necesario y actualizar secrets en Kubernetes
5. Verificar firewall/network policies que puedan bloquear puerto 5672

---

#### Error: "Timeout en procesamiento - Factura con millones de registros"

**Causa**: Volumen de datos excede capacidad de procesamiento en tiempo configurado  
**Diagnóstico**:

```bash
# Verificar total de registros de la factura
SELECT COUNT(*) 
FROM ADM_VIDAGRUPOREPORTES.CHARGE_DETAIL_ITEMS 
WHERE invoice_number = 'FAC2025999999';

# Verificar tiempo de procesamiento
SELECT invoice_number, status, created_date, updated_date, 
       (updated_date - created_date) AS processing_time
FROM ADM_VIDAGRUPOREPORTES.CHARGE_DETAIL_PRINCIPAL 
WHERE invoice_number = 'FAC2025999999';

# Revisar logs de timeout
kubectl logs deployment/microintegrador-reportes-vidagrupo | grep "Timeout"
```

**Solución**:  
1. Aumentar `processing.timeout` de 30 a 60 minutos para facturas masivas
2. Optimizar consultas SQL en WorkQueue 1 (agregar índices, revisar execution plan)
3. Aumentar `batch.size.insert` para reducir número de transacciones
4. Considerar particionar facturas muy grandes en múltiples solicitudes
5. Escalar horizontalmente el microservicio (más réplicas) si el problema persiste

---

### Comandos de Diagnóstico Útiles

```bash
# Verificar estado de componentes
kubectl get pods -l app=microintegrador-reportes-vidagrupo
kubectl describe pod <pod-name>

# Revisar logs en tiempo real
kubectl logs -f deployment/microintegrador-reportes-vidagrupo --tail=100

# Verificar métricas de recursos
kubectl top pod -l app=microintegrador-reportes-vidagrupo

# Verificar configuración actual
kubectl exec -it <pod-name> -- env | grep -E "oracle|rabbitmq|azure"

# Revisar cron jobs activos (Work Queues)
kubectl logs deployment/microintegrador-reportes-vidagrupo | grep "Quartz Scheduler"

# Verificar estado de reportes en BD
sqlplus ADM_VIDAGRUPOREPORTES/<password>@//host:1537/SERVICE << EOF
SELECT status, COUNT(*) as count 
FROM CHARGE_DETAIL_PRINCIPAL 
GROUP BY status 
ORDER BY status;
EXIT;
EOF

# Verificar mensajes pendientes en RabbitMQ
# Acceder a RabbitMQ Management: http://msglab.suramericana.com.co:15672
# Filtrar por queue: sura.seguros.vidagrupo.chargedetail.risk.qu

# Query de Splunk para análisis de errores
index=vida_grupo source="microintegrador-reportes" 
| search "ERROR" OR "FATAL"
| stats count by error_type, component
| sort -count
```

---

## 🚀 **Optimizaciones Futuras**

### Oportunidades de Mejora Identificadas

1. **Implementar Cache de Consultas Frecuentes**
   - **Descripción**: Cachear resultados de consultas a PolicyCenter para productos y coberturas estáticas que no cambian frecuentemente
   - **Beneficio**: Reducir en 30-40% el tiempo de consulta en WorkQueue 1, disminuir carga en BD PolicyCenter
   - **Complejidad**: Media (implementar Redis o Hazelcast, gestionar invalidación)
   - **Prioridad**: Alta

2. **Paralelizar Construcción de Registros en WorkQueue 2**
   - **Descripción**: Utilizar procesamiento paralelo (Java Streams paralelos o ExecutorService) para construcción de registros de detalle
   - **Beneficio**: Reducir en 50% el tiempo de construcción para facturas con >100K asegurados
   - **Complejidad**: Media (requiere gestión de hilos, pool de conexiones adecuado)
   - **Prioridad**: Alta

3. **Implementar Compresión de Archivos en Azure**
   - **Descripción**: Comprimir archivos CSV antes de enviar a Azure (gzip, zip) para reducir tamaño y tiempo de transferencia
   - **Beneficio**: Reducir en 60-70% el tiempo de envío de bloques grandes, optimizar ancho de banda
   - **Complejidad**: Baja (Azure Massive Download ya soporta compresión)
   - **Prioridad**: Media

4. **Dashboard de Monitoreo en Tiempo Real**
   - **Descripción**: Crear dashboard en Grafana/Kibana con métricas de generación de reportes (tiempo promedio, tasa de éxito, facturas en proceso)
   - **Beneficio**: Visibilidad completa del estado del sistema, alertas proactivas, mejor troubleshooting
   - **Complejidad**: Media (requiere exportar métricas, configurar dashboard)
   - **Prioridad**: Media

5. **API Webhook para Notificación de Finalización**
   - **Descripción**: Permitir que clientes registren webhooks para recibir notificación HTTP cuando el reporte esté listo (alternativa a polling)
   - **Beneficio**: Reducir carga de polling innecesario, mejorar experiencia de usuario
   - **Complejidad**: Alta (requiere gestión de webhooks, reintentos, seguridad)
   - **Prioridad**: Baja

6. **Optimización de Consultas SQL con Materialized Views**
   - **Descripción**: Crear materialized views en BD Oracle con datos pre-agregados de facturas y asegurados para acelerar consultas
   - **Beneficio**: Reducir en 40-50% el tiempo de consulta en WorkQueue 1
   - **Complejidad**: Media (requiere coordinación con DBA, gestión de refresh de views)
   - **Prioridad**: Alta

### Roadmap de Evolución

```mermaid
timeline
    title Roadmap de Optimización del Flujo - Generación de Reportes

    Q1 2026 : Fase 1 - Performance
            : Implementar cache de consultas (Redis)
            : Paralelizar WorkQueue 2
            : Optimizar índices de BD

    Q2 2026 : Fase 2 - Observabilidad
            : Dashboard de monitoreo (Grafana)
            : Alertas proactivas (PagerDuty)
            : Métricas de SLA

    Q3 2026 : Fase 3 - Escalabilidad
            : Compresión de archivos Azure
            : Materialized views en Oracle
            : Horizontal scaling automático

    Q4 2026 : Fase 4 - Experiencia de Usuario
            : API Webhook para notificaciones
            : Endpoint de consulta de progreso (%)
            : Generación incremental de reportes
```

---

## 📚 **Referencias**

- **GPS Arquitectónico**: [Arquitectura del Sistema - GPS Principal](./index.md)
- **Documentación de Componentes**:
  - [MicroIntegradorReportesVidaGrupo](./architecture-microintegrador-reportes-vidagrupo.md)
  - [BillingCenter](./index.md#billingcenter) (referencia en GPS)
  - [PolicyCenter](./index.md#policycenter) (referencia en GPS)
- **Configuración**: 
  - `MicroIntegradorReportesVidaGrupoConf/shared/microintegrator.properties`
  - `MicroIntegradorReportesVidaGrupo/src/main/resources/sql/` (Scripts SQL)
- **APIs Externas**:
  - [Azure Massive Download API Documentation](https://labapicorevidagrupo.suramericana.com/massive-download/docs)
  - [RabbitMQ Management](http://msglab.suramericana.com.co:15672)
- **Herramientas de Monitoreo**:
  - [Splunk Holmes Lab](http://holmeslab.suramericana.com.co:9088/)
  - [Oracle Enterprise Manager](https://oem.suramericana.com.co) (si disponible)

---

_Documentación generada con Método Ceiba - Arquitecto_  
_Última actualización: Octubre 28, 2025_  
_Versión: 1.0_
