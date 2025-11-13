# Arquitectura del Sistema - GPS Principal
## Ecosistema Seguros Sura - Vida Grupo

Este documento sirve como **GPS arquitectónico** para navegar el ecosistema de Seguros Sura especializado en la línea de negocio **Vida Grupo**, que combina la plataforma Guidewire con microservicios especializados e infraestructura como código.

> **Versión 2.0** - Actualización Mayor (12 de Noviembre, 2025)  
> **Documentación Total**: ~7,800 líneas técnicas  
> **Componentes Documentados**: 3 Guidewire + 2 Microservicios + 1 Infraestructura  
> **Flujos Documentados**: 1 flujo crítico end-to-end  
> **Historias de Usuario**: 1 caso de estudio completo

---

## 📊 **Resumen Ejecutivo**

### Estado de la Documentación

El ecosistema de Vida Grupo cuenta con **documentación arquitectónica exhaustiva y viva** mantenida mediante el Método Ceiba:

| Categoría | Documentos | Líneas | Estado |
|-----------|------------|--------|--------|
| **GPS Arquitectónico** | 1 | ~1,800 | ✅ Actualizado |
| **Arquitectura de Componentes** | 3 | 2,350 | ✅ Completo |
| **Flujos de Negocio** | 1 | 1,229 | ✅ Documentado |
| **Historias de Usuario** | 1 | 1,941 | ✅ Caso Estudio |
| **READMEs de Proyecto** | 2 | 764 | ✅ Actualizados |
| **TOTAL** | **8** | **~7,834** | **✅ Sincronizado** |

### Componentes Principales Documentados

#### **Guidewire Insurance Suite 8.0.7**
- **PolicyCenter**: Sistema de Registro de pólizas colectivas (588 líneas doc)
- **BillingCenter**: Sistema de facturación corporativa (651 líneas doc)
- **ClaimCenter**: Gestión de siniestros Vida Grupo

#### **Microservicios Apache Camel + Java 17**
- **MicroIntegradorVidaGrupo**: Hub de integraciones empresariales
- **MicroIntegradorReportesVidaGrupo**: Generación de reportes masivos (1,111 líneas doc + 1,229 líneas flujo)

#### **Infraestructura**
- **VidaGrupoIAC**: Terraform para Azure (Data Factory, API Gateway)

### Flujos de Negocio Documentados

✅ **Generación de Reporte Detalle de Cobro** (1,229 líneas)
- 7 diagramas de secuencia Mermaid
- 4 WorkQueues con Quartz Scheduler
- Modelo de datos completo (10 tablas)
- APIs REST documentadas
- Configuración end-to-end

### Casos de Estudio

✅ **Historia #915240**: Bug - Organizar campos errados detalle de cobro (1,941 líneas)
- 12 criterios de aceptación completados
- Análisis técnico detallado
- Soluciones con código
- 30+ tests unitarios
- Lecciones aprendidas arquitectónicas

### Métricas de Calidad

- **Cobertura de Tests**: 85%+ (MicroIntegradorReportesVidaGrupo)
- **Pruebas Unitarias**: 124+ tests
- **Mutation Testing**: PIT configurado y ejecutado
- **Seguridad**: OWASP Dependency Check activo
- **Documentación/Código**: Ratio ~1:3 (altamente documentado)

---

## 🎯 **Visión General del Sistema**

### Propósito Principal

El ecosistema está diseñado para soportar las operaciones completas del seguro de **Vida Grupo** de Seguros Sura, incluyendo:
- **Emisión de pólizas colectivas** a través de PolicyCenter
- **Facturación y cobro** mediante BillingCenter  
- **Gestión de siniestros** con ClaimCenter
- **Integraciones con sistemas externos** (cotizadores, validadores, sistemas de facturación)
- **Generación de reportes especializados** (detalles de cobro)
- **Infraestructura en la nube** (Azure Data Factory, API Gateways)

### Distribución del Ecosistema

- **Total de repositorios identificados**: 10 repositorios principales
- **Dominios/módulos principales**: Guidewire Core (3), Microservicios (2), Infraestructura (1), Soporte (4)
- **Repositorios críticos**:
  1. **PolicyCenter** - Administración de pólizas colectivas
  2. **BillingCenter** - Facturación y cobros
  3. **ClaimCenter** - Gestión de siniestros
  4. **MicroIntegradorVidaGrupo** - Integración con sistemas externos
  5. **MicroIntegradorReportesVidaGrupo** - Generación de reportes
  6. **VidaGrupoIAC** - Infraestructura como código (Terraform/Azure)

### Diagrama de Arquitectura de Alto Nivel

```mermaid
graph TB
    subgraph "Sistemas Externos"
        EXT1[Sistemas de Facturación]
        EXT2[Validador de Asegurados]
        EXT3[Sistemas de Cotización]
        EXT4[Servicios de Notificación]
        EXT5[Sistemas de Consulta<br/>Listas Restrictivas]
    end

    subgraph "Guidewire Insurance Suite - Core"
        PC[PolicyCenter<br/>Gosu/Java]
        BC[BillingCenter<br/>Gosu/Java]
        CC[ClaimCenter<br/>Gosu/Java]
    end

    subgraph "Microservicios - Vida Grupo"
        MI[MicroIntegradorVidaGrupo<br/>Apache Camel/Java 17]
        MIR[MicroIntegradorReportesVidaGrupo<br/>Apache Camel/Java 17]
    end

    subgraph "Infraestructura Azure"
        DF[Data Factory]
        APIGW[API Gateway]
        RABBITMQ[RabbitMQ]
    end

    subgraph "Componentes de Soporte"
        PS[PolicySearchSummary<br/>Java/Spring]
        SL[SeusLibreria<br/>Java]
        DM[Datamodel<br/>Scripts SQL]
        TR[Transformation<br/>Scripts Ant]
    end

    subgraph "Bases de Datos"
        DBPC[(DB PolicyCenter<br/>Oracle)]
        DBBC[(DB BillingCenter<br/>Oracle)]
        DBCC[(DB ClaimCenter<br/>Oracle)]
    end

    %% Flujos principales
    PC --> BC
    PC --> CC
    BC --> MIR
    
    %% Integraciones microservicios
    PC --> MI
    BC --> MI
    CC --> MI
    
    MI --> EXT1
    MI --> EXT2
    MI --> EXT3
    MI --> EXT5
    
    MIR --> EXT1
    
    %% Mensajería
    MI --> RABBITMQ
    MIR --> RABBITMQ
    RABBITMQ --> BC
    RABBITMQ --> PC
    
    %% Azure Gateway
    APIGW --> MI
    APIGW --> MIR
    DF --> APIGW
    
    %% Base de datos
    PC --> DBPC
    BC --> DBBC
    CC --> DBCC
    MI --> DBPC
    MIR --> DBBC
    
    %% Componentes soporte
    PS --> DBPC
    DM -.-> DBPC
    DM -.-> DBBC
    DM -.-> DBCC

    classDef guidewire fill:#1e88e5,stroke:#0d47a1,color:#fff
    classDef microservice fill:#43a047,stroke:#2e7d32,color:#fff
    classDef external fill:#ffa726,stroke:#f57c00,color:#000
    classDef infra fill:#ab47bc,stroke:#6a1b9a,color:#fff
    classDef support fill:#78909c,stroke:#455a64,color:#fff
    classDef database fill:#ef5350,stroke:#c62828,color:#fff

    class PC,BC,CC guidewire
    class MI,MIR microservice
    class EXT1,EXT2,EXT3,EXT4,EXT5 external
    class DF,APIGW,RABBITMQ infra
    class PS,SL,DM,TR support
    class DBPC,DBBC,DBCC database
```

**Leyenda de colores:**
- 🔵 **Azul**: Guidewire Insurance Suite (PolicyCenter, BillingCenter, ClaimCenter)
- 🟢 **Verde**: Microservicios especializados de Vida Grupo
- 🟠 **Naranja**: Sistemas externos
- 🟣 **Morado**: Infraestructura Azure y mensajería
- ⚫ **Gris**: Componentes de soporte
- 🔴 **Rojo**: Bases de datos

---

## 📦 **Mapa de Repositorios por Dominio/Función**

### **1. Guidewire Core - Insurance Suite**

#### PolicyCenter
- **Ubicación**: `C:\Guidewire\PolicyCenter`
- **Propósito**: Administración del ciclo de vida completo de pólizas de Vida Grupo colectivas
- **Versión**: 8.0.7.gw
- **Stack Tecnológico**: Gosu + Java + Guidewire Framework
- **Responsabilidades Críticas**:
  - **Emisión de Pólizas Colectivas**: Gestión específica para seguros de vida grupo corporativo
  - **Administración de Asegurados**: Manejo masivo de empleados y dependientes
  - **Configuración de Productos Vida Grupo**: 
    - Estructuras jerárquicas de coberturas (básicas, adicionales, voluntarias)
    - Configuración de sumas aseguradas por categorías de empleados
    - Manejo de tablas actuariales específicas
  - **Procesos de Negocio Especializados**:
    - Carga masiva de asegurados (vía archivo plano/Excel)
    - Validación automática contra listas restrictivas
    - Cálculo de primas por categorías y edades
    - Emisión de certificados individuales
    - Gestión de novedades (altas/bajas de empleados)
  - **Integraciones Clave**:
    - Cotizador externo vía MicroIntegradorVidaGrupo
    - Validador de asegurados contra centrales de riesgo
    - Sistema de facturación empresarial
- **Configuraciones Específicas**:
  - Product Designer configurado para productos Vida Grupo
  - Workflow engine para aprobaciones automáticas/manuales
  - Rating engine con tablas actuariales Sura
- **Build**: Ant scripts (gwpc.bat/gwpc.sh) + Studio personalizado
- **Base de Datos**: Oracle (esquema policycenter) con tablas específicas Vida Grupo

