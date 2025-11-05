# Desarrollar Historia de Usuario - Instructions

<critical>The workflow execution engine is governed by: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {installed_path}/workflow.yaml</critical>
<critical>ALWAYS communicate STRICTLY in {communication_language} regardless of the language used by the user</critical>
<critical>Generate all documents in {document_output_language}</critical>
<critical>This workflow implements an estimated user story by following the refined tasks, with iterative development, comprehensive testing, and status updates.</critical>
<critical>Each TASK in refinement already includes implementation + tests + configs together - do NOT separate them</critical>

<workflow>

<step n="1" goal="Cargar Historia y determinar modo de trabajo">

<ask>¿Qué historia de usuario deseas desarrollar?

Proporciona el número de la historia (ejemplo: 5) o la ruta completa del archivo.</ask>

<action>Buscar archivo en {dev_story_location} usando el número o nombre proporcionado</action>
<action>Resolver path completo del archivo encontrado y almacenar en {{story_file_path}}</action>

<check if="archivo no existe">
<action>HALT con error: "Historia no encontrada en {dev_story_location}"</action>
</check>

<action>Leer contenido completo del archivo de historia</action>
<action>Parsear secciones: Historia, Criterios de Aceptación, Tareas de Implementación, Dev Agent Record, File List, Change Log, Status, Revisión de Código (Peer Review)</action>

<!-- VERIFICAR CORRECCIONES PENDIENTES -->
<action>Buscar checkboxes sin marcar en "Revisión de Código (Peer Review)" → "Acciones Requeridas"</action>

<check if="tiene acciones pendientes">
  <ask>📋 Acciones de revisión pendientes: {{pending_count}}

¿Implementar correcciones? (Sí/No)</ask>
  
  <action if="respuesta == Sí">
    <action>Establecer {{working_on_review_corrections}} = true</action>
    <goto step="1.5"></goto>
  </action>
  
  <action if="respuesta == No">
    <action>HALT</action>
  </action>
</check>

<check if="NO tiene acciones pendientes">
  <action>Establecer {{working_on_review_corrections}} = false</action>
</check>

<!-- VERIFICAR ESTADO VÁLIDO -->
<check if="estado != 'Estimado (Developer)' AND estado != 'Cambios Solicitados' AND estado != 'En Desarrollo (Developer)'">
<action>HALT con error:</action>
<output>❌ ESTADO INCORRECTO

Estado actual: {{estado_actual}}

Estados válidos para desarrollo:
- "Estimado (Developer)" → Desarrollo inicial
- "Cambios Solicitados" → Implementar correcciones de revisión
- "En Desarrollo (Developer)" → Continuar desarrollo en progreso

Ejecuta primero:
- *refinamiento-tecnico (para refinar la historia)
- *estimar-historia-usuario (para estimar la historia)</output>
</check>

<!-- CONTINUAR SOLO SI NO ES MODO CORRECCIONES -->
<check if="{{working_on_review_corrections}} == false">

<check if="NO existe sección 'Tareas de Implementación' OR sección está vacía">
<action>HALT con error:</action>
<output>❌ SIN TAREAS DE IMPLEMENTACIÓN

La historia no contiene tareas definidas.

Ejecuta *refinamiento-tecnico para generar las tareas antes de desarrollar.</output>
</check>

<action>Parsear estructura de Tareas: extraer Fases → Componentes → Tareas por componente</action>

<check if="existe 'Fase 0: Infraestructura'">
<action>Establecer {{componente_actual}} = "Fase 0: Infraestructura"</action>
<output>⚠️ Detectada Fase 0 (Infraestructura) - Se ejecutará PRIMERO como prerequisito</output>
</check>

<check if="NO existe Fase 0">
<action>Identificar PRIMER componente con tareas incompletas y establecer como {{componente_actual}}</action>
</check>

<action>Identificar PRIMERA tarea incompleta [ ] dentro de {{componente_actual}}</action>

<check if="NO hay tareas incompletas en ningún componente">
<output>✅ Todas las tareas están completadas</output>
<goto step="5">Ir a completación final</goto>
</check>

<action>Actualizar estado de la historia a: "En Desarrollo (Developer)"</action>
<action>Guardar cambio de estado en el archivo</action>

<output>🚀 DESARROLLO INICIADO

Historia: {{story_file_path}}
Estado: En Desarrollo (Developer)

📦 Componente actual: {{componente_actual}}
📋 Primera tarea: {{primera_tarea_descripcion}}
📊 Tareas pendientes en este componente: {{num_tareas_pendientes_componente}}

Modo: COMPONENTE POR COMPONENTE (pausará entre componentes para revisión)</output>

</check>

</step>

<step n="1.5" goal="Implementar Correcciones de Revisión" if="{{working_on_review_corrections}} == true">

