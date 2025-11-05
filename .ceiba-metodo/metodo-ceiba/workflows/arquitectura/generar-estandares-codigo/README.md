# Generar Estándares de Código - Workflow

## 🎯 **Propósito**

Este workflow automatiza la creación o actualización del documento de estándares de código basándose en análisis del código existente, documentación previa y mejores prácticas específicas del proyecto. Identifica patrones, convenciones y reglas de desarrollo que deben ser seguidas por el equipo.

## 📋 **Cuándo Usar Este Workflow**

**Usa este workflow cuando:**

- Necesitas crear estándares de código para un proyecto existente
- Quieres formalizar las convenciones no documentadas del equipo
- Requieres actualizar estándares obsoletos o incompletos
- El proyecto no tiene documentación de estándares pero sí código establecido
- Nuevos miembros del equipo necesitan guías claras de desarrollo

## 🔧 **Prerequisites**

- Proyecto con código base existente (preferiblemente)
- Configuración de `core-config.yaml` con `architectureShardedLocation`
- Acceso al código fuente para análisis

## 📁 **Estructura de Archivos**

```
generar-estandares-codigo/
├── workflow.yaml          # Configuración del workflow
├── instructions.md        # Pasos detallados del workflow
├── template.md           # Plantilla del documento final
├── checklist.md          # Lista de validación
└── README.md            # Esta documentación
```

## ⚡ **Ejecución Rápida**

1. **Activar el workflow** desde BMad Builder
2. **Responder las preguntas** sobre documentación existente, convenciones y herramientas
3. **Revisar el análisis** del código base y documentación
4. **Validar el documento** generado de estándares
5. **Confirmar y ajustar** según sea necesario

## 🔄 **Proceso del Workflow**

### Paso 0: Configuración y Contexto Inicial
- Carga configuración del proyecto
- Elicita información sobre documentación existente, convenciones no documentadas y herramientas

### Paso 1: Análisis de Documentación Existente
- Revisa documentación de estándares existente
- Extrae información de archivos como CONTRIBUTING.md, README.md, configuraciones de linters

### Paso 2: Análisis del Código Base
- Analiza patrones de código, nomenclatura y estructura
- Identifica tecnologías, herramientas y convenciones implícitas

### Paso 3: Consolidación de Estándares
- Combina información de todas las fuentes
- Categoriza en estándares obligatorios, recomendados y configuración de herramientas

### Paso 4: Creación del Documento
- Genera `coding-standards.md` usando la plantilla
- Valida y refina con el usuario

### Paso 5: Entrega y Comunicación
- Proporciona resumen completo del trabajo realizado

## 📄 **Salida del Workflow**

**Archivo generado:**
- `{architectureShardedLocation}/coding-standards.md` - Documento completo de estándares de código

**Contenido incluido:**
- Estándares obligatorios con ejemplos específicos
- Convenciones recomendadas del proyecto
- Configuración de herramientas (linters, formatters)
- Estándares de testing y documentación
- Métricas de calidad y umbrales
- Mejores prácticas específicas por tecnología
- Proceso de actualización y mantenimiento

## ✅ **Criterios de Calidad**

- **Basado en realidad**: Estándares extraídos del código existente
- **Práctico**: Reglas aplicables y verificables
- **Completo**: Cubre tecnologías principales del proyecto
- **Específico**: Ejemplos concretos de código correcto e incorrecto
- **Herramientas**: Configuración de linters y formatters incluida
- **Evolutivo**: Proceso de actualización definido

## 🎯 **Variables del Workflow**

| Variable | Descripción | Fuente |
|----------|-------------|--------|
| `project_name` | Nombre del proyecto | Elicitación |
| `existing_docs` | Documentación existente | Análisis |
| `linting_tools` | Herramientas de linting | Análisis + Elicitación |
| `main_technologies` | Tecnologías principales | Análisis del código |
| `architecture_sharded_location` | Ubicación de documentos | Config |

## 🛠️ **Personalización**

### Modificar Template
Edita `template.md` para ajustar la estructura del documento final según las necesidades del equipo.

### Ajustar Pasos
Modifica `instructions.md` para agregar o quitar pasos específicos del análisis.

### Configurar Validación
Actualiza `checklist.md` para incluir criterios de validación específicos.

## 📊 **Métricas de Éxito**

- ✅ Documento de estándares generado y validado
- ✅ Patrones del código base identificados y documentados
- ✅ Configuración de herramientas especificada
- ✅ Ejemplos prácticos incluidos del proyecto real
- ✅ Proceso de mantenimiento definido

## 🔗 **Workflows Relacionados**

- **documentar-componente**: Para documentar componentes específicos
- **arquitectura-solucion**: Para definir arquitectura general

## 📞 **Soporte**

Este workflow es parte del **Método Ceiba - Arquitecto**. Para soporte o mejoras, contacta al equipo de arquitectura.

---

_Generado con BMAD v6 - Método Ceiba_  
_Versión: 1.0_