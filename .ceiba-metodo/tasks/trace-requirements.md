<!-- Powered by Método Ceiba -->

# trace-requirements

Mapear requisitos de historia a casos de prueba usando patrones Given-When-Then para trazabilidad integral.

## Propósito

Crear una matriz de trazabilidad de requisitos que asegure que cada criterio de aceptación tenga cobertura de prueba correspondiente. Esta tarea ayuda a identificar brechas en pruebas y asegura que todos los requisitos sean validados.

**IMPORTANTE**: Given-When-Then se usa aquí para documentar el mapeo entre requisitos y pruebas, NO para escribir el código real de prueba. Las pruebas deben seguir los estándares de prueba de tu proyecto (sin sintaxis BDD en código de prueba).

## Prerrequisitos

- Archivo de historia con criterios de aceptación claros
- Acceso a archivos de prueba o especificaciones de prueba
- Comprensión de la implementación

## Proceso de Trazabilidad

### 1. Extraer Requisitos

Identificar todos los requisitos probables de:

- Criterios de Aceptación (fuente primaria)
- Declaración de historia de usuario
- Tareas/subtareas con comportamientos específicos
- Requisitos no funcionales mencionados
- Casos límite documentados

### 2. Mapear a Casos de Prueba

Para cada requisito, documentar qué pruebas lo validan. Usar Given-When-Then para describir qué valida la prueba (no cómo está escrita):

```yaml
requirement: 'AC1: Usuario puede hacer login con credenciales válidas'
test_mappings:
  - test_file: 'auth/login.test.ts'
    test_case: 'debería hacer login exitosamente con email y password válidos'
    # Given-When-Then describe QUÉ valida la prueba, no CÓMO está codificada
    given: 'Un usuario registrado con credenciales válidas'
    when: 'Envían el formulario de login'
    then: 'Son redirigidos al dashboard y se crea la sesión'
    coverage: full

  - test_file: 'e2e/auth-flow.test.ts'
    test_case: 'flujo completo de login'
    given: 'Usuario en página de login'
    when: 'Ingresando credenciales válidas y enviando'
    then: 'Dashboard carga con datos de usuario'
    coverage: integration
```

### 3. Análisis de Cobertura

Evaluar cobertura para cada requisito:

**Niveles de Cobertura:**

- `full`: Requisito completamente probado
- `partial`: Algunos aspectos probados, existen brechas
- `none`: No se encontró cobertura de prueba
- `integration`: Cubierto solo en pruebas de integración/e2e
- `unit`: Cubierto solo en pruebas unitarias

### 4. Identificación de Brechas

Documentar cualquier brecha encontrada:

```yaml
coverage_gaps:
  - requirement: 'AC3: Email de reset de password enviado en 60 segundos'
    gap: 'Sin prueba para tiempo de entrega de email'
    severity: medium
    suggested_test:
      type: integration
      description: 'Probar cumplimiento SLA del servicio de email'

  - requirement: 'AC5: Soportar 1000 usuarios concurrentes'
    gap: 'Sin pruebas de carga implementadas'
    severity: high
    suggested_test:
      type: performance
      description: 'Prueba de carga con 1000 conexiones concurrentes'
```

## Salidas

### Salida 1: Bloque YAML Gate

**Generar para pegar en archivo gate bajo `trace`:**

```yaml
trace:
  totals:
    requirements: X
    full: Y
    partial: Z
    none: W
  planning_ref: 'qa.qaLocation/assessments/{epic}.{story}-test-design-{YYYYMMDD}.md'
  uncovered:
    - ac: 'AC3'
      reason: 'Sin prueba encontrada para tiempo de reset de password'
  notes: 'Ver qa.qaLocation/assessments/{epic}.{story}-trace-{YYYYMMDD}.md'
```

### Salida 2: Reporte de Trazabilidad

**Guardar en:** `qa.qaLocation/assessments/{epic}.{story}-trace-{YYYYMMDD}.md`

Crear un reporte de trazabilidad con:

