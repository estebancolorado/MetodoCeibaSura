# Documentar Flujo de Negocio - Instructions

````xml
<critical>The workflow execution engine is governed by: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {project-root}/.ceiba-metodo/metodo-ceiba/workflows/arquitectura/documentar-flujo-negocio/workflow.yaml</critical>
<critical>Comunica todas las respuestas en {communication_language} y adapta el lenguaje a {user_skill_level}</critical>
<critical>Genera todos los documentos en {document_output_language}</critical>

<critical>**Usa esta tarea cuando:** 
  Necesitas documentar un flujo de negocio crítico y complejo
  El flujo involucra múltiples componentes/servicios con interacciones asíncronas
  Requieres clarificar decisiones arquitectónicas o integraciones complejas
  Nuevos desarrolladores necesitan entender workflows clave del sistema
  Buscas documentar rutas de manejo de errores y recuperación
  El flujo incluye APIs externas, message queues o procesos batch
</critical>

<critical>**Prerequisites:** 
  Conocimiento del flujo de negocio a documentar
  Acceso a documentación existente (si está disponible)
  Comprensión de los componentes involucrados en el flujo
  Acceso al GPS arquitectónico del sistema (si existe)
</critical>

<critical>REGLAS DE SALIDA DEL TEMPLATE: (1) Remover comentarios HTML de secciones completadas, mantener para secciones vacías/opcionales. (2) Asegurar que TODOS los elementos del template (títulos, encabezados, etiquetas) coincidan con {document_output_language}, no solo el contenido.</critical>

<workflow>

<step n="0" goal="Configuración y Contexto Inicial">

  <step n="0.1" goal="Cargar Configuración del Proyecto">

    <action>Verificar que existe `{project-root}/.ceiba-metodo/metodo-ceiba/config.yaml`</action>
    <action>Confirmar configuración de `architecture_sharded_location: {architecture_sharded_location}`</action>
    <action if="no existe config">Crear estructura base de documentación</action>
    
    
    <template-output>step0_1_configuracion_proyecto</template-output>

  </step>

  <step n="0.2" goal="Elicitación de Contexto del Flujo">

    <critical>Realizar las siguientes preguntas obligatorias al usuario</critical>
    
    <ask>**Identificación del Flujo:**
    1. ¿Cuál es el nombre/descripción del flujo de negocio a documentar?
    2. ¿Cuáles son los 3-5 componentes principales involucrados en este flujo?</ask>
    
    <ask>**Scope y Complejidad:**
    1. ¿El flujo incluye comunicación asíncrona (message queues, eventos)?
    2. ¿Hay integraciones con sistemas externos en este flujo?</ask>
    
    <ask>**Contexto del Negocio:**
    1. Describe brevemente el proceso de negocio (2-3 líneas máximo)
    2. ¿Cuáles son los puntos críticos donde suelen ocurrir errores?</ask>
    
    <template-output>step0_2_elicitacion_contexto_flujo</template-output>
    
  </step>

</step>

