<!-- Powered by Método Ceiba -->

# nfr-assess

Validación rápida de NFR enfocada en los cuatro principales: seguridad, rendimiento, confiabilidad, mantenibilidad.

## Entradas

```yaml
required:
  - story_id: '{epic}.{story}' # ej., "1.3"
  - story_path: `.ceiba-metodo/core-config.yaml` para el `devStoryLocation`

optional:
  - architecture_refs: `.ceiba-metodo/core-config.yaml` para el `architecture.architectureFile`
  - technical_preferences: `.ceiba-metodo/core-config.yaml` para el `technicalPreferences`
  - acceptance_criteria: Del archivo de historia
```

## Propósito

Evaluar requisitos no funcionales para una historia y generar:

1. Bloque YAML para la sección `nfr_validation` del archivo gate
2. Evaluación markdown breve guardada en `qa.qaLocation/assessments/{epic}.{story}-nfr-{YYYYMMDD}.md`

## Proceso

### 0. Seguridad para Entradas Faltantes

Si no se puede encontrar story_path o archivo de historia:

- Aún crear archivo de evaluación con nota: "Historia fuente no encontrada"
- Establecer todos los NFRs seleccionados a CONCERNS con notas: "Objetivo desconocido / evidencia faltante"
- Continuar con evaluación para proporcionar valor

### 1. Elicitar Alcance

**Modo interactivo:** Preguntar qué NFRs evaluar
**Modo no interactivo:** Por defecto los cuatro principales (seguridad, rendimiento, confiabilidad, mantenibilidad)

```text
¿Qué NFRs debo evaluar? (Ingresa números o presiona Enter para por defecto)
[1] Seguridad (por defecto)
[2] Rendimiento (por defecto)
[3] Confiabilidad (por defecto)
[4] Mantenibilidad (por defecto)
[5] Usabilidad
[6] Compatibilidad
[7] Portabilidad
[8] Adecuación Funcional

> [Enter para 1-4]
```

### 2. Verificar Umbrales

Buscar requisitos NFR en:

- Criterios de aceptación de historia
- Archivos `docs/architecture/*.md`
- `docs/technical-preferences.md`

**Modo interactivo:** Preguntar por umbrales faltantes
**Modo no interactivo:** Marcar como CONCERNS con "Objetivo desconocido"

```text
No se encontraron requisitos de rendimiento. ¿Cuál es tu tiempo de respuesta objetivo?
> 200ms para llamadas API

No se encontraron requisitos de seguridad. ¿Método de auth requerido?
> JWT con tokens de actualización
```

**Política de objetivos desconocidos:** Si falta un objetivo y no se proporciona, marcar estado como CONCERNS con notas: "Objetivo desconocido"

### 3. Evaluación Rápida

Para cada NFR seleccionado, verificar:

- ¿Hay evidencia de que está implementado?
- ¿Podemos validarlo?
- ¿Hay brechas obvias?

### 4. Generar Salidas

## Salida 1: Bloque YAML Gate

Generar SOLO para NFRs realmente evaluados (sin placeholders):

```yaml
# Gate YAML (copiar/pegar):
nfr_validation:
  _assessed: [security, performance, reliability, maintainability]
  security:
    status: CONCERNS
    notes: 'Sin limitación de tasa en endpoints de auth'
  performance:
    status: PASS
    notes: 'Tiempos de respuesta < 200ms verificados'
  reliability:
    status: PASS
    notes: 'Manejo de errores y reintentos implementados'
  maintainability:
    status: CONCERNS
    notes: 'Cobertura de pruebas al 65%, objetivo es 80%'
```

## Reglas de Estado Determinísticas

- **FAIL**: Cualquier NFR seleccionado tiene brecha crítica u objetivo claramente no cumplido
- **CONCERNS**: Sin FAILs, pero cualquier NFR es desconocido/parcial/falta evidencia
- **PASS**: Todos los NFRs seleccionados cumplen objetivos con evidencia

## Cálculo de Puntuación de Calidad

```
quality_score = 100
- 20 por cada atributo FAIL
- 10 por cada atributo CONCERNS
Piso en 0, techo en 100
```

Si `technical-preferences.md` define pesos personalizados, usar esos en su lugar.

## Salida 2: Reporte de Evaluación Breve

**SIEMPRE guardar en:** `qa.qaLocation/assessments/{epic}.{story}-nfr-{YYYYMMDD}.md`

