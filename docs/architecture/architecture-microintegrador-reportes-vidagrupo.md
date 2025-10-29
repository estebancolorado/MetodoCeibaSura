# Componente: MicroIntegradorReportesVidaGrupo

## 📋 **Overview**

### Propósito

El MicroIntegradorReportesVidaGrupo es un microservicio especializado construido sobre Apache Camel que se encarga de **generar reportes detallados de cobro para facturas colectivas de pólizas de Vida Grupo**. El componente gestiona la construcción asíncrona de archivos de reporte que pueden contener desde miles hasta millones de registros, utilizando un patrón de work queues programadas para procesar grandes volúmenes de datos de forma eficiente.

### Contexto de Negocio

Dentro del ecosistema de Seguros Sura - Vida Grupo, este microservicio sirve como puente crítico entre **BillingCenter** y la infraestructura de generación de archivos en **Azure (Massive Download API)**. Los reportes de detalle de cobro son documentos esenciales que contienen información granular de cada asegurado dentro de una factura colectiva, incluyendo coberturas, primas, valores asegurados y demás información regulatoria necesaria para clientes corporativos.

Los productos soportados son:
- **Vida Grupo Integral**
- **Deudores**
- **Docentes**  
- **Condiciones de Uso**

### Responsabilidades Principales

- **Generación Asíncrona de Reportes**: Construir reportes de detalle de cobro para facturas colectivas mediante procesamiento distribuido en work queues
- **Gestión de Volúmenes Masivos**: Procesar eficientemente reportes con millones de registros utilizando insert-select optimizados
- **Integración con Azure Massive Download**: Comunicarse con la API de Azure para envío de bloques, cabeceras y cierre de archivos
- **Consulta de Estado**: Proporcionar endpoints para solicitar generación y consultar estado/descarga de reportes
- **Persistencia y Limpieza**: Mantener tablas de control de generación de reportes y limpieza automática de registros antiguos
- **Mensajería Asíncrona**: Notificar cambios de estado del detalle de cobro a aplicaciones externas fuera del dominio que consumen esta funcionalidad vía RabbitMQ

### Ubicación

- **Repositorio**: MetodoCeibaSura
- **Ruta**: `/MicroIntegradorReportesVidaGrupo`
- **Tipo**: Microservicio (Apache Camel)

---

## 🏗️ **Architecture**

### Stack Tecnológico

- **Lenguaje**: Java 17
- **Framework**: Apache Camel 3.20.0
- **Build Tool**: Gradle 6.9.2
- **Base de Datos**: Oracle Database (JDBC 19.8.0.0)
- **Message Broker**: RabbitMQ
- **Containerización**: Docker
- **Logging**: Log4j2 + SLF4J 1.7.36 + Splunk Integration
- **Testing**: JUnit 4.13.2, Mockito 4.11.0
- **Seguridad**: OWASP Dependency Check
- **Mutation Testing**: PIT 1.15.8

### Patrones de Diseño

El componente implementa una arquitectura basada en **patrones empresariales de integración** combinados con **arquitectura hexagonal** (ports & adapters):

- **Patrón Principal**: **Work Queue Pattern** con scheduling programado (Quartz)
  - 4 work queues independientes con responsabilidades específicas
  - Procesamiento batch con volúmenes configurables (2-10,000 registros)
  - Ejecución en intervalos temporales específicos
  
- **Event-Driven Architecture**: Comunicación asíncrona mediante RabbitMQ
- **Hexagonal Architecture**: Separación clara entre dominio, puertos, adaptadores e infraestructura
- **Repository Pattern**: Abstracción del acceso a datos a través de ports (Query/Command)
- **Processor Pattern** (Apache Camel): Lógica de negocio encapsulada en procesadores reutilizables

**Justificación**: El patrón de work queues programadas permite gestionar el procesamiento masivo de datos sin afectar el rendimiento del sistema, mientras que la arquitectura hexagonal facilita testing y mantenibilidad.

### Estructura del Código

