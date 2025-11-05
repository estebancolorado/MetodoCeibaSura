# Create User Story - Escritor HU Especialista

<workflow>

<critical>The workflow execution engine is governed by: {project-root}/.ceiba-metodo/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {installed_path}/workflow.yaml</critical>
<critical>ALWAYS communicate STRICTLY in {communication_language} regardless of the language used by the user</critical>
<critical>HTML COMMENTS HANDLING: El template contiene comentarios HTML como guías. Al generar el output final, REMOVER comentarios HTML de secciones completas y MANTENER comentarios de secciones vacías/incompletas</critical>
<critical>Generate all documents in {document_output_language}</critical>
<critical>NUNCA modificar archivos template - deben permanecer inmutables para reutilización</critical>


<note title="Purpose">
Crear o importar historias de usuario siguiendo una metodología estructurada, enfocándose en obtener información completa del usuario sin asumir detalles. Esta tarea es responsabilidad del Product Owner (PO) y genera la base que posteriormente será refinada. Soporta dos modos: escribir desde cero o importar y validar historias existentes.
</note>

<note title="When to Use This Workflow">
- El PO necesita crear una nueva historia de usuario desde cero
- Se requiere recopilar información detallada del stakeholder
- Es necesario crear una historia completa sin asumir detalles
- Se tiene una historia existente (de Jira, otro sistema, documento) que necesita validación y completitud
- Se requiere importar historias externas al formato estándar del proyecto
- Se necesita una base sólida para posterior refinamiento técnico
</note>

<note title="Prerequisites">
- Identificación clara del requerimiento o funcionalidad
- Acceso al stakeholder o usuario para aclaraciones
- Ubicación definida para almacenar la historia (dev_story_location en config del módulo)
</note>

<mandate title="Reglas de Comportamiento OBLIGATORIAS">
1. DETECCIÓN ANTI-ASUNCIÓN: Si piensas "seguramente se refiere a...", "es lógico que...", "probablemente necesita..." → OBLIGATORIO preguntar específicamente
2. NO ASUMIR INFORMACIÓN CRÍTICA: NUNCA asumir tipos de usuario, permisos, formatos de datos, comportamientos de error, ubicación en interfaz, o integraciones
3. PREGUNTAS INTELIGENTES: Solo preguntar sobre gaps reales identificados en el análisis de completitud, aplicando filtro por perfil técnico
4. HISTORIAS EXISTENTES: ANALIZAR SIEMPRE - OBLIGATORIAMENTE revisar historias existentes para identificar patrones, dependencias y lecciones aprendidas
5. CONTEXTO vs DECISIÓN TÉCNICA: SÍ documentar contexto de negocio mencionado por stakeholder (módulo afectado, pantalla, integración con sistema X). NO tomar decisiones técnicas sobre endpoints, componentes de código, cambios backend/frontend, o arquitectura interna - eso es trabajo del ARQUITECTO
6. PRESERVAR INTENCIÓN EN MODO IMPORTAR: Si modo == 2, preservar la intención original de la historia importada mientras se completa información faltante y se mejora claridad
</mandate>

<step n="0" goal="Captura Inicial del Requerimiento">
  <critical>OBJETIVO: Entender QUÉ quiere hacer el usuario y CÓMO quiere trabajar</critical>
  
  <action>Mostrar mensaje de bienvenida al usuario:</action>
  
  <message>
¡Hola! Voy a ayudarte a crear una nueva historia de usuario siguiendo nuestra metodología.

**Paso 1/6: Caracterización de perfil y modo de trabajo**

Primero, necesito conocer tu perfil para adaptar mis preguntas de manera adecuada:

**Caracterización de perfil:**

¿Cuál de estas opciones describe mejor tu rol y conocimiento?

**A) Perfil Funcional Puro:**
- Te enfocas exclusivamente en aspectos de negocio
- No manejas conceptos técnicos (APIs, bases de datos, arquitectura)
- Tu expertise está en procesos, flujos de usuario y reglas de negocio

**B) Perfil Técnico-Funcional:**
- Tienes conocimiento tanto de negocio como técnico
- Puedes discutir aspectos de arquitectura, integración y tecnología
- Entiendes conceptos como APIs, microservicios, bases de datos

