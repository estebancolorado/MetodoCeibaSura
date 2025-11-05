# Documentar Componente Individual - Instrucciones de Workflow

````xml
<workflow>

<critical>The workflow execution engine is governed by: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {project-root}/.ceiba-metodo/metodo-ceiba/workflows/arquitectura/documentar-componente/workflow.yaml</critical>
<critical>ALWAYS communicate STRICTLY in {communication_language} regardless of the language used by the user</critical>

<critical>Generate all documents in {document_output_language}</critical>

<critical>REGLAS DE SALIDA DEL TEMPLATE: (1) Remover comentarios HTML de secciones completadas, mantener para secciones vacías/opcionales. (2) Asegurar que TODOS los elementos del template (títulos, encabezados, etiquetas) coincidan con {document_output_language}, no solo el contenido.</critical>

<critical>NUNCA modificar archivos template - deben permanecer inmutables para reutilización</critical>

<critical>**Propósito**
   Documentar un componente de software específico del sistema, creando documentación técnica completa que sirva como guía práctica para desarrolladores y operadores. Esta tarea está optimizada para componentes individuales dentro de ecosistemas empresariales complejos.
</critical>

<critical>**Usa esta tarea cuando:**
    Necesitas documentar un componente específico en detalle
    El componente es crítico para el sistema y requiere documentación técnica completa
    Nuevos desarrolladores necesitan entender cómo funciona un componente particular
    Buscas crear documentación estándar para componentes del ecosistema
    Requieres documentar APIs, dependencias y deployment de un componente específico
</critical>

<critical>**Prerequisites:**
    Acceso al código fuente del componente
    Conocimiento de la arquitectura del componente
    Documentación existente del componente (si está disponible)
    Acceso a configuraciones de despliegue y variables de entorno
</critical>

<step n="0" goal="CONFIGURACIÓN Y CONTEXTO INICIAL">

    <step n="0.1" goal="Elicitación de Contexto del Componente">

    <critical>**1. Identificación del Componente:**</critical>
    <ask>
    - ¿Cuál es el nombre del componente que quieres documentar?
    - ¿En qué ruta/directorio se encuentra el componente?
    - ¿Es un servicio, librería, API, frontend, u otro tipo de componente?</ask>

    <critical>**2. Contexto Técnico:**</critical>
    <ask>
    - ¿Qué tecnologías principales usa este componente (lenguaje, framework, etc.)?
    - ¿Cuál es el propósito principal de este componente dentro del sistema?</ask>

    <critical>**3. Scope de Documentación:**</critical>
    <ask>
    - ¿Existe documentación previa de este componente que deba revisar?</ask>

    <template-output>step0_2_context_info</template-output>

    </step>

</step>

<step n="1" goal="ANÁLISIS PROFUNDO DEL COMPONENTE">

    <step n="1.1" goal="Exploración de Estructura del Componente">

        <action>Ejecutar análisis exhaustivo del componente:</action>
        <action if="Si existe {architecture_sharded_location}/index.md (GPS del sistema)">**Revisar GPS arquitectónico**: revisar información sobre este componente para contexto adicional</action>
        <action>**Mapear estructura interna**: Examinar directorios, archivos principales y organización del código en {{component_path}}</action>
        <action>**Identificar configuraciones**: Buscar archivos de configuración, variables de entorno, manifiestos</action>
        <action>**Revisar documentación existente**: Localizar READMEs, comentarios de código, documentación inline</action>
        <action>**Analizar dependencias**: Identificar dependencies en package.json, requirements.txt, pom.xml, etc.</action>

        <template-output>step1_1_structure_info</template-output>

    </step>

    <step n="1.2" goal="Análisis de Arquitectura y Patrones">

        <action>Para el componente identificado, documentar:</action>

        <action>**Patrones de diseño**: Identificar arquitectura interna, patrones usados (MVC, Repository, Factory, etc.)</action>
        <action>**Tecnologías y frameworks**: Documentar stack tecnológico específico y versiones</action>
        <action>**Estructura de código**: Mapear organización de carpetas, módulos principales, puntos de entrada</action>
        <action>**Configuración y setup**: Identificar requisitos de configuración y setup inicial</action>

        <template-output>step1_2_architecture_patterns</template-output>

    </step>

    <step n="1.3" goal="Análisis de APIs y Interfaces">

        <action>Mapear interfaces expuestas:</action>
        <action>**Endpoints REST/GraphQL**: Identificar rutas, métodos, parámetros y respuestas</action>
        <action>**APIs internas**: Documentar interfaces para comunicación con otros componentes</action>
        <action>**Eventos y mensajería**: Identificar eventos publicados/consumidos</action>
        <action>**Contratos y versionamiento**: Documentar estrategias de versionado de APIs</action>

        <template-output>step1_3_apis_interfaces</template-output>

    </step>

    <step n="1.4" goal="Análisis de Dependencias y Integraciones">

        <action>Evaluar relaciones del componente:</action>
        <action>**Dependencias externas**: Librerías, servicios externos, APIs de terceros</action>
        <action>**Dependencias internas**: Otros componentes del sistema que requiere</action>
        <action>**Consumidores**: Qué otros componentes/servicios usan este componente</action>
        <action>**Puntos de integración**: Bases de datos, message queues, servicios externos</action>

        <template-output>step1_4_dependencies</template-output>

    </step>

    <step n="1.5" goal="Análisis de Deployment y Operaciones">

        <action>Mapear aspectos operacionales:</action>
        <action>**Scripts de build**: Comandos de compilación, testing, empaquetado</action>
        <action>**Variables de entorno**: Configuraciones requeridas para diferentes ambientes</action>
        <action>**Prerequisitos de infraestructura**: Dependencias de infraestructura necesarias</action>
        <action>**Monitoreo y logging**: Herramientas y configuraciones de observabilidad</action>

        <template-output>step1_5_deployment</template-output>

    </step>

</step>

<step n="2" goal="Validación y Refinamiento">

    <action>Revisar la documentación generada:</action>
    <action>Verificar completitud de todas las secciones</action>
    <action>Asegurar consistencia en el nivel de detalle</action>
    <action>Validar que la información sea práctica y útil</action>
    <action>Confirmar que los diagramas Mermaid sean apropiados</action>
    <action>Verificar que las secciones N/A estén apropiadamente documentadas</action>

    <ask>
    - ¿Hay alguna sección que requiera más detalle?
    - ¿Falta algún aspecto específico del componente?
    - ¿La documentación cubre las necesidades de tu equipo?</ask>

     <check if="usuario solicita realizar ajustes">
         <action>Aplicar cambios</action>
         <action>Actualizar documentación</action>
         <action>Repetir validación</action>
     </check>
</step>

</workflow>
````
