<!-- Powered by Método Ceiba -->

# review-story

Realizar una revisión integral de arquitectura de pruebas con decisión de quality gate. Esta revisión adaptativa y consciente del riesgo crea tanto una actualización de historia como un archivo detallado de gate.

## Entradas

```yaml
required:
  - story_id: '{epic}.{story}' # ej., "1.3"
  - story_path: '{devStoryLocation}/{epic}.{story}.*.md' # Ruta desde core-config.yaml
  - story_title: '{title}' # Si falta, derivar del archivo de historia H1
  - story_slug: '{slug}' # Si falta, derivar del título (minúsculas, con guiones)
```

## Prerrequisitos

- El estado de la historia debe ser "Revisión"
- El desarrollador ha completado todas las tareas y actualizado la Lista de Archivos
- Todas las pruebas automatizadas están pasando

## Proceso de Revisión - Arquitectura de Pruebas Adaptativa

### 1. Evaluación de Riesgo (Determina la Profundidad de Revisión)

**Auto-escalar a revisión profunda cuando:**

- Se tocan archivos de auth/pagos/seguridad
- No se agregaron pruebas a la historia
- Diff > 500 líneas
- La gate anterior fue FAIL/CONCERNS
- La historia tiene > 5 criterios de aceptación

### 2. Análisis Integral

**A. Trazabilidad de Requisitos**

- Mapear cada criterio de aceptación a sus pruebas de validación (documentar mapeo con Given-When-Then, no código de prueba)
- Identificar brechas de cobertura
- Verificar que todos los requisitos tienen casos de prueba correspondientes

**B. Revisión de Calidad de Código**

- Arquitectura y patrones de diseño
- Oportunidades de refactoring (y realizarlas)
- Duplicación de código o ineficiencias
- Optimizaciones de rendimiento
- Vulnerabilidades de seguridad
- Adherencia a mejores prácticas

**C. Evaluación de Arquitectura de Pruebas**

- Adecuación de cobertura de pruebas en niveles apropiados
- Apropiación del nivel de prueba (qué debería ser unitaria vs integración vs e2e)
- Calidad de diseño de pruebas y mantenibilidad
- Estrategia de gestión de datos de prueba
- Uso apropiado de mocks/stubs
- Cobertura de casos límite y escenarios de error
- Tiempo de ejecución y confiabilidad de pruebas

**D. Requisitos No Funcionales (NFRs)**

- Seguridad: Autenticación, autorización, protección de datos
- Rendimiento: Tiempos de respuesta, uso de recursos
- Confiabilidad: Manejo de errores, mecanismos de recuperación
- Mantenibilidad: Claridad de código, documentación

**E. Evaluación de Testabilidad**

- Controlabilidad: ¿Podemos controlar las entradas?
- Observabilidad: ¿Podemos observar las salidas?
- Debuggabilidad: ¿Podemos debuggear fallas fácilmente?

**F. Identificación de Deuda Técnica**

- Atajos acumulados
- Pruebas faltantes
- Dependencias desactualizadas
- Violaciones de arquitectura

### 3. Refactoring Activo

- Refactorizar código donde sea seguro y apropiado
- Ejecutar pruebas para asegurar que los cambios no rompan funcionalidad
- Documentar todos los cambios en la sección QA Results con POR QUÉ y CÓMO claros
- NO alterar contenido de historia más allá de la sección QA Results
- NO cambiar Status de historia o Lista de Archivos; solo recomendar siguiente estado

### 4. Verificación de Cumplimiento de Estándares

- Verificar adherencia a `docs/coding-standards.md`
- Verificar cumplimiento con `docs/unified-project-structure.md`
- Validar enfoque de pruebas contra `docs/testing-strategy.md`
- Asegurar que todas las guías mencionadas en la historia se siguen

### 5. Validación de Criterios de Aceptación

- Verificar que cada AC está completamente implementado
- Verificar cualquier funcionalidad faltante
- Validar casos límite manejados

### 6. Documentación y Comentarios

- Verificar que el código es auto-documentado donde sea posible
- Agregar comentarios para lógica compleja si faltan
- Asegurar que cualquier cambio de API esté documentado

