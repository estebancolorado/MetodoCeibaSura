## Revisión de Código (Peer Review)

**Revisor**: {{reviewer_name}}  
**Fecha**: {{review_date}}  
**Resultado**: {{review_outcome}}

---

### Resumen

{{summary}}

---

### Hallazgos

{{#if has_findings}}
{{#each findings}}
- **[{{severity}}]** {{description}} en `{{file_path}}:{{line_number}}`
  - **Problema**: {{issue}}
  - **Solución**: {{solution}}

{{/each}}
{{else}}
✅ No se encontraron problemas - Código cumple con estándares de calidad.
{{/if}}

---

### Cobertura de Criterios de Aceptación

{{ac_coverage_summary}}

---

### Notas de Seguridad

{{security_notes}}

---

### Acciones Requeridas

{{#if has_action_items}}
{{#each action_items}}
- [ ] **[Peer-Review][{{severity}}]** {{description}} ({{reference}})
{{/each}}
{{else}}
✅ No hay correcciones pendientes
{{/if}}

---

**Próximos Pasos**: {{next_steps}}

