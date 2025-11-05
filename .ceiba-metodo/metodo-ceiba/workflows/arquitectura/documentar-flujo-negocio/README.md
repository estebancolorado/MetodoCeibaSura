# Documentar Flujo de Negocio

## Propósito

Documentar flujos de negocio críticos del sistema mediante diagramas de secuencia detallados que ilustren las interacciones entre componentes, incluyendo rutas de éxito, manejo de errores y operaciones asíncronas. Esta tarea está optimizada para sistemas empresariales complejos con múltiples componentes e integraciones.

## Cuándo Usar Este Workflow

**Usa este workflow cuando:**

- Necesitas documentar un flujo de negocio crítico y complejo
- El flujo involucra múltiples componentes/servicios con interacciones asíncronas
- Requieres clarificar decisiones arquitectónicas o integraciones complejas
- Nuevos desarrolladores necesitan entender workflows clave del sistema
- Buscas documentar rutas de manejo de errores y recuperación
- El flujo incluye APIs externas, message queues o procesos batch

**Prerequisites:**

- Conocimiento del flujo de negocio a documentar
- Acceso a documentación existente (si está disponible)
- Comprensión de los componentes involucrados en el flujo
- Acceso al GPS arquitectónico del sistema (si existe)

## Estructura del Workflow

```
documentar-flujo-negocio/
├── workflow.yaml          # Configuración del workflow
├── instructions.md        # Pasos detallados de ejecución
├── template.md           # Plantilla de documentación final
├── checklist.md          # Lista de validación de completitud
└── README.md            # Esta documentación
```

## Variables del Workflow

### Variables Requeridas (del config)
- `user_name`: Nombre del usuario
- `communication_language`: Idioma de comunicación
- `architecture_sharded_location`: Ubicación de documentación arquitectónica

### Variables Elicitadas Durante Ejecución
- `flow_name`: Nombre del flujo de negocio
- `flow_description`: Descripción del proceso de negocio
- `main_components`: Componentes principales (3-5)
- `has_async`: Si incluye comunicación asíncrona
- `has_external_integrations`: Si hay integraciones externas
- `business_process`: Descripción breve del proceso
- `critical_error_points`: Puntos críticos donde ocurren errores

## Resultado Esperado

El workflow genera un archivo de documentación completo:

```
{architecture_sharded_location}/flujo-{nombre-flujo}.md
```

### Contenido del Documento Final

1. **Introducción**
   - Descripción del flujo
   - Scope del documento
   - Componentes involucrados

2. **Diagramas de Secuencia**
   - Flujo principal con Mermaid
   - Manejo de errores (si aplica)
   - Operaciones asíncronas (si aplica)

3. **Estados y Transiciones**
   - Diagrama de estados del flujo

4. **Configuración Técnica**
   - Parámetros de configuración
   - Message queues (si aplica)

5. **Métricas y Monitoreo**
   - Puntos críticos de medición
   - Logs a monitorear

6. **Casos de Prueba**
   - Escenarios en formato Gherkin
   - TC001: Flujo exitoso
   - TC002: Error crítico
   - TC003: Timeout

7. **Troubleshooting**
   - Problemas comunes y soluciones
   - Comandos de diagnóstico

8. **Optimizaciones Futuras**
   - Oportunidades de mejora
   - Roadmap de evolución

## Flujo de Ejecución

### Fase 1: Configuración y Contexto
1. Cargar configuración del proyecto
2. Elicitar contexto del flujo (preguntas obligatorias)
3. Crear archivo inicial con template

### Fase 2: Análisis Profundo
1. Revisar contexto arquitectónico existente
2. Analizar componentes del flujo
3. Mapear interacciones y secuencias
4. Analizar escenarios de error

### Fase 3: Creación de Documentación
1. Generar diagramas de secuencia
2. Documentar manejo de errores
3. Documentar operaciones asíncronas

### Fase 4: Información Técnica
1. Generar diagrama de estados
2. Documentar configuración y parámetros
3. Definir métricas y monitoreo

### Fase 5: Casos de Prueba
1. Definir escenarios de prueba críticos
2. Documentar troubleshooting

### Fase 6: Optimizaciones
1. Identificar oportunidades de mejora
2. Completar referencias

### Fase 7: Validación
1. Revisar documentación generada
2. Validar con usuario final

## Características Especiales

- **Diagramas Automáticos**: Genera diagramas Mermaid basados en componentes identificados
- **Validación Interactiva**: Pregunta al usuario en puntos críticos
- **Template Flexible**: Se adapta a flujos con/sin operaciones asíncronas
- **Casos de Prueba**: Genera automáticamente casos en formato Gherkin
- **Troubleshooting**: Incluye sección práctica para operadores

## Integración con Método Ceiba

Este workflow es parte del ecosistema Método Ceiba - Arquitecto y:

- **Se integra** con el GPS arquitectónico existente
- **Referencia** documentación de componentes individuales
- **Sigue** estándares de documentación del método
- **Genera** archivos compatibles con el sistema de documentación

## Validación y Calidad

El workflow incluye un checklist exhaustivo de 72 puntos que valida:

- ✅ **Completitud**: Toda la información necesaria está presente
- ✅ **Calidad Técnica**: Diagramas y sintaxis son correctos
- ✅ **Usabilidad**: El documento sirve a su audiencia target
- ✅ **Mantenimiento**: Proceso de actualización está definido

**Criterio de Aprobación**: Mínimo 90% (65/72 items)

## Comandos de Ejecución

Para usar este workflow desde BMad Builder:

```
*create-workflow documentar-flujo-negocio
```

O directamente:

```yaml
workflow: "{project-root}/.ceiba-metodo/metodo-ceiba/workflows/arquitectura/documentar-flujo-negocio/workflow.yaml"
```

---

_Documentación del Workflow - Método Ceiba_  
_Versión: 1.0_  
_Compatible con: BMAD v6_