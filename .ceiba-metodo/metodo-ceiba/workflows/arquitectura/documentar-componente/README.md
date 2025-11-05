# Workflow: Documentar Componente Individual

## Propósito

Este workflow documenta un componente de software específico del sistema, creando documentación técnica completa que sirva como guía práctica para desarrolladores y operadores. Está optimizado para componentes individuales within ecosistemas empresariales complejos.

## Cuándo Usar

**Usa este workflow cuando:**
- Necesitas documentar un componente específico en detalle
- El componente es crítico para el sistema y requiere documentación técnica completa
- Nuevos desarrolladores necesitan entender cómo funciona un componente particular
- Buscas crear documentación estándar para componentes del ecosistema
- Requieres documentar APIs, dependencias y deployment de un componente específico

## Cómo Ejecutar

### Activación del Workflow
Este workflow se ejecuta a través del sistema BMad Core. Para activarlo:
1. Carga el agente correspondiente del módulo
2. Selecciona la opción correspondiente del menú
3. O utiliza el comando trigger específico del workflow

### Inputs Esperados

El workflow te solicitará:

1. **Identificación del Componente:**
   - Nombre del componente
   - Ruta/directorio donde se encuentra
   - Tipo de componente (servicio, librería, API, frontend, etc.)

2. **Contexto Técnico:**
   - Tecnologías principales utilizadas
   - Propósito principal dentro del sistema

3. **Scope de Documentación:**
   - Documentación previa existente

## Outputs Generados

### Archivo Principal
- `{{architecture_sharded_location}}/architecture-{{component_name}}.md` - Documentación completa del componente

### Artefactos del Workflow
- **template.md** - Plantilla estructurada para la documentación
- **instructions.md** - Instrucciones detalladas del proceso de workflow
- **checklist.md** - Lista de validación completa para asegurar calidad
- **workflow.yaml** - Configuración y metadatos del workflow

### Template-Outputs Generados Durante el Proceso
- `step0_2_context_info` - Información de contexto del componente
- `step1_1_structure_info` - Análisis de estructura y organización  
- `step1_2_architecture_patterns` - Patrones arquitectónicos y tecnologías
- `step1_3_apis_interfaces` - APIs, eventos y interfaces
- `step1_4_dependencies` - Dependencias y relaciones
- `step1_5_deployment` - Configuración de despliegue y operaciones

### Estructura del Documento Generado
1. **📋 Overview** - Propósito, contexto de negocio, responsabilidades, ubicación
2. **🏗️ Architecture** - Stack tecnológico, patrones, estructura de código, diagramas
3. **🔌 APIs** - Endpoints, eventos, contratos de versionamiento
4. **📦 Dependencies** - Dependencias externas/internas, consumidores, gestión
5. **🚀 Deployment** - Configuración, comandos, pipeline, variables por ambiente

## Requisitos Previos

- Acceso al código fuente del componente
- Conocimiento de la arquitectura del componente
- Configuración válida en el módulo correspondiente
- Variable `architecture_sharded_location` definida en config

## Características Especiales

- **Análisis Exhaustivo**: Examina estructura, dependencias, configuraciones y deployment
- **Documentación Práctica**: Incluye comandos ejecutables y ejemplos reales
- **Diagramas Mermaid**: Genera diagramas conceptuales de la arquitectura
- **Tablas Estructuradas**: Organiza información compleja en formatos fáciles de consultar
- **Proceso de Validación**: Workflow estructurado en 3 fases con validación continua
- **Checklist Completo**: 80+ puntos de validación que cubren desde proceso hasta calidad final
- **Template-Outputs Trazables**: Cada step genera outputs específicos para máxima trazabilidad

## Proceso de Validación

### Fases del Workflow
1. **Contexto del Componente** - Identificación, contexto técnico y scope
2. **Análisis Técnico Completado** - Exploración exhaustiva de todos los aspectos
3. **Refinamiento Final** - Validación de completitud y utilidad práctica

### Uso del Checklist
El checklist.md incluye validaciones para:
- ✅ **Validación del Proceso** - Verificar que se completaron todos los steps
- 📋 **Estructura del Documento** - Format, jerarquía y estándares markdown
- 🎯 **Calidad del Contenido** - Completitud y precisión de cada sección
- 🔧 **Completitud Técnica** - Información suficiente para desarrolladores
- 📏 **Consistencia y Estándares** - Uniformidad y formato profesional
- 🔄 **Mantenibilidad** - Trazabilidad y actualizaciones futuras

## Próximos Pasos (Post-Instalación)

1. **Cargar agente del módulo** - Activar el agente correspondiente
2. **Seleccionar workflow** - Usar el menú para ejecutar documentar-componente
3. **Seguir el proceso** - Completar los steps del workflow
4. **Validar con checklist** - Usar checklist.md para asegurar calidad completa
