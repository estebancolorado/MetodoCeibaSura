<!-- Powered by Método Ceiba -->

# Refine Story Task - Refinamiento Técnico de Historia de Usuario

## Purpose

Refinar una historia de usuario existente creada por el PO, añadiendo contexto técnico profundo, análisis de implementación, descomposición en tareas técnicas y preparación completa para desarrollo. Esta tarea enriquece el mismo archivo de historia sin crear uno nuevo.

## When to Use This Task

**Use this task when:**

- Existe una historia creada por el PO en estado "Borrador (PO)" o este analizada por el Arquitecto
- Se necesita añadir contexto técnico para implementación
- Se debe preparar la historia para estimación y desarrollo
- Se necesita descomponer la funcionalidad en tareas técnicas específicas

**Prerequisites:**

- Historia de usuario existente creada por PO
- Acceso a documentación de arquitectura del proyecto
- Core-config.yaml configurado correctamente
- Conocimiento del stack tecnológico del proyecto

## Task Execution Instructions

### 0. CONFIGURACIÓN Y VALIDACIÓN INICIAL

#### 0.1 Cargar Configuración

- Cargar `.ceiba-metodo/core-config.yaml`
- Extraer configuraciones: `devStoryLocation`, `architecture.*`, `prd.*`
- Si no existe core-config.yaml, HALT: "core-config.yaml no encontrado"

#### 0.2 Solicitar Historia a Refinar

**Preguntar al usuario:**
"¿Qué historia de usuario deseas refinar?

- Proporciona el número de la historia (ejemplo: 5 para refinar `5.story.md`)
- O especifica la ruta completa del archivo de historia"

#### 0.3 Validar Historia Existente

- Verificar que existe `{devStoryLocation}/{número}.story.md`
- Cargar contenido completo de la historia
- Verificar estado: debe ser "Analizado (Arquitecto)" o en casos excepcionales "Borrador (PO)"
- **PREFERENCIA POR ANÁLISIS ARQUITECTÓNICO**: Si la historia está en "Borrador (PO)" sin análisis arquitectónico:

  **ADVERTENCIA OBLIGATORIA:**
  "⚠️ **REFINAMIENTO SIN ANÁLISIS ARQUITECTÓNICO**

  La historia está en estado 'Borrador (PO)' sin análisis arquitectónico previo.

  **RECOMENDACIÓN FUERTE**: Ejecutar `analisis-y-diseno` en el agente arquitecto antes del refinamiento para:
  - ✅ Obtener decisiones arquitectónicas validadas
  - ✅ Mejor comprensión de componentes afectados
  - ✅ Refinamiento técnico más preciso y completo
  - ✅ Separación clara de responsabilidades arquitectónicas vs técnicas

  **OPCIONES:**
  1. **PAUSA RECOMENDADA** - Ejecutar `analisis-y-diseno` primero (calidad superior)
  2. **CONTINUAR LIMITADO** - Proceder sin análisis arquitectónico (resultado menos preciso)

  ¿Prefieres pausar para análisis arquitectónico o continuar con refinamiento limitado?"

  **BLOQUEO:** Esperar respuesta del usuario antes de continuar.

- Si no existe el archivo o está en estado incompatible, HALT con mensaje apropiado

### 1. ANÁLISIS DE ARQUITECTURA Y CONTEXTO

#### 1.1 Verificar y Utilizar Análisis Arquitectónico Existente

**PRIMERA PRIORIDAD: Revisar si existe análisis arquitectónico:**

- Buscar sección "Análisis Arquitectónico (Arquitecto)" en la historia
- Si existe análisis arquitectónico validado:
  - **USAR COMO BASE PRINCIPAL** para el refinamiento técnico
  - Extraer decisiones de diseño, componentes identificados, patrones seleccionados
  - Utilizar las especificaciones técnicas como guía para descomposición
  - Referenciar estrategias de implementación propuestas
  - Considerar riesgos arquitectónicos ya identificados

**VENTAJAS DEL ANÁLISIS ARQUITECTÓNICO PREVIO:**

- ✅ Decisiones validadas por arquitecto humano
- ✅ Componentes específicamente identificados para la funcionalidad
- ✅ Patrones arquitectónicos seleccionados apropiadamente
- ✅ Interfaces y contratos ya especificados
- ✅ Riesgos arquitectónicos pre-identificados
- ✅ Base sólida para descomposición técnica precisa

