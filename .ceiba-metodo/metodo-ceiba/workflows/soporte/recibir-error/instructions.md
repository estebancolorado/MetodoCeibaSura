# Instructions - Recibir Error

<workflow>
  <critical>The workflow execution engine is governed by: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
  <critical>You MUST have already loaded and processed: {installed_path}/workflow.yaml</critical>
  <critical>ALWAYS communicate STRICTLY in {communication_language} regardless of the language used by the user</critical>
  <critical>HTML COMMENTS HANDLING: El template contiene comentarios HTML como guías. Al generar el output final, REMOVER comentarios HTML de secciones completas y MANTENER comentarios de secciones vacías/incompletas</critical>
  <critical>Generate all documents in {document_output_language}</critical>
  <critical>NUNCA modificar archivos template - deben permanecer inmutables para reutilización</critical>
<step n="1" goal="Capturar información básica del error">
  <action>Saluda al usuario y explica el propósito:
  
  "Voy a ayudarte a documentar este error/incidente de manera completa para facilitar su diagnóstico y resolución. 
  Te haré algunas preguntas para capturar toda la información necesaria."
  </action>

  <ask>Número de incidente (consecutivo o ID único):</ask>

  <ask>Entorno donde ocurre el error:
  1. Production
  2. Staging
  3. Development
  
  Selecciona número (1-3):</ask>
  
  <ask>Severidad del incidente:
  P0 - Crítico: Sistema caído, pérdida de datos, brecha de seguridad
  P1 - Alto: Funcionalidad crítica bloqueada, múltiples usuarios afectados
  P2 - Medio: Funcionalidad no crítica, workaround disponible
  P3 - Bajo: Issue cosmético, impacto mínimo en usuarios
  P4 - Trivial: Mejora menor, sin impacto funcional
  
  Selecciona (P0/P1/P2/P3/P4):</ask>
  
  <ask>Tipo de error:
  1. Bug - Comportamiento incorrecto en funcionalidad existente
  2. Regression - Funcionalidad que antes funcionaba correctamente
  3. Performance - Degradación de rendimiento o lentitud
  4. Security - Vulnerabilidad de seguridad detectada
  5. Data Loss - Pérdida o corrupción de datos
  
  Selecciona número (1-5):</ask>
  
  <action>Guarda los valores originales de categorización para trazabilidad de posibles cambios futuros:
  
  - original_severity = {{severity}}
  - original_error_type = {{error_type}}
  
  Estos valores se preservarán para comparación en caso de que se proponga una recategorización posteriormente.
  </action>
  
  <template-output>incident_number, environment, severity, error_type, original_severity, original_error_type</template-output>
</step>

<step n="2" goal="Explorar descripción del error y su impacto">
  <action>Facilita la exploración del error mediante preguntas abiertas adaptadas al perfil del usuario:

  Ayuda al usuario a articular claramente:
  - ¿Qué está fallando exactamente?
  - ¿Cuál es el comportamiento esperado?
  - ¿Qué comportamiento incorrecto está ocurriendo?
  - ¿Desde cuándo ocurre este error?
  - ¿El error es consistente (siempre ocurre) o intermitente (a veces sí, a veces no)?
  - ¿Cuántos usuarios están afectados aproximadamente?
  - ¿Qué funcionalidad está bloqueada o afectada?
  - ¿Hay algún workaround temporal disponible?

  Adapta tu profundidad según las respuestas:
  - Si el usuario es técnico (menciona stack traces, logs, APIs), profundiza en detalles de implementación
  - Si es funcional (describe comportamiento de usuario), enfócate en el flujo observable y UX
  
  Busca identificar patrones clave:
  - Condiciones específicas que desencadenan el error
  - Flujos de usuario que están rotos
  - Impacto en funcionalidad crítica del negocio
  - Datos de transacciones o pérdida de información
  
  Solicita pasos de reproducción detallados:
  - Paso 1: ¿Qué haces primero?
  - Paso 2: ¿Qué acción sigues?
  - Paso 3: ¿En qué momento aparece el error?
  - Resultado: ¿Qué ves cuando ocurre?
  </action>
  
  <ask>Describe el comportamiento ESPERADO (lo que debería pasar):</ask>
  
  <ask>Describe el comportamiento ACTUAL que observas (lo que está pasando incorrectamente):</ask>
  
  <action>Genera una descripción concisa del error sintetizando toda la información recopilada.
  
  Genera también una cuantificación del impacto:
  - Número de usuarios afectados (si se conoce)
  - Funcionalidad bloqueada o degradada
  - Pérdida de datos o transacciones (si aplica)
  - Workaround disponible (sí/no)
  </action>
  
  <template-output>error_description, expected_behavior, actual_behavior, reproduction_steps, impact</template-output>
