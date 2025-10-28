<!-- Powered by Método Ceiba -->

# test-design

Crear escenarios de prueba integrales con recomendaciones apropiadas de nivel de prueba para implementación de historia.

## Entradas

```yaml
required:
  - story_id: '{epic}.{story}' # ej., "1.3"
  - story_path: '{devStoryLocation}/{epic}.{story}.*.md' # Ruta desde core-config.yaml
  - story_title: '{title}' # Si falta, derivar del archivo de historia H1
  - story_slug: '{slug}' # Si falta, derivar del título (minúsculas, con guiones)
```

## Propósito

Diseñar una estrategia de prueba completa que identifique qué probar, en qué nivel (unitaria/integración/e2e), y por qué. Esto asegura cobertura eficiente de pruebas sin redundancia mientras mantiene límites apropiados de prueba.

## Dependencias

```yaml
data:
  - test-levels-framework.md # Criterios de decisión Unit/Integration/E2E
  - test-priorities-matrix.md # Sistema de clasificación P0/P1/P2/P3
```

## Proceso

### 1. Analizar Requisitos de Historia

Desglosar cada criterio de aceptación en escenarios probables. Para cada AC:

- Identificar la funcionalidad principal a probar
- Determinar variaciones de datos necesarias
- Considerar condiciones de error
- Notar casos límite

### 2. Aplicar Marco de Nivel de Prueba

**Referencia:** Cargar `test-levels-framework.md` para criterios detallados

Reglas rápidas:

- **Unitaria**: Lógica pura, algoritmos, cálculos
- **Integración**: Interacciones de componentes, operaciones de BD
- **E2E**: Jornadas críticas de usuario, cumplimiento

### 3. Asignar Prioridades

**Referencia:** Cargar `test-priorities-matrix.md` para clasificación

Asignación rápida de prioridad:

- **P0**: Crítico para ingresos, seguridad, cumplimiento
- **P1**: Jornadas principales de usuario, frecuentemente usado
- **P2**: Funcionalidades secundarias, funciones de admin
- **P3**: Nice-to-have, raramente usado

### 4. Diseñar Escenarios de Prueba

Para cada necesidad de prueba identificada, crear:

```yaml
test_scenario:
  id: '{epic}.{story}-{LEVEL}-{SEQ}'
  requirement: 'Referencia AC'
  priority: P0|P1|P2|P3
  level: unit|integration|e2e
  description: 'Qué se está probando'
  justification: 'Por qué se eligió este nivel'
  mitigates_risks: ['RISK-001'] # Si existe perfil de riesgo
```

### 5. Validar Cobertura

Asegurar:

- Cada AC tiene al menos una prueba
- Sin cobertura duplicada entre niveles
- Rutas críticas tienen múltiples niveles
- Se abordan mitigaciones de riesgo

## Salidas

### Salida 1: Documento de Diseño de Pruebas

**Guardar en:** `qa.qaLocation/assessments/{epic}.{story}-test-design-{YYYYMMDD}.md`

```markdown
# Diseño de Pruebas: Historia {epic}.{story}

Fecha: {fecha}
Diseñador: Revisor par - Ceiba (Arquitecto de Pruebas)

## Resumen de Estrategia de Pruebas

- Total de escenarios de prueba: X
- Pruebas unitarias: Y (A%)
- Pruebas de integración: Z (B%)
- Pruebas E2E: W (C%)
- Distribución de prioridad: P0: X, P1: Y, P2: Z

## Escenarios de Prueba por Criterios de Aceptación

### AC1: {descripción}

#### Escenarios

| ID           | Nivel       | Prioridad | Prueba                    | Justificación           |
| ------------ | ----------- | --------- | ------------------------- | ----------------------- |
| 1.3-UNIT-001 | Unitaria    | P0        | Validar formato entrada   | Lógica validación pura  |
| 1.3-INT-001  | Integración | P0        | Servicio procesa petición | Flujo multi-componente  |
| 1.3-E2E-001  | E2E         | P1        | Usuario completa jornada  | Validación ruta crítica |

[Continuar para todos los ACs...]

## Cobertura de Riesgos

[Mapear escenarios de prueba a riesgos identificados si existe perfil de riesgo]

## Orden de Ejecución Recomendado

1. Pruebas unitarias P0 (fallar rápido)
2. Pruebas de integración P0
3. Pruebas E2E P0
4. Pruebas P1 en orden
5. P2+ según tiempo permita
```

### Salida 2: Bloque YAML Gate

Generar para inclusión en quality gate:

```yaml
test_design:
  scenarios_total: X
  by_level:
    unit: Y
    integration: Z
    e2e: W
  by_priority:
    p0: A
    p1: B
    p2: C
  coverage_gaps: [] # Listar cualquier AC sin pruebas
```

### Salida 3: Referencias de Trazabilidad

Imprimir para uso por tarea trace-requirements:

```text
Matriz de diseño de pruebas: qa.qaLocation/assessments/{epic}.{story}-test-design-{YYYYMMDD}.md
Pruebas P0 identificadas: {count}
```

## Lista de Verificación de Calidad

Antes de finalizar, verificar:

- [ ] Cada AC tiene cobertura de prueba
- [ ] Los niveles de prueba son apropiados (no sobre-probar)
- [ ] Sin cobertura duplicada entre niveles
- [ ] Las prioridades se alinean con riesgo de negocio
- [ ] IDs de prueba siguen convención de nombres
- [ ] Los escenarios son atómicos e independientes

## Principios Clave

- **Shift left**: Preferir unitaria sobre integración, integración sobre E2E
- **Basado en riesgo**: Enfocarse en lo que podría salir mal
- **Cobertura eficiente**: Probar una vez al nivel correcto
- **Mantenibilidad**: Considerar mantenimiento de pruebas a largo plazo
- **Retroalimentación rápida**: Pruebas rápidas se ejecutan primero