```
MicroIntegradorReportesVidaGrupo/
├── src/
│   ├── main/
│   │   ├── java/com/sura/mi/reportes/vidagrupo/
│   │   │   ├── MicrointegratorMain.java          # Punto de entrada principal
│   │   │   ├── application/
│   │   │   │   └── processor/                    # Procesadores de Camel
│   │   │   │       ├── detailcharge/             # Procesadores de generación/consulta
│   │   │   │       └── workqueue/                # Procesadores de work queues (1-4)
│   │   │   ├── domain/                           # Capa de dominio (hexagonal)
│   │   │   │   ├── common/
│   │   │   │   │   ├── constant/                 # Constantes del sistema
│   │   │   │   │   ├── exception/                # Excepciones de dominio
│   │   │   │   │   └── util/                     # Utilidades de negocio
│   │   │   │   ├── dto/                          # Data Transfer Objects
│   │   │   │   ├── enums/                        # Enumeraciones
│   │   │   │   ├── model/                        # Modelos de dominio
│   │   │   │   ├── ports/                        # Ports (interfaces)
│   │   │   │   │   ├── command/                  # Comandos (escritura)
│   │   │   │   │   └── query/                    # Queries (lectura)
│   │   │   │   ├── provider/                     # Proveedores de reglas/mapeos
│   │   │   │   └── service/                      # Servicios de dominio
│   │   │   └── infrastructure/                   # Capa de infraestructura
│   │   │       ├── adapter/                      # Adaptadores (hexagonal)
│   │   │       │   ├── data/                     # Entidades de BD
│   │   │       │   ├── mapper/                   # Mappers Domain ↔ Data
│   │   │       │   ├── repository/               # Implementación de ports
│   │   │       │   │   ├── command/              # Repositorios de escritura
│   │   │       │   │   └── query/                # Repositorios de lectura
│   │   │       │   └── util/                     # Utilidades de BD
│   │   │       ├── config/                       # Configuraciones
│   │   │       └── route/                        # Rutas de Apache Camel
│   │   │           ├── CommonRoute.java          # Rutas comunes/manejo errores
│   │   │           ├── RestApiRoute.java         # API REST expuesta
│   │   │           ├── detailcharge/             # Rutas de generación/consulta
│   │   │           └── workqueue/                # Rutas de work queues (1-4)
│   │   └── resources/
│   │       ├── log4j2.yml                        # Configuración logging
│   │       ├── properties/
│   │       │   └── microintegrator.properties    # Configuración del microservicio
│   │       └── sql/                              # Scripts SQL
│   │           ├── CREATE.sql                    # DDL de tablas
│   │           ├── script_billing.sql            # Consultas BillingCenter
│   │           ├── script_policy.sql             # Consultas PolicyCenter
│   │           └── script_bc_pc.sql              # Consultas combinadas
│   └── test/                                      # Tests unitarios y de integración
├── build.gradle                                   # Configuración de Gradle
├── gradle.properties                              # Versiones centralizadas
├── Dockerfile                                     # Imagen Docker multi-stage
└── README.md                                      # Documentación básica
```

### Diagrama Conceptual