#### 1.2 Revisar Documentación de Arquitectura Completa (Complementaria) - Importante: este paso ejecutarlo solo si no existe ANÁLISIS ARQUITECTÓNICO PREVIO

**Análisis integral del ecosistema de documentación para complementar análisis arquitectónico:**

**GPS Arquitectónico (`{architectureShardedLocation}/index.md`):**

- **Si hay análisis arquitectónico**: Validar componentes mencionados en el GPS
- **Sin análisis arquitectónico**: Revisar overview del sistema y identificar módulos relevantes
- Extraer patrones y estándares arquitectónicos aplicables

**Documentación de Componentes (`{architectureShardedLocation}/architecture-*.md`):**

- **Con análisis arquitectónico**: Profundizar en componentes ya identificados por el arquitecto
- **Sin análisis arquitectónico**: Revisar componentes que podrían verse afectados
- Analizar APIs, dependencias y patrones de integración existentes
- Identificar responsabilidades y limitaciones de cada componente
  para este paso usa varios comodines de busqueda para encontrar los flujos y la documentación de cada componente

**Flujos de Negocio (`{architectureShardedLocation}/flujo-*.md`):**

- **Con análisis arquitectónico**: Validar y extender flujos mencionados en análisis
- **Sin análisis arquitectónico**: Buscar flujos existentes relacionados con la funcionalidad
- Analizar diagramas de secuencia y patrones de interacción
- Identificar puntos de integración y extensión del flujo
  para este paso usa varios comodines de busqueda para encontrar los flujos y la documentación de cada componente

**Historias Refinadas Existentes (`{devStoryLocation}/*.story.md`):**

- **DEBE revisar** historias refinadas existentes para identificar:
  - **Patrones de refinamiento técnico** aplicados en funcionalidades similares
  - **Descomposición de tareas** utilizada en historias relacionadas
  - **Estrategias de implementación** documentadas en historias previas
  - **Componentes técnicos identificados** en refinamientos anteriores
  - **Riesgos y complejidades** ya documentados en historias similares
  - **Estimaciones y justificaciones** de historias con funcionalidades relacionadas
  - **Lecciones aprendidas** del desarrollo de historias previas (sección Dev Agent Record)
  - **Patrones de testing** implementados en historias similares
  - **Actualizaciones documentales** requeridas en refinamientos anteriores

- **DEBE buscar** historias que hayan involucrado:
  - Los mismos componentes arquitectónicos identificados
  - Funcionalidades del mismo dominio de negocio
  - Patrones de integración similares
  - Tecnologías o capas similares del sistema

- **DEBE extraer** de historias refinadas:
  - **Plantillas de tareas técnicas** exitosas
  - **Criterios de completitud** ya probados

**⚠️ VERIFICACIÓN DE COMPLETITUD DE DOCUMENTACIÓN:**

Si NO se encuentra alguno de estos elementos críticos:

- ❌ Arquitectura base (`{architectureShardedLocation}/index.md` o `{architectureFile}`)
- ❌ Documentación de componentes (`architecture-*.md`, `component*.md`, etc.)
- ❌ Flujos de negocio documentados (`flujo-*.md`, `flow-*.md`, `proceso-*.md`, etc.)

**ADVERTENCIA OBLIGATORIA AL USUARIO:**

"⚠️ **ADVERTENCIA: Documentación Arquitectónica Incompleta**

No se encontró documentación completa en las siguientes áreas:

- [Lista específica de elementos faltantes]

**IMPACTO EN EL REFINAMIENTO:** Sin esta documentación, el refinamiento técnico será:

- ✗ Menos preciso en la identificación de componentes
- ✗ Limitado en la comprensión de patrones existentes
- ✗ Menos efectivo en la estimación de complejidad
- ✗ Menos confiable en la descomposición de tareas

**OPCIONES:**

1. **CONTINUAR** - Proceder con refinamiento basado únicamente en análisis de código (resultado menos preciso)
2. **PAUSAR** - Detener para documentar la arquitectura primero (recomendado para mejor calidad)

¿Deseas continuar con el refinamiento limitado o prefieres pausar para completar la documentación arquitectónica?"

