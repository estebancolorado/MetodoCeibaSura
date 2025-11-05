# Checklist de Validación - Documentar Componente Individual

## Validación del Proceso

### Contexto del Componente
- [ ] Identificación del componente completada (nombre, ruta, tipo de componente)
- [ ] Contexto técnico documentado (tecnologías principales, propósito del componente)
- [ ] Scope de documentación definido y documentación previa revisada
- [ ] Template-output step0_2_context_info generado correctamente

### Análisis Técnico Completado
- [ ] **Exploración de estructura**: Mapeo completo de directorios, archivos principales y organización
- [ ] **Revisión GPS arquitectónico**: Si existe {architecture_sharded_location}/index.md, fue consultado
- [ ] **Configuraciones identificadas**: Archivos de configuración, variables de entorno, manifiestos localizados
- [ ] **Patrones de arquitectura**: Patrones de diseño específicos documentados con justificación técnica
- [ ] **APIs y interfaces**: Endpoints, eventos, mensajería y contratos de versionado mapeados
- [ ] **Dependencias completas**: Análisis de dependencias externas/internas y consumidores realizado
- [ ] **Deployment y operaciones**: Scripts de build, monitoreo, logging y prerequisitos documentados
- [ ] Todos los template-outputs requeridos fueron generados (step1_1 a step1_5)

### Refinamiento Final
- [ ] Revisión de completitud realizada
- [ ] Validación de consistencia en nivel de detalle ejecutada
- [ ] Confirmación de utilidad práctica de la información

## Estructura del Documento

- [ ] Todas las secciones principales están presentes (Overview, Architecture, APIs, Dependencies, Deployment)
- [ ] No quedan placeholders sin reemplazar ({{variable_name}})
- [ ] El formato markdown es correcto y renderiza apropiadamente
- [ ] Los títulos siguen la jerarquía correcta (H1 para título principal, H2 para secciones principales)
- [ ] Los emojis de sección están presentes y son apropiados

## Calidad del Contenido

### Overview
- [ ] El propósito del componente está claramente definido en 1-2 párrafos
- [ ] El contexto de negocio explica por qué existe el componente
- [ ] Las responsabilidades principales están limitadas a 3-5 items específicos
- [ ] La ubicación incluye repositorio, ruta y tipo de componente

### Architecture
- [ ] El stack tecnológico especifica lenguaje, framework y versiones exactas
- [ ] Los patrones de diseño están identificados con justificación técnica específica
- [ ] La estructura del código muestra organización real del proyecto con módulos principales
- [ ] El diagrama Mermaid (si aplica) refleja la arquitectura actual del componente
- [ ] Puntos de entrada y configuración inicial están claramente documentados

### APIs
- [ ] Si el componente expone APIs: endpoints documentados con métodos, rutas, parámetros y respuestas
- [ ] APIs internas para comunicación con otros componentes documentadas
- [ ] Eventos y mensajería: eventos publicados/consumidos identificados (si aplica)
- [ ] Códigos de error incluyen casos específicos del componente
- [ ] Contratos y estrategia de versionado están definidos
- [ ] Si no aplica: sección marcada como "N/A" con explicación clara

### Dependencies
- [ ] Dependencias externas categorizadas por criticidad (🔴 🟡 🟢) incluyendo librerías y APIs de terceros
- [ ] Servicios externos documentados con su propósito específico
- [ ] Dependencias internas listan componentes del sistema requeridos
- [ ] Consumidores del componente están claramente identificados
- [ ] Puntos de integración específicos documentados (bases de datos, message queues, servicios externos)
- [ ] Comandos de gestión de dependencias son específicos para el tipo de proyecto

### Deployment
- [ ] Variables de entorno incluyen descripción, ejemplo y si son requeridas
- [ ] Archivo .env de ejemplo tiene valores realistas
- [ ] Scripts de build documentados: comandos de compilación, testing, empaquetado
- [ ] Comandos de desarrollo son ejecutables y específicos del proyecto
- [ ] Pipeline de despliegue describe etapas reales
- [ ] Prerequisitos de infraestructura claramente especificados
- [ ] Monitoreo y logging: herramientas y configuraciones de observabilidad documentadas
- [ ] Variables por ambiente están diferenciadas apropiadamente
- [ ] Instrucciones de rollback son claras y ejecutables

## Completitud Técnica

- [ ] La información es suficientemente detallada para un desarrollador nuevo
- [ ] Los comandos shell son correctos para el tipo de proyecto
- [ ] Las rutas de archivos son válidas y accesibles
- [ ] Los ejemplos de código/configuración son funcionales
- [ ] No hay información contradictoria entre secciones

## Consistencia y Estándares

- [ ] La terminología es consistente a lo largo del documento
- [ ] Los nombres de componentes/servicios coinciden en todas las secciones
- [ ] El nivel de detalle es consistente entre secciones similares
- [ ] Las tablas están completas y bien formateadas
- [ ] Los bloques de código tienen la sintaxis correcta

## Mantenibilidad

- [ ] La fecha de creación está registrada en notas de mantenimiento
- [ ] El autor está identificado
- [ ] El documento incluye instrucciones de actualización
- [ ] Las versiones de dependencias están especificadas para evitar obsolescencia

## Validación Final

### Issues Encontrados:
- [ ] **Críticos**: _Issues que previenen el uso del documento_
  - 

- [ ] **Importantes**: _Issues que afectan la calidad pero no bloquean el uso_
  - 

- [ ] **Menores**: _Mejoras menores de estilo o formato_
  - 

### Estado del Documento:
- [ ] ✅ **Listo para usar**: Documento completo y validado
- [ ] ⚠️ **Requiere ajustes menores**: Issues menores identificados
- [ ] ❌ **Requiere revisión major**: Issues críticos o importantes pendientes

### Próximos Pasos:
- [ ] Documento guardado en ubicación correcta: `{{architecture_sharded_location}}/architecture-{{component_name}}.md`
- [ ] Equipo de desarrollo notificado de nueva documentación
- [ ] Documentación agregada al índice arquitectónico (si existe)

---

**Fecha de Validación**: {{date}}  
**Validado por**: {{user_name}}