---

**¿Cómo quieres trabajar esta historia?**

**OPCIÓN 1: ESCRIBIR DESDE CERO** ✍️
- Tienes un requerimiento verbal o conceptual
- Yo te haré preguntas para construir la historia completa
- Partes de una descripción general y yo estructuro todo

**OPCIÓN 2: IMPORTAR HISTORIA EXISTENTE** 📋
- Ya tienes una historia escrita (de Jira, otro sistema, documento, etc.)
- Yo la validaré, completaré información faltante y la formatearé
- Partes de contenido ya estructurado que necesita revisión

---

Por favor responde:
- **Tu perfil:** A o B
- **Modo de trabajo:** 1 (Escribir desde cero) o 2 (Importar existente)
- **Contenido:**
  - Si elegiste **1 (Escribir)**: Descripción breve del requerimiento (¿Qué funcionalidad necesitas? ¿Cuál es el objetivo?)
  - Si elegiste **2 (Importar)**: Pega el contenido completo de la historia de usuario que ya tienes

No te preocupes por los detalles técnicos ahora, solo la información inicial.
  </message>
  
  <ask>Esperar respuesta del usuario con perfil, modo y contenido</ask>
  
  <action>REGISTRAR INTERNAMENTE la respuesta del perfil para condicionar todas las preguntas posteriores:</action>
  <action if="perfil == A">Establecer filtro: SOLO preguntas de negocio, flujos funcionales y criterios de aceptación</action>
  <action if="perfil == B">Establecer filtro: Permitir preguntas técnicas cuando sea necesario para completar la historia</action>
  
  <action>REGISTRAR INTERNAMENTE el modo de trabajo seleccionado:</action>
  <action if="modo == 1">Establecer modo: ESCRIBIR (construir desde cero)</action>
  <action if="modo == 2">Establecer modo: IMPORTAR (validar y mejorar existente)</action>
  
  <critical>BLOQUEO OBLIGATORIO: No continuar hasta obtener:</critical>
  <check if="modo == 1 (Escribir)">
    <action>Validar que se tiene: Caracterización del perfil (A o B), Modo (1), Descripción del requerimiento, Propósito o beneficio básico</action>
  </check>
  <check if="modo == 2 (Importar)">
    <action>Validar que se tiene: Caracterización del perfil (A o B), Modo (2), Contenido completo de la historia a importar</action>
  </check>
  
  <check if="información insuficiente">
    <message>
Necesito completar la información inicial para poder ayudarte. Por favor proporciona:
- Tu perfil: ¿A (Funcional Puro) o B (Técnico-Funcional)?
- Modo de trabajo: ¿1 (Escribir) o 2 (Importar)?
- Contenido: 
  * Si modo 1: ¿Qué funcionalidad necesitas? ¿Quién la va a usar y para qué?
  * Si modo 2: Pega el contenido completo de la historia a importar
    </message>
    <goto step="0">Repetir captura inicial</goto>
  </check>
</step>

