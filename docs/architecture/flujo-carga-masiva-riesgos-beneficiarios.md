# Sistema Vida Grupo - Flujo: Carga Masiva de Riesgos y Beneficiarios 🔄

## 📋 **Introducción**

### Descripción del Flujo

La **Carga Masiva de Riesgos y Beneficiarios** es un proceso crítico de negocio que permite a los expedidores de pólizas de Vida Grupo cargar de forma masiva (batch) información de asegurados y sus beneficiarios para pólizas colectivas corporativas. Este flujo soporta operaciones de:

- **Ingreso**: Creación de nuevos riesgos (asegurados) en una póliza master
- **Modificación**: Actualización de información de riesgos existentes
- **Cancelación**: Baja de riesgos de la póliza
- **Renovación**: Renovación de riesgos en procesos de renovación de póliza

El proceso está diseñado para manejar **volúmenes masivos** (desde cientos hasta miles de registros) de manera **completamente asíncrona**, dividido en **dos fases independientes**:

- **Fase 1 (Azure)**: Validación de estructura y formato del archivo mediante Azure Data Factory
- **Fase 2 (PolicyCenter)**: Validación de reglas de negocio, transformación e inserción de registros mediante Workqueues

Este diseño permite **procesamiento no bloqueante**, donde el usuario puede continuar trabajando mientras la carga se procesa en background, con visibilidad en tiempo real del estado mediante un dashboard interactivo.

### Scope del Documento

**Enfoque Principal**: Documentación técnica end-to-end del flujo de trabajo de Carga Masiva  
**Audiencia**: Desarrolladores, Arquitectos, Analistas de Negocio, DevOps  
**Última Actualización**: 25 de Noviembre, 2025

### Componentes Involucrados

| Componente         | Tecnología   | Puerto/Contexto   | Responsabilidad               |
| ------------------ | ------------ | ----------------- | ----------------------------- |
| **PolicyCenter** | Gosu + Java + Guidewire 8.0.7 | http://localhost:8080/pc | Sistema de registro de pólizas. Inicia carga, procesa Fase 2, gestiona UI del dashboard |
| **VidaGrupoIAC (Azure APIM)** | Azure API Management | https://api-vidagrupo-{env}-apigateway.azure-api.net | Gateway de APIs. Expone endpoints de Upload, Status, Download con autenticación y rate limiting |
| **Azure Function Upload** | Node.js/C# Serverless | https://{env}Upload.azurewebsites.net | Recepción de bloques de archivo, almacenamiento en Storage Account |
| **Azure Function Status** | Node.js/C# Serverless | https://{env}Status.azurewebsites.net | Consulta y actualización de estados de carga en Cosmos DB |
| **Azure Function Download** | Node.js/C# Serverless | https://{env}Download.azurewebsites.net | Generación y descarga de archivos de errores por fase |
| **Azure Data Factory** | PaaS Orchestration | N/A (servicio administrado) | Orquestación de pipelines de validación Fase 1 con expresiones regulares |
| **Cosmos DB** | NoSQL Database | N/A (servicio administrado) | Almacenamiento de registros de carga, errores de validación, metadatos |
| **Azure Storage Account** | Blob Storage | N/A (servicio administrado) | Almacenamiento de archivos originales y archivos de errores generados |
| **RabbitMQ** | Message Broker | msglab.suramericana.com.co:5672 | Mensajería asíncrona entre Azure y PolicyCenter para notificaciones de estado |

---

## 🔄 **Diagramas de Secuencia**

### 1. Flujo Principal: Carga Masiva End-to-End

