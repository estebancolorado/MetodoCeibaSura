# Refinamiento Técnico - Instructions

<critical>The workflow execution engine is governed by: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {installed_path}/workflow.yaml</critical>
<critical>ALWAYS communicate STRICTLY in {communication_language} regardless of the language used by the user</critical>
<critical>Generate all documents in {document_output_language}</critical>
<critical>This workflow enriches an existing user story file with technical context, implementation task breakdown, and preparation for estimation. Modifies the original story file in-place.</critical>

<workflow>

<step n="1" goal="Cargar y Validar Historia a Refinar">

<critical>Este workflow MODIFICA una historia existente - NO crea archivo nuevo</critical>
<critical>La historia cargada aquí será enriquecida y sobrescrita en Step 7</critical>

<ask>¿Qué historia de usuario deseas refinar?

Opciones:
1. Proporciona el número de la historia (ejemplo: 5 para refinar 5.story.md)
2. Especifica la ruta completa del archivo de historia

Ingresa el número o ruta:</ask>

<action>Calcular ruta completa: {dev_story_location}/{story_number}.story.md</action>

<check if="no existe archivo de historia">
<action>HALT con error: "Historia no encontrada en ruta calculada"</action>
</check>

<action>Cargar contenido completo de la historia</action>
<action>Verificar estado actual de la historia</action>

<check if="estado == 'Borrador (PO)' AND sin análisis arquitectónico">
<action>Mostrar ADVERTENCIA sobre refinamiento sin análisis arquitectónico:</action>
<ask>⚠️ REFINAMIENTO SIN ANÁLISIS ARQUITECTÓNICO

La historia está en estado 'Borrador (PO)' sin análisis arquitectónico previo.

RECOMENDACIÓN FUERTE: Ejecutar analisis-y-diseno en el agente arquitecto antes del refinamiento para:
✅ Obtener decisiones arquitectónicas validadas
✅ Mejor comprensión de componentes afectados  
✅ Refinamiento técnico más preciso y completo
✅ Separación clara de responsabilidades arquitectónicas vs técnicas

OPCIONES:
1. PAUSA RECOMENDADA - Ejecutar analisis-y-diseno primero (calidad superior)
2. CONTINUAR LIMITADO - Proceder sin análisis arquitectónico (resultado menos preciso)

¿Prefieres pausar para análisis arquitectónico (1) o continuar con refinamiento limitado (2)?</ask>

<check if="usuario elige opción 1">
<action>HALT con mensaje: "Por favor ejecuta el agente arquitecto con *analisis-y-diseno primero"</action>
</check>
</check>

<check if="estado == 'Analizado (Arquitecto)'">
<action>Estado: "Refinado (Developer) - Basado en Análisis Arquitectónico"</action>
</check>

<check if="else">
<action>Estado: "Refinado (Developer) - Refinamiento Directo"</action>
</check>

</step>

<step n="2" goal="Extracción de Decisiones Arquitectónicas">

<critical>Este workflow DEPENDE del análisis arquitectónico previo</critical>
<critical>El arquitecto ya definió: patrones, componentes, hitos de implementación</critical>
<critical>El Developer NO re-analiza arquitectura - CONSUME las decisiones del arquitecto</critical>

<action>Extraer de la sección "Análisis Arquitectónico (Arquitecto)" en la historia:</action>

<action>**Decisiones de Diseño:**</action>
<action>- Patrón arquitectónico seleccionado y justificación</action>
<action>- Componentes principales identificados (nuevos o modificados)</action>
<action>- Hitos de implementación con dependencias</action>
<action>- Especificaciones técnicas (interfaces, APIs, modelos)</action>
<action>- Riesgos arquitectónicos y estrategias de mitigación</action>

<action>**Notas Técnicas del Arquitecto (si existen):**</action>
<action>- Detalles técnicos útiles que el arquitecto mencionó</action>
<action>- Recomendaciones de implementación específicas</action>
<action>- Advertencias o consideraciones técnicas</action>

<action>Revisar referencias arquitectónicas consultadas por el arquitecto</action>
<action>Usar esta información como BASE para el análisis técnico profundo</action>

</step>

<step n="3" goal="Análisis Técnico Profundo del Código Base">

<critical>Ahora sí bajar a DETALLES DE IMPLEMENTACIÓN - trabajo del Developer</critical>

