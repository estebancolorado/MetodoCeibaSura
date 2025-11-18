# Dashboard - Gestión de Incidentes

**Generado**: {{current_timestamp}}

---

## 🔍 Filtros Aplicados

| Filtro | Valor |
|--------|-------|
| Severidad | {{filter_severity}} |
| Estado | {{filter_status}} |
| Tipo de Error | {{filter_error_type}} |
| Búsqueda | {{search_keyword}} |

**Resultados**: {{total_filtered_incidents}} incidentes encontrados

---

## 📋 Incidentes (Filtrados)

{{#if incidents_list}}
{{incidents_list}}
{{else}}
_No hay incidentes que cumplan los filtros especificados._
{{/if}}

---

## 📊 Estadísticas Generales

### Por Severidad

| Severidad | Total | Open | Triaged | In Progress | Resolved | Cerrado |
|-----------|-------|------|---------|-------------|----------|---------|
| **P0** | {{p0_total}} | {{p0_open}} | {{p0_triaged}} | {{p0_in_progress}} | {{p0_resolved}} | {{p0_cerrado}} |
| **P1** | {{p1_total}} | {{p1_open}} | {{p1_triaged}} | {{p1_in_progress}} | {{p1_resolved}} | {{p1_cerrado}} |
| **P2** | {{p2_total}} | {{p2_open}} | {{p2_triaged}} | {{p2_in_progress}} | {{p2_resolved}} | {{p2_cerrado}} |
| **P3** | {{p3_total}} | {{p3_open}} | {{p3_triaged}} | {{p3_in_progress}} | {{p3_resolved}} | {{p3_cerrado}} |
| **P4** | {{p4_total}} | {{p4_open}} | {{p4_triaged}} | {{p4_in_progress}} | {{p4_resolved}} | {{p4_cerrado}} |
| **TOTAL** | **{{total_incidents}}** | **{{total_open}}** | **{{total_triaged}}** | **{{total_in_progress}}** | **{{total_resolved}}** | **{{total_cerrado}}** |

### Por Tipo de Error

| Tipo | Total | % del Total |
|------|-------|-------------|
| Código | {{codigo_total}} | {{codigo_percentage}}% |
| Datos | {{datos_total}} | {{datos_percentage}}% |
| Infraestructura | {{infra_total}} | {{infra_percentage}}% |
| Seguridad | {{seguridad_total}} | {{seguridad_percentage}}% |
| Configuración | {{config_total}} | {{config_percentage}}% |
| Rendimiento | {{rendimiento_total}} | {{rendimiento_percentage}}% |
| Integración | {{integracion_total}} | {{integracion_percentage}}% |
| Mixto | {{mixto_total}} | {{mixto_percentage}}% |

### Tiempo Promedio de Resolución

| Severidad | SLA | Tiempo Promedio | Casos Resueltos | Tendencia |
|-----------|-----|-----------------|-----------------|-----------|
| **P0** | < 4h | {{p0_avg_resolution}} | {{p0_resolved_count}} | {{p0_trend}} |
| **P1** | < 24h | {{p1_avg_resolution}} | {{p1_resolved_count}} | {{p1_trend}} |
| **P2** | < 1 semana | {{p2_avg_resolution}} | {{p2_resolved_count}} | {{p2_trend}} |
| **P3** | < 2 semanas | {{p3_avg_resolution}} | {{p3_resolved_count}} | {{p3_trend}} |

---

## ✅ SLA Compliance

| Severidad | SLA Target | Cumplieron | NO Cumplieron | Total Cerrado | % Compliance | Status |
|-----------|------------|------------|---------------|---------------|--------------|--------|
| **P0** | < 4h | {{p0_sla_met}} | {{p0_sla_missed}} | {{p0_total_closed}} | {{p0_compliance}}% | {{p0_compliance_status}} |
| **P1** | < 24h | {{p1_sla_met}} | {{p1_sla_missed}} | {{p1_total_closed}} | {{p1_compliance}}% | {{p1_compliance_status}} |
| **P2** | < 1 semana | {{p2_sla_met}} | {{p2_sla_missed}} | {{p2_total_closed}} | {{p2_compliance}}% | {{p2_compliance_status}} |
| **P3** | < 2 semanas | {{p3_sla_met}} | {{p3_sla_missed}} | {{p3_total_closed}} | {{p3_compliance}}% | {{p3_compliance_status}} |

**Leyenda**:
- ✅ Excelente (>= 80% compliance)
- ⚠️ Necesita Mejora (60-79% compliance)
- ❌ Crítico (< 60% compliance)

---

## 🚀 Acciones Rápidas

### Crear Nuevo Incidente
```
*recibir-error
```

### Diagnosticar Incidentes Pendientes

{{#if triaged_incidents}}
Los siguientes incidentes están esperando diagnóstico:

{{#each triaged_incidents}}
- **[{{id}}]** {{title}} ({{severity}}) - `*diagnosticar incident_file="docs/incidents/{{id}}.incident.md"`
{{/each}}
{{else}}
_No hay incidentes en estado "Triaged" pendientes de diagnóstico._
{{/if}}

### Documentar Post-Mortem

{{#if resolved_incidents}}
Los siguientes incidentes están resueltos pero pendientes de post-mortem:

{{#each resolved_incidents}}
- **[{{id}}]** {{title}} ({{severity}}) - `*documentar-incidente incident_file="docs/incidents/{{id}}.incident.md"`
{{/each}}
{{else}}
_No hay incidentes en estado "Resolved" pendientes de post-mortem._
{{/if}}

---

## 📈 Insights y Recomendaciones

### Top 3 Tipos de Error

1. **{{top_error_type_1}}**: {{top_error_count_1}} incidentes ({{top_error_percentage_1}}%)
2. **{{top_error_type_2}}**: {{top_error_count_2}} incidentes ({{top_error_percentage_2}}%)
3. **{{top_error_type_3}}**: {{top_error_count_3}} incidentes ({{top_error_percentage_3}}%)

### Incidentes Críticos (P0-P1)

- **Total P0-P1**: {{critical_total}}
- **Abiertos**: {{critical_open}}
- **En Progreso**: {{critical_in_progress}}

{{#if critical_open > 0}}
⚠️ **ATENCIÓN**: Hay {{critical_open}} incidentes críticos abiertos que requieren atención inmediata.
{{/if}}

### Tendencias

- **Incidentes nuevos este mes**: {{incidents_this_month}}
- **Incidentes resueltos este mes**: {{resolved_this_month}}
- **Tasa de resolución**: {{resolution_rate}}%

---

## 🔗 Enlaces Útiles

- [Crear Nuevo Incidente](../../FLUJO-SOPORTE-MANTENIMIENTO.md#paso-1-recepción-del-error)
- [Knowledge Base](../knowledge-base/README.md)
- [Guía de Flujo de Soporte](../../FLUJO-SOPORTE-MANTENIMIENTO.md)
- [Incidentes por Categoría](../knowledge-base/index-by-category.md)
- [Incidentes por Etiquetas](../knowledge-base/index-by-tags.md)

---

_Dashboard generado automáticamente. Para actualizar, vuelve a ejecutar el workflow `*gestionar-incidentes`._