```mermaid
graph TB
    subgraph "Capa de Exposición"
        API[RestApiRoute<br/>Netty HTTP]
    end

    subgraph "Capa de Aplicación - Processors"
        GEN[GenerateDetailChargeProcessor]
        GET[GetDetailChargeProcessor]
        WQ1[WorkQueue 1 Processors]
        WQ2[WorkQueue 2 Processors]
        WQ3[WorkQueue 3 Processors]
        WQ4[WorkQueue 4 Processors]
    end

    subgraph "Capa de Dominio"
        SVC[Services<br/>Reglas de negocio]
        PORTS[Ports<br/>Query/Command]
        PROV[Providers<br/>Reglas de coberturas]
    end

    subgraph "Capa de Infraestructura"
        REPO[Repositories<br/>Command & Query]
        ROUTE[Camel Routes<br/>Quartz Scheduling]
    end

    subgraph "Integraciones Externas"
        DB[(Oracle DB<br/>Tablas Control)]
        DB_GW[(Oracle DB<br/>Guidewire)]
        AZURE[Azure Massive<br/>Download API]
        RABBIT[RabbitMQ]
    end

    API --> GEN
    API --> GET
    ROUTE --> WQ1
    ROUTE --> WQ2
    ROUTE --> WQ3
    ROUTE --> WQ4

    GEN --> SVC
    GET --> SVC
    WQ1 --> SVC
    WQ2 --> SVC
    WQ3 --> SVC
    WQ4 --> SVC

    SVC --> PORTS
    SVC --> PROV
    PORTS --> REPO

    REPO --> DB
    REPO --> DB_GW
    SVC --> AZURE
    WQ3 --> RABBIT

    classDef api fill:#e3f2fd
    classDef processor fill:#fff3e0
    classDef domain fill:#e8f5e9
    classDef infra fill:#f3e5f5
    classDef external fill:#ffebee

    class API api
    class GEN,GET,WQ1,WQ2,WQ3,WQ4 processor
    class SVC,PORTS,PROV domain
    class REPO,ROUTE infra
    class DB,DB_GW,AZURE,RABBIT external
```

---

## 🔌 **APIs**

### Endpoints Expuestos

El componente expone **2 endpoints REST** a través de Apache Camel Netty HTTP.

#### REST Endpoints

| Método | Ruta                                                   | Descripción                                             | Parámetros               | Respuesta          |
| ------ | ------------------------------------------------------ | ------------------------------------------------------- | ------------------------ | ------------------ |
| POST   | `/v1/he/invoices/{invoiceNumber}/chargedetail/report` | Solicita generación de reporte de detalle de cobro      | `{invoiceNumber}` (path) | `200 OK` / `500`   |
| GET    | `/v1/he/invoices/{invoiceNumber}/chargedetail/report` | Consulta y descarga archivo de reporte ya generado      | `{invoiceNumber}` (path) | `200 OK` / `404`   |

**Detalles de Endpoints:**

**POST - Generar Reporte**
- **Descripción**: Registra una solicitud de generación de reporte en la tabla principal con estado 1. El procesamiento real ocurre asíncronamente mediante work queues.
- **Parámetros**:
  - `invoiceNumber` (path): Número de factura colectiva
- **Headers esperados**: `autorizacion`
- **Response**:
  - `200 OK`: Solicitud registrada exitosamente
  - `500 Internal Server Error`: Error al procesar solicitud

**GET - Consultar/Descargar Reporte**
- **Descripción**: Verifica el estado del reporte y descarga el archivo desde Azure Massive Download API si está disponible (estado 4).
- **Parámetros**:
  - `invoiceNumber` (path): Número de factura colectiva
- **Headers esperados**: `autorizacion`
- **Response**:
  - `200 OK`: Archivo disponible y descargado
  - `404 Not Found`: Reporte no encontrado o aún en proceso
  - `500 Internal Server Error`: Error al procesar solicitud

#### Códigos de Error

| Código | Descripción          | Casos                                                 |
| ------ | -------------------- | ----------------------------------------------------- |
| 200    | OK                   | Operación exitosa                                     |
| 400    | Bad Request          | Parámetro invoiceNumber inválido                      |
| 401    | Unauthorized         | Header de autorización inválido/faltante             |
| 404    | Not Found            | Reporte no encontrado o aún no disponible             |
| 500    | Internal Error       | Error de BD, Azure API, o procesamiento interno       |

#### Contratos y Versionamiento

- **Estrategia de versionado**: Versionado mediante prefijo en URL (`/v1/`)
- **Versión actual**: v1
- **Breaking changes**: Cambios en URL o parámetros requerirán nueva versión (v2)

### Eventos y Mensajería

#### Eventos Publicados

| Evento                                      | Descripción                                  | Payload          | Consumidores                          |
| ------------------------------------------- | -------------------------------------------- | ---------------- | ------------------------------------- |
| `sura.seguros.vidagrupo.chargedetail.risk`  | Notificación de cambio de estado del reporte | JSON con estado  | Aplicaciones externas del ecosistema  |

