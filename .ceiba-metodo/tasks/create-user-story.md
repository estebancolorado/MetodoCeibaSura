<!-- Powered by Método Ceiba -->

# Create User Story Task - Escritor HU Especialista

## Purpose

Crear historias de usuario iniciales siguiendo una metodología estructurada, enfocándose en obtener información completa del usuario sin asumir detalles. Esta tarea es responsabilidad del Product Owner (PO) y genera la base que posteriormente será refinada por el Scrum Master.

## When to Use This Task

**Use this task when:**

- El PO necesita crear una nueva historia de usuario desde cero
- Se requiere recopilar información detallada del stakeholder
- Es necesario crear una historia completa sin asumir detalles
- Se necesita una base sólida para posterior refinamiento técnico

**Prerequisites:**

- Identificación clara del requerimiento o funcionalidad
- Acceso al stakeholder o usuario para aclaraciones
- Ubicación definida para almacenar la historia (`devStoryLocation` en core-config.yaml)

## 🎯 RESPONSABILIDADES DEL PRODUCT OWNER

**IMPORTANTE:** Como PO, tu enfoque debe ser 100% de NEGOCIO:

- ✅ **PUEDES hacer internamente**: Revisar código, documentación y arquitectura para entender el contexto
- ✅ **TUS PREGUNTAS AL USUARIO**: Solo aspectos de negocio, flujos funcionales, criterios de aceptación
- ❌ **NO preguntes al usuario**: Detalles técnicos, arquitectura, tecnologías específicas

**Las dudas técnicas las resuelves internamente o las delegas al Arquitecto (análisis-y-diseno) y Scrum Master (refine-story)pero nunca le preguntes nada técnico al usuario**

## Task Execution Instructions

### 0. CAPTURA INICIAL DEL REQUERIMIENTO

**🎯 OBJETIVO: Entender QUÉ quiere hacer el usuario**

#### 0.1 Solicitar Requerimiento Inicial y Caracterización del Perfil

**Mensaje:**
"¡Hola! Voy a ayudarte a crear una nueva historia de usuario siguiendo nuestra metodología

**Paso 1/6: Caracterización de perfil y descripción del requerimiento**

Primero, necesito conocer tu perfil para adaptar mis preguntas de manera adecuada:

**Caracterización de perfil:**

¿Cuál de estas opciones describe mejor tu rol y conocimiento?

**A) Perfil Funcional Puro:**

- Te enfocas exclusivamente en aspectos de negocio
- No manejas conceptos técnicos (APIs, bases de datos, arquitectura)
- Tu expertise está en procesos, flujos de usuario y reglas de negocio

**B) Perfil Técnico-Funcional:**

- Tienes conocimiento tanto de negocio como técnico
- Puedes discutir aspectos de arquitectura, integración y tecnología
- Entiendes conceptos como APIs, microservicios, bases de datos

**Y ahora describe tu requerimiento:**

- ¿Qué funcionalidad nueva necesitas?
- ¿Cuál es el objetivo o beneficio esperado?

Por favor responde:

1. **Tu perfil:** A o B
2. **Tu requerimiento:** Descripción de la funcionalidad

No te preocupes por los detalles técnicos ahora, solo una descripción general de lo que necesitas."

**REGISTRAR INTERNAMENTE la respuesta del perfil para condicionar todas las preguntas posteriores:**

- **Si responde A (Funcional Puro)**: SOLO hacer preguntas de negocio, flujos funcionales y criterios de aceptación
- **Si responde B (Técnico-Funcional)**: Permitir preguntas técnicas cuando sea necesario para completar la historia

#### 0.2 Validar Información Mínima

**BLOQUEO OBLIGATORIO:** No continuar hasta obtener:

- **Caracterización del perfil**: A o B claramente especificado
- **Descripción de la funcionalidad**: Qué funcionalidad nueva necesita
- **Propósito o beneficio básico**: Objetivo o beneficio esperado

**Si la información es insuficiente:**
"Necesito completar la información inicial para poder ayudarte. Por favor proporciona:

1. **Tu perfil:** ¿A (Funcional Puro) o B (Técnico-Funcional)?
2. **Funcionalidad:** ¿Qué funcionalidad específica necesitas?
3. **Objetivo:** ¿Quién la va a usar y para qué la necesita?"
   (esta información es una muestra de lo que debes pedir al usuario, pero hacer mas preguntas si lo consideras)

