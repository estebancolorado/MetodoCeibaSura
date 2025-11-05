# Analizar y Diseñar Historia de Usuario - Arquitecto Especialista

<workflow>

<critical>The workflow execution engine is governed by: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {installed_path}/workflow.yaml</critical>
<critical>ALWAYS communicate STRICTLY in {communication_language} regardless of the language used by the user</critical>
<critical>Generate all documents in {document_output_language}</critical>


<note title="Purpose">
Analizar una historia de usuario desde la perspectiva arquitectónica y proponer un diseño de implementación validado por el usuario humano. Esta tarea se enfoca exclusivamente en decisiones arquitectónicas y de diseño, dejando la descomposición técnica detallada para el refinamiento y estimación posterior por medio del agente Developer. El análisis se documenta EN EL MISMO ARCHIVO de la historia de usuario.
</note>

<note title="When to Use This Workflow">
- Existe una historia creada por el PO en estado "Borrador (PO)"
- Se necesita determinar el impacto arquitectónico de la funcionalidad
- Se requiere validación de decisiones de diseño antes del refinamiento técnico
- La historia puede afectar componentes existentes o requerir nuevos componentes
- Se necesita asegurar coherencia con la arquitectura existente
</note>

<note title="Prerequisites">
- Historia de usuario existente creada por PO
- Documentación de arquitectura actualizada
- Acceso al usuario humano (arquitecto) para validación
- Config del módulo configurado correctamente (config_source)
</note>

<mandate title="Reglas de Comportamiento OBLIGATORIAS">
0. VALIDAR CÓDIGO REAL ANTES DE PROPONER: Leer código fuente de componentes mencionados en historia ANTES de diseñar solución (paso 1.5 OBLIGATORIO). PROHIBIDO proponer basándose en suposiciones o solo en documentación arquitectónica
1. INCORPORAR CONSIDERACIONES DEL ARQUITECTO: Aplicar y justificar cómo se incorporan las especificaciones del arquitecto (paso 0.3) a lo largo de TODO el análisis arquitectónico
2. PRESERVAR INFORMACIÓN DEL PO: Mantener intacto todo el trabajo original del PO en la historia de usuario - trabajar siempre sobre el archivo existente
3. ENFOQUE ARQUITECTÓNICO: Priorizar decisiones de diseño de alto nivel (patrones, componentes, dependencias). Los detalles técnicos útiles que surjan pueden documentarse en "Notas Técnicas del Arquitecto", pero no son el foco principal
4. NO INCLUIR ESTIMACIONES DE TIEMPO: NUNCA agregar estimaciones de horas/días en el análisis arquitectónico. Las estimaciones son responsabilidad del Developer en la fase de refinamiento y tasking posterior
5. COHERENCIA CON DOCUMENTACIÓN: Asegurar alineación con GPS arquitectónico, componentes documentados, flujos existentes e historias previas
6. TRAZABILIDAD COMPLETA: Documentar referencias a arquitectura y historias consultadas en cada decisión tomada
7. ESPECIFICACIÓN DE INTERFACES: Definir claramente contratos entre componentes cuando aplique para facilitar desarrollo posterior
</mandate>

<step n="0" goal="Configuración y Validación Inicial">
  <substep n="0.1" title="Solicitar Historia a Analizar">
    <message>
🏗️ **Análisis Arquitectónico de Historia de Usuario**

¿Qué historia de usuario requiere análisis arquitectónico?

Puedes proporcionar:
- **Número de la historia** (ejemplo: 5 para analizar 5.{nombre}.story.md)
- **Ruta completa** del archivo de historia

La historia debe estar en estado "Borrador (PO)" o sin análisis arquitectónico previo.
    </message>
    
    <ask>Esperar respuesta del arquitecto con número o ruta de la historia</ask>
  </substep>
  
  <substep n="0.2" title="Validar y Cargar Historia Existente">
    <action if="usuario proporcionó número">Construir ruta del archivo: {dev_story_location}/{{story_number}}.*.story.md</action>
    <action if="usuario proporcionó ruta">Usar ruta directa proporcionada</action>
    
    <action>Buscar y verificar que existe el archivo de historia en la ubicación especificada</action>
    
    <check if="archivo no encontrado">
      <message>
❌ **ERROR: Historia no encontrada**