```markdown
# Evaluación NFR: {epic}.{story}

Fecha: {fecha}
Revisor: Revisor par - Ceiba

<!-- Nota: Historia fuente no encontrada (si aplica) -->

## Resumen

- Seguridad: CONCERNS - Falta limitación de tasa
- Rendimiento: PASS - Cumple requisito <200ms
- Confiabilidad: PASS - Manejo de errores apropiado
- Mantenibilidad: CONCERNS - Cobertura de pruebas bajo objetivo

## Problemas Críticos

1. **Sin limitación de tasa** (Seguridad)
   - Riesgo: Ataques de fuerza bruta posibles
   - Solución: Agregar middleware de limitación de tasa a endpoints de auth

2. **Cobertura de pruebas 65%** (Mantenibilidad)
   - Riesgo: Rutas de código sin probar
   - Solución: Agregar pruebas para ramas no cubiertas

## Victorias Rápidas

- Agregar limitación de tasa: ~2 horas
- Aumentar cobertura de pruebas: ~4 horas
- Agregar monitoreo de rendimiento: ~1 hora
```

## Salida 3: Línea de Actualización de Historia

**Terminar con esta línea para que la tarea de revisión cite:**

```
Evaluación NFR: qa.qaLocation/assessments/{epic}.{story}-nfr-{YYYYMMDD}.md
```

## Salida 4: Línea de Integración Gate

**Siempre imprimir al final:**

```
Bloque NFR Gate listo → pegar en qa.qaLocation/gates/{epic}.{story}-{slug}.yml bajo nfr_validation
```

## Criterios de Evaluación

### Seguridad

**PASS si:**

- Autenticación implementada
- Autorización aplicada
- Validación de entrada presente
- Sin secretos hardcodeados

**CONCERNS si:**

- Falta limitación de tasa
- Encriptación débil
- Autorización incompleta

**FAIL si:**

- Sin autenticación
- Credenciales hardcodeadas
- Vulnerabilidades de inyección SQL

### Rendimiento

**PASS si:**

- Cumple objetivos de tiempo de respuesta
- Sin cuellos de botella obvios
- Uso razonable de recursos

**CONCERNS si:**

- Cerca de límites
- Faltan índices
- Sin estrategia de caché

**FAIL si:**

- Excede límites de tiempo de respuesta
- Fugas de memoria
- Consultas no optimizadas

### Confiabilidad

**PASS si:**

- Manejo de errores presente
- Degradación elegante
- Lógica de reintentos donde se necesita

**CONCERNS si:**

- Algunos casos de error no manejados
- Sin circuit breakers
- Faltan health checks

**FAIL si:**

- Sin manejo de errores
- Fallas en errores
- Sin mecanismos de recuperación

### Mantenibilidad

**PASS si:**

- Cobertura de pruebas cumple objetivo
- Código bien estructurado
- Documentación presente

**CONCERNS si:**

- Cobertura de pruebas bajo objetivo
- Algo de duplicación de código
- Falta documentación

**FAIL si:**

- Sin pruebas
- Código altamente acoplado
- Sin documentación

## Referencia Rápida

### Qué Verificar

```yaml
security:
  - Mecanismo de autenticación
  - Verificaciones de autorización
  - Validación de entrada
  - Gestión de secretos
  - Limitación de tasa

performance:
  - Tiempos de respuesta
  - Consultas de base de datos
  - Uso de caché
  - Consumo de recursos

reliability:
  - Manejo de errores
  - Lógica de reintentos
  - Circuit breakers
  - Health checks
  - Logging

maintainability:
  - Cobertura de pruebas
  - Estructura de código
  - Documentación
  - Dependencias
```

## Principios Clave

- Enfocarse en los cuatro NFRs principales por defecto
- Evaluación rápida, no análisis profundo
- Formato de salida listo para gate
- Hallazgos breves y accionables
- Omitir lo que no aplica
- Reglas de estado determinísticas para consistencia
- Objetivos desconocidos → CONCERNS, no suposiciones

---

## Apéndice: Referencia ISO 25010

<details>
<summary>Modelo de Calidad ISO 25010 Completo (click para expandir)</summary>

### Las 8 Características de Calidad

1. **Adecuación Funcional**: Completitud, corrección, apropiación
2. **Eficiencia de Rendimiento**: Comportamiento temporal, uso de recursos, capacidad
3. **Compatibilidad**: Coexistencia, interoperabilidad
4. **Usabilidad**: Aprendizaje, operabilidad, accesibilidad
5. **Confiabilidad**: Madurez, disponibilidad, tolerancia a fallas
6. **Seguridad**: Confidencialidad, integridad, autenticidad
7. **Mantenibilidad**: Modularidad, reusabilidad, testabilidad
8. **Portabilidad**: Adaptabilidad, instalabilidad

Usar estas al evaluar más allá de los cuatro principales.

</details>

<details>
<summary>Ejemplo: Análisis Profundo de Rendimiento (click para expandir)</summary>

```yaml
performance_deep_dive:
  response_times:
    p50: 45ms
    p95: 180ms
    p99: 350ms
  database:
    slow_queries: 2
    missing_indexes: ['users.email', 'orders.user_id']
  caching:
    hit_rate: 0%
    recommendation: 'Agregar Redis para datos de sesión'
  load_test:
    max_rps: 150
    breaking_point: 200 rps
```

</details>