### 1. ANÁLISIS OBLIGATORIO DE CONTEXTO Y EXTRACCIÓN DE CONCEPTOS CLAVE

**🛑 PASO OBLIGATORIO - NO CONTINUAR SIN COMPLETAR**

**EJECUTAR INMEDIATAMENTE después de tener el requerimiento inicial del paso 0:**

#### 1.1 Cargar Configuración del Proyecto

**OBLIGATORIO antes de cualquier análisis:**

- Cargar `.ceiba-metodo/core-config.yaml`
- Extraer configuraciones: `architectureShardedLocation`, `architectureFile`
- Si no existe core-config.yaml, HALT: "core-config.yaml no encontrado. Ejecuta la instalación de BMad primero."

#### 1.2 Análisis Obligatorio de Documentación Arquitectónica

**BLOQUEO TOTAL: Este análisis es OBLIGATORIO antes de continuar al paso 2**

**1. Documentación arquitectónica existente (OBLIGATORIO):**

- **DEBE revisar** si existe arquitectura base (`{architectureShardedLocation}/index.md`)
- **DEBE buscar** documentación de componentes relacionados (`{architectureShardedLocation}/architecture-*.md`) para este paso usa varios comodines de busqueda para encontrar los flujos y la documentación de cada componente
- **DEBE revisar** flujos de negocio existentes que puedan estar relacionados (`{architectureShardedLocation}/flujo-*.md`) para este paso usa varios comodines de busqueda para encontrar los flujos y la documentación de cada componente

Importante leer todo lo que está en la carpeta `{architectureShardedLocation}/architecture-*.md`

**2. Historias de usuario existentes (OBLIGATORIO):**

- **DEBE revisar** historias existentes en `{devStoryLocation}` para identificar:
  - Funcionalidades similares o relacionadas
  - Patrones de criterios de aceptación aplicables
  - Actores y roles ya definidos en otras historias
  - Componentes ya mencionados en historias previas
  - Integraciones documentadas en historias anteriores
- **DEBE buscar** historias que involucren las mismas entidades de negocio
- **DEBE identificar** dependencias potenciales con historias ya implementadas
- **DEBE extraer** lecciones aprendidas de historias similares (notas de refinamiento, problemas encontrados)

**3. Funcionalidades similares en código base (OBLIGATORIO):** (para este paso debio leer y revisar la arquitectura)

- Buscar en el código base funcionalidades similares al requerimiento descrito
- Identificar patrones existentes relevantes

**4. Documentación relacionada (OBLIGATORIO):**

- Buscar documentación adicional relacionada con el requerimiento

#### 1.3 Extracción Obligatoria de Conceptos Clave

**DEBE extraer conceptos del requerimiento + contexto arquitectónico + historias existentes encontrados:**

- 🎯 **Actores y roles** involucrados (revisar flujos existentes y historias previas)
- 🔄 **Procesos y flujos** de negocio (identificar si ya están documentados o implementados)
- 📋 **Entidades y datos** manejados (revisar componentes existentes y historias relacionadas)
- 🏗️ **Módulos y componentes** potencialmente afectados (basado en documentación y historias previas)
- 🔗 **Integraciones** necesarias con sistemas existentes (revisar historias de integraciones similares)
- 📚 **Patrones identificados** en historias similares (criterios de aceptación, validaciones, flujos)
- ⚠️ **Lecciones aprendidas** de historias relacionadas (problemas conocidos, mejores prácticas)

#### 1.4 Documentar Hallazgos del Análisis

**OBLIGATORIO: Documentar internamente los resultados encontrados para usar en pasos posteriores:**

- Componentes arquitectónicos identificados
- Flujos de negocio relacionados
- **Historias relacionadas encontradas** y sus patrones aplicables
- **Dependencias con historias existentes** identificadas
- **Lecciones aprendidas** de historias similares (problemas, mejores prácticas)
- Patrones existentes aplicables (arquitectónicos y de negocio)
- Funcionalidades similares encontradas en código
- Gaps o áreas no documentadas

**⚠️ VERIFICACIÓN DE COMPLETITUD DE DOCUMENTACIÓN:**

Si NO se encuentra alguno de estos elementos críticos:

- ❌ Arquitectura base (`{architectureShardedLocation}/index.md`)
- ❌ Documentación de componentes (`architecture-*.md`, `component*.md`, etc.)
- ❌ Flujos de negocio documentados (`flujo-*.md`, `flow-*.md`, `proceso-*.md`, etc.)
  **ADVERTENCIA OBLIGATORIA AL USUARIO:**