```mermaid
sequenceDiagram
    participant U as Usuario Expedidor
    participant UI as PolicyCenter UI<br/>(PCF + JavaScript)
    participant APIM as Azure API Management
    participant FN_UP as Azure Function<br/>Massive Upload
    participant STORAGE as Azure Storage<br/>Blob Container
    participant DF as Azure Data Factory<br/>Pipeline Validation
    participant COSMOS as Cosmos DB<br/>MASSIVE_UPLOAD
    participant FN_ST as Azure Function<br/>Massive Status
    participant RMQ as RabbitMQ<br/>Messaging
    participant WQ1 as PolicyCenter<br/>Workqueue Enrichment
    participant WQ2 as PolicyCenter<br/>Workqueue BulkInsert
    participant EDGE as PolicyCenter<br/>Edge Services
    participant DB as PolicyCenter<br/>Oracle Database

    Note over U,DB: INICIO: Usuario carga archivo en PolicyCenter

    U->>UI: 1. Acceder a póliza master → Procesos Masivos
    UI->>UI: 2. Mostrar dashboard de historico (LifeCreateMassivelyInsuredsPopup.pcf)
    U->>UI: 3. Click en "Cargar" → Seleccionar archivo CSV/Excel
    UI->>UI: 4. JavaScript: Calcular MD5 del archivo
    UI->>APIM: 5. GET uploadInProcessExists(policyNumber)
    APIM->>FN_ST: 5.1. Consultar cargas activas
    FN_ST->>COSMOS: 5.2. Query cargas en proceso
    COSMOS-->>FN_ST: 5.3. Resultado (vacío o con carga)
    FN_ST-->>APIM: 5.4. 404 (sin carga) o 200 (carga activa)
    APIM-->>UI: 5.5. Respuesta validación

    alt Carga en proceso detectada
        UI->>U: 6. Error: "Póliza procesando otra carga"
    else Sin carga en proceso
        UI->>UI: 7. Dividir archivo en bloques de 64KB
        
        loop Por cada bloque
            UI->>APIM: 8. PUT /file/{codOffer}/{operation}/{typeFile}/{policyId}/{md5}/blocks/{blockId}
            APIM->>FN_UP: 8.1. Enviar bloque
            FN_UP->>STORAGE: 8.2. Almacenar bloque en blob
            STORAGE-->>FN_UP: 8.3. Confirmación
            FN_UP-->>APIM: 8.4. 200 OK
            APIM-->>UI: 8.5. Bloque enviado
            UI->>U: 8.6. Actualizar barra de progreso (X%)
        end

        UI->>APIM: 9. POST /file/{codOffer}/{operation}/{typeFile}/{policyId}/{md5}/blocks?blockCount=N
        APIM->>FN_UP: 9.1. Commit archivo
        FN_UP->>STORAGE: 9.2. Consolidar bloques en archivo único
        FN_UP->>COSMOS: 9.3. Crear registro de carga (status=UPLOADED)
        COSMOS-->>FN_UP: 9.4. Confirmación
        FN_UP-->>APIM: 9.5. 201 Created
        APIM-->>UI: 9.6. Archivo cargado
        UI->>U: 9.7. "Archivo cargado exitosamente (100%)"
    end

    Note over STORAGE,DF: FASE 1: Validación Azure Data Factory (Asíncrono)

    STORAGE->>DF: 10. Trigger: BlobCreated event
    DF->>DF: 11. Pipeline startFileValidation ejecuta
    DF->>STORAGE: 12. Leer archivo CSV/Excel
    STORAGE-->>DF: 13. Contenido del archivo
    
    loop Por cada registro del archivo
        DF->>DF: 14. Aplicar validaciones de formato (expresiones regulares)
        DF->>DF: 15. Validar estructura según controlSchema
        
        alt Registro válido
            DF->>COSMOS: 16. INSERT registro en container UPLOAD_DETAIL (status=VALIDATED)
        else Registro inválido
            DF->>COSMOS: 17. INSERT error en container UPLOAD_DETAIL (status=ERROR_PHASE1)
        end
    end

    alt Fase 1 exitosa (sin errores críticos)
        DF->>COSMOS: 18. UPDATE registro principal (status=PHASE1_COMPLETED)
        DF->>RMQ: 19. Publicar mensaje: NotifyPhaseStarted
        RMQ->>DB: 20. Insertar en NotifyPhaseMessage_Ext
        DF->>RMQ: 21. Publicar mensaje por registro: RecordReadyForProcessing
        
        loop Por cada registro válido
            RMQ->>WQ1: 22. Consumir mensaje de registro
            WQ1->>DB: 23. INSERT en MassiveUploadMessage_Ext (status=PENDING_SEND)
        end
    else Fase 1 con errores
        DF->>STORAGE: 24. Generar archivo ErrorFase1_{md5}.csv
        DF->>COSMOS: 25. UPDATE registro (status=ERROR_PHASE1, linkErrors=urlArchivo)
        DF->>RMQ: 26. Publicar NotifyPhaseError
    end

    Note over WQ1,DB: FASE 2: Procesamiento PolicyCenter (Asíncrono)

    WQ1->>WQ1: 27. Workqueue Enrichment inicia (scheduled)
    WQ1->>DB: 28. SELECT * FROM MassiveUploadMessage_Ext WHERE status=PENDING_SEND
    DB-->>WQ1: 29. Registros pendientes
    
    loop Por cada registro pendiente
        WQ1->>APIM: 30. GET /massivestatus/{policyNumber}/{md5}
        APIM->>FN_ST: 30.1. Consultar detalle
        FN_ST->>COSMOS: 30.2. Query registro completo
        COSMOS-->>FN_ST: 30.3. Datos del registro
        FN_ST-->>APIM: 30.4. 200 OK + JSON data
        APIM-->>WQ1: 30.5. Detalles del registro
        
        WQ1->>WQ1: 31. Validar campos de negocio (OperationValidationFactory)
        
        alt Validación exitosa
            WQ1->>WQ1: 32. Transformar datos (TransformerFactoryProducer)
            WQ1->>DB: 33. UPDATE MassiveUploadMessage_Ext (status=ACKED, response=transformedData)
            WQ1->>APIM: 34. PUT /massivestatus/{policyNumber}/{md5} (status=PROCESSING_PHASE2)
            APIM->>FN_ST: 34.1. Actualizar estado
            FN_ST->>COSMOS: 34.2. UPDATE estado
        else Validación fallida
            WQ1->>DB: 35. UPDATE MassiveUploadMessage_Ext (status=ERROR, errorMessage=...)
            WQ1->>APIM: 36. PUT /massivestatus/{policyNumber}/{md5} (status=ERROR_PHASE2)
            WQ1->>STORAGE: 37. Agregar error a archivo ErrorFase2_{md5}.csv
        end
    end

    Note over WQ2,EDGE: FASE 2: Bulk Insert según Volumen

    WQ2->>WQ2: 38. Workqueue BulkInsert inicia (por volumen: Low/Medium/High)
    WQ2->>DB: 39. SELECT * FROM MassiveUploadMessage_Ext WHERE status=ACKED
    DB-->>WQ2: 40. Registros listos para insertar
    
    loop Por cada registro ACKED
        WQ2->>EDGE: 41. Invocar Edge Service según operación
        
        alt Operación: Ingreso (Submission)
            EDGE->>DB: 42. Crear nueva póliza/riesgo (INSERT)
        else Operación: Modificación (PolicyChange)
            EDGE->>DB: 43. Actualizar riesgo existente (UPDATE)
        else Operación: Cancelación (Cancellation)
            EDGE->>DB: 44. Cancelar riesgo (UPDATE + estado)
        else Operación: Renovación (Renewal)
            EDGE->>DB: 45. Renovar riesgo (INSERT nueva versión)
        end
        
        DB-->>EDGE: 46. Resultado operación
        
        alt Inserción exitosa
            EDGE-->>WQ2: 47. Success
            WQ2->>DB: 48. UPDATE MassiveUploadMessage_Ext (status=COMPLETED)
            WQ2->>APIM: 49. Notificar éxito a Azure
            APIM->>FN_ST: 49.1. Actualizar contador
            FN_ST->>COSMOS: 49.2. INCREMENT successful_count
        else Inserción fallida
            EDGE-->>WQ2: 50. Error (timeout, constraint violation, etc.)
            WQ2->>DB: 51. UPDATE MassiveUploadMessage_Ext (status=ERROR)
            WQ2->>APIM: 52. Notificar error a Azure
            APIM->>FN_ST: 52.1. Actualizar contador
            FN_ST->>COSMOS: 52.2. INCREMENT error_count
        end
    end

    Note over U,DB: FINALIZACIÓN: Usuario consulta resultados

    U->>UI: 53. Click en "Refrescar Dashboard"
    UI->>APIM: 54. GET /massivestatus/{policyNumber}?page=1&size=10
    APIM->>FN_ST: 54.1. Listar cargas con paginación
    FN_ST->>COSMOS: 54.2. Query cargas ordenadas por fecha
    COSMOS-->>FN_ST: 54.3. Lista de cargas
    FN_ST-->>APIM: 54.4. 200 OK + JSON array
    APIM-->>UI: 54.5. Datos del dashboard
    UI->>U: 54.6. Mostrar tabla con MD5, fecha, estado, registros exitosos/fallidos

    alt Hay errores en alguna fase
        U->>UI: 55. Click en "Descargar Errores Fase X"
        UI->>APIM: 56. GET /massive-download/file/output-errors/...
        APIM->>FN_DOWN: 56.1. Solicitar archivo de errores
        FN_DOWN->>STORAGE: 56.2. Leer ErrorFaseX_{md5}.csv
        STORAGE-->>FN_DOWN: 56.3. Contenido del archivo
        FN_DOWN-->>APIM: 56.4. 200 OK + CSV data
        APIM-->>UI: 56.5. Archivo CSV
        UI->>U: 56.6. Descargar ErrorFase1_{md5}.csv
    end
```

