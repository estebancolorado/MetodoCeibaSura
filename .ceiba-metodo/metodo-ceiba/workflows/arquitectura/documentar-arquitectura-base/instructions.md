# Documentar Arquitectura Base - Instrucciones de Workflow

````xml
<critical>The workflow execution engine is governed by: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {project-root}/.ceiba-metodo/metodo-ceiba/workflows/arquitectura/documentar-arquitectura-base/workflow.yaml</critical>
<critical>ALWAYS communicate STRICTLY in {communication_language} regardless of the language used by the user</critical>
<critical>Generate all documents in {document_output_language}</critical>

<critical>REGLAS DE SALIDA DEL TEMPLATE: (1) Remover comentarios HTML de secciones completadas, mantener para secciones vacías/opcionales. (2) Asegurar que TODOS los elementos del template (títulos, encabezados, etiquetas) coincidan con {document_output_language}, no solo el contenido.</critical>

<critical>NUNCA modificar archivos template - deben permanecer inmutables para reutilización</critical>

<critical>**Propósito**
   Documentar proyectos complejos con múltiples repositorios, creando una "arquitectura de alto nivel" que guíe el desarrollo cuando llegan historias de usuario. Esta tarea está optimizada para ecosistemas empresariales con múltiples repositorios y sistemas distribuidos.
</critical>

<critical>**Usa esta tarea cuando:**
   Tienes un proyecto con múltiples repositorios
   Coexisten diferentes stacks tecnológicos
   Necesitas documentar la realidad actual del sistema
   Buscas crear un "GPS" arquitectónico para guiar el desarrollo y toda la documentación del sistema
   Existen reglas de negocio complejas distribuidas en múltiples sistemas
</critical>

<critical>**Prerequisites:**
   Acceso al código fuente de todos los repositorios
   Conocimiento del arquitecto responsable del sistema
   Documentación existente (si está disponible)
   Acceso a configuraciones de infraestructura
</critical>

<workflow>

<step n="0" goal="Configuración y contexto inicial">

   <step n="0.1" goal="Elicitación de contexto del proyecto">

      <critical>**Contexto General:**</critical>
      <ask>
      - ¿Cuál es el propósito principal de este ecosistema?
      - ¿Cuántos repositorios aproximadamente tiene el proyecto?
      - ¿Cuáles son los 3-5 repositorios más críticos?</ask>

      <critical>**Organización:**</critical>
      <ask>
      - ¿Cómo están organizados los componentes (por dominio, función, etc.)?
      - ¿Hay documentación de arquitectura existente que deba revisar? Si tienes documentos .md, carpetas de documentación, o archivos específicos de arquitectura, por favor especifícalos para realizar un análisis exhaustivo.</ask>

      <critical>**Tecnología e Integraciones:**</critical>
      <ask>
      - ¿Qué tecnologías principales usa cada módulo?
      - ¿Cómo se comunican entre sí los sistemas?
      - ¿Qué sistemas externos usa la solución? (IDPs como Auth0, IAM, payment gateways como Stripe/PayPal, servicios de email como SendGrid, storage como AWS S3, CDNs como CloudFlare, etc.)</ask>

      <critical>**Desarrollo:**</critical>
      <ask>
      - ¿Qué tipos de historias de usuario llegan más frecuentemente?
      - ¿Qué componentes suelen verses afectados juntos?</ask>

      <critical>**🔗 CONSIDERACIÓN CRÍTICA - SISTEMAS EXTERNOS:**
      Durante todo el análisis, **prestar especial atención** a las integraciones con sistemas externos y servicios de la nube. Estos son fundamentales para entender la arquitectura completa y DEBEN ser prominentemente incluidos en todos los diagramas generados.
      </critical>

      <template-output>contexto_proyecto</template-output>
      
   </step>

</step>

