# Checklist de Validación - Generar Estándares de Código

## ✅ **PASO 0: Configuración y Contexto Inicial**

### Paso 0.1: Cargar Configuración del Proyecto
- [ ] ✅ Verificada existencia de `{root}/config.yaml`
- [ ] ✅ Confirmada configuración de `architectureShardedLocation: {architectureShardedLocation}`
- [ ] ✅ Si no existe, usar `docs/architecture` como ubicación por defecto

### Paso 0.2: Elicitación Inicial de Contexto
- [ ] ✅ Preguntadas las 3 categorías obligatorias:
  - [ ] ✅ Documentación existente consultada
  - [ ] ✅ Estándares no documentados identificados
  - [ ] ✅ Tecnologías y herramientas relevadas
- [ ] ✅ Respuestas del usuario capturadas y documentadas

## ✅ **PASO 1: Análisis de Documentación Existente**

### Paso 1.1: Revisar Documentación de Estándares
- [ ] ✅ Buscados archivos `CONTRIBUTING.md`, `README.md`
- [ ] ✅ Revisadas configuraciones de linters (`.eslintrc`, `prettier.config.js`)
- [ ] ✅ Verificada existencia de `coding-standards.md` previo
- [ ] ✅ Analizada documentación de arquitectura disponible

### Paso 1.2: Extraer Información Existente
- [ ] ✅ Extraídas reglas ya documentadas
- [ ] ✅ Analizadas configuraciones de herramientas existentes
- [ ] ✅ Identificadas convenciones implícitas en documentación

## ✅ **PASO 2: Análisis del Código Base**

### Paso 2.1: Análisis de Patrones de Código
- [ ] ✅ Revisada estructura de directorios y organización
- [ ] ✅ Identificados patrones de nomenclatura (variables, funciones, clases, archivos)
- [ ] ✅ Analizados estilos de comentarios y documentación
- [ ] ✅ Examinados patrones arquitectónicos (MVC, componentes, servicios)

### Paso 2.2: Análisis de Tecnologías y Herramientas
- [ ] ✅ Detectados lenguajes principales y versiones
- [ ] ✅ Identificados frameworks y librerías principales
- [ ] ✅ Analizadas herramientas de build y testing
- [ ] ✅ Revisadas configuraciones de CI/CD disponibles

### Paso 2.3: Identificación de Convenciones
- [ ] ✅ Extraídos patrones de nomenclatura para:
  - [ ] ✅ Variables y funciones
  - [ ] ✅ Clases y componentes
  - [ ] ✅ Archivos y directorios
- [ ] ✅ Identificadas convenciones de imports y organización
- [ ] ✅ Analizados patrones de testing y documentación

## ✅ **PASO 3: Consolidación de Estándares**

### Paso 3.1: Combinar Fuentes de Información
- [ ] ✅ Consolidada información de documentación existente
- [ ] ✅ Integradas respuestas del usuario
- [ ] ✅ Combinados patrones del código base
- [ ] ✅ Incluidas mejores prácticas para tecnologías detectadas

### Paso 3.2: Categorizar Estándares
- [ ] ✅ Definidos estándares obligatorios (reglas críticas)
- [ ] ✅ Establecidas convenciones recomendadas
- [ ] ✅ Especificada configuración de herramientas
- [ ] ✅ Definida organización de archivos y directorios

## ✅ **PASO 4: Creación del Documento de Estándares**

### Paso 4.1: Crear Estructura de Archivo
- [ ] ✅ Creado directorio en `{architectureShardedLocation}`
- [ ] ✅ Verificada estructura de archivos correcta

### Paso 4.2: Generar `coding-standards.md` - Estándares de Código
- [ ] ✅ **1. Cargar Plantilla Base**: Leído contenido del archivo {installed_path}/template.md como plantilla base
- [ ] ✅ **2. Reemplazar información**: Sustituida información correspondiente en variables de diferentes secciones de la plantilla
- [ ] ✅ **3. Generar Archivo Final**: Creado archivo final en la ruta {default_output_file}
- [ ] ✅ **IMPORTANTE - Verificaciones**:
  - [ ] ✅ NO modificado el archivo template.md (solo lectura como plantilla)
  - [ ] ✅ Archivo de salida completamente nuevo, generado a partir del template
  - [ ] ✅ Todas las secciones del template completas con información relevante
  - [ ] ✅ Secciones N/A apropiadamente documentadas con explicación

### Paso 4.3: Validación y Refinamiento
- [ ] ✅ **Revisión del documento generado**:
  - [ ] ✅ Verificado que estándares reflejen realmente el código existente
  - [ ] ✅ Asegurado que reglas sean prácticas y aplicables
  - [ ] ✅ Validadas configuraciones de herramientas correctas
  - [ ] ✅ Confirmados ejemplos de código precisos
- [ ] ✅ **Preguntas al usuario**:
  - [ ] ✅ ¿Los estándares capturan correctamente las convenciones del proyecto?
  - [ ] ✅ ¿Hay alguna regla específica que quieras agregar o modificar?
  - [ ] ✅ ¿La configuración de herramientas refleja lo que actualmente usan?
- [ ] ✅ **Refinamientos**: Aplicados refinamientos solicitados (si los hay)

## ✅ **PASO 5: Entrega y Comunicación**

- [ ] ✅ **Mensaje final obligatorio generado** con:
  - [ ] ✅ **Archivo creado**: `{architectureShardedLocation}/coding-standards.md`
  - [ ] ✅ **Análisis completado**: Documentación existente, código base, tecnologías, reglas y convenciones
  - [ ] ✅ **Contenido generado**: Stack tecnológico, estándares obligatorios, convenciones, configuración, métricas
  - [ ] ✅ **Próximos pasos**: Revisión con equipo, ajustar herramientas, integrar CI/CD, incluir en onboarding
- [ ] ✅ **Pregunta final**: ¿El documento refleja correctamente los estándares del proyecto?

## 🎯 **Criterios de Calidad Verificados**

- [ ] ✅ **Basado en realidad**: Estándares extraídos del código existente
- [ ] ✅ **Práctico**: Reglas aplicables y verificables
- [ ] ✅ **Completo**: Cubre tecnologías principales del proyecto
- [ ] ✅ **Específico**: Ejemplos concretos de código correcto e incorrecto
- [ ] ✅ **Herramientas**: Configuración de linters y formatters incluida
- [ ] ✅ **Evolutivo**: Proceso de actualización definido

## 🚨 **Reglas de Comportamiento Cumplidas**

- [ ] ✅ **ANÁLISIS PRIMERO**: Código existente analizado antes de crear estándares teóricos
- [ ] ✅ **ELICITACIÓN COMPLETA**: Toda la información del usuario obtenida
- [ ] ✅ **BASADO EN EVIDENCIA**: Estándares reflejan patrones reales del código
- [ ] ✅ **EJEMPLOS PRÁCTICOS**: Código de ejemplo extraído del proyecto
- [ ] ✅ **HERRAMIENTAS REALES**: Configuraciones basadas en uso real del proyecto
- [ ] ✅ **VALIDACIÓN FINAL**: Confirmación del usuario sobre corrección de estándares

---

**DOCUMENTO VÁLIDO CUANDO TODOS LOS ELEMENTOS ESTÁN MARCADOS ✅**

_Checklist generado con Método Ceiba - Arquitecto_  
_Versión: 1.0_