**BLOQUEO:** Esperar respuesta del usuario antes de continuar.

#### 1.3 Identificar Componentes Técnicos (Basado en Análisis Arquitectónico)

**Si existe análisis arquitectónico, usar sus especificaciones como base:**

- **Componentes identificados por el arquitecto**: Usar decisiones ya validadas
- **Modificaciones específicas propuestas**: Implementar según especificaciones arquitectónicas
- **Nuevos componentes sugeridos**: Seguir responsabilidades definidas por el arquitecto
- **Interfaces especificadas**: Implementar contratos ya diseñados

**Si NO existe análisis arquitectónico, determinar independientemente:**

- **Capa de datos**: Modelos, esquemas, migraciones necesarias
- **Capa de negocio**: Servicios, validaciones, reglas de negocio
- **Capa de presentación**: Controladores, vistas, componentes UI
- **Integraciones**: APIs externas, servicios de terceros
- **Infraestructura**: Configuraciones, despliegues, variables de entorno

### 2. ANÁLISIS TÉCNICO PROFUNDO DEL CÓDIGO BASE (CON CONTEXTO)

**Realizar análisis dirigido del código existente basado en el contexto arquitectónico identificado o lo que consideres:**

#### 2.1 Análisis de Funcionalidades Similares y Componentes Específicos

**Buscar e identificar en base a la arquitectura documentada:**

- Funcionalidades existentes similares en los **componentes identificados**
- Patrones arquitectónicos implementados en los **módulos relevantes**
- Convenciones de código utilizadas en las **capas específicas** (datos, negocio, presentación)
- Estructuras de datos y modelos relacionados con los **esquemas identificados**

#### 2.2 Análisis de Integración y Dependencias Dirigido

**Investigar específicamente:**

- Puntos de integración con los **componentes y servicios identificados**
- Dependencias existentes en los **módulos arquitectónicos relevantes**
- APIs internas/externas utilizadas según **documentación de componentes**
- Servicios que interactúan según **flujos de negocio documentados**

#### 2.3 Análisis de Estrategias de Testing Contextualizado

**Revisar en base a componentes identificados:**

- Estrategias de testing en los **módulos y capas específicas**
- Frameworks utilizados para **tipos de componentes similares**
- Patrones de pruebas en **funcionalidades relacionadas**
- Cobertura en **áreas arquitectónicas similares**

#### 2.4 Consolidar Información Técnica con Contexto Arquitectónico

**Documentar hallazgos integrados:**

- **Patrones específicos**: Cómo se implementan en los componentes identificados
- **Arquitectura validada**: Confirmación de módulos, capas, y componentes
- **Dependencias confirmadas**: Servicios, APIs, bases de datos validadas en código
- **Estrategias de testing aplicables**: Frameworks y patrones usados en componentes similares

#### 2.5 Validación Técnica con Desarrollador (OPCIONAL)

**Si el agente tiene dudas técnicas o incertidumbres después del análisis:**

**2.5.1 Identificar Puntos de Incertidumbre**

Evaluar si existen:

- Aspectos técnicos que requieren clarificación específica
- Supuestos sobre componentes o patrones que podrían ser incorrectos
- Gaps en el entendimiento de la implementación actual
- Dudas sobre la complejidad real de ciertas tareas
- Incertidumbre sobre el mejor enfoque técnico

**2.5.2 Formular Preguntas Específicas al Usuario-Desarrollador**

**Nota Importante: Si hay incertidumbres, preguntar antes de continuar:**

**Validación de Componentes y Arquitectura:**

- Realiza las preguntas que consideraste que son importantes y que no debes asumir antes de continuar

**2.5.3 Incorporar Feedback del Desarrollador**

**Ajustar el análisis basado en las respuestas:**

- Corregir componentes o módulos mal identificados
- Actualizar estimaciones de complejidad si es necesario
- Modificar enfoques técnicos según recomendaciones
- Refinar tareas de implementación según feedback real

**Documentar cambios realizados:**

- Listar correcciones realizadas basadas en feedback
- Actualizar nivel de confianza en el refinamiento
- Anotar recomendaciones específicas del desarrollador

### 3. ENRIQUECIMIENTO DE LA HISTORIA EXISTENTE