<step n="1" goal="Análisis profundo del ecosistema">

   <step n="1.1" goal="Análisis de documentación existente">

      <critical>**🚨 ANÁLISIS EXHAUSTIVO DE DOCUMENTACIÓN EXISTENTE - PASO CRÍTICO**</critical>

      <check if="usuario especifica documentos de arquitectura o carpetas con documentación .md">
      
         <critical>**PASO OBLIGATORIO: Análisis Completo de Documentación Proporcionada**</critical>

         <step n="1.1.1" goal="Inventario completo de archivos"> 

            <action if="es una carpeta">localizar todos los archivos .md dentro de la carpeta y subcarpetas</action>
            <action if="es un documento específico">agregar a lista de análisis</action>
            <critical>🚨NO omitir ningún archivo .md encontrado</critical>

         </step>

         <step n="1.1.2" goal="Lectura sistemática archivo por archivo">

            <action>Leer cada documento encontrado COMPLETO (no solo primeras líneas)</action>
            <action>**Extraer información relevante** de cada documento:</action>
            <action>Componentes mencionados</action>
            <action>Tecnologías especificadas</action>
            <action>Integraciones descritas</action>
            <action>Diagramas de arquitectura</action>
            <action>Patrones arquitectónicos</action>
            <action>Reglas de negocio</action>
            <action>Flujos de datos</action>
            <action>APIs y servicios</action>
            <action>Toda la documentación necesaria e importante para este objetivo</action>

         </step>

         <step n="1.1.3" goal="Seguimiento de referencias cruzadas" repeat="hasta que no haya más dependencias">

            <action>Buscar en cada documento **referencias a otros archivos** (ej: "ver documento X", "como se describe en Y")</action>
            <action>Localizar archivos referenciados</action>
            <action>Leer **TODOS los archivos referenciados** encontrados completamente</action>

         </step>

         <step n="1.1.4" goal="Síntesis de documentación analizada">

            <action>Crear mapa consolidado de toda la información arquitectónica encontrada</action>
            <action>Identificar inconsistencias entre documentos</action>
            <action>Detectar gaps de información no documentada</action>
            <action>Marcar información que necesita validación con código</action>

         </step>

         <critical>**REGLA CRÍTICA:** 🚨 No avanzar al siguiente paso hasta completar el análisis de TODA la documentación existente y sus referencias.</critical>

      </check>

      <action>**Continuación del análisis general:**</action>

      <action>Identificar motores de reglas o sistemas de validación</action>
      <action>Mapear dónde residen las reglas de negocio críticas</action>
      <action>Documentar procesos de validación y workflows</action>

      <template-output>documentacion_analizada</template-output>
      
   </step>

   <step n="1.2" goal="Exploración de estructura de repositorios">

      <action>**Ejecutar análisis exhaustivo del ecosistema (complementando lo encontrado en documentación):**</action>
      <action>**Mapear estructura del proyecto**: Examinar directorios raíz y organización general</action>
      <action>**Identificar configuraciones clave**: Buscar archivos de build y dependencias (package.json, pom.xml, requirements.txt, etc.)</action>
      <action>**Revisar documentación adicional**: Localizar READMEs, documentación no especificada inicialmente</action>
      <action>**Analizar patrones de configuración**: Buscar conexiones entre sistemas y configuraciones compartidas</action>
      <action>**Validar información documentada**: Verificar que lo documentado coincida con la realidad del código</action>

      <template-output>estructura_repositorios</template-output>

   </step>

   <step n="1.3" goal="Identificación de stacks tecnológicos">

      <action>**Basándose en la documentación analizada, validar y complementar la información técnica:**

      Para cada módulo/dominio identificado en la documentación:

      <step n="1.3.1" goal="Validación de Stack Documentado" repeat="para cada módulo/dominio identificado en la documentación">

         <action>Verificar que las tecnologías documentadas coincidan con la realidad del código</action>
         <action>Identificar tecnologías en uso no mencionadas en documentación</action>
         <action>Documentar versiones específicas encontradas vs. las documentadas</action>

      </step>

      <step n="1.3.2" goal="Análisis de Dependencias Reales" repeat="para cada módulo/dominio identificado en la documentación">

         <action>Mapear dependencias y configuraciones reales vs. documentadas</action>
         <action>Identificar restricciones técnicas no documentadas</action>
         <action>Documentar configuraciones específicas encontradas</action>

      </step>

      <step n="1.3.3" goal="Patrones Arquitectónicos Implementados" repeat="para cada módulo/dominio identificado en la documentación">

         <action>Verificar patrones arquitectónicos documentados vs. implementados</action>
         <action>Identificar patrones no documentados pero implementados</action>
         <action>Mapear integración real entre módulos vs. documentada</action>

      </step>

      <step n="1.3.4" goal="Gaps de Documentación Técnica" repeat="para cada módulo/dominio identificado en la documentación">

         <action>Identificar tecnologías críticas no documentadas</action>
         <action>Documentar configuraciones complejas no mencionadas</action>
         <action>Marcar inconsistencias entre documentación y realidad</action>

      </step>

      <template-output>stacks_tecnologicos</template-output>

   </step>

   <step n="1.4" goal="Análisis de deuda técnica y estado actual">

      <action>**Evaluar el estado real del ecosistema:**</action>
      <action>**Identificar componentes con deuda técnica**: Buscar patrones de código legacy, comentarios TODO/FIXME, warnings de build</action>
      <action>**Documentar restricciones técnicas**: Limitaciones conocidas, workarounds temporales</action>
      <action>**Mapear dependencias externas críticas**: Identificar librerías obsoletas, versiones desactualizadas, vulnerabilidades conocidas</action>
      <action>**Evaluar realidad de testing**: Cobertura real por módulo, tipos de testing existentes, gaps críticos</action>

      <template-output>deuda_tecnica</template-output>

   </step>

   <step n="1.5" goal="Análisis de patrones de integración y seguridad">

      <action>**Mapear mecanismos de integración del ecosistema:**</action>
      <action>**Canales de comunicación**: Identificar protocolos usados (REST APIs, GraphQL, eventos/mensajería, SOAP, conexiones directas a DB, etc.)</action>
      <action>**Participantes por canal**: Documentar qué sistemas/repositorios participan en cada tipo de integración</action>
      <action>**Mecanismos de Auth/Authz**: Detallar autenticación y autorización por capa (API keys, OAuth, JWT, SAML, etc.)</action>
      <action>**Monitoreo de integraciones**: Identificar herramientas de monitoring y logging para cada canal de comunicación</action>
      <action>**Patrones de resilencia**: Circuit breakers, retries, timeouts, fallbacks identificados</action>
      <action>**Flujos críticos**: Identificar workflows de negocio principales para diagramar</action>

      <template-output>patrones_integracion</template-output>

   </step>

   <step n="1.6" goal="Identificación de diagramas arquitectónicos">

      <action>**Evaluar qué diagramas Mermaid serían útiles basado en el análisis:**</action>
      <action>**Diagrama de arquitectura general**: Si hay 3+ dominios/módulos principales claramente diferenciados</action>
      <action>**Diagrama de flujo de integración**: Si hay múltiples sistemas con comunicación compleja</action>
      <action>**Diagrama de despliegue**: Si hay infraestructura distribuida</action>
      <action>**Diagrama de base de datos**: Si hay múltiples DBs con relaciones complejas</action>
      <action>**Diagrama de autenticación**: Si hay múltiples capas de auth/authz</action>

      <template-output>diagramas_requeridos</template-output>

   </step>

   <step n="1.7" goal="Análisis de comandos de desarrollo">
   
      <action>**Identificar herramientas y comandos críticos:**</action>
      <action>**Scripts de build**: package.json scripts, Makefiles, gradle tasks</action>
      <action>**Comandos de setup**: instalación, configuración inicial</action>
      <action>**Testing**: comandos de test unitarios, integración, e2e</action>
      <action>**Deployment**: scripts de despliegue, configuración de entornos</action>

      <template-output>comandos_desarrollo</template-output>

   </step>

