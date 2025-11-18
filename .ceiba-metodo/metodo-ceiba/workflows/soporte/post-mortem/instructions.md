# WORKFLOW: Documentar Incidente (Post-Mortem)

## Critical Headers

**Workflow Name**: Documentar Incidente (Post-Mortem)  
**Agent Role**: Architect  
**Variables Required**: `incident_file`, `incident_id`, `severity`  
**Templates Used**: `post-mortem-template.md`, `kb-criteria.md`

---

## PURPOSE

Realizar análisis post-mortem de un incidente resuelto, documentar aprendizajes, y evaluar si debe incluirse en la Knowledge Base para facilitar búsqueda y reutilización por el equipo.

---

## INSTRUCTIONS

<workflow>
  <critical>The workflow execution engine is governed by: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
  <critical>You MUST have already loaded and processed: {installed_path}/workflow.yaml</critical>
  <critical>ALWAYS communicate STRICTLY in {communication_language} regardless of the language used by the user</critical>
  <critical>HTML COMMENTS HANDLING: El template contiene comentarios HTML como guías. Al generar el output final, REMOVER comentarios HTML de secciones completas y MANTENER comentarios de secciones vacías/incompletas</critical>
  <critical>Generate all documents in {document_output_language}</critical>
  <critical>NUNCA modificar archivos template - deben permanecer inmutables para reutilización</critical>
  <step id="0" name="Validar Pre-requisitos del Flujo">
    <action type="validate">
      CRÍTICO: Antes de iniciar el post-mortem, valida que:
      1. El archivo del incidente existe y es accesible
      2. El incidente haya completado todos los pasos obligatorios
      
      **Validación 0.1: Existencia del Archivo**
      - Verificar que el archivo `{{incident_file}}` existe
      - Si NO existe → HALT con mensaje de error indicando la ruta incorrecta
      
      **Validación 0.2: Leer Contenido del Incidente**
      Lee el archivo `{{incident_file}}` y verifica:
      
      1. **Status del Incidente**:
         - Busca el campo "Status:" en la sección 6 (Implementación)
         - Busca el campo "Status:" en la sección 7 (Revisión por Pares)
         - Verificar que ambos sean "Completado"
      
      2. **Sección 6 - Implementación (OBLIGATORIO)**:
         - Status debe ser "Completado" (no "Pendiente")
         - Código del Fix Implementado debe estar documentado
         - Tests Unitarios deben estar documentados
         - Pull Request debe existir
      
      3. **Sección 7 - Revisión por Pares (OBLIGATORIO)**:
         - Status debe ser "Completado" (no "Pendiente")
         - Decisión debe ser "PASS" o "CONCERNS" (NO "FAIL")
         - Quality Gate Validation debe estar marcada
      
      4. **Pasos Opcionales (si aplicaron)**:
         - Si Sección 3 existe, Status debe ser "No Aplica" o "Completado" (no "Pendiente")
         - Si Sección 5 existe, Status debe ser "No Aplica" o "Completado" (no "Pendiente")
    </action>
    
    <check if="incident_file_not_found">
      <action type="halt">
        🚫 **ARCHIVO DE INCIDENTE NO ENCONTRADO**
        
        No se pudo acceder al archivo del incidente en la ruta especificada:
        `{{incident_file}}`
        
        **Verifica que**:
        1. La ruta del archivo es correcta
        2. El archivo existe en el sistema de archivos
        3. Tienes permisos de lectura sobre el archivo
        
        **Ejemplo de ruta correcta**:
        - `docs/incidents/042.incident.md`
        - `d:/swat/proyectos/chat/metodo-ceiba/docs/incidents/042.incident.md`
        
        Corrige la ruta del archivo e intenta nuevamente.
      </action>
      <halt>Workflow detenido - Archivo no encontrado</halt>
    </check>
    
    <check if="seccion_6_status != 'Completado' OR seccion_7_status != 'Completado' OR seccion_7_decision == 'FAIL'">
      <action type="halt">
        🚫 **PRE-REQUISITOS NO CUMPLIDOS PARA POST-MORTEM**
        
        El incidente no ha completado los pasos obligatorios del flujo de soporte.
        
        Problemas detectados:
        {{listar_problemas_detectados}}
        
        **Acción requerida**:
        1. Si la Sección Implementación está incompleta:
           → Ejecutar comando: *desarrollar-historia-usuario
        
        2. Si la Revisión por Pares está incompleta:
           → Ejecutar comando: *revisar-historia
        
        3. Si la Revisión obtuvo "FAIL":
           → El código debe ser corregido y re-implementado antes del post-mortem
           → Volver a la Implementación
        
        Una vez completados todos los pasos obligatorios, podrás ejecutar el Post-Mortem.
      </action>
      <halt>Workflow detenido - Pre-requisitos no cumplidos</halt>
    </check>
    
    <check if="seccion_6_status == 'Completado' AND seccion_7_status == 'Completado' AND (seccion_7_decision == 'PASS' OR seccion_7_decision == 'CONCERNS')">
      <action type="inform">
        ✅ Pre-requisitos validados correctamente.
        
        Verificado:
        - ✅ Implementación completada (Sección 6)
        - ✅ Revisión por pares aprobada (Sección 7: {{seccion_7_decision}})
        - ✅ Incidente listo para Post-Mortem
        
        Continuando con el análisis post-mortem...
      </action>
    </check>
  </step>

  <step id="1" name="Leer Incidente Completo">
    <action type="read">
      Lee el archivo completo del incidente en `{{incident_file}}` para obtener contexto de las secciones 1-7 ya documentadas.
    </action>
    <ask>
      ¿Qué información ya está documentada en las secciones anteriores del incidente?
    </ask>
  </step>

  <step id="2" name="Construir Timeline del Incidente">
    <action type="analyze">
      Extrae TODOS los timestamps y eventos del incidente desde las secciones 1-7.
      
      **Eventos OBLIGATORIOS** (deben extraerse):
      - Reported Date (sección 1.1)
      - Fecha de Diagnóstico (sección 2)
      - Fecha de Refinamiento (sección 4)
      - Fecha de inicio de Implementación (sección 6)
      - Fecha de Code Review (sección 7)
      - Fecha de Deploy/Validación (sección 7 - si está disponible)
      
      **Eventos OPCIONALES** (extraer si existen):
      - Fecha de Análisis y Diseño (sección 3 - verificar si Status != "No Aplica")
      - Fecha de Estimación (sección 5 - verificar si Status != "No Aplica")
      
      **Validación**:
      1. Para secciones 3 y 5: Leer el Status de cada sección
      2. Si Status == "Completado" → Extraer fecha y añadir al timeline
      3. Si Status == "No Aplica" o "Pendiente" → No incluir en timeline
      
      Construye una tabla cronológica completa con columnas: Timestamp | Evento | Responsable
      
      Ordena TODOS los eventos cronológicamente (de más antiguo a más reciente).
    </action>
    <template-output name="timeline_events">
      Genera la tabla de timeline en formato Markdown con todos los eventos disponibles.
    </template-output>
  </step>

  <step id="3" name="Analizar What Went Wrong">
    <action type="reflect">
      Identifica todos los errores, fallas de proceso, y decisiones incorrectas que contribuyeron al incidente:
      - ¿Qué procesos fallaron? (ej: code review no detectó breaking changes)
      - ¿Qué herramientas faltaron? (ej: no había tests end-to-end)
      - ¿Qué decisiones fueron incorrectas? (ej: deploy sin validación suficiente)
      - ¿Qué comunicación falló? (ej: alertas tardías)
      
      Para cada falla, documenta el impacto y la relación con la causa raíz.
    </action>
    <template-output name="what_went_wrong">
      Lista numerada de fallas con descripción e impacto.
    </template-output>
  </step>

  <step id="4" name="Analizar What Went Right">
    <action type="reflect">
      Identifica aspectos positivos en el manejo del incidente:
      - ¿Qué procesos funcionaron bien? (ej: escalación rápida a P1)
      - ¿Qué decisiones fueron acertadas? (ej: uso de 5 Whys para diagnóstico)
      - ¿Qué herramientas ayudaron? (ej: monitoreo identificó error rápidamente)
      - ¿Qué comunicación fue efectiva? (ej: updates regulares en Slack)
      
      Reconoce aciertos para replicarlos en futuros incidentes.
    </action>
    <template-output name="what_went_right">
      Lista numerada de aciertos con descripción.
    </template-output>
  </step>

  <step id="5" name="Extraer Lessons Learned">
    <action type="synthesize">
      Basándote en "What Went Wrong" y "What Went Right", identifica lecciones clave aprendidas:
      - Conclusiones generalizables más allá de este incidente específico
      - Patrones que pueden aplicarse a otros contextos
      - Cambios de mindset o proceso necesarios
      
      Cada lección debe ser actionable y específica (no vaga como "mejorar testing").
    </action>
    <template-output name="lessons_learned">
      Lista numerada de lecciones con explicación detallada.
    </template-output>
  </step>

  <step id="6" name="Definir Medidas Preventivas">
    <action type="plan">
      Para cada lección aprendida, define medidas concretas para prevenir recurrencia:
      - Cambios en procesos (ej: nuevo checklist en code review)
      - Herramientas a implementar (ej: nuevos tests, alertas)
      - Políticas a establecer (ej: dependency management policy)
      - Training necesario para el equipo
      
      Cada medida debe tener:
      - Descripción clara
      - Responsable de implementación
      - Fecha objetivo de implementación
    </action>
    <template-output name="preventive_measures">
      Lista numerada de medidas con descripción, responsable y fecha.
    </template-output>
  </step>

  <step id="7" name="Evaluar Elegibilidad para Knowledge Base">
    <action type="evaluate">
      Lee el archivo `kb-criteria.md` para entender los criterios de inclusión en KB.
      
      Evalúa si el incidente cumple AL MENOS UNO de estos criterios:
      - ✅ Incidente P0-P1 (obligatorio)
      - ✅ Error recurrente o con patrón común
      - ✅ Solución compleja o no obvia
      - ✅ Aprendizaje valioso para el equipo
      - ✅ Aplicable a otros contextos
      
      Si cumple criterios, determina:
      - Categoría KB (Seguridad, Rendimiento, Integración, etc.)
      - Tipo de Error (Código, Datos, Infraestructura, etc.)
      - Etiquetas para búsqueda (mínimo 3, máximo 7)
      - Palabras clave searchables
    </action>
    <template-output name="kb_eligible">
      Respuesta: SÍ o NO con justificación.
    </template-output>
    <check if="kb_eligible == 'SÍ'">
      <template-output name="kb_category">
        Categoría principal para KB.
      </template-output>
      <template-output name="kb_tags">
        Lista de etiquetas en formato #tag separadas por espacio.
      </template-output>
    </check>
  </step>

  <step id="8" name="Documentar Post-Mortem en Incidente">
    <sub-step id="8.0" name="Leer y Validar Template">
      <action type="read">
        Lee el archivo template `post-mortem-template.md` para obtener la estructura de la sección 8.
        
        **Validaciones del Template**:
        1. Verificar que el archivo existe en `{installed_path}/post-mortem-template.md`
        2. Identificar todas las secciones requeridas (8.1 a 8.6)
        3. Identificar todas las variables a reemplazar (ej: {{timeline_events}}, {{kb_eligible}}, etc.)
        4. Validar estructura de markdown del template
        
        Si el template NO existe o está corrupto → HALT con error.
      </action>
    </sub-step>
    
    <action type="write">
      Actualiza el archivo `{{incident_file}}` añadiendo la sección 8 completa usando el template `post-mortem-template.md`:
      
      **Sección 8.1**: Timeline del Incidente (usar `{{timeline_events}}`)
      **Sección 8.2**: What Went Wrong (usar `{{what_went_wrong}}`)
      **Sección 8.3**: What Went Right (usar `{{what_went_right}}`)
      **Sección 8.4**: Lessons Learned (usar `{{lessons_learned}}`)
      **Sección 8.5**: Knowledge Base (solo si `{{kb_eligible}} == SÍ`)
      **Sección 8.6**: Medidas Preventivas (usar `{{preventive_measures}}`)
      
      **Procesamiento del Template**:
      1. Procesar el template reemplazando todas las variables con valores generados en steps anteriores
      2. Si `{{kb_eligible}} == NO` → Omitir completamente la sección 8.5
      3. Si `{{kb_eligible}} == SÍ` → Completar sección 8.5 con:
         - KB Metadata (categoría, etiquetas, keywords)
         - Descripción del Problema
         - Causa Raíz Documentada (copiar análisis 5 Whys)
         - Solución Implementada (código clave)
         - Resultados y Validación
         - Aprendizajes y Prevención
         - Aplicabilidad a Otros Contextos
      
      **Validación Post-Escritura**:
      - Verificar que la sección 8 se añadió correctamente al archivo
      - Verificar que todas las sub-secciones están presentes
      - Si kb_eligible == SÍ, verificar que sección 8.5 existe con anchor válido
    </action>
  </step>


  <step id="9" name="Actualizar Índices de Knowledge Base">
    <action type="conditional-execute">
      Este paso actualiza los índices de Knowledge Base si el incidente fue evaluado como elegible en el Step 7.
      
      **Lógica Condicional**:
      - Usar la variable `{{kb_eligible}}` generada en el Step 7
      - Si `{{kb_eligible}} == "SÍ"` → Ejecutar sub-pasos 9.1 a 9.5 (actualización de índices)
      - Si `{{kb_eligible}} == "NO"` → Saltar a Step 10 (sin actualización)
    </action>
    
    <check if="kb_eligible == 'NO'">
      <action type="inform">
        ⏭️ **Actualización de KB saltada**
        
        El incidente no cumple criterios para Knowledge Base según evaluación del Step 7.
        Los índices de KB no requieren actualización.
        
        Continuando con cierre de incidente...
      </action>
    </check>
    
    <check if="kb_eligible == 'SÍ'">
      <sub-step id="9.1" name="Verificar y Crear Estructura de KB">
        <action type="validate-and-create">
          Verificar estructura de Knowledge Base y crear archivos si no existen:
          
          1. **Verificar directorio KB**: `{{kb_index_location}}`
             - Si NO existe → Crear directorio
          
          2. **Verificar index-by-category.md**:
             - Si NO existe → Crear usando template `index-by-category-template.md`
             - Si SÍ existe → Continuar
          
          3. **Verificar index-by-tags.md**:
             - Si NO existe → Crear usando template `index-by-tags-template.md`
             - Si SÍ existe → Continuar
        </action>
      </sub-step>
      
      <sub-step id="9.2" name="Extraer Metadata del Incidente">
        <action type="extract">
          Lee la sección 8.5 (Knowledge Base) del archivo `{{incident_file}}` y extrae:
          
          - **incident_id**: `{{incident_id}}`
          - **incident_title**: Extraer título del incidente (sin el prefijo "# INCIDENTE-XXX:")
          - **severity**: `{{severity}}`
          - **category**: Buscar "Categoría:" en KB Metadata de sección 8.5
          - **error_type**: Buscar "Tipo de Error:" en KB Metadata de sección 8.5
          - **tags**: Buscar "Etiquetas:" en KB Metadata de sección 8.5 (lista de #tags)
          - **incident_path**: Path relativo al archivo del incidente desde `{{kb_index_location}}`
        </action>
      </sub-step>
      
      <sub-step id="9.3" name="Actualizar index-by-category.md">
        <action type="write">
          Actualizar archivo `{{kb_index_location}}/index-by-category.md`:
          
          1. Abrir el archivo
          2. Buscar la sección correspondiente a `{{category}}` (ej: "## Código")
          3. En la tabla de esa categoría, añadir nueva fila:
          
          **Formato**:
          ```
          | {{incident_id}} | {{incident_title}} | {{severity}} | {{error_type}} | {{tags}} | [Link]({{incident_path}}#85-knowledge-base) |
          ```
          
          4. Actualizar "Última actualización" con fecha actual
          5. Incrementar "Total de casos"
          6. Guardar archivo
        </action>
      </sub-step>
      
      <sub-step id="9.4" name="Actualizar index-by-tags.md">
        <action type="write">
          Actualizar archivo `{{kb_index_location}}/index-by-tags.md`:
          
          Para CADA tag en `{{tags}}`:
          
          1. Abrir el archivo
          2. Determinar la letra inicial del tag (sin #)
          3. Buscar la sección de esa letra (ej: "## N" para #nullpointerexception)
          4. Buscar la sub-sección del tag específico (ej: "### #nullpointerexception")
             - Si NO existe → Crearla en orden alfabético bajo la letra correspondiente
          5. Añadir entrada bajo ese tag:
          
          **Formato**:
          ```
          - [{{incident_id}} - {{incident_title}}]({{incident_path}}#85-knowledge-base) ({{severity}})
          ```
          
          6. Después de procesar todos los tags:
             - Actualizar "Última actualización" con fecha actual
             - Incrementar "Total de etiquetas únicas" si se crearon nuevas
             - Incrementar "Total de casos"
          7. Guardar archivo
        </action>
      </sub-step>
      
      <sub-step id="9.5" name="Validar Integridad de Índices Actualizados">
        <action type="validate">
          CRÍTICO: Validar que las actualizaciones de índices se aplicaron correctamente.
          
          **Validaciones Obligatorias**:
          
          1. **Validar index-by-category.md**:
             - Leer el archivo
             - Buscar la sección de categoría `{{category}}`
             - Verificar que existe una entrada con `{{incident_id}}`
             - Verificar que el link `{{incident_path}}#85-knowledge-base` está presente
             - Verificar que el formato de la fila es correcto
          
          2. **Validar index-by-tags.md**:
             - Para CADA tag en `{{tags}}`:
               * Buscar la sección del tag
               * Verificar que existe entrada con `{{incident_id}}`
               * Verificar que el link está presente
          
          3. **Validar Archivo del Incidente**:
             - Verificar que la sección 8.5 existe en `{{incident_file}}`
             - Verificar que el anchor `#85-knowledge-base` es válido
          
          4. **Contador de Errores**:
             - Si TODAS las validaciones pasan → Continuar
             - Si ALGUNA validación falla → Reportar error y HALT workflow
        </action>
        
        <check if="validation_failed">
          <action type="halt">
            🚫 **ERROR EN ACTUALIZACIÓN DE ÍNDICES KB**
            
            Se detectaron problemas al actualizar los índices de Knowledge Base:
            
            {{validation_errors_list}}
            
            **Acciones requeridas**:
            1. Revisar manualmente los archivos de índices
            2. Corregir las entradas faltantes o malformadas
            3. Verificar que el archivo del incidente tiene sección 8.5
            
            **Archivos afectados**:
            - `{{kb_index_location}}/index-by-category.md`
            - `{{kb_index_location}}/index-by-tags.md`
            - `{{incident_file}}`
            
            El workflow se detiene para prevenir inconsistencias en la Knowledge Base.
          </action>
          <halt>Workflow detenido - Validación de índices KB falló</halt>
        </check>
        
        <check if="validation_passed">
          <action type="inform">
            ✅ **Validación de Índices KB Exitosa**
            
            Todas las validaciones pasaron:
            - ✅ Entrada en index-by-category.md verificada
            - ✅ Entradas en index-by-tags.md verificadas ({{tags_count}} tags)
            - ✅ Links funcionando correctamente
            - ✅ Formato de entradas correcto
            
            Los índices están consistentes y el incidente es searchable.
          </action>
        </check>
      </sub-step>
      
      <action type="inform">
        ✅ **Índices de KB actualizados correctamente**
        
        Incidente {{incident_id}} añadido a:
        - `index-by-category.md` en categoría: {{category}}
        - `index-by-tags.md` bajo {{tags_count}} etiquetas: {{tags}}
        
        Los índices están actualizados y el caso es searchable en la Knowledge Base.
      </action>
    </check>
  </step>

  <step id="10" name="Notificar Equipo sobre Nuevo Caso en KB">
    <action type="conditional-inform">
      Este step solo se ejecuta si el incidente fue documentado en KB (`{{kb_eligible}} == "SÍ"` del Step 7).
    </action>
    
    <check if="kb_eligible == 'NO'">
      <action type="skip">
        ⏭️ Notificación de KB saltada - El incidente no fue documentado en Knowledge Base.
      </action>
    </check>
    
    <check if="kb_eligible == 'SÍ'">
      <action type="inform">
        Genera mensaje para compartir con el equipo sobre el nuevo caso documentado en KB:
        
        **Datos a extraer de sección 8.5**:
        - Título del incidente
        - Categoría KB
        - Primera lección aprendida (de sección 8.4)
        - Aplicabilidad (de sección 8.5 "Aplicabilidad a Otros Contextos")
        - Etiquetas
        
        **Template de notificación**:
        ```
        📚 *Nuevo caso en Knowledge Base*
        
        *ID*: {{incident_id}} | *Severidad*: {{severity}} | *Categoría*: {{category}}
        
        *Título*: {{incident_title}}
        
        *Aprendizaje clave*:
        > {{primera_leccion_aprendida}}
        
        *Aplicable a*: {{aplicabilidad_resumen}}
        
        *Tags*: {{tags}}
        
        🔗 [Ver caso completo]({{incident_path}}#85-knowledge-base)
        ```
      </action>
      <template-output name="kb_notification">
        Mensaje formateado para Slack o sistema de comunicación del equipo.
      </template-output>
    </check>
  </step>


  <step id="11" name="Cerrar Incidente">
    <action type="finalize">
      Actualiza el METADATA del incidente en `{{incident_file}}`:
      - **Status**: Cerrado
      - **Fecha de Cierre**: [Timestamp actual]
      - **Tiempo de Resolución**: [Calcular diferencia entre Reported Date y Fecha de Cierre]
      
      Añade al final del archivo:
      ```
      **Incidente Cerrado**: [Fecha y hora]
      **Closed By**: [Nombre del Architect]
      **Final Status**: [Resumen en una frase del estado final]
      ```
    </action>
  </step>
</workflow>

---

## VALIDATION

Antes de completar este workflow, valida usando `checklist.md`:

- ✅ **PRE-REQUISITOS CUMPLIDOS**:
  - Status de Implementación (Sección 6) = "Completado"
  - Status de Revisión (Sección 7) = "Completado"
  - Decisión de Revisión = "PASS" o "CONCERNS" (NO "FAIL")
- ✅ Timeline completa con todos los eventos
- ✅ What Went Wrong identifica todas las fallas
- ✅ What Went Right reconoce aciertos
- ✅ Lessons Learned son actionables y específicas
- ✅ Medidas Preventivas tienen responsable y fecha
- ✅ Evaluación de KB es justificada (criterios documentados en kb-criteria.md)
- ✅ Timeline incluye eventos de secciones opcionales (3 y 5) si aplicaron
- ✅ Template de post-mortem fue leído y validado (Sub-step 8.0)
- ✅ **VALIDACIÓN CRÍTICA DE ÍNDICES KB** (si kb_eligible = SÍ):
  - ✅ Directorio `{{kb_index_location}}` existe
  - ✅ Archivo `index-by-category.md` existe Y contiene entrada del incidente en categoría correcta
  - ✅ Archivo `index-by-tags.md` existe Y contiene entrada del incidente bajo CADA tag
  - ✅ Sub-step 9.5 (Validación de Integridad) ejecutado y PASÓ
  - ✅ Enlaces a incidente funcionan (anchor #85-knowledge-base existe en archivo del incidente)
  - ✅ Formato de entradas cumple con templates
  - ✅ Fechas "Última actualización" y contadores actualizados
- ✅ Incidente cerrado con metadata actualizado (Status, Closed Date, Closed By)

**Si CUALQUIERA de las validaciones de KB falla**:
- 🚫 El workflow NO debe marcarse como completado
- 🚫 Sub-step 9.5 debe reportar error específico y HALT
- 🚫 Solicitar corrección manual antes de cerrar incidente

---

## STYLE GUIDE

**Instruction Style**: Intent-Based

Confía en el juicio del Architect para:
- Identificar fallas y aciertos relevantes
- Priorizar lecciones aprendidas más valiosas
- Determinar medidas preventivas más efectivas
- Evaluar elegibilidad para KB con criterio

No seas excesivamente prescriptivo en qué eventos incluir o cómo formular lecciones. El Architect conoce el contexto del incidente y puede decidir qué es más valioso documentar.

---

## NOTES

- **Post-Mortem es OBLIGATORIO** para incidentes P0-P1 (verificar en `{{require_post_mortem_p0_p1}}`)
- **Post-Mortem es OPCIONAL** para incidentes P2-P4 (a criterio del Architect)
- La sección 8.5 Knowledge Base NO crea archivo separado, se documenta en el mismo archivo del incidente para mantener trazabilidad completa
- Medidas preventivas deben implementarse después del post-mortem (no es responsabilidad de este workflow implementarlas, solo documentarlas)
- Si el incidente genera múltiples lecciones, priorizar las 3-5 más impactantes
