# Historia #{{story_number}}: {{story_title}}

<!-- Estado fijo al crear: Borrador (PO) -->
**Estado:** Borrador (PO)

---

## Historia de Usuario

<!-- OBLIGATORIO: Historia de Usuario en formato Como/Quiero/Para
     GUÍA: Ser específico en el rol - evitar "usuario del sistema" genérico
     Ejemplo: "usuario del sistema de asignaciones que puede ver Mis Asignaciones"
-->

**Como** {{role}},  
**Quiero** {{action}},  
**Para** {{benefit}}

## Descripción

<!-- OBLIGATORIO: Descripción - Contexto general de la funcionalidad
     GUÍA: 2-4 líneas explicando QUÉ se necesita y contexto básico
     Incluir: módulo/área del sistema(si se tiene), comportamiento esperado general
-->

{{description}}

---

## Criterios de Aceptación

<!-- OBLIGATORIO: Criterios de Aceptación en formato Dado/Cuando/Entonces
     GUÍA: Mínimo 2-3 escenarios, cubrir flujo principal, validaciones y casos extremos
     Incluir: casos sin resultados, casos límite con fechas/datos, permisos
-->

### Escenario 1: Flujo Principal

- **Dado** que {{given_1}}
- **Cuando** {{when_1}}
- **Entonces** {{then_1}}

### Escenario 2: Validaciones

- **Dado** que {{given_2}}
- **Cuando** {{when_2}}
- **Entonces** {{then_2}}

### Escenario 3: Casos Extremos

- **Dado** que {{given_3}}
- **Cuando** {{when_3}}
- **Entonces** {{then_3}}

{{additional_scenarios}}

---

## Información Recopilada

<!-- OBLIGATORIO: Información Recopilada - Resultado de elicitación con stakeholders
     GUÍA: Documentar todo lo recopilado en entrevistas/reuniones organizadas por categoría
     Subsecciones: Usuario y Contexto, Datos y Validaciones, Interfaz y Navegación, Integraciones
-->

### Usuario y Contexto

<!-- Subsección: Usuario y Contexto
     GUÍA: Tipo de usuario específico, permisos necesarios, valor de negocio que aporta
-->

- **Tipo de usuario:** {{user_type}}
- **Permisos requeridos:** {{permissions}}
- **Valor de negocio:** {{business_value}}

### Reglas de Negocio (Si aplica)

<!-- Políticas y restricciones del dominio -->

{{business_rules}}

### Interfaz (Si aplica)

<!-- Pantalla, elementos UI y flujo de navegación -->

{{interface_navigation}}

### Sistemas Externos (Si aplica)

<!-- Solo si stakeholder mencionó integración con sistemas EXTERNOS (email, APIs externas, ERP, servicios de terceros)
     NO documentar decisiones técnicas: endpoints, componentes, si requiere cambios en backend/frontend
     Eso es trabajo del ARQUITECTO -->

{{external_systems_mentioned}}

---

## Contexto y Referencias

<!-- Documentos y HU relacionadas para contexto -->

**Arquitectura (Si aplica):** {{architecture_doc_reference}}

**Historias relacionadas (Si aplica):** {{related_stories_ids}}

**Lecciones aprendidas (Si aplica):** 
<!-- SOLO información de NEGOCIO - NUNCA decisiones técnicas (componentes, endpoints, librerías, arquitectura)
     Ejemplos CORRECTOS: "Validar permisos de período cerrado", "Casos sin resultados requieren mensaje claro"
     Ejemplos INCORRECTOS: "Usa mat-datepicker", "Endpoint /api/x existe", "Filtrado en cliente" -->
{{lessons_learned_simple}}

---

<!-- ============================================================================ -->
<!-- PUNTO DE INSERCIÓN: Análisis Arquitectónico (Arquitecto)                    -->
<!-- ============================================================================ -->
<!-- Agregado por: Workflow analizar-disenar-historia-usuario                    -->
<!-- Template: seccion-analisis-arquitectonico.template.md                       -->
<!-- Descripción: El Arquitecto analiza la HU y agrega decisiones de diseño,     -->
<!--              patrones arquitectónicos, componentes afectados y referencias   -->
<!-- Estado después de inserción: "Analizado (Arquitecto)"                       -->
<!-- ============================================================================ -->

## Definición de Terminado (Inicial)

<!-- Criterios funcionales desde perspectiva PO -->

- [ ] Funcionalidad implementada según criterios de aceptación
- [ ] Validaciones funcionando correctamente
- [ ] Mensajes implementados

---

## Registro de Cambios

<!-- SECCIÓN: Registro de Cambios
     GUÍA: Auto-generado por el sistema, mantener tabla simple
-->

| Fecha | Versión | Descripción | Autor |
|-------|---------|-------------|-------|
| {{date}} | 1.0 | Historia creada por PO | {{user_name}} (PO) |

---

## Metadata

<!-- Información administrativa -->

**Autor:** {{user_name}} (PO)  
**Fecha:** {{date}}  
**Historias relacionadas:** {{related_stories_ids}}

---

## Métricas de redacción de la historia de usuario con el PO 

<!-- Tiempo de REDACCIÓN de la HU con el PO - NO incluye desarrollo/implementación -->

**Tiempo con Método Ceiba:** {{tiempo_metodo_ceiba}} minutos  
**Tiempo estimado tradicional:** {{tiempo_tradicional}} minutos  
**Optimización:** {{porcentaje_optimizacion}}%

---