<step n="1" goal="Análisis Obligatorio de Contexto y Extracción de Conceptos Clave">
  <critical>PASO OBLIGATORIO - NO CONTINUAR SIN COMPLETAR</critical>
  <critical>EJECUTAR INMEDIATAMENTE después de tener el requerimiento inicial del paso 0</critical>
  
  <substep n="1.1" title="Cargar Configuración del Proyecto">
    <action>Resolve variables from config_source: dev_story_location, architecture_sharded_location, architecture_file, output_folder, user_name, communication_language</action>
    
    <check if="dev_story_location es undefined">
      <message>❌ ERROR: dev_story_location no encontrado en config. Verifica que {config_source} tenga configuradas las ubicaciones necesarias.</message>
      <action>HALT workflow</action>
    </check>
    
    <check if="architecture_sharded_location es undefined">
      <message>⚠️ ADVERTENCIA: architecture_sharded_location no configurado. El análisis arquitectónico será limitado.</message>
    </check>
  </substep>
  
  <substep n="1.2" title="Identificación de Contexto de Negocio (¿Dónde vive esta funcionalidad?)">
    <critical>OBJETIVO: Identificar el CONTEXTO DE NEGOCIO para hacer preguntas más precisas - NO hacer análisis técnico</critical>
    
    <action>1. Ubicar el área de negocio donde vive esta funcionalidad:</action>
    <action>- Revisar arquitectura base ({architecture_sharded_location}/index.md) para identificar el MÓDULO o ÁREA DE NEGOCIO relacionada</action>
    <action>- Listar componentes documentados que manejan funcionalidades similares ({architecture_sharded_location}/architecture-*.md)</action>
    <action>- Identificar flujos de negocio existentes relacionados ({architecture_sharded_location}/flujo-*.md)</action>
    <action>- PROPÓSITO: Entender "¿Dónde vive esta funcionalidad en el negocio?" para contextualizar preguntas posteriores</action>
    <action>- NO analizar patrones técnicos, APIs, o arquitectura interna (eso es trabajo del Arquitecto)</action>
    
    <action>2. Historias de usuario existentes (CONTEXTO Y LECCIONES):</action>
    <action>- Revisar historias existentes en {dev_story_location} para identificar:</action>
    <action>  * Funcionalidades similares o relacionadas (solo nombres y números)</action>
    <action>  * Actores y roles ya definidos en otras historias del mismo contexto</action>
    <action>  * Lecciones de negocio: validaciones importantes, permisos, casos extremos, problemas de usabilidad</action>
    <action>- PROHIBIDO: Extraer o documentar decisiones técnicas de HU pasadas (componentes, endpoints, librerías, "filtrado en cliente/servidor", arquitectura)</action>
    <action>- PROPÓSITO: Contexto de negocio para hacer preguntas más efectivas</action>
  </substep>
  
  <substep n="1.3" title="Extracción Simple de Conceptos Clave">
    <action>Extraer conceptos del requerimiento (NIVEL DE NEGOCIO solamente):</action>
    <action>🎯 Actores y roles involucrados (de negocio, no técnicos)</action>
    <action>📋 Entidades principales mencionadas (solo nombres, NO tipos de datos)</action>
    <action>🔗 Sistemas externos mencionados por el usuario (si aplica)</action>
    <action>🏢 Área de negocio identificada (módulo, componente de negocio)</action>
    <action>⚠️ Lecciones aprendidas de historias similares</action>
  </substep>
  
  <substep n="1.4" title="Documentar Referencias y Contexto Encontrado">
    <action>SOLO documentar internamente (para usar en pasos posteriores):</action>
    <action>- Área/módulo de negocio donde vive la funcionalidad</action>
    <action>- Lista de historias relacionadas (números y nombres)</action>
    <action>- Lecciones aprendidas relevantes</action>
    <action>- Referencias a docs de arquitectura (rutas) para consulta futura</action>
    <action>- Actores ya definidos en el contexto</action>
    <action>- NO documentar análisis técnico o arquitectónico detallado</action>
  </substep>
  
  <substep n="1.5" title="Verificación de Completitud de Documentación">
    <action>Verificar si existen elementos críticos:</action>
    <action>- ❌ Arquitectura base ({architecture_sharded_location}/index.md)</action>
    <action>- ❌ Documentación de componentes (architecture-*.md, component*.md, etc.)</action>
    <action>- ❌ Flujos de negocio documentados (flujo-*.md, flow-*.md, proceso-*.md, etc.)</action>
    
    <check if="documentación incompleta detectada">
      <message>
⚠️ **ADVERTENCIA: Documentación Incompleta Detectada**

No se encontró documentación completa en las siguientes áreas:
{{lista_elementos_faltantes}}

**IMPACTO:** Sin esta documentación, el análisis puede ser menos preciso y las preguntas podrían no cubrir todos los aspectos arquitectónicos importantes.

**OPCIONES:**
1. **CONTINUAR** - Proceder con el análisis basado en exploración de código y preguntas adicionales
2. **PAUSAR** - Detener para documentar la arquitectura primero (recomendado para mejor precisión)

¿Deseas continuar sin la documentación completa o prefieres pausar para documentar la arquitectura primero?
      </message>
      
      <ask>Esperar respuesta del usuario (CONTINUAR o PAUSAR)</ask>
      
      <check if="usuario elige PAUSAR">
        <action>HALT workflow con mensaje: "Workflow pausado. Por favor documenta la arquitectura y vuelve a ejecutar."</action>
      </check>
    </check>
  </substep>
  
  <critical>REGLA CRÍTICA: NO continuar al paso 2 sin completar TODO este análisis obligatorio</critical>
