# Validation Checklist - Documentar Arquitectura Base

## Estructura del Documento

- [ ] Todas las secciones están presentes según el template
- [ ] No quedan placeholders sin reemplazar ({{variable_name}})
- [ ] El formato Markdown es consistente y correcto
- [ ] Los diagramas Mermaid renderizan correctamente
- [ ] La fecha y autor están correctamente especificados

## Análisis de Documentación Existente

- [ ] Se analizó TODA la documentación existente especificada por el usuario
- [ ] Se siguieron TODAS las referencias cruzadas entre documentos
- [ ] Se creó un mapa consolidado de la información arquitectónica encontrada
- [ ] Se identificaron y documentaron las inconsistencias entre documentos
- [ ] Se marcaron explícitamente los gaps de información no documentada

## Cobertura del Ecosistema

- [ ] Se identificaron y listaron todos los repositorios del ecosistema
- [ ] Se documentaron los 3-5 repositorios más críticos
- [ ] Se mapeo la organización por dominios/funciones
- [ ] Se analizó la estructura de directorios y configuraciones clave
- [ ] Se validó que la información documentada coincide con la realidad del código

## Stack Tecnológico

- [ ] Se identificaron todas las tecnologías principales por módulo/dominio
- [ ] Se documentaron las versiones específicas encontradas
- [ ] Se validaron las tecnologías documentadas vs. la realidad del código
- [ ] Se identificaron tecnologías críticas no documentadas previamente
- [ ] Se documentaron los patrones arquitectónicos implementados

## Integraciones y Sistemas Externos

- [ ] Se identificaron TODOS los sistemas externos (IDPs, payment gateways, email services, etc.)
- [ ] Se documentaron TODOS los servicios de la nube (CDN, queues, monitoring, storage, etc.)
- [ ] Se mappearon todos los canales de comunicación entre sistemas
- [ ] Se documentaron los mecanismos de autenticación/autorización por capa
- [ ] Se identificaron los patrones de resilencia implementados
- [ ] TODOS los diagramas incluyen explícitamente los sistemas externos identificados

## Diagramas de Arquitectura

- [ ] Los diagramas Mermaid reflejan la realidad del proyecto analizado, no solo templates genéricos
- [ ] El diagrama de arquitectura general incluye TODOS los sistemas externos identificados
- [ ] El diagrama de flujo de integraciones muestra las comunicaciones reales del sistema
- [ ] Los diagramas son consistentes entre sí y con la descripción textual
- [ ] Los estilos y clasificaciones de nodos en los diagramas son apropiados

## Deuda Técnica y Estado Actual

- [ ] Se identificaron componentes específicos con deuda técnica
- [ ] Se documentaron las restricciones técnicas conocidas
- [ ] Se evaluó la realidad de testing por módulo/dominio
- [ ] Se identificaron gaps críticos de testing
- [ ] Se analizaron las dependencias externas y sus riesgos

## Comandos de Desarrollo

- [ ] Los comandos de desarrollo listados son reales y ejecutables en el proyecto
- [ ] Se incluyen comandos de setup, build, testing y deployment
- [ ] Los comandos de debugging y monitoreo son funcionales
- [ ] Se documentaron herramientas de logging y observabilidad

## Validación de Contenido

- [ ] La información técnica es precisa y actualizada
- [ ] Las descripciones son claras y específicas (no genéricas)
- [ ] La terminología es consistente a lo largo del documento
- [ ] Los ejemplos y configuraciones son reales del proyecto

## Completitud para Desarrollo

- [ ] Un desarrollador nuevo puede entender la arquitectura general
- [ ] Se proporcionan suficientes puntos de entrada para investigación detallada
- [ ] Los próximos pasos están claramente definidos
- [ ] Se identifican las dependencias críticas para el funcionamiento

## Elicitación Completa

- [ ] Se realizaron TODAS las preguntas obligatorias de contexto general
- [ ] Se obtuvieron respuestas sobre organización y documentación existente
- [ ] Se elicitó información sobre tecnologías e integraciones
- [ ] Se preguntó sobre patrones de desarrollo y historias de usuario
- [ ] Se prestó especial atención a sistemas externos durante todo el análisis

## Validación Final con Usuario

- [ ] Se presentó un resumen estructurado de la documentación generada
- [ ] Se solicitó validación explícita de exactitud de la información
- [ ] Se preguntó específicamente sobre repositorios, tecnologías e integraciones faltantes
- [ ] Se validó que los diagramas muestran correctamente la arquitectura
- [ ] Se obtuvo confirmación explícita del usuario antes de finalizar

## Verificación de Reglas Obligatorias

- [ ] **ANÁLISIS COMPLETO DE DOCUMENTACIÓN:** Se leyó toda la documentación existente y referencias
- [ ] **INCLUSIÓN DE SISTEMAS EXTERNOS:** Todos los diagramas incluyen sistemas externos identificados
- [ ] **ELICITACIÓN COMPLETA:** Se hicieron todas las preguntas obligatorias y se obtuvieron respuestas
- [ ] **VALIDACIÓN DE DIAGRAMAS:** Cada diagrama refleja la realidad del proyecto, no templates
- [ ] **COBERTURA DE INTEGRACIONES:** Se incluyen tanto sistemas internos como externos
- [ ] **COMANDOS FUNCIONALES:** Los comandos listados son reales y ejecutables
- [ ] **DEPENDENCIAS ACTUALIZADAS:** Las dependencias reflejan versiones reales encontradas
- [ ] **ARQUITECTURA VALIDADA:** Consistencia entre descripción textual y diagramas
- [ ] **GAPS IDENTIFICADOS:** Se listaron explícitamente las limitaciones de información
- [ ] **CONFIRMACIÓN FINAL OBLIGATORIA:** Se obtuvo confirmación explícita del usuario

## Issues Found

### Critical Issues

- [ ] No critical issues found

### Minor Issues

- [ ] No minor issues found

### Recommendations for Improvement

- [ ] No recommendations at this time

---

**Note:** Este checklist debe completarse antes de considerar el workflow como finalizado. Todos los elementos marcados como críticos deben estar completados sin excepción.