"⚠️ **ADVERTENCIA: Documentación Incompleta Detectada**

No se encontró documentación completa en las siguientes áreas:

- [Lista específica de elementos faltantes]

**IMPACTO:** Sin esta documentación, el análisis puede ser menos preciso y las preguntas podrían no cubrir todos los aspectos arquitectónicos importantes.

**OPCIONES:**

1. **CONTINUAR** - Proceder con el análisis basado en exploración de código y preguntas adicionales
2. **PAUSAR** - Detener para documentar la arquitectura primero (recomendado para mejor precisión)

¿Deseas continuar sin la documentación completa o prefieres pausar para documentar la arquitectura primero?"

**BLOQUEO:** Esperar respuesta del usuario antes de continuar.

**REGLA CRÍTICA: NO continuar al paso 2 sin completar TODO este análisis obligatorio**

### 2. ANÁLISIS EXHAUSTIVO DE COMPLETITUD

**🔍 EVALUACIÓN CRÍTICA OBLIGATORIA: Revisar la información del requerimiento inicial + contexto arquitectónico encontrado y determinar si un desarrollador puede trabajar SIN ASUMIR NADA.**

**CRITERIOS OBJETIVOS DE SUFICIENCIA - TODOS deben estar claros:**

#### 2.1 Información de Usuario y Contexto (OBLIGATORIO)

- ❓ **¿Está claro QUIÉN específicamente puede realizar esta acción?** (rol exacto, no genérico)
- ❓ **¿Están definidos los PERMISOS específicos necesarios?**
- ❓ **¿Es evidente el VALOR DE NEGOCIO concreto?** (no solo "para mejorar")

#### 2.2 Funcionalidad Específica (OBLIGATORIO)

- ❓ **¿Está clara la ACCIÓN EXACTA que realiza el usuario?** (verbo específico + objeto)
- ❓ **¿Están definidos los DATOS de entrada con sus tipos y formatos?**
- ❓ **¿Están claras las VALIDACIONES y reglas de negocio?**

#### 2.3 Interfaz y Comportamiento (OBLIGATORIO)

- ❓ **¿Está claro DÓNDE se ubica en el sistema?** (pantalla específica)
- ❓ **¿Está definido el FLUJO DE NAVEGACIÓN del usuario?**
- ❓ **¿Están claros los MENSAJES de éxito y error específicos?**

#### 2.4 Casos Extremos y Errores (OBLIGATORIO)

- ❓ **¿Están definidos los comportamientos en CASOS DE FALLO?**
- ❓ **¿Están claros los escenarios con DATOS EXTREMOS?**

#### 2.5 Impacto en el Sistema (OBLIGATORIO si aplica)

- ❓ **¿Están claros los SISTEMAS o MÓDULOS que se ven afectados?** (basado en análisis de código)
- ❓ **¿Están definidas las INTEGRACIONES con otros procesos de negocio?**

**🚨 REGLA CRÍTICA: Si hay CUALQUIER "❓" sin respuesta clara, la información es INSUFICIENTE**

**DECISIÓN ESTRICTA:**

- **✅ TODOS los criterios claros** → Saltar al paso 5 (Historia Final)
- **❌ CUALQUIER criterio unclear** → OBLIGATORIO continuar al paso 3 (Preguntas)

**EJEMPLOS DE INFORMACIÓN INSUFICIENTE:**

- "Quiero que el usuario pueda subir archivos" (¿qué usuario? ¿qué tipos? ¿dónde? ¿para qué?)
- "Necesito un reporte" (¿de qué datos? ¿para quién? ¿en qué formato? ¿cuándo se genera?)
- "Agregar validación" (¿qué validación específica? ¿qué mensaje mostrar?)

**EJEMPLOS DE INFORMACIÓN SUFICIENTE:**

- "Como administrador de sistema, quiero subir archivos PDF de hasta 10MB en la pantalla de configuración para actualizar manuales de usuario, mostrando progreso de carga y mensaje de confirmación"

### 3. PREGUNTAS DETALLADAS

**PREREQUISITO:** Completar pasos 0, 1 y 2 antes de preguntar

**🚫 REGLAS CRÍTICAS ANTI-ASUNCIÓN:**

