# Instructions - Diagnosticar Error

<workflow>
<critical>The workflow execution engine is governed by: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {installed_path}/workflow.yaml</critical>
<critical>ALWAYS communicate STRICTLY in {communication_language} regardless of the language used by the user</critical>
<critical>HTML COMMENTS HANDLING: El template contiene comentarios HTML como guías. Al generar el output final, REMOVER comentarios HTML de secciones completas y MANTENER comentarios de secciones vacías/incompletas</critical>
<critical>Generate all documents in {document_output_language}</critical>
<critical>NUNCA modificar archivos template - deben permanecer inmutables para reutilización</critical>


<mandate title="Reglas de Comportamiento OBLIGATORIAS">
1. DETECCIÓN ANTI-ASUNCIÓN: Si piensas "seguramente se refiere a...", "es lógico que...", "probablemente necesita..." → OBLIGATORIO preguntar específicamente
2. NO ASUMIR INFORMACIÓN CRÍTICA: NUNCA asumir tipos de usuario, permisos, formatos de datos, comportamientos de error, ubicación en interfaz, o integraciones
3. PREGUNTAS INTELIGENTES: Solo preguntar sobre gaps reales identificados en el análisis de completitud, aplicando filtro por perfil técnico
4. HISTORIAS EXISTENTES: ANALIZAR SIEMPRE - OBLIGATORIAMENTE revisar historias existentes para identificar patrones, dependencias y lecciones aprendidas
5. CONTEXTO vs DECISIÓN TÉCNICA: SÍ documentar contexto de negocio mencionado por stakeholder (módulo afectado, pantalla, integración con sistema X). NO tomar decisiones técnicas sobre endpoints, componentes de código, cambios backend/frontend, o arquitectura interna - eso es trabajo del ARQUITECTO
6. PRESERVAR INTENCIÓN EN MODO IMPORTAR: Si modo == 2, preservar la intención original de la historia importada mientras se completa información faltante y se mejora claridad
</mandate>
<step n="1" goal="Cargar contexto del incidente">
  <ask>Proporciona el path completo al archivo de incidente a diagnosticar:
  (ej: docs/incidents/042.incident.md)</ask>
  
  <action>Lee el archivo de incidente completo</action>
  
  <action>Extrae la información clave:
  - Número de incidente
  - Severidad
  - Tipo de error reportado
  - Descripción del error
  - Pasos de reproducción
  - Evidencias técnicas
  - Valores que causan error
  - Componentes afectados
  </action>
  
  <action>Valida que el archivo existe y tiene la sección 1 (Recepción) completa.
  Si está incompleta, notifica al usuario que debe completar la recepcion del error</action>
  
  <template-output>incident_file_path, incident_number, severity, error_summary</template-output>
</step>

