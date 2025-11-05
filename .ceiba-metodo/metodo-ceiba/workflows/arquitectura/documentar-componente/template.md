# Componente: {{component_name}}

## 📋 **Overview**

### Propósito

<!-- Formato: Descripción de qué hace el componente y por qué existe -->
{{step0_2_context_info}}

### Contexto de Negocio

<!-- Formato: Explicación del contexto de negocio y propósito dentro del sistema más amplio -->
{{step1_1_structure_info}}

### Responsabilidades Principales

<!-- Formato: Lista con viñetas de 3-5 responsabilidades principales -->
{{step0_2_context_info}}

### Ubicación

<!-- Formato: Lista de datos técnicos de ubicación:
- **Repositorio**: {{repository_name}}
- **Ruta**: {{component_path}}  
- **Tipo**: {{component_type}} -->
{{step0_2_context_info}}

## 🏗️ **Architecture**

### Stack Tecnológico

<!-- Formato: Lista de tecnologías:
- **Lenguaje**: lenguaje principal
- **Framework**: framework principal
- **Versión**: versión específica
- **Otras tecnologías**: librerías/herramientas adicionales -->
{{step1_2_architecture_patterns}}

### Patrones de Diseño

<!-- Formato: Descripción de los patrones arquitectónicos utilizados:
- **Patrón Principal**: patrón usado - ej: MVC, Repository, Clean Architecture
- **Justificación**: por qué se eligió este patrón -->
{{step1_2_architecture_patterns}}

### Estructura del Código

<!-- Formato: Estructura en árbol con comentarios:
```
{componente}/
├── src/
│   ├── controllers/     # descripción
│   ├── services/        # descripción
│   ├── models/          # descripción
│   └── utils/           # descripción
├── tests/               # descripción
├── config/              # descripción
└── docs/                # descripción
``` -->
{{step1_1_structure_info}}

### Diagrama Conceptual

<!-- Formato: Diagrama Mermaid de la arquitectura interna, adaptado según la arquitectura real del componente:

graph TB
    A[Controller Layer] --> B[Service Layer]
    B --> C[Repository Layer]
    C --> D[Database]

    A --> E[External APIs]
    B --> F[Message Queue]

    classDef controller fill:#e1f5fe
    classDef service fill:#f3e5f5
    classDef data fill:#e8f5e8

    class A controller
    class B service
    class C,D data

Nota: Adaptar según la arquitectura real del componente -->
{{step1_2_architecture_patterns}}

## 🔌 **APIs**

### Endpoints Expuestos

<!-- Formato: Si el componente expone APIs:

#### REST Endpoints

| Método | Ruta                   | Descripción | Parámetros        | Respuesta         |
| ------ | ---------------------- | ----------- | ----------------- | ----------------- |
| GET    | `/api/{resource}`      | descripción | parámetros        | tipo de respuesta |
| POST   | `/api/{resource}`      | descripción | body schema       | tipo de respuesta |
| PUT    | `/api/{resource}/{id}` | descripción | parámetros + body | tipo de respuesta |
| DELETE | `/api/{resource}/{id}` | descripción | parámetros        | tipo de respuesta |

#### Códigos de Error

| Código | Descripción    | Casos             |
| ------ | -------------- | ----------------- |
| 400    | Bad Request    | casos específicos |
| 401    | Unauthorized   | casos específicos |
| 404    | Not Found      | casos específicos |
| 500    | Internal Error | casos específicos |

#### Contratos y Versionamiento

- **Estrategia de versionado**: descripción de la estrategia
- **Versión actual**: versión actual de la API
- **Breaking changes**: política de cambios -->
{{step1_3_apis_interfaces}}

### Eventos y Mensajería

<!-- Formato: Si aplica:

#### Eventos Publicados

| Evento    | Descripción | Payload  | Consumidores             |
| --------- | ----------- | -------- | ------------------------ |
| `evento1` | descripción | `schema` | componentes que escuchan |
| `evento2` | descripción | `schema` | componentes que escuchan |

#### Eventos Consumidos

| Evento    | Descripción | Origen            | Acción                |
| --------- | ----------- | ----------------- | --------------------- |
| `evento1` | descripción | componente origen | qué hace al recibirlo |
| `evento2` | descripción | componente origen | qué hace al recibirlo | --> |
{{step1_3_apis_interfaces}}

### **N/A**

<!-- Formato: Si el componente no expone APIs públicas o interfaces externas, explicar brevemente por qué (ej: "Es una librería interna", "Solo para uso interno del módulo X") -->
{{step1_3_apis_interfaces}}

## 📦 **Dependencies**

### Dependencias Externas

#### Librerías Críticas

<!-- Formato: Tabla de librerías con criticidad:
| Librería  | Versión | Propósito       | Criticidad   |
| --------- | ------- | --------------- | ------------ |
| librería1 | versión | para qué se usa | 🔴 Crítica    |
| librería2 | versión | para qué se usa | 🟡 Importante |
| librería3 | versión | para qué se usa | 🟢 Opcional   | --> |
{{step1_4_dependencies}}

#### Servicios Externos

<!-- Formato: Lista de servicios externos:
- **Servicio Externo 1**: descripción y para qué se usa
- **Servicio Externo 2**: descripción y para qué se usa -->
{{step1_4_dependencies}}

### Dependencias Internas

#### Componentes del Sistema

<!-- Formato: Lista de componentes internos:
- **Componente A**: para qué lo usa
- **Componente B**: para qué lo usa -->
{{step1_4_dependencies}}

