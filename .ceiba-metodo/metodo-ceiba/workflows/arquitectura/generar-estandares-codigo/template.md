<!-- Formato: Usar el nombre real del proyecto -->
# {Nombre del Proyecto} - Estándares de Código 📝

## 📋 **Información General**

### Propósito del Documento

<!-- Formato: Usar el nombre real del proyecto -->
Este documento define los estándares de código obligatorios y recomendados para el desarrollo en {Nombre del Proyecto}. Estos estándares garantizan consistencia, legibilidad y mantenibilidad del código.

**Audiencia**: Desarrolladores, Code Reviewers, DevOps  
**Última Actualización**: {fecha actual}  
**Estado**: {Activo/En Revisión}

---

## 🚨 **Estándares Obligatorios**

### 1. Nomenclatura

#### Variables y Funciones
<!-- Formato: Ejemplos específicos extraídos del análisis del código base -->

```{lenguaje principal}
// ✅ CORRECTO
const userAccountBalance = 1500;
function calculateTotalPrice(items) { ... }

// ❌ INCORRECTO
const bal = 1500;
function calc(items) { ... }
```

{{patrones_codigo}}
{{convenciones_codigo}}

#### Clases y Componentes

```{lenguaje principal}
// ✅ CORRECTO
class PaymentProcessor { ... }
const UserProfileComponent = () => { ... }

// ❌ INCORRECTO
class payment { ... }
const userprofile = () => { ... }
```

{{convenciones_codigo}}
{{patrones_codigo}}

#### Archivos y Directorios
<!-- Formato: Estructura real identificada en el proyecto -->

```bash
# ✅ CORRECTO
src/
├── components/
│   ├── UserProfile.{extensión}
│   └── PaymentForm.{extensión}
├── services/
│   └── payment-service.{extensión}

# ❌ INCORRECTO
src/
├── comp/
│   ├── userprofile.{extensión}
│   └── paymentform.{extensión}
```

{{convenciones_codigo}}

### 2. Estructura de Código

#### Organización de Imports
<!-- Formato: Patrones de imports específicos del stack tecnológico -->

```{lenguaje principal}
// ✅ CORRECTO - Orden de imports
// 1. Librerías externas
import React from 'react';
import axios from 'axios';

// 2. Imports internos (servicios, utils)
import { PaymentService } from '../services/payment-service';
import { formatCurrency } from '../utils/formatters';

// 3. Imports de componentes
import UserProfile from './UserProfile';
```

{{tecnologias_herramientas}}


#### Estructura de Funciones
<!-- Formato: Ejemplos de funciones bien estructuradas del código existente -->

```{lenguaje principal}
// ✅ CORRECTO
/**
 * Calcula el precio total incluyendo impuestos
 * @param {Object[]} items - Lista de items del carrito
 * @param {number} taxRate - Tasa de impuesto (0-1)
 * @returns {number} Precio total con impuestos
 */
function calculateTotalWithTax(items, taxRate) {
  if (!items || !Array.isArray(items)) {
    throw new Error('Items debe ser un array válido');
  }

  const subtotal = items.reduce((sum, item) => sum + item.price, 0);
  return subtotal * (1 + taxRate);
}
```

{{convenciones_codigo}}
{{patrones_codigo}}

### 3. Manejo de Errores

#### Manejo de Errores Obligatorio
<!-- Formato: Patrones de manejo de errores identificados -->

```{lenguaje principal}
// ✅ CORRECTO
try {
  const result = await apiCall();
  return result;
} catch (error) {
  logger.error('Error en apiCall:', error);
  throw new ApiError('Falló la llamada a la API', error);
}

// ❌ INCORRECTO
const result = await apiCall(); // Sin manejo de errores
```

{{convenciones_codigo}}
{{patrones_codigo}}


### 4. Comentarios y Documentación

#### Comentarios Obligatorios
<!-- Formato: Estilos de documentación encontrados en el código -->

