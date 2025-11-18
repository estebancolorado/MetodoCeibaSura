# Post-Mortem Template

_Este template se usa  para documentar el análisis post-mortem del incidente. Todas las secciones deben completarse en el archivo del incidente._

---

## 8. POST-MORTEM (Architect)

### 8.1 Timeline del Incidente

| Timestamp | Evento | Responsable |
|-----------|--------|-------------|
| {{reported_date}} | Incidente reportado | {{reporter}} |
| {{diagnostic_date}} | Diagnóstico completado | Architect |
| {{analysis_date}} | Análisis y diseño completado (si aplica) | Architect |
| {{refinement_date}} | Refinamiento técnico completado | Developer |
| {{estimation_date}} | Estimación completada (si aplica) | Developer |
| {{implementation_start}} | Inicio de implementación | Developer |
| {{implementation_end}} | Implementación completada | Developer |
| {{code_review_date}} | Code review completado | Peer Reviewer |
| {{deploy_staging_date}} | Deploy a staging | DevOps |
| {{validation_staging_date}} | Validación en staging | QA Team |
| {{deploy_production_date}} | Deploy a producción | DevOps |
| {{validation_production_date}} | Validación en producción | QA Team |
| {{closure_date}} | Incidente cerrado | Product Owner |

**Tiempo Total**: {{total_resolution_time}}

---

### 8.2 What Went Wrong

_Identificar errores, fallas de proceso, y decisiones incorrectas que contribuyeron al incidente._

1. **{{falla_1_titulo}}**
   - {{falla_1_descripcion}}
   - **Impacto**: {{falla_1_impacto}}

2. **{{falla_2_titulo}}**
   - {{falla_2_descripcion}}
   - **Impacto**: {{falla_2_impacto}}

3. **{{falla_3_titulo}}**
   - {{falla_3_descripcion}}
   - **Impacto**: {{falla_3_impacto}}

_[Añadir más fallas si es necesario]_

---

### 8.3 What Went Right

_Reconocer aspectos positivos en el manejo del incidente._

1. **{{acierto_1_titulo}}**
   - {{acierto_1_descripcion}}

2. **{{acierto_2_titulo}}**
   - {{acierto_2_descripcion}}

3. **{{acierto_3_titulo}}**
   - {{acierto_3_descripcion}}

_[Añadir más aciertos si es necesario]_

---

### 8.4 Lessons Learned

_Lecciones clave aprendidas, actionables y generalizables._

1. **{{leccion_1_titulo}}**
   - {{leccion_1_explicacion}}

2. **{{leccion_2_titulo}}**
   - {{leccion_2_explicacion}}

3. **{{leccion_3_titulo}}**
   - {{leccion_3_explicacion}}

_[Añadir más lecciones si es necesario]_

---

### 8.5 Knowledge Base

_Completar SOLO si el incidente cumple criterios para KB (ver kb-criteria.md)._

**KB Metadata**:
- **Categoría**: {{kb_category}}
- **Tipo de Error**: {{error_type}}
- **Etiquetas**: {{kb_tags}}
- **Palabras Clave**: {{kb_keywords}}

**Descripción del Problema**:
{{kb_problem_description}}

**Causa Raíz Documentada**:
{{kb_root_cause_5_whys}}

**CAUSA RAÍZ**: {{kb_final_root_cause}}

**Solución Implementada**:
{{kb_solution_description}}

```{{language}}
{{kb_key_code}}
```

**Archivos Modificados**:
{{kb_files_modified}}

**Resultados y Validación**:
- **Tiempo de resolución**: {{total_resolution_time}}
- **Tests**: {{kb_tests_summary}}
- **Regresiones**: {{kb_regressions}}
- **Métricas de negocio**: {{kb_business_metrics}}

**Aprendizajes y Prevención**:
{{kb_learnings_summary}}

**Aplicabilidad a Otros Contextos**:
{{kb_applicability}}

**Rating de Utilidad**: {{kb_rating}} (1-5 estrellas)

**Referencias y Casos Relacionados**:
{{kb_related_cases}}

---

### 8.6 Medidas Preventivas Implementadas

_Acciones concretas para prevenir recurrencia del incidente._

1. **{{medida_1_titulo}}**:
   - {{medida_1_descripcion}}
   - **Responsable**: {{medida_1_responsable}}
   - **Fecha objetivo**: {{medida_1_fecha}}

2. **{{medida_2_titulo}}**:
   - {{medida_2_descripcion}}
   - **Responsable**: {{medida_2_responsable}}
   - **Fecha objetivo**: {{medida_2_fecha}}

3. **{{medida_3_titulo}}**:
   - {{medida_3_descripcion}}
   - **Responsable**: {{medida_3_responsable}}
   - **Fecha objetivo**: {{medida_3_fecha}}

_[Añadir más medidas si es necesario]_

---

**Incidente Cerrado**: {{closure_date}}

**Closed By**: {{closed_by}}

**Final Status**: {{final_status_summary}}
