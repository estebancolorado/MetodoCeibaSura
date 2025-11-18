# Criterios de Inclusión en Knowledge Base

## 🎯 Propósito de la Knowledge Base

La Knowledge Base (KB) documenta incidentes relevantes para facilitar:

- **Búsqueda rápida** de soluciones a problemas similares
- **Aprendizaje del equipo** de experiencias pasadas
- **Reutilización de soluciones** en otros contextos
- **Prevención proactiva** de errores recurrentes
- **Onboarding de nuevos miembros** con casos reales

---

## ✅ Criterios de Inclusión

Un incidente se documenta en la KB si cumple **AL MENOS UNO** de estos criterios:

### 1. Incidente P0-P1 (Obligatorio)

**Criterio**: Todos los incidentes de severidad P0 (Crítico) o P1 (Alto) DEBEN documentarse en KB.

**Justificación**:
- Impacto crítico en negocio requiere máxima visibilidad
- Prevenir recurrencia es prioritario
- Equipo completo debe conocer lecciones de incidentes mayores

**Evaluación**: Automática (si `severity == P0 || severity == P1` → KB = SÍ)

---

### 2. Error Recurrente o con Patrón Común

**Criterio**: El error ha ocurrido antes (mismo síntoma o causa raíz similar) O representa un patrón común de falla.

**Ejemplos**:
- Error de autenticación que ocurrió 3 veces en los últimos 6 meses
- Timeout en API externa que ocurre frecuentemente
- Race condition en proceso de checkout que se repite

**Señales de patrón común**:
- Múltiples incidentes con misma causa raíz en 5 Whys
- Error está en categoría con alta incidencia (ej: integraciones externas)
- Equipo reconoce "ya vimos esto antes"

**Evaluación**: Revisar incidentes pasados para identificar recurrencia

---

### 3. Solución Compleja o No Obvia

**Criterio**: La solución requirió análisis profundo, debugging extenso, o enfoque creativo.

**Ejemplos**:
- Bug que requirió 3+ días de investigación
- Solución que involucró múltiples componentes o sistemas
- Fix que requirió entender interacción sutil entre módulos
- Workaround creativo debido a limitación externa

**Señales de complejidad**:
- Tiempo de diagnóstico > 4 horas para P1 (o > 50% del tiempo de resolución)
- Análisis 5 Whys llegó a 5to nivel (causa raíz no inmediata)
- Solución requirió research en documentación externa o código fuente de librería

**Evaluación**: Revisar sección 2 (Diagnóstico) y 6 (Implementación) para medir complejidad

---

### 4. Aprendizaje Valioso para el Equipo

**Criterio**: El incidente genera lecciones importantes que el equipo completo debe conocer.

**Ejemplos**:
- Expuso gap en proceso (ej: code review sin checklist de breaking changes)
- Reveló limitación de herramienta o infraestructura
- Enseñó nueva técnica o metodología (ej: efectividad de 5 Whys)
- Generó cambio en política o estándar del equipo

**Señales de aprendizaje valioso**:
- Sección "Lessons Learned" tiene 4+ lecciones importantes
- Medidas preventivas generan cambios en procesos del equipo
- Incidente provocó discusión significativa en retrospectiva

**Evaluación**: Revisar sección 8.4 (Lessons Learned) para calidad de aprendizajes

---

### 5. Aplicable a Otros Contextos

**Criterio**: La solución puede reutilizarse en otros módulos, proyectos o contextos fuera del incidente específico.

**Ejemplos**:
- Patrón de validación de JWT aplicable a cualquier auth module
- Estrategia de retry con backoff exponencial reutilizable en APIs
- Approach de migración de datos aplicable a otras migraciones
- Configuración de monitoring aplicable a otros endpoints críticos

**Señales de aplicabilidad**:
- Solución es genérica (no hardcoded a caso específico)
- Código puede extraerse como utility/helper function
- Patrón es best practice reconocido en industria
- Otros equipos o proyectos podrían beneficiarse

**Evaluación**: Pregunta clave: "¿Otro developer podría usar esta solución en un contexto diferente?"

---

## 📋 Proceso de Evaluación

### Paso 1: Evaluar Criterios Automáticos

```
SI severity == P0 O P1 ENTONCES
  KB = SÍ (obligatorio)
  SALTAR a Paso 3 (Clasificación)
SINO
  CONTINUAR a Paso 2
FIN SI
```

### Paso 2: Evaluar Criterios Opcionales (para P2-P4)

Para cada criterio opcional (2-5), responder:

- **Error Recurrente**: ¿Ha ocurrido antes o es patrón común? (SÍ/NO)
- **Solución Compleja**: ¿Requirió análisis profundo o enfoque creativo? (SÍ/NO)
- **Aprendizaje Valioso**: ¿Genera lecciones importantes para equipo? (SÍ/NO)
- **Aplicable a Otros Contextos**: ¿Solución reutilizable? (SÍ/NO)

**Decisión**:
- Si **AL MENOS 2** criterios opcionales = SÍ → KB = SÍ
- Si **SOLO 1** criterio = SÍ → KB = MAYBE (a criterio del Architect)
- Si **TODOS** = NO → KB = NO