<action>Cargar documentación de estándares del proyecto (si existe):</action>
<action>Buscar {architecture_sharded_location}/{coding_standards_file}</action>
<action>Si existe: Leer contenido completo y extraer convenciones de nombres, patrones de código, estructura de archivos, reglas de estilo</action>
<action>Si NO existe: Continuar con análisis de código existente para inferir estándares</action>

<action>Tomar cada HITO del arquitecto y traducirlo a archivos específicos:</action>

<action>**Para cada componente identificado por el arquitecto:**</action>
<action>- Identificar archivos exactos a modificar/crear</action>
<action>- Buscar clases, interfaces o módulos específicos</action>
<action>- Revisar código existente en esos archivos</action>
<action>- Analizar patrones de código utilizados</action>
<action>- Identificar convenciones del equipo</action>

<action>Buscar funcionalidades existentes similares en los componentes identificados</action>
<action>Identificar patrones arquitectónicos implementados en los módulos relevantes</action>
<action>Analizar convenciones de código utilizadas en las capas específicas</action>
<action>Revisar estructuras de datos y modelos relacionados</action>

<action>Investigar puntos de integración con componentes y servicios identificados</action>
<action>Analizar dependencias existentes en los módulos arquitectónicos relevantes</action>
<action>Revisar APIs internas/externas utilizadas</action>

<action>Documentar hallazgos consolidados con contexto arquitectónico</action>

</step>

<step n="3.5" goal="Análisis de Estrategia de Testing">

<critical>Las pruebas automatizadas son FUNDAMENTALES - analizar estrategia completa de testing</critical>

<substep n="3.5.1" goal="Revisar Documentación Arquitectónica sobre Testing">

<action>Consultar **GPS Arquitectónico** ({architecture_sharded_location}/index.md):</action>
<action>- Buscar sección de "Testing" o "Quality Assurance"</action>
<action>- Identificar frameworks y herramientas estándar del proyecto</action>
<action>- Extraer políticas de cobertura de código</action>
<action>- Identificar estrategias de testing por capa</action>

<action>Consultar **Documentación de Componentes** ({architecture_sharded_location}/architecture-*.md, component-*.md, testing-*.md):</action>
<action>- Buscar documentación específica de testing con comodines: testing-*.md, test-*.md, qa-*.md</action>
<action>- Revisar documentación de componentes afectados para ver sus estrategias de testing</action>
<action>- Identificar frameworks específicos por componente (ej: Jest para frontend, JUnit para backend)</action>
<action>- Extraer convenciones de nombres de archivos de test</action>
<action>- Identificar ubicaciones de Test Data Builders si están documentados</action>

</substep>

<substep n="3.5.2" goal="Buscar Archivos de Pruebas Existentes en el Código">

<action>Analizar estructura del proyecto para identificar convenciones de testing:</action>
<action>- Revisar estructura de carpetas del proyecto (explorar árbol de directorios)</action>
<action>- Identificar carpetas/módulos dedicados a pruebas </action>
<action>- Buscar archivos con extensiones/sufijos de test según el stack tecnológico detectado</action>
<action>- Analizar dependencias del proyecto (package.json, pom.xml, requirements.txt, go.mod, gradle, etc.) para inferir frameworks de testing</action>

<action>Identificar ubicación y convenciones de cada tipo de test que EXISTA en el proyecto:</action>
<action>- **Tests unitarios:** ¿Dónde están? ¿Qué convención de nombres usan? ¿Qué framework?</action>
<action>- **Tests de integración:** ¿Existen? ¿Cómo se diferencian de unitarios? ¿En qué carpeta/sufijo?</action>
<action>- **Tests E2E:** ¿Hay carpeta/proyecto separado? ¿Qué herramienta usan?</action>
<action>- **Tests de performance:** ¿Existe infraestructura? ¿Qué herramienta? ¿Dónde están los scripts?</action>

<mandate>NO asumir estructura estándar - DESCUBRIR la estructura real del proyecto</mandate>
<mandate>Cada proyecto organiza tests diferente según tecnología, framework y equipo</mandate>
<mandate>Documentar convenciones encontradas para reutilizar en esta historia</mandate>

</substep>

<substep n="3.5.3" goal="Analizar Historias Refinadas Similares">

