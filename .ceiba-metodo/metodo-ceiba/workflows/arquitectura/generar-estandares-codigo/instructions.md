# Generar Estándares de Código - Instructions

<workflow>

<critical>The workflow execution engine is governed by: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {project-root}/.ceiba-metodo/metodo-ceiba/workflows/arquitectura/generar-estandares-codigo/workflow.yaml</critical>
<critical>Comunica todas las respuestas en {communication_language} y adapta el lenguaje a {user_skill_level}</critical>
<critical>Genera todos los documentos en {document_output_language}</critical>
<critical>REGLAS DE SALIDA DEL TEMPLATE: (1) Remover comentarios HTML de secciones completadas, mantener para secciones vacías/opcionales. (2) Asegurar que TODOS los elementos del template (títulos, encabezados, etiquetas) coincidan con {document_output_language}, no solo el contenido.</critical>

<step n="0" goal="CONFIGURACIÓN Y CONTEXTO INICIAL">

<step n="0.1" goal="Cargar Configuración del Proyecto">
<action>Verificar que existe `{root}/config.yaml`</action>
<action>Confirmar configuración de `architectureShardedLocation: {architectureShardedLocation}`</action>
<action if="!architectureShardedLocation">Si no existe, usar `docs/architecture` como ubicación por defecto</action>

<template-output>config_inicial</template-output>
</step>

<step n="0.2" goal="Elicitación Inicial de Contexto">
<ask>Realizar las siguientes preguntas obligatorias al usuario:

**1. Documentación Existente:**
- ¿Existe algún documento de estándares de desarrollo que pueda usar como base?
- ¿Hay algún README, CONTRIBUTING.md, o guía de estilos que deba considerar?

**2. Estándares No Documentados:**
- ¿Hay convenciones o reglas de desarrollo que el equipo sigue pero no están documentadas?
- ¿Existen patrones específicos que quieres que se incluyan obligatoriamente?

**3. Tecnologías y Herramientas:**
- ¿Usan algún linter específico (ESLint, Prettier, SonarQube, etc.)?</ask>

<template-output>contexto_inicial</template-output>
</step>

</step>

<step n="1" goal="ANÁLISIS DE DOCUMENTACIÓN EXISTENTE">

<step n="1.1" goal="Revisar Documentación de Estándares">
<action>Buscar y analizar documentación existente:

- Buscar archivos como `CONTRIBUTING.md`, `README.md`, `.eslintrc`, `prettier.config.js`
- Revisar si existe `{architectureShardedLocation}/coding-standards.md`
- Analizar configuraciones de linters y herramientas de calidad
- Revisar documentación de arquitectura existente si está disponible</action>

<template-output>documentacion_existente</template-output>
</step>

<step n="1.2" goal="Extraer Información Existente">
<action>Documentar hallazgos:

- **Reglas ya documentadas**: Extraer estándares explícitos existentes
- **Configuraciones de herramientas**: Analizar configuraciones de linters, formatters
- **Convenciones implícitas**: Identificar patrones en documentación existente</action>

<template-output>informacion_existente</template-output>
</step>

</step>

<step n="2" goal="ANÁLISIS DEL CÓDIGO BASE">

<step n="2.1" goal="Análisis de Patrones de Código">
<action>Analizar la estructura y patrones del código existente:

- Revisar estructura de directorios y organización de archivos
- Identificar patrones de nomenclatura (variables, funciones, clases, archivos)
- Analizar estilos de comentarios y documentación en código
- Examinar patrones arquitectónicos utilizados (MVC, componentes, servicios)</action>

<template-output>patrones_codigo</template-output>
</step>

<step n="2.2" goal="Análisis de Tecnologías y Herramientas">
<action>Identificar stack tecnológico y herramientas:

- Detectar lenguajes principales y versiones
- Identificar frameworks y librerías principales
- Analizar herramientas de build y testing
- Revisar configuraciones de CI/CD si están disponibles</action>

<template-output>tecnologias_herramientas</template-output>
</step>

<step n="2.3" goal="Identificación de Convenciones">
<action>Extraer convenciones implícitas del código:

- **Nombres**: Patrones de nomenclatura para variables, funciones, clases
- **Estructura**: Organización de archivos y directorios
- **Comentarios**: Estilos de documentación en código
- **Imports**: Convenciones de importación y organización
- **Testing**: Patrones de naming y organización de tests</action>

<template-output>convenciones_codigo</template-output>
</step>

</step>

<step n="3" goal="CONSOLIDACIÓN DE ESTÁNDARES">

<step n="3.1" goal="Combinar Fuentes de Información">
<action>Consolidar información de:

- Documentación existente analizada
- Respuestas del usuario en elicitación
- Patrones identificados en el código base
- Mejores prácticas generales para las tecnologías detectadas</action>

