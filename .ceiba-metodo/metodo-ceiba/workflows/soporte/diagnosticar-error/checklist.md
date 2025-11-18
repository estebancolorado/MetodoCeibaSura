# Checklist de Validación - Diagnóstico de Error

## Validación de Error Real

- [ ] Determinación clara documentada: Error Real o Falso Positivo
- [ ] Justificación específica de la determinación
- [ ] Si Falso Positivo: Razón específica seleccionada y explicada
- [ ] Si Falso Positivo: Acciones de cierre sugeridas y documentadas
- [ ] Si Falso Positivo: Incidente marcado como cerrado correctamente

## Análisis de Causa Raíz (Solo si Error Real)

- [ ] Metodología 5 Whys aplicada completamente (5 niveles documentados)
- [ ] Cada "por qué" tiene respuesta específica y técnica (no genérica)
- [ ] Cadena lógica: cada respuesta es causa directa de la anterior
- [ ] Causa raíz identificada es ACTIONABLE (se puede corregir con acción concreta)
- [ ] Causa raíz es ESPECÍFICA (no vaga como "falta de testing" o "falta comunicación")
- [ ] Evidencias técnicas del incidente soportan el análisis de causa raíz

## Clasificación de Tipo de Error

- [ ] Tipo de error claramente clasificado (Código/Datos/Infraestructura/Seguridad/Configuración/Rendimiento/Integración/Mixto)
- [ ] Justificación de clasificación documentada con base técnica
- [ ] Si Mixto: Todos los tipos involucrados identificados explícitamente
- [ ] Estrategia de resolución definida y apropiada según tipo de error
- [ ] Estrategia es específica y ejecutable (no genérica)

## Escenarios de Fallo

- [ ] Escenario de Fallo Principal documentado completamente con:
  - [ ] Precondiciones del sistema claramente definidas
  - [ ] Datos de entrada específicos que causan el error
  - [ ] Acción exacta que desencadena el fallo
  - [ ] Resultado esperado vs. resultado actual diferenciados
- [ ] Al menos UN escenario relacionado o edge case identificado
- [ ] Al menos UN escenario de no-regresión definido (qué debe seguir funcionando)
- [ ] Escenarios utilizan valores específicos de "valores que causan error" de sección 1
- [ ] Escenarios son testables (pueden convertirse en casos de prueba automatizados)

## Componentes Afectados

- [ ] Todos los componentes de código afectados listados explícitamente
- [ ] Archivos específicos a modificar identificados (si error de código)
- [ ] Datasets o tablas afectadas identificadas (si error de datos)
- [ ] Variables de configuración a ajustar listadas (si error de configuración)
- [ ] Dependencias downstream documentadas si aplica
- [ ] Relación entre componentes explicada si son múltiples

## Evaluación de Impacto Arquitectónico

- [ ] Impacto arquitectónico evaluado y clasificado (Alto/Medio/Bajo)
- [ ] Clasificación justificada con número de componentes y complejidad
- [ ] Decisión sobre análisis arquitectónico claramente documentada (Sí/No)
- [ ] Si requiere análisis: Razones específicas documentadas (no vaga)
- [ ] Si NO requiere: Confirmado que fix es directo y no necesita diseño profundo

## Completitud del Diagnóstico

- [ ] Sección 2 del incident.md actualizada completamente
- [ ] Todas las variables generadas tienen valores (no placeholders)
- [ ] Información es coherente entre secciones
- [ ] No hay contradicciones entre causa raíz y tipo de error
- [ ] Fecha de diagnóstico y nombre del Architect registrados

## Ready for Next Step

- [ ] Si requiere análisis: Sección 3 marcada como "Pendiente"
- [ ] Si NO requiere análisis: Sección 3 marcada como "No Aplica"
- [ ] Instrucciones claras para siguiente paso proporcionadas
- [ ] Developer tiene suficiente información para refinar (si NO requiere análisis)
- [ ] Architect tiene contexto completo para análisis profundo (si requiere análisis)

## Criterios de Calidad del Diagnóstico

### Excelente (90-100%)

- Causa raíz altamente específica y actionable
- 5 Whys con cadena lógica impecable
- Múltiples escenarios de fallo documentados
- Estrategia de resolución detallada y clara
- Componentes identificados con archivos específicos
- Impacto arquitectónico bien evaluado

### Bueno (70-89%)

- Causa raíz identificada y actionable
- 5 Whys completo con lógica sólida
- Escenarios principales documentados
- Estrategia de resolución clara
- Componentes principales identificados

### Aceptable (50-69%)

- Causa raíz presente pero podría ser más específica
- 5 Whys completo con alguna vaguedad
- Escenario principal documentado
- Estrategia general definida
- Componentes identificados

### Insuficiente (<50%)

- Causa raíz vaga o no actionable
- 5 Whys incompleto o con lógica débil
- Escenarios de fallo faltantes o vagos
- Estrategia genérica o ausente
- **REQUIERE**: Re-ejecución del diagnóstico

## Validación de Consistencia

- [ ] Tipo de error es consistente con causa raíz identificada
- [ ] Estrategia de resolución alineada con tipo de error
- [ ] Escenarios de fallo son coherentes con descripción del error (sección 1)
- [ ] Componentes afectados coinciden con evidencias técnicas
- [ ] Impacto arquitectónico refleja número y complejidad de componentes