<step n="2" goal="Validar si es Error Real o Falso Positivo">
  <action>Analiza cuidadosamente el reporte del incidente para determinar si es un error real o falso positivo.
  
  Indicadores de FALSO POSITIVO:
  - Error no reproducible en ambiente actual (puede haber sido corregido ya)
  - "Error" es comportamiento esperado mal entendido por el usuario
  - Problema ya fue resuelto en deployment posterior
  - Reporte duplicado de incidente ya cerrado
  - Configuración incorrecta en ambiente de pruebas (no en producción)
  - Expectativa incorrecta del usuario sobre funcionalidad
  - Logs/alertas con threshold muy sensible generando falsa alarma
  - Comportamiento es correcto según especificación actual
  
  Indicadores de ERROR REAL:
  - Reproducible de manera consistente en ambiente especificado
  - Comportamiento diverge de especificación o documentación
  - Afecta a usuarios reales en producción
  - Impacto en funcionalidad crítica documentada
  - Stack trace o logs muestran error evidente
  - Datos se corrompen o pierden
  - Seguridad comprometida
  </action>
  
  <action>Presenta tu análisis al usuario explicando los indicadores encontrados</action>
  
  <ask>Basado en el análisis, ¿es un Error Real o Falso Positivo?
  
  Responde:
  - "real" si es un error que requiere corrección
  - "falso" si no es un error real
  </ask>
  
  <check if="respuesta es falso">
    <ask>¿Cuál es la razón específica por la que este es un Falso Positivo?
    
    Opciones:
    1. No reproducible / Ya corregido
    2. Comportamiento esperado mal entendido
    3. Configuración incorrecta de ambiente de pruebas
    4. Expectativa incorrecta del usuario
    5. Reporte duplicado
    6. Falsa alarma de monitoreo
    7. Otra (especificar)
    </ask>
    
    <action>Actualiza la sección 2 del archivo incident.md:
    
    ## 2. Diagnóstico (Architect)
    
    **Validación de Error Real**: FALSO POSITIVO
    
    **Razón del Falso Positivo**:
    {{false_positive_reason}}
    
    **Análisis**:
    {{análisis_detallado_de_por_qué_no_es_error_real}}
    
    **Acciones Sugeridas**:
    {{acciones_de_cierre_sugeridas}}
    - Actualizar documentación si aplica
    - Ajustar alertas/monitoreo si es falsa alarma
    - Capacitar usuario si es malentendido de funcionalidad
    
    **Status**: Cerrado como Falso Positivo
    **Fecha de Cierre**: {{date}}
    </action>
    
    <action>Notifica al usuario:
    
    "❌ Incidente {{incident_number}} cerrado como FALSO POSITIVO.
    
    Razón: {{false_positive_reason}}
    
    No se requieren pasos adicionales de diagnóstico, refinamiento o implementación.
    
    Archivo actualizado: {{incident_file_path}}
    
    Se recomienda: {{acciones_sugeridas}}"
    </action>
    
    <action>DETENER workflow - Incidente cerrado como Falso Positivo. No continuar a siguientes pasos.</action>
  </check>
  
  <check if="respuesta es real">
    <action>Actualiza la sección 2 del archivo incident.md:
    
    **Validación de Error Real**: ERROR REAL
    
    **Justificación**:
    {{justificación_de_por_qué_es_error_real}}
    </action>
    
    <action>Notifica: "✅ Error confirmado como REAL. Continuando con análisis de causa raíz..."</action>
    <action>Proceder al siguiente step para análisis 5 Whys</action>
  </check>
  
  <template-output>is_real_error, false_positive_reason</template-output>
</step>

