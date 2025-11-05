# Explorar Proyecto - Instructions

<workflow>

<critical>The workflow execution engine is governed by: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {project-root}/.ceiba-metodo/metodo-ceiba/workflows/arquitectura/explorar-proyecto/workflow.yaml</critical>
<critical>Comunica todas las respuestas en {communication_language} y adapta el lenguaje a {user_skill_level}</critical>
<critical>Genera todos los documentos en {document_output_language}</critical>

<critical>REGLAS DE SALIDA DEL TEMPLATE: (1) Remover comentarios HTML de secciones completadas, mantener para secciones vacías/opcionales. (2) Asegurar que TODOS los elementos del template (títulos, encabezados, etiquetas) coincidan con {document_output_language}, no solo el contenido.</critical>


<step n="0" goal="CAPTURA DE INTENCIÓN DE EXPLORACIÓN">
<action>**🎯 OBJETIVO: Entender qué quiere explorar el usuario**</action>
<step n="0.1" goal="Solicitar Tema de Exploración">
<ask>Realizar la siguiente consulta al usuario:

**¡Hola! Soy tu asistente. Te ayudo a encontrar y entender cualquier aspecto del proyecto de manera rápida y completa.**

**¿Qué quieres explorar hoy?**

Puedes preguntarme sobre:

🏗️ **Arquitectura y Componentes:**
- "¿Cómo está estructurado el sistema?"
- "¿Qué componentes maneja el módulo X?"
- "¿Cuáles son las dependencias entre módulos?"

📋 **Funcionalidades y Procesos:**
- "¿Cómo funciona el proceso de login?"
- "¿Qué hace el sistema de pagos?"
- "¿Cuáles son los flujos de usuario para X?"

💻 **Código y Implementación:**
- "¿Dónde está implementada la funcionalidad Y?"
- "¿Qué archivos están relacionados con Z?"
- "¿Cómo se conecta el frontend con el backend?"

📖 **Documentación y Configuración:**
- "¿Qué documentación existe sobre X?"
- "¿Cómo está configurado el proyecto?"
- "¿Dónde están definidas las reglas de negocio?"

**Escribe tu pregunta o tema de interés:**</ask>

<template-output>step0_1_exploration_request</template-output>
</step>

<step n="0.2" goal="Clasificar Tipo de Consulta">
<action>Analizar automáticamente el tipo de consulta basado en la respuesta del usuario:

**Tipos de consulta identificados:**
- **🏗️ Arquitectura:** Estructura, componentes, módulos, diagramas
- **📋 Funcional:** Procesos, flujos, reglas de negocio, historias
- **💻 Técnico:** Código, implementación, APIs, integraciones
- **📖 Documentación:** Guías, manuales, configuración, estándares
- **🔍 General:** Exploración amplia o múltiples aspectos

Documentar el tipo identificado para orientar la estrategia de búsqueda.</action>

<template-output>step0_2_query_classification</template-output>
</step>

<step n="0.3" goal="Refinar Pregunta si es Necesario">
<action if="consulta_vaga_o_ambigua">Si la pregunta es muy vaga o ambigua, solicitar clarificación:

**Tu pregunta es muy interesante. Para darte la mejor respuesta, necesito un poco más de contexto:**

**Tu pregunta:** [pregunta original]

**¿Podrías especificar más sobre:**
- ¿Qué aspecto específico te interesa más? (técnico, funcional, arquitectónico)
- ¿Es para entender, modificar, o documentar algo?
- ¿Hay algún módulo o área en particular?

**Por ejemplo:**
- En lugar de "¿Cómo funciona el sistema?" → "¿Cómo funciona el proceso de autenticación?"
- En lugar de "¿Dónde está el código?" → "¿Dónde está el código del carrito de compras?"

**Pregunta refinada:**</action>

<template-output>step0_3_refined_question</template-output>
</step>

</step>

<step n="1" goal="CARGA OBLIGATORIA DE CONFIGURACIÓN Y CONTEXTO">
<action>**🛑 PASO OBLIGATORIO - Ejecutar antes de cualquier búsqueda**</action>
<step n="1.1" goal="Cargar Configuración del Proyecto">
<action>Ejecutar carga obligatoria de configuración:

**OBLIGATORIO:**
- Cargar `{project-root}/.ceiba-metodo/metodo-ceiba/config.yaml`
- Extraer todas las configuraciones de ubicaciones:
  - `architecture_sharded_location` (documentación arquitectónica)
  - `dev_story_location` (historias de usuario)
  - `qa_location` (documentación de QA)
  - Otras ubicaciones configuradas

