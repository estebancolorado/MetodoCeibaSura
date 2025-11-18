# Checklist - Documentar Incidente (Post-Mortem)

## 🚦 Validación de Pre-requisitos del Flujo

**CRÍTICO**: Antes de ejecutar el post-mortem, valida que se hayan completado los pasos obligatorios previos.

### Estado del Incidente

- [ ] **Status del incidente es válido para Post-Mortem**
  - ✅ Status = "Implementado" (Sección 6 completada)
  - ✅ Status = "Testeado" (Sección 7 completada - PASS o CONCERNS)
  - ❌ Si Status = "Pendiente", "Triaged", o "En Diagnóstico" → **NO continuar**

**⚠️ ADVERTENCIA**: Si el incidente NO está en estado "Implementado" o "Testeado", debes ejecutar los pasos obligatorios previos antes de realizar el post-mortem.

---

### Pasos Obligatorios Completados

Verifica que las siguientes secciones del archivo de incidente estén completadas:

**Recepción del Error** (Obligatorio)
- [ ] Sección 1 completada con toda la información del error

**Diagnóstico** (Obligatorio)
- [ ] Sección 2 completada con:
  - [ ] Validación de Error Real (no es Falso Positivo ni Funcionalidad No Contemplada)
  - [ ] Root Cause Analysis (5 Whys) documentado
  - [ ] Clasificación de Tipo de Error definida
  - [ ] Escenarios de Fallo documentados

**Refinamiento** (Obligatorio)
- [ ] Sección 4 completada con:
  - [ ] Tareas Técnicas Detalladas
  - [ ] Criterios de Aceptación Técnicos
  - [ ] Estrategia de Testing

**Implementación** (Obligatorio)
- [ ] Sección 6 completada con:
  - [ ] Status = "Completado"
  - [ ] Código del Fix Implementado documentado
  - [ ] Tests Unitarios realizados
  - [ ] Pull Request creado

**Revisión por Pares** (Obligatorio)
- [ ] Sección 7 completada con:
  - [ ] Status = "Completado"
  - [ ] Quality Gate Validation ejecutada
  - [ ] Decisión = "PASS" o "CONCERNS" (NO "FAIL")

**PASOS OPCIONALES** (Validar si aplicaron):

- [ ] **Análisis y Diseño** - Verificar si Status = "No Aplica" o "Completado"
  - Si Status = "Pendiente" → ⚠️ Advertencia: Análisis arquitectónico incompleto

- [ ] **Estimación** - Verificar si Status = "No Aplica - P0-P1" o "Completado"
  - Si Status = "Pendiente" para P2-P4 → ⚠️ Advertencia: Estimación pendiente

---

**Criterio de Bloqueo**:

🚫 **NO CONTINUAR con Post-Mortem si**:
- Status del incidente ≠ "Implementado" o "Testeado"
- Sección 6 (Implementación) Status ≠ "Completado"
- Sección 7 (Revisión) Status ≠ "Completado" o Decisión = "FAIL"

**Mensaje de Error**:
```
❌ PRE-REQUISITOS NO CUMPLIDOS PARA POST-MORTEM

El incidente no ha completado los pasos obligatorios del flujo de soporte.

Pasos faltantes detectados:
- [Lista de secciones con Status != Completado]

Acción requerida:
1. Completar (Implementación) - Comando: *desarrollar-historia-usuario
2. Completar (Revisión por Pares) - Comando: *revisar-historia
3. Asegurar que la Revisión obtuvo PASS o CONCERNS (no FAIL)

Una vez completados todos los pasos, podrás ejecutar el Post-Mortem.
```

---

## ✅ Validación de Completitud

### Timeline del Incidente

- [ ] **Timeline incluye TODOS los eventos clave** del ciclo de vida
  - Reported Date (Sección 1)
  - Fecha de Diagnóstico (Sección 2)
  - Fecha de Análisis/Diseño (Sección 3 - SI Status == "Completado")
  - Fecha de Refinamiento (Sección 4)
  - Fecha de Estimación (Sección 5 - SI Status == "Completado")
  - Fecha de inicio de Implementación (Sección 6)
  - Fecha de Code Review (Sección 7)
  - Fecha de Deploy (Sección 7)
  - Fecha de Validación (Sección 7)
  - Fecha de Cierre

- [ ] **Cada evento tiene timestamp preciso** (fecha y hora)

- [ ] **Cada evento identifica responsable** (rol o nombre)

- [ ] **Timeline está en orden cronológico**

