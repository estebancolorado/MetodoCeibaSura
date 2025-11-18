## Estimación

**Estimador:** {user_name}  
**Fecha:** {date}

### Estimación por Tareas

#### Tareas Aumentadas por IA (Impactadas por Método Ceiba)

**Leyenda:** MC = Método Ceiba

| # | Tarea | Complejidad | Junior | Semi Sr | Senior | MC Jr | MC Semi Sr | MC Sr |
|---|-------|-------------|--------|---------|--------|-------|------------|-------|
{{#each tareas}}
| {{numero}} | {{descripcion}} | {{complejidad}} | {{junior}}h | {{semi_sr}}h | {{senior}}h | {{mc_junior}}h | {{mc_semi_sr}}h | **{{mc_senior}}h** |
{{/each}}

{{#if tareas_manuales}}
#### Tareas Manuales (No Impactadas por Método Ceiba)

Estas tareas requieren intervención humana directa y no se benefician de la optimización por IA.

| # | Tarea | Tiempo Estimado |
|---|-------|-----------------|
{{#each tareas_manuales}}
| {{numero}} | {{descripcion}} | {{tiempo_estimado}}h |
{{/each}}

**Total Tareas Manuales:** {{total_tareas_manuales}}h

{{/if}}

### Totales Comparativos por Rol

| Perfil | Tradicional | Método Ceiba | Optimización |
|--------|-------------|--------------|--------------|
| Junior | {{total_junior}}h | {{total_mc_junior}}h | {{optimizacion_mc_junior}}% |
| Semi Senior | {{total_semi_sr}}h | {{total_mc_semi_sr}}h | {{optimizacion_mc_semi_sr}}% |
| **Senior** | **{{total_senior}}h** | **{{total_mc_senior}}h** | **{{optimizacion_mc_senior}}%** |

{{#if tareas_manuales}}
#### 📌 Tiempo Total de Desarrollo

El tiempo total de desarrollo incluye las tareas aumentadas por IA más las tareas manuales:

- **Junior:** {{total_mc_junior}}h (Método Ceiba) + {{total_tareas_manuales}}h (Manuales) = **{{total_desarrollo_junior}}h**
- **Semi Senior:** {{total_mc_semi_sr}}h (Método Ceiba) + {{total_tareas_manuales}}h (Manuales) = **{{total_desarrollo_semi_sr}}h**
- **Senior:** {{total_mc_senior}}h (Método Ceiba) + {{total_tareas_manuales}}h (Manuales) = **{{total_desarrollo_senior}}h**

{{/if}}


### Notas Adicionales

{{notas_adicionales}}
{{/if}}

---

*Estimación generada usando workflow estimar-historia-usuario del Método Ceiba.*
