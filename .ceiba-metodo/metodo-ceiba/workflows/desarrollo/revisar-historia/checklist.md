# Checklist de Validación - Revisión de Código (Peer Review)

<critical>Este checklist es ejecutado como parte de: {project-root}/.ceiba-metodo/metodo-ceiba/workflows/desarrollo/revisar-historia/workflow.yaml</critical>
<critical>Validar TODOS los items antes de marcar el workflow como completado</critical>

## 1. Preparación y Contexto

- [ ] Historia de usuario localizada y leída completamente
- [ ] Estado de la historia es válido para revisión ('Lista para Revisión' o 'Desarrollo Completado')
- [ ] Secciones obligatorias parseadas correctamente: Status, Criterios de Aceptación, Tasks/Subtasks, Lista de Archivos
- [ ] Contexto de análisis arquitectónico cargado (si existe en la historia)
- [ ] GPS arquitectónico (index.md) cargado y revisado
- [ ] Estándares de código (coding-standards.md) cargados y revisados
- [ ] Documentación de componentes relevantes cargada (architecture-*.md, component-*.md)
- [ ] Flujos de negocio relacionados cargados (flujo-*.md, flow-*.md)
- [ ] Tech stack detectado correctamente (frameworks, versiones)

## 2. Revisión de Criterios de Aceptación

- [ ] TODOS los Criterios de Aceptación tienen evidencia de implementación (archivos y líneas específicas)
- [ ] TODOS los Criterios de Aceptación tienen tests correspondientes (unit/integration/E2E según aplique)
- [ ] Tabla de cobertura de ACs creada con estado de cada criterio
- [ ] Gaps de implementación o testing documentados explícitamente
- [ ] Cada AC incompleto tiene hallazgo de Alta Severidad asociado

## 3. Revisión de Seguridad (OWASP Top 10)

### 3.1 Inyección
- [ ] Verificado uso de queries parametrizadas / ORMs seguros (SQL, NoSQL)
- [ ] No se encontró construcción dinámica de comandos del sistema
- [ ] No se encontró inyección LDAP o XML insegura

### 3.2 Autenticación y Autorización
- [ ] Implementación de autenticación es robusta (no passwords en claro, hashing seguro)
- [ ] Tokens JWT (si aplica) usan algoritmos seguros y expiran apropiadamente
- [ ] Autorización verificada en TODOS los endpoints/rutas sensibles
- [ ] No hay bypass de autorización a nivel de objeto (IDOR)

### 3.3 Exposición de Datos Sensibles
- [ ] No hay secretos hardcodeados (passwords, API keys, tokens, conexiones DB)
- [ ] Datos sensibles no se loggean (passwords, PII, tokens)
- [ ] Comunicación usa HTTPS / TLS (no HTTP para datos sensibles)

### 3.4 Configuración de Seguridad
- [ ] CORS configurado correctamente (no `*` en producción sin justificación)
- [ ] Headers de seguridad presentes (CSP, X-Frame-Options, X-Content-Type-Options, etc.)
- [ ] No se exponen stack traces o errores detallados en producción
- [ ] Defaults son seguros (no configs inseguras por defecto)

### 3.5 XSS y Sanitización
- [ ] Outputs sanitizados correctamente en templates
- [ ] Content-Security-Policy configurado (si aplica frontend)
- [ ] No se usa innerHTML o eval con datos no confiables

### 3.6 Dependencias
- [ ] Versiones de dependencias revisadas en manifiestos
- [ ] No se detectaron dependencias con CVEs críticos conocidos
- [ ] Dependencias actualizadas a versiones seguras (si aplica)

## 4. Revisión de Calidad de Código

### 4.1 Manejo de Errores
- [ ] Todos los casos de error tienen manejo apropiado (try-catch, error handlers)
- [ ] Errores se loggean con contexto suficiente (sin datos sensibles)
- [ ] No hay catch vacíos o que silencian errores sin acción

### 4.2 Validación de Inputs
- [ ] Todos los inputs de usuario son validados (tipo, formato, rango)
- [ ] Validación ocurre en el servidor (no solo cliente)
- [ ] Mensajes de error de validación son claros pero no exponen internals

### 4.3 Async/Await y Concurrencia
- [ ] Operaciones asíncronas usan async/await correctamente (no callbacks anidados)
- [ ] Promesas tienen manejo de rechazo (.catch o try-catch)
- [ ] No hay race conditions evidentes
- [ ] Recursos compartidos tienen sincronización apropiada

### 4.4 Gestión de Recursos
- [ ] Conexiones de DB se cierran apropiadamente (finally, using, context managers)
- [ ] Archivos abiertos se cierran
- [ ] No hay memory leaks evidentes (listeners no removidos, referencias circulares)

### 4.5 Rendimiento
- [ ] No hay N+1 queries evidentes
- [ ] Operaciones costosas no están en loops innecesariamente
- [ ] Caching usado apropiadamente donde aplique

## 5. Revisión de Tests