<action>Revisar historias similares refinadas ({dev_story_location}/*.story.md) para identificar:</action>
<action>- ¿Qué frameworks de testing se usaron en componentes similares?</action>
<action>- ¿Qué patrones de testing se aplicaron?</action>
<action>- ¿Qué utilidades de datos de prueba se crearon o reutilizaron?</action>
<action>- ¿Cómo se gestionan dependencias en tests unitarios? (inferir uso y estrategia de mocks)</action>
<action>- ¿Qué cobertura se logró?</action>

</substep>

<substep n="3.5.4" goal="Determinar Tipos de Tests Requeridos por Capa">

<mandate>CRITERIOS POR CAPA (basados en documentación arquitectónica):</mandate>
<action>- **APIs/Controladores** → Tests de integración (request/response completo, puede incluir BD en memoria o testcontainers)</action>
<action>- **Capa de Negocio/Servicios** → Tests unitarios (lógica aislada con mocks de dependencias, inferir estrategia de mocking del proyecto)</action>
<action>- **Dominio/Entidades** → Tests unitarios (validaciones, reglas de negocio sin dependencias externas)</action>
<action>- **Componentes UI** → Tests de componente (si aplica)</action>
<action>- **Flujos completos E2E** → SOLO recomendar si: (a) la documentación arquitectónica lo especifica, O (b) ya existen tests E2E en el proyecto</action>

<mandate>NO recomendar tests E2E por defecto - solo si están documentados o implementados en el proyecto</mandate>

</substep>

<substep n="3.5.5" goal="Evaluar Necesidad de Tests de Carga/Performance">

<check if="historia involucra APIs OR procesos concurrentes OR alta concurrencia esperada">
<action>Marcar necesidad de tests de carga</action>
<action>Revisar documentación arquitectónica sobre performance testing</action>
<action>Buscar archivos de herramientas según stack del proyecto (no limitarse a estos ejemplos):</action>
<action>- JMeter: `**/*.jmx`, `jmeter/**/*`</action>
<action>- K6: `**/*.k6.*`, `k6/**/*`</action>
<action>- Gatling: `gatling/**/*`</action>
<action>- Locust: `locust/**/*`, `**/*locust*`</action>
<action>Si existen tests de carga similares, identificar para reutilizar estructura</action>
<action>Si NO existen, documentar necesidad de crear baseline de performance según estándares del proyecto</action>
</check>

</substep>

<substep n="3.5.6" goal="Identificar Test Data Builders Reutilizables">

<action>Buscar utilidades de construcción de datos de prueba existentes en el proyecto:</action>
<action>- En documentación: Revisar si arquitectura documenta builders/helpers/factories disponibles</action>
<action>- En código: Buscar clases/funciones dedicadas a crear datos de prueba (analizar nombres, ubicaciones, patrones)</action>
<action>- Evaluar si se pueden reutilizar para esta historia</action>
<action>- Si NO existen y se necesitan datos complejos, recomendar crearlos siguiendo patrones y convenciones del proyecto</action>

<mandate>Cada proyecto organiza test utilities diferente según lenguaje y equipo</mandate>

</substep>

<substep n="3.5.7" goal="Consolidar Estrategia de Testing">

<action>Documentar estrategia completa basada en:</action>
<action>- Framework(s) de testing definidos en arquitectura</action>
<action>- Patrones de testing del proyecto (AAA, Test Data Builder, etc.)</action>
<action>- Tipos de tests requeridos por capa</action>
<action>- Test Data Builders a reutilizar o crear</action>
<action>- Herramientas de performance testing según stack del proyecto</action>
<action>- Cobertura esperada según políticas del proyecto</action>
<action>- Convenciones de nombres y ubicación de archivos de test</action>

</substep>

</step>

<step n="4" goal="Validación Técnica con Desarrollador" optional="true">

<critical>NO ASUMIR soluciones técnicas cuando hay brechas entre requisitos y capacidades del proyecto</critical>
<critical>VALIDAR con el desarrollador cuando encuentres incertidumbre en decisiones técnicas</critical>

<action>Identificar situaciones que requieren validación con el desarrollador:</action>

<mandate>EJEMPLOS DE SITUACIONES QUE REQUIEREN VALIDACIÓN:</mandate>
<action>- **Migraciones de BD:** Historia requiere migraciones pero NO hay framework/proceso identificado (Flyway, Liquibase, migrations/, etc.)</action>
<action>- **Tests faltantes:** Se necesitan tipos de pruebas NO existentes en el proyecto (E2E, performance, etc.)</action>
<action>- **Dependencias nuevas:** Se requieren librerías sin estándar de versionado o política de aprobación clara</action>
<action>- **Patrones sin ejemplos:** Se debe implementar patrón arquitectónico sin ejemplos claros en el código</action>
<action>- **Integraciones externas:** Se requiere integrarse con servicio/API sin documentación o ejemplos previos</action>
<action>- **Breaking changes:** Cambios con impacto no cuantificable en consumidores existentes</action>
<action>- **Performance crítico:** Historia con requisitos de escalabilidad sin métricas baseline o SLAs definidos</action>

<check if="se identificó AL MENOS UNA situación de incertidumbre técnica">
<ask>⚠️ VALIDACIÓN TÉCNICA REQUERIDA

He identificado los siguientes puntos que requieren tu validación:

[Para cada situación encontrada, listar:
- **Problema detectado:** [descripción del problema con contexto]
- **Solución propuesta:** [solución técnica específica que sugiere el agente]
- **Justificación:** [por qué esta solución]
]

**¿Estás de acuerdo con las soluciones propuestas o prefieres realizar algún cambio?**

Puedes responder:
- "De acuerdo con todas" para continuar
- Indicar ajustes específicos: "Para [problema X], mejor usar [alternativa]"
- Proveer contexto adicional sobre estándares del proyecto

Tu respuesta:</ask>

<action>Incorporar todas las respuestas del desarrollador</action>
<action>Ajustar análisis técnico, estrategia de testing y tareas según feedback recibido</action>
<action>Documentar decisiones técnicas validadas en sección "Consideraciones Generales"</action>
</check>

</step>

<step n="5" goal="Enriquecimiento de la Historia Existente">

<action>Cambiar estado de la historia según corresponda:</action>
<check if="basado en análisis arquitectónico">
<action>Estado: "Refinado (Developer) - Basado en Análisis Arquitectónico"</action>
</check>
<check if="else">
<action>Estado: "Refinado (Developer) - Refinamiento Directo"</action>
</check>

<action>Añadir fecha de refinamiento: {date}</action>
<action>Mantener toda la información original del PO intacta</action>

<critical>MODO ENRIQUECIMIENTO: NO crear archivo nuevo - MODIFICAR la historia existente cargada en Step 1</critical>
<critical>El template template.md define la estructura completa de la sección "Refinamiento Técnico (Developer)"</critical>
<critical>Todas las variables deben coincidir EXACTAMENTE con los nombres en el template</critical>

<!-- Sección de Refinamiento Técnico (Developer) del template.md -->

<mandate>REGLAS DE SALIDA DEL TEMPLATE: (1) Remover comentarios HTML de secciones completadas, mantener para secciones vacías/opcionales. (2) Asegurar que TODOS los elementos del template (títulos, encabezados, etiquetas) coincidan con {document_output_language}, no solo el contenido.</mandate>

<mandate>Variables OBLIGATORIAS - Consideraciones Generales:</mandate>
<template-output>architectural_reference</template-output>
<template-output>complexity_level</template-output>
<template-output>complexity_justification</template-output>
<template-output>known_risks</template-output>
<template-output>code_patterns</template-output>
<template-output>new_dependencies</template-output>

<mandate>Variables OBLIGATORIAS - Estrategia de Testing (línea única compacta):</mandate>
<template-output>testing_frameworks</template-output>
<template-output>test_types_required</template-output>
<template-output>expected_coverage</template-output>
<template-output>test_data_builders</template-output>

<mandate>Variables CONDICIONALES - Performance Testing:</mandate>
<template-output>requires_performance_testing</template-output>
<template-output>performance_base_files</template-output>
<template-output>performance_scenarios</template-output>

<mandate>Variables OBLIGATORIAS - Historias Relacionadas:</mandate>
<template-output>related_stories</template-output>
<template-output>reused_patterns</template-output>
<template-output>best_practices</template-output>

<mandate>Variables CONDICIONALES - Fase 0 (Solo si aplica):</mandate>
<template-output>has_infrastructure_tasks</template-output>
<template-output>migrations_needed</template-output>
<template-output>migrations</template-output>
<template-output>config_changes</template-output>
<template-output>configs</template-output>
<template-output>breaking_changes</template-output>

<note>Si NO hay migraciones, configs o breaking changes, set has_infrastructure_tasks = false</note>

<critical>El template ya fue procesado automáticamente por el engine - ahora insertar en la historia</critical>

<action>Insertar sección "## Refinamiento Técnico (Developer)" generada desde template después del Análisis Arquitectónico (Arquitecto)</action>

</step>

<step n="6" goal="Descomposición en Tareas Técnicas Detalladas">

<critical>CADA SUBTAREA DEBE INCLUIR EL ARCHIVO ESPECÍFICO - Formato: "Acción + Archivo"</critical>

<action>Analizar criterios de aceptación de la historia</action>
<action>Tomar cada Hito del Arquitecto y descomponerlo en tareas concretas con archivos específicos</action>

<action>Determinar si se requiere Fase 0 (migraciones, configs, breaking changes, setup performance)</action>
<action>Crear Fases 1+ basadas en Hitos del Arquitecto (1 Hito = 1 Fase)</action>
<action>Dentro de cada Fase, agrupar tareas por componente desplegable (ejemplos: Frontend, Order Service, Payment Service, Admin Dashboard)</action>
<action>Cada Fase tiene componentes, cada componente tiene tareas, cada tarea tiene subtareas con archivos específicos</action>

<mandate>SIEMPRE agregar como ÚLTIMA FASE:</mandate>

<action>Crear Fase N (última): "QA y Deployment" con 3 componentes:</action>

<action>1. Componente "Code Quality" con tareas:</action>
<action>   - Tarea: "Ejecutar revisión par en Diagnosticador"</action>
<action>   - Tarea: "Resolver incidentes del Diagnosticador" (condicional)</action>
<action>     * Subtarea: Revisar reporte de incidentes</action>
<action>     * Subtarea: Aplicar correcciones necesarias</action>

<action>2. Componente "Deployment DEV" con tareas:</action>
<action>   - Tarea: "Crear Pull Request"</action>
<action>     * Subtarea: Crear PR hacia rama develop/dev</action>
<action>     * Subtarea: Asignar reviewers del equipo</action>
<action>   - Tarea: "Ejecutar pipeline deployment DEV"</action>
<action>     * Subtarea: Ejecutar pipeline de deployment</action>
<action>     * Subtarea: Verificar deployment exitoso</action>

<action>3. Componente "Testing Manual" con tareas:</action>
<action>   - Tarea: "Diseñar set de pruebas manuales"</action>
<action>     * Subtarea: Documentar casos de prueba por cada criterio de aceptación</action>
<action>     * Subtarea: Preparar datos de prueba necesarios</action>
<action>   - Tarea: "Ejecutar pruebas manuales"</action>
<action>     * Subtarea: Validar cada criterio de aceptación en el ambiente DEV</action>
<action>     * Subtarea: Reportar defectos encontrados si aplica</action>

<critical>Estas tareas son MANUALES y NO automatizables por IA - marcar para que en estimación NO se aplique descuento Método Ceiba</critical>
<note>El tiempo de estas tareas depende del tamaño y complejidad de la historia de usuario - ajustar nivel de detalle proporcionalmente</note>

<mandate>Variables OBLIGATORIAS:</mandate>
<template-output>phases</template-output>

<action>Insertar o reemplazar sección "## Tareas de Implementación (Developer)" en la historia existente</action>

</step>

<step n="7" goal="Finalización y Validación">

<action>Actualizar estado final de la historia</action>
<action>Añadir nota de que está lista para estimate-story</action>

<action>Verificar completitud del refinamiento:</action>
<action>✅ Información original del PO intacta</action>
<action>✅ Refinamiento técnico completo añadido</action>
<action>✅ Tareas de implementación detalladas</action>
<action>✅ Referencias a arquitectura y patrones existentes</action>

<critical>SOBRESCRIBIR el archivo original {dev_story_location}/{story_number}.story.md con el contenido enriquecido</critical>
<critical>NO crear archivo nuevo - el archivo debe mantener el mismo nombre y ubicación</critical>

<action>Guardar historia enriquecida sobrescribiendo el archivo original</action>

</step>

<step n="8" goal="Entrega y Comunicación">

<action>Mostrar resumen del refinamiento completado con:</action>
<action>- Archivo actualizado</action>
<action>- Número de fases definidas</action>
<action>- Estado actual</action>
<action>- Base del refinamiento (con/sin análisis arquitectónico)</action>
<action>- Documentación consultada</action>
<action>- Patrones aplicados</action>
<action>- Recomendaciones para desarrollo</action>
<action>- Próximos pasos</action>

<ask>¿El refinamiento cubre todas las consideraciones técnicas necesarias?</ask>

</step>

</workflow>