```{lenguaje principal}
// ✅ CORRECTO - Funciones públicas documentadas
/**
 * Función que procesa pagos de usuarios
 * @param {Object} paymentData - Datos del pago
 * @param {string} paymentData.amount - Monto en centavos
 * @param {string} paymentData.currency - Código de moneda ISO
 * @returns {Promise<Object>} Resultado del procesamiento
 */

// ✅ CORRECTO - Lógica compleja explicada
// Aplicamos descuento escalonado: 5% > $100, 10% > $500, 15% > $1000
if (amount > 100000) { // $1000 en centavos
  discount = 0.15;
}
```

{{convenciones_codigo}}
{{patrones_codigo}}

---

## 💡 **Convenciones Recomendadas**

### 1. Organización de Archivos
<!-- Formato: Estructura de directorios recomendada basada en el análisis -->

```
src/
├── components/           # Componentes reutilizables
│   ├── common/          # Componentes compartidos
│   └── pages/           # Componentes específicos de página
├── services/            # Lógica de negocio y APIs
├── utils/               # Funciones utilitarias
├── hooks/               # Custom hooks (si aplica)
├── types/               # Definiciones de tipos
└── __tests__/           # Tests organizados por módulo
```

{{patrones_codigo}}
{{convenciones_codigo}}


### 2. Patrones de Código

#### Destructuring y Spread
<!-- Formato: Patrones recomendados identificados del código base -->

```{lenguaje principal}
// ✅ RECOMENDADO
const { name, email, address } = user;
const newUser = { ...user, lastLogin: new Date() };

// 👌 ACEPTABLE pero menos preferido
const name = user.name;
const email = user.email;
```

{{categorizacion_estandares}}
{{convenciones_codigo}}
{{patrones_codigo}}

#### Funciones Puras cuando sea Posible

```{lenguaje principal}
// ✅ RECOMENDADO - Función pura
function formatPrice(amount, currency) {
  return new Intl.NumberFormat('es-ES', {
    style: 'currency',
    currency: currency
  }).format(amount);
}
```

---

## 🔧 **Configuración de Herramientas**

### ESLint/Linter Configuration
<!-- Formato: Configuración real de linters encontrada o recomendada -->

```json
{
  "extends": ["{configuración base identificada}"],
  "rules": {
    "{regla 1}": "{valor}",
    "{regla 2}": "{valor}"
  }
}
```

{{tecnologias_herramientas}}
{{categorizacion_estandares}}
{{informacion_existente}}


### Prettier/Formatter Configuration
<!-- Formato: Configuración de formatters identificada -->

```json
{
  "printWidth": {valor identificado},
  "tabWidth": {valor identificado},
  "singleQuote": {true/false},
  "trailingComma": "{configuración}"
}
```

{{informacion_existente}}


### Scripts Recomendados
<!-- Formato: Scripts npm/package.json encontrados -->

```json
{
  "scripts": {
    "lint": "{comando de linting}",
    "lint:fix": "{comando de auto-fix}",
    "format": "{comando de formateo}",
    "test": "{comando de testing}"
  }
}
```

{{tecnologias_herramientas}}

---

## 🧪 **Estándares de Testing**

### Nomenclatura de Tests
<!-- Formato: Patrones de testing identificados en el proyecto -->

```{lenguaje de testing}
// ✅ CORRECTO
describe('PaymentProcessor', () => {
  describe('processPayment', () => {
    test('should process valid payment successfully', () => {
      // Test implementation
    });

    test('should throw error when amount is negative', () => {
      // Test implementation
    });
  });
});
```

{{convenciones_codigo}}
{{tecnologias_herramientas}}

### Estructura de Tests
<!-- Formato: Estructura de tests encontrada en el proyecto -->

```{lenguaje de testing}
// ✅ PATRÓN AAA (Arrange, Act, Assert)
test('should calculate discount correctly', () => {
  // Arrange
  const amount = 1000;
  const discountRate = 0.1;

  // Act
  const result = calculateDiscount(amount, discountRate);

  // Assert
  expect(result).toBe(100);
});
```