1. **NUNCA asumir** el tipo de usuario específico (no "usuario", sino "administrador", "cliente", "operador", etc.)
2. **NUNCA asumir** permisos o roles (preguntar explícitamente qué puede y no puede hacer)
3. **NUNCA asumir** formatos de datos (fechas, números, textos tienen formatos específicos)
4. **NUNCA asumir** comportamientos en errores (cada error debe tener comportamiento definido)
5. **NUNCA asumir** ubicación en la interfaz (preguntar pantalla exacta y navegación)
6. **NUNCA asumir** integraciones con otros sistemas (confirmar conexiones específicas)
7. **NUNCA asumir** que "es obvio" - si no está explícito, hay que preguntarlo

**DETECTORES DE ASUNCIÓN - Si piensas esto, DEBES preguntar:**

- "Seguramente el usuario se refiere a..." → ❌ PREGUNTA
- "Es lógico que sería..." → ❌ PREGUNTA
- "Probablemente necesita..." → ❌ PREGUNTA
- "Supongo que el flujo es..." → ❌ PREGUNTA
- "Debe ser similar a otras funcionalidades..." → ❌ PREGUNTA

**Mensaje introductorio obligatorio:**
"**Paso 3/6: Preguntas de aclaración**

Basándome en tu requerimiento inicial y el análisis del contexto existente, detecté información que no puedo asumir. Necesito aclarar estos detalles específicos para crear una historia completa:"

**FORMATO OBLIGATORIO: Solo preguntar sobre gaps identificados en el paso 2, usando preguntas numeradas y específicas:**

**GUÍA PARA PREGUNTAS INTELIGENTES (usar solo según gaps del paso 2):**

**⚠️ FILTRO OBLIGATORIO POR PERFIL TÉCNICO:**

**🎯 PARA PERFIL FUNCIONAL PURO (opción A):**

- ✅ **PERMITIDAS**: Preguntas de negocio, flujos funcionales, criterios de aceptación, validaciones de negocio
- ❌ **PROHIBIDAS**: Arquitectura, APIs, tecnologías, integraciones técnicas, estructura de datos

**🔧 PARA PERFIL TÉCNICO-FUNCIONAL (opción B):**

- ✅ **PERMITIDAS**: Todas las preguntas, incluyendo aspectos técnicos cuando sea necesario

#### Si falta información de Usuario y Contexto:

**Preguntar específicamente:**

1. "¿Qué tipo exacto de usuario realiza esta acción? (administrador, cliente, operador, etc.)"
2. "¿Qué permisos específicos necesita este usuario para realizar la acción?"
3. "¿Cuál es el beneficio concreto que obtiene el usuario al realizar esta acción?"

#### Si falta información de Funcionalidad:

**Preguntar específicamente:** 4. "¿Qué acción exacta realiza el usuario? (verbo específico + objeto específico)" 5. "¿Sobre qué datos específicos actúa? (tipo, formato, origen)" 6. "¿Qué validaciones exactas deben aplicarse? (ej: campo obligatorio, formato email, etc.)"

#### Si falta información de Interfaz:

**Preguntar específicamente:** 7. "¿En qué pantalla exacta se ubica esta funcionalidad?" 8. "¿Cómo navega el usuario para llegar a esta funcionalidad?" 9. "¿Qué elementos de interfaz específicos se necesitan? (botones, campos, etc.)"

#### Si falta información de Errores:

**Preguntar específicamente:** 10. "¿Qué mensaje exacto se muestra cuando la operación falla?" 11. "¿Qué sucede específicamente cuando los datos son inválidos?" 12. "¿Cómo se comporta el sistema con datos extremos? (vacíos, muy largos, etc.)"

#### Si falta información de Negocio (solo si es unclear después del análisis):

**Preguntar específicamente sobre aspectos de negocio:**

- "¿Esta funcionalidad afecta algún proceso de negocio existente?"
- "¿En qué parte del flujo de trabajo del usuario debería aparecer esta funcionalidad?"

**⚠️ REGLA CRÍTICA: Solo hacer las preguntas cuya respuesta NO esté clara en la información ya recopilada**

**ANTI-PATRÓN - NO hacer estas preguntas genéricas como por ejemplo:**

- ❌ "¿Qué tecnología prefieres usar?"
- ❌ "¿Qué base de datos usamos?"
- ❌ "¿Prefieres REST o GraphQL?"
- ❌ "¿Como debe ser la arquitectura del componente?"
- ❌ "¿Qué estructura de datos necesitas?"