**Detalles del Evento:**

- **Exchange**: `sura.seguros.vidagrupo.chargedetail.ex`
- **Routing Key**: `sura.seguros.vidagrupo.chargedetail.risk`
- **Queue**: `sura.seguros.vidagrupo.chargedetail.risk.qu`
- **Cuándo se publica**: Al completar el cierre de archivo (WorkQueue 3)
- **Propósito**: Notificar a aplicaciones externas fuera del dominio que el reporte ha cambiado de estado y está listo para descarga

#### Eventos Consumidos

| Evento                                            | Descripción                        | Origen                   | Acción                              |
| ------------------------------------------------- | ---------------------------------- | ------------------------ | ----------------------------------- |
| `sura.seguros.vidagrupo.chargedetail.risk.reply`  | Respuesta de aplicaciones externas | Aplicaciones consumidoras | Procesar confirmación de recepción  |

**Detalles del Evento Consumido:**

- **Exchange**: `sura.seguros.vidagrupo.chargedetail.ex`
- **Routing Key**: `sura.seguros.vidagrupo.chargedetail.risk.reply`
- **Queue**: `sura.seguros.vidagrupo.chargedetail.risk.reply.qu`
- **Acción**: Registrar confirmación de que la aplicación externa recibió la notificación de cambio de estado

---

## 📦 **Dependencies**

### Dependencias Externas

#### Librerías Críticas

| Librería                             | Versión     | Propósito                                  | Criticidad     |
| ------------------------------------ | ----------- | ------------------------------------------ | -------------- |
| Apache Camel (Suite completa)        | 3.20.0      | Framework de integración y orquestación    | 🔴 Crítica     |
| Oracle JDBC Driver (ojdbc8)          | 19.8.0.0    | Conectividad con bases de datos Oracle     | 🔴 Crítica     |
| Quartz Scheduler                     | 2.3.2       | Scheduling de work queues programadas      | 🔴 Crítica     |
| Apache Commons DBCP2                 | 2.9.0       | Pool de conexiones de BD                   | 🔴 Crítica     |
| Jackson Datatype JDK8                | 2.13.3      | Serialización/deserialización JSON         | 🟡 Importante  |
| OkHttp                               | 4.10.0      | Cliente HTTP para Azure API                | 🟡 Importante  |
| ms-lib-seus                          | 1.0.1       | Librería interna de Seguros Sura           | 🟡 Importante  |
| Lombok                               | 1.18.26     | Reducción de boilerplate                   | 🟢 Opcional    |
| SLF4J + Log4j2                       | 1.7.36      | Logging y observabilidad                   | 🟡 Importante  |
| Splunk Java Logging                  | 1.7.3       | Envío de logs a Splunk                     | 🟢 Opcional    |

#### Servicios Externos

- **Azure Massive Download API**: API de Microsoft Azure para generación y almacenamiento de archivos masivos. Se utiliza para enviar bloques de datos, cerrar archivos y consultar disponibilidad.
  - **Base URL**: `https://labapicorevidagrupo.suramericana.com/massive-download`
  - **Criticidad**: 🔴 Crítica (sin este servicio no se pueden generar reportes)
  
- **Splunk (Holmes Lab)**: Plataforma de observabilidad para centralización de logs
  - **URL**: `http://holmeslab.suramericana.com.co:9088/`
  - **Criticidad**: 🟢 Opcional (útil para troubleshooting)

### Dependencias Internas

#### Componentes del Sistema

- **BillingCenter (Guidewire)**: Sistema core de facturación. Se consulta su base de datos Oracle para obtener información de facturas colectivas, items de factura, coberturas y períodos.
- **PolicyCenter (Guidewire)**: Sistema core de pólizas. Se consulta su base de datos Oracle para obtener información de asegurados, pólizas maestras, productos y condiciones.

#### Bases de Datos