</step>

<step n="2" goal="Análisis Exhaustivo de Completitud">
  <critical>EVALUACIÓN CRÍTICA OBLIGATORIA: Revisar la información del requerimiento inicial + contexto arquitectónico encontrado y determinar si un desarrollador puede trabajar SIN ASUMIR NADA.</critical>
  
  <action>CRITERIOS OBJETIVOS DE SUFICIENCIA - TODOS deben estar claros:</action>
  
  <substep n="2.1" title="Información de Usuario y Contexto (OBLIGATORIO)">
    <action>❓ ¿Está claro QUIÉN específicamente puede realizar esta acción? (rol exacto, no genérico)</action>
    <action>❓ ¿Están definidos los PERMISOS específicos necesarios?</action>
    <action>❓ ¿Es evidente el VALOR DE NEGOCIO concreto? (no solo "para mejorar")</action>
  </substep>
  
  <substep n="2.2" title="Funcionalidad Específica (OBLIGATORIO)">
    <action>❓ ¿Está clara la ACCIÓN EXACTA que realiza el usuario? (verbo específico + objeto)</action>
    <action>❓ ¿Están claras las VALIDACIONES y reglas de negocio?</action>
  </substep>
  
  <substep n="2.3" title="Interfaz y Comportamiento (OBLIGATORIO)">
    <action>❓ ¿Está claro DÓNDE se ubica en el sistema? (pantalla específica)</action>
    <action>❓ ¿Está definido el FLUJO DE NAVEGACIÓN del usuario?</action>
  </substep>
  
  <substep n="2.4" title="Casos Extremos y Errores (OBLIGATORIO)">
    <action>❓ ¿Están definidos los comportamientos en CASOS DE FALLO?</action>
    <action>❓ ¿Están claros los escenarios con DATOS EXTREMOS?</action>
  </substep>
  
  <substep n="2.5" title="Integraciones (OBLIGATORIO si aplica)">
    <action>❓ ¿Están claras las INTEGRACIONES con sistemas externos? (si aplica)</action>
  </substep>
  
  <critical>REGLA CRÍTICA: Si hay CUALQUIER "❓" sin respuesta clara, la información es INSUFICIENTE</critical>
  
  <action>DECISIÓN ESTRICTA:</action>
  <check if="TODOS los criterios claros">
    <action>Saltar al paso 5 (Historia Final)</action>
    <goto step="5">Historia Final</goto>
  </check>
  
  <check if="CUALQUIER criterio unclear">
    <action>OBLIGATORIO continuar al paso 3 (Preguntas)</action>
  </check>
</step>

<step n="3" goal="Preguntas Detalladas">
  <critical>PREREQUISITO: Completar pasos 0, 1 y 2 antes de preguntar</critical>
  
  <critical>CONTEXTO PARA PREGUNTAS INTELIGENTES: Usar TODA la información recopilada en paso 1.4 (área de negocio, historias relacionadas, actores conocidos, entidades existentes) + descripción del usuario para formular preguntas contextualizadas y efectivas, NO preguntas genéricas</critical>
  
  <action>Mostrar mensaje introductorio:</action>
  <message>
**Paso 3/6: Preguntas de aclaración**

