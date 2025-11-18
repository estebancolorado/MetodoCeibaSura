# Estimar Historia de Usuario - Instructions

<critical>The workflow execution engine is governed by: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {installed_path}/workflow.yaml</critical>
<critical>ALWAYS communicate STRICTLY in {communication_language} regardless of the language used by the user</critical>
<critical>Generate all documents in {document_output_language}</critical>
<critical>This workflow estimates development time for refined user stories with defined implementation tasks. Enriches the story file with estimation section.</critical>

<workflow>

<step n="1" goal="Cargar y Validar Historia a Estimar">

<critical>PREREQUISITO: La historia DEBE estar refinada por el Developer con tareas de implementación definidas</critical>

<ask>¿Qué historia de usuario o incidente deseas estimar?

Proporciona el número de la historia (ejemplo: 5) o incidente (ejemplo: INC-123) o la ruta completa del archivo.</ask>

<action>Determinar tipo de documento basado en la entrada del usuario</action>
<action>Si incluye "INC-" o ".incident.md", buscar en {incident_location}</action>
<action>Si no, buscar archivo en {dev_story_location} usando el número o nombre proporcionado</action>
<action>Resolver path completo del archivo encontrado y almacenar en {{story_file_path}}</action>

<check if="archivo no existe">
<action>HALT con error: "Historia/Incidente no encontrado"</action>
</check>

<action>Cargar contenido COMPLETO de la historia</action>
<action>Verificar estado de la historia</action>

<check if="estado != 'Refinado (Developer)' AND estado != 'Refinado (Developer) - Basado en Análisis Arquitectónico'">
<action>Mostrar ADVERTENCIA:</action>
<ask>⚠️ HISTORIA NO REFINADA

La historia NO tiene estado "Refinado (Developer)".

Estado actual: {{estado_actual}}

PREREQUISITO OBLIGATORIO: La historia debe ser refinada por el Developer usando *refinamiento-tecnico antes de poder estimar.

¿Deseas:
1. PAUSAR - Ejecutar refinamiento-tecnico primero (RECOMENDADO)
2. CONTINUAR - Intentar estimar sin refinamiento (NO RECOMENDADO - resultados imprecisos)

Selecciona opción (1/2):</ask>

<check if="usuario elige opción 1">
<action>HALT con mensaje: "Por favor ejecuta *refinamiento-tecnico en el agente Developer primero"</action>
</check>
</check>

<action>Verificar presencia de sección "Tareas de Implementación"</action>

<check if="NO existe sección 'Tareas de Implementación' OR sección está vacía">
<action>HALT con error:</action>
<output>❌ FALTA DESCOMPOSICIÓN EN TAREAS

La historia no contiene la sección "Tareas de Implementación" o está vacía.

ACCIÓN REQUERIDA: Ejecuta *refinamiento-tecnico en el agente Developer para:
✅ Generar descomposición técnica en tareas
✅ Analizar contexto del código base
✅ Preparar la historia para estimación

No es posible estimar sin tareas definidas.</output>
</check>

<critical>La siguiente es la forma para extraer solo las tareas principales que son las que se van a estimar en los siguientes pasos</critical>
<mandate>Extraer SOLO tareas principales (formato: - [ ] **Tarea Principal**) - IGNORAR subtareas con sangría</mandate>
<critical>Patrón exacto de tarea principal: "- [ ] **Texto de la tarea**" (sin sangría, texto entre doble asterisco)</critical>
<critical>Las subtareas con sangría (  - [ ]) son CHECKLIST de implementación - NUNCA se extraen ni estiman</critical>
<action>Ejemplo: "- [ ] **Crear 5 tests de validación**" → ✅ ESTIMAR | "  - [ ] Test sábado" → ❌ IGNORAR</action>
<action>Contar número de tareas principales encontradas</action>

<output>✅ Historia validada:
- Archivo: {{story_file_path}}
- Estado: {{estado_actual}}
- Tareas encontradas: {{num_tareas}}</output>

</step>

<step n="2" goal="Extraer Contexto del Refinamiento">