<step n="1" goal="Análisis Profundo del Flujo">

  <step n="1.1" goal="Revisión del Contexto Arquitectónico">

    <critical>**Preparar contexto base:**</critical>

    <action>**Revisar GPS arquitectónico**: Si existe `{architectureShardedLocation}/index.md`, revisar información sobre los componentes involucrados</action>
    <action>**Revisar documentación de componentes**: Para cada componente del flujo, buscar y revisar `{architectureShardedLocation}/architecture-{nombre-componente}.md` si existe para obtener información sobre responsabilidades, patrones de integración, APIs y dependencias</action>
    <action>**Identificar patrones de integración**: Revisar protocolos de comunicación (REST, eventos, DB directa, etc.)</action>
    <action>**Mapear dependencias**: Entender relaciones entre componentes del flujo</action>
    
    <template-output>step1_1_revision_contexto_arquitectonico</template-output>

  </step>

  <step n="1.2" goal="Análisis de Componentes del Flujo">

    <critical>**Para cada componente identificado:**</critical>

    <action>**Rol en el flujo**: Qué responsabilidad tiene en el proceso de negocio</action>
    <action>**Tecnología**: Stack tecnológico específico del componente</action>
    <action>**Interfaces**: APIs, eventos, o mecanismos de comunicación que expone/consume</action>
    <action>**Puntos de falla**: Posibles errores o timeouts específicos del componente</action>
    
    <template-output>step1_2_analisis_componentes_flujo</template-output>

  </step>

  <step n="1.3" goal="Mapeo de Interacciones y Secuencias">

    <critical>**Identificar el flujo paso a paso:**</critical>

    <action>**Trigger inicial**: Qué inicia el flujo (usuario, evento, scheduler, etc.)</action>
    <action>**Secuencia principal**: Pasos del happy path del flujo</action>
    <action>**Decisiones y branches**: Puntos donde el flujo puede tomar diferentes rutas</action>
    <action>**Operaciones asíncronas**: Procesos que no bloquean la ejecución</action>
    <action>**Puntos de persistencia**: Dónde se guardan estados intermedios</action>
    
    <template-output>step1_3_mapeo_interacciones_secuencias</template-output>

  </step>

  <step n="1.4" goal="Análisis de Escenarios de Error">

    <critical>**Evaluar manejo de errores:**</critical>

    <action>**Errores técnicos**: Timeouts, conexiones perdidas, servicios no disponibles</action>
    <action>**Errores de negocio**: Validaciones fallidas, datos inconsistentes</action>
    <action>**Estrategias de recuperación**: Reintentos, compensación, rollbacks</action>
    <action>**Notificaciones**: Cómo se informa al usuario sobre errores</action>

    <template-output>step1_4_analisis_escenarios_error</template-output>

  </step>

</step>

<step n="2" goal="Creación de la Documentación del Flujo">

  <step n="2.1" goal="Crear Archivo de Documentación">
    <critical>**Crear estructura específica del flujo:**
    ```
    {architectureShardedLocation}/
    └── flujo-{nombre-flujo}.md    # Documentación del flujo de negocio
    ```
    </critical>

    <template-output>step2_1_crear_estructura_documentacion</template-output>

  </step>
  
  <step n="2.2" goal="Generar Documentación del Flujo">

    <critical>**Crear documento completo basado en el análisis realizado, usando la plantilla.**</critical>
    <critical>**1. Cargar Plantilla Base**: Leer el contenido del archivo {installed_path}/template.md como plantilla base.</critical>
    <critical>**2. Reemplazar información**: Sustituir la información correspondiente en las variables de las diferentes secciones de la plantilla.</critical>
    <critical>**3. Generar Archivo Final**: crear el archivo final en la ruta {default_output_file}.</critical>
   
    <critical>NO modificar el archivo template.md - es solo para lectura como plantilla</critical>
    <critical>El archivo de salida debe ser completamente nuevo, generado a partir del template</critical>
    <critical>Asegurar que todas las secciones del template estén completas con información relevante</critical>
    <critical>Si alguna sección no aplica al proyecto, mantener la estructura pero indicar "N/A" con explicación</critical>

    <template-output>step2_1_generar_documentacion_flujo</template-output>

  </step>

  <step n="2.3" goal="Validación y Refinamiento">

    <critical>**Revisar la documentación generada:**</critical>

    <action>Verificar que los diagramas Mermaid reflejen correctamente el flujo</action>
    <action>Asegurar que los escenarios de error estén bien documentados</action>
    <action>Validar que la información sea práctica para desarrolladores</action>
    <action>Confirmar que los casos de prueba cubren escenarios críticos</action>

    <critical>**Preguntar al usuario:**</critical>
    
    <ask>¿Los diagramas capturan correctamente las interacciones del flujo?</ask>
    
    <ask>¿Hay algún escenario de error específico que deba agregar?</ask>
    
    <ask>¿La documentación cubre las necesidades de tu equipo para entender este flujo?</ask>
    
    <check if="usuario solicita cambios">
      <action>Realizar ajustes necesarios</action>
      <action>Actualizar documentación final</action>
    </check>
    
    <template-output>step6_1_validacion_y_refinamiento</template-output>

  </step>

</step>

</workflow>
````