</step>

<step n="3" goal="Recopilar evidencias técnicas">
  <action>Solicita evidencias técnicas apropiadas al tipo de error y perfil del usuario:

  Para errores de UI/Frontend:
  - Screenshots mostrando el error exacto (mensaje, pantalla, estado)
  - Video corto de reproducción (15-30 segundos máximo)
  - Errores de consola del navegador (F12 → Console → screenshot o copy text)
  - Network tab si es fallo de API (F12 → Network → filtrar failed requests)
  - Browser y versión (Chrome 118, Firefox 119, Safari 16, etc.)
  - Device si es móvil (iPhone 14, Android Samsung Galaxy, etc.)
  
  Para errores de Backend/API:
  - Logs del servidor (últimas 50-100 líneas relevantes al error)
  - Stack trace completo del error
  - Request HTTP que falla (método, URL, headers, body)
  - Response HTTP recibida (status code, headers, body)
  - Versión de deployment o release
  - Métricas de monitoreo si disponibles (Sentry, New Relic, CloudWatch, etc.)
  
  Para errores de Datos:
  - Valores específicos que causan error (IDs, valores de campos)
  - Screenshots de registros corruptos o inconsistentes
  - Queries o requests que fallan con esos datos
  - Ejemplos de datos que SÍ funcionan (para comparar)
  - Estado de la base de datos antes/después del error
  
  Adapta según el contexto del paso 2:
  - Si es usuario no técnico, guíalo paso a paso: "Presiona F12, luego haz click en Console, toma un screenshot"
  - Si es usuario técnico, pide directamente: "Adjunta stack trace y request/response HTTP"
  
  No solicites evidencias irrelevantes al tipo de error.
  Si el usuario no tiene cierta evidencia, marca como "No disponible" y continúa.
  </action>
  
  <action>Organiza las evidencias recopiladas en una sección estructurada con formato:
  
  **Screenshots**:
  - Descripción breve de cada screenshot
  - Path/ubicación del archivo adjunto (o nota "Adjuntar en docs/incidents/{número}/")
  
  **Videos**:
  - Descripción del contenido del video
  - Path/ubicación
  
  **Logs Adjuntos**:
  - Tipo de log (application, server, database)
  - Líneas relevantes o path al archivo
  
  **Technical Context**:
  - URL(s) afectadas con paths completos
  - Versión de deployment/release
  - Browser/OS/Device (si aplica)
  - Request/Response details (si aplica)
  - Stack trace (si disponible)
  </action>
  
  <template-output>evidences_section, technical_context</template-output>
</step>