Basándome en tu requerimiento inicial y el análisis del contexto existente, detecté información que no puedo asumir. Necesito aclarar estos detalles específicos para crear una historia completa:
  </message>
  
  <critical>REGLAS CRÍTICAS ANTI-ASUNCIÓN:</critical>
  <action>- NUNCA asumir el tipo de usuario específico</action>
  <action>- NUNCA asumir permisos o roles</action>
  <action>- NUNCA asumir formatos de datos</action>
  <action>- NUNCA asumir comportamientos en errores</action>
  <action>- NUNCA asumir ubicación en la interfaz</action>
  <action>- NUNCA asumir integraciones con otros sistemas</action>
  <action>- NUNCA asumir que "es obvio"</action>
  
  <critical>FILTRO OBLIGATORIO POR PERFIL TÉCNICO:</critical>
  
  <check if="user_profile == A (Funcional Puro)">
    <action>✅ PERMITIDAS: Preguntas de negocio, flujos funcionales, criterios de aceptación, validaciones de negocio</action>
    <action>❌ PROHIBIDAS: Arquitectura, APIs, tecnologías, integraciones técnicas, estructura de datos</action>
  </check>
  
  <check if="user_profile == B (Técnico-Funcional)">
    <action>✅ PERMITIDAS: Todas las preguntas, incluyendo aspectos técnicos cuando sea necesario</action>
  </check>
  
  <action>FORMATO OBLIGATORIO: Solo preguntar sobre gaps identificados en el paso 2</action>
  
  <action>GUÍA PARA PREGUNTAS INTELIGENTES (usar solo según gaps del paso 2):</action>
  
  <check if="falta información de Usuario y Contexto">
    <ask>1. ¿Qué tipo exacto de usuario realiza esta acción? [Si encontraste actores en paso 1.4, sugiérelos: "¿Es Administrador de X, Auditor, u otro rol?"]</ask>
    <ask>2. ¿Qué permisos específicos necesita este usuario para realizar la acción?</ask>
    <ask>3. ¿Cuál es el beneficio concreto que obtiene el usuario al realizar esta acción?</ask>
  </check>
  
  <check if="falta información de Funcionalidad">
    <ask>4. ¿Qué acción exacta realiza el usuario? (verbo específico + objeto específico)</ask>
    <ask>5. ¿Sobre qué datos específicos actúa? [Si encontraste entidades en paso 1.4, sugiérelas: "¿Actúa sobre Facturas, Períodos, o ambos?"]</ask>
    <ask>6. ¿Qué validaciones exactas deben aplicarse? [Si encontraste lecciones en historias relacionadas, mencionarlas]</ask>
  </check>
  
  <check if="falta información de Interfaz">
    <ask>7. ¿En qué pantalla exacta se ubica esta funcionalidad? [Si usuario mencionó módulo/área, contextualizarlo: "¿En qué sección del módulo de X?"]</ask>
    <ask>8. ¿Cómo navega el usuario para llegar a esta funcionalidad?</ask>
    <ask>9. ¿Qué elementos de interfaz específicos se necesitan? (botones, campos, etc.)</ask>
  </check>
  
  <check if="falta información de Errores">
    <ask>10. ¿Qué mensaje exacto se muestra cuando la operación falla?</ask>
    <ask>11. ¿Qué sucede específicamente cuando los datos son inválidos?</ask>
    <ask>12. ¿Cómo se comporta el sistema con datos extremos? (vacíos, muy largos, etc.)</ask>
  </check>
  
  <check if="falta información de Negocio">
    <check if="user_profile == A OR información NO técnica">
      <ask>13. ¿Esta funcionalidad afecta algún proceso de negocio existente?</ask>
      <ask>14. ¿En qué parte del flujo de trabajo del usuario debería aparecer esta funcionalidad?</ask>
    </check>
  </check>
  
  <critical>REGLA CRÍTICA: Solo hacer las preguntas cuya respuesta NO esté clara en la información ya recopilada</critical>
  
  <action>PATRÓN CORRECTO - Hacer preguntas específicas de negocio:</action>
  <action>✅ "¿Qué campos específicos debe completar el usuario?"</action>
  <action>✅ "¿Qué mensaje exacto se muestra al usuario cuando guarda exitosamente?"</action>
  <action>✅ "¿Quién específicamente puede ver esta información?"</action>
  
  <action>ANTI-PATRÓN - NO hacer estas preguntas genéricas:</action>
  <action>❌ "¿Qué tecnología prefieres usar?"</action>
  <action>❌ "¿Qué base de datos usamos?"</action>
  <action>❌ "¿Prefieres REST o GraphQL?"</action>
  <action>❌ "¿Como debe ser la arquitectura del componente?"</action>
</step>

<step n="4" goal="Confirmación Obligatoria">
  <critical>BLOQUEO TOTAL DEL PROCESO</critical>
  <critical>NUNCA continuar hasta que el usuario responda TODAS las preguntas del paso 3</critical>
  
  <message>
