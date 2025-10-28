<!-- Powered by Método Ceiba -->

# risk-profile

Generar una matriz de evaluación de riesgo integral para una implementación de historia usando análisis de probabilidad × impacto.

## Entradas

```yaml
required:
  - story_id: '{epic}.{story}' # ej., "1.3"
  - story_path: 'docs/stories/{epic}.{story}.*.md'
  - story_title: '{title}' # Si falta, derivar del archivo de historia H1
  - story_slug: '{slug}' # Si falta, derivar del título (minúsculas, con guiones)
```

## Propósito

Identificar, evaluar y priorizar riesgos en la implementación de historia. Proporcionar estrategias de mitigación de riesgos y áreas de enfoque de pruebas basadas en niveles de riesgo.

## Marco de Evaluación de Riesgos

### Categorías de Riesgo

**Prefijos de Categoría:**

- `TECH`: Riesgos Técnicos
- `SEC`: Riesgos de Seguridad
- `PERF`: Riesgos de Rendimiento
- `DATA`: Riesgos de Datos
- `BUS`: Riesgos de Negocio
- `OPS`: Riesgos Operacionales

1. **Riesgos Técnicos (TECH)**
   - Complejidad de arquitectura
   - Desafíos de integración
   - Deuda técnica
   - Preocupaciones de escalabilidad
   - Dependencias del sistema

2. **Riesgos de Seguridad (SEC)**
   - Fallas de autenticación/autorización
   - Vulnerabilidades de exposición de datos
   - Ataques de inyección
   - Problemas de gestión de sesión
   - Debilidades criptográficas

3. **Riesgos de Rendimiento (PERF)**
   - Degradación del tiempo de respuesta
   - Cuellos de botella de throughput
   - Agotamiento de recursos
   - Optimización de consultas de base de datos
   - Fallas de caché

4. **Riesgos de Datos (DATA)**
   - Potencial de pérdida de datos
   - Corrupción de datos
   - Violaciones de privacidad
   - Problemas de cumplimiento
   - Brechas de backup/recuperación

5. **Riesgos de Negocio (BUS)**
   - Funcionalidad no cumple necesidades del usuario
   - Impacto en ingresos
   - Daño a reputación
   - No cumplimiento regulatorio
   - Tiempo de mercado

6. **Riesgos Operacionales (OPS)**
   - Fallas de despliegue
   - Brechas de monitoreo
   - Preparación de respuesta a incidentes
   - Inadecuación de documentación
   - Problemas de transferencia de conocimiento

## Proceso de Análisis de Riesgos

### 1. Identificación de Riesgos

Para cada categoría, identificar riesgos específicos:

```yaml
risk:
  id: 'SEC-001' # Usar prefijos: SEC, PERF, DATA, BUS, OPS, TECH
  category: security
  title: 'Validación de entrada insuficiente en formularios de usuario'
  description: 'Entradas de formulario no sanitizadas apropiadamente podrían llevar a ataques XSS'
  affected_components:
    - 'UserRegistrationForm'
    - 'ProfileUpdateForm'
  detection_method: 'Revisión de código reveló validación faltante'
```

### 2. Evaluación de Riesgos

Evaluar cada riesgo usando probabilidad × impacto:

**Niveles de Probabilidad:**

- `Alto (3)`: Probable que ocurra (>70% de posibilidad)
- `Medio (2)`: Posible ocurrencia (30-70% de posibilidad)
- `Bajo (1)`: Poco probable que ocurra (<30% de posibilidad)

**Niveles de Impacto:**

- `Alto (3)`: Consecuencias severas (violación de datos, sistema caído, pérdida financiera mayor)
- `Medio (2)`: Consecuencias moderadas (rendimiento degradado, problemas menores de datos)
- `Bajo (1)`: Consecuencias menores (problemas cosméticos, ligera inconveniencia)

### Puntuación de Riesgo = Probabilidad × Impacto