### 2. Flujo de Manejo de Errores

```mermaid
sequenceDiagram
    participant UI as PolicyCenter UI
    participant APIM as Azure API Management
    participant FN_ST as Function Status
    participant COSMOS as Cosmos DB
    participant DF as Data Factory
    participant WQ as Workqueue
    participant STORAGE as Storage Account

    Note over UI,STORAGE: ESCENARIO 1: Error en Validación Fase 1 (DataFactory)

    DF->>DF: Validar registro con regex
    DF->>DF: Detectar formato inválido (ej: fecha futura, campo vacío)
    DF->>COSMOS: INSERT error detail (phase=1, errorMessage="Campo X inválido")
    DF->>DF: Incrementar contador de errores
    
    alt Todos los registros con error
        DF->>COSMOS: UPDATE carga (status=ERROR_PHASE1)
        DF->>STORAGE: Generar ErrorFase1_{md5}.csv
        DF->>COSMOS: UPDATE linkErrors con URL de archivo
    else Errores parciales
        DF->>COSMOS: UPDATE carga (status=PHASE1_PARTIAL_ERROR)
        DF->>STORAGE: Generar ErrorFase1_{md5}.csv (solo errores)
        DF->>DF: Continuar con registros válidos a Fase 2
    end

    Note over UI,STORAGE: ESCENARIO 2: Timeout en Azure Upload

    UI->>APIM: PUT /file/.../blocks/{blockId}
    APIM->>FN_ST: Enviar bloque
    FN_ST->>FN_ST: Procesamiento lento (red, tamaño)
    
    alt Timeout antes de respuesta
        FN_ST--xUI: Timeout (sin respuesta)
        UI->>UI: Detectar timeout
        UI->>APIM: GET /file/.../blocks?asCount=true
        APIM->>FN_ST: Consultar último bloque recibido
        FN_ST->>COSMOS: Query lastBlockId
        COSMOS-->>FN_ST: blockId=X
        FN_ST-->>APIM: 200 OK {lastBlock: X}
        APIM-->>UI: Último bloque confirmado
        UI->>UI: Reanudar desde bloque X+1
        UI->>APIM: PUT /file/.../blocks/{X+1}
    else Respuesta exitosa después de demora
        FN_ST-->>APIM: 200 OK (con delay)
        APIM-->>UI: Bloque recibido
        UI->>UI: Continuar con siguiente bloque
    end

    Note over UI,STORAGE: ESCENARIO 3: Error en Validación Fase 2 (PolicyCenter)

    WQ->>WQ: OperationValidationFactory.validate()
    WQ->>WQ: Detectar error de negocio (ej: riesgo no existe para beneficiario)
    WQ->>WQ: Lanzar ValidationException
    
    alt Error recuperable (reintentable)
        WQ->>WQ: Verificar attempts < maxAttempts
        WQ->>WQ: Incrementar attempts
        WQ->>WQ: Re-encolar registro (retry después de delay)
    else Error no recuperable
        WQ->>COSMOS: UPDATE registro (status=ERROR_PHASE2, errorMessage="...")
        WQ->>STORAGE: Append error a ErrorFase2_{md5}.csv
        WQ->>APIM: PUT /massivestatus/{policyNumber}/{md5} (actualizar contador errores)
        APIM->>FN_ST: Actualizar estadísticas
        FN_ST->>COSMOS: INCREMENT error_count_phase2
    end

    Note over UI,STORAGE: ESCENARIO 4: Fallo en Servicios Edge

    WQ->>WQ: EdgeProcessing.process()
    WQ->>WQ: Invocar Edge Service (Submission/Change/Renewal)
    WQ->>WQ: Edge Service timeout o exception
    
    alt Timeout Edge Service
        WQ->>WQ: Catch TimeoutException
        WQ->>WQ: Verificar attempts < maxAttempts (ej: 3 reintentos)
        
        alt Reintentos disponibles
            WQ->>WQ: Incrementar attempts, re-encolar
        else Reintentos agotados
            WQ->>COSMOS: Marcar ERROR_EDGE_TIMEOUT
            WQ->>STORAGE: Agregar a archivo ErrorFase2
        end
    else Exception crítica Edge
        WQ->>WQ: Catch Exception
        WQ->>COSMOS: Marcar ERROR_EDGE_EXCEPTION
        WQ->>STORAGE: Agregar detalle de exception a ErrorFase2
    end

    Note over UI,STORAGE: ESCENARIO 5: Estado Inconsistente (Carga Finalizada en Policy, En Progreso en Azure)

    UI->>UI: Usuario reporta carga "atascada"
    UI->>APIM: GET /massivestatus/{policyNumber}/{md5}
    APIM->>FN_ST: Consultar estado
    FN_ST->>COSMOS: Query estado actual
    COSMOS-->>FN_ST: status=PROCESSING (inconsistente)
    FN_ST-->>APIM: 200 OK {status: "En Progreso"}
    APIM-->>UI: Estado: "En Progreso"
    
    UI->>UI: Operador detecta inconsistencia
    UI->>APIM: PUT /massivestatus/{policyNumber}/{md5} (force_complete=true)
    APIM->>FN_ST: Forzar actualización de estado
    FN_ST->>COSMOS: UPDATE status=COMPLETED (manual override)
    COSMOS-->>FN_ST: Confirmación
    FN_ST-->>APIM: 200 OK
    APIM-->>UI: Estado actualizado
    UI->>UI: Refrescar dashboard → mostrar "Finalizada"
```

