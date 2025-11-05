---
title: 'Checklist de Completitud - Desarrollar Historia de Usuario'
validation-target: 'Archivo de historia ({{story_path}})'
required-inputs:
  - 'Archivo de historia con Tareas de Implementación y Criterios de Aceptación'
optional-inputs:
  - 'Resultados de tests (si se guardaron)'
  - 'Logs de CI (si aplica)'
validation-rules:
  - 'Solo se modificaron las secciones permitidas: Tareas de Implementación (checkboxes), Dev Agent Record, File List, Change Log, y Status'
---

# Checklist de Completitud - Desarrollar Historia de Usuario

## Completitud de Tareas

- [ ] Todas las tareas y subtareas de esta historia están marcadas como completadas [x]
- [ ] La implementación cumple con TODOS los criterios de aceptación de la historia

## Validación y Tests

- [ ] Build ejecutado exitosamente (si el proyecto requiere compilación)
- [ ] Suite completa de tests ejecutada y pasando (sin regresiones)
- [ ] Linting y code quality ejecutados (si están configurados)

## Actualizaciones al Archivo de Historia

- [ ] Sección File List incluye TODOS los archivos nuevos/modificados/eliminados (paths relativos a raíz del repo)
- [ ] Sección Dev Agent Record contiene Debug Log y/o Completion Notes relevantes
- [ ] Sección Change Log incluye resumen breve de los cambios
- [ ] Solo se modificaron las secciones permitidas del archivo de historia

## Definition of Done

- [ ] Definition of Done checklist ejecutado (si la historia incluye uno)
- [ ] Todos los items del DoD están cumplidos

## Estado Final

- [ ] Suite de regresión ejecutada exitosamente
- [ ] Estado de la historia es "Lista para Revisión"
