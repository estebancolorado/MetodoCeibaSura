
## Análisis Arquitectónico (Arquitecto)

<!-- ============================================================================ -->
<!-- SECCIÓN AGREGADA POR: Workflow analizar-disenar-historia-usuario            -->
<!-- ETAPA: Análisis Arquitectónico                                              -->
<!-- RESPONSABLE: Arquitecto                                                     -->
<!-- ============================================================================ -->

### Decisiones de Diseño

<!-- OBLIGATORIO: Patrón arquitectónico, justificación, componentes, hitos -->

**Patrón Arquitectónico:** {{patron_arquitectonico}}

**Justificación:** {{justificacion_patron}}

**Componentes Afectados:**

<!-- GUÍA: Para cada componente especificar nombre, tipo (Nuevo/Modificación), responsabilidades
     Si es Modificación: nivel de cambio (Menor/Mayor/Crítico)
     Incluir especificaciones técnicas relevantes (código, APIs, interfaces) -->

{{#each componentes_principales}}
- **{{nombre}} ({{tipo}}):** {{responsabilidades}}
  {{#if nivel_cambio}}- Nivel de cambio: {{nivel_cambio}}{{/if}}
  {{#if especificaciones_tecnicas}}
  - Especificaciones: {{especificaciones_tecnicas}}
  {{/if}}
{{/each}}

**Hitos de Implementación:**

<!-- GUÍA: Secuencia lógica desde perspectiva ARQUITECTÓNICA - Define QUÉ y DÓNDE, NO el CÓMO
     NO INCLUIR estimaciones de tiempo - eso es responsabilidad del Developer
     Incluir: componente, descripción, dependencias entre componentes -->

{{#each orden_implementacion}}
{{numero}}. **{{componente}}** - {{descripcion}}
   - Dependencias: {{dependencias}}
{{/each}}

### Validación de Impacto

<!-- OBLIGATORIO: Resultado de validación técnica (paso 1.5)
     Incluir: análisis de dependencias, consumidores, cadena de invocación, impacto en performance
     Documentar hallazgos críticos que fundamentan las decisiones -->

{{validacion_impacto}}

### Notas Técnicas (Si aplica)

<!-- OPCIONAL: Detalles técnicos adicionales, alternativas descartadas, consideraciones especiales
     Puede incluir: clases específicas, advertencias, recomendaciones de implementación
     OMITIR si no hay notas adicionales -->

{{#if notas_tecnicas}}
{{notas_tecnicas}}
{{/if}}

### Referencias y Validación

<!-- OBLIGATORIO: Trazabilidad de decisiones -->

**Documentación consultada:**
{{#each documentacion_consultada}}
- {{nombre_documento}} - {{ruta_o_referencia}}
{{/each}}

**Historias relacionadas:**
{{#each historias_relacionadas}}
- Historia #{{numero}}: {{titulo}} - {{relacion}}
{{/each}}

**Validado por:** {{user_name}} | **Fecha:** {{date}} | **Enfoque:** {{enfoque_arquitectonico}}

---