### 3. Flujo de Operaciones Asíncronas

```mermaid
sequenceDiagram
    participant UI as Usuario
    participant JS as JavaScript Client<br/>(massiveLoad.js)
    participant APIM as API Gateway
    participant FN_UP as Azure Function Upload
    participant STORAGE as Storage Account
    participant DF as Data Factory<br/>(Trigger Automático)
    participant COSMOS as Cosmos DB
    participant RMQ as RabbitMQ
    participant WQ1 as Workqueue Enrichment<br/>(Scheduled Job)
    participant WQ2 as Workqueue BulkInsert<br/>(Scheduled Job)

    Note over UI,WQ2: PROCESAMIENTO ASÍNCRONO - NO BLOQUEANTE

    UI->>JS: Seleccionar archivo y Click "Cargar"
    JS->>JS: Calcular MD5 + Dividir en bloques (async)
    
    par Envío de bloques (paralelo no bloqueante)
        loop Bloque 1 a N
            JS->>APIM: XMLHttpRequest async PUT /blocks/{blockId}
            APIM->>FN_UP: Procesar bloque
            FN_UP->>STORAGE: Write blob chunk
            STORAGE-->>FN_UP: OK
            FN_UP-->>APIM: 200 OK
            APIM-->>JS: Callback success
            JS->>JS: Actualizar progress bar (no bloquea UI)
        end
    and Usuario continúa trabajando
        UI->>UI: Usuario puede navegar, consultar dashboard, etc.
    end

    JS->>APIM: POST /blocks (commit final)
    APIM->>FN_UP: Consolidar archivo
    FN_UP->>STORAGE: Commit blob (operación atómica)
    STORAGE-->>FN_UP: Blob URI
    FN_UP-->>APIM: 201 Created
    APIM-->>JS: Archivo cargado
    JS->>UI: Notificación: "Archivo cargado, procesamiento iniciado"
    JS->>UI: Cerrar modal, volver a dashboard

    Note over STORAGE,COSMOS: PROCESAMIENTO EN BACKGROUND - FASE 1

    STORAGE->>DF: BlobCreated Event (trigger automático)
    activate DF
    DF->>DF: Pipeline startFileValidation ejecuta
    DF->>STORAGE: Leer archivo en chunks
    
    loop Procesar lotes de 100 registros
        DF->>DF: Validar lote con dataflow
        DF->>COSMOS: Batch INSERT registros validados
    end
    
    DF->>COSMOS: UPDATE metadata (status=PHASE1_COMPLETED)
    DF->>RMQ: Publicar eventos NotifyPhase (async)
    deactivate DF

    Note over RMQ,WQ1: COLA DE MENSAJERÍA ASÍNCRONA

    RMQ->>WQ1: Consumir mensaje (pull o push)
    WQ1->>WQ1: Crear trabajo en cola (no procesado inmediatamente)

    Note over WQ1,WQ2: PROCESAMIENTO PROGRAMADO (SCHEDULED JOBS)

    loop Cada 5 minutos (configurable)
        activate WQ1
        WQ1->>WQ1: Scheduled trigger ejecuta
        WQ1->>WQ1: Procesar batch de 50 registros PENDING_SEND
        
        par Procesamiento de registros (paralelo interno)
            WQ1->>WQ1: Validar registro 1
            WQ1->>WQ1: Transformar registro 1
        and
            WQ1->>WQ1: Validar registro 2
            WQ1->>WQ1: Transformar registro 2
        and
            WQ1->>WQ1: Validar registro 3...N
        end
        
        WQ1->>WQ1: Marcar registros como ACKED
        deactivate WQ1
    end

    loop Cada 5 minutos (por volumen: Low/Medium/High)
        activate WQ2
        WQ2->>WQ2: Scheduled trigger ejecuta (según volumen)
        WQ2->>WQ2: Procesar batch de registros ACKED
        
        loop Por cada registro en batch
            WQ2->>WQ2: Invocar Edge Service
            WQ2->>WQ2: Persistir resultado
        end
        
        WQ2->>WQ2: Marcar registros como COMPLETED/ERROR
        WQ2->>COSMOS: Actualizar estadísticas (async via RabbitMQ)
        deactivate WQ2
    end

    Note over UI,COSMOS: CONSULTA DE ESTADO (NO BLOQUEANTE)

    loop Usuario refresca dashboard periódicamente
        UI->>APIM: GET /massivestatus/{policyNumber}
        APIM->>COSMOS: Query estados
        COSMOS-->>APIM: Snapshot actual
        APIM-->>UI: Estado en tiempo real
        UI->>UI: Actualizar tabla dashboard
    end
```

