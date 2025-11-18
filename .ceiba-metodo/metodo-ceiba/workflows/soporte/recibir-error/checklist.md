# Checklist de Validación - Recepción de Error

## Información Básica

- [ ] Número de incidente asignado y único
- [ ] Entorno claramente identificado (Production/Staging/Development)
- [ ] Severidad clasificada correctamente (P0/P1/P2/P3/P4)
- [ ] Tipo de error definido (Bug/Regression/Performance/Security/Data Loss)
- [ ] Fecha de reporte capturada

## Descripción del Error

- [ ] Resumen conciso del error (1-2 oraciones descriptivas)
- [ ] Impacto cuantificado (número de usuarios afectados, funcionalidad bloqueada)
- [ ] Pasos de reproducción documentados paso a paso (mínimo 2-3 pasos)
- [ ] Comportamiento esperado claramente definido
- [ ] Comportamiento actual documentado con especificidad
- [ ] Indicación de consistencia del error (siempre ocurre vs. intermitente)

## Evidencias Técnicas

- [ ] Al menos UNA evidencia visual adjuntada (screenshot o video)
- [ ] Logs relevantes capturados (mínimo últimas 50 líneas si aplica al tipo de error)
- [ ] Stack trace completo documentado (si disponible y aplica)
- [ ] Errores de consola capturados (para errores de UI/Frontend)
- [ ] URLs afectadas documentadas con paths completos
- [ ] Browser/OS/Device documentado (si error de frontend)
- [ ] Versión de deployment identificada (si disponible)

## Valores que Causan Error

- [ ] Al menos UN valor específico que reproduce el error documentado
- [ ] Valores que SÍ funcionan documentados (para comparación y análisis)
- [ ] Patrón de datos identificado si aplica (ej: emails con '+', montos > 1000)
- [ ] IDs de entidades afectadas registrados (si aplica)
- [ ] Análisis comparativo presente (diferencia entre valores que fallan vs funcionan)

## Contexto Técnico

- [ ] Request/Response HTTP documentado (si error de API)
- [ ] Variables de ambiente relevantes identificadas (si aplica)
- [ ] Métricas de monitoreo capturadas (si disponibles)
- [ ] Timestamp del error o rango temporal de ocurrencia

## Componentes Afectados

- [ ] Al menos UN componente técnico identificado claramente
- [ ] Nombres de componentes son específicos (no genéricos como "el sistema")
- [ ] Relación entre componentes documentada (si aplica a múltiples)

## Completitud General

- [ ] No quedan placeholders vacíos en secciones CRÍTICAS
- [ ] Toda la información proporcionada es verificable y específica (no vaga)
- [ ] El documento permite a un Architect diagnosticar sin preguntas adicionales básicas
- [ ] Clasificación de severidad está justificada por el impacto documentado
- [ ] No hay información contradictoria entre secciones

## Ready for Next Step

- [ ] Documento guardado correctamente en `docs/incidents/{número}.incident.md`
- [ ] Status del incidente actualizado a "Triaged"
- [ ] Notificación enviada o preparada para el Architect
- [ ] Si P0-P1: Marcado como urgente y listo para diagnóstico inmediato
- [ ] Si P2-P4: Listo para planificación en próximo sprint

## Criterios de Calidad

### Excelente (90-100%)
- Todas las secciones críticas completas
- Múltiples evidencias técnicas adjuntas
- Valores específicos documentados con ejemplos de comparación
- Pasos de reproducción detallados y verificados
- Contexto técnico completo

### Bueno (70-89%)
- Secciones críticas completas
- Al menos una evidencia técnica
- Valores que causan error documentados
- Pasos de reproducción presentes

### Aceptable (50-69%)
- Información básica completa
- Descripción del error clara
- Al menos componente identificado
- Suficiente para iniciar diagnóstico (puede requerir follow-up)

### Insuficiente (<50%)
- Información crítica faltante
- Descripción vaga o ambigua
- No se puede diagnosticar sin preguntas adicionales
- **REQUIERE**: Re-ejecución del workflow para completar información