**Paso 4/6: Confirmación de respuestas**

Por favor, responde todas las preguntas numeradas antes de continuar con la creación de la historia de usuario.
  </message>
  
  <ask>Esperar respuestas completas del usuario a TODAS las preguntas del paso 3</ask>
  
  <check if="respuestas incompletas">
    <action>Recordar al usuario las preguntas pendientes</action>
    <goto step="4">Volver a solicitar respuestas</goto>
  </check>
</step>

<step n="5" goal="Historia de Usuario Final">
  
  <substep n="5.1" title="Configuración y Solicitud de Información">
    <action>Verificar que las variables estén resueltas:</action>
    <action>- dev_story_location debe estar definido desde config_source</action>
    
    <check if="dev_story_location es undefined">
      <message>❌ ERROR: dev_story_location no encontrado. Verifica la configuración del módulo.</message>
      <action>HALT workflow</action>
    </check>
    
    <message>
**Paso 5/6: Información de la historia**

Para crear la historia de usuario, necesito que me proporciones:

1. **Número/Consecutivo de la historia:** ¿Qué número quieres asignar a esta historia?
2. **Nombre de la historia:** ¿Cómo quieres que se llame el archivo de la historia?

**Ejemplo:**
- Número: 42
- Nombre: crear-usuario-administrador

El archivo se creará como: `42.crear-usuario-administrador.story.md`
    </message>
    
    <ask>Esperar respuesta del usuario con número y nombre</ask>
    
    <action>Validar y formatear información recibida:</action>
    <action>- Nombre: Aplicar formateo automático:</action>
    <action>  * Convertir a lowercase (minúsculas)</action>
    <action>  * Reemplazar espacios por guiones (-)</action>
    <action>  * Eliminar tildes y caracteres especiales</action>
    <action>  * Eliminar caracteres no alfanuméricos excepto guiones</action>
    
    <example>