- **Oracle DB - Tablas de Control (ADM_VIDAGRUPOREPORTES)**: 
  - `CHARGE_DETAIL_PRINCIPAL`: Tabla principal de control de generación de reportes
  - `CHARGE_DETAIL_ITEMS`: Tabla de items/registros del detalle de cobro
  
- **Oracle DB - BillingCenter**: 
  - Consulta tablas de: `bc_invoice`, `bc_invoiceitem`, `bc_policyperiod`, `bc_accountlocation`, entre otras

- **Oracle DB - PolicyCenter**: 
  - Consulta tablas de: `pc_policy`, `pc_policyline`, `pc_policyperiod`, `pc_grouppersonnel`, `pc_coverage`, entre otras

### Quién Usa Este Componente

#### Consumidores Directos

- **Aplicaciones Externas del Ecosistema**: Aplicaciones fuera del dominio de Guidewire que solicitan generación de reportes de detalle de cobro mediante los endpoints REST y reciben notificaciones de cambios de estado vía RabbitMQ.
- **BillingCenter (Guidewire)**: Puede solicitar generación de reportes de detalle de cobro al facturar pólizas colectivas a través de los endpoints REST.
- **Azure Data Factory (potencial)**: Podría orquestar llamadas al servicio para generación programada de reportes.
- **Portales de Clientes Corporativos**: Sistemas externos que permiten a usuarios finales consultar y descargar reportes de detalle de cobro.

#### Consumidores Indirectos

- **Equipos de Auditoría y Cumplimiento**: Usan los reportes generados para validación regulatoria
- **Equipos de Soporte**: Consultan reportes para atención de casos

### Gestión de Dependencias

```bash
# Actualizar dependencias (desde build.gradle)
./gradlew dependencies --refresh-dependencies

# Verificación de vulnerabilidades (OWASP Dependency Check)
./gradlew dependencyCheckAnalyze

# Auditoría de dependencias
./gradlew dependencyReports

# Construcción completa con tests
./gradlew clean build test jacocoTestReport
```

---

## 🚀 **Deployment**

### Configuración de Entorno

#### Variables de Entorno Requeridas

| Variable                                   | Descripción                                       | Ejemplo                                              | Requerida  |
| ------------------------------------------ | ------------------------------------------------- | ---------------------------------------------------- | ---------- |
| `ARTIFACTORY_TOKEN`                        | Token para acceder a Artifactory de Sura          | `<token-secreto>`                                    | ✅ Sí      |
| `oracle.jdbc.url`                          | URL de conexión a Oracle                          | `jdbc:oracle:thin:@//host:1537/SERVICENAME`          | ✅ Sí      |
| `oracle.jdbc.username`                     | Usuario de base de datos                          | `ADM_VIDAGRUPOREPORTES`                              | ✅ Sí      |
| `oracle.jdbc.password`                     | Contraseña de base de datos                       | `<password-secreto>`                                 | ✅ Sí      |
| `spring.rabbitmq.consumer.host`            | Host de RabbitMQ (consumer)                       | `msglab.suramericana.com.co`                         | ✅ Sí      |
| `spring.rabbitmq.consumer.username`        | Usuario RabbitMQ (consumer)                       | `seguros.core.policy.usr`                            | ✅ Sí      |
| `spring.rabbitmq.consumer.password`        | Password RabbitMQ (consumer)                      | `<password-secreto>`                                 | ✅ Sí      |
| `spring.rabbitmq.producer.host`            | Host de RabbitMQ (producer)                       | `msglab.suramericana.com.co`                         | ✅ Sí      |
| `spring.rabbitmq.producer.username`        | Usuario RabbitMQ (producer)                       | `seguros.core.billing.usr`                           | ✅ Sí      |
| `spring.rabbitmq.producer.password`        | Password RabbitMQ (producer)                      | `<password-secreto>`                                 | ✅ Sí      |
| `api.massive.download.url`                 | Base URL de Azure Massive Download API            | `https://labapicorevidagrupo.suramericana.com/...`   | ✅ Sí      |
| `api.massive.download.authorization`       | Header de autorización para Azure API             | `Basic <base64-encoded-credentials>`                 | ✅ Sí      |
| `api.massive.download.subscription.key`    | API Key de Azure                                  | `f8b7a06d70904951b04f35f433f038c6`                   | ✅ Sí      |
| `SPLUNK_SERVICE_URL`                       | URL del servicio Splunk                           | `http://holmeslab.suramericana.com.co:9088/`         | ⚠️ Opcional |
| `SPLUNK_SERVICE_SSO`                       | SSO Token de Splunk                               | `A763C54B-44BB-4F6C-8C1D-0A6A309D66D3`               | ⚠️ Opcional |