#### 3.1 Actualizar Encabezado

- Cambiar estado de "Borrador (PO) o Analizado" a "Refinado (SM)"
- Añadir fecha de refinamiento
- Mantener toda la información original del PO

#### 3.2 Añadir Sección de Contexto Técnico (Considerando Análisis Arquitectónico)

**⚠️ IMPORTANTE: Insertar nueva sección con las decisiones FINALES APROBADAS**

**DEBE incluir únicamente las decisiones que fueron validadas y aprobadas por el humano**

(agrega solo algunas de estas sesiones que apliquen, no tienes que agregar todas, solo las que apliquen y den valor)

```markdown
## Contexto Técnico (SM)

### Base del Refinamiento

**Análisis arquitectónico utilizado:** {SI/NO - indicar si se basó en análisis previo del arquitecto}

**Si se basó en análisis arquitectónico:**

- **Decisiones arquitectónicas adoptadas:** {decisiones clave del arquitecto}
- **Componentes validados:** {componentes específicos identificados por el arquitecto}
- **Patrones seguidos:** {patrones arquitectónicos seleccionados por el arquitecto}

### Análisis de Arquitectura y Documentación

**Arquitectura:**

- **Componentes principales afectados:** {según análisis arquitectónico O documentación analizada O procesos que has revisado en el código}
- **Módulos relevantes:** {según análisis arquitectónico O documentación analizada O procesos que has revisado en el código}
- **Patrones arquitectónicos:** {patrones del análisis arquitectónico O identificados en la documentación}

**Componentes Específicos:**

- **Componente A:** {según análisis arquitectónico O architecture-componenteA.md - APIs, responsabilidades, limitaciones}
- **Componente B:** {según análisis arquitectónico O architecture-componenteB.md - integraciones, dependencias}
- **Nuevos componentes:** {según análisis arquitectónico O si es necesario crear componentes adicionales}

### Análisis de Historias Refinadas Relacionadas

**Historias similares consultadas:** {lista de historias con funcionalidades relacionadas}

**Patrones de refinamiento reutilizados:**

- **Historia #{X}:** {patrón de descomposición de tareas aplicado}
- **Historia #{Y}:** {estrategia de testing adoptada}
- **Historia #{Z}:** {enfoque de manejo de riesgos utilizado}

**Lecciones aprendidas aplicadas:**

- **Complejidad identificada:** {basado en historias similares - alta/media/baja y justificación}
- **Riesgos conocidos:** {problemas recurrentes identificados en historias previas}
- **Mejores prácticas:** {enfoques exitosos extraídos de refinamientos anteriores}
- **Estimaciones de referencia:** {tiempos y esfuerzos de historias comparables}

**Flujos de Negocio Relacionados:**

- **Flujos existentes:** {según análisis arquitectónico O flujo-\*.md que se conectan con esta funcionalidad}
- **Extensiones de flujo:** {modificaciones necesarias a flujos existentes}
- **Nuevos flujos:** {si requiere documentar nuevos flujos de negocio}

### Análisis de Impacto

- **Código existente:** {archivos/módulos que requieren modificación}
- **Breaking changes:** {cambios que podrían afectar funcionalidad existente}
- **Migraciones:** {cambios de esquema o datos necesarios}
- **Configuración:** {variables de entorno, configuraciones nuevas}
```

### 4. DESCOMPOSICIÓN EN TAREAS TÉCNICAS DETALLADAS

#### 4.1 Crear Sección de Tareas de Implementación

**IMPORTANTE: Las tareas mostradas a continuación son EJEMPLOS y PLANTILLA. Debes analizar qué tareas realmente aplican para esta historia específica basándote en:**

- Los criterios de aceptación de la historia
- El análisis arquitectónico realizado: : Usar los hallazgos de arquitectura y código para definir tareas reales
- La complejidad identificada
- Los componentes realmente afectados

**NO incluir tareas que no sean necesarias para esta historia particular.**

**Reemplazar o enriquecer la sección de "Definición de Terminado" con:**