```markdown
# Matriz de Trazabilidad de Requisitos

## Historia: {epic}.{story} - {título}

### Resumen de Cobertura

- Total de Requisitos: X
- Completamente Cubiertos: Y (Z%)
- Parcialmente Cubiertos: A (B%)
- No Cubiertos: C (D%)

### Mapeos de Requisitos

#### AC1: {Criterio de Aceptación 1}

**Cobertura: FULL**

Mapeos Given-When-Then:

- **Prueba Unitaria**: `auth.service.test.ts::validateCredentials`
  - Given: Credenciales de usuario válidas
  - When: Método de validación llamado
  - Then: Retorna true con objeto de usuario

- **Prueba de Integración**: `auth.integration.test.ts::loginFlow`
  - Given: Usuario con cuenta válida
  - When: API de login llamada
  - Then: Token JWT retornado y sesión creada

#### AC2: {Criterio de Aceptación 2}

**Cobertura: PARTIAL**

[Continuar para todos los ACs...]

### Brechas Críticas

1. **Requisitos de Rendimiento**
   - Brecha: Sin pruebas de carga para usuarios concurrentes
   - Riesgo: Alto - Podría fallar bajo carga de producción
   - Acción: Implementar pruebas de carga usando k6 o similar

2. **Requisitos de Seguridad**
   - Brecha: Limitación de tasa no probada
   - Riesgo: Medio - Potencial vulnerabilidad DoS
   - Acción: Agregar pruebas de límite de tasa a suite de integración

### Recomendaciones de Diseño de Pruebas

Basado en brechas identificadas, recomendar:

1. Escenarios de prueba adicionales necesarios
2. Tipos de prueba a implementar (unitaria/integración/e2e/rendimiento)
3. Requisitos de datos de prueba
4. Estrategias de mock/stub

### Evaluación de Riesgos

- **Riesgo Alto**: Requisitos sin cobertura
- **Riesgo Medio**: Requisitos con solo cobertura parcial
- **Riesgo Bajo**: Requisitos con cobertura completa unitaria + integración
```

## Mejores Prácticas de Trazabilidad

### Given-When-Then para Mapeo (No Código de Prueba)

Usar Given-When-Then para documentar qué valida cada prueba:

**Given**: El contexto inicial que configura la prueba

- Qué estado/datos prepara la prueba
- Contexto de usuario siendo simulado
- Precondiciones del sistema

**When**: La acción que realiza la prueba

- Qué ejecuta la prueba
- Llamadas API o acciones de usuario probadas
- Eventos disparados

**Then**: Lo que aserta la prueba

- Resultados esperados verificados
- Cambios de estado verificados
- Valores validados

**Nota**: Esto es solo para documentación. El código real de prueba sigue los estándares de tu proyecto (ej., bloques describe/it, sin sintaxis BDD).

### Prioridad de Cobertura

Priorizar cobertura basada en:

1. Flujos críticos de negocio
2. Requisitos relacionados con seguridad
3. Requisitos de integridad de datos
4. Funcionalidades orientadas al usuario
5. SLAs de rendimiento

### Granularidad de Prueba

Mapear en niveles apropiados:

- Pruebas unitarias para lógica de negocio
- Pruebas de integración para interacción de componentes
- Pruebas E2E para jornadas de usuario
- Pruebas de rendimiento para NFRs

## Indicadores de Calidad

Buena trazabilidad muestra:

- Cada AC tiene al menos una prueba
- Rutas críticas tienen múltiples niveles de prueba
- Casos límite están explícitamente cubiertos
- NFRs tienen tipos de prueba apropiados
- Given-When-Then claro para cada prueba

## Señales de Alerta

Vigilar por:

- ACs sin cobertura de prueba
- Pruebas que no mapean a requisitos
- Descripciones de prueba vagas
- Cobertura de casos límite faltante
- NFRs sin pruebas específicas

## Integración con Gates

Esta trazabilidad alimenta quality gates:

- Brechas críticas → FAIL
- Brechas menores → CONCERNS
- Pruebas P0 faltantes de test-design → CONCERNS
- Cobertura completa → Contribución PASS

### Salida 3: Línea Hook de Historia

**Imprimir esta línea para que la tarea de revisión cite:**

```text
Matriz de trazabilidad: qa.qaLocation/assessments/{epic}.{story}-trace-{YYYYMMDD}.md
```

## Principios Clave

- Cada requisito debe ser probable
- Usar Given-When-Then para claridad
- Identificar tanto presencia como ausencia
- Priorizar basado en riesgo
- Hacer recomendaciones accionables
