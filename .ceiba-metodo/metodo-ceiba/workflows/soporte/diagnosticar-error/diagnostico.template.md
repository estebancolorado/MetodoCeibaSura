## 2. Diagnóstico (Architect)

<!-- Completado con comando: *diagnosticar -->

**Validación de Error Real**: {{is_real_error}}

<!-- Si es Falso Positivo, el incidente se cierra aquí -->
<!-- Si es Error Real, continuar con diagnóstico -->

{{#if is_false_positive}}
**Razón de Falso Positivo**:

{{false_positive_reason}}

**Status**: Cerrado - Falso Positivo
{{else}}

**Root Cause Analysis (5 Whys)**:

{{root_cause_analysis}}

**Clasificación de Tipo de Error**:

{{error_classification}}

**Componentes Afectados (Detallado)**:

{{affected_components_detailed}}

**Escenarios de Fallo Documentados**:

**Escenario Principal**:
{{failure_scenario_main}}

**Escenarios Relacionados**:
{{failure_scenarios_related}}

**Escenarios para No-Regresión**:
{{failure_scenarios_no_regression}}

**Evaluación de Impacto Arquitectónico**:

{{architectural_impact}}

**¿Requiere Análisis Arquitectónico?**: {{requires_architecture_analysis}}

**Estrategia de Resolución**:

{{resolution_strategy}}

{{#if recategorization_applied}}
**Recategorización Validada en Diagnóstico**:

{{categorization_validated_in_diagnosis}}
{{/if}}

{{/if}}

---
