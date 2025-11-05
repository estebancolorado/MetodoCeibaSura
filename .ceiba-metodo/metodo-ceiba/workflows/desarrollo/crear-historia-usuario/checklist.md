# Checklist de Validación - Create User Story

## Antes de Crear la Historia

- [ ] Se capturó el perfil del usuario (A: Funcional Puro o B: Técnico-Funcional)
- [ ] Se obtuvo la descripción inicial del requerimiento
- [ ] Se identificó el objetivo o beneficio esperado

## Análisis de Contexto Completado

- [ ] Se resolvieron correctamente las variables desde config_source
- [ ] Variable dev_story_location está definida
- [ ] Variable architecture_sharded_location está definida (o se notificó advertencia)
- [ ] Se identificó el área/módulo de negocio donde vive esta funcionalidad
- [ ] Se listaron componentes que manejan funcionalidades similares
- [ ] Se listaron flujos de negocio relacionados
- [ ] Se revisaron historias existentes del mismo contexto para lecciones aprendidas
- [ ] Se identificaron actores y roles ya definidos
- [ ] Se documentaron gaps de documentación (si aplica)
- [ ] Se obtuvo confirmación del usuario para continuar (si falta documentación)

## Análisis de Completitud

- [ ] Usuario y contexto: Rol específico definido
- [ ] Usuario y contexto: Permisos necesarios claros
- [ ] Usuario y contexto: Valor de negocio evidente
- [ ] Funcionalidad: Acción exacta del usuario definida
- [ ] Funcionalidad: Validaciones y reglas claras
- [ ] Interfaz: Ubicación en el sistema definida (si aplica)
- [ ] Interfaz: Flujo de navegación claro (si aplica)
- [ ] Casos extremos: Comportamientos de fallo definidos
- [ ] Casos extremos: Escenarios con datos extremos claros
- [ ] Integraciones: Sistemas externos identificados (si aplica)

## Preguntas al Usuario

- [ ] Se filtraron preguntas según perfil técnico del usuario
- [ ] Solo se hicieron preguntas sobre gaps reales (no asunciones)
- [ ] Se evitaron preguntas técnicas para perfil Funcional Puro
- [ ] Se obtuvieron respuestas completas antes de continuar

## Historia de Usuario Generada

- [ ] Archivo creado con formato correcto: {{numero}}.{{nombre}}.story.md
- [ ] Header con número y título presente
- [ ] Estado marcado como "Borrador (PO)"
- [ ] Historia de usuario con formato "Como/Quiero/Para"
- [ ] Descripción del contexto incluida
- [ ] Criterios de aceptación en formato Dado/Cuando/Entonces
- [ ] Al menos 2-3 escenarios de criterios de aceptación
- [ ] Información recopilada completa (usuario, reglas de negocio, interfaz)
- [ ] Referencias a documentación incluidas (arquitectura, HUs relacionadas)
- [ ] Historias relacionadas y lecciones aprendidas documentadas
- [ ] Notas para refinamiento documentadas
- [ ] Definición de terminado inicial presente
- [ ] Metadata correcta (autor, fecha, estado)

## Criterios de Calidad Verificados

- [ ] ✅ Testeable: Criterios verificables y medibles
- [ ] ✅ Estimable: Funcionalidad clara con complejidad definida
- [ ] ✅ Valuable: Beneficio de negocio evidente y específico
- [ ] ✅ Específico: Sin ambigüedades ni suposiciones
- [ ] ✅ Completo: Toda la información necesaria recopilada
- [ ] ✅ Enfocado: Una funcionalidad específica por historia

## Anti-Patrones Evitados

- [ ] ❌ NO se asumió el tipo de usuario
- [ ] ❌ NO se asumieron permisos o roles
- [ ] ❌ NO se asumieron formatos de datos
- [ ] ❌ NO se asumieron comportamientos en errores
- [ ] ❌ NO se asumió ubicación en la interfaz
- [ ] ❌ NO se asumieron integraciones
- [ ] ❌ NO se consideró nada como "obvio"
- [ ] ❌ NO se preguntaron aspectos técnicos a perfil Funcional Puro

## Próximos Pasos Comunicados

- [ ] Usuario informado sobre análisis arquitectónico (*analisis-y-diseno)
- [ ] Usuario informado sobre refinamiento técnico (refine-story)
- [ ] Usuario informado sobre estimación (estimate-story)
- [ ] Usuario confirmó que la historia está completa

## Archivo Final

- [ ] Historia guardada en: {dev_story_location}/{{numero}}.{{nombre}}.story.md
- [ ] Archivo accesible y legible
- [ ] Formato Markdown correcto
- [ ] Sin errores de sintaxis