#### Archivo .env de Ejemplo

```bash
# Configuración de desarrollo
ARTIFACTORY_TOKEN=your-artifactory-token-here

# Base de datos Oracle
oracle.jdbc.url=jdbc:oracle:thin:@//srcsbdl07.suramericana.com.co:1537/LABGWDWH
oracle.jdbc.username=ADM_VIDAGRUPOREPORTES
oracle.jdbc.password=your-db-password-here
oracle.jdbc.initialSize=5
oracle.jdbc.maxTotal=10
oracle.jdbc.maxConnLifetimeMillis=18300000
oracle.jdbc.removeAbandonedTimeout=18300

# RabbitMQ - Consumer
spring.rabbitmq.consumer.host=msglab.suramericana.com.co
spring.rabbitmq.consumer.port=5672
spring.rabbitmq.consumer.username=seguros.core.policy.usr
spring.rabbitmq.consumer.password=your-rabbit-password-here
spring.rabbitmq.consumer.virtual-host=seguros.vh

# RabbitMQ - Producer
spring.rabbitmq.producer.host=msglab.suramericana.com.co
spring.rabbitmq.producer.port=5672
spring.rabbitmq.producer.username=seguros.core.billing.usr
spring.rabbitmq.producer.password=your-rabbit-password-here
spring.rabbitmq.producer.virtual-host=seguros.vh

# Azure Massive Download API
api.massive.download.url=https://labapicorevidagrupo.suramericana.com/massive-download
api.massive.download.authorization=Basic Y29yZWd3OmNvOHJlOTUxKkcvNjUwdw==
api.massive.download.subscription.key=f8b7a06d70904951b04f35f433f038c6

# Splunk
SPLUNK_SERVICE_URL=http://holmeslab.suramericana.com.co:9088/
SPLUNK_SERVICE_SSO=A763C54B-44BB-4F6C-8C1D-0A6A309D66D3

# Servidor
server.port=9000
environment=local
```

### Comandos de Desarrollo

#### Setup Inicial

```bash
# Clonar repositorio
git clone <repository-url>
cd MicroIntegradorReportesVidaGrupo

# Verificar versión de Java (requiere Java 17)
java -version

# Configurar variables de entorno
export ARTIFACTORY_TOKEN=<your-token>

# Instalar dependencias
./gradlew build --refresh-dependencies
```

#### Compilación

```bash
# Build de desarrollo (sin tests)
./gradlew shadowJar

# Build de producción (con tests y coverage)
./gradlew clean build test jacocoTestReport

# Verificación de build
ls -lh build/libs/*.jar
```

#### Testing

```bash
# Tests unitarios
./gradlew test

# Tests de integración (si existen)
./gradlew integrationTest

# Coverage (JaCoCo)
./gradlew jacocoTestReport
# Reporte en: build/reports/jacocoHtml/index.html

# Linting / Dependency Check
./gradlew dependencyCheckAnalyze

# Mutation Testing (PIT)
./gradlew pitest
# Reporte en: target/pit-reports/pitest/index.html
```

#### Ejecución Local