{{patrones_codigo}}
{{tecnologias_herramientas}}

---

## 📊 **Métricas y Calidad**

### Umbrales de Calidad
<!-- Formato: Tabla con métricas específicas del proyecto -->

| Métrica                     | Umbral Mínimo   | Herramienta   |
| --------------------------- | --------------- | ------------- |
| **Cobertura de Tests**      | {umbral}%       | {herramienta} |
| **Complejidad Ciclomática** | < {valor}       | {herramienta} |
| **Duplicación de Código**   | < {porcentaje}% | {herramienta} |
| **Deuda Técnica**           | < {tiempo}      | {herramienta} |

{{consolidacion_fuentes}}

### Code Review Checklist
<!-- Formato: Checklist basado en los estándares definidos -->

- [ ] ✅ Nomenclatura sigue convenciones
- [ ] ✅ Manejo de errores implementado
- [ ] ✅ Tests cubren casos principales
- [ ] ✅ Sin código duplicado
- [ ] ✅ Performance considerada
- [ ] ✅ Seguridad validada

{{categorizacion_estandares}}

---

## 🚀 **Mejores Prácticas Específicas**
<!-- Formato: Secciones específicas por cada tecnología principal identificada -->

### {Tecnología Específica 1}

{Mejores prácticas específicas identificadas del código}

### {Tecnología Específica 2}

{Mejores prácticas específicas identificadas del código}

{{tecnologias_herramientas}}
{{consolidacion_fuentes}}


### Performance
<!-- Formato: Prácticas de performance identificadas del código -->

{Prácticas de performance identificadas}

{{tecnologias_herramientas}}
{{consolidacion_fuentes}}


### Seguridad
<!-- Formato: Prácticas de seguridad identificadas -->

{Prácticas de seguridad identificadas}

{{tecnologias_herramientas}}
{{consolidacion_fuentes}}  

---

## 🔄 **Proceso de Actualización**

### Cuándo Actualizar estos Estándares
<!-- Formato: Criterios específicos para el proyecto -->

1. **Cambios de tecnología** - Nuevas versiones principales de frameworks
2. **Lecciones aprendidas** - Problemas recurrentes identificados
3. **Revisión trimestral** - Evaluación periódica de efectividad
4. **Onboarding feedback** - Comentarios de nuevos desarrolladores

{{validacion_refinamiento}}


### Proponer Cambios
<!-- Formato: Proceso de cambios adaptado al equipo -->

1. Crear issue/ticket describiendo la propuesta
2. Discutir en review de equipo
3. Implementar en branch de prueba
4. Actualizar este documento
5. Comunicar cambios al equipo

{{validacion_refinamiento}}

---

## 📚 **Referencias y Recursos**

### Documentación Oficial
<!-- Formato: Enlaces a documentación específica del stack tecnológico -->

- [{Tecnología 1} Style Guide]({enlace})
- [{Tecnología 2} Best Practices]({enlace})

{{documentacion_existente}}

### Herramientas Útiles
<!-- Formato: Herramientas realmente configuradas en el proyecto -->

- **Linting**: {herramientas configuradas}
- **Formatting**: {herramientas configuradas}
- **Testing**: {herramientas configuradas}

{{informacion_existente}}
{{tecnologias_herramientas}}

### Recursos de Aprendizaje
<!-- Formato: Recursos relevantes para las tecnologías del proyecto -->

- [{Recurso 1}]({enlace})
- [{Recurso 2}]({enlace})

{{contexto_inicial}}

---

**NOTA IMPORTANTE**: Estos estándares fueron generados analizando el código base existente y las prácticas del equipo. Deben evolucionar con el proyecto y ser revisados periódicamente.

---

_Documento generado con Método Ceiba - Arquitecto_  
_Última actualización: {fecha}_  
_Versión: 1.0_