**PATRÓN CORRECTO - Hacer preguntas específicas de negocio como por ejemplo:**

- ✅ "¿Qué campos específicos debe completar el usuario?"
- ✅ "¿Qué mensaje exacto se muestra al usuario cuando guarda exitosamente?"
- ✅ "¿Quién específicamente puede ver esta información?"

### 4. CONFIRMACIÓN OBLIGATORIA

**🛑 BLOQUEO TOTAL DEL PROCESO**

**NUNCA continuar hasta que el usuario responda TODAS las preguntas del paso 3**

**Mensaje obligatorio:**
"**Paso 4/6: Confirmación de respuestas**

Por favor, responde todas las preguntas numeradas antes de continuar con la creación de la historia de usuario."

### 5. HISTORIA DE USUARIO FINAL

#### 5.1 Configuración y Solicitud de Información

**Cargar configuración del proyecto:**

- Cargar `.ceiba-metodo/core-config.yaml`
- Extraer `devStoryLocation` para ubicación de historias
- Si no existe core-config.yaml, HALT: "core-config.yaml no encontrado. Ejecuta la instalación de BMad primero."

**Solicitar información de la historia al usuario:**

**Mensaje obligatorio:**
"**Paso 5/6: Información de la historia**

Para crear la historia de usuario, necesito que me proporciones:

1. **Número/Consecutivo de la historia**: ¿Qué número quieres asignar a esta historia?
2. **Nombre de la historia**: ¿Cómo quieres que se llame el archivo de la historia?

Ejemplo:

- Número: 42
- Nombre: crear-usuario-administrador

El archivo se creará como: `42.crear-usuario-administrador.story.md`"

**BLOQUEO:** Esperar respuesta del usuario con ambos datos antes de continuar.

**Validar y formatear información recibida:**

- **Nombre**: Aplicar formateo automático según estas reglas:
  - Convertir a lowercase (minúsculas)
  - Reemplazar espacios por guiones (-)
  - Eliminar tildes y caracteres especiales
  - Eliminar caracteres no alfanuméricos excepto guiones
  - Ejemplos de formateo:
    - "Crear Usuario Administrador" → "crear-usuario-administrador"
    - "Módulo de Pagos & Facturación" → "modulo-de-pagos-facturacion"
    - "Login con 2FA" → "login-con-2fa"

**Anunciar creación:**
"**Creando historia #{número}: {nombre formateado}**"

#### 5.2 Crear Archivo de Historia

- Crear archivo: `{devStoryLocation}/{número}.{nombre-formateado}.story.md`
- Usar template base de historia de usuario

**Formato del nombre del archivo:**

- `{número}.{nombre-formateado}.story.md`
- Ejemplo: `42.crear-usuario-administrador.story.md`

#### 5.3 Contenido de la Historia

```markdown
# Historia #{número}: {Título basado en nombre proporcionado}

## Estado: Borrador (PO)

## Historia de Usuario

**Como** {usuario específico}
**Quiero** {acción concreta y específica}
**Para** {valor de negocio claro}

## Descripción

{Contexto general de la funcionalidad, incluyendo el módulo o área del sistema donde se implementará}

## Criterios de Aceptación

**Escenario 1: Flujo Principal**

- **Dado** que {precondición específica}
- **Cuando** {acción exacta del usuario}
- **Entonces** {resultado esperado específico}

**Escenario 2: Validaciones**

- **Dado** que {condición de datos inválidos}
- **Cuando** {usuario intenta la acción}
- **Entonces** {mensaje de error específico y comportamiento}

**Escenario 3: Casos Extremos** (si aplica)

- **Dado** que {condición extrema}
- **Cuando** {acción del usuario}
- **Entonces** {comportamiento esperado}

{Agregar más escenarios según la complejidad}

## Información Recopilada

### Usuario y Contexto

- **Tipo de usuario:** {especificación del rol}
- **Permisos requeridos:** {lista de permisos}
- **Valor de negocio:** {beneficio específico}

### Datos y Validaciones

- **Tipos de datos:** {formatos específicos}
- **Validaciones:** {reglas de validación}
- **Casos de error:** {escenarios de fallo}

### Interfaz y Navegación

- **Ubicación:** {módulo/pantalla específica}
- **Flujo de navegación:** {pasos del usuario}
- **Integraciones:** {conexiones con otros sistemas }
- **Componentes afectados:** {lista de componentes según análisis arquitectónico}

### Análisis de Documentación Existente

- **GPS Arquitectónico:** {referencia a secciones relevantes del index.md}
- **Componentes relacionados:** {lista de componentes documentados que se ven afectados}
- **Flujos de negocio:** {flujos existentes que se conectan con esta funcionalidad}
- **Patrones identificados:** {patrones arquitectónicos aplicables según documentación}

### Análisis de Historias Relacionadas

- **Historias similares encontradas:** {lista de historias que implementan funcionalidades similares}
- **Patrones de criterios de aceptación:** {patrones reutilizables identificados en historias previas}
- **Dependencias identificadas:** {historias existentes que esta nueva historia podría afectar o depender}
- **Lecciones aprendidas:** {problemas conocidos o mejores prácticas extraídas de historias similares}
- **Actores ya definidos:** {roles de usuario ya establecidos en historias previas}
- **Componentes mencionados:** {componentes ya referenciados en otras historias}

## Notas para Refinamiento

{Cualquier información adicional, dudas pendientes, o consideraciones técnicas básicas que el SM debe tener en cuenta durante el refinamiento}

## Definición de Terminado (Inicial)

- [ ] Funcionalidad implementada según criterios de aceptación
- [ ] Validaciones funcionando correctamente
- [ ] Mensajes de error apropiados
- [ ] Pruebas unitarias y de integración
- [ ] Revisión de código completada
- [ ] Documentación actualizada si es necesario

---

**Creado por:** PO
**Fecha:** {fecha actual}
**Requiere refinamiento técnico:** ✅ Pendiente por SM
```