<step n="3" goal="Aplicar metodología 5 Whys para análisis de causa raíz" if="is_real_error == true">
  <action>Explica al usuario la metodología 5 Whys:
  
  "Voy a aplicar la metodología 5 Whys para identificar la causa raíz del error.
  Preguntaré '¿Por qué?' sucesivamente 5 veces para profundizar desde el síntoma hasta la causa fundamental.
  
  Esto nos ayudará a encontrar la causa raíz ACTIONABLE (que podemos corregir)."
  </action>
  
  <action>Inicia con el síntoma (el error observable):
  
  **Síntoma**: {{error_summary}}
  </action>
  
  <action>Pregunta 1 - ¿Por qué ocurre este error?
  
  Analiza las evidencias técnicas, logs, stack traces para identificar la causa inmediata.
  
  Ejemplo:
  - Si es error 500: ¿Por qué el servidor retorna 500?
  - Si es error de UI: ¿Por qué el componente no renderiza?
  </action>
  
  <ask>¿Por qué ocurre {{error_summary}}?
  
  (Proporciona la causa inmediata basándote en evidencias técnicas)</ask>
  
  <template-output>why_1</template-output>
  
  <action>Pregunta 2 - ¿Por qué ocurre {{why_1}}?
  
  Profundiza un nivel más. Busca la razón subyacente.</action>
  
  <ask>¿Por qué ocurre: {{why_1}}?</ask>
  
  <template-output>why_2</template-output>
  
  <action>Pregunta 3 - ¿Por qué ocurre {{why_2}}?
  
  Continúa profundizando hacia la causa raíz.</action>
  
  <ask>¿Por qué ocurre: {{why_2}}?</ask>
  
  <template-output>why_3</template-output>
  
  <action>Pregunta 4 - ¿Por qué ocurre {{why_3}}?
  
  Busca causas de proceso, diseño o arquitectura.</action>
  
  <ask>¿Por qué ocurre: {{why_3}}?</ask>
  
  <template-output>why_4</template-output>
  
  <action>Pregunta 5 - ¿Por qué ocurre {{why_4}}?
  
  Identifica la causa raíz fundamental (usualmente proceso, decisión de diseño, o gap en práctica).</action>
  
  <ask>¿Por qué ocurre: {{why_4}}?
  
  Esta debe ser la CAUSA RAÍZ fundamental.</ask>
  
  <template-output>why_5, root_cause</template-output>
  
  <action>Valida que la causa raíz identificada es ACTIONABLE:
  - ¿Podemos corregirla con código, configuración, datos, o proceso?
  - ¿Es específica y no vaga? (ej: "falta validación de campo X" en vez de "falta testing")
  
  Si no es actionable, replantea el análisis 5 Whys profundizando más.
  </action>
  
  <action>Genera el análisis de causa raíz estructurado:
  
  **Root Cause Analysis (5 Whys)**:
  
  1. ¿Por qué {{síntoma}}?
     → {{why_1}}
  
  2. ¿Por qué {{why_1}}?
     → {{why_2}}
  
  3. ¿Por qué {{why_2}}?
     → {{why_3}}
  
  4. ¿Por qué {{why_3}}?
     → {{why_4}}
  
  5. ¿Por qué {{why_4}}?
     → {{why_5}}
  
  **CAUSA RAÍZ**: {{root_cause}}
  </action>
  
  <template-output>root_cause_analysis</template-output>
</step>

<step n="4" goal="Clasificar tipo de error y definir estrategia">
  <action>Basándote en la causa raíz y el análisis técnico, clasifica el tipo de error:
  
  1. **Error de CÓDIGO**: Bug en lógica, implementación incorrecta, problema arquitectónico
     - Ejemplos: NullPointerException, lógica de negocio incorrecta, algoritmo con bugs
     - Estrategia: Fix en código + tests + deploy
  
  2. **Error de DATOS**: Datos corruptos, inconsistentes, faltantes, migración fallida
     - Ejemplos: Datos nulos en campos obligatorios, duplicados, inconsistencias entre tablas
     - Estrategia: Script de corrección + validación de datos (sin deploy de código)
  
  3. **Error de INFRAESTRUCTURA**: Problemas de configuración, recursos, networking, servicios externos
     - Ejemplos: Base de datos caída, disco lleno, timeout de conexión, DNS fallando
     - Estrategia: Corrección de infraestructura + monitoreo + alertas
  
  4. **Error de SEGURIDAD**: Vulnerabilidades, accesos no autorizados, brechas
     - Ejemplos: SQL injection, XSS, secretos expuestos, CORS mal configurado
     - Estrategia: Patch de seguridad urgente + auditoría + medidas preventivas
  
  5. **Error de CONFIGURACIÓN**: Configuración incorrecta en ambiente, variables, feature flags
     - Ejemplos: Variable de entorno incorrecta, feature flag mal configurado, timeout muy bajo
     - Estrategia: Actualización de configuración + validación + documentación
  
  6. **Error de RENDIMIENTO**: Degradación de performance, queries lentas, memory leaks
     - Ejemplos: Query N+1, falta de índices, carga de datos masivos sin paginación
     - Estrategia: Optimización + profiling + métricas de monitoreo
  
  7. **Error de INTEGRACIÓN**: Problemas con APIs externas, servicios de terceros
     - Ejemplos: API externa caída, cambio de contrato no comunicado, timeout en webhooks
     - Estrategia: Fix de integración + manejo de errores + circuit breaker
  
  8. **Error MIXTO**: Combinación de dos o más categorías
     - Ejemplos: Código no valida datos corruptos (Código + Datos)
     - Estrategia: Plan combinado según categorías involucradas
  </action>
  
  <ask>¿Qué tipo de error es según la clasificación?
  
  Opciones:
  1. Código
  2. Datos
  3. Infraestructura
  4. Seguridad
  5. Configuración
  6. Rendimiento
  7. Integración
  8. Mixto (especificar cuáles)
  
  Selecciona número:</ask>
  
  <action>Genera la estrategia de resolución apropiada según el tipo de error identificado</action>
  
  <template-output>error_classification, resolution_strategy</template-output>