---

## 📊 **Estados y Transiciones**

### Diagrama de Estados del Flujo

```mermaid
stateDiagram-v2
    [*] --> UPLOADING: Usuario inicia carga de archivo
    UPLOADING --> UPLOADED: Todos los bloques enviados exitosamente
    UPLOADING --> ERROR_UPLOAD: Timeout o error en envío de bloques
    
    UPLOADED --> VALIDATING_PHASE1: Data Factory inicia validación
    VALIDATING_PHASE1 --> PHASE1_COMPLETED: Validación exitosa (sin errores críticos)
    VALIDATING_PHASE1 --> PHASE1_PARTIAL_ERROR: Validación con errores parciales
    VALIDATING_PHASE1 --> ERROR_PHASE1: Validación fallida (todos los registros con error)
    
    PHASE1_COMPLETED --> PROCESSING_PHASE2: Workqueue Enrichment inicia
    PHASE1_PARTIAL_ERROR --> PROCESSING_PHASE2: Registros válidos continúan a Fase 2
    
    PROCESSING_PHASE2 --> ENRICHED: Validación y transformación exitosa
    PROCESSING_PHASE2 --> ERROR_PHASE2: Error en validación de negocio
    
    ENRICHED --> INSERTING: Workqueue BulkInsert inicia
    INSERTING --> COMPLETED: Todos los registros insertados exitosamente
    INSERTING --> PARTIAL_COMPLETED: Algunos registros insertados, otros con error
    INSERTING --> ERROR_INSERT: Error crítico en inserción (Edge timeout, DB constraint)
    
    COMPLETED --> [*]: Proceso finalizado exitosamente
    PARTIAL_COMPLETED --> [*]: Proceso finalizado con errores parciales
    ERROR_UPLOAD --> [*]: Usuario debe reintentar carga
    ERROR_PHASE1 --> [*]: Usuario descarga errores, corrige archivo y reintenta
    ERROR_PHASE2 --> [*]: Usuario descarga errores, analiza y reintenta registros fallidos
    ERROR_INSERT --> [*]: Requiere intervención técnica
    
    ERROR_PHASE1 --> REINTENTO: Usuario sube archivo corregido
    ERROR_PHASE2 --> REINTENTO: Usuario sube archivo corregido
    REINTENTO --> UPLOADING: Nueva carga iniciada
```

---

## 📋 **Configuración y Parámetros**

### Configuración del Flujo

| Parámetro         | Valor         | Descripción             | Impacto si se Cambia |
| ----------------- | ------------- | ----------------------- | -------------------- |
| `BLOCK_SIZE` | `64 KB` | Tamaño de cada bloque para envío a Azure | Bloques más grandes: menos requests pero mayor riesgo de timeout. Bloques más pequeños: más requests pero más resiliente |
| `WORKQUEUE_BATCH_SIZE_ENRICHMENT` | `50 registros` | Cantidad de registros procesados por ejecución del Workqueue Enrichment | Valores altos: procesamiento más rápido pero mayor carga en DB. Valores bajos: procesamiento más lento pero menor impacto |
| `WORKQUEUE_SCHEDULE_ENRICHMENT` | `Cada 5 minutos` | Frecuencia de ejecución del Workqueue Enrichment | Menor frecuencia: procesamiento más lento. Mayor frecuencia: más carga en sistema |
| `WORKQUEUE_SCHEDULE_BULKINSERT_LOW` | `Cada 5 min (Lun-Vie 5-18h)` | Schedule para cargas pequeñas (0-100 registros) | Cambiar horario afecta ventana de procesamiento |
| `WORKQUEUE_SCHEDULE_BULKINSERT_MEDIUM` | `Cada 5 min (Lun-Vie 5-18h)` | Schedule para cargas medianas (101-2000 registros) | Cambiar horario afecta ventana de procesamiento |
| `WORKQUEUE_SCHEDULE_BULKINSERT_HIGH` | `Cada 5 min (Lun-Vie 5-18h)` | Schedule para cargas grandes (2001+ registros) | Cambiar horario afecta ventana de procesamiento |
| `MAX_RETRY_ATTEMPTS_ENRICHMENT` | `3 reintentos` | Número máximo de reintentos para validación/transformación | Más reintentos: mayor resiliencia pero registros problemáticos tardan más en marcar como error |
| `MAX_RETRY_ATTEMPTS_BULKINSERT` | `3 reintentos` | Número máximo de reintentos para inserción Edge | Más reintentos: mayor resiliencia para errores transitorios |
| `COSMOS_TTL_RECORDS` | `90 días` | Time To Live de registros en Cosmos DB | Mayor TTL: más almacenamiento. Menor TTL: pérdida de histórico |
| `PAGINATION_SIZE_DASHBOARD` | `10 cargas` | Cantidad de cargas mostradas por página en dashboard | Valores altos: más carga en renderizado UI. Valores bajos: más paginación |

### Message Queues Utilizadas

| Cola             | Exchange     | Routing Key     | TTL     | Propósito               |
| ---------------- | ------------ | --------------- | ------- | ----------------------- |
| `massive.upload.notify.phase` | `sura.seguros.massive` | `notify.phase.started` | `30 días` | Notificar inicio de fase desde Azure a PolicyCenter |
| `massive.upload.record.ready` | `sura.seguros.massive` | `record.ready.processing` | `30 días` | Notificar registro listo para Workqueue Enrichment |
| `massive.upload.status.update` | `sura.seguros.massive` | `status.update` | `7 días` | Actualizar estado de registro en Azure desde PolicyCenter (cola 44) |

