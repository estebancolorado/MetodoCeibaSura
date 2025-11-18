# WORKFLOW: Gestionar Incidentes

## Critical Headers

**Workflow Name**: Gestionar Incidentes  
**Agent Role**: Product Owner (o cualquier rol)  
**Variables Required**: Ninguna (todos los filtros son opcionales)  
**Templates Used**: `dashboard-template.md`

---

## PURPOSE

Proporcionar un dashboard interactivo para listar, filtrar y buscar incidentes documentados en el sistema. Facilita visualización rápida de incidentes abiertos, estadísticas y compliance con SLA.

---

## INSTRUCTIONS

<workflow>
  <step id="1" name="Listar Todos los Incidentes">
    <action type="read">
      Lee todos los archivos `.incident.md` en el directorio `{{incidents_location}}` para construir inventario completo de incidentes.
      
      Para cada incidente, extrae del METADATA:
      - ID
      - Reported Date
      - Status
      - Severity
      - System Impact
      - Users Affected
      - Fecha de Cierre (si está cerrado)
      - Tiempo de Resolución (si está cerrado)
      
      Y de las secciones:
      - Título (de sección 1.1)
      - Tipo de Error (de sección 2.3)
    </action>
  </step>

  <step id="2" name="Aplicar Filtros">
    <action type="filter">
      Filtra la lista de incidentes según los parámetros:
      
      **Filtro de Severidad**:
      - Si `{{filter_severity}} == ALL` → incluir todos
      - Si `{{filter_severity}} == P0|P1|P2|P3|P4` → incluir solo esa severidad
      
      **Filtro de Estado**:
      - Si `{{filter_status}} == ALL` → incluir todos
      - Si `{{filter_status}} == Open|Triaged|In Progress|Resolved|Cerrado` → incluir solo ese estado
      
      **Filtro de Tipo de Error**:
      - Si `{{filter_error_type}} == ALL` → incluir todos
      - Si `{{filter_error_type}} == Código|Datos|etc.` → incluir solo ese tipo
      
      **Búsqueda por Keyword** (si `{{search_keyword}}` está definido):
      - Buscar keyword en título del incidente
      - Buscar keyword en descripción del problema (sección 1.1)
      - Incluir incidente si keyword aparece en cualquiera de los dos
    </action>
    <template-output name="incidents_list">
      Lista filtrada de incidentes en formato tabla Markdown.
    </template-output>
  </step>

  <step id="3" name="Calcular Estadísticas">
    <action type="analyze">
      Calcula estadísticas sobre TODOS los incidentes (sin filtros):
      
      **Total de Incidentes**:
      - Total general
      - Por severidad (P0, P1, P2, P3, P4)
      - Por estado (Open, Triaged, In Progress, Resolved, Cerrado)
      - Por tipo de error (Código, Datos, Infraestructura, etc.)
      
      **Tiempo Promedio de Resolución**:
      - Por severidad (solo incidentes cerrados)
      - Comparar con SLA:
        - P0: SLA < 4 horas
        - P1: SLA < 24 horas
        - P2: SLA < 1 semana
        - P3: SLA < 1 sprint
    </action>
    <template-output name="incidents_stats">
      Estadísticas en formato tabla Markdown.
    </template-output>
  </step>

  <step id="4" name="Evaluar SLA Compliance">
    <action type="evaluate">
      Para cada severidad, calcula:
      - Total de incidentes cerrados
      - Cuántos cumplieron SLA
      - Cuántos NO cumplieron SLA
      - % de compliance
      
      **Criterios de SLA**:
      - P0: Resolución en < 4 horas
      - P1: Resolución en < 24 horas
      - P2: Resolución en < 1 semana (168 horas)
      - P3: Resolución en < 2 semanas (336 horas)
      - P4: No tiene SLA estricto
    </action>
    <template-output name="sla_compliance">
      Tabla de compliance con indicadores de color (✅ si >= 80%, ⚠️ si 60-79%, ❌ si < 60%).
    </template-output>
  </step>

  <step id="5" name="Generar Dashboard">
    <action type="write">
      Usa el template `dashboard-template.md` para generar el dashboard completo con:
      
      **Sección 1: Filtros Aplicados**
      - Mostrar filtros activos
      - Indicar cuántos incidentes resultaron de los filtros
      
      **Sección 2: Incidentes (Filtrados)**
      - Tabla con incidentes filtrados en `{{incidents_list}}`
      - Ordenar por Severity (P0 primero) y luego por Reported Date (más reciente primero)
      
      **Sección 3: Estadísticas Generales**
      - Usar `{{incidents_stats}}`
      
      **Sección 4: SLA Compliance**
      - Usar `{{sla_compliance}}`
      
      **Sección 5: Acciones Rápidas**
      - Links a crear nuevo incidente (*recibir-error)
      - Links a diagnosticar incidentes en estado "Triaged"
      - Links a documentar post-mortem de incidentes "Resolved"
    </action>
  </step>

  <step id="6" name="Mostrar Dashboard">
    <action type="display">
      Presenta el dashboard generado al usuario con formato claro y navegable.
      
      Indica al usuario que puede:
      - Cambiar filtros para ver diferentes vistas
      - Hacer clic en ID de incidente para abrir archivo completo
      - Ejecutar acciones rápidas desde el dashboard
    </action>
  </step>
</workflow>

---

## VALIDATION

No requiere checklist formal. El dashboard es informativo y no modifica estado del sistema.

---

## STYLE GUIDE

**Instruction Style**: Prescriptive

Este workflow debe ser determinístico y producir output consistente. Los pasos son claros y no requieren interpretación del agente.

---

## NOTES

- Este workflow NO modifica archivos de incidentes, solo lee y presenta información
- El dashboard es regenerado cada vez que se invoca el workflow (no se guarda en archivo)
- Los filtros son opcionales - si no se especifican, se usan valores default
- La búsqueda por keyword es case-insensitive
- El dashboard puede invocarse frecuentemente sin impacto (es read-only)
- Útil para daily stand-ups, retrospectivas, o revisión de incidentes