Ejemplos de formateo:
- "Crear Usuario Administrador" → "crear-usuario-administrador"
- "Módulo de Pagos & Facturación" → "modulo-de-pagos-facturacion"
- "Login con 2FA" → "login-con-2fa"
    </example>
    
    <action>Anunciar creación:</action>
    <message>Creando historia #{{story_number}}: {{story_name_formatted}}</message>
  </substep>
  
  <substep n="5.2" title="Crear Archivo de Historia">
    <action>Crear directorio {dev_story_location} si no existe</action>
    <action>Inicializar archivo desde template: {installed_path}/historia-de-usuario.template.md</action>
    <action>Escribir template a: {dev_story_location}/{{story_number}}.{{story_name_formatted}}.story.md</action>
  </substep>
  
  <substep n="5.3" title="Generar Contenido Completo de la Historia">
    <critical>Usar TODA la información recopilada en los pasos 0, 1, 2, 3 y 4 para generar contenido completo y detallado</critical>
    
    <action>Del paso 0: Extraer perfil técnico del usuario (A/B), modo de trabajo (1=Escribir/2=Importar) y contenido inicial (descripción o historia existente)</action>
    <action>Del paso 1: Extraer análisis de arquitectura (GPS, componentes, flujos), historias relacionadas, y patrones del código</action>
    <action>Del paso 2: Extraer análisis de completitud con criterios detallados de usuario, datos, interfaz, errores e impacto</action>
    <action>Del paso 3: Extraer respuestas a preguntas específicas (si se realizaron)</action>
    <action>Del paso 4: Extraer confirmación final y validaciones del usuario</action>
    
    <critical>ESTRATEGIA SEGÚN MODO DE TRABAJO:</critical>
    
    <check if="modo == 1 (ESCRIBIR DESDE CERO)">
      <action>GENERAR TODO el contenido desde cero usando template</action>
      <action>Construir cada sección basándose en la información recopilada en los pasos previos</action>
      <action>Aplicar formato estándar del template.md</action>
      <action>Estructurar criterios de aceptación en formato Given-When-Then</action>
      <action>Organizar toda la información recopilada en las secciones apropiadas</action>
    </check>
    
    <check if="modo == 2 (IMPORTAR HISTORIA EXISTENTE)">
      <action>PARTIR de la historia existente proporcionada por el usuario en el paso 0</action>
      <action>PRESERVAR la intención original y el contenido base de la historia importada</action>
      <action>INTEGRAR la información adicional recopilada en pasos 1-4 (análisis arquitectónico, contexto, respuestas a preguntas)</action>
      <action>COMPLETAR secciones faltantes o incompletas identificadas en el análisis de completitud</action>
      <action>MEJORAR la claridad y precisión manteniendo el espíritu original</action>
      <action>FORMATEAR según el template estándar del proyecto</action>
      <action>AGREGAR metadatos de importación: Indicar que fue una historia importada, validada y mejorada</action>
    </check>
    
    <critical>Escribir TODAS las secciones del template con información recopilada - NO dejar placeholders vacíos</critical>
    
    <mandate>REGLAS DE SALIDA DEL TEMPLATE: (1) Remover comentarios HTML de secciones completadas, mantener para secciones vacías/opcionales. (2) Asegurar que TODOS los elementos del template (títulos, encabezados, etiquetas) coincidan con {document_output_language}, no solo el contenido.</mandate>
    
    <!-- Historia de Usuario -->
    <template-output>story_number</template-output>
    <template-output>story_title</template-output>
    <template-output>role</template-output>
    <template-output>action</template-output>
    <template-output>benefit</template-output>
    <template-output>description</template-output>
    
    <!-- Criterios de Aceptación -->
    <template-output>given_1</template-output>
    <template-output>when_1</template-output>
    <template-output>then_1</template-output>
    <template-output>given_2</template-output>
    <template-output>when_2</template-output>
    <template-output>then_2</template-output>
    <template-output>given_3</template-output>
    <template-output>when_3</template-output>
    <template-output>then_3</template-output>
    <template-output>additional_scenarios</template-output>
    
    <!-- Información Recopilada -->
    <template-output>user_type</template-output>
    <template-output>permissions</template-output>
    <template-output>business_value</template-output>
    <template-output>business_rules</template-output>
    <template-output>interface_navigation</template-output>
    <template-output>external_systems_mentioned</template-output>
    
    <!-- Contexto y Referencias -->
    <template-output>architecture_doc_reference</template-output>
    <template-output>related_stories_ids</template-output>
    <template-output>lessons_learned_simple</template-output>
    
    <!-- Metadata -->
    <template-output>user_name</template-output>
    <template-output>date</template-output>
    <template-output>related_stories_ids</template-output>
  </substep>
  
  <substep n="5.4" title="Calcular Métricas de Tiempo">
    <action>{{tiempo_metodo_ceiba}} = Tiempo total transcurrido desde paso 0 hasta ahora (en minutos)</action>
    <action>{{tiempo_tradicional}} = {{tiempo_metodo_ceiba}} * 2.5</action>
    <action>{{porcentaje_optimizacion}} = (({{tiempo_tradicional}} - {{tiempo_metodo_ceiba}}) / {{tiempo_tradicional}}) * 100</action>
    
    <template-output>tiempo_metodo_ceiba</template-output>
    <template-output>tiempo_tradicional</template-output>
    <template-output>porcentaje_optimizacion</template-output>
  </substep>
</step>

<step n="6" goal="Confirmación Final y Entrega">
  <message>
**Paso 6/6: Historia completada**

✅ Historia de usuario #{{story_number}} creada exitosamente en:
`{dev_story_location}/{{story_number}}.{{story_name_formatted}}.story.md`

**Resumen:**
- **Número:** {{story_number}}
- **Nombre del archivo:** {{story_name_formatted}}.story.md
- **Título:** {{story_title}}
- **Usuario objetivo:** {{user_type}}
- **Funcionalidad:** {{brief_description}}
- **Criterios de aceptación:** {{num_scenarios}} escenarios definidos
- **Estado:** Borrador (PO) - Lista para refinamiento técnico

**Próximos pasos:**

1. 🏗️ **ANÁLISIS ARQUITECTÓNICO PRIMERO:** El Arquitecto debe usar el comando `*analisis-y-diseno` para evaluar el impacto de esta historia en la arquitectura existente

2. 🔧 **Refinamiento técnico:** El Developer debe usar la tarea `refine-story` para añadir contexto técnico basado en el análisis arquitectónico