- 9: Riesgo Crítico (Rojo)
- 6: Riesgo Alto (Naranja)
- 4: Riesgo Medio (Amarillo)
- 2-3: Riesgo Bajo (Verde)
- 1: Riesgo Mínimo (Azul)

### 3. Priorización de Riesgos

Crear matriz de riesgo:

```markdown
## Matriz de Riesgo

| Risk ID  | Descripción            | Probabilidad | Impacto   | Puntuación | Prioridad |
| -------- | ---------------------- | ------------ | --------- | ---------- | --------- |
| SEC-001  | Vulnerabilidad XSS     | Alto (3)     | Alto (3)  | 9          | Crítico   |
| PERF-001 | Consulta lenta en dash | Medio (2)    | Medio (2) | 4          | Medio     |
| DATA-001 | Falla de backup        | Bajo (1)     | Alto (3)  | 3          | Bajo      |
```

### 4. Estrategias de Mitigación de Riesgos

Para cada riesgo identificado, proporcionar mitigación:

```yaml
mitigation:
  risk_id: 'SEC-001'
  strategy: 'preventive' # preventive|detective|corrective
  actions:
    - 'Implementar librería de validación de entrada (ej., validator.js)'
    - 'Agregar headers CSP para prevenir ejecución XSS'
    - 'Sanitizar todas las entradas de usuario antes del almacenamiento'
    - 'Escapar todas las salidas en plantillas'
  testing_requirements:
    - 'Pruebas de seguridad con OWASP ZAP'
    - 'Pruebas de penetración manual de formularios'
    - 'Pruebas unitarias para funciones de validación'
  residual_risk: 'Bajo - Algunas vulnerabilidades zero-day pueden permanecer'
  owner: 'dev'
  timeline: 'Antes del despliegue'
```

## Salidas

### Salida 1: Bloque YAML Gate

Generar para pegar en archivo gate bajo `risk_summary`:

**Reglas de salida:**

- Solo incluir riesgos evaluados; no emitir placeholders
- Ordenar riesgos por puntuación (desc) al emitir highest y cualquier lista tabular
- Si no hay riesgos: totales todos ceros, omitir highest, mantener arrays de recomendaciones vacías

```yaml
# risk_summary (pegar en archivo gate):
risk_summary:
  totals:
    critical: X # puntuación 9
    high: Y # puntuación 6
    medium: Z # puntuación 4
    low: W # puntuación 2-3
  highest:
    id: SEC-001
    score: 9
    title: 'XSS en formulario de perfil'
  recommendations:
    must_fix:
      - 'Agregar sanitización de entrada & CSP'
    monitor:
      - 'Agregar alertas de seguridad para endpoints de auth'
```

### Salida 2: Reporte Markdown

**Guardar en:** `qa.qaLocation/assessments/{epic}.{story}-risk-{YYYYMMDD}.md`