**Si no existe configuración:**
- HALT: "Configuración no encontrada. Ejecuta la instalación del core primero."</action>

<template-output>step1_1_config_loaded</template-output>
</step>

</step>

<step n="2" goal="BÚSQUEDA INTELIGENTE BASADA EN TIPO DE CONSULTA">
<action>**🔍 ESTRATEGIA DE BÚSQUEDA ADAPTATIVA según el tipo identificado**</action>
<step n="2.1" goal="Búsqueda para Consultas de ARQUITECTURA">
<action if="query_type_arquitectura">Para consultas de arquitectura (🏗️):

**Fuentes prioritarias:**
1. **Documentación arquitectónica** (`{architecture_sharded_location}/**`)
2. **Diagramas y diseños** (archivos con 'architecture', 'design', 'diagram')
3. **Configuración de proyecto** (package.json, core-config.yaml)
4. **Estructura de carpetas** (análisis de organización)

**Búsquedas específicas:**
- Términos: arquitectura, componentes, módulos, servicios, capas, patrones
- Archivos: `architecture-*.md`, `index.md`, `*-design.md`
- Configuraciones: dependencias, estructura de proyecto</action>

<template-output>step2_1_architecture_search</template-output>
</step>

<step n="2.2" goal="Búsqueda para Consultas FUNCIONALES">
<action if="query_type_funcional">Para consultas funcionales (📋):

**Fuentes prioritarias:**
1. **Historias de usuario** (`{dev_story_location}/**`)
2. **Flujos de negocio** (`flujo-*.md`, `*-flow.md`)
3. **Documentación de procesos** (docs/processes/, workflows/)
4. **Reglas de negocio** (business-rules, validations)
5. **Código fuente** (src/, components/, lib/)
6. **APIs y servicios** (api/, services/, controllers/)

**Búsquedas específicas:**
- Términos relacionados con la funcionalidad consultada
- Actores y roles involucrados
- Criterios de aceptación y validaciones
- Flujos de trabajo y procesos</action>

<template-output>step2_2_functional_search</template-output>
</step>

<step n="2.3" goal="Búsqueda para Consultas TÉCNICAS">
<action if="query_type_tecnico">Para consultas técnicas (💻):

**Fuentes prioritarias:**
1. **Código fuente** (src/, components/, lib/)
2. **APIs y servicios** (api/, services/, controllers/)
3. **Configuración técnica** (webpack, babel, tsconfig, etc.)
4. **Documentación técnica** (tech-docs/, api-docs/)

**Búsquedas específicas:**
- Nombres de archivos relacionados
- Funciones, clases, y métodos
- Integraciones y conexiones
- Configuraciones técnicas</action>

<template-output>step2_3_technical_search</template-output>
</step>

<step n="2.4" goal="Búsqueda para Consultas de DOCUMENTACIÓN">
<action if="query_type_documentacion">Para consultas de documentación (📖):

**Fuentes prioritarias:**
1. **Documentación general** (docs/, README.md)
2. **Guías y manuales** (guides/, manuals/, how-to/)
3. **Configuración y setup** (installation, configuration)
4. **Estándares y convenciones** (coding-standards, conventions)</action>

<template-output>step2_4_documentation_search</template-output>
</step>

<step n="2.5" goal="Búsqueda GENERAL">
<action if="query_type_general">Para consultas generales (🔍):

**Ejecutar búsqueda combinada en todas las fuentes:**
- Documentación arquitectónica
- Historias de usuario
- Código fuente
- Documentación general
- Configuraciones

**Priorizar por relevancia** según los términos de búsqueda.</action>

<template-output>step2_5_general_search</template-output>
</step>

</step>

<step n="3" goal="EJECUCIÓN DE BÚSQUEDA Y ANÁLISIS">
<action>**🎯 BÚSQUEDA SISTEMÁTICA en fuentes identificadas**</action>
<step n="3.1" goal="Búsqueda Primaria">
<action>Para cada fuente identificada en el paso 2:

1. **Buscar archivos relevantes** usando nombres y patrones
2. **Buscar contenido específico** usando términos de la consulta
3. **Identificar conexiones** entre documentos
4. **Extraer información clave** de cada fuente encontrada</action>

<template-output>step3_1_primary_search</template-output>
</step>

<step n="3.2" goal="Análisis de Dependencias">
<action>Identificar relaciones entre la información encontrada:

- Referencias entre documentos
- Dependencias entre componentes
- Flujos que conectan múltiples módulos
- Configuraciones que afectan múltiples áreas</action>