### Paso 3: Clasificación para KB (solo si KB = SÍ)

Definir:

**Categoría Principal**:
- Seguridad
- Rendimiento
- Integración
- Datos
- Infraestructura
- Configuración
- Código
- UI/UX

**Tipo de Error** :
- Código
- Datos
- Infraestructura
- Seguridad
- Configuración
- Rendimiento
- Integración
- Mixto

**Etiquetas** (mínimo 3, máximo 7):
- Formato: `#etiqueta-en-minusculas`
- Incluir: tecnología (#jwt, #redis), dominio (#authentication), impacto (#breaking-change)
- Ejemplos: `#jwt #authentication #breaking-change #hotfix #jsonwebtoken`

**Palabras Clave** (para búsqueda):
- 5-10 palabras que alguien buscaría para encontrar este caso
- Incluir: error messages clave, nombres de tecnologías, síntomas comunes
- Ejemplos: `JWT, algorithm, HS256, RS256, invalid signature, breaking change`

---

## 📝 Documentación en KB (Sección 8.5)

Si KB = SÍ, completar sección 8.5 en el archivo del incidente con:

### KB Metadata

```markdown
**KB Metadata**:
- **Categoría**: [Categoría principal]
- **Tipo de Error**: [Tipo del Error]
- **Etiquetas**: #tag1 #tag2 #tag3 ...
- **Palabras Clave**: keyword1, keyword2, keyword3 ...
```

### Descripción del Problema

Resumen en 2-3 oraciones de:
- Síntomas clave observados
- Impacto en sistema/usuarios
- Contexto relevante

### Causa Raíz Documentada

Copiar análisis 5 Whys con la causa raíz final destacada.

### Solución Implementada

Incluir:
- Código clave del fix (snippets más importantes )
- Enfoque técnico usado
- Archivos modificados

### Resultados y Validación

Métricas de éxito:
- Tiempo de resolución
- Tests implementados (cantidad y coverage)
- Validación de escenarios de fallo
- Métricas de negocio (si aplican)

### Aprendizajes y Prevención

Resumen de Lessons Learned y Medidas Preventivas más importantes (top 3-5).

### Aplicabilidad a Otros Contextos

Descripción de dónde más puede aplicarse esta solución:
- Otros módulos del mismo proyecto
- Otros proyectos del equipo
- Otros contextos o tecnologías similares
- Best practices generales

---

## 🔄 Actualización de Índices

Una vez documentado en sección 8.5, actualizar:

### index-by-category.md

Añadir entrada en la categoría correspondiente:

```markdown
| 042 | JWT Breaking Change en Login | P1 | Código | #jwt #authentication #breaking-change | [Link](../incidents/042.incident.md#85-knowledge-base) |
```

### index-by-tags.md

Añadir el incidente a cada etiqueta usada:

```markdown
## #jwt
- [042 - JWT Breaking Change en Login](../incidents/042.incident.md#85-knowledge-base) (P1)

## #authentication
- [042 - JWT Breaking Change en Login](../incidents/042.incident.md#85-knowledge-base) (P1)
```

---

## 🎯 Ejemplos de Evaluación

### Ejemplo 1: Incidente P0 - Data Loss

**Criterios**:
- P0: ✅ SÍ (obligatorio)

**Decisión**: KB = SÍ (automático)

---

### Ejemplo 2: Incidente P3 - UI Bug Menor

**Criterios**:
- P3: ❌ (no obligatorio)
- Error Recurrente: ❌ NO (primera vez)
- Solución Compleja: ❌ NO (fix simple en CSS)
- Aprendizaje Valioso: ❌ NO (bug trivial)
- Aplicable a Otros Contextos: ❌ NO (específico a un componente)

**Decisión**: KB = NO (0 criterios opcionales cumplidos)

---

### Ejemplo 3: Incidente P2 - Race Condition en Checkout

**Criterios**:
- P2: ❌ (no obligatorio)
- Error Recurrente: ✅ SÍ (ocurrió 2 veces antes)
- Solución Compleja: ✅ SÍ (requirió debug con profiler + research de concurrency patterns)
- Aprendizaje Valioso: ✅ SÍ (lección sobre race conditions en async code)
- Aplicable a Otros Contextos: ✅ SÍ (patrón de lock/mutex reutilizable)

**Decisión**: KB = SÍ (4/4 criterios opcionales cumplidos)

---

### Ejemplo 4: Incidente P2 - Config Incorrecta

**Criterios**:
- P2: ❌ (no obligatorio)
- Error Recurrente: ❌ NO
- Solución Compleja: ❌ NO (solo cambiar valor en .env)
- Aprendizaje Valioso: ✅ SÍ (expuso gap en proceso de deploy)
- Aplicable a Otros Contextos: ✅ SÍ (checklist de validación de config reutilizable)

**Decisión**: KB = MAYBE (2/4 criterios cumplidos - a criterio del Architect)

**Recomendación**: KB = SÍ si el checklist de validación es significativo y reutilizable

---

_Estos criterios son usados del workflow de Post-Mortem para determinar si un incidente debe documentarse en la Knowledge Base._