---

## 🔧 **Métricas y Monitoreo**

### Puntos Críticos de Medición

| Métrica                    | Componente             | Umbral Esperado | Acción si se Excede    |
| -------------------------- | ---------------------- | --------------- | ---------------------- |
| **Tiempo de carga de archivo** | JavaScript + Azure Function Upload | < 2 minutos por cada 10MB | Revisar latencia de red, tamaño de bloques, concurrencia de requests |
| **Duración Fase 1 (DataFactory)** | Azure Data Factory | < 10 minutos por cada 1000 registros | Optimizar pipelines, aumentar paralelismo, revisar expresiones regulares |
| **Duración Fase 2 (Enrichment)** | Workqueue Enrichment | < 5 minutos por cada 100 registros | Optimizar validaciones, revisar queries a Cosmos/Oracle, aumentar batch size |
| **Duración Fase 2 (BulkInsert)** | Workqueue BulkInsert | < 10 minutos por cada 100 registros | Optimizar servicios Edge, revisar performance de DB, aumentar paralelismo |
| **Tasa de error Fase 1** | Data Factory | < 5% de registros con error | Revisar calidad de archivos fuente, mejorar documentación para usuarios |
| **Tasa de error Fase 2** | Workqueue Enrichment | < 2% de registros con error | Revisar validaciones de negocio, capacitar a usuarios en reglas |
| **Latencia API Massive Status** | Azure Function Status | < 500ms (p95) | Optimizar queries Cosmos DB, implementar cache, revisar índices |
| **Latencia API Massive Upload** | Azure Function Upload | < 1s por bloque (p95) | Revisar performance Storage Account, optimizar escritura de blobs |

### Logs Críticos a Monitorear

| Componente        | Archivo Log     | Patrón a Buscar       | Severidad |
| ----------------- | --------------- | --------------------- | --------- |
| **PolicyCenter Workqueue** | `PolicyCenter.log` | `ERROR.*MassiveUploadMessageProcess` | ERROR |
| **PolicyCenter Workqueue** | `PolicyCenter.log` | `ERROR.*EnrichmentProcessing` | ERROR |
| **PolicyCenter Workqueue** | `PolicyCenter.log` | `ERROR.*EdgeProcessing` | ERROR |
| **Azure Function Upload** | Application Insights | `exceptions` where `cloud_RoleName` contains "Upload" | ERROR |
| **Azure Function Status** | Application Insights | `exceptions` where `cloud_RoleName` contains "Status" | ERROR |
| **Azure Data Factory** | Monitor Logs | `Pipeline Run Failed` | ERROR |
| **RabbitMQ** | RabbitMQ Management | `Messages Unacked > 100` | WARN |
| **Cosmos DB** | Metrics | `429 Throttling Errors` | WARN |

---

## 🧪 **Escenarios de Prueba**

### Casos de Prueba Críticos

#### TC001: Flujo Exitoso Completo - Carga de Riesgos (Ingreso)

```gherkin
Scenario: Carga masiva exitosa de 100 riesgos nuevos en póliza Vida Grupo Integral
  Given el usuario es un expedidor autenticado en PolicyCenter
  And existe una póliza master de Vida Grupo Integral activa con número "VG-2025-001"
  And el usuario tiene un archivo CSV "riesgos_ingreso_100.csv" con 100 registros válidos
  And el archivo contiene: identificación, nombre, fecha nacimiento, suma asegurada, coberturas
  And no hay otra carga en proceso para la póliza "VG-2025-001"
  
  When el usuario navega a la póliza master "VG-2025-001"
  And hace click en "Procesos Masivos" → "Cargar Masivamente"
  And selecciona "Tipo de Archivo: Riesgo"
  And selecciona "Operación: Ingreso"
  And sube el archivo "riesgos_ingreso_100.csv"
  
  Then el sistema debe calcular el MD5 del archivo
  And debe enviar 100+ bloques de 64KB a Azure Massive Upload API
  And debe mostrar barra de progreso llegando al 100%
  And debe mostrar mensaje "Archivo cargado exitosamente"
  
  And dentro de 10 minutos, Azure Data Factory debe validar los 100 registros (Fase 1)
  And los 100 registros deben pasar las validaciones de formato
  And debe notificar a PolicyCenter vía RabbitMQ
  
  And el Workqueue Enrichment debe procesar los 100 registros (Fase 2 - Validación)
  And todos los registros deben pasar validaciones de negocio
  And debe transformar los datos según el template de Vida Grupo Integral
  
  And el Workqueue BulkInsert debe insertar los 100 riesgos en la póliza
  And debe invocar Edge Service "Submission" para cada riesgo
  And debe crear 100 certificados individuales en PolicyCenter
  
  And al refrescar el dashboard, debe mostrar:
    | Campo | Valor Esperado |
    | Estado | Finalizada |
    | Registros Cargados | 100 |
    | Exitosos | 100 |
    | Fallidos | 0 |
    | Fase | 2 |
```

#### TC002: Manejo de Error Crítico - Validación Fase 1