## Salida 1: Actualizar Archivo de Historia - SOLO Sección QA Results

**CRÍTICO**: SOLO estás autorizado a actualizar la sección "QA Results" del archivo de historia. NO modifiques ninguna otra sección.

**Regla de Anclaje QA Results:**

- Si `## QA Results` no existe, añadirla al final del archivo
- Si existe, añadir una nueva entrada fechada debajo de las entradas existentes
- Nunca editar otras secciones

Después de la revisión y cualquier refactoring, añadir tus resultados al archivo de historia en la sección QA Results:

```markdown
## QA Results

### Fecha de Revisión: [Fecha]

### Revisado por: Revisor par - Ceiba (Arquitecto de Pruebas)

### Evaluación de Calidad de Código

[Evaluación general de la calidad de implementación]

### Refactoring Realizado

[Listar cualquier refactoring que realizaste con explicaciones]

- **Archivo**: [nombre del archivo]
  - **Cambio**: [qué se cambió]
  - **Por qué**: [razón del cambio]
  - **Cómo**: [cómo mejora el código]

### Verificación de Cumplimiento

- Estándares de Código: [✓/✗] [notas si las hay]
- Estructura del Proyecto: [✓/✗] [notas si las hay]
- Estrategia de Pruebas: [✓/✗] [notas si las hay]
- Todos los ACs Cumplidos: [✓/✗] [notas si las hay]

### Lista de Mejoras

[Marcar elementos que manejaste tú mismo, dejar sin marcar para que el dev se encargue]

- [x] Refactorizar servicio de usuario para mejor manejo de errores (services/user.service.ts)
- [x] Agregar pruebas de casos límite faltantes (services/user.service.test.ts)
- [ ] Considerar extraer lógica de validación a clase validadora separada
- [ ] Agregar prueba de integración para escenarios de error
- [ ] Actualizar documentación de API para nuevos códigos de error

### Revisión de Seguridad

[Cualquier preocupación de seguridad encontrada y si fue abordada]

### Consideraciones de Rendimiento

[Cualquier problema de rendimiento encontrado y si fue abordado]

### Archivos Modificados Durante Revisión

[Si modificaste archivos, listarlos aquí - pedir al Dev actualizar Lista de Archivos]

### Estado del Gate

Gate: {STATUS} → qa.qaLocation/gates/{epic}.{story}-{slug}.yml
Perfil de riesgo: qa.qaLocation/assessments/{epic}.{story}-risk-{YYYYMMDD}.md
Evaluación NFR: qa.qaLocation/assessments/{epic}.{story}-nfr-{YYYYMMDD}.md

# Nota: Las rutas deben referenciar core-config.yaml para configuraciones personalizadas

### Estado Recomendado

[✓ Listo para Terminado] / [✗ Cambios Requeridos - Ver elementos sin marcar arriba]
(El dueño de la historia decide el estado final)
```

## Salida 2: Crear Archivo Quality Gate

**Plantilla y Directorio:**

- Renderizar desde `../templates/peer-reviewer-gate-tmpl.yaml`
- Crear directorio definido en `qa.qaLocation/gates` (ver `.ceiba-metodo/core-config.yaml`) si falta
- Guardar en: `qa.qaLocation/gates/{epic}.{story}-{slug}.yml`

Estructura del archivo gate:

```yaml
schema: 1
story: '{epic}.{story}'
story_title: '{título de la historia}'
gate: PASS|CONCERNS|FAIL|WAIVED
status_reason: 'Explicación de 1-2 oraciones de la decisión del gate'
reviewer: 'Revisor par - Ceiba (Arquitecto de Pruebas)'
updated: '{timestamp ISO-8601}'

top_issues: [] # Vacío si no hay problemas
waiver: { active: false } # Establecer active: true solo si WAIVED

# Campos extendidos (opcionales pero recomendados):
quality_score: 0-100 # 100 - (20*FAILs) - (10*CONCERNS) o usar pesos de technical-preferences.md
expires: '{timestamp ISO-8601}' # Típicamente 2 semanas desde la revisión

evidence:
  tests_reviewed: { count }
  risks_identified: { count }
  trace:
    ac_covered: [1, 2, 3] # Números de AC con cobertura de prueba
    ac_gaps: [4] # Números de AC que carecen de cobertura

nfr_validation:
  security:
    status: PASS|CONCERNS|FAIL
    notes: 'Hallazgos específicos'
  performance:
    status: PASS|CONCERNS|FAIL
    notes: 'Hallazgos específicos'
  reliability:
    status: PASS|CONCERNS|FAIL
    notes: 'Hallazgos específicos'
  maintainability:
    status: PASS|CONCERNS|FAIL
    notes: 'Hallazgos específicos'

recommendations:
  immediate: # Debe arreglarse antes de producción
    - action: 'Agregar limitación de tasa'
      refs: ['api/auth/login.ts']
  future: # Puede abordarse después
    - action: 'Considerar caché'
      refs: ['services/data.ts']
```