<step n="4" goal="Documentar valores específicos que causan el error">
  <action>Profundiza en los valores ESPECÍFICOS que reproducen el error para facilitar el diagnóstico:

  Elicita mediante ejemplos concretos:
  - "¿Qué valor de entrada exacto causa el error?" 
    Ejemplos: email específico, ID de usuario, monto de transacción, texto en campo
  
  - "¿Funciona con otros valores? ¿Cuáles funcionan correctamente?"
    Pide al menos 1-2 ejemplos de valores que SÍ funcionan
  
  - "¿Hay un patrón en los datos que fallan?"
    Ejemplos de patrones: 
    - Emails con '+' en el nombre (user+test@example.com)
    - Montos mayores a cierto valor (> 1000)
    - IDs con formato específico (UUID vs numérico)
    - Textos con caracteres especiales (acentos, símbolos)
    - Fechas en formato particular
    - Valores null, vacíos, o muy largos
  
  Documenta comparativamente para facilitar diagnóstico:
  
  **Valores que FALLAN** (ejemplos concretos):
  - Ejemplo 1: [valor específico] → Error: [mensaje]
  - Ejemplo 2: [valor específico] → Error: [mensaje]
  - Patrón identificado: [descripción del patrón común]
  
  **Valores que FUNCIONAN** (ejemplos concretos):
  - Ejemplo 1: [valor específico] → Éxito
  - Ejemplo 2: [valor específico] → Éxito
  
  **Análisis comparativo**:
  - Diferencia clave entre valores que fallan vs funcionan
  
  POR QUÉ esto es importante:
  Esta información permite al Architect identificar la causa raíz mucho más rápido,
  reduciendo el tiempo de diagnóstico de horas a minutos en muchos casos.
  
  Si el usuario no tiene ejemplos específicos, ayúdalo a pensar:
  "¿Recuerdas qué valor ingresaste cuando viste el error por primera vez?"
  "¿Puedes probar con otro valor similar y ver si también falla?"
  </action>
  
  <action>Estructura la información de valores en formato claro y searchable</action>
  
  <template-output>failing_values</template-output>
</step>

<step n="5" goal="Identificar componentes técnicos afectados">
  <ask>¿Qué componentes técnicos del sistema están afectados?

  Ejemplos:
  - Servicios: authentication-service, user-api, payment-module, notification-service
  - Frontend: login-page, dashboard-component, checkout-flow
  - Backend: auth-middleware, database-layer, cache-service
  - Infraestructura: database, message-queue, CDN, API gateway
  
  Lista al menos 1 componente, separados por comas si son múltiples:</ask>
  
  <action>Valida que al menos un componente esté identificado.
  Si el usuario no está seguro, ayúdalo basándose en la descripción del error:
  - Si es error de login → authentication-service, auth-middleware
  - Si es error de UI → componente de frontend específico
  - Si es error de API → servicio backend correspondiente
  </action>
  
  <template-output>affected_components</template-output>
</step>

