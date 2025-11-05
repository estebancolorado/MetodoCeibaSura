# Arquitectura Solución - Instrucciones de Workflow

<critical>El motor de ejecución de workflows está gobernado por: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
<critical>Debes haber cargado y procesado: {project-root}/.ceiba-metodo/metodo-ceiba/workflows/arquitectura/crear-arquitectura-solucion/workflow.yaml</critical>
<critical>Comunica SIEMPRE ESTRICTAMENTE en {communication_language} sin importar el idioma usado por el usuario</critical>
<critical>Genera todos los documentos en {document_output_language}</critical>

<workflow>

<step n="1" goal="Preparación y Contexto Inicial">
<action>Verificar disponibilidad de documentos base, especialmente docs/prd.md</action>
<check if="docs/prd.md no disponible">
<ask>¿Qué documentos servirán de base para la arquitectura? Proporciona las rutas:</ask>
</check>
<action>Cargar y revisar todos los documentos relevantes proporcionados</action>
<ask>Confirma el nombre del proyecto para {{project_name}}:</ask>
</step>

<step n="2" goal="Introducción y Objetivos">
<template-output>
# Introducción y Objetivos

Este documento describe la arquitectura general del proyecto para {{project_name}}, incluyendo sistemas de backend, servicios compartidos y aspectos no específicos de la interfaz de usuario (UI). Su objetivo principal es servir como el plano arquitectónico guía para el desarrollo impulsado por IA, asegurando la consistencia y el cumplimiento de los patrones y tecnologías elegidos.
</template-output>
</step>

<step n="3" goal="Resumen de Requisitos">
<action>Analizar documentos proporcionados para extraer requisitos funcionales clave</action>
<elicit-required>
Proveer un extracto o resumen de los requisitos funcionales y las motivaciones o fuerzas impulsoras del sistema. 
- Es importante que esta sección sea concisa. 
- Si existen documentos de requisitos, esta visión general debe referenciar a estos documentos. 
- Mantén estos extractos lo más breves posible. 
- Equilibra la legibilidad de este documento con la potencial redundancia respecto a los documentos de requisitos.
</elicit-required>
<template-output>
## Resumen de Requisitos

{{resumen_requisitos}}
</template-output>
</step>

<step n="4" goal="Objetivos de Calidad">
<elicit-required>
1. Identifica y prioriza los 3 a 5 objetivos de calidad más críticos para la arquitectura
- Basate en el contexto y las expectativas de los interesados proporcionados. 
- La priorización debe reflejar el impacto directo en las decisiones de arquitectura.
- Asegúrate de que los objetivos se centren en los atributos de calidad del sistema (qué tan bien hace algo) y no en los objetivos funcionales del proyecto (qué hace).

2. Redacta cada objetivo de calidad:

- Como un ASR principal
- De forma clara y concreta
- Evita términos de moda o ambiguos.

3. Justifica la relevancia de cada objetivo:

- Explica por qué es crucial para los interesados
- Cómo influirá en las decisiones de diseño arquitectónico.

4. El resultado final debe dejar claro que estos no son simples deseos, sino requisitos rigurosos que dictarán las decisiones de arquitectura más importantes del proyecto.
   </elicit-required>
   <template-output>

## Objetivos de Calidad

{{objetivos_calidad}}
</template-output>
</step>

<step n="5" goal="Interesados">
<elicit-required>
Identifica las personas, roles u organizaciones que:
- Deben conocer la arquitectura.
- Deben aprobar o dar su visto bueno a la arquitectura.
- Deben trabajar con la arquitectura o con el código.
- Necesitan la documentación de la arquitectura para su trabajo.
- Deben tomar decisiones sobre el sistema o su desarrollo.
</elicit-required>
<template-output>
## Interesados

| Nombre | Expectativas |
| ------ | ------------ |

{{tabla_interesados}}
</template-output>
</step>

<step n="6" goal="Restricciones">
<elicit-required>
Documentar cualquier requisito, que deba ser respetado, y que limite o condicione la libertad en la toma de decisiones de diseño o implementación, así como decisiones sobre el proceso de desarrollo. 
Estas restricciones a menudo van más allá de los sistemas individuales y son válidas para organizaciones y empresas enteras. 
Las restricciones NO son supuestos
Las restricciones se documentan en lista sencillas con explicaciones y agrupadas según la clasificación de las restricciones que se encuentran en un proyecto:

- **Restricciones Técnicas:**
  - Tecnologías específicas que deben utilizarse (por ejemplo, lenguajes de programación, frameworks, plataformas).
  - Limitaciones de infraestructura (por ejemplo, requisitos de hardware, sistemas operativos, bases de datos).
  - Normativas de seguridad y cumplimiento.
- **Restricciones Organizativas:**
  - Directrices corporativas (por ejemplo, políticas de TI, estándares de calidad).
  - Procesos de desarrollo establecidos (por ejemplo, metodologías ágiles, ciclos de lanzamiento).
- **Restricciones Políticas:**
  - Normativas legales y de regulación que deben cumplirse.
  - Acuerdos contractuales con terceros o proveedores.
- **Convenciones:**
  - Guías de programación (estilo de codificación, estándares de revisión de código).
  - Directrices de versionado y gestión de configuraciones.
  - Convenciones de documentación y nomenclatura.
    </elicit-required>
    <template-output>

# Restricciones

{{restricciones}}
</template-output>
</step>