</step>

<step n="4.1" goal="Analizar y proponer recategorización basada en diagnóstico">
  <action>Ahora que has completado el diagnóstico profundo, analiza si la severidad y tipo de error originalmente reportados son apropiados:
  
  **Información Original del Incidente**:
  - Severidad original: {{original_severity}}
  - Tipo de error original: {{original_error_type}}
  
  **Nueva Información del Diagnóstico**:
  - Causa raíz identificada: {{root_cause}}
  - Clasificación técnica: {{error_classification}}
  - Componentes afectados: {{affected_components_detailed}}
  - Escenarios de fallo: {{failure_scenarios_summary}}
  
  **Análisis de Severidad**:
  Evalúa si la severidad debe cambiar considerando:
  
  Criterios para AUMENTAR severidad:
  - El diagnóstico reveló que afecta a más usuarios de lo reportado → P1 o P0
  - La causa raíz muestra que el impacto es mayor (ej: pérdida de datos, brecha de seguridad) → P0
  - El error afecta múltiples componentes críticos → P1
  - Sin workaround viable identificado en el diagnóstico → elevar un nivel
  - Riesgo de corrupción de datos o propagación del error → P0 o P1
  - Error de seguridad confirmado → P0
  
  Criterios para DISMINUIR severidad:
  - El diagnóstico reveló que el impacto es menor de lo reportado → bajar un nivel
  - Se identificó workaround viable durante el análisis → bajar un nivel
  - Solo afecta escenarios muy específicos o edge cases → P3 o P4
  - Falso positivo parcial (error real pero impacto mínimo) → P4
  
  **Análisis de Tipo de Error**:
  Compara el tipo original con la clasificación técnica del diagnóstico:
  
  Recategorizar si:
  - Tipo original: "Bug" → Diagnóstico reveló: "Performance", "Datos", "Configuración", etc.
  - Tipo original: "Performance" → Diagnóstico identificó bug de código o error de integración
  - Tipo original reportado no coincide con la clasificación técnica del paso 4
  - El diagnóstico reveló que es "Security" (siempre recategorizar a Security si se confirma)
  </action>
  
  <action>Determina si hay necesidad de recategorización:
  
  - Si severidad original Y tipo original son apropiados según diagnóstico → NO proponer cambios
  - Si detectas discrepancia significativa → PROPONER recategorización
  </action>
  
  <check if="se detecta necesidad de recategorización">
    <action>Presenta tu análisis al usuario de forma clara y justificada:
    
    "🔍 **Propuesta de Recategorización Post-Diagnóstico**
    
    Basándome en el diagnóstico completo realizado, identifico que la categorización inicial no refleja adecuadamente la naturaleza y el impacto real del error:
    
    **Categorización Original** (reportada por usuario):
    - Severidad: {{original_severity}}
    - Tipo de Error: {{original_error_type}}
    
    **Categorización Propuesta** (basada en diagnóstico):
    - Severidad: {{proposed_severity}}
    - Tipo de Error: {{proposed_error_type}}
    
    **Justificación Técnica**:
    {{justificación_detallada_basada_en_diagnóstico}}
    
    **Evidencias del Diagnóstico**:
    - Causa Raíz: {{root_cause}}
    - Clasificación Técnica: {{error_classification}}
    - Impacto Identificado: {{impact_analysis}}
    - Componentes Afectados: {{components_count}} componentes
    
    **Cambios Específicos**:
    {{detallar_cada_cambio_propuesto_con_razón}}"
    </action>
    
    <ask>¿Apruebas la recategorización propuesta basada en el diagnóstico?
    
    Opciones:
    1. Sí, aprobar recategorización completa
    2. No, mantener categorización original
    3. Modificar parcialmente (especificar)
    
    Selecciona opción (1/2/3):</ask>
    
    <check if="respuesta es 1 (aprobar)">
      <action>Actualiza la información del incidente:
      
      - severity actualizada: {{proposed_severity}}
      - error_type actualizado: {{proposed_error_type}}
      </action>
      
      <action>Documenta el cambio en la sección 2 del incident.md:
      
      **Recategorización Aplicada Post-Diagnóstico**:
      
      | Categoría | Original | Nueva | Razón del Cambio |
      |-----------|----------|-------|------------------|
      | Severidad | {{original_severity}} | {{proposed_severity}} | {{razón_cambio_severidad}} |
      | Tipo de Error | {{original_error_type}} | {{proposed_error_type}} | {{razón_cambio_tipo}} |
      
      - **Aprobado por**: {{user_name}}
      - **Fecha de recategorización**: {{date}}
      - **Basado en**: Análisis de causa raíz y diagnóstico técnico completo
      </action>
      
      <action>Notifica: "✅ Recategorización aplicada. El incidente ahora refleja correctamente su severidad e impacto real."</action>
      
      <template-output>recategorization_applied, new_severity, new_error_type, recategorization_justification</template-output>
    </check>
    
    <check if="respuesta es 2 (mantener original)">
      <action>Documenta la decisión en la sección 2 del incident.md:
      
      **Recategorización Propuesta pero NO Aplicada**:
      
      - Se propuso cambiar severidad de {{original_severity}} a {{proposed_severity}}
      - Se propuso cambiar tipo de {{original_error_type}} a {{proposed_error_type}}
      - **Razón de la propuesta**: {{justificación_técnica_resumida}}
      - **Decisión del usuario**: Mantener categorización original
      - **Justificación del usuario**: {{razón_del_usuario_opcional}}
      - **Fecha**: {{date}}
      
      **Nota**: La categorización original se mantiene a petición del usuario a pesar de la propuesta técnica.
      </action>
      
      <action>Notifica: "📝 Se mantendrá la categorización original. El diagnóstico continúa con {{original_severity}} - {{original_error_type}}."</action>
      
      <template-output>recategorization_rejected, maintained_original_categorization</template-output>
    </check>
    
    <check if="respuesta es 3 (modificar parcialmente)">
      <ask>¿Qué aspecto deseas modificar?
      
      1. Solo cambiar severidad a: {{proposed_severity}} (mantener tipo original)
      2. Solo cambiar tipo a: {{proposed_error_type}} (mantener severidad original)
      3. Cambiar severidad a valor personalizado (especificar: P0/P1/P2/P3/P4)
      4. Cambiar tipo a valor personalizado (especificar tipo)
      
      Selecciona opción:</ask>
      
      <action>Aplica los cambios parciales según la selección del usuario</action>
      
      <action>Documenta el cambio parcial en la sección 2 del incident.md:
      
      **Recategorización Parcial Aplicada**:
      
      {{documentar_qué_se_cambió_y_qué_se_mantuvo}}
      
      - **Aprobado por**: {{user_name}}
      - **Fecha**: {{date}}
      </action>
      
      <template-output>partial_recategorization_applied, updated_values</template-output>
    </check>
  </check>
  
  <check if="NO se detecta necesidad de recategorización">
    <action>Documenta la validación en la sección 2 del incident.md:
    
    **Validación de Categorización**:
    ✅ La categorización original ({{original_severity}} - {{original_error_type}}) es correcta y apropiada según el diagnóstico realizado.
    
    - Severidad {{original_severity}} validada: {{justificación_breve}}
    - Tipo {{original_error_type}} validado: {{justificación_breve}}
    - No se requieren cambios de categorización
    </action>
    
    <action>Notifica brevemente: "✅ Categorización validada: {{original_severity}} - {{original_error_type}} es apropiada."</action>
    
    <template-output>categorization_validated_in_diagnosis</template-output>
  </check>