<step n="5.1" goal="Analizar y proponer recategorización si es necesario">
  <action>IMPORTANTE: Ahora que tienes TODA la información recopilada (descripción, impacto, evidencias, valores específicos, Y componentes afectados), puedes realizar un análisis completo y preciso de la categorización.</action>
  

  <action>Analiza la información recopilada hasta ahora para detectar si la severidad o tipo de error inicialmente indicados por el usuario son apropiados:
  
  **Análisis de Severidad**:
  Evalúa si la severidad {{severity}} es correcta considerando:
  
  Criterios para AUMENTAR severidad:
  - Más de 10 usuarios afectados → considerar P1 (Alto)
  - Más de 50 usuarios afectados → considerar P0 (Crítico)
  - Funcionalidad crítica del negocio bloqueada (pagos, autenticación, datos sensibles) → P0 o P1
  - **Componentes críticos afectados** ({{affected_components}} incluye: authentication-service, payment-module, user-api, database) → P0 o P1
  - Pérdida de datos confirmada → P0
  - Brecha de seguridad confirmada → P0
  - Sistema completamente caído → P0
  - Error en producción vs staging/development → elevar un nivel
  
  Criterios para DISMINUIR severidad:
  - Workaround viable disponible y documentado → bajar un nivel
  - Solo 1-2 usuarios afectados en escenario muy específico → considerar P3 o P4
  - Error solo en development (no staging/production) → considerar P3 o P4
  - Impacto cosmético sin afectación funcional → P4
  
  **Análisis de Tipo de Error**:
  Evalúa si el tipo {{error_type}} es correcto considerando las evidencias:
  
  Señales de recategorización:
  - Usuario reportó "Bug" pero logs muestran degradación de performance → Performance
  - Usuario reportó "Bug" pero evidencias muestran datos corruptos → Data Loss
  - Usuario reportó "Performance" pero stack trace muestra error 500 → Bug
  - Usuario reportó "Bug" pero evidencias muestran vulnerabilidad → Security
  - Usuario reportó "Bug" pero funcionalidad funcionaba antes y dejó de funcionar → Regression
  </action>
  
  <action>Determina si hay necesidad de recategorización:
  
  - Si la severidad Y tipo de error son apropiados → NO proponer cambios
  - Si detectas discrepancia significativa → PROPONER recategorización
  </action>
  
  <check if="se detecta necesidad de recategorización">
    <action>Presenta tu análisis al usuario de forma clara y justificada:
    
    "🔍 **Análisis de Categorización**
    
    Basándome en la información recopilada, he detectado que la categorización inicial podría no reflejar adecuadamente la situación:
    
    **Categorización Actual**:
    - Severidad: {{severity}}
    - Tipo de Error: {{error_type}}
    
    **Categorización Propuesta**:
    - Severidad: {{proposed_severity}}
    - Tipo de Error: {{proposed_error_type}}
    
    **Justificación**:
    {{justificación_detallada_con_evidencias}}
    
    Ejemplos de evidencias consideradas:
    - {{evidencia_1}}
    - {{evidencia_2}}
    - {{evidencia_3}}"
    </action>
    
    <ask>¿Apruebas la recategorización propuesta?
    
    Opciones:
    1. Sí, aprobar cambios propuestos
    2. No, mantener categorización original
    3. Modificar parcialmente (especificar qué cambiar)
    
    Selecciona opción (1/2/3):</ask>
    
    <check if="respuesta es 1 (aprobar)">
      <action>Actualiza las variables:
      - severity = {{proposed_severity}}
      - error_type = {{proposed_error_type}}
      </action>
      
      <action>Registra el cambio de categorización:
      
      **Recategorización Aplicada**:
      - Severidad original: {{original_severity}} → Nueva: {{severity}}
      - Tipo original: {{original_error_type}} → Nuevo: {{error_type}}
      - Razón: {{justificación_resumida}}
      - Aprobado por: {{user_name}}
      - Fecha: {{date}}
      </action>
      
      <action>Genera el log de recategorización para el documento final:
      
      recategorization_log = "
**Recategorization Applied during Initial Triage**:

| Aspect | Original | Updated | Reason |
|--------|----------|---------|--------|
| Severity | {{original_severity}} | {{severity}} | {{recategorization_reason_severity}} |
| Type | {{original_error_type}} | {{error_type}} | {{recategorization_reason_type}} |

- **Date**: {{date}}
- **Approved by**: {{user_name}}
- **Phase**: Initial Reception (Step 5.1)
"
      </action>
      
      <action>Notifica: "✅ Categorización actualizada exitosamente."</action>
      
      <template-output>severity, error_type, original_severity, original_error_type, proposed_severity, proposed_error_type, recategorization_applied, recategorization_reason, recategorization_log</template-output>
    </check>
    
    <check if="respuesta es 2 (mantener original)">
      <action>Registra la decisión de mantener categorización original:
      
      **Recategorización Propuesta pero NO Aplicada**:
      - Se propuso cambiar severidad a: {{proposed_severity}}
      - Se propuso cambiar tipo a: {{proposed_error_type}}
      - Usuario decidió mantener categorización original
      - Razón del usuario: {{razón_opcional_del_usuario}}
      - Fecha: {{date}}
      </action>
      
      <action>Genera el log de recategorización para el documento final:
      
      recategorization_log = "