- [ ] **Secciones opcionales validadas correctamente** (3 y 5 solo si Status == "Completado")

**Scoring**:
- 4 puntos: Todos los eventos con timestamp y responsable, incluyendo opcionales si aplicaron
- 3 puntos: Todos los eventos obligatorios, algunos sin timestamp exacto
- 2 puntos: Faltan 1-2 eventos menores o eventos opcionales no validados correctamente
- 1 punto: Timeline incompleta con gaps significativos
- 0 puntos: Timeline ausente o mínima

**Puntuación**: ___/4

---

### What Went Wrong

- [ ] **Identifica AL MENOS 3 fallas o errores** cometidos

- [ ] **Cada falla está claramente descrita** con contexto

- [ ] **Cada falla documenta su impacto** en el incidente

- [ ] **Fallas están relacionadas con causa raíz**

- [ ] **Incluye fallas de proceso, no solo técnicas**
  - Ejemplos: Code review insuficiente, falta de tests, alertas tardías, deploy sin validación

**Scoring**:
- 4 puntos: 4+ fallas bien documentadas con impacto y relación a causa raíz
- 3 puntos: 3 fallas bien documentadas
- 2 puntos: 2 fallas o fallas sin impacto documentado
- 1 punto: 1 falla mínimamente descrita
- 0 puntos: Sección ausente o vacía

**Puntuación**: ___/4

---

### What Went Right

- [ ] **Identifica AL MENOS 2 aciertos** en el manejo del incidente

- [ ] **Cada acierto está claramente descrito**

- [ ] **Aciertos cubren diferentes aspectos**:
  - Procesos (ej: escalación rápida)
  - Decisiones (ej: uso de 5 Whys)
  - Herramientas (ej: monitoreo efectivo)
  - Comunicación (ej: updates regulares)

- [ ] **Aciertos son replicables** en futuros incidentes

**Scoring**:
- 4 puntos: 3+ aciertos bien documentados en diferentes aspectos
- 3 puntos: 2 aciertos bien documentados
- 2 puntos: 1 acierto o aciertos vagos
- 1 punto: Aciertos mínimos sin detalle
- 0 puntos: Sección ausente o vacía

**Puntuación**: ___/4

---

### Lessons Learned

- [ ] **Identifica AL MENOS 3 lecciones aprendidas**

- [ ] **Cada lección es ESPECÍFICA y ACTIONABLE**
  - ❌ MAL: "Mejorar testing"
  - ✅ BIEN: "Implementar integration tests end-to-end para flujos críticos de autenticación"

- [ ] **Lecciones son GENERALIZABLES** más allá del incidente específico

- [ ] **Lecciones están basadas** en "What Went Wrong" y "What Went Right"

- [ ] **Cada lección tiene explicación detallada** de por qué es importante

**Scoring**:
- 5 puntos: 5+ lecciones específicas, actionables y generalizables con explicación
- 4 puntos: 3-4 lecciones de calidad
- 3 puntos: 3 lecciones, algunas vagas
- 2 puntos: 2 lecciones mínimas
- 1 punto: 1 lección genérica
- 0 puntos: Sección ausente o lecciones vagas

**Puntuación**: ___/5

---

### Medidas Preventivas

- [ ] **Cada lección aprendida tiene AL MENOS UNA medida preventiva** asociada

- [ ] **Cada medida es CONCRETA y ESPECÍFICA**
  - Cambio en proceso definido
  - Herramienta a implementar
  - Política a establecer
  - Training necesario

- [ ] **Cada medida tiene responsable identificado** (rol o nombre)

- [ ] **Cada medida tiene fecha objetivo** de implementación

- [ ] **Medidas son VIABLES** (no idealistas o imposibles de implementar)

**Scoring**:
- 5 puntos: Todas las medidas con descripción, responsable y fecha objetiva
- 4 puntos: Medidas completas, algunas sin responsable/fecha
- 3 puntos: Medidas presentes pero incompletas
- 2 puntos: Medidas vagas o sin seguimiento
- 1 punto: Medidas mínimas
- 0 puntos: Sección ausente

**Puntuación**: ___/5

---

### Knowledge Base (si aplica)

**Evaluación de Elegibilidad**:

- [ ] **Decisión de KB está JUSTIFICADA** (SÍ o NO con razones)

- [ ] **Criterios de inclusión están evaluados**:
  - P0-P1 (obligatorio)
  - Error recurrente
  - Solución compleja
  - Aprendizaje valioso
  - Aplicable a otros contextos

