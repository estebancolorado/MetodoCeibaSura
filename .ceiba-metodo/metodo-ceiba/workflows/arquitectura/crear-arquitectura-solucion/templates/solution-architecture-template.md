# Documento de Arquitectura para {{project_name}}

**Fecha:** {{date}}  
**Versión:** 1.0  
**Autor:** {{user_name}}

---

## Introducción y Objetivos

Este documento describe la arquitectura general del proyecto para {{project_name}}, incluyendo sistemas de backend, servicios compartidos y aspectos no específicos de la interfaz de usuario (UI). Su objetivo principal es servir como el plano arquitectónico guía para el desarrollo impulsado por IA, asegurando la consistencia y el cumplimiento de los patrones y tecnologías elegidos.

### Resumen de Requisitos

{{resumen_requisitos}}

### Objetivos de Calidad

{{objetivos_calidad}}

### Interesados

| Nombre | Expectativas |
| ------ | ------------ |

{{tabla_interesados}}

---

## Restricciones

{{restricciones}}

---

## Contexto y Alcance

### Contexto de Negocio

```mermaid
{{diagrama_contexto_negocio}}
```

#### Tabla de Contexto de Negocio

| Elemento | Entradas | Salidas |
| -------- | -------- | ------- |

{{tabla_contexto_negocio}}

### Contexto Técnico

```mermaid
{{diagrama_contexto_tecnico}}
```

#### Lista de Servicios/Componentes

{{lista_contexto_tecnico}}

---

## Estrategia de Solución

{{estrategia_solucion}}

---

## Vista Estática (Componentes)

```mermaid
{{diagrama_vista_estatica}}
```

### Tabla de Componentes

| Contenedor | Propósito/Responsabilidad | Interfaces |
| ---------- | ------------------------- | ---------- |

{{tabla_vista_estatica}}

---

## Vista Dinámica (Ejecución)

{{flujos_dinamicos}}

---

## Notas de Implementación

- Este documento fue generado usando el workflow de Arquitectura Solución del Método Ceiba
- Para actualizaciones, ejecutar el workflow nuevamente con los datos actualizados
- Diagramas generados usando Mermaid para compatibilidad con herramientas de documentación

---

**Generado por:** Método Ceiba - Arquitectura Solución Workflow  
**Fecha de generación:** {{date}}
