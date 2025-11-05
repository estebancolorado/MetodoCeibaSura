# Workflow: Explorar Proyecto

## 📋 **Descripción**

Ayuda al usuario a explorar y entender cualquier aspecto del proyecto mediante búsqueda inteligente en documentación, código y configuración. Proporciona respuestas contextualizadas y completas basadas en la información disponible del proyecto, facilitando la comprensión de arquitectura, funcionalidades, procesos y estructura del sistema.

## 🎯 **Cuándo Usar Este Workflow**

**Úsalo cuando necesites:**

- ✅ Entender un aspecto específico del proyecto
- ✅ Buscar información sobre arquitectura, componentes o funcionalidades  
- ✅ Analizar dependencias entre módulos
- ✅ Mapear flujos de negocio o procesos técnicos
- ✅ Encontrar documentación relacionada con un tema
- ✅ Localizar código específico relacionado con una funcionalidad

**No lo uses cuando:**

- ❌ Necesites crear nueva documentación (usa `documentar-componente`)
- ❌ Requieras análisis de código específico (usa herramientas de análisis de código)
- ❌ Busques información de sistemas externos al proyecto

## 🚀 **Cómo Ejecutar**

### Desde BMad Builder

```bash
# Activar BMad Builder
*help

# Seleccionar workflow
*explore-project
```

### Información Requerida

El workflow te pedirá:

1. **🎯 Tema de Exploración**: 
   - ¿Qué quieres explorar del proyecto?
   - Ejemplos: "sistema de autenticación", "arquitectura del módulo X", "proceso de pagos"

2. **📋 Contexto Adicional** (si es necesario):
   - Aspecto específico de interés
   - Propósito de la exploración
   - Área o módulo particular

## 📁 **Estructura de Archivos**

```
explorar-proyecto/
├── workflow.yaml        # Configuración del workflow
├── instructions.md      # Instrucciones paso a paso
├── checklist.md        # Lista de verificación de calidad
├── README.md           # Documentación del workflow
└── temp/              # Archivos temporales
```

## 🔧 **Prerrequisitos**

### Configuración Requerida

- ✅ **Configuración del proyecto**: `{project-root}/.ceiba-metodo/metodo-ceiba/config.yaml`
- ✅ **Documentación del proyecto**: Estructura de docs disponible
- ✅ **Acceso a archivos**: Permisos de lectura en la estructura del proyecto

### Variables de Configuración

```yaml
# En metodo-ceiba/config.yaml
architecture_sharded_location: "ruta/a/docs/arquitectura"
dev_story_location: "ruta/a/historias"
qa_location: "ruta/a/qa"
```

## 📋 **Proceso del Workflow**

### Paso 0: Captura de Intención de Exploración
- **0.1**: Solicitar tema de exploración al usuario
- **0.2**: Clasificar tipo de consulta automáticamente
- **0.3**: Refinar pregunta si es necesario

### Paso 1: Carga de Configuración y Contexto
- **1.1**: Cargar configuración del proyecto obligatoriamente

### Paso 2: Búsqueda Inteligente
- **2.1**: Búsqueda para consultas de Arquitectura (🏗️)
- **2.2**: Búsqueda para consultas Funcionales (📋)
- **2.3**: Búsqueda para consultas Técnicas (💻)
- **2.4**: Búsqueda para consultas de Documentación (📖)
- **2.5**: Búsqueda General (🔍)

### Paso 3: Análisis de Resultados
- **3.1**: Búsqueda primaria en fuentes identificadas
- **3.2**: Análisis de dependencias y conexiones
- **3.3**: Detección de gaps de información

### Paso 4: Respuesta Estructurada
- **4.1**: Generar respuesta principal estructurada
- **4.2**: Detectar gaps y proporcionar sugerencias
- **4.3**: Ofrecer opciones de seguimiento interactivo

## � **Salidas del Workflow**

### Respuesta Interactiva

El workflow proporciona **respuestas directas e interactivas** al usuario, **no genera archivos de salida**.

**Contenido de la respuesta**:
- 🎯 Resumen ejecutivo de hallazgos
- 📁 Fuentes consultadas y información encontrada
- 🔗 Conexiones y dependencias identificadas
- ⚠️ Gaps de información detectados
- 💡 Sugerencias y próximos pasos
- 🤔 Opciones de seguimiento

### Información Estructurada

- **Respuesta directa** a la consulta original
- **Referencias específicas** con ubicaciones exactas
- **Contexto de relaciones** entre componentes
- **Identificación de brechas** de documentación
- **Sugerencias accionables** para próximos pasos

## 🎯 **Tipos de Consultas Soportadas**

### 🏗️ Arquitectura y Componentes
- Estructura del sistema
- Componentes y módulos
- Dependencias entre módulos
- Patrones arquitectónicos

### 📋 Funcionalidades y Procesos  
- Flujos de negocio
- Procesos de usuario
- Reglas de negocio
- Historias de usuario

### 💻 Código y Implementación
- Implementación de funcionalidades
- Archivos relacionados
- Integraciones técnicas
- APIs y servicios

### 📖 Documentación y Configuración
- Documentación disponible
- Configuración del proyecto
- Guías y manuales
- Estándares y convenciones

## ✅ **Criterios de Éxito**

- **✅ Pregunta Respondida**: La consulta original está completamente respondida
- **✅ Fuentes Identificadas**: Se encontraron y revisaron las fuentes relevantes
- **✅ Información Completa**: Se extrajo toda la información disponible relacionada
- **✅ Referencias Precisas**: Se proporcionaron ubicaciones específicas
- **✅ Contexto Proporcionado**: Se explicaron las relaciones y dependencias
- **✅ Gaps Identificados**: Se detectó información faltante si la hay
- **✅ Seguimiento Ofrecido**: Se facilitaron opciones para exploración adicional
- **✅ Respuesta Estructurada**: La información se presentó de manera clara y organizada

## 🚨 **Problemas Comunes y Soluciones**

### Configuración No Encontrada
```
❌ Error: "Configuración no encontrada"
✅ Solución: Ejecutar instalación de BMad y verificar config.yaml
```

### Fuentes No Accesibles
```
❌ Error: No se pueden leer archivos del proyecto
✅ Solución: Verificar permisos y rutas en la configuración
```

### Pregunta Muy Vaga
```
❌ Error: Resultados poco específicos
✅ Solución: El workflow refinará automáticamente la pregunta
```

### Información Limitada
```
❌ Error: Pocas fuentes encontradas
✅ Solución: El workflow identificará gaps y sugerirá áreas de mejora
```

## 🔄 **Workflows Relacionados**

- **`documentar-componente`**: Para crear documentación de componentes específicos
- **`arquitectura-general`**: Para análisis arquitectónico completo del sistema
- **`analisis-dependencias`**: Para análisis profundo de dependencias

## 📝 **Notas de Mantenimiento**

- **Última actualización**: {{date}}
- **Versión**: 1.0.0
- **Autor**: Método Ceiba - Arquitecto
- **Compatible con**: BMAD v6.0+

---

_Este workflow es parte del Método Ceiba y sigue los estándares BMAD v6._