<critical>NO re-analizar - LEER lo que ya documentó el workflow de refinamiento</critical>
<critical>El refinamiento ya determinó complejidad, precedentes y riesgos</critical>

<action>Buscar sección "## Refinamiento Técnico (Developer)" en la historia</action>

<check if="NO existe sección de refinamiento">
<action>HALT con error: "Falta sección de Refinamiento Técnico. La historia debe estar completamente refinada antes de estimar."</action>
</check>

<action>Extraer información YA DOCUMENTADA en "Consideraciones Generales":</action>
<action>- Nivel de complejidad documentado</action>
<action>- Justificación de complejidad</action>
<action>- Riesgos técnicos conocidos</action>
<action>- Patrones y convenciones del equipo</action>
<action>- Implementaciones similares analizadas (precedentes)</action>
<action>- Estrategia de testing definida</action>

<action>Contar estructura de tareas en "## Tareas de Implementación":</action>
<action>- Número de Fases (Fase 0, Fase 1, Fase 2...)</action>
<action>- Número de Componentes por Fase</action>
<action>- Número total de subtareas con archivos específicos</action>

</step>

<step n="3" goal="Estimar Cada Tarea por Perfil de Desarrollador">

<mandate>Estimar SOLO tareas principales extraídas en Step 1 - NO incluir subtareas</mandate>
<critical>Iterar ÚNICAMENTE sobre tareas principales (sin sangría) - Las subtareas NO están en el array</critical>
<critical>NO crear nuevas tareas ni modificar las existentes</critical>

<for-each item="tarea" in="tareas_implementacion">

<substep n="3.1" goal="Analizar Tarea Individual">

<critical>Esta tarea es una tarea PRINCIPAL - Las subtareas bajo ella son solo contexto, NO se estiman</critical>
<action>Extraer descripción completa de la tarea</action>
<action>Identificar tipo de tarea:</action>
<action>- Desarrollo de código (backend/frontend)</action>
<action>- Creación de tests (unitarios/integración/E2E)</action>
<action>- Documentación (código/usuario)</action>
<action>- Configuración (infraestructura/deployment)</action>
<action>- Refactorización</action>
<action>- Integración con servicios externos</action>

<action>Analizar dependencias de la tarea:</action>
<action>- ¿Depende de otras tareas?</action>
<action>- ¿Es tarea crítica en la ruta?</action>
<action>- ¿Requiere coordinación con otros equipos?</action>

</substep>

<substep n="3.2" goal="Calcular Estimación Base con PERT (Senior)">

<critical>Método PERT: (Optimista + 4×Probable + Pesimista) ÷ 6</critical>
<critical>Captura incertidumbre de la tarea usando 3 escenarios</critical>

<action>Para cada tarea, generar 3 estimaciones desde perspectiva de un Senior:</action>

<action>**Escenario Optimista (O):**</action>
<action>- Todo funciona a la primera, sin interrupciones</action>
<action>- Código base está limpio y bien documentado</action>
<action>- Usa contexto de Step 2: Si hay precedente similar → O es menor</action>

<action>**Escenario Más Probable (M):**</action>
<action>- Tiempo más realista considerando ciclo normal</action>
<action>- Incluye desarrollo + testing + correcciones típicas</action>
<action>- Usa nivel de complejidad extraído en Step 2</action>

<action>**Escenario Pesimista (P):**</action>
<action>- Se materializan riesgos conocidos del refinamiento</action>
<action>- Incluye refactorización o debugging complejo</action>
<action>- Usa riesgos documentados en Step 2</action>

<action>Calcular Estimación Base:</action>
<action>**Senior = (O + 4×M + P) ÷ 6**</action>

<action>Calcular Rango de Incertidumbre:</action>
<action>**Riesgo = P - O** (cuanto mayor, más incertidumbre)</action>

</substep>

<substep n="3.3" goal="Calcular Estimaciones por Seniority">

<critical>Aplicar multiplicadores según complejidad para calcular tiempo de cada perfil</critical>
<critical>Multiplicadores configurables en workflow.yaml: {estimation_factors.multipliers}</critical>