#### Bases de Datos

<!-- Formato: Lista de bases de datos:
- **Base de Datos 1**: qué tablas/colecciones usa
- **Base de Datos 2**: qué tablas/colecciones usa -->
{{step1_4_dependencies}}

### Quién Usa Este Componente

#### Consumidores Directos

<!-- Formato: Lista de consumidores directos:
- **Componente X**: cómo lo usa
- **Componente Y**: cómo lo usa -->
{{step1_4_dependencies}}

#### Consumidores Indirectos

<!-- Formato: Lista de consumidores indirectos:
- **Sistema Z**: a través de qué componente -->
{{step1_4_dependencies}}

### Gestión de Dependencias

<!-- Formato: Comandos de gestión:
```bash
# Comandos para actualizar dependencias
comandos específicos para el tipo de proyecto

# Verificación de vulnerabilidades
comandos de seguridad

# Auditoría de dependencias
comandos de auditoría
``` -->
{{step1_4_dependencies}}

## 🚀 **Deployment**

### Configuración de Entorno

#### Variables de Entorno Requeridas

<!-- Formato: Tabla de variables de entorno:
| Variable | Descripción | Ejemplo         | Requerida  |
| -------- | ----------- | --------------- | ---------- |
| `VAR_1`  | descripción | `valor ejemplo` | ✅ Sí       |
| `VAR_2`  | descripción | `valor ejemplo` | ⚠️ Opcional |
| `VAR_3`  | descripción | `valor ejemplo` | ✅ Sí       | --> |
{{step1_5_deployment}}

#### Archivo .env de Ejemplo

<!-- Formato: Ejemplo de archivo .env:
```bash
# Configuración de desarrollo
VAR_1=valor_desarrollo
VAR_2=valor_opcional
VAR_3=valor_requerido

# Configuración específica del componente
COMPONENT_SPECIFIC_VAR=valor
``` -->
{{step1_5_deployment}}

### Comandos de Desarrollo

#### Setup Inicial

<!-- Formato: Comandos de setup:
```bash
# Instalación de dependencias
comando de instalación

# Configuración inicial
comandos de setup

# Verificación de setup
comando de verificación
``` -->
{{step1_5_deployment}}

#### Compilación

<!-- Formato: Comandos de compilación:
```bash
# Build de desarrollo
comando de build dev

# Build de producción
comando de build prod

# Verificación de build
comando de verificación
``` -->
{{step1_5_deployment}}

#### Testing

<!-- Formato: Comandos de testing:
```bash
# Tests unitarios
comando de tests unitarios

# Tests de integración
comando de tests integración

# Coverage
comando de coverage

# Linting
comando de linting
``` -->
{{step1_5_deployment}}

#### Ejecución Local

<!-- Formato: Comandos de ejecución:
```bash
# Modo desarrollo
comando de desarrollo

# Modo producción local
comando de producción

# Debug mode
comando de debug
``` -->
{{step1_5_deployment}}

### Pipeline de Despliegue

#### Prerequisitos de Infraestructura

<!-- Formato: Lista de prerequisitos:
- **Requisito 1**: descripción del requisito
- **Requisito 2**: descripción del requisito -->
{{step1_5_deployment}}

#### Etapas del Pipeline

<!-- Formato: Lista numerada de etapas:
1. **Build Stage**
   - descripción de lo que sucede
   - Comandos: `comandos específicos`

2. **Test Stage**
   - descripción de tests ejecutados
   - Comandos: `comandos específicos`

3. **Deploy Stage**
   - descripción del despliegue
   - Comandos: `comandos específicos` -->
{{step1_5_deployment}}

#### Variables de Entorno por Ambiente

<!-- Formato: Variables por ambiente:

**Desarrollo:**
```bash
variables específicas de desarrollo
```

**Staging:**
```bash
variables específicas de staging
```

**Producción:**
```bash
variables específicas de producción
``` -->
{{step1_5_deployment}}

### Buenas Prácticas de Despliegue

<!-- Formato: Lista de buenas prácticas:
- **Práctica 1**: descripción y justificación
- **Práctica 2**: descripción y justificación
- **Práctica 3**: descripción y justificación -->
{{step1_5_deployment}}

### Pasos Manuales

<!-- Formato: Si existen pasos manuales:
1. **Paso Manual 1**: descripción detallada
2. **Paso Manual 2**: descripción detallada

Si no hay pasos manuales: "Todo el proceso de despliegue está automatizado" -->
{{step1_5_deployment}}

### Rollback

<!-- Formato: Comandos de rollback:
```bash
# Comando para rollback
comando de rollback

# Verificación post-rollback
comando de verificación
``` -->
{{step1_5_deployment}}

### Monitoreo Post-Despliegue

<!-- Formato: Herramientas de monitoreo:
- **Health Checks**: `comando o URL de health check`
- **Logs**: `comando para acceder a logs`
- **Métricas**: `herramienta/comando para métricas` -->
{{step1_5_deployment}}

---

**📌 Esta documentación debe mantenerse actualizada con cada cambio significativo en el componente.**

## 📝 **Notas de Mantenimiento**

<!-- Formato: Tabla de cambios -->

| Fecha   | Versión | Cambios               | Autor   |
| ------- | ------- | --------------------- | ------- |
| {fecha} | 1.0     | Documentación inicial | {autor} |


_Documentación generada con Método Ceiba - Arquitecto_