```bash
# Modo desarrollo (usando Gradle)
./gradlew runWithJavaExec

# Modo producción local (JAR compilado)
java -jar build/libs/vidagruporeportes-mi-1.0-SNAPSHOT-all.jar

# Debug mode (con puerto de debugging)
java -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005 \
  -jar build/libs/vidagruporeportes-mi-1.0-SNAPSHOT-all.jar

# Ejecución con Docker local
docker build -t microintegrador-reportes-vidagrupo:local .
docker run -p 9000:9000 \
  -e oracle.jdbc.url="jdbc:oracle:thin:@//host:1537/SERVICENAME" \
  -e oracle.jdbc.username="ADM_VIDAGRUPOREPORTES" \
  -e oracle.jdbc.password="password" \
  microintegrador-reportes-vidagrupo:local
```

### Pipeline de Despliegue

#### Prerequisitos de Infraestructura

- **Oracle Database**: Base de datos Oracle con esquema `ADM_VIDAGRUPOREPORTES` creado y tablas `CHARGE_DETAIL_PRINCIPAL` y `CHARGE_DETAIL_ITEMS` inicializadas
- **RabbitMQ**: Virtual host `seguros.vh` configurado con exchanges y queues necesarias
- **Azure Massive Download API**: Endpoint accesible y credenciales configuradas
- **Container Registry**: Acceso a registry de Docker para push de imágenes
- **Kubernetes/Openshift Cluster**: Cluster configurado para deployment del microservicio

#### Etapas del Pipeline

1. **Build Stage**
   - Compilación del código fuente con Gradle
   - Ejecución de tests unitarios
   - Generación de reporte de coverage (JaCoCo)
   - Análisis de vulnerabilidades (OWASP Dependency Check)
   - Comandos: `./gradlew clean build test jacocoTestReport dependencyCheckAnalyze`

2. **Test Stage**
   - Ejecución de tests de integración (si existen)
   - Mutation testing con PIT
   - Validación de cobertura mínima requerida
   - Comandos: `./gradlew integrationTest pitest`

3. **Package Stage**
   - Construcción de fat JAR con ShadowJar
   - Build de imagen Docker multi-stage
   - Tag de imagen con versión y commit SHA
   - Comandos: `docker build -t <registry>/microintegrador-reportes-vidagrupo:${VERSION} .`

4. **Deploy Stage**
   - Push de imagen a container registry
   - Deployment a Kubernetes/Openshift
   - Health checks y smoke tests
   - Comandos: `kubectl apply -f k8s/deployment.yaml && kubectl rollout status deployment/microintegrador-reportes`

#### Variables de Entorno por Ambiente

**Desarrollo:**

```bash
environment=dev
oracle.jdbc.url=jdbc:oracle:thin:@//dev-db-host:1537/DEVGWDWH
api.massive.download.url=https://devapicorevidagrupo.suramericana.com/massive-download
spring.rabbitmq.consumer.host=msgdev.suramericana.com.co
server.port=9000
```

**Staging:**

```bash
environment=staging
oracle.jdbc.url=jdbc:oracle:thin:@//stg-db-host:1537/STGGWDWH
api.massive.download.url=https://stgapicorevidagrupo.suramericana.com/massive-download
spring.rabbitmq.consumer.host=msgstg.suramericana.com.co
server.port=9000
```

**Producción:**

```bash
environment=production
oracle.jdbc.url=jdbc:oracle:thin:@//prod-db-host:1537/PRODGWDWH
api.massive.download.url=https://apicorevidagrupo.suramericana.com/massive-download
spring.rabbitmq.consumer.host=msg.suramericana.com.co
server.port=9000
```

### Buenas Prácticas de Despliegue

- **Health Checks Configurados**: Implementar endpoints `/actuator/health` para validación de disponibilidad del servicio
- **Graceful Shutdown**: Configurar Apache Camel para shutdown ordenado de rutas antes de detener el contenedor
- **Resource Limits**: Definir límites de CPU y memoria en Kubernetes para evitar consumo excesivo (recomendado: 2 CPU, 4GB RAM)
- **Secrets Management**: Utilizar Kubernetes Secrets o Azure Key Vault para gestión de credenciales sensibles (nunca hardcodear passwords)
- **Horizontal Scaling**: Configurar múltiples réplicas (mínimo 2) con balanceo de carga para alta disponibilidad
- **Rolling Updates**: Despliegues sin downtime mediante rolling updates en Kubernetes
- **Monitoring Activo**: Integrar con Splunk y herramientas de APM para monitoreo de performance y errores