**Si KB = SÍ**:

- [ ] **Categoría KB asignada** (Seguridad, Rendimiento, Integración, etc.)

- [ ] **Tipo de Error documentado** (Código, Datos, Infraestructura, etc.)

- [ ] **Etiquetas definidas** (mínimo 3, máximo 7 en formato #tag)

- [ ] **Palabras clave searchables** definidas

- [ ] **Sección 8.5 Knowledge Base está COMPLETA**:
  - KB Metadata
  - Descripción del Problema
  - Causa Raíz Documentada (con 5 Whys)
  - Solución Implementada (código clave)
  - Resultados y Validación
  - Aprendizajes y Prevención
  - Aplicabilidad a Otros Contextos

- [ ] **Índices de KB actualizados Y VALIDADOS** (Sub-step 9.5):
  - ✅ `index-by-category.md` actualizado con entrada del incidente
  - ✅ `index-by-tags.md` actualizado con entrada bajo CADA tag
  - ✅ Validación de integridad pasó (Step 9.5)
  - ✅ Enlaces funcionan correctamente (#85-knowledge-base existe)
  - ✅ Formato de entradas es correcto
  - ✅ Contadores y fechas actualizados

**Scoring**:
- 5 puntos: KB evaluada correctamente + si SÍ, sección 8.5 completa, índices actualizados Y validados
- 4 puntos: KB evaluada + sección 8.5 completa + índices actualizados pero validación parcial
- 3 puntos: KB evaluada + sección 8.5 completa pero índices no validados
- 2 puntos: KB evaluada vagamente o sección 8.5 incompleta
- 1 punto: KB no evaluada correctamente
- 0 puntos: Sección ausente

**Puntuación**: ___/5 (N/A si KB = NO)

---

### Cierre del Incidente

- [ ] **METADATA actualizado** en el archivo del incidente:
  - Status: Cerrado
  - Fecha de Cierre
  - Tiempo de Resolución (calculado correctamente)

- [ ] **Nota final de cierre** al final del archivo con:
  - Incidente Cerrado (fecha y hora)
  - Closed By (nombre del Architect)
  - Final Status (resumen en una frase)

- [ ] **Archivo del incidente está bien formateado** (Markdown correcto)

**Scoring**:
- 3 puntos: METADATA completo + nota de cierre + formato correcto
- 2 puntos: METADATA completo, nota de cierre incompleta
- 1 punto: Solo METADATA actualizado
- 0 puntos: Incidente no cerrado correctamente

**Puntuación**: ___/3

---

## 📊 Puntuación Total

| Sección | Puntos Obtenidos | Puntos Posibles |
|---------|------------------|-----------------|
| Timeline | ___/4 | 4 |
| What Went Wrong | ___/4 | 4 |
| What Went Right | ___/4 | 4 |
| Lessons Learned | ___/5 | 5 |
| Medidas Preventivas | ___/5 | 5 |
| Knowledge Base | ___/5 | 5 (o N/A) |
| Cierre del Incidente | ___/3 | 3 |
| **TOTAL** | **___/30** | **30** |

---

## ✅ Criterios de Aprobación

**EXCELENTE** (27-30 puntos): Post-mortem comprehensivo y completo. Ready para cierre de incidente.

**BUENO** (22-26 puntos): Post-mortem sólido. Requiere ajustes menores antes de cierre.

**SUFICIENTE** (18-21 puntos): Post-mortem aceptable pero incompleto. Requiere completar secciones faltantes.

**INSUFICIENTE** (< 18 puntos): Post-mortem no cumple estándares mínimos. Requiere re-trabajo significativo.

---

## 🎯 Quality Gates

**Bloqueadores** (debe cumplir TODOS para cerrar incidente):

- ✅ Timeline está completa
- ✅ What Went Wrong identifica causas de falla
- ✅ Lessons Learned son actionables
- ✅ Medidas Preventivas tienen responsable y fecha
- ✅ Si P0-P1, Knowledge Base está evaluado
- ✅ Incidente cerrado con METADATA actualizado

**Warnings** (recomendado pero no bloqueante):

- ⚠️ What Went Right documenta aciertos
- ⚠️ Lessons Learned son generalizables
- ⚠️ Medidas Preventivas son viables
- ⚠️ Si KB = SÍ, índices actualizados

---

_Este checklist es usado por el Architect para validar que el post-mortem está completo antes de cerrar el incidente._
