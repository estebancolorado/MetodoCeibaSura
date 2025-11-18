# Checklist - Estimar Historia de Usuario

## 🚦 Pre-requisitos del Flujo

**CRÍTICO**: La historia DEBE estar refinada antes de estimar.

### Secciones Obligatorias Completadas

- [ ] **Sección "## Refinamiento Técnico (Developer)"** existe y está completa
  - [ ] Nivel de complejidad documentado (BAJA/MEDIA/ALTA)
  - [ ] Justificación de complejidad presente
  - [ ] Riesgos técnicos conocidos identificados
  - [ ] Estrategia de testing definida

- [ ] **Sección "## Tareas de Implementación (Developer)"** existe y NO está vacía
  - [ ] Al menos 1 Fase documentada
  - [ ] Tareas principales con formato: `- [ ] **Tarea Principal**`
  - [ ] Subtareas con sangría documentan detalles de implementación

---

## ✅ Extracción de Tareas

### Identificación de Tareas Principales

- [ ] **Solo se extraen tareas principales** (formato: `- [ ] **Tarea**`)
- [ ] **Subtareas con sangría se IGNORAN** (son checklist, no se estiman)
- [ ] Número de tareas principales identificado correctamente

**Ejemplo Correcto**:
```markdown
- [ ] **Crear 5 tests de validación** → ✅ SE ESTIMA
  - [ ] Test sábado → ❌ NO SE ESTIMA (es checklist)
  - [ ] Test domingo → ❌ NO SE ESTIMA (es checklist)
```

---

## 📊 Estimación PERT

### Por Cada Tarea Principal

- [ ] **Escenario Optimista (O)** calculado con contexto de precedentes
- [ ] **Escenario Más Probable (M)** considera complejidad documentada
- [ ] **Escenario Pesimista (P)** incluye riesgos documentados
- [ ] **Estimación Senior = (O + 4×M + P) ÷ 6** calculada correctamente
- [ ] **Rango de Incertidumbre (P - O)** documentado

### Multiplicadores por Seniority

- [ ] **Complejidad extraída del refinamiento** (no re-calculada)
- [ ] **Multiplicadores aplicados correctamente** según complejidad

---

## 🤖 Clasificación: IA vs Manual

### Tareas Aumentadas por IA

- [ ] **Solo tareas de escribir/generar código** clasificadas como aumentadas por IA
- [ ] Método Ceiba (60% descuento) aplicado SOLO a estas tareas
- [ ] Incluidas en **array "tareas"** (tabla principal)

### Tareas Manuales

- [ ] **Tareas manuales identificadas correctamente, ejemplo de algunas**:
  - [ ] Ejecución de comandos (gradlew test, npm run build)
  - [ ] Operaciones Git (crear PR, merge, aprobar)
  - [ ] Ejecución de pipelines y despliegues
  - [ ] Configuraciones manuales en servidores
  - [ ] Coordinación con equipos externos
  - [ ] Aprobaciones de seguridad/compliance

- [ ] Tareas manuales incluidas en **array "tareas_manuales"** (separado)
- [ ] Tiempo Senior usado sin variación por seniority
- [ ] NO incluidas en tabla principal de estimación

**Criterio de Validación**:
⚠️ Si **TODAS** las tareas están en "aumentadas por IA" → REVISAR
- Probablemente hay tareas manuales no identificadas (ejecución de tests, PRs, deploys)

---

## 📈 Consolidación de Totales

### Tabla Principal (Tareas Aumentadas por IA)

- [ ] Columnas completas: #, Tarea, Complejidad, Jr, Semi Sr, Senior, MC Jr, MC Semi Sr, MC Sr
- [ ] Totales sumados correctamente por columna
- [ ] Valores redondeados a 1 decimal (ej: 12.5h)
- [ ] Porcentaje de optimización MC vs Tradicional calculado por perfil

### Tareas Manuales (Si existen)

- [ ] Array separado con: numero, descripcion, tiempo_estimado
- [ ] total_tareas_manuales = suma de todos los tiempos
- [ ] Totales de desarrollo calculados:
  - [ ] total_desarrollo_junior = total_mc_junior + total_tareas_manuales
  - [ ] total_desarrollo_semi_sr = total_mc_semi_sr + total_tareas_manuales
  - [ ] total_desarrollo_senior = total_mc_senior + total_tareas_manuales

---

## 📝 Generación de Sección

### Template Procesado

- [ ] **Todas las variables coinciden** con nombres en template.md
- [ ] Comentarios HTML removidos de secciones completadas
- [ ] Idioma del documento coincide con {document_output_language}
- [ ] Sección "## Estimación" generada sin errores

### Integración en Historia

- [ ] Sección añadida al final del archivo (antes de notas/logs)
- [ ] Contenido original intacto
- [ ] Estado actualizado a: **"Estimado (Developer)"**
- [ ] Metadata de estimación presente (fecha, estimador)

---

## 🎯 Validación Final

### Coherencia de Estimación

- [ ] **Tiempo total realista** considerando complejidad documentada
- [ ] Tareas con alta incertidumbre (riesgo > 3h) identificadas
- [ ] Ratio MC vs Tradicional coherente (~60% optimización en tareas aumentadas)

### Comparación con Refinamiento

- [ ] Número de tareas principales estimadas ≤ Número de tareas principales en refinamiento
- [ ] Complejidad usada en estimación = Complejidad documentada en refinamiento
- [ ] Riesgos considerados en escenario Pesimista = Riesgos documentados en refinamiento

---
_Este checklist es usado por el Developer para validar que la estimación está completa y coherente con el refinamiento previo._