### 5.1 Cobertura
- [ ] Cada Criterio de Aceptación tiene al menos un test
- [ ] Tests unitarios para lógica de negocio crítica
- [ ] Tests de integración para flujos completos
- [ ] Tests E2E para user journeys críticos (si aplica)

### 5.2 Calidad de Tests
- [ ] Aserciones son significativas (no solo `toBeDefined()` o `toExist()`)
- [ ] Casos borde están cubiertos (nulos, vacíos, límites, negativos)
- [ ] Tests son deterministas (no dependen de orden, timestamps, random)
- [ ] Fixtures y datos de test son claros y mantenibles
- [ ] No hay patrones de flakiness (timers sin mockear, red real, race conditions)

### 5.3 Nomenclatura y Organización
- [ ] Nombres de tests describen claramente qué se está testeando
- [ ] Tests siguen patrón AAA (Arrange, Act, Assert) o Given-When-Then
- [ ] Tests están organizados lógicamente (por feature, componente, etc.)

## 6. Revisión Arquitectónica

- [ ] Implementación sigue decisiones arquitectónicas documentadas en análisis
- [ ] Patrones arquitectónicos son respetados (capas, hexagonal, CQRS, etc.)
- [ ] Separación de responsabilidades es clara (SRP)
- [ ] Inyección de dependencias usada correctamente (no `new` hardcodeados)
- [ ] No hay violaciones de reglas de dependencias entre capas
- [ ] Componentes modificados son los correctos según análisis arquitectónico

## 7. Revisión de Estándares de Código

- [ ] Código sigue convenciones del documento coding-standards.md
- [ ] Nombres de variables/funciones/clases son descriptivos
- [ ] Funciones son pequeñas y hacen una sola cosa
- [ ] Complejidad ciclomática es manejable (< 10 idealmente)
- [ ] Código es legible sin comentarios excesivos
- [ ] Comentarios explican "por qué", no "qué"

## 8. Documentación de Revisión

- [ ] Sección "Revisión de Código (Peer Review)" agregada al final de la historia
- [ ] Subsecciones completas: Revisor, Fecha, Resultado, Resumen, Hallazgos, Cobertura ACs, Tests, Arquitectura, Seguridad, Mejores Prácticas, Action Items
- [ ] Hallazgos clasificados por severidad (Alta/Media/Baja) con emoji 🔴🟡🔵
- [ ] Cada hallazgo incluye: archivo, línea, rationale, sugerencia
- [ ] Tabla de cobertura de ACs es completa y precisa
- [ ] Resultado de revisión es justificado (Aprobado/Cambios Solicitados/Bloqueado)
- [ ] Action Items son concretos, accionables, y mapeados a ACs/archivos

## 9. Persistencia de Action Items

- [ ] Action Items normalizados con severidad, tipo, owner, referencias
- [ ] Si `persist_action_items` y `story_tasks`: subsección "Seguimiento de Revisión (Peer Review)" agregada a Tasks/Subtasks
- [ ] Cada action item es checkbox sin marcar con formato: `- [ ] [Peer-Review][Severidad] Descripción (AC #X, archivo:línea)`
- [ ] Action Items están ordenados por severidad (Alta → Media → Baja)

## 10. Actualización de Estado

- [ ] Change Log actualizado con entrada de revisión y fecha
- [ ] Si `update_status_on_result`: Status actualizado según resultado
  - Aprobado → "Revisión Aprobada"
  - Cambios Solicitados → "Cambios Solicitados"
  - Bloqueado → "Bloqueado - Revisión"
- [ ] Archivo de historia guardado correctamente

## 11. Salida del Workflow

- [ ] Mensaje de completitud mostrado con resumen ejecutivo
- [ ] Detalles incluyen: story_number, outcome, counts de hallazgos por severidad, action_item_count
- [ ] Próximos pasos son claros según el resultado de la revisión
- [ ] Usuario entiende qué hacer a continuación

---

## Criterios de Calidad de la Revisión

### ✅ Revisión de Alta Calidad
- Todos los ACs verificados con evidencia concreta
- Revisión de seguridad exhaustiva (10 categorías OWASP)
- Hallazgos son específicos (archivo:línea, no genéricos)
- Rationale claro para cada hallazgo
- Sugerencias de corrección concretas
- Severidades asignadas consistentemente

### ⚠️ Revisión Incompleta (NO ACEPTABLE)
- ACs no verificados o sin evidencia
- Hallazgos genéricos sin ubicación específica
- Sin rationale o sugerencias
- Revisión de seguridad superficial
- Tests no revisados en detalle

---

**Fecha de Validación**: {{date}}  
**Validado por**: {{user_name}}  
**Historia Revisada**: {{story_number}}

## Estado Final del Checklist

- [ ] TODOS los items marcados
- [ ] Revisión es de Alta Calidad (no Incompleta)
- [ ] Historia tiene documentación completa de revisión
- [ ] Action items persistidos correctamente
- [ ] Estado actualizado apropiadamente

**✅ Workflow `revisar-historia` completado exitosamente**