<action>Determinar multiplicadores usando complejidad extraída en Step 2:</action>

<check if="complejidad = BAJA">
<action>Ejemplos: CRUD simple, UI estático, cambios menores</action>
<action>- Junior = Senior × {estimation_factors.multipliers.BAJA.junior}</action>
<action>- Semi Senior = Senior × {estimation_factors.multipliers.BAJA.semi_senior}</action>
</check>

<check if="complejidad = MEDIA">
<action>Ejemplos: Lógica de negocio, APIs, integraciones estándar</action>
<action>- Junior = Senior × {estimation_factors.multipliers.MEDIA.junior}</action>
<action>- Semi Senior = Senior × {estimation_factors.multipliers.MEDIA.semi_senior}</action>
</check>

<check if="complejidad = ALTA">
<action>Ejemplos: Arquitectura, refactoring complejo, nuevos componentes (lambdas, workers), integraciones nunca desarrolladas (broker de mensajería, comunicación asíncrona), algoritmos complejos</action>
<action>- Junior = Senior × {estimation_factors.multipliers.ALTA.junior}</action>
<action>- Semi Senior = Senior × {estimation_factors.multipliers.ALTA.semi_senior}</action>
</check>

</substep>

<substep n="3.4" goal="Clasificar Tarea: Aumentada por IA vs Manual">

<action>Evaluar si la tarea puede ser aumentada/impactada por IA o requiere intervención 100% manual</action>
<action>Identificar tareas manuales sin beneficio de IA, como por ejemplo: Ejecución de pipelines y despliegues, Configuraciones manuales en servidores/infraestructura,  Coordinación con equipos externos (emails, reuniones), Aprobaciones de seguridad/compliance, Ejecución manual de scripts SQL en base de datos </action>
<check if="tarea es MANUAL (NO impactada por IA)">
    <action>Para tareas manuales:</action>
    <action>1. Añadir a array separado: **tareas_manuales**</action>
    <action>2. Incluir propiedades: numero, descripcion, tiempo_estimado</action>
    <action>3. Usar tiempo Senior como tiempo_estimado (sin variación por seniority)</action>
    <action>4. NO incluir en tabla principal de estimación</action>

    <action>SKIP del paso 3.5, solo se estiman tareas aumentadas por IA</action>
</check>

</substep>

<substep n="3.5" goal="Calcular Tiempo Método Ceiba (Solo Tareas Aumentadas por IA)">
    <critical>Este substep solo aplica para tareas aumentadas/impactadas por IA</critical>
    <critical>Método Ceiba discount = {estimation_factors.metodo_ceiba_discount} (60% de ahorro)</critical>
    <critical>Fórmula: Método Ceiba = Senior × (1 - discount)</critical>
    <action>Calcular Método Ceiba Senior:</action>
    <action>**Método Ceiba Senior = Senior × (1 - {estimation_factors.metodo_ceiba_discount})**</action>
    <action>Ejemplo: Senior = 10h → MC Senior = 10h × 0.40 = 4h</action>
    <action>Aplicar multiplicadores de seniority al resultado:</action>
    <action>**Método Ceiba Junior = MC Senior × multiplicador_complejidad**</action>
    <action>**Método Ceiba Semi Senior = MC Senior × multiplicador_complejidad**</action>
    <action>Ejemplo completo con complejidad MEDIA:</action>
    <action>- Senior tradicional: 10h</action>
    <action>- MC Senior: 10h × 0.40 = 4h</action>
    <action>- MC Semi Sr: 4h × 1.6 = 6.4h</action>
    <action>- MC Junior: 4h × 2.5 = 10h</action>
    <action>Añadir tarea a array: **tareas** (tabla principal)</action>
</substep>
</for-each>
</step>

<step n="4" goal="Consolidar Estimaciones y Calcular Totales">

<action>Crear tabla principal con tareas aumentadas por IA</action>
<action>Incluir columnas:</action>
<action>- #, Tarea, Complejidad</action>
<action>- Junior, Semi Sr, Senior (estimaciones tradicionales)</action>
<action>- MC Jr, MC Semi Sr, MC Sr (Método Ceiba por seniority)</action>