3. 📊 **Estimación:** Posterior estimación con la tarea `estimate-story`

4. 💻 **Desarrollo:** Desarrollo por el Dev Agent con claridad arquitectónica

**Criterios de Calidad para la Historia:**
- ✅ Testeable: Criterios verificables y medibles
- ✅ Estimable: Funcionalidad clara con complejidad definida
- ✅ Valuable: Beneficio de negocio evidente y específico
- ✅ Específico: Sin ambigüedades ni suposiciones
- ✅ Completo: Toda la información necesaria recopilada
- ✅ Enfocado: Una funcionalidad específica por historia

**Métricas de Tiempo de Redacción:**
⏱️ Tiempo con Método Ceiba: {{tiempo_metodo_ceiba}} minutos
⏱️ Tiempo estimado método tradicional: {{tiempo_tradicional}} minutos
📈 Optimización: {{porcentaje_optimizacion}}%
  </message>
  
  <ask>¿La historia está completa según tu expectativa o necesita algún ajuste?</ask>
  
  <check if="necesita ajustes">
    <action>Realizar ajustes solicitados</action>
    <goto step="5">Regenerar historia con ajustes</goto>
  </check>
  
  <action>Workflow completado exitosamente</action>
</step>

<note title="Enfoque y Metodología">
Esta tarea se enfoca en la recopilación completa de requisitos funcionales SIN ASUMIR NADA. Soporta dos modos: ESCRIBIR (construir desde cero) e IMPORTAR (validar, completar y mejorar historia existente). SIEMPRE inicia capturando el perfil, modo y contenido antes de buscar contexto del proyecto.
</note>

<note title="Análisis Obligatorio de Contexto">
El paso 1 (análisis de arquitectura) es OBLIGATORIO y no puede omitirse para ambos modos. El paso 2 (análisis crítico) es el FILTRO CLAVE para detectar información faltante vs. asumida. Este análisis de contexto arquitectónico es fundamental para hacer preguntas inteligentes.
</note>

<note title="Documentación y Precisión">
ADVERTENCIA CRÍTICA: Sin documentación completa (arquitectura base, componentes, flujos, historias existentes), el resultado será menos preciso. Siempre avisar al usuario y obtener confirmación para continuar. DOCUMENTACIÓN INCOMPLETA = RESULTADO MENOS PRECISO: Siempre informar al usuario sobre las limitaciones.
</note>

<note title="Historias como Contexto">
Las historias existentes son tan importantes como la arquitectura para crear historias consistentes y evitar duplicación. Aprovechar patrones de criterios de aceptación y estructuras de historias similares. Identificar dependencias con historias ya implementadas evita conflictos futuros.
</note>

<note title="Principio Anti-Asunción">
Si la información no está explícitamente definida, hay que preguntarla. No hay "información obvia". PREGUNTAS INTELIGENTES: Solo sobre gaps reales de negocio, no sobre detalles técnicos de implementación. El PO NO toma decisiones técnicas sobre componentes, endpoints, arquitectura - eso es trabajo del ARQUITECTO. El PO SÍ documenta contexto de negocio mencionado por stakeholder (ej: "impacta módulo de facturación", "nueva ventana en sección X").
</note>

<note title="Modos de Trabajo">
MODO IMPORTAR: Preservar intención original, completar gaps, mejorar claridad sin perder el espíritu de la historia original. MODO ESCRIBIR: Construir toda la estructura desde cero basándose en el análisis y las respuestas del usuario.
</note>

<note title="Alcance y Limitaciones">
No incluye análisis técnico profundo (eso lo hace el SM en refine-story). No asume conocimientos técnicos del stakeholder. Prioriza la comprensión del negocio sobre la implementación. El archivo creado será la base para todo el trabajo posterior del equipo.
</note>

<note title="Calidad sobre Velocidad">
CALIDAD > VELOCIDAD: Es mejor hacer preguntas específicas que crear historias con asunciones incorrectas. La numeración de pasos ayuda al usuario a entender el progreso del proceso. El contexto de negocio (módulo, pantalla) ayuda a hacer preguntas más efectivas, pero el análisis técnico es trabajo del ARQUITECTO.
</note>

</workflow>