```markdown
## Tareas de Implementación (SM)

### Fase 1: Preparación y Análisis

- [ ] **Análisis detallado de código existente**
  - [ ] Revisar {archivos específicos identificados}
  - [ ] Documentar patrones actuales en {módulo}
  - [ ] Identificar puntos de integración con {servicios}

- [ ] **Diseño técnico** (AC: {números de criterios relevantes})
  - [ ] Crear/actualizar modelos de datos: {modelos específicos}
  - [ ] Definir interfaces de API: {endpoints específicos}
  - [ ] Diseñar componentes UI: {componentes específicos}

### Fase 2: Implementación Backend (si aplica)

- [ ] **Capa de datos** (AC: {números de criterios relevantes})
  - [ ] Crear/modificar modelo: {modelo específico}
  - [ ] Implementar migraciones: {tablas/campos específicos}
  - [ ] Añadir validaciones: {reglas específicas}

- [ ] **Capa de negocio** (AC: {números de criterios relevantes})
  - [ ] Implementar servicio: {servicio específico}
  - [ ] Añadir validaciones de negocio: {reglas específicas}
  - [ ] Integrar con: {servicios específicos}

- [ ] **Capa de API** (AC: {números de criterios relevantes})
  - [ ] Crear endpoints: {rutas específicas} (AC: {específicos})
  - [ ] Implementar autenticación/autorización
  - [ ] Añadir documentación de API

### Fase 3: Implementación Frontend (si aplica)

- [ ] **Componentes UI** (AC: {números de criterios relevantes})
  - [ ] Crear componente: {componente específico} (AC: {específicos})
  - [ ] Implementar formulario: {campos específicos} (AC: {específicos})
  - [ ] Añadir validaciones cliente: {validaciones específicas} (AC: {específicos})

- [ ] **Integración con Backend** (AC: {números de criterios relevantes})
  - [ ] Implementar llamadas a API: {endpoints específicos} (AC: {específicos})
  - [ ] Manejar estados de carga y error
  - [ ] Añadir feedback de usuario (AC: {específicos})

### Fase 4: Testing

- [ ] **Tipos de pruebas que apliquen según el contexto**
  - [ ] Tipo de prueba de ejemplo: {modelo específico}

{para esto basarte en las tareas de documentación que tiene el agente architect o sugerir al agente architect que lo haga}

### Fase 5: Documentación y Despliegue

- Sugerir si existen cambios o nuevos componentes usar el agente arquitecto con su tarea documentar-arquitectura-base para integrar el nuevo componente
- Si este desarrollo cambió el comportamiento de los componentes sugererir usar el agente arquitecto con su tarea documentar-componente para actualizar su información
- Si este desarrollo es importante se recomienda al usuario usar el agente arquitecto con su tarea de documentar-flujo-negocio para que esto sea como insumo en los proximos desarrollos

**Notas sobre vinculación con Criterios de Aceptación:**

- Cada tarea debe indicar qué criterio(s) de aceptación ayuda a completar usando (AC: X, Y, Z)
- Las tareas de infraestructura y setup pueden no tener vinculación directa a AC específicos
- Algunas tareas pueden contribuir parcialmente a múltiples criterios
```

### 5. FINALIZACIÓN Y VALIDACIÓN

#### 5.1 Actualizar Estado de la Historia

- **Si se basó en análisis arquitectónico**: Cambiar estado a "Refinado (SM) - Lista para Estimación"
- **Si no hubo análisis arquitectónico**: Cambiar estado a "Refinado (SM) - Sin Análisis Arquitectónico"
- Añadir fecha de refinamiento
- Añadir nota de que está lista para `estimate-story`

#### 5.2 Validación de Completitud

**Verificar que la historia refinada contiene:**

- ✅ Toda la información original del PO intacta
- ✅ Contexto técnico completo añadido
- ✅ Tareas de implementación detalladas
- ✅ Referencias a arquitectura y patrones existentes

#### 5.3 Guardar Historia Enriquecida

- Sobrescribir el archivo original `{devStoryLocation}/{número}.story.md`
- Mantener el mismo nombre de archivo
- El archivo ahora contiene tanto información del PO como refinamiento del SM

### 6. ENTREGA Y COMUNICACIÓN

**Mensaje final obligatorio:**

