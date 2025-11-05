# Checklist de Validación - Análisis Arquitectónico de Historia de Usuario

## Configuración y Carga Inicial
- [ ] Config del módulo cargado correctamente (dev_story_location, architecture_sharded_location)
- [ ] Historia de usuario existente identificada y cargada
- [ ] Estado de la historia validado ("Borrador (PO)" o equivalente)
- [ ] Número y título de historia extraídos correctamente

## Consulta de Enfoque Arquitectónico
- [ ] Consultado al arquitecto sobre consideraciones iniciales (OBLIGATORIO)
- [ ] Enfoque arquitectónico registrado (Dirigido o Exploratorio)
- [ ] Consideraciones específicas del arquitecto documentadas (si aplica)
- [ ] Base para el análisis establecida claramente

## Análisis Arquitectónico Profundo
- [ ] GPS Arquitectónico revisado (index.md)
- [ ] Componentes documentados analizados (architecture-*.md, component*.md)
- [ ] Flujos de negocio consultados (flujo-*.md, flow-*.md, proceso-*.md)
- [ ] Historias arquitectónicas previas revisadas para consistencia
- [ ] Decisiones arquitectónicas de historias similares identificadas
- [ ] Patrones existentes relevantes documentados
- [ ] Componentes afectados identificados con nivel de impacto
- [ ] Nuevos componentes justificados (si aplica)
- [ ] Integraciones requeridas especificadas

## Propuesta de Diseño
- [ ] Consideraciones del arquitecto incorporadas en la propuesta
- [ ] Patrón arquitectónico seleccionado y justificado
- [ ] Distribución de responsabilidades definida
- [ ] Flujo de datos especificado
- [ ] Componentes y sus responsabilidades detallados
- [ ] Modificaciones requeridas documentadas
- [ ] Dependencias entre componentes mapeadas
- [ ] Interfaces y contratos especificados (si aplica)
- [ ] Propuesta completa presentada al arquitecto

## Validación Humana
- [ ] Propuesta presentada al arquitecto para validación (OBLIGATORIO)
- [ ] Feedback del arquitecto recibido y registrado
- [ ] Ajustes incorporados según feedback (si aplica)
- [ ] Nueva validación solicitada si cambios fueron significativos
- [ ] Aprobación final del arquitecto obtenida (CRÍTICO)
- [ ] Decisión arquitectónica final capturada exactamente como fue aprobada

## Actualización de Historia de Usuario
- [ ] Sección "Análisis Arquitectónico (Arquitecto)" preparada con decisiones finales
- [ ] Subsección "Decisiones de Diseño" completa (OBLIGATORIO)
- [ ] Subsección "Especificaciones Técnicas" incluida (si aplica)
- [ ] Subsección "Impacto Arquitectónico" documentada (si aplica)
- [ ] Subsección "Validación Arquitectónica" con metadata (OBLIGATORIO)
- [ ] Subsección "Referencias Arquitectónicas" con trazabilidad (OBLIGATORIO)
- [ ] Sección insertada en ubicación correcta del archivo (después de Análisis de Historias Relacionadas)
- [ ] Estado de historia actualizado a "Analizado (Arquitecto)"
- [ ] Metadata actualizada con fecha de análisis y arquitecto responsable
- [ ] Registro de Cambios actualizado con nueva entrada
- [ ] Archivo guardado en su ubicación original

## Calidad y Coherencia
- [ ] Decisiones alineadas con arquitectura existente
- [ ] Patrones coherentes con los ya establecidos en el sistema
- [ ] Referencias a documentación arquitectónica incluidas
- [ ] Referencias a historias relacionadas documentadas
- [ ] Justificaciones claras para todas las decisiones
- [ ] Riesgos arquitectónicos identificados con mitigaciones (si aplica)
- [ ] Información suficiente para refinamiento técnico posterior
- [ ] Separación clara entre diseño arquitectónico y detalles de implementación

## Preservación de Información
- [ ] TODO el contenido original del PO preservado intacto
- [ ] NO se creó archivo nuevo - se modificó el existente
- [ ] NO se eliminó información previa de la historia
- [ ] Solo se AGREGÓ la sección de análisis arquitectónico

## Entrega y Documentación
- [ ] Resumen del análisis presentado al arquitecto
- [ ] Decisiones arquitectónicas clave listadas
- [ ] Próximos pasos documentados claramente
- [ ] Confirmación final del arquitecto solicitada
- [ ] Trazabilidad completa de decisiones mantenida

## Criterios de Éxito Finales
- [ ] ✅ Coherencia con arquitectura existente verificada
- [ ] ✅ Validación humana obtenida y documentada
- [ ] ✅ Documentación completa en la HU
- [ ] ✅ Componentes claramente especificados
- [ ] ✅ Base sólida preparada para refinamiento técnico
- [ ] ✅ Trazabilidad de decisiones completa
- [ ] ✅ Un solo archivo - información centralizada

---

## Notas de Validación

**CRÍTICO:** Los siguientes puntos son OBLIGATORIOS y NO pueden omitirse:
1. Consulta de enfoque arquitectónico al inicio
2. Validación humana de la propuesta antes de documentar
3. Captura de decisión final aprobada
4. Actualización del archivo de historia con análisis completo
5. Preservación de información original del PO

**Si alguno de estos puntos no se cumple, el workflow NO ha sido ejecutado correctamente.**