<step n="7" goal="Contexto de Negocio">
<elicit-required>
Identificación de todos los sistemas y/o personas con los que interactúa el sistema que se está construyendo. 
Se deben especificar las entradas y salidas propias del dominio para mostrar como encaja en la solución. 
Los detalles no son importantes aquí, ya que esta es una vista ampliada que muestra un panorama general del panorama del sistema. 
El enfoque debe estar en las personas (actores, roles, personajes, etc.) y los sistemas de software. 
Es el tipo de diagrama que podría mostrar a personas sin conocimientos técnicos.
</elicit-required>
<template-output>
# Contexto y Alcance

## Contexto de Negocio

```mermaid
{{diagrama_contexto_negocio}}
```

### Tabla de Contexto de Negocio

| Elemento | Entradas | Salidas |
| -------- | -------- | ------- |

{{tabla_contexto_negocio}}
</template-output>
</step>

<step n="8" goal="Contexto Técnico">
<elicit-required>
Utilizar un diagrama de despliegue para describir los canales hacia los sistemas vecinos
Para las soluciones en nube se debe preferir la generación de diagramas usando los iconos y notación propuesta por el proveedor de servicios en la nube seleccionado ([AWS](https://aws.amazon.com/architecture/icons/), [Azure](https://learn.microsoft.com/en-us/azure/architecture/icons/), [GCP](https://cloud.google.com/icons))
</elicit-required>
<template-output>
## Contexto Técnico

```mermaid
{{diagrama_contexto_tecnico}}
```

### Lista de Servicios/Componentes

{{lista_contexto_tecnico}}
</template-output>
</step>

<step n="9" goal="Estrategia de Solución">
<elicit-required>
Ofrece un resumen y una explicación breve de las decisiones fundamentales y las estrategias de solución que configuran la arquitectura del sistema. 
Incluye:
  - **Decisiones tecnológicas:** Elecciones sobre las tecnologías que se utilizarán.
  - **Decisiones sobre la descomposición de alto nivel del sistema:** Uso de patrones arquitectónicos o de diseño.
  - **Decisiones sobre cómo alcanzar objetivos clave de calidad:** Estrategias para cumplir con los objetivos de calidad del sistema.
  - **Decisiones organizativas relevantes:** Selección de procesos de desarrollo o delegación de tareas a terceros.
Mantén la explicación de estas decisiones breve, centrate en lo que has decidido y por qué decidiste de esa manera, basándote en la declaración del problema, los objetivos de calidad y las restricciones clave. 
Las secciones Vista Estática y Vista Dinámica son ideales para, en caso de ser necesario, detallar las estrategias de solución.
La lista de las decisiones NO debe estar agrupada.
</elicit-required>
<template-output>
# Estrategia de Solución

{{estrategia_solucion}}
</template-output>
</step>

<step n="10" goal="Vista Estática (Componentes)">
<elicit-required>
La vista estática muestra la descomposición del sistema en bloques de construcción (módulos, componentes, subsistemas, etc.) así como sus dependencias (relaciones, asociaciones, etc.). 
Esta vista es obligatoria para toda documentación de arquitectura.
Mostrar cómo el sistema se descompone en contenedores, que pueden ser aplicaciones, servicios, bases de datos, etc., y cómo interactúan entre sí.
</elicit-required>
<template-output>
# Vista Estática (Componentes)

```mermaid
{{diagrama_vista_estatica}}
```

## Tabla de Componentes

| Contenedor | Propósito/Responsabilidad | Interfaces |
| ---------- | ------------------------- | ---------- |

{{tabla_vista_estatica}}
</template-output>
</step>

<step n="11" goal="Vista Dinámica (Ejecución)" repeat="for-each-flujo">
<action>Determinar flujos arquitectónicamente relevantes</action>
<elicit-required>
El criterio principal para la elección de posibles escenarios (secuencias, flujos de trabajo) es la relevancia arquitectónica. 
Debes documentar los escenarios que son críticos y/o representativos para la comprensión arquitectónica y que tengan un impacto significativo sobre la arquitectura del sistema.
La vista dinámica describe el comportamiento concreto y las interacciones de los bloques del sistema en forma de escenarios desde los siguientes puntos de vista:

1. **Casos de uso o características importantes:** ¿Cómo ejecutan los bloques estos casos de uso?
2. **Interacciones en interfaces externas críticas:** ¿Cómo cooperan los bloques de construcción con los usuarios y sistemas vecinos?
3. **Operación y administración:** Inicio, arranque, detención, escenarios de error y caminos excepción.
   </elicit-required>
   <ask>¿Nombre del flujo a documentar?</ask>
   <template-output>

# Vista Dinámica (Ejecución)

## {{flujo_nombre}}

```mermaid
sequenceDiagram
{{diagrama_secuencia_flujo}}
```

{{descripcion_flujo}}
</template-output>
<ask>¿Agregar otro flujo? (y/n)</ask>
<check if="agregar_otro_flujo">
<goto step="11">Repetir para otro flujo</goto>
</check>
</step>

<step n="12" goal="Finalización y Validación">
<ask>¿Generar el documento de arquitectura completo? (y/n)</ask>
<check if="generar_documento">
<action>Compilar todas las secciones en el documento final</action>
<template-output>
{{documento_completo}}
</template-output>
</check>
<invoke-task>{project-root}/.ceiba-metodo/metodo-ceiba/workflows/arquitectura/crear-arquitectura-solucion/checklist.md</invoke-task>
</step>

</workflow>