<action>Para cada acción pendiente de "Acciones Requeridas" (orden: Alta → Media → Baja):</action>
<action>Leer archivo referenciado en {{reference}} (formato: archivo:línea)</action>
<action>Implementar solución sugerida</action>
<action>Marcar checkbox: `- [ ]` → `- [x]`</action>
<action>Actualizar "Lista de Archivos" si no está ya incluido</action>

<action>Guardar cambios en archivo de historia</action>

<output>✅ Correcciones completadas: {{correcciones_count}}

Re-ejecutar *revisar-historia para validar</output>

<action>HALT</action>

</step>

<step n="2" goal="Implementar Tarea Completa" for-each="tarea">

<critical>Una TAREA incluye implementación + tests + configs según el refinamiento</critical>

<action>Identificar próxima tarea incompleta (checkbox [ ]) dentro del {{componente_actual}}</action>

<check if="NO hay más tareas incompletas en {{componente_actual}}">
<output>✅ TODAS LAS TAREAS DEL COMPONENTE COMPLETADAS</output>
<goto step="4">Validar componente completo</goto>
</check>

<action>Cargar documentación de estándares del proyecto (si existe y no se ha cargado):</action>
<action>Buscar {architecture_sharded_location}/{coding_standards_file}</action>
<action>Si existe: Leer contenido y extraer convenciones de nombres, patrones de código, estructura de archivos</action>
<action>Si NO existe: Usar patrones del refinamiento y código existente</action>

<action>Cargar contexto completo de la tarea actual</action>
<action>Fase y Componente al que pertenece</action>
<action>Descripción de la tarea</action>
<action>Complejidad estimada</action>
<action>Criterios de Aceptación vinculados</action>
<action>TODAS las subtareas (implementación, tests, configs juntos)</action>

<action>Revisar contexto global de la historia</action>
<action>Consideraciones Generales del Refinamiento</action>
<action>Estrategia de Testing definida</action>
<action>Patrones y convenciones del equipo</action>

<output>🔨 IMPLEMENTANDO TAREA

📦 Componente: {{componente_actual}}
📋 Tarea: {{tarea_descripcion}}
🎯 Complejidad: {{complejidad_tarea}}
🔗 ACs vinculados: {{ac_numbers}}</output>

<action>Planificar implementación</action>
<action>Analizar qué archivos se deben crear/modificar</action>
<action>Identificar patrones arquitectónicos a seguir</action>
<action>Determinar estrategia de testing</action>
<action>Escribir plan breve en Dev Agent Record → Debug Log</action>

<action>Implementar TODAS las subtareas en orden</action>
<action>Leer descripción completa de cada subtarea</action>
<action>Implementar EXACTAMENTE lo que pide (código + test + config)</action>
<action>Seguir patrones arquitectónicos del proyecto</action>
<action>Manejar casos edge y errores apropiadamente</action>

<critical>Código AUTODESCRIPTIVO: Usar nombres claros de variables/funciones en lugar de comentarios. Comentarios SOLO para: decisiones arquitectónicas no obvias. NO comentar qué hace el código (debe ser obvio por los nombres de metodos, variables, clases  y demás).</critical>

<check if="3 intentos consecutivos de implementación fallan">
<action>HALT y solicitar orientación del usuario</action>
</check>

<check if="falta configuración requerida">
<action>HALT con error: "No se puede proceder sin: {{config_faltante}}"</action>
</check>

<action>Documentar en Dev Agent Record → Debug Log</action>
<action>Decisiones técnicas (si difieren del refinamiento, explicar por qué)</action>
<action>Problemas encontrados y soluciones aplicadas</action>
<action>Cambios respecto a lo planificado</action>
<action>Archivos modificados/creados</action>

<output>✅ Implementación completada</output>

</step>

<step n="3" goal="Marcar Tarea Completa">

<action>✅ Marcar checkbox principal de la tarea con [x]</action>
<action>✅ Marcar checkboxes de TODAS las subtareas con [x]</action>

<action>Actualizar sección File List del archivo de historia con archivos modificados/creados/eliminados</action>
<action>Agregar entrada en sección Dev Agent Record → Completion Notes (tarea, componente, enfoque técnico, decisiones importantes)</action>
<action>Agregar entrada en sección Change Log (descripción breve del cambio, fecha, usuario)</action>
<action>Guardar archivo de historia</action>

<output>✅ TAREA COMPLETADA

Tarea: {{tarea_descripcion}}
Componente: {{componente_actual}}
Progreso en componente: {{tareas_completadas_componente}}/{{tareas_totales_componente}}
</output>

<action>Verificar si el {{componente_actual}} está completado (todas sus tareas marcadas [x])</action>

<check if="componente actual completado">
<goto step="4">Validar componente completo</goto>
</check>

<check if="quedan más tareas en {{componente_actual}}">
<output>⏭️ Continuando con siguiente tarea en {{componente_actual}}</output>
<goto step="2">Continuar con siguiente tarea</goto>
</check>

</step>

<step n="4" goal="Validar Componente Completado">

