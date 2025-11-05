## Refinamiento Técnico (Developer)

<!-- ============================================================================ -->
<!-- SECCIÓN AGREGADA POR: Workflow refinamiento-tecnico                         -->
<!-- ETAPA: Refinamiento Técnico                                                  -->
<!-- RESPONSABLE: Developer                                                       -->
<!-- BASE: Análisis Arquitectónico (Arquitecto) - Ver sección arriba             -->
<!-- ============================================================================ -->

### Consideraciones Generales

**Basado en análisis arquitectónico:**
{{architectural_reference}}

**Nivel de complejidad:**
{{complexity_level}} - {{complexity_justification}}

**Riesgos técnicos conocidos:**
{{known_risks}}

**Patrones y convenciones del equipo:**
{{code_patterns}}

**Dependencias nuevas a instalar:**
{{new_dependencies}}

**Estrategia de testing:**
{{testing_frameworks}} | {{test_types_required}} | Cobertura: {{expected_coverage}}{{#if test_data_builders}} | Builders: {{test_data_builders}}{{/if}}

### Historias Relacionadas Consultadas

**Implementaciones similares analizadas:**
{{related_stories}}

**Patrones de código reutilizados:**
{{reused_patterns}}

**Mejores prácticas aplicadas:**
{{best_practices}}

---

## Tareas de Implementación (Developer)

<!-- GUÍA: Descomponer Hitos del Arquitecto en tareas técnicas con archivos específicos
     
     ESTRUCTURA:
     - Fase 0 (opcional): Infraestructura (migraciones, configs, breaking changes, setup performance)
     - Fases 1+: Implementación basada en Hitos del Arquitecto, agrupadas por componente desplegable
     
     COMPONENTES DESPLEGABLES (ejemplos del análisis arquitectónico):
     - Frontend (React/Angular/Vue app)
     - Order Service (microservicio)
     - Payment Service (microservicio)
     - Admin Dashboard (aplicación web)
     
     CADA SUBTAREA:
     - Acción + Archivo específico
     - Tests SOLO si tiene lógica ejecutable (servicios, endpoints, validadores, helpers con lógica)
     - NO tests para: migraciones, configs, DTOs/interfaces, modelos sin métodos
     - Vincular a AC cuando aplique (AC: X, Y)
     
     EJEMPLO:
     - [ ] Implementar `OrderService.create()` en `src/orders/order.service.ts`
     - [ ] Test unitario en `tests/unit/orders/order.service.spec.ts` (mockear repos)
     - [ ] Crear DTO `CreateOrderDTO` en `src/orders/dto/create-order.dto.ts` (sin test)
-->

{{#if has_infrastructure_tasks}}
### Fase 0: Infraestructura y Setup

<!-- Esta fase es OPCIONAL - Solo incluir si hay tareas de infraestructura -->

{{#if migrations_needed}}
#### Migraciones de Base de Datos
{{#each migrations}}
- [ ] **{{description}}**
  - [ ] Crear archivo de migración: `{{file_path}}`
  - [ ] Ejecutar migración en ambiente de desarrollo
  - [ ] Verificar rollback funciona correctamente
{{/each}}
{{/if}}

{{#if config_changes}}
#### Cambios de Configuración
{{#each configs}}
- [ ] **{{description}}**
  - [ ] Modificar `{{file_path}}`
  - [ ] Actualizar `.env.example` con nuevas variables
  - [ ] Documentar en README si aplica
{{/each}}
{{/if}}

{{#if breaking_changes}}
#### Breaking Changes (⚠️ Crítico)
{{#each breaking_changes}}
- [ ] **{{description}}**
  - [ ] Actualizar archivos: {{affected_files}}
  - [ ] Actualizar tests afectados
  - [ ] Documentar cambio en CHANGELOG
{{/each}}
{{/if}}

{{#if requires_performance_testing}}
#### Tests de Performance/Carga
- [ ] **Configurar tests de performance**
  - [ ] {{#if performance_base_files}}Reutilizar y adaptar: {{performance_base_files}}{{/if}}{{^if performance_base_files}}Crear nuevo archivo JMeter/K6 base{{/if}}
  - [ ] Definir escenarios: {{performance_scenarios}}
  - [ ] Establecer baseline de performance esperado
  - [ ] Configurar métricas a monitorear (throughput, latency, error rate)
{{/if}}

{{/if}}

{{#phases}}
### Fase {{phase_number}}: {{phase_name}}

<!-- Basado en Hito #{{phase_number}} del Análisis Arquitectónico -->
<!-- AGRUPAR TAREAS POR COMPONENTE DESPLEGABLE (ejemplos del análisis arquitectónico: Frontend, Order Service, Payment Service, Admin Dashboard) -->

{{#components}}
#### 📦 {{component_name}}

{{#tasks}}
- [ ] **{{task_name}}** {{#if ac_numbers}}(AC: {{ac_numbers}}){{/if}}
  {{#subtasks}}
  - [ ] {{description}}{{#if file}} - Archivo: `{{file}}`{{/if}}{{#if pattern}} (Patrón: {{pattern}}){{/if}}
  {{/subtasks}}
{{/tasks}}

{{/components}}

{{/phases}}

---

**Notas sobre vinculación con Criterios de Aceptación:**
- Cada tarea debe indicar qué criterio(s) de aceptación ayuda a completar usando (AC: X, Y, Z)
- Las tareas de infraestructura (Fase 0) pueden no tener vinculación directa a AC específicos
- Algunas tareas pueden contribuir parcialmente a múltiples criterios
- Si una subtarea no especifica archivo, es porque aplica a múltiples archivos o es una tarea conceptual