</step>

<step n="2" goal="Validación final obligatoria con el usuario">
   <critical>🚨 PASO CRÍTICO - NO OMITIR</critical>
   <action>Una vez generado el documento completo, realizar la validación final:</action>
   
   <step n="2.1" goal="Presentar resumen al usuario">
      <action>**Mostrar al usuario un resumen estructurado:**

      📋 RESUMEN DE DOCUMENTACIÓN GENERADA

      ✅ Análisis completado:

      - [x] Documentación existente analizada: {lista de archivos leídos}
      - [x] Repositorios analizados: {número y nombres}
      - [x] Sistemas externos identificados: {lista o "Ninguno"}
      - [x] Diagramas generados: {tipos de diagramas creados}
      </action>
   </step>

   <step n="2.2" goal="Solicitar validación explícita">

      <critical>🚨 **Preguntar obligatoriamente:**</critical>

      <ask>
      1. ¿La información documentada refleja correctamente la realidad de tu sistema?
      2. ¿Hay repositorios, tecnologías o integraciones importantes que falten?
      3. ¿Los diagramas muestran correctamente la arquitectura de tu ecosistema?
      4. ¿Hay información crítica que consideras incorrecta o incompleta?
      5. ¿Necesitas que ajuste algo antes de finalizar?</ask>

   </step>

   <step n="2.3" goal="Procesar retroalimentación">
      <check if="hay faltantes o correcciones">
         <action>Aplicar cambios</action>
         <action>Rrepetir validación</action>
      </check>
   
   <action if="hay gaps de información">Marcarlos explícitamente en la documentación</action>   

   <action if="usuario confirma exactitud">Proceder a finalizar</action>   

   <critical>**🔒 REGLA INQUEBRANTABLE:** No finalizar la tarea sin confirmación explícita del usuario de que la documentación es exacta y completa.</critical>

   <critical>⚠️ **Reglas Obligatorias de Verificación**
      Antes de considerar completada la tarea, verificar OBLIGATORIAMENTE cada punto:
      1. **ANÁLISIS COMPLETO DE DOCUMENTACIÓN:** Confirmar que se leyó TODA la documentación existente especificada por el usuario y sus referencias cruzadas
      2. **INCLUSIÓN DE SISTEMAS EXTERNOS:** SI se identifican sistemas externos o servicios de la nube, verificar que TODOS los diagramas los incluyen explícitamente
      3. **ELICITACIÓN COMPLETA:** Confirmar que se hicieron TODAS las preguntas obligatorias de la sección 0.2 y se obtuvieron respuestas
      4. **VALIDACIÓN DE DIAGRAMAS:** Revisar que cada diagrama Mermaid generado refleje la realidad del proyecto analizado, no solo el template
      5. **COBERTURA DE INTEGRACIONES:** Verificar que la sección de "Puntos de Integración Críticos" incluye tanto sistemas internos como externos
      6. **COMANDOS FUNCIONALES:** Confirmar que los comandos de desarrollo listados son reales y ejecutables en el proyecto
      7. **DEPENDENCIAS ACTUALIZADAS:** Verificar que las dependencias críticas listadas reflejen las versiones reales encontradas en el análisis
      8. **ARQUITECTURA VALIDADA:** Confirmar que la arquitectura documentada es consistente entre la descripción textual y los diagramas
      9. **GAPS IDENTIFICADOS:** Listar explícitamente qué información no se pudo obtener y requiere investigación adicional
      10. **CONFIRMACIÓN FINAL OBLIGATORIA:** Presentar al usuario un resumen de la documentación generada y solicitar explícitamente que valide su exactitud. Preguntar específicamente: "¿Hay información faltante o incorrecta que deba corregir antes de finalizar?" No continuar sin confirmación explícita del usuario.
   </critical>

   <critical>**🚨 REGLA CRÍTICA:** No dar por terminada la tarea sin confirmar cada punto de verificación con evidencia específica del análisis realizado.</critical>

   <critical>**📌 Este GPS será el punto de partida para crear documentación más específica según las necesidades del equipo de desarrollo.**</critical>

   </step>

</step>

</workflow>
````