</step>

<step n="5" goal="Documentar componentes afectados en detalle">
  <action>Basándote en el análisis de causa raíz, identifica TODOS los componentes técnicos afectados en detalle:
  
  - Servicios backend afectados
  - Componentes de frontend impactados
  - Capas de la arquitectura involucradas (presentación, lógica, datos)
  - Bases de datos o tablas específicas
  - APIs o endpoints afectados
  - Servicios de terceros involucrados
  - Archivos de código específicos a modificar (si error de código)
  - Datasets a corregir (si error de datos)
  - Variables de configuración a ajustar (si error de configuración)
  </action>
  
  <template-output>affected_components_detailed</template-output>
</step>

<step n="6" goal="Documentar escenarios de fallo para testing">
  <action>Define escenarios de fallo específicos que servirán para generar casos de prueba:
  
  **1. Escenario de Fallo Principal** (el que causó el incidente):
  
  - **Precondiciones**: Estado inicial del sistema
  - **Datos de entrada**: Valores específicos que causan el error
  - **Acción**: Operación que desencadena el fallo
  - **Resultado esperado**: Comportamiento correcto
  - **Resultado actual**: Error observado
  
  **2. Escenarios de Fallo Relacionados** (casos edge detectados):
  
  - Variaciones del escenario principal
  - Casos límite que también podrían fallar
  - Combinaciones de datos que agravan el problema
  
  **3. Escenarios de No-Regresión** (validar que no se rompa lo existente):
  
  - Casos que SÍ deben seguir funcionando
  - Flujos alternativos que no deben afectarse
  
  Usa los "valores que causan error" de la sección 1 para definir datos de entrada específicos.
  </action>
  
  <template-output>failure_scenario_main, failure_scenarios_related, failure_scenarios_no_regression</template-output>