No se encontró la historia especificada en: {{ruta_buscada}}

Verifica:
- El número o ruta proporcionada es correcta
- El archivo existe en la ubicación configurada: {dev_story_location}
- El formato del nombre es correcto (ej: 5.filtrar-asignaciones.story.md)
      </message>
      <action>HALT workflow</action>
    </check>
    
    <action>Cargar contenido COMPLETO de la historia de usuario</action>
    <action>Extraer: story_number, story_title, estado actual, metadata existente</action>
    
    <action>Verificar estado de la historia:</action>
    <check if='estado != "Borrador (PO)" AND estado != "Sin análisis arquitectónico"'>
      <message>
⚠️ **ADVERTENCIA: Estado de Historia**

La historia está en estado: {{estado_actual}}

Este workflow está diseñado para historias en "Borrador (PO)" o sin análisis previo.

¿Deseas continuar con el análisis arquitectónico de todos modos? (s/n)
      </message>
      <ask>Esperar confirmación del arquitecto</ask>
      <check if="respuesta == n">
        <message>Análisis cancelado por el usuario.</message>
        <action>HALT workflow</action>
      </check>
    </check>
    
    <message>
✅ **Historia Cargada Exitosamente**

- **Historia #{{story_number}}:** {{story_title}}
- **Estado actual:** {{estado_actual}}
- **Archivo:** {{ruta_archivo}}

Iniciando análisis arquitectónico...
    </message>
  </substep>
  
  <substep n="0.3" title="Consulta Obligatoria de Enfoque Arquitectónico">
    <critical>🚨 OBLIGATORIO: Consultar consideraciones del arquitecto antes de iniciar análisis</critical>
    
    <message>
🏗️ **CONSULTA DE ENFOQUE ARQUITECTÓNICO**

Antes de iniciar el análisis arquitectónico de la Historia #{{story_number}}: {{story_title}}

¿Tienes alguna consideración, restricción o planteamiento inicial que deba tener en cuenta?

**Opciones de trabajo:**

**A) ENFOQUE DIRIGIDO** - Tienes consideraciones específicas:
- ¿Hay patrones arquitectónicos específicos que prefieres?
- ¿Existen restricciones técnicas o de negocio particulares?
- ¿Hay componentes específicos que debo considerar o evitar?
- ¿Tienes algún planteamiento inicial de diseño?
- ¿Existen componentes, flujos, o código que deba reutilizar?

**B) ENFOQUE EXPLORATORIO** - Quieres que el agente analice primero:
- El agente realizará el análisis completo de arquitectura disponible
- Presentará una propuesta fundamentada basada en la documentación existente
- Luego recibirás la propuesta para validación y ajustes

---

Por favor responde:
1. **Opción preferida:** A (Dirigido) o B (Exploratorio)
2. **Si eliges A:** Detalla tus consideraciones específicas
3. **Si eliges B:** Confirma que proceda con análisis exploratorio
4. **Cualquier consideración adicional** que deba tener presente
    </message>
    
    <ask critical="true">⚠️ NO CONTINUAR hasta recibir respuesta del arquitecto</ask>
    
    <action>Documentar respuesta del arquitecto:</action>
    <action if="enfoque == A (Dirigido)">Registrar TODAS las consideraciones específicas mencionadas</action>
    <action if="enfoque == B (Exploratorio)">Confirmar libertad total de análisis técnico</action>
    <action>Esta información será la BASE para todo el análisis posterior</action>
    
    <message>
✅ **Enfoque Arquitectónico Registrado**

Enfoque seleccionado: {{enfoque_seleccionado}}
{{consideraciones_registradas}}

Procederé con el análisis teniendo en cuenta estas consideraciones.
    </message>
  </substep>
</step>