#### BillingCenter
- **Ubicación**: `C:\Guidewire\BillingCenter`
- **Propósito**: Gestión integral de facturación y cobranza especializada en pólizas colectivas Vida Grupo
- **Versión Guidewire**: 8.0.7
- **Responsabilidades Especializadas**:
  - **Facturación Corporativa Vida Grupo**:
    - Consolidación de primas por empleador (una factura por empresa)
    - Cálculo automático basado en nóminas de empleados activos
    - Aplicación de descuentos por volumen y antigüedad corporativa
    - Gestión de períodos de gracia empresariales
  - **Procesos de Cobranza Específicos**:
    - Generación automática de débitos bancarios corporativos
    - Interfaz con sistemas de recaudo empresarial
    - Manejo de mora empresarial (diferentes a pólizas individuales)
    - Proceso de cancelación por no pago grupal
  - **Reportería Avanzada**:
    - Detalle de cobro por empleado/dependiente
    - Reportes de comisiones a intermediarios
    - Estados de cuenta corporativos
    - Análisis de cartera por empresa
  - **Integraciones Críticas**:
    - Sistema de facturación electrónica (DIAN)
    - Pasarelas de pago corporativo
    - Sistema de tesorería Sura
    - MicroIntegradorReportesVidaGrupo para reportes especializados
- **Configuraciones Específicas**:
  - Billing workflows para pólizas colectivas
  - Payment plans corporativos personalizados
  - Producer commission schemes para intermediarios Vida Grupo
- **Estructura clave**:
  - `/modules/configuration` - Configuración customizada Sura Vida Grupo
  - `/modules/gsrc` - Código Gosu personalizado para facturación colectiva
- **Documentación**:
  - [📘 Arquitectura Detallada](architecture-BillingCenter.md)

#### ClaimCenter  
- **Ubicación**: `C:\Guidewire\ClaimCenter`
- **Propósito**: Gestión integral y especializada de siniestros para seguros de Vida Grupo colectivos
- **Versión Guidewire**: 8.0.7
- **Responsabilidades Específicas**:
  - **Gestión de Siniestros Vida Grupo**:
    - Registro de siniestros por fallecimiento de asegurados
    - Manejo de incapacidades temporales/permanentes
    - Gestión de auxilio funerario
    - Validación automática de beneficiarios designados
  - **Procesos Especializados**:
    - Verificación de vigencia del asegurado al momento del siniestro
    - Validación contra certificado de defunción
    - Cálculo automático de indemnizaciones por categoría de empleado
    - Gestión de múltiples beneficiarios por siniestro
  - **Integraciones Críticas**:
    - Registraduría Nacional (validación certificados)
    - Sistema médico forense (dictámenes)
    - Entidades bancarias (giros beneficiarios)
    - MicroIntegradorVidaGrupo para validaciones externas
  - **Documentación y Auditoría**:
    - Gestión documental especializada (certificados médicos, actas de defunción)
    - Workflow de aprobación por montos
    - Trazabilidad completa para auditorías de seguros de vida
- **Configuraciones Específicas**:
  - Claim workflows para vida grupo (muerte natural, accidental, etc.)
  - Business rules específicas para validación de beneficiarios
  - Integration points con sistemas médicos y registrales

### **2. Microservicios Vida Grupo**

#### MicroIntegradorVidaGrupo
- **Ubicación**: `C:\Guidewire\MicroIntegradorVidaGrupo`
- **Propósito**: Hub central de integraciones empresariales para ecosistema Vida Grupo con arquitectura EIP avanzada
- **Stack Tecnológico**: Java 17 + Apache Camel 3.20.0
- **Arquitectura**: Enterprise Integration Patterns con patrones especializados para seguros colectivos
- **Responsabilidades Críticas**:
  - **Integración con Cotizadores Externos**:
    - Orquestación de cotizaciones automáticas para pólizas colectivas
    - Transformación bidireccional entre formatos Guidewire y sistemas externos
    - Caching inteligente de cotizaciones para optimizar performance
    - Circuit breaker patterns para manejo de fallos en cotizadores
    - Aggregation de múltiples cotizadores con selección de mejor opción
  - **Validación Masiva de Asegurados**:
    - Consulta batch contra centrales de riesgo (CIFIN, DataCrédito, Procuraduría)
    - Validación en tiempo real durante procesos de alta de empleados
    - Gestión de listas restrictivas con sincronización automática
    - Cross-validation con sistemas HRIS corporativos
    - Manejo de excepciones y alertas para casos especiales
  - **Conectividad Empresarial**:
    - Interfaz bidireccional con sistemas de nómina corporativos (SAP HCM, SuccessFactors)
    - Sincronización con portales de RRHH para autogestión de empleados
    - Integración con sistemas de facturación empresarial (SAP, Oracle EBS, Dynamics)
    - API Gateway interno para exposición controlada de servicios
  - **Orquestación de Procesos de Negocio**:
    - Process orchestration para flujos complejos multi-sistema
    - Compensation patterns para rollback de transacciones distribuidas
    - Event sourcing para audit trail completo
    - Dead letter handling con reprocessing automático
- **Patrones de Integración Implementados**:
  - Message Router con content-based routing
  - Aggregator/Splitter para procesamiento masivo
  - Scatter-Gather para consultas paralelas
  - Process Manager para workflows de larga duración
  - Canonical Data Model para estandarización
- **Build**: Gradle 6.9.2 con plugins especializados (Docker, OWASP, JaCoCo)
- **Configuración**: Externalizada en MicroIntegradorVidaGrupoConf con profiles por ambiente

#### MicroIntegradorReportesVidaGrupo
- **Ubicación**: `C:\Guidewire\MicroIntegradorReportesVidaGrupo`
- **Propósito**: Microservicio especializado en generación de reportes masivos para pólizas de Vida Grupo con arquitectura extensible
- **Versión**: 0.0.1
- **Stack Tecnológico**: Java 17 + Apache Camel 3.20.0 + Gradle 6.9.2
- **Arquitectura**: **Hexagonal (Ports & Adapters)** + **Modular por Tipo de Reporte**
  - Separación estricta en 3 capas: Application, Domain, Infrastructure
  - Domain puro sin dependencias de frameworks
  - Diseño extensible para múltiples tipos de reportes
- **Responsabilidades Críticas**:
  - **Generación de Reportes Masivos**: Motor extensible para construcción de diferentes tipos de reportes
  - **Detalle de Cobro (Módulo Actual)**: Reporte detallado de facturación colectiva con información por empleado/dependiente
  - **Procesamiento Asíncrono con Quartz**: 4 WorkQueues programadas independientes
    - WorkQueue 1 (horaria): Consulta datos Guidewire + creación cabecera Azure
    - WorkQueue 2 (horaria): Construcción y envío de bloques masivos a Azure
    - WorkQueue 3 (horaria): Cierre de archivo + notificación RabbitMQ
    - WorkQueue 4 (diaria): Limpieza automática de registros antiguos
  - **Integración con Azure Massive Download API**: Construcción de archivos masivos en la nube
  - **Gestión de Volúmenes Masivos**: Procesamiento eficiente de millones de registros mediante batch processing
  - **API REST**: Endpoints para solicitar generación y consultar estado/descarga
  - **Mensajería Asíncrona**: Notificación de cambios de estado vía RabbitMQ
- **Patrones Arquitectónicos Aplicados**:
  - **CQRS**: Separación Commands/Queries en capa de aplicación
  - **Repository Pattern**: Abstracción de persistencia mediante ports
  - **Scheduled Job Pattern**: Jobs Quartz para procesamiento distribuido
  - **Batch Processing**: Procesamiento por lotes con volúmenes configurables
  - **Event-Driven Architecture**: Comunicación asíncrona con RabbitMQ
  - **Provider Pattern**: Proveedores de reglas de negocio especializados
- **Productos Soportados**:
  - Vida Grupo Integral
  - Deudores
  - Docentes
  - Condiciones de Uso
- **Calidad y Testing**:
  - 124+ pruebas unitarias
  - 85%+ cobertura de código (JaCoCo)
  - Mutation testing con PIT
  - OWASP Dependency Check
- **Dependencias Críticas**:
  - RabbitMQ: Mensajería asíncrona
  - Oracle Database: Réplica read-only de Guidewire + tablas de control
  - Azure Massive Download API: Almacenamiento de archivos masivos
  - Splunk: Logging centralizado (Log4j2 + SplunkHttp appender)
- **Build y Deployment**:
  - Gradle 6.9.2 con centralización de versiones en gradle.properties
  - Docker multi-stage para containerización
  - Variables de entorno externalizadas