**Recategorization Proposed but Not Applied**:
- Proposed severity change: {{original_severity}} → {{proposed_severity}} (NOT applied)
- Proposed type change: {{original_error_type}} → {{proposed_error_type}} (NOT applied)
- User decision: Keep original categorization
- Date: {{date}}
"
      </action>
      
      <action>Notifica: "📝 Se mantendrá la categorización original como indicaste."</action>
      
      <template-output>recategorization_proposed_not_applied, original_severity, original_error_type, proposed_severity, proposed_error_type, recategorization_log</template-output>
    </check>
    
    <check if="respuesta es 3 (modificar parcialmente)">
      <ask>¿Qué deseas modificar?
      
      1. Solo cambiar severidad a: {{proposed_severity}}
      2. Solo cambiar tipo de error a: {{proposed_error_type}}
      3. Especificar valores personalizados
      
      Selecciona opción:</ask>
      
      <action>Aplica los cambios parciales según la selección del usuario</action>
      
      <action>Registra el cambio parcial de categorización</action>
      
      <action>Genera el log de recategorización para el documento final (ajustado según cambios parciales aplicados)</action>
      
      <template-output>severity, error_type, partial_recategorization_applied, original_severity, original_error_type, proposed_severity, proposed_error_type, recategorization_log</template-output>
    </check>
  </check>
  
  <check if="NO se detecta necesidad de recategorización">
    <action>Notifica brevemente: "✅ La categorización inicial ({{severity}} - {{error_type}}) es apropiada según el análisis."</action>
    
    <action>Genera el log de recategorización para el documento final:
    
    recategorization_log = "_No recategorization needed. Initial categorization validated._"
    </action>
    
    <template-output>categorization_validated, recategorization_log</template-output>
  </check>
</step>

<step n="6" goal="Generar y guardar documento de incidente">
  <action>Genera un título conciso y descriptivo para el incidente basado en error_description.
  
  Formato recomendado: "[Componente] Error al [acción] - [síntoma clave]"
  
  Ejemplos:
  - "Login endpoint retornando 500 error tras deployment"
  - "Dashboard no carga datos para usuarios con rol Admin"
  - "Payment API falla con montos decimales mayores a 1000"
  </action>
  
  <action>Crea el documento de incidente usando el template incident.template.md
  
  Reemplaza TODAS las variables capturadas en los pasos anteriores:
  - incident_number
  - incident_title
  - date
  - environment
  - severity
  - error_type
  - user_name
  - recategorization_log (ya generado en Step 5.1)
  - error_description
  - impact
  - reproduction_steps
  - expected_behavior
  - actual_behavior
  - evidences_section
  - technical_context
  - failing_values
  - affected_components
  </action>
  
  <action>Valida que no queden placeholders vacíos en secciones críticas:
  
  CRÍTICAS (no pueden estar vacías):
  - Incident Information completa
  - Error Description con resumen
  - Steps to Reproduce con al menos 2-3 pasos
  - Expected vs Actual behavior
  - Affected Components con al menos 1 componente
  
  IMPORTANTES (pueden tener "No disponible" si no aplica):
  - Evidencias (ideal tener al menos 1)
  - Technical Context (ideal tener URL o versión)
  - Valores que causan error (ideal tener al menos 1 ejemplo)
  
  Si alguna sección crítica está vacía, solicita la información antes de continuar.
  </action>
  
  <action>Guarda el archivo en: {incidents_location}/{{incident_number}}.incident.md
  
  Crea el directorio si no existe.
  </action>
  
  <action>Muestra un resumen al usuario:
  
  "✅ Incidente {{incident_number}} creado exitosamente.
  
  📋 Resumen:
  - Título: {{incident_title}}
  - Severidad: {{severity}}
  - Entorno: {{environment}}
  - Componentes: {{affected_components}}
  
  📍 Archivo guardado en: {{incident_file_path}}
  
  🔜 Próximo paso:
  El Architect debe diagnosticar la causa raíz del error.
  
  Por favor invoca al Architect con el comando: *diagnosticar
  y proporciona el path del incidente: {{incident_file_path}}
  
  Si es incidente P0-P1 (Crítico/Alto), proceder INMEDIATAMENTE al diagnóstico.
  Si es P2-P4, puede planificarse para próximo sprint."
  </action>
  
  <template-output>incident_title, incident_file_path</template-output>
</step>

</workflow>