### Criterios de Decisión del Gate

**Regla determinística (aplicar en orden):**

Si existe risk_summary, aplicar sus umbrales primero (≥9 → FAIL, ≥6 → CONCERNS), luego estados NFR, luego severidad de top_issues.

1. **Umbrales de riesgo (si risk_summary está presente):**
   - Si cualquier puntuación de riesgo ≥ 9 → Gate = FAIL (a menos que se renuncie)
   - Si no, si cualquier puntuación ≥ 6 → Gate = CONCERNS

2. **Brechas de cobertura de prueba (si trace está disponible):**
   - Si falta cualquier prueba P0 de test-design → Gate = CONCERNS
   - Si falta prueba P0 de seguridad/pérdida-de-datos → Gate = FAIL

3. **Severidad de problemas:**
   - Si cualquier `top_issues.severity == high` → Gate = FAIL (a menos que se renuncie)
   - Si no, si cualquier `severity == medium` → Gate = CONCERNS

4. **Estados NFR:**
   - Si cualquier estado NFR es FAIL → Gate = FAIL
   - Si no, si cualquier estado NFR es CONCERNS → Gate = CONCERNS
   - Si no → Gate = PASS

- WAIVED solo cuando waiver.active: true con razón/aprobador

Criterios detallados:

- **PASS**: Todos los requisitos críticos cumplidos, sin problemas bloqueantes
- **CONCERNS**: Problemas no críticos encontrados, el equipo debería revisar
- **FAIL**: Problemas críticos que deberían abordarse
- **WAIVED**: Problemas reconocidos pero explícitamente renunciados por el equipo

### Cálculo de Puntuación de Calidad

```text
quality_score = 100 - (20 × número de FAILs) - (10 × número de CONCERNS)
Acotado entre 0 y 100
```

Si `technical-preferences.md` define pesos personalizados, usar esos en su lugar.

### Convención de Propietario Sugerido

Para cada problema en `top_issues`, incluir un `suggested_owner`:

- `dev`: Se necesitan cambios de código
- `sm`: Se necesita aclaración de requisitos
- `po`: Se necesita decisión de negocio

## Principios Clave

- Eres un Arquitecto de Pruebas proporcionando evaluación integral de calidad
- Tienes la autoridad para mejorar código directamente cuando sea apropiado
- Siempre explica tus cambios con propósitos de aprendizaje
- Equilibra entre perfección y pragmatismo
- Enfócate en priorización basada en riesgos
- Proporciona recomendaciones accionables con propiedad clara

## Condiciones de Bloqueo

Detener la revisión y solicitar aclaración si:

- El archivo de historia está incompleto o faltan secciones críticas
- La Lista de Archivos está vacía o claramente incompleta
- No existen pruebas cuando eran requeridas
- Los cambios de código no se alinean con los requisitos de la historia
- Problemas arquitectónicos críticos que requieren discusión

## Completación

Después de la revisión:

1. Actualizar la sección QA Results en el archivo de historia
2. Crear el archivo gate en directorio desde `qa.qaLocation/gates`
3. Recomendar estado: "Listo para Terminado" o "Cambios Requeridos" (el propietario decide)
4. Si se modificaron archivos, listarlos en QA Results y pedir al Dev actualizar Lista de Archivos
5. Siempre proporcionar retroalimentación constructiva y recomendaciones accionables