```markdown
# Perfil de Riesgo: Historia {epic}.{story}

Fecha: {fecha}
Revisor: Revisor par - Ceiba (Arquitecto de Pruebas)

## Resumen Ejecutivo

- Total de Riesgos Identificados: X
- Riesgos Críticos: Y
- Riesgos Altos: Z
- Puntuación de Riesgo: XX/100 (calculada)

## Riesgos Críticos Requiriendo Atención Inmediata

### 1. [ID]: Título del Riesgo

**Puntuación: 9 (Crítico)**
**Probabilidad**: Alto - Razonamiento detallado
**Impacto**: Alto - Consecuencias potenciales
**Mitigación**:

- Acción inmediata requerida
- Pasos específicos a tomar
  **Enfoque de Pruebas**: Escenarios de prueba específicos necesarios

## Distribución de Riesgos

### Por Categoría

- Seguridad: X riesgos (Y críticos)
- Rendimiento: X riesgos (Y críticos)
- Datos: X riesgos (Y críticos)
- Negocio: X riesgos (Y críticos)
- Operacional: X riesgos (Y críticos)

### Por Componente

- Frontend: X riesgos
- Backend: X riesgos
- Base de datos: X riesgos
- Infraestructura: X riesgos

## Registro Detallado de Riesgos

[Tabla completa de todos los riesgos con puntuaciones y mitigaciones]

## Estrategia de Pruebas Basada en Riesgos

### Prioridad 1: Pruebas de Riesgo Crítico

- Escenarios de prueba para riesgos críticos
- Tipos de prueba requeridos (seguridad, carga, caos)
- Requisitos de datos de prueba

### Prioridad 2: Pruebas de Riesgo Alto

- Escenarios de pruebas de integración
- Cobertura de casos límite

### Prioridad 3: Pruebas de Riesgo Medio/Bajo

- Pruebas funcionales estándar
- Suite de pruebas de regresión

## Criterios de Aceptación de Riesgos

### Debe Arreglarse Antes de Producción

- Todos los riesgos críticos (puntuación 9)
- Riesgos altos afectando seguridad/datos

### Puede Desplegarse con Mitigación

- Riesgos medios con controles compensatorios
- Riesgos bajos con monitoreo en lugar

### Riesgos Aceptados

- Documentar cualquier riesgo que el equipo acepta
- Incluir aprobación de autoridad apropiada

## Requisitos de Monitoreo

Monitoreo post-despliegue para:

- Métricas de rendimiento para riesgos PERF
- Alertas de seguridad para riesgos SEC
- Tasas de error para riesgos operacionales
- KPIs de negocio para riesgos de negocio

## Disparadores de Revisión de Riesgos

Revisar y actualizar perfil de riesgo cuando:

- La arquitectura cambia significativamente
- Se agregan nuevas integraciones
- Se descubren vulnerabilidades de seguridad
- Se reportan problemas de rendimiento
- Cambian requisitos regulatorios
```

## Algoritmo de Puntuación de Riesgos

Calcular puntuación general de riesgo de historia:

```text
Puntuación Base = 100
Para cada riesgo:
  - Crítico (9): Deducir 20 puntos
  - Alto (6): Deducir 10 puntos
  - Medio (4): Deducir 5 puntos
  - Bajo (2-3): Deducir 2 puntos

Puntuación mínima = 0 (extremadamente riesgoso)
Puntuación máxima = 100 (riesgo mínimo)
```

## Recomendaciones Basadas en Riesgos

Basado en perfil de riesgo, recomendar:

1. **Prioridad de Pruebas**
   - Qué pruebas ejecutar primero
   - Tipos de prueba adicionales necesarios
   - Requisitos de ambiente de prueba

2. **Enfoque de Desarrollo**
   - Áreas de énfasis de revisión de código
   - Validación adicional necesaria
   - Controles de seguridad a implementar

3. **Estrategia de Despliegue**
   - Despliegue por fases para cambios de alto riesgo
   - Feature flags para funcionalidades riesgosas
   - Procedimientos de rollback

4. **Configuración de Monitoreo**
   - Métricas a rastrear
   - Alertas a configurar
   - Requisitos de dashboard

## Integración con Quality Gates

**Mapeo determinístico de gate:**

- Cualquier riesgo con puntuación ≥ 9 → Gate = FAIL (a menos que se renuncie)
- Si no, si cualquier puntuación ≥ 6 → Gate = CONCERNS
- Si no → Gate = PASS
- Riesgos no mitigados → Documentar en gate

### Salida 3: Línea Hook de Historia

**Imprimir esta línea para que la tarea de revisión cite:**

```text
Perfil de riesgo: qa.qaLocation/assessments/{epic}.{story}-risk-{YYYYMMDD}.md
```

## Principios Clave

- Identificar riesgos temprano y sistemáticamente
- Usar puntuación consistente de probabilidad × impacto
- Proporcionar estrategias de mitigación accionables
- Vincular riesgos a requisitos específicos de prueba
- Rastrear riesgo residual después de mitigación
- Actualizar perfil de riesgo conforme evoluciona la historia