### 6. CONFIRMACIÓN FINAL Y ENTREGA

**Mensaje exacto obligatorio:**
"**Paso 6/6: Historia completada**

Historia de usuario #{número} creada exitosamente en `{devStoryLocation}/{número}.{nombre-formateado}.story.md`

**Resumen:**

- **Número:** {número proporcionado por usuario}
- **Nombre del archivo:** {nombre-formateado}.story.md
- **Título:** {título de la historia}
- **Usuario objetivo:** {tipo de usuario}
- **Funcionalidad:** {descripción breve}
- **Criterios de aceptación:** {número} escenarios definidos
- **Estado:** Borrador (PO) - Lista para refinamiento técnico

**Próximos pasos:**

1. **🏗️ ANÁLISIS ARQUITECTÓNICO PRIMERO:** El Arquitecto debe usar el comando `*analisis-y-diseno` para evaluar el impacto de esta historia en la arquitectura existente
2. **🔧 Refinamiento técnico:** El Scrum Master debe usar la tarea `refine-story` para añadir contexto técnico basado en el análisis arquitectónico
3. **📊 Estimación:** Posterior estimación con la tarea `estimate-story`
4. **💻 Desarrollo:** Desarrollo por el Dev Agent con claridad arquitectónica

¿La historia está completa según tu expectativa o necesita algún ajuste?"

## Criterios de Calidad para la Historia

- **✅ Testeable**: Criterios verificables y medibles
- **✅ Estimable**: Funcionalidad clara con complejidad definida
- **✅ Valuable**: Beneficio de negocio evidente y específico
- **✅ Específico**: Sin ambigüedades ni suposiciones
- **✅ Completo**: Toda la información necesaria recopilada
- **✅ Enfocado**: Una funcionalidad específica por historia

## Reglas de Comportamiento OBLIGATORIAS