<step n="1" goal="Análisis Arquitectónico Profundo">
  <critical>⚠️ Considerar SIEMPRE las especificaciones del arquitecto del paso 0.3</critical>
  
  <substep n="1.1" title="Revisión Integral de Documentación Arquitectónica">
    <action>Análisis integral del ecosistema arquitectónico según enfoque definido:</action>
    
    <action if="enfoque == A (Dirigido)">Priorizar análisis en las áreas específicas mencionadas por el arquitecto</action>
    <action if="enfoque == B (Exploratorio)">Realizar análisis comprehensivo sin sesgos predefinidos</action>
    <action>En ambos casos: Documentar cómo las consideraciones del arquitecto influencian el análisis</action>
    
    <action>**1. GPS Arquitectónico** ({architecture_sharded_location}/index.md):</action>
    <action>- Revisar overview del sistema y identificar módulos relevantes</action>
    <action>- Mapear la funcionalidad de la historia contra componentes existentes</action>
    <action>- Identificar patrones arquitectónicos aplicables</action>
    <action>- Evaluar impacto en la arquitectura general</action>
    
    <action>**2. Documentación de Componentes** ({architecture_sharded_location}/architecture-*.md, component*.md, etc.):</action>
    <action>- Usar comodines de búsqueda para encontrar TODOS los componentes documentados</action>
    <action>- Analizar componentes que podrían verse afectados por la funcionalidad</action>
    <action>- Revisar APIs, interfaces y responsabilidades de componentes existentes</action>
    <action>- Identificar capacidades y limitaciones actuales</action>
    <action>- Evaluar necesidad de extensiones o modificaciones</action>
    
    <action>**3. Flujos de Negocio** ({architecture_sharded_location}/flujo-*.md, flow-*.md, proceso-*.md, etc.):</action>
    <action>- Usar comodines de búsqueda para encontrar TODOS los flujos documentados</action>
    <action>- Identificar flujos existentes relacionados con la nueva funcionalidad</action>
    <action>- Analizar puntos de integración y extensión en flujos actuales</action>
    <action>- Evaluar impacto en flujos existentes</action>
    <action>- Determinar si se requieren nuevos flujos o modificaciones</action>
    
    <action>**4. Historias Arquitectónicas Previas** ({dev_story_location}/*.story.md):</action>
    <critical>DEBE revisar historias con análisis arquitectónico previo para:</critical>
    <action>- Decisiones arquitectónicas tomadas en funcionalidades similares</action>
    <action>- Patrones de implementación aplicados exitosamente</action>
    <action>- Componentes identificados para funcionalidades relacionadas</action>
    <action>- Estrategias de integración utilizadas anteriormente</action>
    <action>- Lecciones arquitectónicas documentadas en historias previas</action>
    
    <action>DEBE buscar historias que hayan involucrado:</action>
    <action>- Los mismos dominios de negocio</action>
    <action>- Componentes arquitectónicos similares</action>
    <action>- Patrones de integración comparables</action>
    <action>- Decisiones de diseño relacionadas</action>
  </substep>
  
  <substep n="1.2" title="Análisis de Impacto Arquitectónico">
    <action>Evaluar impacto específico de la historia en el sistema:</action>
    
    <action>**Componentes Afectados:**</action>
    <action>- Identificar qué componentes existentes se verán modificados</action>
    <action>- Evaluar la capacidad actual vs. requerimientos de la historia</action>
    <action>- Determinar nivel de modificación necesario (menor, mayor, reestructuración)</action>
    
    <action>**Nuevos Componentes:**</action>
    <action>- Evaluar si se requieren componentes completamente nuevos</action>
    <action>- Justificar la necesidad de nuevos componentes vs. extensión de existentes</action>
    <action>- Proponer responsabilidades y ubicación en la arquitectura</action>
    
    <action>**Integraciones:**</action>
    <action>- Identificar nuevas integraciones requeridas entre componentes</action>
    <action>- Evaluar impacto en interfaces existentes</action>
    <action>- Proponer estrategias de integración coherentes con patrones actuales</action>
    
    <action>Documentar hallazgos internamente para usar en propuesta de diseño</action>
  </substep>
</step>

<step n="1.5" goal="Validación Técnica de Viabilidad">
  <critical>🚨 CHECKPOINT: Verificar código real ANTES de proponer solución</critical>
  
  <action>Identificar componentes técnicos mencionados en la historia (endpoints, servicios, pantallas, componentes, archivos de configuración)</action>
  
  <action>Para CADA componente identificado:</action>
  <action>1. Localizar el código fuente real en el proyecto</action>
  <action>2. Leer implementación actual: ¿Qué hace? ¿Qué datos maneja? ¿Qué limitaciones tiene?</action>
  <action>3. Validar: ¿Soporta la funcionalidad requerida SIN modificación?</action>
  <action>4. Si NO soporta: Documentar QUÉ archivos/métodos específicos requieren cambios</action>
  
  <mandate>PROHIBIDO proponer solución sin haber leído el código de componentes involucrados</mandate>
  <mandate>PROHIBIDO asumir que "ya funciona" - VERIFICAR siempre con código real</mandate>
  <mandate>Si propones "solo modificar X", DEBES haber verificado que Y y Z ya funcionan correctamente</mandate>
  
  <message>
✅ **Validación técnica completada**

Código real verificado. Procedo a diseñar propuesta basada en implementación actual confirmada.
  </message>
</step>

<step n="2" goal="Propuesta de Diseño Arquitectónico">
  <critical>⚠️ Incorporar consideraciones específicas del arquitecto (paso 0.3)</critical>
  <critical>⚠️ USAR ÚNICAMENTE hallazgos verificados del paso 1.5 - NO asumir</critical>
  
  <substep n="2.1" title="Definir Estrategia de Implementación">
    <action>Definir enfoque arquitectónico considerando:</action>
    
    <action>**Consideraciones del Arquitecto:**</action>
    <action if="enfoque == A (Dirigido)">- Aplicar restricciones, patrones o preferencias específicas mencionadas</action>
    <action if="enfoque == B (Exploratorio)">- Proponer la mejor opción basada en análisis técnico puro</action>
    <action>- Justificar cómo se incorporan (o por qué se descartan) las consideraciones del arquitecto</action>
    
    <action>**Patrón Arquitectónico Principal:**</action>
    <action>- Seleccionar patrón arquitectónico apropiado basado en documentación existente Y consideraciones del arquitecto</action>
    <action>- Justificar selección contra alternativas consideradas (incluyendo sugerencias del arquitecto si aplica)</action>
    <action>- Asegurar coherencia con patrones ya establecidos en el sistema</action>
    
    <action>**Distribución de Responsabilidades:**</action>
    <action>- Definir qué componente maneja cada aspecto de la funcionalidad</action>
    <action>- Asegurar adhesión al principio de responsabilidad única</action>
    <action>- Mantener cohesión con responsabilidades existentes</action>
    
    <action>**Flujo de Datos:**</action>
    <action>- Proponer cómo fluirán los datos a través de los componentes</action>
    <action>- Definir transformaciones y validaciones necesarias</action>
    <action>- Identificar puntos de persistencia y recuperación</action>
  </substep>
  
  <substep n="2.2" title="Especificar Componentes y Modificaciones">
    <action>Para cada componente afectado/nuevo, definir:</action>
    
    <action>**Responsabilidades Específicas:**</action>
    <action>- Definir qué funcionalidades específicas manejará</action>
    <action>- Establecer contratos con otros componentes</action>
    
    <action>**Modificaciones Requeridas:**</action>
    <action>- Detallar cambios específicos en componentes existentes</action>
    <action>- Proponer nuevas operaciones o endpoints</action>
    <action>- Identificar deprecaciones o refactorizaciones necesarias</action>
    
    <action>**Dependencias:**</action>
    <action>- Mapear dependencias con otros componentes</action>
    <action>- Identificar nuevas dependencias introducidas</action>
    <action>- Evaluar impacto de dependencias en testabilidad y mantenibilidad</action>
  </substep>
  
  <substep n="2.3" title="Preparar Documento de Propuesta Arquitectónica">
    <action>Generar propuesta arquitectónica clara y concisa para validación</action>
    
    <message>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📋 **PROPUESTA ARQUITECTÓNICA - Historia #{{story_number}}: {{story_title}}**

---

### � Patrón Arquitectónico

**Patrón seleccionado:** {{patrón}}

**¿Por qué este patrón?** {{justificación}}

---

### 🏗️ Componentes

**Componentes afectados:**
{{lista_componentes_con_tipo_y_responsabilidad}}

**Orden de implementación:**
{{secuencia_numerada_con_dependencias}}

---

### 🔗 Integraciones (si aplica)

{{interfaces_o_contratos_entre_componentes}}

---

### ⚠️ Riesgos identificados (si aplica)

{{riesgos_con_mitigaciones}}

---

### ✅ Decisión requerida

¿Apruebas esta propuesta arquitectónica o requieres ajustes?

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    </message>
  </substep>
</step>

<step n="3" goal="Validación Humana Obligatoria">
  <critical>🚨 BLOQUEO OBLIGATORIO - NO CONTINUAR SIN VALIDACIÓN</critical>
  
  <substep n="3.1" title="Solicitud de Validación del Arquitecto">
    <message>
🏗️ **VALIDACIÓN ARQUITECTÓNICA REQUERIDA**

He completado el análisis arquitectónico para la Historia #{{story_number}}: {{story_title}}

**PROPUESTA PRESENTADA ARRIBA ↑**

Necesito tu validación como arquitecto antes de proceder a actualizar la historia:

**Preguntas de validación:**
1. ¿Apruebas la propuesta arquitectónica presentada?
2. ¿Hay ajustes que debo realizar en el diseño?
3. ¿Existen alternativas que debo considerar?
4. ¿Falta información arquitectónica importante?

---

**Opciones de respuesta:**
- **APROBADO** - Proceder con el diseño tal como está propuesto
- **AJUSTES** - Realizar modificaciones específicas (detalla los cambios)
- **REPLANTEAR** - Reconsiderar el enfoque arquitectónico completo

Por favor, proporciona tu feedback detallado.
    </message>
    
    <ask critical="true">⚠️ NO CONTINUAR hasta recibir validación del arquitecto</ask>
  </substep>
  
  <substep n="3.2" title="Iteración Basada en Feedback">
    <check if="respuesta == AJUSTES OR respuesta == REPLANTEAR">
      <action>Incorporar feedback específico del arquitecto</action>
      <action>Revisar decisiones según orientaciones recibidas</action>
      <action>Actualizar propuesta arquitectónica con cambios</action>
      
      <action>Documentar iteración:</action>
      <action>- Registrar feedback recibido</action>
      <action>- Documentar cambios realizados y justificaciones</action>
      <action>- Mantener trazabilidad de decisiones tomadas</action>
      
      <check if="cambios son significativos">
        <message>He actualizado la propuesta arquitectónica con tus consideraciones. Por favor revisa:</message>
        <action>Mostrar propuesta actualizada</action>
        <goto step="3.1">Solicitar nueva validación</goto>
      </check>
    </check>
    
    <check if="respuesta == APROBADO">
      <message>
✅ **DECISIÓN ARQUITECTÓNICA APROBADA**

La propuesta ha sido validada y aprobada por el arquitecto.
Procederé a documentar las decisiones finales en la historia de usuario.
      </message>
    </check>
  </substep>
</step>

<step n="4" goal="Actualización de Historia con Decisiones Arquitectónicas">
  <critical>🚨 OBLIGATORIO: Esta sección es CRÍTICA y NO puede omitirse</critical>
  <critical>⚠️ IMPORTANTE: Insertar nueva sección con las decisiones FINALES APROBADAS</critical>
  <critical>DEBE incluir únicamente las decisiones que fueron validadas y aprobadas por el arquitecto</critical>
  
  <substep n="4.1" title="Generar Sección de Análisis Arquitectónico">
    <critical>El template seccion-analisis-arquitectonico.template.md define la estructura completa</critical>
    <critical>Todas las variables deben coincidir EXACTAMENTE con los nombres en el template</critical>
    <critical>Usar sintaxis Handlebars para arrays: generar objetos JSON válidos</critical>
    
    <action>Generar contenido completo de la sección "Análisis Arquitectónico" con las decisiones arquitectónicas finales aprobadas</action>
    
    <mandate>REGLAS DE SALIDA DEL TEMPLATE: (1) Remover comentarios HTML de secciones completadas, mantener para secciones vacías/opcionales. (2) Asegurar que TODOS los elementos del template (títulos, encabezados, etiquetas) coincidan con {document_output_language}, no solo el contenido.</mandate>
    
    <mandate>OBLIGATORIAS - Generar siempre:</mandate>
    <template-output>patron_arquitectonico</template-output>
    <template-output>justificacion_patron</template-output>
    <template-output>componentes_principales</template-output>
    <template-output>orden_implementacion</template-output>
    <template-output>validacion_impacto</template-output>
    <template-output>documentacion_consultada</template-output>
    <template-output>historias_relacionadas</template-output>
    <template-output>enfoque_arquitectonico</template-output>
    
    <mandate>OPCIONALES - Solo si hay detalles técnicos adicionales útiles:</mandate>
    <template-output>notas_tecnicas</template-output>
    
    <note>Variables del config (automáticas): user_name, date</note>
    <note>Las especificaciones técnicas (código, APIs, interfaces) se incluyen dentro de componentes_principales</note>
  </substep>
  
  <substep n="4.2" title="Insertar Sección en el Archivo de Historia">
    <critical>Modificar el archivo de historia existente EN SU UBICACIÓN ORIGINAL</critical>
    <critical>El template ya fue procesado automáticamente por el engine - ahora insertar en la historia</critical>
    
    <action>Ubicar el punto de inserción en el archivo: después de "## Análisis de Historias Relacionadas" y antes de "## Notas para Refinamiento"</action>
    <action>Insertar la sección completa de "Análisis Arquitectónico" que fue generada desde el template</action>
    <action>Guardar el archivo con la nueva sección incluida</action>
  </substep>
  
  <substep n="4.3" title="Actualizar Estado y Metadata de Historia">
    <action>Cambiar el estado de la historia:</action>
    <action>- De: "Borrador (PO)" o "{{estado_anterior}}"</action>
    <action>- A: "Analizado (Arquitecto)"</action>
    
    <action>Actualizar metadata:</action>
    <action>- Estado del análisis arquitectónico: "Completado"</action>
    <action>- Agregar entrada en Registro de Cambios:</action>
    
    <example>
| {{date}} | 1.1 | Análisis arquitectónico completado | {{user_name}} (Arquitecto) |
    </example>
    
    <action>Guardar cambios en el archivo</action>
  </substep>
</step>

<step n="5" goal="Confirmación Final y Entrega">
  <message>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ **ANÁLISIS ARQUITECTÓNICO COMPLETADO**

La historia ha sido actualizada con las decisiones arquitectónicas aprobadas.

📄 **Archivo:** {{ruta_archivo}}
📊 **Estado:** Analizado (Arquitecto) - Listo para refinamiento técnico

---

### 📋 PRÓXIMOS PASOS

El **Developer** debe usar el workflow de **refinamiento técnico** para:
1. Descomponer en tareas basadas en los "Hitos de Implementación"
2. Identificar archivos específicos y tests concretos
3. Preparar para desarrollo

---

¿Deseas revisar la historia actualizada o tienes algún comentario adicional?

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  </message>
  
  <ask optional="true">Esperar confirmación final del arquitecto o comentarios adicionales</ask>
</step>

<note title="Contexto del Workflow">
Este workflow es el puente entre el trabajo del PO (requerimientos funcionales) y el Developer (tasking técnico). La calidad del análisis arquitectónico impacta directamente la eficiencia del desarrollo posterior.
</note>

<note title="Separación de Responsabilidades">
- **Arquitecto (este workflow):** Decisiones de diseño, patrones arquitectónicos, componentes afectados, hitos de implementación (alto nivel). Opcionalmente: notas técnicas útiles que surgieron en el análisis
- **Developer (refinamiento posterior):** Identificación de archivos específicos, descomposición en tareas técnicas granulares, estimación de esfuerzo (horas/días), implementación del código
</note>

<note title="NO Incluir Estimaciones de Tiempo">
Este workflow se enfoca EXCLUSIVAMENTE en arquitectura y diseño. Las estimaciones de tiempo/esfuerzo (ej: 2h, 0.5 días) son responsabilidad del Developer en la fase de refinamiento y tasking. El Arquitecto define QUÉ componentes, en QUÉ orden, con QUÉ dependencias. El Developer define archivos específicos, tests concretos y CUÁNTO tiempo tomará cada tarea.
</note>

<note title="Salidas Críticas para Tasking">
La sección "Hitos de Implementación" está específicamente diseñada para que el Developer la use como base para crear tareas técnicas. Cada hito define el componente y sus dependencias arquitectónicas. El Developer tomará esta secuencia y la convertirá en tareas concretas con archivos específicos y estimaciones.
</note>

<note title="Validación y Calidad">
La validación humana del arquitecto (paso 3) NO es negociable - previene retrabajos costosos durante desarrollo. Es mejor iterar en diseño arquitectónico antes de escribir código que refactorizar después. La iteración basada en feedback garantiza que las decisiones finales sean sólidas y consensuadas.
</note>

</workflow>