```gherkin
Scenario: Archivo con errores de formato es rechazado en Fase 1
  Given el usuario es un expedidor autenticado en PolicyCenter
  And existe una póliza master "VG-2025-002"
  And el usuario tiene un archivo CSV "riesgos_invalidos.csv" con 50 registros
  And 30 registros tienen fechas de nacimiento en formato incorrecto (DD/MM/YYYY en lugar de YYYY-MM-DD)
  And 10 registros tienen identificaciones con caracteres especiales inválidos
  And 10 registros son válidos
  
  When el usuario carga el archivo "riesgos_invalidos.csv"
  
  Then el archivo debe cargarse exitosamente a Azure Storage
  And Azure Data Factory debe ejecutar validaciones de formato
  And debe detectar 40 errores de formato (30 fechas + 10 identificaciones)
  And debe generar archivo "ErrorFase1_{md5}.csv" con detalle de los 40 errores
  And debe almacenar el archivo en Azure Storage
  And debe actualizar estado de la carga a "ERROR_PHASE1" en Cosmos DB
  
  And al refrescar el dashboard, debe mostrar:
    | Campo | Valor Esperado |
    | Estado | Error en Fase 1 |
    | Registros Cargados | 50 |
    | Exitosos Fase 1 | 10 |
    | Fallidos Fase 1 | 40 |
  
  And el usuario debe poder hacer click en "Descargar Errores Fase 1"
  And debe descargar archivo CSV con las 40 líneas con error y descripción del problema
  And debe mostrar mensajes como "Fecha inválida en columna 5", "Caracteres especiales no permitidos en identificación"
```

#### TC003: Timeout en Procesamiento

```gherkin
Scenario: Timeout en Edge Service requiere reintento automático
  Given hay una carga en Fase 2 con 20 registros en estado ACKED
  And el Workqueue BulkInsert está procesando los registros
  And el servicio Edge de PolicyCenter tiene alta latencia (> 30 segundos)
  
  When el Workqueue intenta insertar el registro #5
  And invoca el Edge Service "Submission"
  And el Edge Service no responde en 30 segundos (timeout)
  
  Then el Workqueue debe capturar TimeoutException
  And debe verificar que attempts < maxAttempts (3)
  And debe incrementar el contador de attempts a 1
  And debe re-encolar el registro #5 para reintentar en 5 minutos
  And debe continuar procesando el registro #6
  
  And en el segundo intento (5 minutos después)
  When el Edge Service sigue con timeout
  Then debe incrementar attempts a 2 y re-encolar
  
  And en el tercer intento
  When el Edge Service finalmente responde exitosamente
  Then debe marcar el registro como COMPLETED
  And debe actualizar estado en Azure a "Procesado"
```

---

## 🔍 **Troubleshooting**

### Problemas Comunes y Soluciones

#### Error: "La póliza máster está procesando otra carga y no ha finalizado"

**Causa**: Hay una carga previa en estado "En Progreso" que no ha finalizado (posiblemente atascada)

**Diagnóstico**:
```bash
# 1. Verificar cargas activas en Cosmos DB (Azure Portal)
# Navegar a: Cosmos DB → MASSIVE_UPLOAD → Container: MAIN_UPLOAD
# Query:
SELECT * FROM c WHERE c.policyNumber = "VG-2025-001" AND c.status IN ("PROCESSING_PHASE2", "VALIDATING_PHASE1")

# 2. Verificar en PolicyCenter Oracle DB
SELECT * FROM MassiveUploadMessage_Ext 
WHERE MasterPolicy = 'VG-2025-001' 
  AND Status IN ('PENDING_SEND', 'ACKED')
ORDER BY LastUpdate DESC;

# 3. Verificar estado de Workqueues
# En PolicyCenter Admin Console:
# Admin → Operations → WorkQueues → Buscar "MassiveUpload"
```

**Solución**: 
1. Si la carga realmente está procesando, esperar a que finalice
2. Si está atascada (sin cambios por > 1 hora):
   - Opción A: Actualizar estado manualmente en Cosmos DB a "COMPLETED"
   - Opción B: Usar API `PUT /massivestatus/{policyNumber}/{md5}` con `force_complete=true`
   - Opción C: Ejecutar script de limpieza en PolicyCenter

```sql
-- Script para marcar carga como completada manualmente
UPDATE MassiveUploadMessage_Ext 
SET Status = 'COMPLETED', LastUpdate = SYSDATE
WHERE LoadId = '{md5}' AND MasterPolicy = 'VG-2025-001';
COMMIT;
```

#### Error: "Archivo de errores no encontrado para Fase X"

**Causa**: El archivo de errores fue eliminado de Azure Storage o nunca se generó

**Diagnóstico**:
```bash
# Verificar en Azure Portal
# Storage Account → Containers → Buscar archivo "ErrorFase1_{md5}.csv"

# Verificar linkErrors en Cosmos DB
SELECT c.linkErrors FROM c WHERE c.md5 = "{md5}"
```

**Solución**: 
- Si el link existe pero el archivo no: Regenerar archivo de errores desde Cosmos DB
- Si no hay link: Verificar logs de Data Factory para ver si hubo errores al generar el archivo

#### Error: "Validation Exception: Riesgo no encontrado para beneficiario"

**Causa**: Carga de beneficiarios donde el riesgo asociado no existe en la póliza

**Diagnóstico**:
```bash
# En PolicyCenter Oracle
SELECT * FROM pc_policyrisk 
WHERE PolicyNumber = 'VG-2025-001' 
  AND TaxId = '123456789';
```

**Solución**: 
- Verificar que el archivo de beneficiarios use identificaciones de riesgos existentes
- Cargar primero los riesgos, luego los beneficiarios
- Revisar que la operación de beneficiarios sea "Modificación" (no "Ingreso")

### Comandos de Diagnóstico Útiles

