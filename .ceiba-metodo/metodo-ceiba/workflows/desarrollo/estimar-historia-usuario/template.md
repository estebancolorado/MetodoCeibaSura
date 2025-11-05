## Estimación

**Estimador:** {user_name}  
**Fecha:** {date}

### Estimación por Tareas

**Leyenda:** MC = Método Ceiba

| # | Tarea | Complejidad | Junior | Semi Sr | Senior | MC Jr | MC Semi Sr | MC Sr |
|---|-------|-------------|--------|---------|--------|-------|------------|-------|
{{#each tareas}}
| {{numero}} | {{descripcion}} | {{complejidad}} | {{junior}}h | {{semi_sr}}h | {{senior}}h | {{mc_junior}}h | {{mc_semi_sr}}h | **{{mc_senior}}h** |
{{/each}}

### Totales Comparativos

| Perfil | Tradicional | Método Ceiba | Optimización |
|--------|-------------|--------------|--------------|
| Junior | {{total_junior}}h | {{total_mc_junior}}h | {{optimizacion_mc_junior}}% |
| Semi Senior | {{total_semi_sr}}h | {{total_mc_semi_sr}}h | {{optimizacion_mc_semi_sr}}% |
| **Senior** | **{{total_senior}}h** | **{{total_mc_senior}}h** | **{{optimizacion_mc_senior}}%** |

**💡 Insight:** El Método Ceiba optimiza el proceso completo mediante orquestación agéntica (Refinamiento → Estimación → Desarrollo → QA coordinados).


{{#if notas_adicionales}}
### Notas Adicionales

{{notas_adicionales}}
{{/if}}

---

*Estimación generada usando workflow estimar-historia-usuario del Método Ceiba.*