<action>Sumar totales por cada columna</action>
<action>Redondear a un decimal (ej: 12.5h)</action>

<action>Calcular porcentaje de optimización del Método Ceiba vs tradicional para cada perfil</action>

<check if="existen tareas_manuales">
<action>Crear array separado con tareas manuales</action>
<action>Para cada tarea manual incluir: numero, descripcion, tiempo_estimado</action>
<action>Calcular total_tareas_manuales (suma de todos los tiempos)</action>
<action>Calcular totales de desarrollo completo por rol:</action>
<action>- total_desarrollo_junior = total_mc_junior + total_tareas_manuales</action>
<action>- total_desarrollo_semi_sr = total_mc_semi_sr + total_tareas_manuales</action>
<action>- total_desarrollo_senior = total_mc_senior + total_tareas_manuales</action>
</check>

</step>

<step n="5" goal="Documentar Supuestos y Riesgos Residuales">

<critical>El riesgo por tarea ya está incluido en PERT (P - O)</critical>
<critical>Este paso documenta supuestos y riesgos externos</critical>

<action>Documentar supuestos de las estimaciones:</action>
<action>- Disponibilidad del equipo (vacaciones, rotación)</action>
<action>- Estabilidad de requisitos (cambios del PO/cliente)</action>
<action>- Ambiente de desarrollo funcional</action>
<action>- Acceso a recursos necesarios (APIs, datos)</action>

<action>Identificar riesgos NO incluidos en PERT:</action>
<action>- Dependencias externas fuera del control del equipo</action>
<action>- Aprobaciones legales o compliance</action>
<action>- Cambios organizacionales</action>

<action>Calcular incertidumbre total del proyecto:</action>
<action>- Sumar rangos de riesgo (P - O) de todas las tareas</action>
<action>- Identificar tareas con alta incertidumbre (riesgo > 3h)</action>

