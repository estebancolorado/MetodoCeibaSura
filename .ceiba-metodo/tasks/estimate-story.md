<!-- Powered by Método Ceiba -->

# Estimate Story Task - Estimador de Historia de Usuario

## Purpose

Estimar tiempos de desarrollo para historias de usuario previamente refinadas que ya contienen tareas de implementación definidas. Se enfoca exclusivamente en calcular estimaciones precisas para diferentes perfiles de desarrollador, incluyendo el Método Ceiba que proporciona optimización significativa.

## When to Use This Task

**Use this task when:**

- La historia de usuario ya ha sido refinada por el SM con la tarea `refine-story`
- La historia tiene estado "Refinado (SM)" y contiene la sección "Tareas de Implementación"
- Se necesita una estimación detallada para planificación del sprint
- Se requiere evaluar diferentes opciones de desarrollo (tradicional vs Método Ceiba)

**Prerequisites:**

- Historia de usuario refinada completamente por SM
- Sección "Tareas de Implementación" definida en la historia
- Core-config.yaml configurado correctamente
- Acceso al código base para análisis de complejidad

## Task Execution Instructions

### 1. CONFIGURACIÓN Y CARGA DE HISTORIA

#### 1.1 Cargar Configuración

- Cargar `.ceiba-metodo/core-config.yaml`
- Extraer `devStoryLocation` para ubicación de historias
- Si no existe core-config.yaml, HALT: "core-config.yaml no encontrado. Ejecuta la instalación de BMad primero."

#### 1.2 Solicitar Historia a Estimar

**Preguntar al usuario:**
"¿Cuál es el número de la historia de usuario que deseas estimar?
Por favor, proporciona el número (ejemplo: 1, 2, 3, etc.)"

#### 1.3 Validar Historia

- Verificar que existe `{devStoryLocation}/{numero}.story.md`
- Cargar contenido completo de la historia
- Verificar estado: debe ser "Refinado (SM)" o similar
- **CRÍTICO**: Confirmar que tiene la sección "Tareas de Implementación" con tareas definidas
- Si no cumple requisitos, HALT: "La historia debe ser refinada primero usando la tarea `refine-story`"

### 2. ANÁLISIS DE CONTEXTO TÉCNICO

**Realizar investigación profunda del código base y documentación:**

**Análisis del código base:**

- Revisar complejidad del código existente y patrones similares
- Buscar precedentes y funcionalidades relacionadas
- Identificar refactorización necesaria
- Analizar puntos de integración críticos

**Documentar hallazgos:**

- Nivel de complejidad técnica detectado
- Funcionalidades similares encontradas
- Refactorización necesaria
- Puntos de integración críticos

### 3. EXTRACCIÓN DE TAREAS EXISTENTES

**USAR ÚNICAMENTE las tareas definidas en la historia refinada:**

- Extraer todas las tareas de la sección "Tareas de Implementación"
- NO crear nuevas tareas ni modificar las existentes
- Analizar descripción y complejidad específica de cada tarea
- Identificar dependencias entre tareas

### 4. ESTIMACIÓN POR PERFILES DE DESARROLLADOR

**Perfiles disponibles:**

**Jr (0-2 años):**

- Requiere documentación detallada y supervisión
- Mayor tiempo en investigación y debugging
- Menor eficiencia en tareas complejas

**Semi Sr (2-5 años):**

- Autonomía media, consulta en decisiones técnicas
- Eficiencia balanceada entre desarrollo y testing
- Capacidad de refactoring moderado

**Sr (5+ años):**

- Contexto completo del sistema, toma decisiones técnicas
- Alta eficiencia en todos los aspectos
- Lidera refactoring y optimizaciones

**Método Ceiba:**

- Proceso completo automatizado con agentes IA autónomos
- Flujo end-to-end: HU → refinamiento → estimación → desarrollo → QA
- Optimización significativa comparado con desarrollo tradicional
- Coordinación humana mínima requerida

### 5. FACTORES DE DESCUENTO

**Desarrollo Tradicional con IA (20-30% descuento):**

- Aplicar descuento en: desarrollo de código, creación de pruebas, documentación
- NO aplicar en: análisis, toma de decisiones, coordinación entre equipos

**Método Ceiba:**

- Calcular tiempo como 60% del "Senior Con IA"
- Aplicar este factor al tiempo total del Senior Con IA
- Representa la optimización del proceso automatizado completo

## Output Format

```markdown
### Estimación de Historia de Usuario: [Título de la HU]

#### Análisis de Complejidad

- **Nivel de complejidad**: [Baja/Media/Alta]
- **Justificación**: [Basado en análisis del código base]
- **Refactorización requerida**: [Sí/No - Justificación específica]
- **Precedentes encontrados**: [Funcionalidades similares]

#### Estimación por Tareas

| Tarea     | Descripción   | Jr Sin IA | Jr Con IA | Semi Sr Sin IA | Semi Sr Con IA | Sr Sin IA | Sr Con IA | Método Ceiba |
| --------- | ------------- | --------- | --------- | -------------- | -------------- | --------- | --------- | ------------ |
| [Tarea 1] | [Descripción] | X.X h     | X.X h     | X.X h          | X.X h          | X.X h     | X.X h     | X.X h        |
| [Tarea 2] | [Descripción] | X.X h     | X.X h     | X.X h          | X.X h          | X.X h     | X.X h     | X.X h        |
| [Tarea N] | [Descripción] | X.X h     | X.X h     | X.X h          | X.X h          | X.X h     | X.X h     | X.X h        |

#### Totales Estimados

| Perfil       | Total Sin IA | Total Con IA | Método Ceiba |
| ------------ | ------------ | ------------ | ------------ |
| Jr           | XX.X h       | XX.X h       | -            |
| Semi Sr      | XX.X h       | XX.X h       | -            |
| Sr           | XX.X h       | XX.X h       | -            |
| Método Ceiba | XX.X h       | -            | XX.X h       |

#### Análisis de Riesgo

- **Porcentaje de desviación estimado**: X%
- **Factores de riesgo**: [Identificados durante análisis]
- **Recomendaciones**: [Consideraciones adicionales]
```

### 6. VALIDACIÓN E INTEGRACIÓN

#### 6.1 Confirmación con Usuario

**Mensaje obligatorio:**
"He completado la estimación de la historia #{numero}. Por favor confirma si está correcta o necesitas ajustes. Una vez confirmada, la integraré automáticamente en el archivo de la historia."

#### 6.2 Integración Automática

**Una vez confirmada:**

- Añadir sección "## Estimación" al final del archivo de historia
- Mantener todo el contenido original intacto
- Confirmar: "✅ Estimación integrada en {devStoryLocation}/{numero}.story.md"

## Success Criteria

1. ✅ Historia cargada y validada correctamente
2. ✅ Tareas extraídas sin crear nuevas
3. ✅ Análisis de complejidad completado
4. ✅ Estimaciones calculadas para todos los perfiles
5. ✅ Factores de descuento aplicados correctamente
6. ✅ Usuario confirmó la estimación
7. ✅ Estimación integrada en archivo de historia

## Important Notes

- **NO CREAR TAREAS**: Usar únicamente las definidas en "Tareas de Implementación"
- **HALT si no hay tareas**: Solicitar refinamiento si faltan tareas
- **Método Ceiba**: Calcular como 60% del tiempo "Senior Con IA"
- **Análisis obligatorio**: Siempre investigar código base y precedentes antes de estimar
- **No integrar hasta confirmación**: Esperar aprobación del usuario