<template-output>step3_2_dependencies_analysis</template-output>
</step>

<step n="3.3" goal="Detección de Gaps">
<action>Identificar información faltante:

- Documentación mencionada pero no encontrada
- Código sin documentación asociada
- Procesos parcialmente documentados
- Referencias rotas o desactualizadas</action>

<template-output>step3_3_gaps_detected</template-output>
</step>

</step>

<step n="4" goal="RESPUESTA ESTRUCTURADA Y COMPLETA">
<action>**📝 PRESENTACIÓN DE RESULTADOS de manera organizada**</action>
<step n="4.1" goal="Generar Respuesta Principal">
<action>Crear respuesta estructurada basada en el análisis realizado:

**Estructura de respuesta:**

"**🔍 EXPLORACIÓN COMPLETADA: [Tema Consultado]**

**📋 RESUMEN EJECUTIVO:**
[Respuesta directa y clara a la pregunta original]

**🏗️ INFORMACIÓN ENCONTRADA:**

**1. [Categoría Principal]**

- **📁 Fuente:** `[archivo o ubicación]`
- **📝 Contenido:** [información relevante extraída]
- **🔗 Ubicación:** [ruta específica/líneas si aplica]

**2. [Categoría Secundaria]**

- **📁 Fuente:** `[archivo o ubicación]`
- **📝 Contenido:** [información relevante extraída]
- **🔗 Ubicación:** [ruta específica/líneas si aplica]

[Repetir para todas las categorías encontradas]

**🔗 CONEXIONES IDENTIFICADAS:**

- [Cómo se relaciona con otros componentes/procesos]
- [Dependencias importantes]
- [Flujos de interacción]
</action>

<template-output>step4_1_structured_response</template-output>
</step>

<step n="4.2" goal="Detección de Gaps y Sugerencias">
<action>**Si hay información faltante:**

"**⚠️ GAPS DE INFORMACIÓN DETECTADOS:**

**📋 Información Mencionada pero No Encontrada:**

- [Item A]: Mencionado en [ubicación] pero sin documentación detallada
- [Item B]: Referenciado en código pero sin documentación

**💡 SUGERENCIAS:**

- Revisar [ubicación específica] para más detalles sobre [aspecto]
- Consultar con el equipo sobre [tema no documentado]
- Considerar crear documentación para [área faltante]"
</action>

<template-output>step4_2_gaps_and_suggestions</template-output>
</step>

<step n="4.3" goal="Ofrecer Seguimiento Interactivo">
<ask>**Preguntas de seguimiento:**

"**🤔 ¿HAY ALGO MÁS QUE QUIERAS EXPLORAR?**

**Puedes preguntarme sobre:**

- Detalles específicos de [componentes mencionados]
- Implementación técnica de [procesos identificados]
- Otras funcionalidades relacionadas con [tema principal]
- Cualquier aspecto que no haya quedado claro

**¿Qué más te gustaría saber sobre el proyecto?**"</ask>

## Reglas Obligatorias

1. **CARGA COMPLETA:** Siempre cargar configuración antes de buscar
2. **BÚSQUEDA EXHAUSTIVA:** Revisar todas las fuentes relevantes identificadas
3. **RESPUESTA ESTRUCTURADA:** Presentar información de manera organizada
4. **REFERENCIAS ESPECÍFICAS:** Incluir ubicaciones exactas de la información
5. **DETECTAR GAPS:** Identificar información faltante o inconsistente
6. **SEGUIMIENTO:** Ofrecer opciones de exploración adicional
7. **CONTEXTUALIZAR:** Explicar cómo se relaciona con el ecosistema general
8. **ADAPTARSE:** Ajustar profundidad según el tipo de consulta

## Criterios de Éxito

- **✅ Pregunta Respondida:** La consulta original está completamente respondida
- **✅ Fuentes Identificadas:** Se encontraron y revisaron las fuentes relevantes
- **✅ Información Completa:** Se extrajo toda la información disponible relacionada
- **✅ Referencias Precisas:** Se proporcionaron ubicaciones específicas
- **✅ Contexto Proporcionado:** Se explicaron las relaciones y dependencias
- **✅ Gaps Identificados:** Se detectó información faltante si la hay
- **✅ Seguimiento Ofrecido:** Se facilitaron opciones para exploración adicional
- **✅ Respuesta Estructurada:** La información se presentó de manera clara y organizada

<template-output>step4_3_follow_up_offered</template-output>
</step>

</step>

</workflow>