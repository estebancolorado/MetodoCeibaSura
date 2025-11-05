# Revisión de Código (Peer Review) - Instrucciones del Workflow

```xml
<critical>El motor de ejecución de workflows está gobernado por: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
<critical>DEBES haber cargado y procesado: {installed_path}/workflow.yaml</critical>
<critical>Comunica todas las respuestas en {communication_language} y adapta el lenguaje a {user_skill_level}</critical>
<critical>Genera todos los documentos en {document_output_language}</critical>
<critical>Este workflow realiza una Revisión de Código tipo Peer Review en una historia marcada como 'Lista para Revisión', añade notas de revisión estructuradas, y actualiza el estado basado en el resultado.</critical>
<critical>SOLO modifica el archivo de historia en estas áreas: Status (opcional según settings), Sección 'Revisión de Código (Peer Review)' al final del documento, y Change Log.</critical>
<critical>Ejecuta TODOS los pasos en orden exacto; NO omitas pasos</critical>


<workflow>

  <step n="1" goal="Localizar historia y verificar estado para revisión">
    <ask>¿Qué historia de usuario deseas revisar?

Proporciona el número de la historia (ejemplo: 5) o la ruta completa del archivo.</ask>

    <action>Buscar archivo en {dev_story_location} usando el número o nombre proporcionado</action>
    <action>Resolver path completo del archivo encontrado y almacenar en {{story_path}}</action>

    <check if="archivo no existe">
      <action>HALT con error: "Historia no encontrada en {dev_story_location}"</action>
    </check>

    <action>Leer contenido COMPLETO del archivo de historia</action>
    <action>Parsear secciones: Status, Historia de Usuario, Contexto de Negocio, Criterios de Aceptación, Tasks/Subtasks (y estados de completitud), Notas del Desarrollador, Registro del Agente de Desarrollo (Referencia de Contexto, Notas de Completitud, Lista de Archivos), Change Log.</action>
    <action>Extraer {{story_number}} del filename si es posible (ej: 6.nombre.story.md → story_number=6).</action>

    <check if="Status no está en {{allow_status_values}}">
      <action>HALT con mensaje:</action>
      <output>❌ ESTADO INCORRECTO PARA REVISIÓN

Estado actual: {{estado_actual}}

La historia DEBE estar en uno de estos estados para proceder con la revisión:
- Lista para Revisión
- Desarrollo Completado
- En Revisión

Por favor completa el desarrollo de la historia antes de ejecutar la revisión.</output>
    </check>
  </step>

  <step n="2" goal="Cargar contexto arquitectónico y estándares de código">
    <action>Localizar Contexto de Análisis Arquitectónico dentro de la historia: Buscar sección "## Análisis Arquitectónico (Arquitecto)". Si existe, leer contenido completo que incluye decisiones arquitectónicas, componentes afectados, y consideraciones técnicas.</action>
    <action if="no se encuentra sección de análisis arquitectónico">Continuar pero registrar WARNING en notas de revisión: "No se encontró análisis arquitectónico previo en la historia".</action>
    
    <action>Cargar documentación de arquitectura y estándares:</action>
    <action>1. **GPS Arquitectónico**: Si existe {{architecture_file}} (típicamente {architecture_sharded_location}/index.md), leer COMPLETO. Este documento contiene visión general del sistema, componentes principales, patrones arquitectónicos, y mapa de navegación.</action>
    <action>2. **Estándares de Código**: Buscar {{coding_standards_file}} en {{architecture_sharded_location}}. Si existe, leer COMPLETO. Este documento define convenciones de código, patrones recomendados, y prácticas prohibidas.</action>
    <action>3. **Documentación de Componentes**: Usar comodines para buscar en {{architecture_sharded_location}}: `architecture-*.md`, `component-*.md`. Leer aquellos componentes mencionados en la lista de archivos modificados de la historia.</action>
    <action>4. **Flujos de Negocio**: Usar comodines para buscar en {{architecture_sharded_location}}: `flujo-*.md`, `flow-*.md`, `proceso-*.md`. Leer flujos relacionados con la funcionalidad implementada.</action>
    
    <action if="coding-standards.md no encontrado">Registrar WARNING: "No se encontró documento de estándares de código. Revisión se basará en mejores prácticas generales".</action>
    <action if="index.md no encontrado">Registrar WARNING: "No se encontró GPS arquitectónico (index.md). Revisión arquitectónica será limitada".</action>
  </step>

  <step n="3" goal="Detectar tech stack y establecer conjunto de mejores prácticas">
    <action>Detectar ecosistema(s) primario(s) escaneando manifiestos (ej: package.json, pyproject.toml, go.mod, Dockerfile, pom.xml, Gemfile, etc.). Registrar frameworks clave (ej: Node/Express, React/Vue/Angular, Python/FastAPI/Django, Java/Spring, Ruby/Rails, etc.).</action>
    <action>Identificar versiones de frameworks y lenguajes para detectar características obsoletas o vulnerabilidades conocidas.</action>
    <action>Sintetizar nota concisa de "Mejores Prácticas y Referencias" capturando consideraciones específicas del stack detectado que deben influir en la revisión (citar versiones si están disponibles).</action>
    <action>Ejemplos de consideraciones: async/await en Node.js, context managers en Python, dependency injection en Spring, hooks en React, etc.</action>
  </step>

  <step n="4" goal="Evaluar implementación contra criterios de aceptación y especificaciones">
    <action>De la historia, leer Criterios de Aceptación y Tasks/Subtasks con sus estados de completitud.</action>
    <action>De la sección "Registro del Agente de Desarrollo" → "Lista de Archivos", compilar lista de archivos cambiados/agregados. Si la Lista de Archivos está faltante o claramente incompleta, buscar en el repo cambios recientes relevantes al scope de la historia (heurísticas: nombres de archivos que coincidan con componentes/servicios/rutas/tests inferidos de ACs/tasks).</action>
    <action>Para cada Criterio de Aceptación, verificar que existe evidencia de implementación y tests correspondientes. Los tests esperados están documentados en las Tasks/Subtasks del refinamiento técnico (unit/integration/E2E según lo definido). Notar gaps explícitamente.</action>
    <action>Si existe análisis arquitectónico en la historia, verificar que la implementación sigue las decisiones arquitectónicas documentadas (patrones, componentes, flujos, integraciones).</action>
    <action if="decisiones arquitectónicas críticas son violadas (ej: capas, reglas de dependencias, patrones obligatorios)">Marcar como hallazgo de Severidad Alta.</action>
  </step>

  <step n="5" goal="Realizar revisión de calidad de código y riesgos">
    <action>Para cada porción de código modificada (nuevo código o cambios en código existente), revisar problemas comunes apropiados al stack: manejo de errores, validación de inputs, logging, inyección de dependencias, correctitud de thread-safety/async, limpieza de recursos, anti-patrones de rendimiento.</action>
    <action>Usar la "Lista de Archivos" del Registro del Agente de Desarrollo para identificar qué archivos revisar. Enfocarse en las secciones modificadas, no en el archivo completo.</action>
    
    <action>**Hardcoded Values y Magic Numbers**: Buscar literales (strings/números) en lógica de negocio que deberían ser constantes/enums. Excepciones: UI/display, índices 0/1, booleanos. Severidad Media si ≥2 referencias, Baja si 1 referencia.</action>
    
    <action>**Código Autodescriptivo**: Priorizar nombres claros sobre comentarios redundantes. Comentarios solo para: decisiones arquitectónicas no obvias, workarounds temporales, algoritmos complejos, TODOs. Marcar Severidad Baja si comentarios explican "qué hace" en lugar de "por qué".</action>
    
    <action>**Revisión de Seguridad** (OWASP Top 10 - aplicar solo a código nuevo/modificado):</action>
    <action>1. **Inyección**: Buscar SQL injection, NoSQL injection, command injection, LDAP injection. Verificar uso de queries parametrizadas, ORMs seguros, sanitización de inputs.</action>
    <action>2. **Autenticación/Autorización**: Verificar implementación correcta de authN/authZ, manejo de sesiones, tokens JWT seguros, verificación de permisos en cada endpoint.</action>
    <action>3. **Exposición de Datos Sensibles**: Buscar secretos hardcodeados (passwords, API keys, tokens), datos sensibles en logs, transmisión sin encripción.</action>
    <action>4. **XML External Entities (XXE)**: Si se procesa XML, verificar parsers seguros y deshabilitación de entidades externas.</action>
    <action>5. **Control de Acceso Roto**: Verificar que usuarios no puedan acceder a recursos de otros usuarios, autorización a nivel de objeto.</action>
    <action>6. **Configuración Incorrecta de Seguridad**: Revisar CORS mal configurado, headers de seguridad faltantes, defaults inseguros, stack traces expuestos.</action>
    <action>7. **Cross-Site Scripting (XSS)**: Verificar sanitización de outputs, escapado correcto en templates, Content-Security-Policy.</action>
    <action>8. **Deserialización Insegura**: Verificar deserialización de datos no confiables.</action>
    <action>9. **Componentes con Vulnerabilidades Conocidas**: Revisar versiones de dependencias en manifiestos, buscar CVEs conocidos.</action>
    <action>10. **Logging y Monitoreo Insuficiente**: Verificar que eventos de seguridad son loggeados, no se loggean datos sensibles.</action>
    
    <action>**Revisión de Calidad de Tests**:</action>
    <action>- Aserciones son significativas (no solo `expect(result).toBeDefined()`)</action>
    <action>- Casos borde están cubiertos (valores nulos, arrays vacíos, strings largos, números negativos, etc.)</action>
    <action>- Comportamiento determinista (no dependen de orden de ejecución, timestamps actuales sin mockear, etc.)</action>
    <action>- Fixtures apropiados (datos de test bien estructurados, factories/builders)</action>
    <action>- No patrones de flakiness (timers sin mockear, dependencias de red real, race conditions)</action>
    
    <action>Capturar sugerencias concretas y accionables con severidad (Alta/Media/Baja) y justificación. Cuando sea posible, sugerir cambios específicos a nivel de código (nombres de archivos + rangos de líneas) sin reescribir secciones grandes.</action>
    <action>Formato de hallazgo: "**[Severidad]** Descripción del problema en `archivo.ts:línea`. **Justificación**: Explicación del porqué es un problema. **Solución**: Acción específica a tomar."</action>
  </step>

  <step n="6" goal="Decidir resultado de revisión y preparar notas">
    <action>Determinar resultado: **Aprobado**, **Cambios Solicitados**, o **Bloqueado**.</action>
    <action>Criterios: Aprobado = todos ACs OK + sin issues críticos; Cambios Solicitados = issues presentes; Bloqueado = violaciones graves.</action>
    
    <critical>El template template.md define la estructura SIMPLE de la sección "Revisión de Código (Peer Review)"</critical>
    <critical>SOLO llenar las variables esenciales listadas abajo - sin complicaciones</critical>

    <mandate>REGLAS DE SALIDA DEL TEMPLATE: (1) Remover comentarios HTML de secciones completadas, mantener para secciones vacías/opcionales. (2) Asegurar que TODOS los elementos del template (títulos, encabezados, etiquetas) coincidan con {document_output_language}, no solo el contenido.</mandate>

    <mandate>Variables OBLIGATORIAS - Básicas:</mandate>
    <template-output>reviewer_name</template-output>
    <template-output>review_date</template-output>
    <template-output>review_outcome</template-output>
    <template-output>summary</template-output>

    <mandate>Variables OBLIGATORIAS - Hallazgos (lista simple):</mandate>
    <template-output>has_findings</template-output>
    <template-output>findings</template-output>
    <!-- findings: [ { severity: "Alta|Media|Baja", description: "...", file_path: "...", line_number: X, issue: "...", solution: "..." } ] -->

    <mandate>Variables OBLIGATORIAS - ACs y Seguridad:</mandate>
    <template-output>ac_coverage_summary</template-output>
    <!-- Texto simple: "3/3 ACs implementados" o "2/3 ACs completos - falta AC#2" -->
    
    <template-output>security_notes</template-output>
    <!-- Texto simple: "Sin vulnerabilidades detectadas" o "2 issues de seguridad: SQL injection en file.ts:45, secreto en config.ts:12" -->

    <mandate>Variables OBLIGATORIAS - Acciones Requeridas:</mandate>
    <template-output>has_action_items</template-output>
    <template-output>action_items</template-output>
    <!-- action_items: [ { severity: "Alta|Media|Baja", description: "...", reference: "AC #X, file.ts:line" } ] -->

    <template-output>next_steps</template-output>
    <!-- Texto simple según outcome: "Listo para merge" o "Implementar correcciones y re-ejecutar revisión" -->

    <action>IMPORTANTE: Mantener hallazgos concretos y específicos (archivo, línea, problema, solución)</action>
    <action>IMPORTANTE: Acciones requeridas en formato task checkbox listo para implementar</action>
  </step>

  <step n="7" goal="Agregar revisión a historia y actualizar metadata">
    <critical>MODO APPEND: Agregar la sección "Revisión de Código (Peer Review)" al FINAL del archivo de historia</critical>
    <critical>NO modificar contenido existente - solo agregar nueva sección al final</critical>
    
    <action>Abrir {{story_path}} y ubicar el final del archivo.</action>
    <action>Agregar nueva sección usando el template.md SIMPLE con las variables llenadas.</action>
    <action>Agregar entrada en Change Log: "Revisión de código (Peer Review) - Resultado: {{review_outcome}}".</action>
    <action>Si {{update_status_on_result}}: actualizar Estado de la historia según resultado.</action>
    <action>Guardar el archivo de historia.</action>
  </step>

  <step n="8" goal="Validación y completitud">
    <invoke-task>Ejecutar checklist de validación en {installed_path}/checklist.md usando {project-root}/.ceiba-metodo/core/tasks/validate-workflow.xml</invoke-task>
    <action>Reportar completitud del workflow.</action>
    <output>**✅ Revisión de Código Completada, {user_name}!**

**Detalles de la Historia:**
- Historia: {{story_number}}
- Resultado de Revisión: {{outcome}}
- Acciones Requeridas: {{action_item_count}}
- Hallazgos Alta Severidad: {{high_severity_count}}
- Hallazgos Media Severidad: {{medium_severity_count}}
- Hallazgos Baja Severidad: {{low_severity_count}}

**Estado actualizado:** {{new_status}}

**Próximos Pasos:**
{{#if outcome == "Aprobado"}}
1. ✅ La historia está lista para merge a rama principal
2. Si hay acciones requeridas de baja prioridad:
   - **Para implementarlas de inmediato:** Ve al agente **Developer** y ejecuta el workflow `*desarrollar-historia-usuario`
   - O considera implementarlas en futuras mejoras
{{/if}}
{{#if outcome == "Cambios Solicitados"}}
1. Revisa las acciones requeridas en la sección "Revisión de Código (Peer Review)"
2. **Para aplicar las correcciones:** Ve al agente **Developer** y ejecuta el workflow `*desarrollar-historia-usuario`
3. Una vez completadas las correcciones, regresa aquí y ejecuta nuevamente `*revisar-historia`
{{/if}}
{{#if outcome == "Bloqueado"}}
1. ⚠️ La historia tiene problemas críticos que requieren atención inmediata
2. Revisa los hallazgos de Alta Severidad en la sección de revisión
3. Considera consultar con el arquitecto si hay violaciones arquitectónicas
4. **Para resolver los bloqueos:** Ve al agente **Developer** y ejecuta el workflow `*desarrollar-historia-usuario`
5. Una vez resueltos, regresa aquí y ejecuta nuevamente `*revisar-historia`
{{/if}}
    </output>
  </step>

</workflow>
```