```
Historia #{número} refinada exitosamente.

**Archivo actualizado:** `{devStoryLocation}/{número}.story.md`

**Refinamiento completado:**
✅ Contexto técnico añadido basado en documentación arquitectónica
✅ Tareas de implementación descompuestas
✅ {número} fases de implementación definidas
✅ Documentación arquitectónica revisada e integrada

**Estado actual:** Refinado (SM) - Lista para Estimación

**Base del refinamiento:** {Basado en análisis arquitectónico validado / Refinamiento directo sin análisis arquitectónico}

{si aplica}
**Documentación consultada:**
- GPS Arquitectónico: {secciones relevantes}
- Componentes revisados: {lista de componentes analizados}
- Flujos de negocio: {flujos relacionados identificados}
- **Historias refinadas consultadas:** {lista de historias similares analizadas}

**Si se utilizó análisis arquitectónico:**
- **Decisiones arquitectónicas aplicadas:** {resumen de decisiones del arquitecto incorporadas}
- **Componentes especificados:** {componentes validados por el arquitecto}
- **Patrones arquitectónicos seguidos:** {patrones seleccionados por el arquitecto}

**Patrones de refinamiento aplicados:**
- **Descomposición de tareas:** Basada en patrones exitosos de historia #{X} {y decisiones arquitectónicas si aplican}
- **Estrategias de testing:** Siguiendo enfoque probado en historia #{Y}
- **Identificación de riesgos:** Considerando lecciones de historia #{Z} {y riesgos arquitectónicos identificados si aplican}
- **Complejidad estimada:** Referenciando métricas de historias similares

**Recomendaciones para desarrollo:**
- Seguir patrones identificados en componente {X}
- Integrar siguiendo el flujo documentado en {Y}
- Actualizar documentación arquitectónica según checklist de tareas

**Próximos pasos:**
1. Usar `estimate-story` para estimación detallada
2. Asignar a desarrollador según perfil recomendado
3. Proceder con implementación usando tareas definidas
4. **IMPORTANTE**: Actualizar documentación arquitectónica según checklist de tareas

**Complejidad identificada:** {nivel general}
**Tiempo estimado:** {rango preliminar}

¿El refinamiento cubre todas las consideraciones técnicas necesarias?
```

## Criterios de Éxito del Refinamiento

- **✅ Información preservada**: Todo el trabajo del PO se mantiene intacto
- **✅ Contexto técnico completo**: Arquitectura, patrones, y dependencias documentadas
- **✅ Tareas implementables**: Descomposición clara y ejecutable por desarrollador
- **✅ Trazabilidad AC**: Cada tarea funcional vinculada a criterios de aceptación específicos
- **✅ Validación técnica**: Incertidumbres resueltas con desarrollador cuando sea necesario
- **✅ Un solo archivo**: Toda la información centralizada en el archivo original

## Reglas de Comportamiento OBLIGATORIAS

1. **PRESERVAR INFORMACIÓN**: Nunca eliminar o modificar la información original del PO
2. **UN ARCHIVO**: Trabajar siempre sobre el archivo existente, no crear uno nuevo
3. **ADVERTENCIA DE DOCUMENTACIÓN FALTANTE**: Si no se encuentra arquitectura base, componentes documentados, flujos de negocio, OBLIGATORIAMENTE advertir al usuario sobre las limitaciones del refinamiento y solicitar confirmación para continuar
4. **ANÁLISIS DE HISTORIAS REFINADAS**: OBLIGATORIAMENTE revisar historias refinadas existentes para identificar patrones, lecciones aprendidas y estrategias de refinamiento exitosas
5. **ANÁLISIS PROFUNDO**: Realizar investigación exhaustiva del código base, arquitectura existente y refinamientos previos
6. **VALIDACIÓN COLABORATIVA**: Cuando haya dudas técnicas, consultar con el desarrollador antes de continuar
7. **TAREAS ESPECÍFICAS**: Descomponer en tareas concretas y ejecutables, basándose en patrones exitosos de historias similares
8. **TAREAS RELEVANTES**: Solo incluir tareas que realmente aplican a esta historia específica, no copiar toda la plantilla
9. **MAPEO DE CRITERIOS**: Vincular cada tarea funcional con los criterios de aceptación que ayuda a completar (AC: X, Y)
10. **ESTADO CLARO**: Actualizar estado para indicar progreso en el flujo
11. **PREPARAR ESTIMACIÓN**: Dejar la historia lista para estimación detallada