```bash
# 1. Verificar estado de componentes Azure
# Azure CLI
az functionapp show --name {env}Upload --resource-group {rg} --query "state"
az functionapp show --name {env}Status --resource-group {rg} --query "state"

# Verificar logs de Azure Functions
az functionapp log tail --name {env}Upload --resource-group {rg}

# 2. Verificar Data Factory pipeline runs
az datafactory pipeline-run query-by-factory \
  --factory-name {dataFactoryName} \
  --resource-group {rg} \
  --last-updated-after "2025-11-25T00:00:00Z"

# 3. Revisar logs de PolicyCenter Workqueues
# En servidor PolicyCenter
tail -f /path/to/PolicyCenter/logs/PolicyCenter.log | grep "MassiveUpload"

# 4. Verificar RabbitMQ queues
# RabbitMQ Management UI: http://msglab.suramericana.com.co:15672
# Verificar:
# - Queue: massive.upload.notify.phase (messages ready, unacked)
# - Queue: massive.upload.record.ready (messages ready, unacked)

# 5. Verificar métricas de Cosmos DB
# Azure Portal → Cosmos DB → Metrics
# Métricas clave:
# - Total Requests
# - Throttled Requests (429 errors)
# - Server Side Latency
```

---

## 🚀 **Optimizaciones Futuras**

### Oportunidades de Mejora Identificadas

1. **Paralelización de Workqueues**
   - Actualmente: Procesamiento secuencial de registros en cada Workqueue
   - Mejora propuesta: Implementar procesamiento paralelo con Thread Pool
   - Beneficio esperado: Reducción de 40-60% en tiempo de Fase 2 para cargas grandes
   - Complejidad estimada: Media (requiere gestión de concurrencia y locks en DB)

2. **Cache de Validaciones**
   - Actualmente: Cada registro hace queries individuales a Cosmos/Oracle para validaciones
   - Mejora propuesta: Implementar cache distribuido (Redis) para catálogos, pólizas master, configuraciones
   - Beneficio esperado: Reducción de 30-50% en latencia de validaciones Fase 2
   - Complejidad estimada: Media (requiere infraestructura Redis + invalidación de cache)

3. **Streaming de Archivos (chunked upload)**
   - Actualmente: Archivo completo se sube antes de iniciar Fase 1
   - Mejora propuesta: Iniciar validación Fase 1 mientras se están subiendo bloques finales
   - Beneficio esperado: Reducción de 20-30% en tiempo total para archivos grandes (> 10MB)
   - Complejidad estimada: Alta (requiere cambios en DataFactory triggers y coordinación)

4. **Notificaciones Push en Dashboard**
   - Actualmente: Usuario debe refrescar manualmente el dashboard
   - Mejora propuesta: Implementar WebSockets o Server-Sent Events para actualización en tiempo real
   - Beneficio esperado: Mejor experiencia de usuario, visibilidad instantánea de cambios
   - Complejidad estimada: Media (requiere infraestructura WebSocket en PolicyCenter)

5. **Auto-corrección de Errores Comunes**
   - Actualmente: Todos los errores requieren intervención manual
   - Mejora propuesta: Implementar reglas de auto-corrección para errores comunes (ej: formato de fechas, trim de espacios)
   - Beneficio esperado: Reducción de 10-20% en tasa de error Fase 1
   - Complejidad estimada: Baja (lógica de transformación en DataFactory)

6. **Historización y Auditoría Mejorada**
   - Actualmente: TTL de 90 días en Cosmos DB, sin histórico detallado
   - Mejora propuesta: Implementar Data Lake para almacenamiento a largo plazo de auditoría
   - Beneficio esperado: Cumplimiento normativo, análisis de tendencias históricos
   - Complejidad estimada: Media (requiere pipeline ETL Cosmos → Data Lake)

### Roadmap de Evolución

```mermaid
timeline
    title Roadmap de Optimización del Flujo de Carga Masiva

    Q1 2026 : Fase de Quick Wins
            : Implementar auto-corrección de errores comunes (Fase 1)
            : Optimizar queries Cosmos DB con índices adicionales
            : Configurar alertas proactivas en Application Insights

    Q2 2026 : Fase de Performance
            : Implementar cache distribuido (Redis) para validaciones
            : Paralelización de Workqueues con Thread Pool
            : Optimización de servicios Edge (revisión de queries SQL)

    Q3 2026 : Fase de UX
            : Notificaciones push en dashboard (WebSockets)
            : Streaming de archivos con validación temprana
            : Mejoras en UI del dashboard (filtros, búsqueda, exportación)

    Q4 2026 : Fase de Governance
            : Implementar Data Lake para auditoría a largo plazo
            : Dashboard analítico de tendencias y métricas históricas
            : Implementar ML para predicción de errores comunes
```

---

## 📚 **Referencias**

- **GPS Arquitectónico**: [Arquitectura del Sistema - GPS Principal](./index.md)
- **Documentación de Componentes**:
  - [PolicyCenter - Arquitectura Detallada](./architecture-policycenter.md)
  - [VidaGrupoIAC - Infraestructura como Código](./architecture-VidaGrupoIAC.md)
- **Configuración**: 
  - PolicyCenter: `./PolicyCenter/modules/configuration/config/web/pcf/`
  - VidaGrupoIAC: `./VidaGrupoIAC/modules/`
- **Código Fuente**:
  - JavaScript Client: `./PolicyCenter/modules/configuration/deploy/resources/javascript/massiveLoad.js`
  - Workqueue Enrichment: `./PolicyCenter/modules/configuration/gsrc/sura/pc/massiveupload/batch/enrichment/EnrichmentProcessing.gs`
  - Workqueue Message Process: `./PolicyCenter/modules/configuration/gsrc/sura/pc/massiveupload/plugin/messaging/MassiveUploadMessageProcess.gs`
  - Edge Processing: `./PolicyCenter/modules/configuration/gsrc/sura/pc/massiveupload/batch/edge/EdgeProcessing.gs`
  - Data Factory Pipelines: `./VidaGrupoIAC/modules/datafactory/pipelines/`

---

_Documentación generada con Método Ceiba - Arquitecto_  
_Última actualización: 25 de Noviembre, 2025_  
_Versión: 1.0_