<template-output>consolidacion_fuentes</template-output>
</step>

<step n="3.2" goal="Categorizar Estándares">
<action>Organizar en categorías:

- **Estándares obligatorios**: Reglas críticas que deben seguirse
- **Convenciones recomendadas**: Patrones sugeridos pero no obligatorios
- **Herramientas y configuración**: Setup de linters y formatters
- **Organización**: Estructura de archivos y directorios</action>

<template-output>categorizacion_estandares</template-output>
</step>

</step>

<step n="4" goal="CREACIÓN DEL DOCUMENTO DE ESTÁNDARES">

<step n="4.1" goal="Crear Estructura de Archivo">
<action>Crear el archivo de estándares:

{architectureShardedLocation}/
└── coding-standards.md    # Documento de estándares de código
</action>

<template-output>estructura_archivo</template-output>
</step>

<step n="4.2" goal="Generar `coding-standards.md` - Estándares de Código">
<action>Crear documento completo basado en el análisis realizado, usando la plantilla.

**1. Cargar Plantilla Base**: Leer el contenido del archivo {installed_path}/template.md como plantilla base.

**2. Reemplazar información**: Sustituir la información correspondiente en las variables de las diferentes secciones de la plantilla.

**3. Generar Archivo Final**: crear el archivo final en la ruta {default_output_file}.

**IMPORTANTE**: 
- NO modificar el archivo template.md - es solo para lectura como plantilla
- El archivo de salida debe ser completamente nuevo, generado a partir del template
- Asegurar que todas las secciones del template estén completas con información relevante
- Si alguna sección no aplica al proyecto, mantener la estructura pero indicar "N/A" con explicación</action>

<template-output>documento_estandares</template-output>
</step>

<step n="4.3" goal="Validación y Refinamiento">
<action>Revisar el documento generado:
- Verificar que los estándares reflejen realmente el código existente
- Asegurar que las reglas sean prácticas y aplicables
- Validar que las configuraciones de herramientas sean correctas
- Confirmar que los ejemplos de código sean precisos</action>

<ask>Preguntar al usuario:
- ¿Los estándares capturan correctamente las convenciones del proyecto?
- ¿Hay alguna regla específica que quieras agregar o modificar?
- ¿La configuración de herramientas refleja lo que actualmente usan?</ask>

<action if="user_requests_changes">Aplicar refinamientos solicitados</action>

<template-output>validacion_refinamiento</template-output>
</step>

</step>

<step n="5" goal="ENTREGA Y COMUNICACIÓN">
<action>Generar mensaje final obligatorio:


Estándares de código generados exitosamente.

**Archivo creado:** `{architectureShardedLocation}/coding-standards.md`

**Análisis completado:**
✅ Documentación existente revisada
✅ Código base analizado para identificar patrones
✅ {número} tecnologías principales documentadas
✅ {número} reglas obligatorias definidas
✅ {número} convenciones recomendadas incluidas
✅ Configuración de herramientas especificada

**Contenido generado:**
- Stack tecnológico identificado
- Estándares obligatorios basados en análisis de código
- Convenciones recomendadas del equipo
- Configuración de linters y formatters
- Umbrales de calidad y métricas
- Proceso de actualización definido

**Próximos pasos:**
1. Revisar el documento con el equipo de desarrollo
2. Ajustar configuraciones de herramientas si es necesario
3. Integrar en el flujo de CI/CD
4. Incluir en proceso de onboarding

¿El documento refleja correctamente los estándares del proyecto?
</action>

<template-output>entrega_comunicacion</template-output>
</step>

</workflow>

## Criterios de Calidad para los Estándares

- **✅ Basado en realidad**: Estándares extraídos del código existente
- **✅ Práctico**: Reglas aplicables y verificables
- **✅ Completo**: Cubre tecnologías principales del proyecto
- **✅ Específico**: Ejemplos concretos de código correcto e incorrecto
- **✅ Herramientas**: Configuración de linters y formatters incluida
- **✅ Evolutivo**: Proceso de actualización definido

## Reglas de Comportamiento OBLIGATORIAS

1. **ANÁLISIS PRIMERO**: Analizar código existente antes de crear estándares teóricos
2. **ELICITACIÓN COMPLETA**: Obtener toda la información del usuario antes de proceder
3. **BASADO EN EVIDENCIA**: Estándares deben reflejar patrones reales del código
4. **EJEMPLOS PRÁCTICOS**: Incluir código de ejemplo extraído del proyecto
5. **HERRAMIENTAS REALES**: Configuraciones basadas en lo que realmente usa el proyecto
6. **VALIDACIÓN FINAL**: Confirmar con el usuario que los estándares son correctos