<output>⚠️ Tareas con Alta Incertidumbre:
{{#each tareas_alto_riesgo}}
- Tarea {{numero}}: Riesgo de {{riesgo}}h ({{descripcion_corta}})
{{/each}}

Recomendación: Considerar spikes técnicos o división de tareas.</output>

</step>

<step n="6" goal="Generar Sección de Estimación">

<critical>El template template.md define la estructura completa de la sección "Estimación"</critical>
<critical>Todas las variables deben coincidir EXACTAMENTE con los nombres en el template</critical>

<mandate>REGLAS DE SALIDA DEL TEMPLATE: (1) Remover comentarios HTML de secciones completadas, mantener para secciones vacías/opcionales. (2) Asegurar que TODOS los elementos del template (títulos, encabezados, etiquetas) coincidan con {document_output_language}, no solo el contenido.</mandate>

<mandate>Variables OBLIGATORIAS - Estimación por Tareas (array):</mandate>
<template-output>tareas</template-output>

<note>Cada elemento en 'tareas' debe incluir: numero, descripcion, complejidad, junior, semi_sr, senior, mc_junior, mc_semi_sr, mc_senior</note>

<mandate>Variables OPCIONALES - Tareas Manuales (array):</mandate>
<template-output>tareas_manuales</template-output>
<template-output>total_tareas_manuales</template-output>
<template-output>total_desarrollo_junior</template-output>
<template-output>total_desarrollo_semi_sr</template-output>
<template-output>total_desarrollo_senior</template-output>

<note>Cada elemento en 'tareas_manuales' debe incluir: numero, descripcion, tiempo_estimado. Los totales de desarrollo son la suma de MC + tareas manuales por cada rol. Solo incluir si existen tareas manuales.</note>

<mandate>Variables OBLIGATORIAS - Totales:</mandate>
<template-output>total_junior</template-output>
<template-output>total_semi_sr</template-output>
<template-output>total_senior</template-output>
<template-output>total_mc_junior</template-output>
<template-output>total_mc_semi_sr</template-output>
<template-output>total_mc_senior</template-output>

<mandate>Variables OBLIGATORIAS - Optimización (Método Ceiba vs Tradicional):</mandate>
<template-output>optimizacion_mc_junior</template-output>
<template-output>optimizacion_mc_semi_sr</template-output>
<template-output>optimizacion_mc_senior</template-output>

<mandate>Variables OPCIONALES:</mandate>
<template-output>notas_adicionales</template-output>

<critical>El template será procesado automáticamente por el engine con estas variables</critical>

</step>

<step n="7" goal="Integrar Estimación en Archivo de Historia">

<critical>Modificar archivo de historia para añadir sección de Estimación</critical>

<action>Cargar archivo completo usando el path resuelto: {{story_file_path}}</action>

<check if="ya existe sección '## Estimación'">
<ask>⚠️ La historia ya tiene una sección de Estimación.

¿Deseas:
1. SOBRESCRIBIR - Reemplazar estimación existente
2. AGREGAR - Añadir nueva estimación (con timestamp)
3. CANCELAR - No modificar el archivo

Selecciona opción (1/2/3):</ask>

<check if="usuario elige CANCELAR">
<action>HALT con mensaje: "Estimación no integrada. Generación completada."</action>
</check>
</check>

<action>Añadir sección "## Estimación" al final del archivo (antes de cualquier sección de notas/logs)</action>
<action>Insertar contenido generado en Step 6</action>
<action>Mantener TODO el contenido original intacto</action>

<action>Guardar archivo modificado</action>

<output>✅ ESTIMACIÓN INTEGRADA EXITOSAMENTE

Archivo: {dev_story_location}/{story_number}.story.md
Sección añadida: ## Estimación
Fecha: {date}
Estimador: {user_name}</output>

</step>

<step n="8" goal="Actualizar Estado de la Historia">

<action>Buscar línea que contiene "**Estado:**" en el archivo</action>
<action>Actualizar a: **Estado:** Estimado (Developer)</action>
<action>Guardar cambio</action>

<output>✅ Estado actualizado a: Estimado (Developer)</output>

</step>

<step n="9" goal="Validar Completitud de Estimación">

<invoke-task>Validate against checklist at {validation} using ceiba-metodo/core/tasks/validate-workflow.xml</invoke-task>

</step>

<step n="10" goal="Resumen y Próximos Pasos">

<output>
╔════════════════════════════════════════════════════════╗
║       ESTIMACIÓN COMPLETADA EXITOSAMENTE ✅            ║
╚════════════════════════════════════════════════════════╝

📊 RESUMEN:

Historia: #{story_number}
Complejidad: {{complejidad_nivel}}
Tareas: {{num_tareas}}

⏱️  TIEMPOS ESTIMADOS:
Tradicional - Senior: {{total_senior}}h
Método Ceiba - Senior: {{total_mc_senior}}h
Optimización: {{optimizacion_mc_senior}}%

📁 Actualizado: {dev_story_location}/{story_number}.story.md

🎯 PRÓXIMOS PASOS:
1. Revisar estimación con el equipo
2. Considerar factores de riesgo identificados  
3. Usar para planificación de sprint

</output>

<ask>¿Estás de acuerdo con la estimación generada o necesitas ajustes?

Opciones:
1. CONFORME - La estimación es correcta, finalizar
2. AJUSTAR - Hay algo que corregir o no estoy de acuerdo
3. OTRA - Estimar otra historia

Selecciona opción (1/2/3):</ask>

<check if="usuario elige AJUSTAR">
<ask>¿Qué necesitas ajustar? Describe los cambios:</ask>
<action>Realizar ajustes según feedback del usuario</action>
<action>Re-generar sección de estimación con ajustes</action>
<action>Actualizar archivo de historia con versión corregida</action>
<goto step="10">Volver a validación final</goto>
</check>

<check if="usuario elige OTRA">
<goto step="1">Iniciar nueva estimación</goto>
</check>

<output>✅ Estimación validada y finalizada. ¡Excelente trabajo! 💻✨</output>

</step>

</workflow>