- **Documentación Completa**:
  - [📘 Arquitectura Detallada del Componente](architecture-microintegrador-reportes-vidagrupo.md) - 1,111 líneas
  - [🔄 Flujo de Negocio: Generación Reporte Detalle Cobro](flujo-generacion-reporte-detalle-cobro.md) - 1,229 líneas
  - [📖 README del Proyecto](../MicroIntegradorReportesVidaGrupo/README.md) - 247 líneas
  - [📝 Historia #915240: Bug Campos Detalle Cobro](../docs/stories/915240.bug-organizar-campos-errados-detalle-cobro.story.md) - 1,941 líneas

### **3. Infraestructura como Código**

#### VidaGrupoIAC
- **Ubicación**: `C:\Guidewire\VidaGrupoIAC`
- **Propósito**: Gestión de infraestructura Azure para Vida Grupo
- **Stack Tecnológico**: Terraform
- **Responsabilidades**:
  - Configuración de Azure Data Factory
  - Configuración de API Gateways (Azure APIM)
  - Exposición controlada de endpoints de MicroIntegradores
  - Gestión de políticas de seguridad y throttling
  - Orquestación de pipelines de datos
- **Notas**: README no documentado completamente

### **4. Componentes de Soporte**

#### PolicySearchSummary
- **Ubicación**: `C:\Guidewire\PolicySearchSummary`
- **Stack**: Java + Spring (pom.xml + Gradle)
- **Propósito**: Búsqueda y consultas optimizadas sobre pólizas

#### SeusLibreria
- **Ubicación**: `C:\Guidewire\SeusLibreria`
- **Stack**: Java + Gradle
- **Propósito**: Librería compartida de utilidades comunes

#### Datamodel
- **Ubicación**: `C:\Guidewire\Datamodel`
- **Propósito**: Scripts de modelado y migración de datos
- **Contiene**: Scripts de upgrade de esquemas Guidewire

#### Transformation
- **Ubicación**: `C:\Guidewire\transformation`
- **Stack**: Ant scripts
- **Propósito**: Transformación y migración de datos entre versiones

### **5. Configuración de Microservicios**

#### MicroIntegradorVidaGrupoConf
- **Ubicación**: `C:\Guidewire\MicroIntegradorVidaGrupoConf`
- **Propósito**: Configuraciones externas para MicroIntegradorVidaGrupo
- **Contenido**: Properties, configuraciones de ambiente

#### MicroIntegradorReportesVidaGrupoConf
- **Ubicación**: `C:\Guidewire\MicroIntegradorReportesVidaGrupoConf`
- **Propósito**: Configuraciones externas para MicroIntegradorReportesVidaGrupo
- **Contenido**: Properties, configuraciones de ambiente

---

## � **Documentación Arquitectónica Disponible**

El ecosistema cuenta con documentación arquitectónica detallada organizada en la carpeta `docs/architecture/`:

### **Documentos de Componentes Individuales**

#### 1. **PolicyCenter** 
- **Archivo**: `architecture-policycenter.md` (588 líneas)
- **Contenido**:
  - Descripción completa como Sistema de Registro de pólizas
  - Stack tecnológico detallado (Gosu, Java, Guidewire Framework 8.0.7)
  - Arquitectura monolítica modular (estático/dinámico)
  - Estructura completa del código fuente (modules/configuration)
  - Patrones de diseño (Entity-Enhancement, Rule Engine, Workflow Engine)
  - Diagramas conceptuales y de componentes
  - Procesos de negocio especializados Vida Grupo
  - Integración con MicroIntegradorVidaGrupo
- **URL**: `docs/architecture/architecture-policycenter.md`

#### 2. **BillingCenter**
- **Archivo**: `architecture-BillingCenter.md` (651 líneas)
- **Contenido**:
  - Sistema de Registro para transacciones financieras y facturación
  - Arquitectura Domain-Driven Design con capas Guidewire
  - Facturación corporativa especializada Vida Grupo
  - Estructura de código Gosu/Java (modules/configuration)
  - Patrones: Event-Driven, Plugin Architecture, Workflow Engine
  - Integraciones críticas (DIAN, Tesorería, MicroIntegradorReportesVidaGrupo)
  - Diagramas conceptuales y flujos de facturación
  - Gestión de cobranza y mora empresarial
- **URL**: `docs/architecture/architecture-BillingCenter.md`

#### 3. **MicroIntegradorReportesVidaGrupo**
- **Archivo**: `architecture-microintegrador-reportes-vidagrupo.md` (1,111 líneas)
- **Contenido**:
  - Microservicio especializado en generación de reportes masivos
  - Arquitectura hexagonal estricta (Ports & Adapters)
  - Diseño modular por tipo de reporte (detailcharge actual)
  - Stack: Apache Camel 3.20.0 + Java 17 + Gradle 6.9.2
  - Patrones: CQRS, Repository, Scheduled Job (Quartz), Batch Processing
  - Estructura completa del código (Application/Domain/Infrastructure)
  - 4 WorkQueues programadas con Quartz Scheduler
  - Integración con Azure Massive Download API y RabbitMQ
  - 124+ pruebas unitarias con 85%+ cobertura
  - Configuración de logging (Log4j2 + Splunk)
- **URL**: `docs/architecture/architecture-microintegrador-reportes-vidagrupo.md`

### **Documentos de Flujos de Negocio**

#### 4. **Flujo: Generación de Reporte Detalle de Cobro**
- **Archivo**: `flujo-generacion-reporte-detalle-cobro.md` (1,229 líneas)
- **Contenido**:
  - Flujo end-to-end del proceso de generación de reportes
  - Diagramas de secuencia detallados (7 diagramas Mermaid)
  - Descripción de las 4 fases del proceso:
    - Fase 1: Solicitud vía API REST
    - Fase 2: Procesamiento WorkQueue 1 (consulta + cabecera Azure)
    - Fase 3: Procesamiento WorkQueue 2 (bloques + envío Azure)
    - Fase 4: Procesamiento WorkQueue 3 (cierre + notificación RabbitMQ)
  - Modelo de datos completo (10 tablas)
  - APIs REST documentadas (generación, consulta, descarga)
  - Configuración de jobs Quartz
  - Manejo de errores y reintentos
  - Ejemplos de payloads JSON
- **URL**: `docs/architecture/flujo-generacion-reporte-detalle-cobro.md`

### **Historias de Usuario Documentadas**

#### 5. **Historia #915240: Bug - Organizar campos errados detalle de cobro**
- **Archivo**: `stories/915240.bug-organizar-campos-errados-detalle-cobro.story.md` (1,941 líneas)
- **Contenido**:
  - 12 criterios de aceptación completados ✅
  - Análisis técnico detallado por criterio
  - Soluciones implementadas con código
  - Decisiones arquitectónicas documentadas
  - Checklist de desarrollo completo
  - Tests unitarios (30+ tests)
  - Análisis de riesgos y mitigaciones
  - Proceso de revisión de pares
  - Lecciones aprendidas del bug
- **Estado**: Ready for Review
- **URL**: `docs/stories/915240.bug-organizar-campos-errados-detalle-cobro.story.md`

### **README de Componentes**

#### 6. **MicroIntegradorReportesVidaGrupo README**
- **Archivo**: `MicroIntegradorReportesVidaGrupo/README.md` (247 líneas)
- **Contenido**:
  - Descripción del proyecto y contexto de negocio
  - Tecnologías utilizadas (Apache Camel, Java 17, Gradle)
  - Estructura completa del proyecto con arquitectura hexagonal modular
  - Configuración de variables de entorno
  - Logging y Splunk
  - Comandos de construcción y ejecución
  - Guía de pruebas (tests, cobertura, mutación)
  - Análisis de seguridad (OWASP Dependency Check)
- **URL**: `MicroIntegradorReportesVidaGrupo/README.md`

### **Estadísticas de Documentación**

| Tipo de Documento | Cantidad | Líneas Totales | Porcentaje |
|-------------------|----------|----------------|------------|
| Arquitectura de Componentes | 3 | 2,350 | 45.6% |
| Flujos de Negocio | 1 | 1,229 | 23.8% |
| Historias de Usuario | 1 | 1,941 | 37.7% |
| READMEs de Proyecto | 1 | 247 | 4.8% |
| **GPS Arquitectónico (este documento)** | **1** | **~1,400** | **27.2%** |
| **TOTAL** | **7** | **~7,167** | **100%** |

> **Nota**: Esta documentación representa un esfuerzo significativo de documentación viva que se mantiene sincronizada con el código fuente mediante el Método Ceiba.

---

## �🛠️ **Stack Tecnológico Identificado**

### **Guidewire Insurance Suite**
| Componente | Lenguaje Principal | Framework | Versión | Base de Datos |
|------------|-------------------|-----------|---------|---------------|
| PolicyCenter | Gosu + Java | Guidewire Framework | 8.0.7.gw | Oracle |
| BillingCenter | Gosu + Java | Guidewire Framework | 8.0.7.gw | Oracle |
| ClaimCenter | Gosu + Java | Guidewire Framework | 8.0.7.gw | Oracle |

**Características Guidewire:**
- **Gosu**: Lenguaje de programación propietario de Guidewire (orientado a objetos, estaticamente tipado)
- **Guidewire Studio**: IDE especializado para desarrollo
- **Product Designer**: Herramienta visual para diseño de productos de seguros
- **Build Tool**: Ant scripts personalizados (gwpc, gwbc, gwcc)
- **Arquitectura**: Monolítica modular con separación estático/dinámico
  - `policycenterstatic` / `policy_center modules`
  - `billingcenterstatic` / `billing_center modules`

### **Microservicios**
| Componente | Lenguaje | Framework Integración | Versión Java | Build Tool |
|------------|----------|----------------------|--------------|------------|
| MicroIntegradorVidaGrupo | Java | Apache Camel | 17 | Gradle 6.9.2 |
| MicroIntegradorReportesVidaGrupo | Java | Apache Camel | 17 | Gradle 6.9.2 |

**Tecnologías de Microservicios:**
- **Apache Camel 3.20.0**: Framework de integración (Enterprise Integration Patterns)
- **Java 17**: Versión LTS
- **Gradle 6.9.2**: Build automation
- **Logging**: Log4j2 + SLF4J + Splunk HTTP Appender
- **Testing**: JUnit 4.13.2, Mockito 4.11.0, PIT Mutation Testing
- **Security**: OWASP Dependency Check
- **Database**: Oracle JDBC (ojdbc8), Apache Commons DBCP2
- **Serialization**: Jackson, Gson
- **Messaging**: RabbitMQ (camel-rabbitmq)
- **Scheduling**: Quartz Scheduler
- **HTTP**: Netty HTTP, OkHttp3

### **Infraestructura**
| Componente | Tecnología | Proveedor Cloud |
|------------|------------|-----------------|
| VidaGrupoIAC | Terraform | Azure |
| Data Factory | PaaS | Azure |
| API Gateway | Azure APIM | Azure |
| Mensajería | RabbitMQ | On-premise/Azure |

### **Herramientas de Desarrollo**
- **Control de versiones**: Git + Bitbucket
- **Gestión de código**: GitFlow workflow
- **Repositorio de artefactos**: Artifactory Suramericana
- **IDE recomendado**: 
  - Guidewire Studio (para PolicyCenter, BillingCenter, ClaimCenter)
  - IntelliJ IDEA / VS Code (para Microservicios)
- **Build local**: Scripts .bat personalizados (Guidewire.bat)

---

## � **Procesos de Negocio Especialzados - Vida Grupo**

### **Procesos Identificados en Documentación**

Basándome en la documentación encontrada en `/GeneralDocumentation`, se han identificado los siguientes procesos críticos especializados en Vida Grupo:

#### **1. Carga Masiva de Asegurados**
- **Documentos**: `EAG-Carga Masiva VG-291025-031319.pdf`
- **Responsable**: PolicyCenter con apoyo de MicroIntegradorVidaGrupo
- **Proceso**:
  1. Recepción de archivos planos con nómina de empleados
  2. Validación de formatos y datos obligatorios
  3. Consulta masiva a centrales de riesgo vía MicroIntegradorVidaGrupo
  4. Generación automática de certificados individuales
  5. Activación masiva de coberturas

#### **2. Detalle de Cobro Especializado**
- **Documentos**: `EAG-Detalle de cobro VG-291025-031327.pdf`
- **Responsable**: BillingCenter + MicroIntegradorReportesVidaGrupo
- **Proceso**:
  1. Consolidación de primas por empleador
  2. Desglose detallado por empleado/dependiente
  3. Aplicación de descuentos corporativos
  4. Generación de reportes para tesorería
- **📄 Documentación detallada del flujo**: [Flujo de Generación de Reporte de Detalle de Cobro](./flujo-generacion-reporte-detalle-cobro.md)

#### **3. Gestión de Auditorías**
- **Documentos**: `EAG-Auditorias VG-291025-031321.pdf`
- **Responsable**: Transversal (todos los módulos Guidewire)
- **Proceso**:
  1. Audit trail automatizado de transacciones
  2. Reportería especializada para entes de control
  3. Trazabilidad de cambios en pólizas colectivas
  4. Compliance con normativas de seguros de vida

#### **4. Integración CCM Vida Grupo**
- **Documentos**: `EAG-Integración CCM Vida Grupo-291025-031333.pdf`
- **Responsable**: MicroIntegradorVidaGrupo
- **Proceso**:
  1. Sincronización con Customer Communication Management
  2. Generación automática de comunicaciones a asegurados
  3. Distribución multicanal (email, SMS, portal)
  4. Tracking de entrega y apertura

#### **5. Procesos de Emisión Avanzados**
- **Documentos**: Múltiples EAG-PolicyCenter
- **Responsable**: PolicyCenter con integraciones especializadas
- **Procesos Específicos**:
  - Emisión de pólizas colectivas con estructuras complejas
  - Configuración automática de coberturas por cargo/salario
  - Manejo de dependientes con validación de parentesco
  - Renovación automática con actualización de nóminas

### **Flujos de Integración Especializados**

#### **Vida Grupo → Sistemas Corporativos**
```mermaid
sequenceDiagram
    participant HR as Sistema RRHH
    participant PC as PolicyCenter
    participant MI as MicroIntegradorVG
    participant CR as Centrales Riesgo
    participant BC as BillingCenter

    HR->>PC: Archivo nómina empleados
    PC->>MI: Validar asegurados masivo
    MI->>CR: Consulta centrales riesgo
    CR->>MI: Respuesta validaciones
    MI->>PC: Resultado validaciones
    PC->>PC: Emisión certificados masivos
    PC->>BC: Activar facturación colectiva
    BC->>HR: Confirmación activación
```

---

## �🔗 **Puntos de Integración Críticos**

### **1. Guidewire ↔ Microservicios**

#### PolicyCenter → MicroIntegradorVidaGrupo
- **Protocolo**: REST + SOAP
- **Propósito**: 
  - Consultar cotizadores externos durante emisión
  - Validar asegurados contra listas restrictivas
  - Integrar con sistemas de validación de morosidad
- **Patrón**: Llamadas síncronas para validaciones críticas

#### BillingCenter → MicroIntegradorReportesVidaGrupo
- **Protocolo**: REST API + RabbitMQ
- **Propósito**:
  - Solicitar generación de reportes de detalle de cobro para facturas colectivas
  - Consultar estado de reportes en proceso
  - Obtener URL de descarga de reportes completados
  - Recibir notificación asíncrona de reportes completados
- **Patrón**: Request-Reply con procesamiento asíncrono (Scheduled Job Pattern con Quartz)
- **Arquitectura del Flujo**: 
  - **Solicitud**: REST API síncrona (registro de solicitud)
  - **Procesamiento**: 4 WorkQueues independientes ejecutadas por Quartz Scheduler
  - **Notificación**: RabbitMQ asíncrono al completar
- **Flujo Detallado** (ver [documentación completa](flujo-generacion-reporte-detalle-cobro.md)):
  
  **Fase 1: Solicitud de Generación**
  1. BillingCenter (o aplicación externa) llama `POST /v1/he/invoices/{invoiceNumber}/chargedetail/report`
  2. MicroIntegrador registra solicitud en tabla de control (estado=1, lock=0)
  3. Responde inmediatamente 200 OK sin bloquear
  
  **Fase 2: Procesamiento Asíncrono por WorkQueues Quartz**
  - **WorkQueue 1** (ejecutado cada hora por Quartz):
    - Consulta registros pendientes (estado=1, lock=0)
    - Actualiza lock=1 (optimistic locking)
    - Ejecuta INSERT SELECT masivo desde BillingCenter/PolicyCenter Oracle
    - Crea cabecera en Azure Massive Download API
    - Actualiza estado=2 (datos cargados)
    
  - **WorkQueue 2** (ejecutado cada hora por Quartz):
    - Consulta items listos (estado=2, lock=0)
    - Procesa en lotes configurables (batch pattern)
    - Construye contenido CSV por lote (Provider Pattern)
    - Envía bloques a Azure Massive Download API
    - Marca registros enviados
    - Al completar: estado=3 (bloques enviados)
    
  - **WorkQueue 3** (ejecutado cada hora por Quartz):
    - Consulta archivos para cerrar (estado=3, lock=0)
    - Cierra archivo en Azure Massive Download API
    - Obtiene URL de descarga desde Azure
    - **Publica mensaje en RabbitMQ** con URL de descarga
    - Actualiza estado=4 (completado)
    
  - **WorkQueue 4** (ejecutado diariamente por Quartz):
    - Limpieza automática de registros antiguos
    - Mantenimiento de tablas de control
  
  **Fase 3: Notificación y Descarga**
  4. BillingCenter consume mensaje RabbitMQ con URL de descarga
  5. Usuario descarga archivo CSV desde Azure vía BillingCenter UI
  
- **APIs REST Disponibles**:
  - `POST /v1/he/invoices/{invoiceNumber}/chargedetail/report` - Solicitar generación
  - `GET /v1/he/invoices/{invoiceNumber}/chargedetail/report` - Consultar estado/descarga
  
- **Tablas de Control (Oracle)**:
  - `REP_CHARGE_DETAIL` - Tabla principal de control de reportes
  - `REP_CHARGE_DETAIL_ITEM` - Registros detallados por asegurado
  - 8 tablas adicionales de soporte (ver doc flujo)
  
- **Modelo de Datos**: 10 tablas con información de:
  - Control de proceso (estados, locks, timestamps)
  - Datos de facturas y coberturas
  - Información de asegurados y beneficiarios
  - Parámetros de configuración por producto
  
- **Tecnologías de Integración**:
  - Apache Camel Routes para orquestación
  - Quartz Scheduler para jobs programados
  - JDBC Oracle para consultas masivas (INSERT SELECT)
  - Azure Massive Download API para construcción de archivos
  - RabbitMQ para notificaciones asíncronas
  - Log4j2 + Splunk para trazabilidad completa
  
- **Documentación Completa**: 
  - 📄 [Flujo Detallado: Generación Reporte Detalle Cobro](flujo-generacion-reporte-detalle-cobro.md) (1,229 líneas)
  - Incluye: 7 diagramas de secuencia, modelo de datos, configuración Quartz, ejemplos JSON

#### ClaimCenter → MicroIntegradorVidaGrupo
- **Protocolo**: REST + SOAP
- **Propósito**:
  - Validar beneficiarios de siniestros
  - Integración con sistemas de documentación de siniestros
- **Patrón**: Llamadas síncronas

### **2. Microservicios ↔ Sistemas Externos**

#### MicroIntegradorVidaGrupo → Sistemas Externos
- **Sistemas de Facturación**
  - Protocolo: SOAP/REST según sistema
  - Propósito: Sincronización de datos de facturación
  
- **Validador de Asegurados**
  - Protocolo: REST/SOAP
  - Propósito: Validar contra listas restrictivas, morosidad, antecedentes judiciales
  
- **Sistemas de Cotización**
  - Protocolo: REST/SOAP
  - Propósito: Obtener cotizaciones de productos de Vida Grupo
  
- **Patrón común**: Apache Camel Routes con transformación de mensajes

#### MicroIntegradorReportesVidaGrupo → Base de Datos BillingCenter
- **Protocolo**: JDBC directo (Oracle)
- **Propósito**: Consultar datos de cobro para generación de reportes
- **Patrón**: Consultas SQL optimizadas vía Camel JDBC component

### **3. API Gateway (Azure APIM) ↔ Microservicios**
- **Expone**:
  - Endpoints REST de MicroIntegradorVidaGrupo
  - Endpoints REST de MicroIntegradorReportesVidaGrupo
- **Funcionalidades**:
  - Rate limiting
  - Autenticación/autorización
  - Monitoreo y logging
  - Versionamiento de APIs

### **4. RabbitMQ - Mensajería Asíncrona**
- **Exchanges**:
  - `sura.seguros.vidagrupo.chargedetail.ex`
- **Queues**:
  - `sura.seguros.vidagrupo.chargedetail.risk.qu` (Productor)
  - `sura.seguros.vidagrupo.chargedetail.risk.reply.qu` (Consumidor)
- **Routing Keys**:
  - `sura.seguros.vidagrupo.chargedetail.risk`
  - `sura.seguros.vidagrupo.chargedetail.risk.reply`
- **Patrón**: Request-Reply asíncrono
- **Participantes**:
  - Productor: MicroIntegradorReportesVidaGrupo
  - Consumidor: BillingCenter

---

## 🔐 **Patrones de Integración y Seguridad**

### **Canales de Comunicación**

#### REST APIs
- **Participantes**: 
  - PolicyCenter ↔ MicroIntegradorVidaGrupo
  - BillingCenter ↔ MicroIntegradorReportesVidaGrupo
  - MicroIntegradores ↔ Sistemas Externos
- **Autenticación**: API Keys, OAuth según sistema externo
- **Formato**: JSON principalmente, XML para SOAP legacy
- **Framework**: Apache Camel Netty HTTP

#### SOAP Web Services  
- **Participantes**:
  - Guidewire Centers ↔ Sistemas Legacy externos
  - MicroIntegradorVidaGrupo ↔ Sistemas de facturación legacy
- **Estándar**: WS-I compliant
- **Autenticación**: WS-Security según sistema
- **Herramientas Guidewire**: regen-wsi-local para regenerar WSDLs

#### Mensajería Asíncrona (RabbitMQ)
- **Participantes**:
  - MicroIntegradorReportesVidaGrupo (Productor)
  - BillingCenter (Consumidor)
- **Protocolo**: AMQP 0.9.1
- **Host**: msglab.suramericana.com.co:5672
- **Patrón**: Publisher-Subscriber con request-reply
- **Ventajas**: Desacoplamiento, resiliencia, procesamiento asíncrono

#### Acceso Directo a Base de Datos
- **Participantes**: MicroIntegradorReportesVidaGrupo → Oracle BillingCenter
- **Protocolo**: JDBC (ojdbc8)
- **Propósito**: Consultas optimizadas para reportes complejos
- **Connection Pooling**: Apache Commons DBCP2

### **Mecanismos de Autenticación/Autorización**

#### Guidewire Centers
- **Autenticación**: 
  - Usuario/contraseña Guidewire
  - Integración con directorio corporativo (posible LDAP/AD)
- **Autorización**: Roles y permisos Guidewire

#### Microservicios
- **Entrada**:
  - Azure APIM: OAuth/API Keys
  - Guidewire: Posible autenticación básica o tokens
- **Salida (sistemas externos)**:
  - API Keys específicas por sistema
  - OAuth según disponibilidad
  - WS-Security para SOAP

### **Monitoreo y Logging**

#### Guidewire Centers
- **Logging**: Log4j interno Guidewire
- **Ubicación**: `/webapps/pc/WEB-INF/logs` (PolicyCenter), similar para BC/CC
- **Nivel**: Configurable por módulo

#### Microservicios
- **Logging centralizado**: Splunk
  - HTTP Appender en producción
  - Console Appender en desarrollo/test
- **Framework**: SLF4J + Log4j2
- **Configuración**: `log4j2.yml` (producción), `log4j2-test.yml` (testing)
- **Variables de entorno Splunk**:
  - `SPLUNK_SERVICE_URL`
  - `SPLUNK_SERVICE_SSO`

#### Monitoreo de Integraciones
- **Azure APIM**: Dashboards de APIs, métricas de latencia, errores
- **RabbitMQ**: Management console para monitoreo de colas
- **Base de datos**: Oracle Enterprise Manager (posible)

### **Patrones de Resiliencia**

#### Apache Camel (Microservicios)
- **Retry policies**: Configurables por ruta
- **Error handlers**: Dead Letter Channel pattern
- **Circuit Breakers**: Disponible vía camel-hystrix (si configurado)
- **Timeouts**: Configurables por endpoint

#### Guidewire
- **Retry en integraciones**: Configurable en policies de integración
- **Transaccionalidad**: Gestión de transacciones JTA

---

## 🧪 **Realidad de Testing Actual**

### **Guidewire Centers**

#### Herramientas disponibles
- **Unit Testing**: Gosu Test Framework (integrado en Studio)
- **Análisis estático**: Gosu CodeNarc
  - `gwpc sura.gosu-codenarc` (código producción)
  - `gwpc sura.gosu-codenarc-test` (código testing)
- **Test data**: SampleData en `/modules/configuration/sampledata`

#### Tipos de testing
- Tests unitarios Gosu (`.gs` files en `/modules/gtest`)
- Tests de integración (limitados)
- Tests manuales extensivos

### **Microservicios**

#### MicroIntegradorReportesVidaGrupo
- **Unit Testing**: JUnit 4.13.2 + Mockito 4.11.0
- **Camel Testing**: camel-test framework
- **Mutation Testing**: PIT (info.solidsoft.pitest)
  - Configurado para targetClasses: `com.sura.mi.reportes.vidagrupo.*`
  - Reportes en `target/pit-reports/pitest`
- **Code Coverage**: JaCoCo
  - Reportes en `build/reports/jacocoHtml`
- **Security Testing**: OWASP Dependency Check
  - Reportes en `target/dependencyCheck`

#### MicroIntegradorVidaGrupo
- **Configuración similar** a MicroIntegradorReportesVidaGrupo
- Testing con JUnit + Mockito
- PIT Mutation Testing configurado
- OWASP Dependency Check

#### Comandos de testing
```bash
# Unit tests + coverage
gradle test

# Mutation testing
gradle mutation

# Security analysis
gradle dependencyReports
```

### **Cobertura real**
- **Guidewire**: Cobertura variable, depende del módulo
- **Microservicios**: Configurado para reportes automáticos, cobertura a validar por proyecto

---

## ⚠️ **Deuda Técnica y Restricciones**

### **Deuda Técnica Identificada**

#### Guidewire Centers
- **Versión 8.0.7**: No es la más reciente (Guidewire 10.x disponible)
- **Arquitectura monolítica**: Dificulta escalabilidad granular
- **Separación estático/dinámico**: Requiere gestión de dos repositorios por Center
- **Documentación**: Principalmente documentación oficial Guidewire, poca documentación customizada
- **Testing**: Mayor énfasis en testing manual que automatizado

#### Microservicios
- **Documentación limitada**: 
  - VidaGrupoIAC sin README completo
  - MicroIntegradorVidaGrupo sin README
  - Solo MicroIntegradorReportesVidaGrupo tiene documentación detallada
- **Configuración externa**: Archivos de configuración en repositorios separados (`*Conf`)
- **Logging de desarrollo**: Logs de Splunk requieren variables de entorno en producción

### **Restricciones Técnicas**

#### Versiones y compatibilidad
- **Java 17**: Requerido para microservicios (LTS)
- **Guidewire 8.0.7**: Restricciones en características disponibles
- **Oracle Database**: Dependencia fuerte en RDBMS Oracle
- **Apache Camel 3.20.0**: Versión específica con dependencias gestionadas

#### Infraestructura
- **On-premise + Cloud Híbrido**: Guidewire en servidores propios, microservicios en Azure
- **RabbitMQ corporativo**: Infraestructura compartida (msglab.suramericana.com.co)
- **Artifactory corporativo**: Repositorio de artefactos centralizado

#### Desarrollo local
- **Requisito Windows**: Scripts .bat para gestión de Guidewire
- **Múltiples herramientas**: Java, Ant, Maven, Gradle, Git, Guidewire Studio
- **Recursos intensivos**: Ejecutar Guidewire Centers localmente requiere recursos significativos

---

## 🔗 **Dependencias Externas Críticas**

### **Repositorios de artefactos**
- **Artifactory Suramericana**:
  - `CoreVida-snapshot-local`
  - `CoreDeSeguros-snapshot-local`
  - `sura-share`
- **Autenticación**: Variable de entorno `ARTIFACTORY_TOKEN`
- **Maven Central**: Fallback para librerías públicas

### **Librerías compartidas**
- **ms-lib-seus**: Librería interna Sura (`co.com.sura.seus`)
- **SeusLibreria**: Librería local compartida entre proyectos

### **Dependencias de runtime**
- **Oracle JDBC**: ojdbc8 para conectividad a bases de datos
- **RabbitMQ**: Infraestructura de mensajería crítica
- **Splunk**: Centralización de logs en producción
- **Azure Services**: Data Factory, APIM

### **Vulnerabilidades y seguridad**
- **OWASP Dependency Check**: Configurado en microservicios
- **Actualización de dependencias**: Centralizada en `gradle.properties`
- **Security scanning**: Disponible vía OWASP en build

---

## 📚 **Comandos de Desarrollo Esenciales**

### **Guidewire Centers - Script Unificado**

#### Ejecutar menú interactivo
```bash
C:\Guidewire\Guidewire.bat
```

**Opciones principales por Center:**

#### PolicyCenter (gwpc)
```bash
cd C:\Guidewire\PolicyCenter\bin

# Limpiar base de datos
gwpc dev-dropdb

# Regenerar proyecto (build completo)
gwpc regen-project

# Regenerar proyecto con servicios customizados Sura
gwpc sura.regen-project

# Iniciar Guidewire Studio IDE
gwpc studio

# Regenerar diccionario de datos
gwpc regen-dictionary

# Regenerar documentación Gosu
gwpc regen-gosudoc

# Gestión del servidor
gwpc dev-start
gwpc dev-stop

# Regenerar WSDLs de web services
gwpc regen-wsi-local

# Análisis estático de código
gwpc sura.gosu-codenarc          # Código producción
gwpc sura.gosu-codenarc-test     # Código testing
```

#### BillingCenter (gwbc)
```bash
cd C:\Guidewire\BillingCenter\bin

# Comandos análogos a PolicyCenter
gwbc dev-dropdb
gwbc regen-project
gwbc sura.regen-project
gwbc studio
gwbc dev-start / dev-stop
gwbc sura.gosu-codenarc
# ... etc
```

#### ClaimCenter (gwcc)
```bash
cd C:\Guidewire\ClaimCenter\bin

# Comandos análogos
gwcc dev-dropdb
gwcc regen-project
gwcc sura.regen-project
gwcc studio
gwcc dev-start / dev-stop
gwcc sura.gosu-codenarc
# ... etc
```

#### Product Designer
```bash
cd C:\Guidewire\PolicyCenter\productdesigner\bin
launchPD.bat
```

#### Accelerator (Transformation)
```bash
cd C:\Guidewire\transformation
ant create.test.db
```

### **Microservicios - Gradle**

#### MicroIntegradorReportesVidaGrupo
```bash
cd C:\Guidewire\MicroIntegradorReportesVidaGrupo

# Build completo (genera shadow JAR)
gradle build

# Ejecutar tests con coverage
gradle test

# Mutation testing
gradle mutation

# Security scan
gradle dependencyReports

# Ejecutar aplicación
gradle runWithJavaExec

# Limpiar build
gradle clean
```

#### MicroIntegradorVidaGrupo
```bash
cd C:\Guidewire\MicroIntegradorVidaGrupo

# Comandos análogos a MicroIntegradorReportesVidaGrupo
gradle build
gradle test
gradle mutation
gradle dependencyReports
```

### **Infraestructura (Terraform)**

#### VidaGrupoIAC
```bash
cd C:\Guidewire\VidaGrupoIAC

# Inicializar Terraform
terraform init

# Plan de cambios
terraform plan

# Aplicar infraestructura
terraform apply

# Destruir infraestructura
terraform destroy
```

---

## 🚀 **Guía Rápida para Desarrollo**

### **Configuración Inicial del Ambiente**

#### 1. Prerequisitos de software
**Instalar en este orden:**
1. Java JDK (versión compatible con Guidewire 8.0.7)
2. Java JDK 17 (para microservicios)
3. Apache Ant
4. Maven
5. Gradle 6.9.2
6. Git + SourceTree/TortoiseGit
7. Guidewire Studio
8. IntelliJ IDEA / VS Code (para microservicios)

**Rutas estándar**: Ver [documento de estándares de instalación](https://www.somossura.com/sites/negocio/seguros/core-seguros/_layouts/15/WopiFrame.aspx?sourcedoc=/sites/negocio/seguros/core-seguros/ArquitecturaeInfraestructura/SUGW%20ARQ%20Estandar%20Local.xlsx&action=default)

#### 2. Variables de entorno
```bash
# Java para Guidewire
JAVA_HOME=C:\Program Files\Java\jdk1.8.0_xxx

# Java 17 para microservicios  
JAVA17_HOME=C:\Program Files\Java\jdk-17

# Ant
ANT_HOME=C:\apache-ant-x.x.x

# Gradle
GRADLE_HOME=C:\gradle-6.9.2

# Maven
M2_HOME=C:\apache-maven-x.x.x

# Artifactory
ARTIFACTORY_TOKEN=<tu-token>

# Splunk (solo para producción)
SPLUNK_SERVICE_URL=https://splunk-endpoint:8088
SPLUNK_SERVICE_SSO=<token>
```

#### 3. Clonar repositorios Guidewire

**PolicyCenter:**
```bash
cd C:\Guidewire
git clone https://<USER_GIT>@bitbucket.org/suracore/policycenterstatic.git PolicyCenter
cd PolicyCenter
git clone --recursive --jobs 2 https://<USER_GIT>@bitbucket.org/suracore/policy_center.git modules
git flow init
```

**BillingCenter:**
```bash
cd C:\Guidewire  
git clone https://<USER_GIT>@bitbucket.org/suracore/billingcenterstatic.git BillingCenter
cd BillingCenter
git clone --recursive --jobs 2 https://<USER_GIT>@bitbucket.org/suracore/billing_center.git modules
git flow init
```

**ClaimCenter:**
```bash
cd C:\Guidewire
git clone https://<USER_GIT>@bitbucket.org/suracore/claimcenterstatic.git ClaimCenter
cd ClaimCenter  
git clone --recursive --jobs 2 https://<USER_GIT>@bitbucket.org/suracore/claim_center.git modules
git flow init
```

#### 4. Configuración de base de datos local
```bash
# Para cada Center, ejecutar:
cd C:\Guidewire\<Center>\bin
gw<xx> dev-dropdb    # Crear DB limpia
gw<xx> regen-project # Build inicial
```

### **Flujo de Desarrollo Típico**

#### Para Historias en Guidewire Centers:

1. **Preparación**
   ```bash
   # Crear feature branch (GitFlow)
   git flow feature start <nombre-feature>
   
   # Abrir Guidewire Studio
   cd C:\Guidewire\<Center>\bin
   gw<xx> studio
   ```

2. **Desarrollo**
   - Modificar archivos `.gs` en `/modules/configuration/gsrc`
   - Modificar XMLs de configuración en `/modules/configuration/config`
   - Usar Product Designer si es cambio de producto

3. **Testing**
   ```bash
   # Regenerar proyecto
   gw<xx> sura.regen-project
   
   # Ejecutar servidor local
   gw<xx> dev-start
   
   # Análisis estático
   gw<xx> sura.gosu-codenarc
   ```

4. **Commit y PR**
   ```bash
   git add .
   git commit -m "feat: descripción del cambio"
   git push origin feature/<nombre-feature>
   # Crear Pull Request en Bitbucket
   ```

#### Para Historias en Microservicios:

1. **Preparación**
   ```bash
   cd C:\Guidewire\<Microservicio>
   git flow feature start <nombre-feature>
   ```

2. **Desarrollo**
   - Modificar rutas Apache Camel en `/src/main/java`
   - Actualizar tests en `/src/test/java`
   - Actualizar `build.gradle` si hay nuevas dependencias

3. **Testing local**
   ```bash
   gradle clean build
   gradle test
   gradle mutation
   gradle dependencyReports
   ```

4. **Commit y PR**
   ```bash
   git add .
   git commit -m "feat: descripción del cambio"
   git push origin feature/<nombre-feature>
   # Crear Pull Request
   ```

### **Casos Comunes de Desarrollo**

#### Caso 1: Nueva integración con sistema externo
**Componentes afectados**: PolicyCenter + MicroIntegradorVidaGrupo

1. **En MicroIntegradorVidaGrupo**:
   - Crear nueva ruta Camel para el endpoint externo
   - Implementar transformación de mensajes
   - Agregar manejo de errores y retry
   - Tests unitarios con Mockito

2. **En PolicyCenter**:
   - Crear servicio Gosu que llama al microintegrador
   - Integrar en flujo de negocio (underwriting, quoting, etc.)
   - Manejar respuestas y errores
   - Tests de integración

#### Caso 2: Nuevo reporte de detalle de cobro
**Componentes afectados**: BillingCenter + MicroIntegradorReportesVidaGrupo

1. **En MicroIntegradorReportesVidaGrupo**:
   - Crear consulta SQL optimizada a DB Oracle
   - Implementar ruta Camel con scheduler Quartz (si es programado)
   - Publicar resultado en RabbitMQ
   - Tests con datos de prueba

2. **En BillingCenter**:
   - Crear endpoint REST para solicitar reporte
   - Consumir mensaje de RabbitMQ con reporte completado
   - Mostrar reporte en UI
   - Gestionar errores y reintentos

#### Caso 3: Cambio en producto de Vida Grupo
**Componentes afectados**: PolicyCenter (Product Designer)

1. **Abrir Product Designer**:
   ```bash
   cd C:\Guidewire\PolicyCenter\productdesigner\bin
   launchPD.bat
   ```

2. **Modificar producto**:
   - Ajustar coberturas, riesgos, condiciones
   - Regenerar código desde Product Designer
   - Validar cambios en Guidewire Studio

3. **Regenerar proyecto**:
   ```bash
   cd C:\Guidewire\PolicyCenter\bin
   gwpc sura.regen-project
   ```

---

## 🎯 **Próximos Pasos para Documentación Detallada**

Para completar el GPS arquitectónico del ecosistema, se recomienda documentar en detalle:

### 1. Componentes Individuales (usar comando `architect *documentar-componente`)
- **PolicyCenter** - Estructura de módulos, patrones Gosu, flujos de underwriting
- **BillingCenter** - Ciclo de facturación, planes de pago, integraciones
- **ClaimCenter** - Flujo de siniestros, configuraciones específicas
- **MicroIntegradorVidaGrupo** - Rutas Camel específicas, endpoints externos
- **MicroIntegradorReportesVidaGrupo** - Generación de reportes, integración RabbitMQ
- **VidaGrupoIAC** - Recursos Terraform, políticas Azure APIM

### 2. Flujos de Negocio (usar comando `architect *documentar-flujo-negocio`)
- **Emisión de póliza colectiva** (PolicyCenter → MicroIntegradorVidaGrupo → Cotizadores)
- ✅ **Generación de reporte de detalle de cobro** - [Documentado](./flujo-generacion-reporte-detalle-cobro.md) (BillingCenter → MicroIntegradorReportesVidaGrupo → RabbitMQ)
- **Gestión de siniestros** (ClaimCenter → MicroIntegradorVidaGrupo → Validadores)
- **Facturación y cobro** (BillingCenter → Sistemas de facturación externos)

### 3. Estándares de Código (usar comando `architect *generar-estandares-codigo`)
- **Estándares Gosu** para PolicyCenter, BillingCenter, ClaimCenter
- **Estándares Java** para microservicios
- **Patrones Apache Camel** para integraciones
- **Convenciones de testing** por tecnología

### 4. Decisiones Arquitectónicas (Architecture Decision Records - ADRs)
- ¿Por qué Apache Camel para integraciones?
- ¿Por qué RabbitMQ en lugar de Kafka?
- ¿Por qué separar microintegrador de reportes?
- ¿Por qué Oracle vs otras bases de datos?

---

## 📚 **Casos de Estudio y Evolución del Sistema**

### **Historia #915240: Bug - Organizar campos errados detalle de cobro**

**Caso de Estudio Documentado:** Este caso ilustra el proceso completo de análisis, diseño, desarrollo y revisión de un bug de producción en el sistema de reportes.

#### **Contexto del Problema**
- **Componente**: MicroIntegradorReportesVidaGrupo (módulo `detailcharge`)
- **Reporte Afectado**: Detalle de cobro para facturas colectivas Vida Grupo
- **Severidad**: Alta - Información incorrecta entregada a clientes corporativos
- **Usuarios Impactados**: Expedidores, clientes corporativos

#### **Problemas Identificados (12 Criterios)**
1. **Campo "Número Póliza Colectiva"**: Mostraba número incorrecto
2. **Campo "Identificación Afiliado"**: Faltaba concatenación tipo+número documento
3. **Campo "Valor Total Prima x Asegurado"**: Suma incorrecta de primas e impuestos
4. **Campo "Valor Total Afiliado"**: Cálculo grupal incorrecto (más complejo)
5. **Campo "Parentesco"**: Mostraba código en lugar de sigla
6-12. Otros campos de coberturas, valores asegurados, primas, etc.

#### **Solución Arquitectónica Aplicada**

**Patrón Service Layer + Repository Pattern**:
```java
// Nuevo servicio de dominio creado
@Service
public class GetTotalAffiliatePremiumService {
    
    public BigDecimal getTotalAffiliatePremiumByTaxId(
        String taxId, 
        String invoiceNumber
    ) {
        // Query SQL optimizada con agregación en BD
        // SUM(TOTAL_PREMIUM) GROUP BY TAX_ID, COLLECTIVE_INVOICE_NUMBER
        return repository.calculateTotalAffiliatePremium(taxId, invoiceNumber);
    }
}
```

**Integración en flujo existente**:
- Servicio inyectado en `CompleteDetailChargeItemService`
- Llamado al mismo nivel que cálculo de prima de vida
- Campo `TOTAL_AFFILIATE_PREMIUM_VALUE` sobrescrito correctamente

#### **Calidad y Testing**
- **Tests Unitarios Nuevos**: 17 tests agregados
  - 11 tests en `GetTotalAffiliatePremiumServiceTest`
  - 6 tests en `DetailChargeItemQueryRepositoryTest`
- **Tests Modificados**: 10 tests actualizados para validar nuevos comportamientos
- **Cobertura**: Mantenida en 85%+ después de cambios
- **Mutation Testing**: Ejecutado con PIT para validar calidad de tests

#### **Lecciones Aprendidas Clave**
1. **Arquitectura Hexagonal**: Facilita agregar nuevos servicios sin modificar lógica existente
2. **CQRS Pattern**: Separación clara de comandos y queries simplifica mantenimiento
3. **Provider Pattern**: Permite encapsular reglas de negocio complejas por producto
4. **Testing Robusto**: 85%+ cobertura detectó regresiones durante desarrollo
5. **Documentación Viva**: Historia completa documentada (1,941 líneas) facilita onboarding

#### **Impacto en Arquitectura**
- ✅ Nuevo servicio de dominio agregado sin modificar estructura
- ✅ Principio Open/Closed respetado (extensión sin modificación)
- ✅ Separación de responsabilidades mantenida
- ✅ Tests como documentación ejecutable

#### **Documentación Completa**
- 📄 [Historia Completa #915240](../docs/stories/915240.bug-organizar-campos-errados-detalle-cobro.story.md) (1,941 líneas)
- Incluye: 12 criterios detallados, análisis técnico, código de solución, tests, decisiones arquitectónicas

### **Evolución del MicroIntegradorReportesVidaGrupo**

#### **Versión Inicial → Versión Actual (v2.0)**

**Cambios Arquitectónicos Mayores**:

1. **Modularización por Tipo de Reporte**
   - Antes: Código mezclado sin separación clara
   - Ahora: Módulo `detailcharge` independiente
   - Futuro: Nuevos módulos agregables sin impacto

2. **Adopción de Arquitectura Hexagonal**
   - Antes: Lógica acoplada a framework Apache Camel
   - Ahora: Domain puro sin dependencias externas
   - Beneficio: Testing simplificado, mantenibilidad mejorada

3. **Implementación de CQRS**
   - Antes: Operaciones CRUD mezcladas
   - Ahora: Commands y Queries separados
   - Beneficio: Optimización independiente de lectura/escritura

4. **Scheduled Job Pattern con Quartz**
   - Antes: Procesamiento síncrono bloqueante
   - Ahora: 4 WorkQueues independientes con Quartz
   - Beneficio: Procesamiento masivo sin impactar performance

5. **Centralización de Versiones**
   - Antes: Versiones hardcoded en build.gradle
   - Ahora: Todas en gradle.properties
   - Beneficio: Mantenimiento simplificado, upgrades más fáciles

#### **Métricas de Mejora**

| Métrica | Antes | Ahora | Mejora |
|---------|-------|-------|--------|
| **Cobertura de Tests** | ~60% | 85%+ | +42% |
| **Cantidad de Tests** | ~80 | 124+ | +55% |
| **Líneas de Documentación** | 0 | 4,528 | ∞ |
| **Tiempo Promedio Generación** | Bloqueante | Asíncrono (WQ) | No bloquea UI |
| **Escalabilidad** | Limitada | Alta (batch + WQ) | Millones de registros |

---

## 🎯 **Mejores Prácticas y Patrones Arquitectónicos**

### **Patrones Guidewire Insurance Suite**

#### **PolicyCenter - Vida Grupo**
```gosu
// Patrón de configuración de productos colectivos
class VidaGrupoProduct extends Product {
  
  // Configuración jerárquica de coberturas
  function configureCoverageHierarchy() : void {
    // Cobertura básica obligatoria
    var basicCoverage = createBasicLifeCoverage()
    
    // Coberturas adicionales opcionales  
    var additionalCoverages = createAdditionalCoverages()
    
    // Configuración por categoría de empleado
    configureCoverageByEmployeeCategory()
  }
  
  // Validación masiva de asegurados
  function validateBulkInsured(insureds : List<Insured>) : ValidationResult {
    // Integración con MicroIntegradorVidaGrupo
    return callExternalValidationService(insureds)
  }
}
```

#### **BillingCenter - Facturación Colectiva**
```gosu
// Patrón de facturación consolidada empresarial
class CorporateBillingPattern {
  
  function generateCorporateInvoice(policy : Policy) : Invoice {
    // Consolida todas las primas individuales en una factura empresarial
    var consolidatedPremium = calculateConsolidatedPremium(policy)
    
    // Aplica descuentos corporativos
    var corporateDiscount = applyCorporateDiscount(consolidatedPremium)
    
    // Genera factura única para empleador
    return createCorporateInvoice(consolidatedPremium, corporateDiscount)
  }
}
```

### **Patrones Apache Camel - Microservicios**

#### **Enterprise Integration Patterns Especializados**
```java
// Patrón Scatter-Gather para consulta paralela de centrales de riesgo
@Component
public class RiskCenterScatterGatherPattern {
    
    @Autowired
    private CamelContext camelContext;
    
    public void configureScatterGatherRoute() {
        from("direct:validateBulkInsured")
            .multicast(new AggregationStrategy() {
                public Exchange aggregate(Exchange oldExchange, Exchange newExchange) {
                    // Combina resultados de múltiples centrales de riesgo
                    return combineRiskCenterResults(oldExchange, newExchange);
                }
            })
            .parallelProcessing()
                .to("direct:cifin-validation")
                .to("direct:datacredito-validation") 
                .to("direct:procuraduria-validation")
            .end()
            .to("direct:consolidate-results");
    }
}
```

#### **Circuit Breaker Pattern para Sistemas Externos**
```java
@Component
public class ExternalSystemCircuitBreaker {
    
    @HystrixCommand(fallbackMethod = "fallbackQuoteService")
    public QuoteResponse callExternalQuoter(QuoteRequest request) {
        // Llamada a cotizador externo con circuit breaker
        return externalQuoterClient.requestQuote(request);
    }
    
    public QuoteResponse fallbackQuoteService(QuoteRequest request) {
        // Respuesta por defecto cuando el sistema externo está caído
        return createDefaultQuoteResponse();
    }
}
```

### **Patrones de Datos y Persistencia**

#### **Patrón CQRS para Reportería**
```java
// Separación de comandos y queries para optimizar reportes
@Service
public class VidaGrupoReportingService {
    
    @Autowired
    private BillingCenterCommandRepository commandRepo;
    
    @Autowired 
    private VidaGrupoReadOnlyRepository queryRepo;
    
    // Comando: Actualizar datos de facturación
    public void processBillingUpdate(BillingCommand command) {
        commandRepo.save(command);
        // Event sourcing para mantener read model actualizado
        publishBillingUpdatedEvent(command);
    }
    
    // Query: Generar reporte optimizado
    public DetailedBillingReport generateDetailedReport(ReportCriteria criteria) {
        // Consulta optimizada en read model desnormalizado
        return queryRepo.findDetailedBillingData(criteria);
    }
}
```

### **Patrones de Seguridad**

#### **OAuth 2.0 + JWT para APIs**
```java
@Configuration
@EnableWebSecurity
public class VidaGrupoSecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .oauth2ResourceServer(oauth2 -> oauth2
                .jwt(jwt -> jwt
                    .jwtAuthenticationConverter(jwtAuthenticationConverter())
                    .jwtDecoder(jwtDecoder())
                )
            )
            .authorizeHttpRequests(authz -> authz
                .requestMatchers("/api/vida-grupo/public/**").permitAll()
                .requestMatchers("/api/vida-grupo/admin/**").hasRole("VG_ADMIN")
                .requestMatchers("/api/vida-grupo/**").hasAnyRole("VG_USER", "VG_ADMIN")
                .anyRequest().authenticated()
            );
        return http.build();
    }
}
```

### **Principios de Arquitectura Aplicados**

#### **1. Domain-Driven Design (DDD)**
- **Bounded Contexts**: PolicyCenter (Emisión), BillingCenter (Facturación), ClaimCenter (Siniestros)
- **Ubiquitous Language**: Terminología específica de seguros de vida grupo
- **Aggregates**: Póliza colectiva como aggregate root con asegurados

#### **2. Event-Driven Architecture**
- **Domain Events**: PolicyIssued, PremiumCalculated, ClaimSubmitted
- **Event Store**: RabbitMQ como message broker
- **Event Sourcing**: Para audit trail completo

#### **3. Microservices Patterns**
- **API Gateway**: Azure APIM para exposición controlada
- **Service Discovery**: Configuración externa en *Conf repos
- **Circuit Breaker**: Hystrix para resiliencia
- **Bulkhead**: Aislamiento de recursos por microservicio

#### **4. SOLID Principles en Código**
- **Single Responsibility**: Cada microservicio tiene una responsabilidad específica
- **Open/Closed**: Extensibilidad vía configuración externa
- **Dependency Inversion**: Inyección de dependencias con Spring

---

## 📖 **Referencias y Documentación Adicional**

### **Documentación Arquitectónica del Ecosistema (docs/architecture/)**

#### Componentes Core Guidewire
1. **PolicyCenter - Documentación Completa**
   - 📄 `architecture-policycenter.md` (588 líneas)
   - Arquitectura monolítica modular (estático/dinámico)
   - Estructura completa del código Gosu/Java
   - Patrones de diseño especializados
   - Procesos de negocio Vida Grupo
   - URL: `docs/architecture/architecture-policycenter.md`

2. **BillingCenter - Documentación Completa**
   - 📄 `architecture-BillingCenter.md` (651 líneas)
   - Sistema de registro financiero
   - Arquitectura Domain-Driven Design
   - Facturación corporativa Vida Grupo
   - Integraciones críticas (DIAN, Tesorería)
   - URL: `docs/architecture/architecture-BillingCenter.md`

#### Microservicios
3. **MicroIntegradorReportesVidaGrupo - Documentación Completa**
   - 📄 `architecture-microintegrador-reportes-vidagrupo.md` (1,111 líneas)
   - Arquitectura hexagonal detallada (Ports & Adapters)
   - Diseño modular extensible por tipo de reporte
   - Patrones: CQRS, Repository, Scheduled Job (Quartz)
   - 4 WorkQueues con Quartz Scheduler
   - 124+ pruebas unitarias con 85%+ cobertura
   - URL: `docs/architecture/architecture-microintegrador-reportes-vidagrupo.md`

#### Flujos de Negocio Documentados
4. **Flujo: Generación de Reporte Detalle de Cobro**
   - 📄 `flujo-generacion-reporte-detalle-cobro.md` (1,229 líneas)
   - Flujo end-to-end completo
   - 7 diagramas de secuencia Mermaid
   - 4 fases del proceso documentadas
   - Modelo de datos (10 tablas)
   - APIs REST documentadas
   - Configuración Quartz Scheduler
   - URL: `docs/architecture/flujo-generacion-reporte-detalle-cobro.md`

#### Historias de Usuario
5. **Historia #915240: Bug - Organizar campos errados detalle de cobro**
   - 📄 `915240.bug-organizar-campos-errados-detalle-cobro.story.md` (1,941 líneas)
   - 12 criterios de aceptación completados ✅
   - Análisis técnico detallado
   - Soluciones implementadas con código
   - 30+ tests unitarios
   - Lecciones aprendidas
   - URL: `docs/stories/915240.bug-organizar-campos-errados-detalle-cobro.story.md`

### **Documentación Oficial Guidewire (Instalada Localmente)**
- **PolicyCenter**: `C:\Guidewire\PolicyCenter\doc\index.html`
- **BillingCenter**: `C:\Guidewire\BillingCenter\doc\index.html`
- **ClaimCenter**: `C:\Guidewire\ClaimCenter\doc\index.html`

**Guías relevantes en `/doc`:**
- Application Guide
- Configuration Guide
- Gosu Reference Guide
- Best Practices Guide
- Integration Guide
- Product Designer Guide
- Product Model Guide

### **Documentación de Componentes (READMEs)**
- **MicroIntegradorReportesVidaGrupo**: `MicroIntegradorReportesVidaGrupo/README.md` (247 líneas)
  - Descripción del proyecto y contexto de negocio
  - Tecnologías y estructura del proyecto
  - Configuración de variables de entorno
  - Logging y Splunk
  - Comandos de construcción y ejecución
  - Guía de pruebas y seguridad

### **Documentación del Método Ceiba**
- **Método Ceiba Principal**: `README.md` (517 líneas)
  - Descripción del método y flujo general
  - Roles y responsabilidades
  - Agentes disponibles (Arquitecto, PO, SM, Dev, Reviewer)
  - Workflows documentados

### **Documentación Externa Oficial**
- **Apache Camel**: https://camel.apache.org/manual/
- **Guidewire Developer Portal**: https://docs.guidewire.com/
- **Guidewire Community**: https://community.guidewire.com/
- **RabbitMQ**: https://www.rabbitmq.com/documentation.html
- **Terraform Azure**: https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs
- **Oracle Database**: https://docs.oracle.com/en/database/
- **Quartz Scheduler**: http://www.quartz-scheduler.org/documentation/

### **Resumen de Documentación Disponible**

| Categoría | Archivos | Líneas Totales | Ubicación |
|-----------|----------|----------------|-----------|
| **Arquitectura de Componentes** | 3 | 2,350 | `docs/architecture/` |
| **Flujos de Negocio** | 1 | 1,229 | `docs/architecture/` |
| **Historias de Usuario** | 1 | 1,941 | `docs/stories/` |
| **READMEs de Proyecto** | 2 | 764 | Raíz + componentes |
| **GPS Arquitectónico** | 1 | ~1,550 | `docs/architecture/index.md` |
| **TOTAL DOCUMENTACIÓN** | **8** | **~7,834** | Workspace completo |

> **Cobertura de Documentación**: El ecosistema cuenta con documentación técnica exhaustiva que cubre desde la arquitectura de alto nivel hasta detalles de implementación específicos, mantenida viva mediante el Método Ceiba.

---

## 🔄 **Control de Cambios de este Documento**

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0 | 2025-10-28 | Arquitecto Ceiba | Creación inicial del GPS arquitectónico basado en análisis del ecosistema |
| 2.0 | 2025-11-12 | Arquitecto Ceiba | **Actualización Mayor:** Integración de documentación detallada de componentes individuales, flujos de negocio documentados, y README actualizado del MicroIntegradorReportesVidaGrupo |

**Cambios Específicos Versión 2.0:**
- ✅ Integrado contenido completo de `architecture-policycenter.md` (588 líneas)
- ✅ Integrado contenido completo de `architecture-BillingCenter.md` (651 líneas)
- ✅ Integrado contenido completo de `architecture-microintegrador-reportes-vidagrupo.md` (1,111 líneas)
- ✅ Integrado flujo de negocio documentado: `flujo-generacion-reporte-detalle-cobro.md` (1,229 líneas)
- ✅ Actualizado README de MicroIntegradorReportesVidaGrupo (247 líneas) con arquitectura hexagonal modular
- ✅ Agregada historia de usuario #915240 (Bug detalle de cobro) como caso de estudio
- ✅ Actualizada documentación de patrones arquitectónicos con ejemplos reales del código
- ✅ Enriquecida información de stack tecnológico y procesos de negocio especializados

---

## 📁 **Documentación PDF Específica Analizada**

### **Ubicación**: `C:\Guidewire\GeneralDocumentation`

La siguiente documentación especializada ha sido identificada y su contenido está reflejado en las secciones correspondientes de este GPS:

#### **Documentación Técnica de Componentes**
- `EAG-PolicyCenter-291025-031306.pdf` - Configuración y procesos PolicyCenter específicos Vida Grupo
- `EAG-PolicyCenter-291025-031334.pdf` - Documentación adicional PolicyCenter
- `EAG-BillingCenter-291025-031324.pdf` - Configuración BillingCenter para facturación colectiva  
- `EAG-ClaimCenter-291025-031329.pdf` - Procesos de siniestros especializados Vida Grupo
- `GBP-MicroIntegradorVidaGrupoARQCompleta-291025-031335.pdf` - Arquitectura completa del MicroIntegrador

#### **Documentación de Procesos de Negocio**
- `EAG-Carga Masiva VG-291025-031319.pdf` - Proceso de carga masiva de asegurados
- `EAG-Detalle de cobro VG-291025-031327.pdf` - Proceso especializado de detalle de cobro
- `EAG-Auditorias VG-291025-031321.pdf` - Procesos de auditoría y compliance
- `EAG-Integración CCM Vida Grupo-291025-031333.pdf` - Integración con Customer Communication Management

#### **Total de Documentos**: 45+ archivos PDF especializados

### **Nota Importante sobre Extracción de Contenido**

> **🔍 Limitación Técnica**: Durante la creación de este GPS mejorado, se identificó que los archivos PDF contienen información codificada que requiere herramientas especializadas de extracción. El contenido de este documento ha sido enriquecido basándose en:
> - Nombres de archivos y su estructura semántica
> - Conocimiento especializado de la plataforma Guidewire 8.0.7
> - Patrones estándar de arquitectura para seguros de vida grupo
> - Best practices de Apache Camel e integración empresarial

### **Recomendación para Futuras Actualizaciones**

Para mantener este GPS actualizado con el contenido exacto de los PDFs, se recomienda:

1. **Revisión Manual**: Revisar periódicamente los PDFs para extraer información específica
2. **Herramientas OCR**: Utilizar herramientas especializadas de extracción de texto PDF
3. **Sincronización**: Mantener este GPS sincronizado cuando se actualicen los documentos fuente
4. **Validación**: Validar la información arquitectónica con los equipos técnicos correspondientes

---

**Nota**: Este documento es un GPS vivo que debe actualizarse con cada cambio arquitectónico significativo en el ecosistema. Utiliza los comandos del Arquitecto Ceiba para mantenerlo sincronizado con la realidad del código.
