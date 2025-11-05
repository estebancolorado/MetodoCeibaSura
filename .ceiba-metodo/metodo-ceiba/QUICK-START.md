# GUÍA DE INICIO RÁPIDO - MÉTODO CEIBA
## Para el Equipo de Desarrollo

### ✅ ESTRUCTURA CREADA

El módulo Método Ceiba está completamente estructurado y listo para que el equipo comience a crear agentes.

**Ubicación**: src/modules/metodo-ceiba/

```
metodo-ceiba/
├── agents/              ✅ Listo - Crear 15 agentes aquí
├── workflows/           ✅ Listo - Workflows futuros
├── tasks/              ✅ Listo - Tareas compartidas
├── teams/              ✅ Listo - Equipos de agentes
├── _module-installer/  ✅ Configurado
│   └── install-menu-config.yaml
├── config.yaml         ✅ Configurado
└── README.md          ✅ Documentación completa
```

---

## 🚀 CÓMO CREAR AGENTES

### Opción 1: Usando BMad Builder (Recomendado)

```bash
1. Cargar el agente BMad Builder
2. Ejecutar: *create-agent
3. Seguir el wizard interactivo
4. Guardar en: src/modules/metodo-ceiba/agents/
```

### Opción 2: Manual

```bash
1. Crear archivo .md en: src/modules/metodo-ceiba/agents/
2. Usar nomenclatura kebab-case (ej: arquitecto-software.md)
3. Seguir estructura BMAD v6
```

---

## 👥 15 AGENTES A CREAR

### 🏗️ Arquitectura (3)
- [ ] arquitecto-software.md
- [ ] analista-requisitos.md
- [ ] disenador-ux-ui.md

### 💻 Desarrollo (3)
- [ ] desarrollador-backend.md
- [ ] desarrollador-frontend.md
- [ ] revisor-codigo.md

### 🧪 QA/DevOps (3)
- [ ] qa-tester.md
- [ ] devops-engineer.md
- [ ] especialista-performance.md

### 🔒 Seguridad/Datos (2)
- [ ] especialista-seguridad.md
- [ ] analista-datos.md

### 📋 Gestión (4)
- [ ] scrum-master.md
- [ ] product-owner.md
- [ ] documentador-tecnico.md
- [ ] lider-tecnico.md

---

## 📋 PRIORIDAD DE CREACIÓN

**FASE 1 - CORE (Alta Prioridad)**
1. desarrollador-backend
2. desarrollador-frontend
3. qa-tester
4. arquitecto-software
5. devops-engineer

**FASE 2 - ESPECIALISTAS**
6-10. Resto de especialistas

**FASE 3 - GESTIÓN**
11-15. Agentes de gestión

---

## 🎯 CONVENCIONES

- **Nombres**: español, kebab-case
- **Extensión**: .md
- **Ubicación**: src/modules/metodo-ceiba/agents/
- **Estructura**: Seguir formato BMAD v6

---

## 📞 INFORMACIÓN DEL MÓDULO

- **Nombre**: Método Ceiba
- **Código**: metodo-ceiba
- **Propósito**: Aumentar desarrollo de software con IA
- **Empresa**: Ceiba Software
- **Versión**: 1.0.0
- **Fecha**: 2025-10-15

---

## ✅ SIGUIENTE PASO

**El equipo puede comenzar inmediatamente a crear agentes.**

Cada desarrollador:
1. Selecciona un agente de la lista
2. Lo crea usando *create-agent o manualmente
3. Lo guarda en src/modules/metodo-ceiba/agents/
4. Lo marca como completado

---

**¡El Método Ceiba está listo! 🌳🚀**