### Pasos Manuales

**Inicialización de Tablas de Control (Primera vez):**

1. **Conectar a Oracle como usuario ADM_VIDAGRUPOREPORTES**
2. **Ejecutar script SQL**: `src/main/resources/sql/CREATE.sql`
3. **Verificar creación de tablas**: 
   ```sql
   SELECT table_name FROM user_tables WHERE table_name IN ('CHARGE_DETAIL_PRINCIPAL', 'CHARGE_DETAIL_ITEMS');
   ```
4. **Conceder permisos de lectura** a esquemas de Guidewire (BC, PC) si es necesario

**Configuración de RabbitMQ (Primera vez):**

1. **Crear virtual host** `seguros.vh` si no existe
2. **Crear exchange** `sura.seguros.vidagrupo.chargedetail.ex` (tipo: topic, durable: true)
3. **Crear queues**:
   - `sura.seguros.vidagrupo.chargedetail.risk.qu`
   - `sura.seguros.vidagrupo.chargedetail.risk.reply.qu`
4. **Configurar bindings** con routing keys correspondientes
5. **Configurar permisos** para usuarios `seguros.core.policy.usr` y `seguros.core.billing.usr`

### Rollback

```bash
# Rollback en Kubernetes (volver a versión anterior)
kubectl rollout undo deployment/microintegrador-reportes-vidagrupo

# Verificación post-rollback
kubectl rollout status deployment/microintegrador-reportes-vidagrupo
kubectl get pods -l app=microintegrador-reportes-vidagrupo

# Rollback de imagen Docker específica
kubectl set image deployment/microintegrador-reportes-vidagrupo \
  microintegrador-reportes-vidagrupo=<registry>/microintegrador-reportes-vidagrupo:1.0.2

# Verificar logs después del rollback
kubectl logs -f deployment/microintegrador-reportes-vidagrupo
```

### Monitoreo Post-Despliegue

- **Health Checks**: 
  ```bash
  # Verificar servicio activo
  curl http://localhost:9000/actuator/health
  
  # Verificar endpoint REST
  curl -X GET http://localhost:9000/v1/he/invoices/TEST12345/chargedetail/report \
    -H "autorizacion: password"
  ```

- **Logs**: 
  ```bash
  # Logs en Kubernetes
  kubectl logs -f deployment/microintegrador-reportes-vidagrupo --tail=100
  
  # Logs en Splunk (query)
  index=vida_grupo source="microintegrador-reportes" | head 100
  ```

- **Métricas**: 
  ```bash
  # Verificar work queues ejecutándose
  kubectl logs deployment/microintegrador-reportes-vidagrupo | grep "WorkQueue"
  
  # Monitoreo de base de datos
  SELECT status, COUNT(*) 
  FROM ADM_VIDAGRUPOREPORTES.CHARGE_DETAIL_PRINCIPAL 
  GROUP BY status;
  ```

- **RabbitMQ Management**:
  ```bash
  # Verificar colas y mensajes
  # Acceder a RabbitMQ Management UI: http://msglab.suramericana.com.co:15672
  # Verificar mensajes en cola: seguros.vidagrupo.chargedetail.risk.qu
  ```

---

**📌 Esta documentación debe mantenerse actualizada con cada cambio significativo en el componente.**

## 📝 **Notas de Mantenimiento**

| Fecha       | Versión | Cambios                                           | Autor                  |
| ----------- | ------- | ------------------------------------------------- | ---------------------- |
| 2025-10-28  | 1.0     | Documentación inicial del componente completo     | Arquitecto Ceiba (AI)  |

---

_Documentación generada con Método Ceiba - Arquitecto_
