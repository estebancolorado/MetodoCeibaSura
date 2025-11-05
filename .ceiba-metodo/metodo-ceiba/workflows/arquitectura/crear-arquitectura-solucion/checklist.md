# Checklist de Validación - Arquitectura Solución

## Validación de Contenido

### Secciones Obligatorias

- [ ] **Introducción y Objetivos** - Completa y alineada con el propósito del proyecto
- [ ] **Resumen de Requisitos** - Conciso y referencia documentos base
- [ ] **Objetivos de Calidad** - 3-5 objetivos priorizados como ASR
- [ ] **Interesados** - Tabla completa con roles y expectativas
- [ ] **Restricciones** - Clasificadas por tipo (técnicas, organizativas, políticas, convenciones)
- [ ] **Contexto de Negocio** - Diagrama C4 Context y tabla de elementos
- [ ] **Contexto Técnico** - Diagrama de despliegue con justificación de componentes
- [ ] **Estrategia de Solución** - Decisiones fundamentales justificadas
- [ ] **Vista Estática** - Diagrama C4 Container y tabla descriptiva
- [ ] **Vista Dinámica** - Al menos un flujo arquitectónicamente relevante

### Calidad de Diagramas

- [ ] **Diagramas Mermaid** - Sintaxis correcta y renderizable
- [ ] **Contexto de Negocio** - Enfoque en actores y sistemas externos
- [ ] **Contexto Técnico** - Uso de iconos del proveedor cloud seleccionado
- [ ] **Vista Estática** - Contenedores claramente definidos con interfaces
- [ ] **Secuencias** - Flujos críticos para comprensión arquitectónica

### Variables y Consistencia

- [ ] **{{project_name}}** - Consistente en todo el documento
- [ ] **{{date}}** - Fecha actual del sistema
- [ ] **{{user_name}}** - Nombre del arquitecto/autor
- [ ] **Variables de sección** - Todas las variables {{}} reemplazadas correctamente

### Estructura y Formato

- [ ] **Markdown válido** - Sintaxis correcta y bien formateada
- [ ] **Tablas completas** - Todas las filas con datos relevantes
- [ ] **Enlaces internos** - Referencias entre secciones funcionando
- [ ] **Jerarquía** - Niveles de encabezado apropiados (H1, H2, H3)

## Validación de Proceso

### Elicitación

- [ ] **Elicitación ejecutada** - Todas las secciones con <elicit-required> procesadas
- [ ] **Contexto suficiente** - Documentos base revisados antes de comenzar
- [ ] **Interacción usuario** - Confirmaciones y ajustes realizados durante el proceso
- [ ] **Calidad ASR** - Objetivos de calidad formulados como requisitos arquitectónicos

### Flujos Dinámicos

- [ ] **Relevancia arquitectónica** - Flujos seleccionados impactan decisiones de arquitectura
- [ ] **Cobertura completa** - Casos de uso críticos, interfaces externas, y operación/administración
- [ ] **Diagramas de secuencia** - Interacciones claras entre componentes

### Salida Final

- [ ] **Archivo generado** - Documento en ubicación correcta: {{output_folder}}/architecture-{{project_name}}-{{date}}.md
- [ ] **Contenido compilado** - Todas las variables reemplazadas y secciones integradas
- [ ] **Revisión final** - Documento leído y aprobado por el usuario

## Criterios de Aprobación

**✅ APROBADO** - Todos los elementos del checklist completados  
**⚠️ CONDICIONAL** - Elementos menores pendientes, documento usable  
**❌ RECHAZADO** - Elementos críticos faltantes, requiere reelaboración

## Notas de Validación

```
[Espacio para notas del validador]
- Fecha de validación:
- Validador:
- Observaciones:
- Recomendaciones:
```

---

**Checklist v1.0** - Método Ceiba - Arquitectura Solución