</step>

<step n="7" goal="Evaluar impacto arquitectónico y necesidad de análisis profundo">
  <action>Evalúa el impacto arquitectónico del fix basándote en:
  
  - Número de componentes afectados
  - Complejidad de la solución requerida
  - Necesidad de cambios de diseño
  - Impacto en otros sistemas o flujos
  
  Clasifica como:
  - **Alto**: 3+ componentes, cambios arquitectónicos, patrón recurrente
  - **Medio**: 2 componentes, cambios de diseño menores
  - **Bajo**: 1 componente, fix directo sin cambios de arquitectura
  </action>
  
  <action>Determina si requiere análisis arquitectónico profundo:
  
  SÍ requiere si:
  - Impacto arquitectónico Alto
  - El fix requiere cambios arquitectónicos significativos
  - Impacto en múltiples componentes o capas
  - Error tipo: Código complejo, Mixto, Seguridad crítica
  - Patrón de error recurrente que requiere solución arquitectónica
  - Decisiones de diseño deben validarse
  
  NO requiere si:
  - Impacto Bajo o Medio simple
  - Fix simple y directo (typo, validación faltante)
  - Error de Datos (solo requiere script)
  - Error de Configuración (solo cambio de variable)
  - Componente único afectado sin impacto downstream
  - Solución clara desde el diagnóstico
  </action>
  
  <ask>¿Requiere análisis arquitectónico profundo? (sí/no)
  
  Justifica tu decisión basándote en los criterios anteriores.</ask>
  
  <template-output>architectural_impact, requires_architecture_analysis</template-output>