<output>✅ COMPONENTE COMPLETADO: {{componente_actual}}

📊 Resumen del componente:
- Tareas completadas: {{tareas_completadas_componente}}/{{tareas_totales_componente}}
- Archivos modificados en este componente: {{archivos_modificados_componente}}
</output>

<action>Determinar comando de build del proyecto (inferir de package.json, pom.xml, build.gradle, etc.) si no lo encuentras puedes buscarlo en la documentación del componente </action>
<action>Ejecutar build si el proyecto requiere compilación</action>
<check if="build falla">
<action>STOP y corregir antes de continuar</action>
<goto step="2">Volver a implementación</goto>
</check>

<action>Determinar comando de tests del proyecto</action>
<action>Ejecutar suite COMPLETA de tests (verificar que no hay regresiones)</action>
<check if="tests fallan">
<action>STOP y corregir antes de continuar</action>
<goto step="2">Volver a implementación</goto>
</check>

<action>Ejecutar linting y code quality (si configurado)</action>
<action>Validar que implementación del componente cumple ACs vinculados</action>

<action>Identificar siguiente componente con tareas pendientes (si existe)</action>

<check if="existe siguiente componente con tareas">
<output>📦 Siguiente componente: {{siguiente_componente}}
📋 Tareas pendientes: {{tareas_pendientes_siguiente_componente}}</output>
<ask>Indica "Continuar" cuando desees avanzar al siguiente componente</ask>
<action>Actualizar {{componente_actual}} = {{siguiente_componente}}</action>
<goto step="2">Continuar con siguiente componente</goto>
</check>

<check if="NO quedan más componentes con tareas pendientes">
<output>✅ TODOS LOS COMPONENTES COMPLETADOS</output>
<goto step="5">Ir a completación final</goto>
</check>

</step>

<step n="5" goal="Validación Final y Peer Review">

<critical>Validar tareas de desarrollo completadas, ignorar tareas manuales pendientes</critical>

<action>Re-escanear archivo de historia COMPLETO</action>
<action>Identificar tareas MANUALES (keywords: "Ejecutar pipeline", "Crear PR", "Deployment", "QA", "Diagnosticador", "pruebas manuales", "Tests manuales", "Pull Request")</action>
<action>Verificar que TODAS las tareas NO-MANUALES estén marcadas [x]</action>

<check if="existen tareas de desarrollo incompletas (NO manuales)">
<output>⚠️ TAREAS DE DESARROLLO INCOMPLETAS

{{lista_tareas_desarrollo_incompletas}}

NO es posible proceder.</output>
<goto step="2">Volver a implementar</goto>
</check>

<action>Ejecutar suite de regresión COMPLETA final</action>
<action>Confirmar que NADA se rompió</action>

<check if="fallos de regresión">
<action>DETENER - Resolver fallos</action>
<goto step="2">Volver a corregir</goto>
</check>

<action>Confirmar File List completa</action>
<action>Validar TODOS los criterios de aceptación satisfechos</action>
<action>Ejecutar Definition of Done checklist si existe</action>

<check if="algún AC NO satisfecho OR DoD incompleto">
<action>HALT con error</action>
</check>

<action>Actualizar estado: "Lista para Revisión"</action>
<action>Guardar cambio</action>

<output>✅ DESARROLLO COMPLETADO

Estado: Lista para Revisión
Tareas automatizadas: {{tareas_desarrollo_completadas}}/{{tareas_desarrollo_total}} ✅
Tareas manuales pendientes: {{tareas_manuales_count}}

🔍 SIGUIENTE PASO OBLIGATORIO:
*revisar-historia

Tareas manuales (QA/Deployment) se ejecutan DESPUÉS del peer review.</output>

<action>HALT</action>

</step>

<step n="6" goal="Resumen y Próximos Pasos">

<output>✅ DESARROLLO COMPLETADO EXITOSAMENTE

📊 RESUMEN:

Historia: #{story_number}
Título: {{titulo_historia}}
Estado: Lista para Revisión
Desarrollo completado: {date}

📈 MÉTRICAS:
- Componentes implementados: {{num_componentes}}
- Tareas implementadas: {{num_tareas_total}}
- Archivos modificados: {{num_archivos_modificados}}
- Tests ejecutados: {{num_tests_total}}
- Tests pasando: {{num_tests_pasando}}/{{num_tests_total}}

📁 Actualizado: {dev_story_location}/{story_number}.story.md

🎯 PRÓXIMOS PASOS:
1. Revisar código implementado
2. Validar funcionamiento en ambiente local
3. Ejecutar pruebas manuales si aplica

</output>

</step>

<step n="7" goal="Validación y Entrega" optional="true">

<action>Validar contra checklist en {installed_path}/checklist.md</action>
<action>Preparar resumen conciso en sección Dev Agent Record → Completion Notes</action>
<action>Comunicar que la historia está Lista para Revisión</action>

</step>

</workflow>