1. **CARACTERIZACIÓN DEL PERFIL TÉCNICO**: OBLIGATORIAMENTE capturar en el paso 1/6 si el usuario es perfil A (Funcional Puro) o B (Técnico-Funcional) y registrar internamente para filtrar todas las preguntas posteriores. Para perfil A: SOLO preguntas de negocio, PROHIBIDAS preguntas técnicas, ejemplo: (arquitectura, APIs, tecnologías, integraciones técnicas, estructura de datos). Para perfil B: permitir preguntas técnicas cuando sea necesario
2. **CAPTURA INICIAL**: SIEMPRE empezar solicitando el requerimiento del usuario antes de buscar contexto
3. **ANÁLISIS OBLIGATORIO**: SIEMPRE ejecutar completamente el paso 1 (análisis de arquitectura) después del paso 0, sin excepciones
4. **ANÁLISIS CRÍTICO DE COMPLETITUD**: OBLIGATORIAMENTE ejecutar el paso 2 con todos los criterios objetivos antes de decidir si preguntar
5. **ADVERTENCIA DE DOCUMENTACIÓN FALTANTE**: Si no se encuentra arquitectura base, componentes documentados o flujos de negocio, OBLIGATORIAMENTE advertir al usuario sobre la precisión limitada y solicitar confirmación para continuar
6. **SECUENCIA ESTRICTA**: Ejecutar paso 0 (captura) → 1 (contexto OBLIGATORIO) → 2 (análisis CRÍTICO) → 3 (preguntas ESPECÍFICAS) → 4 (confirmación) → 5-6 (configuración, historia y entrega)
7. **DETECCIÓN ANTI-ASUNCIÓN**: Si piensas "seguramente se refiere a...", "es lógico que...", "probablemente necesita..." → OBLIGATORIO preguntar específicamente
8. **NO ASUMIR INFORMACIÓN CRÍTICA**: NUNCA asumir tipos de usuario, permisos, formatos de datos, comportamientos de error, ubicación en interfaz, o integraciones
9. **PREGUNTAS INTELIGENTES**: Solo preguntar sobre gaps reales identificados en el paso 2, aplicando filtro por perfil técnico del paso 1
10. **HISTORIAS EXISTENTES: ANALIZAR SIEMPRE**: OBLIGATORIAMENTE revisar historias existentes para identificar patrones, dependencias y lecciones aprendidas antes de crear la nueva historia
11. **COMPONENTES: PREGUNTAR SIEMPRE**: Si hay dudas sobre qué componentes afectar después del análisis arquitectónico, OBLIGATORIAMENTE preguntar al usuario. Es mejor preguntar que asumir componentes incorrectos
12. **BLOQUEO TOTAL**: No continuar sin respuestas completas a preguntas (paso 4)
13. **UNA HISTORIA**: Entregar una única historia completa y detallada (paso 5)
14. **MENSAJE EXACTO**: Usar confirmación final específica con numeración de pasos (paso 6)
15. **ARCHIVO ÚNICO**: Crear un solo archivo que será refinado posteriormente
16. **ESTADO CLARO**: Marcar como "Borrador (PO)" para indicar que requiere refinamiento

## Notas Importantes

- Esta tarea se enfoca en la **recopilación completa de requisitos funcionales SIN ASUMIR NADA**
- **SIEMPRE inicia capturando el requerimiento** antes de buscar contexto del proyecto
- **El paso 1 (análisis de arquitectura) es OBLIGATORIO** y no puede omitirse
- **El paso 2 (análisis crítico) es el FILTRO CLAVE** para detectar información faltante vs. asumida
- **ADVERTENCIA CRÍTICA**: Sin documentación completa (arquitectura base, componentes, flujos, **historias existentes**), el resultado será menos preciso. Siempre avisar al usuario y obtener confirmación para continuar
- **HISTORIAS COMO CONTEXTO**: Las historias existentes son tan importantes como la arquitectura para crear historias consistentes y evitar duplicación
- **PATRONES REUTILIZABLES**: Aprovechar patrones de criterios de aceptación y estructuras de historias similares
- **DEPENDENCIAS CRÍTICAS**: Identificar dependencias con historias ya implementadas evita conflictos futuros
- **PRINCIPIO ANTI-ASUNCIÓN**: Si la información no está explícitamente definida, hay que preguntarla. No hay "información obvia"
- **PREGUNTAS INTELIGENTES**: Solo sobre gaps reales de negocio, no sobre detalles técnicos de implementación
- **COMPONENTES: PREGUNTAR ES MEJOR QUE ASUMIR** - Si después del análisis arquitectónico hay dudas sobre componentes, siempre preguntar al usuario
- **No incluye análisis técnico profundo** (eso lo hace el SM en `refine-story`)
- **No asume conocimientos técnicos** del stakeholder
- **Prioriza la comprensión del negocio** sobre la implementación
- El archivo creado será **la base para todo el trabajo posterior** del equipo
- **La numeración de pasos ayuda al usuario** a entender el progreso del proceso
- **El análisis de contexto arquitectónico es fundamental** para hacer preguntas inteligentes
- **La precisión en componentes es crítica** para el éxito de la implementación posterior
- **DOCUMENTACIÓN INCOMPLETA = RESULTADO MENOS PRECISO**: Siempre informar al usuario sobre las limitaciones
- **CALIDAD > VELOCIDAD**: Es mejor hacer preguntas específicas que crear historias con asunciones incorrectas