</step>

<step n="8" goal="Generar contenido de diagnóstico desde template">
  <action>Lee el template de diagnóstico: {template_diagnostico}</action>
  
  <action>Procesa el template reemplazando las variables con los valores generados en los steps anteriores:
  
  Variables a reemplazar:
  - {{is_real_error}}: "ERROR REAL" o "FALSO POSITIVO"
  - {{is_false_positive}}: true/false según validación del step 2
  - {{false_positive_reason}}: razón si es falso positivo
  - {{root_cause_analysis}}: análisis 5 Whys completo del step 3
  - {{error_classification}}: clasificación del tipo de error del step 4
  - {{affected_components_detailed}}: componentes detallados del step 5
  - {{failure_scenario_main}}: escenario principal del step 6
  - {{failure_scenarios_related}}: escenarios relacionados del step 6
  - {{failure_scenarios_no_regression}}: escenarios de no-regresión del step 6
  - {{architectural_impact}}: evaluación del impacto del step 7
  - {{requires_architecture_analysis}}: "Sí" o "No" del step 7
  - {{resolution_strategy}}: estrategia de resolución del step 4
  - {{categorization_validated_in_diagnosis}}: resultado del step 4.1
  </action>
  
  <action>Genera el contenido final procesando las condicionales del template:
  - Si is_false_positive es true, mostrar solo sección de falso positivo
  - Si is_false_positive es false, mostrar análisis completo
  - Si recategorization_applied es true, incluir sección de recategorización
  </action>
  
  <template-output>diagnostico_content</template-output>
</step>

<step n="9" goal="Actualizar documento de incidente con diagnóstico completo">
  <action>Lee el archivo de incidente actual: {{incident_file_path}}</action>
  
  <action>Busca el marcador donde debe insertarse la sección 2 (Diagnóstico).
  El marcador es: "<!-- Sección 2: Diagnóstico - Agregada por workflow diagnosticar-error -->"
  </action>
  
  <action>Inserta el contenido generado {{diagnostico_content}} en el lugar apropiado:
  
  - Si ya existe una sección 2 (Diagnóstico), reemplázala completamente
  - Si no existe, insértala después de la sección 1 y antes de los comentarios de placeholder
  </action>
  
  <action>Guarda el archivo actualizado</action>
  
  <check if="requires_architecture_analysis == 'sí'">
    <action>Marca sección 3 del incident.md como "Pendiente"</action>
    
    <action>Notifica al usuario:
    
    "✅ Diagnóstico completado para incidente {{incident_number}}.
    
    🔍 Causa Raíz: {{root_cause}}
    📦 Tipo de Error: {{error_classification}}
    🎯 Impacto Arquitectónico: {{architectural_impact}}
    
    🔜 Próximo paso: ANÁLISIS ARQUITECTÓNICO REQUERIDO
    
    Este incidente requiere análisis arquitectónico profundo debido a:
    {{justificación}}
    
    Por favor invoca el comando: *analisis-y-diseno
    con el incidente: {{incident_file_path}}"
    </action>
  </check>
  
  <check if="requires_architecture_analysis == 'no'">
    <action>Marca sección 3 del incident.md como "No Aplica"</action>
    
    <action>Notifica al usuario:
    
    "✅ Diagnóstico completado para incidente {{incident_number}}.
    
    🔍 Causa Raíz: {{root_cause}}
    📦 Tipo de Error: {{error_classification}}
    🎯 Impacto Arquitectónico: {{architectural_impact}}
    
    🔜 Próximo paso: REFINAMIENTO TÉCNICO
    
    El análisis arquitectónico NO es necesario. La solución es clara.
    
    Por favor invoca al Developer con comando: *refinamiento-tecnico
    con el incidente: {{incident_file_path}}"
    </action>
  </check>
  
  <template-output>incident_file_path_updated</template-output>
</step>

</workflow>
