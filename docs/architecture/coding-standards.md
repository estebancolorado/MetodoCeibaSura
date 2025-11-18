# Proyecto Guidewire Sura - Estándares de Código 📝

## 📋 **Información General**

### Propósito del Documento

Este documento define los estándares de código obligatorios y recomendados para el desarrollo en el ecosistema Guidewire Sura. Estos estándares garantizan consistencia, legibilidad y mantenibilidad del código a través de múltiples tecnologías y componentes.

**Audiencia**: Desarrolladores, Code Reviewers, DevOps, Arquitectos  
**Última Actualización**: 2025-11-18  
**Estado**: Activo

### Alcance del Documento

Este documento cubre estándares para:
- **Componentes Guidewire** (PolicyCenter, BillingCenter, ClaimCenter) - Lenguaje Gosu
- **Microservicios** (MicroIntegradorVidaGrupo, MicroIntegradorReportesVidaGrupo) - Java con Apache Camel
- **Componentes auxiliares** - Según tecnología específica

---

## 🚨 **ESTÁNDARES OBLIGATORIOS**

### Estándares Generales para Todo el Proyecto

#### 1. Idioma del Código

```gosu
// ✅ CORRECTO - Código en inglés
function calculateTotalPremium(policyPeriod : PolicyPeriod) : BigDecimal {
  var total = 0bd
  return total
}

// ❌ INCORRECTO - Código en español
function calcularPrimaTotal(periodo : PolicyPeriod) : BigDecimal {
  var total = 0bd
  return total
}
```

**REGLA OBLIGATORIA**: 
- Todo el código (variables, funciones, clases) debe estar en **inglés**
- Los comentarios y documentación deben estar en **español**

#### 2. Comentarios de Código

```gosu
// ❌ INCORRECTO - Código comentado
// var oldVariable = calculateOldWay()
// return processData(oldVariable)

// ✅ CORRECTO - Sin código comentado, usar control de versiones
var currentVariable = calculateNewWay()
return processData(currentVariable)
```

**REGLA OBLIGATORIA**: 
- **NO dejar código comentado** - Eliminar código no utilizado
- Si se necesita el código antiguo, recuperarlo del sistema de control de versiones
- **NO usar comentarios para documentar código** - El código debe ser autoexplicativo
- Comentarios solo en casos extremadamente complejos (muy raro, debe evitarse)

#### 3. Formato de Archivos

```text
✅ CORRECTO
package com.sura.pc.account

class MyClass {
  // código
}
// FIN DEL ARCHIVO (sin líneas vacías)

❌ INCORRECTO
package com.sura.pc.account

                        ← línea vacía al inicio
class MyClass {
  // código
}
                        ← líneas vacías al final
                        
```

**REGLAS OBLIGATORIAS**:
- **No dejar líneas vacías al inicio** del archivo (antes del package)
- **No dejar líneas vacías al final** del archivo
- Agrupar líneas de código de manera lógica (inicialización, operaciones, retorno)

#### 4. Strings Literales

```gosu
// ❌ INCORRECTO - Strings literales en el código
if (status == "ACTIVE") {
  logger.error("Error procesando póliza")
  throw new Exception("Estado inválido")
}

// ✅ CORRECTO - Usar constantes
if (status == PolicyConstants.STATUS_ACTIVE) {
  logger.error(LogMessages.POLICY_PROCESSING_ERROR)
  throw new Exception(ErrorMessages.INVALID_STATUS)
}

// ✅ CORRECTO (Guidewire) - DisplayKeys para mensajes al usuario
util.Logger.logError(displaykey.Sura.PolicyError.ProcessingFailed)
```

**REGLA OBLIGATORIA**: 
- **NO usar strings literales** en el código
- Declarar todos los strings en **clases de constantes** apropiadas
- En Guidewire: usar **DisplayKeys** (con prefijo `Sura.`) para mensajes al usuario
- Excepciones: strings en tests unitarios (arrange/assert)

---

## ��️ **ESTÁNDARES PARA GUIDEWIRE (GOSU)**

### 1. Nomenclatura

#### Paquetes

```gosu
// ✅ CORRECTO
package sura.pc.webservice.legalreport
package sura.bc.batch.accounting
package sura.cc.plugin.claims

// ❌ INCORRECTO
package com.company.PC.WebService
package SuraBC.Batch
```

**Estructura**: `<compañía>.<código_aplicación>.<mecanismo>.<área_funcional>`

- **compañía**: `sura`
- **código_aplicación**: `pc` (PolicyCenter), `bc` (BillingCenter), `cc` (ClaimCenter), `ab` (ContactManager)
- **mecanismo**: `batch`, `messaging`, `plugin`, `startable`, `webservice`, `classes`
- **área_funcional**: nombre diciente (ej: `util`, `accounting`, `claims`)

#### Clases

```gosu
// ✅ CORRECTO - Notación Pascal
class ResourceURL
class ClientEnhancement_Ext
class AddressUtil

// ❌ INCORRECTO
class resourceURL
class client_enhancement
class addressutil
```

**REGLAS**:
- Notación **PascalCase**
- Palabras completas, evitar acrónimos
- Clases nuevas fuera del paquete `sura` deben terminar en `*_Ext`
- Enhancements nuevos deben terminar en `_Ext`

#### TypeKeys y Entidades

```gosu
// ✅ CORRECTO - TypeKeys
typekey.ContactNoteType.TC_GENERAL
typekey.PolicyStatus.TC_BOUND

// ✅ CORRECTO - Entidades
entity.ABContact
entity.PolicyPeriod
entity.Claim
```

#### Interfaces e Implementaciones

```gosu
// ✅ CORRECTO
interface IHystrixExecution {
  function execute()
}

class HystrixExecutionImpl implements IHystrixExecution {
  override function execute() { }
}

// ❌ INCORRECTO
interface HystrixExecution
class HystrixExecution
```

**REGLAS**:
- Interfaces con prefijo `I`
- Implementaciones con sufijo `Impl`

#### Métodos

```gosu
// ✅ CORRECTO - Verbos en infinitivo, camelCase
function obtainCache() : Cache
function calculateDays(startDate : Date, endDate : Date) : int
function processPayment(amount : BigDecimal) : boolean

// ❌ INCORRECTO
function ObtainCache()
function calcDays()
function payment()
```

#### Propiedades

```gosu
// ✅ CORRECTO
property get HasEvaluations() : boolean
property set AssignArea(width : int)

// ❌ INCORRECTO  
property get hasEvaluations() : boolean
property set assignArea(width : int)
```

**REGLA**: Notación **PascalCase** para propiedades

#### Constantes

```gosu
// ✅ CORRECTO
static final var MINIMUM_WIDTH = 100
static final var MAX_RETRY_ATTEMPTS = 3
static final var DEFAULT_TIMEOUT_SECONDS = 30

// ❌ INCORRECTO
static final var minimumWidth = 100
static final var MaxRetry = 3
```

**REGLA**: Todo en **MAYÚSCULAS** separando palabras con guion bajo (`_`)

#### Variables

```gosu
// ✅ CORRECTO
var firstName : String
var anActivity : Activity
var amount = 125.00
private var _firstName : String as FirstName  // Variable de clase

// ❌ INCORRECTO
var FirstName : String
var an_activity : Activity
public var firstName : String  // Variables públicas no permitidas
```

**REGLAS**:
- Notación **camelCase**
- Variables de clase privadas inician con `_`
- Usar modificador `private`, evitar variables públicas
- Omitir especificación de tipos cuando sea posible (inferencia)
- No usar `protected` excepto para herencia

#### DisplayKeys

```gosu
// ✅ CORRECTO
Sura.SomeName.Label=Name
Sura.PolicyValidation.Error=Error en validación de póliza
Sura.ClaimProcess.Success=Reclamación procesada exitosamente

// ❌ INCORRECTO (conflicto potencial con Guidewire)
SomeName.Label=Name
PolicyValidation.Error=Error
```

**REGLA**: Prefijo `Sura.` en todos los DisplayKeys para evitar conflictos

### 2. Estructura de Código

#### Organización de Imports

```gosu
// ✅ CORRECTO - Imports organizados
uses gw.api.database.Query
uses gw.api.name.ContactNameOwner
uses java.util.Set
uses entity.PolicyPeriod
uses typekey.PolicyStatus

// ❌ INCORRECTO - Imports desorganizados
uses java.util.Set
uses entity.PolicyPeriod
uses gw.api.database.Query
```

#### Indentación

```gosu
// ✅ CORRECTO - 2 espacios
class MyClass {
  function myMethod() {
    if (condition) {
      doSomething()
    }
  }
}

// ❌ INCORRECTO - 4 espacios o tabs
class MyClass {
    function myMethod() {
        if (condition) {
            doSomething()
        }
    }
}
```

**REGLA**: Siempre **2 espacios**, nunca tabs

#### Uso de Llaves

```gosu
// ✅ CORRECTO - Siempre usar llaves, misma línea
if (condition) {
  doSomething()
}

while (hasMore) {
  process()
}

// ❌ INCORRECTO - Sin llaves o en línea diferente
if (condition)
  doSomething()
  
if (condition)
{
  doSomething()
}
```

**REGLAS**:
- Siempre usar llaves `{ }` aunque sea una sola línea
- Llave de apertura `{` en la misma línea
- Llave de cierre `}` en línea aparte

#### Omisiones de Sintaxis

```gosu
// ✅ CORRECTO - Sin punto y coma, sin void
function calculateTotal() : BigDecimal {
  return total
}

function processData() {
  // no retorna nada
}

// ❌ INCORRECTO
function calculateTotal() : BigDecimal {
  return total;  // punto y coma innecesario
}

void function processData() {  // void innecesario
}
```

**REGLAS**:
- **No usar punto y coma** `;` al final de sentencias
- **No usar `void`** en funciones que no retornan

### 3. Manejo de Errores

```gosu
// ✅ CORRECTO
try {
  var result = apiCall()
  return result
} catch (e : Exception) {
  logger.error("Error en apiCall:", e)
  throw new ApiError("Falló la llamada a la API", e)
}

// ❌ INCORRECTO - Sin manejo de errores
var result = apiCall()
return result
```

**REGLAS OBLIGATORIAS**:
- Siempre usar `try-catch` para código que puede fallar
- No mostrar errores inapropiados al usuario
- Loggear excepciones con contexto

### 4. Validación de Nulos

```gosu
// ✅ CORRECTO - Comparar constante primero
if (PolicyConstants.STATUS_ACTIVE.equalsIgnoreCase(policyStatus)) {
  processPolicy()
}

if (policyPeriod != null) {
  print(policyPeriod.DisplayName)
}

// ❌ INCORRECTO - Variable primero (riesgo NPE)
if (policyStatus.equals("ACTIVE")) {
  processPolicy()
}

print(policyPeriod.DisplayName)  // Sin validación de null
```

**REGLAS OBLIGATORIAS**:
- Comparar sobre **constantes** para evitar `NullPointerException`
- Usar `equalsIgnoreCase` cuando sea posible
- Validar nulos antes de acceder a propiedades
- **NO usar strings literales** - usar constantes

### 5. Uso de Paréntesis en Expresiones

```gosu
// ✅ CORRECTO
premium = (rate + limit) * (10.5 + deductible) / (autoGrade - 15)

// ❌ INCORRECTO - Difícil de leer, resultados inesperados
premium = rate + limit * 10.5 + deductible / autoGrade - 15
```

### 6. Operador TypeIs para Downcasting

```gosu
// ✅ CORRECTO
var x : Object = "nice"
var strlen = 0
if (x typeis String) {
  strlen = x.length  // Conversión automática
}

// ❌ INCORRECTO - Conversión redundante
if (x typeis String) {
  strlen = (x as String).length
}
```

### 7. Líneas en Blanco

```gosu
// ✅ CORRECTO
class MyClass {
  var field1 : String
  var field2 : int
                          // 2 líneas en blanco entre definiciones de clases
                          
  function method1() {
    var localVar = 10
                          // 1 línea en blanco después de variables locales
    doSomething()
    doAnotherThing()
  }
                          // 2 líneas en blanco entre métodos (excepto el último)
                          
  function method2() {
    // código
  }
}
```

**REGLAS**:
- **2 líneas en blanco**: Entre clases/interfaces, entre métodos (excepto el último)
- **1 línea en blanco**: Después de variables locales, entre bloques de código

### 8. Variables y Propiedades Públicas

```gosu
// ✅ CORRECTO - Propiedad pública con variable privada
private var _firstName : String as FirstName

// ❌ INCORRECTO - Variable pública
public var FirstName : String
```

**REGLA**: Usar **propiedades públicas** respaldadas por **variables privadas**, no variables públicas

### 10. Variables Estáticas Mutables

```gosu
// ❌ INCORRECTO - Variable estática mutable
class VinIdentifier {
  static var myVector = new Vector()  // Compartida por todas las sesiones JVM
  
  static function myFunction() {
    myVector.add("nuevos datos")  // Afecta todas las sesiones
  }
}

// ✅ CORRECTO - Variable de instancia
class VinIdentifier {
  var myVector = new Vector()
  
  function myFunction() {
    myVector.add("nuevos datos")
  }
}
```

**REGLA**: **NO usar variables estáticas mutables** - son compartidas por toda la JVM

### 10. Optimización de Rendimiento

**NOTA**: En todos los ejemplos, evitar strings literales y usar constantes.

#### Evitar Llamadas Repetidas a Métodos

```gosu
// ❌ INCORRECTO
if (policy.expensiveMethod() == "first possibility") {
  // do something
} else if (policy.expensiveMethod() == "second possibility") {
  // do something else
}

// ✅ CORRECTO
var expensiveValue = policy.expensiveMethod()
if (expensiveValue == "first possibility") {
  // do something
} else if (expensiveValue == "second possibility") {
  // do something else
}
```

#### Retirar Expresiones de Ciclos

```gosu
// ❌ INCORRECTO
var period : PolicyPeriod
for (x in 5) {
  if (x == 3 and period.Active) {  // Evalúa period.Active repetidamente
    print("x == 3 on active period")
  }
}

// ✅ CORRECTO
var period : PolicyPeriod
if (period.Active) {  // Evalúa una sola vez
  for (x in 5) {
    if (x == 3) {
      print("x == 3 on active period")
    }
  }
}
```

#### Interrumpir Ciclos Tempranamente

```gosu
// ✅ CORRECTO
function foundThree() : boolean {
  for (x in 5) {
    if (x == 3) {
      return true  // Retornar inmediatamente
    }
  }
  return false
}
```

#### Pulling Up de Llamadas Costosas

```gosu
// ❌ INCORRECTO - Llamada costosa en múltiples métodos
function computeSomething() {
  computeA()
  computeB()
  computeC()
}

function computeA() {
  var expensiveResult = expensiveCall()  // Llamada 1
  // ...
}

function computeB() {
  var expensiveResult = expensiveCall()  // Llamada 2
  // ...
}

// ✅ CORRECTO - Llamar una vez y pasar resultado
function computeSomething() {
  var expensiveResult = expensiveCall()  // Llamada única
  computeA(expensiveResult)
  computeB(expensiveResult)
  computeC(expensiveResult)
}
```

### 11. Query Builder y Base de Datos

#### Uso de Activity.Pattern.Code

```gosu
// ✅ CORRECTO - Evita lectura a BD
if (activity.ActivityPattern.Code == "MyActivityPatternCode") {
  // ...
}

// ❌ INCORRECTO - Requiere lectura a BD
if (activity.ActivityPattern == ActivityPattern("cc:12345")) {
  // ...
}
```

#### Uso de Count vs Empty

```gosu
// ✅ CORRECTO - Para saber si hay resultados
var result = policyPeriodQuery.select()
if (result.Empty) {
  print("Nothing found.")
} else {
  print("Got some!")
}

// ✅ CORRECTO - Para obtener cantidad
var result = policyPeriodQuery.select()
print("Number of policy periods: " + result.Count)
```

#### Filtrar con Query Builder

```gosu
// ❌ INCORRECTO - Filtrar en código Gosu
var claimQuery = Query.make(Claim)
for (claim in claimQuery.select()) {
  if (claim.Policy == targetPolicy) {
    // procesar
  }
}

// ✅ CORRECTO - Filtrar en base de datos
var claimQuery = Query.make(Claim)
claimQuery.compare(Claim#Policy, Equals, targetPolicy)
for (claim in claimQuery.select()) {
  // procesar
}
```

#### Estructura de Query Builder

```gosu
var query = Query.make(Company)
var tableAddress = query.join(Company#PrimaryAddress)
tableAddress.compare(Address#City, Equals, "Indianapolis")

var result = query.select()

// Validar si está vacío
if (result.Empty) {
  // manejar caso sin resultados
}

// Limitar resultados
result.getCountLimitedBy(10)  // máximo 9 elementos (+1)

// Ordenar
result.orderBy(\ row -> row.PostalCode)
result.orderByDescending(\ row -> row.CreatedDate)
```

#### Índices en Entidades

**REGLA CRÍTICA**: Entidades `Retirable` o `Versionable` tienen columna `Retired`.  
Todos los índices **DEBEN incluir la columna `Retired`**.

### 12. Concatenación de Strings (Rendimiento)

| Técnica | Rendimiento | Ejemplo |
|---------|-------------|---------|
| Concatenation (+) on literals | Muy Rápida | `var s = "Test" + " Test"` |
| Gosu string template | Rápida | `var s = "Test ${anotherString}"` |
| Java string builder | Intermedio | `new StringBuilder().append("Test")` |
| Java string buffer | Moderado | `new StringBuffer().append("Test")` |
| Concatenation (+) on variable | Lento | `s = s + " Test"` |
| Concatenation (+) two variables | Muy lento | `s = s + anotherString` |

**RECOMENDACIÓN**: Usar **templates de Gosu** o **literals** cuando sea posible

**NOTA**: Evitar strings literales - usar constantes para strings reutilizables

### 13. GX Model para Integraciones

```gosu
// ✅ CORRECTO - Usar GX Model para XML
// 1. Crear DTO
// 2. Generar GX Model desde DTO
// 3. Evitar usar entidades directamente

// Para JSON usar Gson
var gson = new Gson()
var json = gson.toJson(myObject)
```

**REGLA**:
- **XML**: Usar GX Model con DTO (no entidades directamente)
- **JSON**: Usar librería Gson

### 14. Plugins

```gosu
// ❌ INCORRECTO - Instancia local (bajo rendimiento)
function processData() {
  var plugin = new com.acme.pc.webservices.plugin.VehiclePlugin()
  plugin.execute()
}

// ✅ CORRECTO - Instancia estática (alto rendimiento)
static var vehiclePlugin = new com.acme.pc.webservices.plugin.VehiclePlugin()

function processData() {
  vehiclePlugin.execute()
}

// ✅ MEJOR - Application scope (rendimiento óptimo)
var plugin = new com.acme.pc.webservices.plugin.VehiclePlugin()
gw.api.web.Scopes.getApplication().put("VehiclePlugin", plugin)
```

**NOTA**: Analizar costo de instancia vs. costo de memoria (OutOfMemoryError)

### 15. Integraciones Externas

**PREREQUISITO**: Completar curso de Guidewire Integrations antes de implementar integraciones.

#### Integraciones Síncronas (Transaccionales)

**Escenario**: Necesito respuesta en línea del servicio externo para tomar decisiones.

```gosu
// ✅ CORRECTO - Implementar en una fachada
class ExternalServiceFacade {
  private static final String SERVICE_NAME = "external-service"
  private static final String CACHE_KEY_PREFIX = "ext_service_"
  
  /**
   * Consume servicio externo a través de fachada
   * @param request - DTO con parámetros requeridos
   * @returns DTO con respuesta del servicio
   * @throws BusinessException - Errores de negocio del servicio
   * @throws TechnicalException - Errores técnicos de comunicación
   */
  function callExternalService(request : ExternalServiceRequestDTO) : ExternalServiceResponseDTO {
    try {
      // 1. Verificar caché (si aplica)
      var cachedResponse = checkCache(request)
      if (cachedResponse != null) {
        return cachedResponse
      }
      
      // 2. Consumir a través del localizador de servicios (Registry 8)
      var serviceLocator = ServiceLocator.getInstance()
      var response = serviceLocator.callService(SERVICE_NAME, request)
      
      // 3. Guardar en caché (si aplica)
      saveToCache(request, response)
      
      return response
      
    } catch (e : BusinessException) {
      // Propagar excepción de negocio tal cual
      throw e
    } catch (e : TechnicalException) {
      // Propagar excepción técnica tal cual
      throw e
    }
  }
  
  // Implementar circuit breaker con Hystrix
  @HystrixCommand(fallbackMethod = "callExternalServiceFallback")
  function callExternalServiceWithCircuitBreaker(request : ExternalServiceRequestDTO) : ExternalServiceResponseDTO {
    return callExternalService(request)
  }
  
  function callExternalServiceFallback(request : ExternalServiceRequestDTO) : ExternalServiceResponseDTO {
    // Manejo de failover
    logger.error(LogMessages.CIRCUIT_BREAKER_ACTIVATED)
    throw new ServiceUnavailableException(ErrorMessages.SERVICE_TEMPORARILY_UNAVAILABLE)
  }
}
```

**REGLAS OBLIGATORIAS para Integraciones Síncronas**:

1. **Implementar en Fachada**
   - Recibir objeto DTO con atributos requeridos
   - NO exponer detalles del servicio externo

2. **Manejo de Excepciones**
   - Capturar `BusinessException` y propagar tal cual
   - Capturar `TechnicalException` y propagar tal cual
   - NO silenciar excepciones

3. **WSDL Import**
   - Modificar namespace del WSDL antes de importar
   - Evitar conflictos en la importación

4. **Localizador de Servicios**
   - SIEMPRE usar `ServiceLocator` (Registry 8 de Sura)
   - NO hacer llamadas directas a URLs hardcodeadas

5. **Caché (si aplica)**
   - Analizar si se debe guardar información en caché
   - Usar librería **Guava** (incluida en Guidewire)
   - Evitar N llamadas al mismo servicio

6. **Autenticación con Seus4**
   - Si el servicio requiere autenticación y está en Seus4
   - Usar usuario nombrado del core

7. **Configuración en WService_Ext**
   - Registrar parámetros de localización
   - Registrar nombramiento del servicio
   - Registrar seguridad (si aplica)

8. **Circuit Breaker (Hystrix)**
   - Implementar para manejo de failover
   - Usar en caso de fallos o lentitudes
   - Definir método fallback apropiado
   - Ver ejemplo: `FinanceFacade.getPlan()` en PolicyCenter

9. **Pruebas de Integración**
   - Implementar prueba SOAPUI para la fachada
   - Usar `com.sura.suite.gw.tester.FacadeTesterAPI`
   - Incluir en Bitbucket para pipeline CI/CD
   - Especificar: clase, método, tipo y valor de parámetros

10. **Implementación en Suite (si aplica)**
    - Si el servicio es de interés general para el CORE
    - Si puede ser consumido por múltiples módulos GW
    - Implementar en la suite común

**Ejemplo de Configuración WService_Ext**:

```gosu
// WService_Ext.gs
class WService_Ext {
  // Parámetros de localización
  static final var EXTERNAL_SERVICE_URL = ScriptParameters.ExternalServiceURL
  
  // Nombramiento
  static final var EXTERNAL_SERVICE_NAME = "ExternalService"
  
  // Seguridad (si aplica)
  static final var EXTERNAL_SERVICE_USER = ScriptParameters.ExternalServiceUser
  static final var EXTERNAL_SERVICE_PASSWORD = ScriptParameters.ExternalServicePassword
}
```

**Ejemplo de Uso de Caché (Guava)**:

```gosu
uses com.google.common.cache.Cache
uses com.google.common.cache.CacheBuilder
uses java.util.concurrent.TimeUnit

class CachedExternalServiceFacade {
  static var _cache : Cache<String, ExternalServiceResponseDTO> = 
    CacheBuilder.newBuilder()
      .maximumSize(1000)
      .expireAfterWrite(10, TimeUnit.MINUTES)
      .build()
  
  private function checkCache(request : ExternalServiceRequestDTO) : ExternalServiceResponseDTO {
    var cacheKey = buildCacheKey(request)
    return _cache.getIfPresent(cacheKey)
  }
  
  private function saveToCache(request : ExternalServiceRequestDTO, response : ExternalServiceResponseDTO) {
    var cacheKey = buildCacheKey(request)
    _cache.put(cacheKey, response)
  }
  
  private function buildCacheKey(request : ExternalServiceRequestDTO) : String {
    return CacheKeyConstants.EXTERNAL_SERVICE_PREFIX + request.RequestId
  }
}
```

#### Integraciones Asíncronas (Desconectadas)

**Escenario**: Consumo desconectado de la transacción de negocio, puede ser asíncrono.

```gosu
// ✅ CORRECTO - Usar RabbitMQ como message broker
class AsyncIntegrationPlugin extends MessageTransportPlugin {
  
  override function send(message : Message, transformedPayload : String) {
    // 1. Preparar mensaje
    var rabbitMessage = prepareRabbitMessage(message, transformedPayload)
    
    // 2. Publicar en RabbitMQ
    var rabbitMQPublisher = RabbitMQPublisher.getInstance()
    rabbitMQPublisher.publish(rabbitMessage, QueueConstants.EXTERNAL_INTEGRATION_QUEUE)
    
    // 3. Loggear envío
    logger.info(LogMessages.MESSAGE_SENT_TO_QUEUE, 
                {message.PublicID, QueueConstants.EXTERNAL_INTEGRATION_QUEUE})
  }
  
  private function prepareRabbitMessage(message : Message, payload : String) : RabbitMessage {
    return new RabbitMessage() {
      :MessageId = message.PublicID,
      :Payload = payload,
      :Timestamp = Date.CurrentDate,
      :CorrelationId = generateCorrelationId()
    }
  }
}
```

**REGLAS OBLIGATORIAS para Integraciones Asíncronas**:

1. **Message Broker**
   - SIEMPRE preferir **RabbitMQ** como message broker
   - Excepción: si el destino es un web service

2. **Desconexión de Transacción**
   - Usar **Transport Plugin** para desconectarse de la transacción
   - NO bloquear la transacción de negocio

3. **Configuración de Colas**
   - Definir nombres de colas en constantes
   - Configurar en ScriptParameters
   - Documentar propósito de cada cola

4. **Manejo de Errores**
   - Implementar dead letter queue (DLQ)
   - Loggear envíos y errores
   - Implementar retry policy

5. **Documentación**
   - Remitirse a documentación de GW: Integration Guide - Plugins, Messaging
   - Documentar flujo completo de mensajería
   - Incluir diagramas de secuencia

**Ejemplo de Configuración de Colas**:

```gosu
// QueueConstants.gs
class QueueConstants {
  static final var EXTERNAL_INTEGRATION_QUEUE = "sura.integration.external.main"
  static final var EXTERNAL_INTEGRATION_DLQ = "sura.integration.external.dlq"
  static final var EXTERNAL_INTEGRATION_RETRY_QUEUE = "sura.integration.external.retry"
}
```

#### Arquitectura de Integración Síncrona

```text
┌─────────────────┐
│   Guidewire     │
│   (Fachada)     │
└────────┬────────┘
         │
         ├──> 1. Verificar Caché (Guava)
         │         └─> Si existe, retornar
         │
         ├──> 2. Localizador de Servicios (Registry 8)
         │         └─> Obtener endpoint del servicio
         │
         ├──> 3. Circuit Breaker (Hystrix)
         │         ├─> Llamada al servicio
         │         └─> Fallback si falla
         │
         ├──> 4. Manejo de Excepciones
         │         ├─> BusinessException → Propagar
         │         └─> TechnicalException → Propagar
         │
         └──> 5. Guardar en Caché (si aplica)

┌─────────────────────────────────────────────┐
│   Servicio Externo                          │
│   - Autenticado con Seus4 (si aplica)      │
│   - Registrado en Registry 8                │
└─────────────────────────────────────────────┘
```

#### Arquitectura de Integración Asíncrona

```text
┌─────────────────┐
│   Guidewire     │
│ (Transport      │
│   Plugin)       │
└────────┬────────┘
         │
         ├──> 1. Preparar Mensaje
         │
         ├──> 2. Publicar en RabbitMQ
         │         ├─> Main Queue
         │         ├─> Retry Queue (si falla)
         │         └─> DLQ (si excede reintentos)
         │
         └──> 3. Desconectar de Transacción

┌─────────────────────────────────────────────┐
│   RabbitMQ                                   │
│   - Exchange: sura.integration               │
│   - Queue: external.main                     │
│   - DLQ: external.dlq                        │
└──────────────┬──────────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────────┐
│   Consumidor / Servicio Externo             │
└─────────────────────────────────────────────┘
```

#### Checklist de Integraciones

**Antes de Implementar**:
- [ ] Curso de Guidewire Integrations completado
- [ ] Tipo de integración identificado (síncrona/asíncrona)
- [ ] Servicio registrado en Registry 8 (si aplica)
- [ ] Requerimientos de caché analizados
- [ ] Requerimientos de seguridad definidos

**Durante Implementación Síncrona**:
- [ ] Fachada implementada con DTO
- [ ] Manejo de excepciones (Business y Technical)
- [ ] WSDL modificado (namespace)
- [ ] Localizador de servicios implementado
- [ ] Caché implementado con Guava (si aplica)
- [ ] Autenticación Seus4 configurada (si aplica)
- [ ] WService_Ext configurado
- [ ] Circuit breaker Hystrix implementado (si aplica)
- [ ] Prueba SOAPUI creada
- [ ] FacadeTesterAPI configurado
- [ ] Pruebas incluidas en Bitbucket

**Durante Implementación Asíncrona**:
- [ ] Transport Plugin implementado
- [ ] RabbitMQ configurado
- [ ] Colas definidas (main, retry, DLQ)
- [ ] Manejo de errores implementado
- [ ] Logs de auditoría implementados
- [ ] Retry policy definida
- [ ] Documentación de flujo completa

---

## 🧪 **ESTÁNDARES DE PRUEBAS UNITARIAS GUIDEWIRE**

### 1. Nomenclatura de Tests

```gosu
// ✅ CORRECTO - Inglés, inicia con "test", nombre descriptivo
function testRatingMustReturnZeroWhenInsuranceValueIsZero() { }
function testValidateClaimNumberWhenTheNumberIsNullThenReturnEmptyMessage() { }

// ❌ INCORRECTO
function prueba1() { }
function calculateTest() { }
function shouldReturnZero() { }  // No inicia con "test"
```

**REGLAS OBLIGATORIAS**:
- Nombre en **inglés**
- Iniciar con **`test`**
- Nombre **descriptivo** del escenario

### 2. Estructura de Tests - Patrón AAA

```gosu
@RunLevel(NONE)
class PaymentProcessorTest extends EESuraTestBase {
  
  var _processor : PaymentProcessor
  
  override function beforeMethod() {
    _processor = Mockito.spy(new PaymentProcessor())
  }
  
  function testProcessPaymentMustReturnSuccessWhenAmountIsValid() {
    // Arrange - Preparación
    var amount = 100bd
    var currency = "COP"
    var expectedResult = true
    
    // Act - Ejecución
    var result = _processor.processPayment(amount, currency)
    
    // Assert - Verificación
    assertThat(result).isEqualTo(expectedResult)
  }
}
```

**REGLAS**:
- **Arrange**: Inicializar objetos, mocks, datos
- **Act**: Invocar función a testear
- **Assert**: Validar resultado
- Separar secciones con línea en blanco
- Opcionalmente comentar secciones

### 3. Principios FIRST

- **Fast**: Tests deben ser rápidos
- **Independent**: Ejecutables en orden aleatorio
- **Repeatable**: Mismo resultado siempre
- **Self-validating**: No depender de recursos externos
- **Timely**: Ejecutar en momentos oportunos

### 4. Clase Base de Tests

```gosu
// ✅ CORRECTO
@RunLevel(NONE)
class MyClassTest extends EESuraTestBase {
  
  override function beforeMethod() {
    super.beforeMethod()
    // inicialización
  }
  
  override function beforeClass() {
    super.beforeClass()  // OBLIGATORIO
    // configuración
  }
}
```

**REGLAS OBLIGATORIAS**:
- Extender de **`EESuraTestBase`**
- Usar **`@RunLevel(NONE)`** (tests más rápidos)
- Llamar **`super.beforeClass()`** si se sobreescribe

### 5. Instancia de Clase a Probar

```gosu
@RunLevel(NONE)
class CalculatorTest extends EESuraTestBase {
  
  private var _calculator : Calculator
  
  override function beforeMethod() {
    var calculator = new Calculator()
    _calculator = Mockito.spy(calculator)  // OBLIGATORIO: usar spy
  }
}
```

**REGLA**: Usar **`Mockito.spy()`** para evitar problemas de ClassLoader

### 6. Soporte de Entidades

```gosu
// ✅ CORRECTO - Crear entidades con "new"
var policyPeriod = new PolicyPeriod()
var claim = new Claim()

Mockito.when(policyPeriod.Status).thenReturn(TC_BOUND)
Mockito.when(claim.ClaimNumber).thenReturn("CL-12345")

// ✅ CORRECTO - Entidades EffDated requieren PolicyPeriod
var policyPeriod = new PolicyPeriod()
var coverage = new PACoverage(policyPeriod)

// ✅ CORRECTO - Entidades abstractas opción 1: usar subtipo
var addlInterest = new PAVehicleAddlInterest(policyPeriod)

// ✅ CORRECTO - Entidades abstractas opción 2: usar bundle
var addlInterest = _bundle.newBeanInstance(AddlInterestDetail) as AddlInterestDetail

// ❌ INCORRECTO
var claim = Mockito.mock(Claim)  // NO hacer mock de entidades
```

### 7. Mock de Enhancements

```gosu
function testCalculateTotalMustUseEnhancementProperties() {
  // Arrange
  var riWorksheetItem = new RIWorksheetItem_Ext()
  
  // Mock de properties de enhancement
  mockStatic(sura.pc.reinsurance.RIWorksheetItemEnhancement,
    {RIWorksheetItemEnhancement#NetPremium,
     RIWorksheetItemEnhancement#TotalExposedValue})
  
  var netPremium = new MonetaryAmount()
  Mockito.when(riWorksheetItem.NetPremium).thenReturn(netPremium)
  
  // Act
  var result = _engine.calculateTotal(riWorksheetItem)
  
  // Assert
  assertThat(result).isNotNull()
  
  // Verify
  verifyStaticPropertyGet(RIWorksheetItemEnhancement#NetPremium, 
                         Mockito.times(1), 
                         riWorksheetItem)
}
```

**REGLAS**:
- Usar **`mockStatic`** para enhancements
- Usar **`verifyStaticPropertyGet`** para properties
- Usar **`verifyStaticPropertySet`** para setters
- Usar **`verifyStatic`** para funciones

### 8. Mock de Permisos

```gosu
function testCompleteAuditMustSucceedWhenUserHasPermission() {
  // Arrange
  mockPerm(perm.System, "completeaudit", true)
  Mockito.doNothing().when(_audit).completeAuditOnExternalSystem()
  
  // Act
  _audit.completeAudit()
  
  // Assert
  Mockito.verify(_audit, Mockito.times(1)).completeAuditOnExternalSystem()
}
```

### 9. Mock de Script Parameters

```gosu
function testBuildControlMustUseApplicationCode() {
  // Arrange
  setScriptParameter("ApplicationCode", "02")
  
  // Act
  var controlDto = _viaUtil.buildControlInDto(_policyPeriod, "EXPEDICION")
  
  // Assert
  assertThat(controlDto.ViaTraficoDTO.CodigoAplicacion).isEqualTo("02")
}
```

### 10. Mock de Plugins

```gosu
function testGetTIVMustCallReinsurancePlugin() {
  // Arrange
  var plugin = Mockito.mock(IReinsuranceConfigPlugin)
  Mockito.when(_pluginConfig.getPlugin(IReinsuranceConfigPlugin)).thenReturn(plugin)
  
  // Act
  var result = TIVCalculator_Ext.getTIVElement(strings, amount, coverage, participation)
  
  // Assert
  Mockito.verify(plugin).getReinsuranceCurrency(coverageList)
}
```

### 11. Tests de Clases Abstractas

```gosu
@RunLevel(NONE)
class MyAbstractClassTest extends EESuraTestBase {
  
  function testPlusMustReturnAPlusB() {
    // Arrange
    var aClass = Mockito.mock(MyAbstractClass)
    Mockito.doCallRealMethod().when(aClass).plus(1, 1)
    
    // Act
    var result = aClass.plus(1, 1)
    
    // Assert
    assertTrue(result == 2)
  }
}
```

### 12. Restricciones de Tests

**NO SE HACEN TESTS PARA**:
- Eventos (Events)
- Reglas Gosu (GosuRules)

**SOLUCIÓN**: Extraer lógica a función en clase/enhancement y testear esa función

### 13. Errores Comunes a Evitar

❌ **NO HACER**:
- `Mockito.mock(Claim)` - No hacer mock de entidades
- `Mockito.verify(...)` directamente - Usar métodos específicos
- `mockStatic(entity.Claim)` - No usar mockStatic con entidades
- `verifyStatic(...)` sin instancia en enhancements
- Usar strings literales en código de producción (permitido en tests)

### 14. Generador de Pruebas Unitarias

```text
Ubicación: scripts/UnitTestGenerator.gsp

Parámetros:
- classToGenerate: Clase a testear
- methods: Lista de métodos (vacío = todos)
- debug: Imprimir análisis (default: false)
- debugAAA: Separar secciones AAA (default: true)
```

**NOTA**: El generador facilita pero **NO reemplaza** la revisión manual

### 15. Dynamic Code Evolution (DCEVM)

```bash
# Verificar instalación
java -version

# Si aparece DCEVM, configurar JVM alternativa
--XXaltjvm=dcevm
```

**ADVERTENCIA**: DCEVM afecta rendimiento de tests unitarios. Instalar solo como `altjvm`.

---

## ☕ **ESTÁNDARES PARA MICROSERVICIOS JAVA**

### 1. Nomenclatura

#### Paquetes

```java
// ✅ CORRECTO
package com.sura.mi.reportes.vidagrupo.domain;
package com.sura.mi.reportes.vidagrupo.infrastructure.common;
package com.sura.mi.vidagrupo.application.service;

// ❌ INCORRECTO
package com.sura.mi.Reportes;
package mi.sura.vidagrupo;
```

**Estructura**: `com.sura.<componente>.<módulo>.<capa>`

#### Clases

```java
// ✅ CORRECTO
public class MicrointegratorMain
public class ApplicationServiceRegistry
public class OracleDataSourceProvider

// ❌ INCORRECTO
public class microIntegratorMain
public class application_service_registry
```

**REGLA**: PascalCase, nombres descriptivos

#### Métodos y Variables

```java
// ✅ CORRECTO
private DataSource configureDatabase(CamelContext camelContext) { }
private void loadProperties(Main main) { }
var serviceRegistry = new ApplicationServiceRegistry();

// ❌ INCORRECTO
private DataSource ConfigureDatabase(CamelContext camelContext) { }
private void load_properties(Main main) { }
var service_registry = new ApplicationServiceRegistry();
```

**REGLA**: camelCase

#### Constantes

```java
// ✅ CORRECTO
public static final String ENVIRONMENTS_PATH = "/config/";
public static final int MAX_RETRY_ATTEMPTS = 3;
public static final String ERROR_MESSAGE = "Error processing request";

// ❌ INCORRECTO
public static final String environmentsPath = "/config/";
public static final int maxRetry = 3;

// ❌ INCORRECTO - String literal en código
logger.error("Error processing request");

// ✅ CORRECTO - Usar constante
logger.error(ERROR_MESSAGE);
```

**REGLA CRÍTICA**: **TODOS los strings deben ser constantes** - NO usar strings literales en el código

### 2. Organización de Imports

```java
// ✅ CORRECTO
// 1. Imports estáticos de constantes
import static com.sura.mi.reportes.vidagrupo.domain.common.constant.ApplicationConstant.ENVIRONMENTS_PATH;
import static com.sura.mi.reportes.vidagrupo.domain.common.constant.DatabaseConstant.ORACLE_DATA_SOURCE;

// 2. Imports de Java/Jakarta
import javax.sql.DataSource;
import java.util.List;

// 3. Imports de librerías externas (Apache Camel, etc.)
import org.apache.camel.CamelContext;
import org.apache.camel.main.Main;

// 4. Imports del proyecto
import com.sura.mi.reportes.vidagrupo.domain.common.exception.DatabaseConfigurationException;
import com.sura.mi.reportes.vidagrupo.infrastructure.common.config.ApplicationServiceRegistry;
```

**REGLAS OBLIGATORIAS**:
- **Siempre usar importaciones estáticas** para constantes
- Agrupar por: estáticos, Java/Jakarta, librerías externas, proyecto
- Orden alfabético dentro de cada grupo

### 3. Estructura de Clases

```java
public class MicrointegratorMain {
    // 1. Constantes
    private static final String DEFAULT_CONFIG = "/config";
    
    // 2. Variables estáticas
    private static ApplicationContext context;
    
    // 3. Variables de instancia
    private final DataSource dataSource;
    
    // 4. Constructor
    public MicrointegratorMain(DataSource dataSource) {
        this.dataSource = dataSource;
    }
    
    // 5. Métodos públicos
    public void start() { }
    
    // 6. Métodos privados/protected
    private void initialize() { }
    
    // 7. Getters/Setters (si no se usa Lombok)
    public DataSource getDataSource() { return dataSource; }
}
```

**REGLA CRÍTICA**: **NO dejar línea en blanco** inmediatamente después de la llave de apertura `{` de la declaración de clase (dentro de la clase)

### 4. Uso de Var (Java 10+)

```java
// ✅ CORRECTO - Tipo claro por contexto
var main = new Main();
var camelContext = main.getCamelContext();
var serviceRegistry = new ApplicationServiceRegistry(main, dataSource, config);

// ❌ INCORRECTO - Tipo no claro
var result = process();  // ¿Qué tipo es result?
var data = getData();    // ¿Qué tipo es data?
```

### 5. Manejo de Excepciones

```java
// ✅ CORRECTO - Usar constante para mensaje
private DataSource configureDatabase(CamelContext camelContext) {
    try {
        return OracleDataSourceProvider.createOracleDataSource(camelContext);
    } catch (Exception e) {
        throw new DatabaseConfigurationException(ErrorMessages.DATABASE_CONFIG_ERROR, e);
    }
}

// ❌ INCORRECTO - String literal en excepción
try {
    return createDataSource();
} catch (Exception e) {
    throw new DatabaseConfigurationException("Error configurando BD", e);
}

// ❌ INCORRECTO - Capturar y no hacer nada
try {
    configureDatabase();
} catch (Exception e) {
    // silenciar excepción
}
```

**REGLA**: Usar **constantes** para mensajes de error, no strings literales

### 6. Lombok

```java
// ✅ CORRECTO - Usar Lombok para reducir boilerplate
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class ReportRequest {
    private String reportId;
    private LocalDate startDate;
    private LocalDate endDate;
}

// ❌ INCORRECTO - Getters/Setters manuales cuando se puede usar Lombok
public class ReportRequest {
    private String reportId;
    
    public String getReportId() { return reportId; }
    public void setReportId(String reportId) { this.reportId = reportId; }
}
```

### 7. Apache Camel Routes

```java
// ✅ CORRECTO - Routes organizadas, nombres descriptivos
public class GenerateDetailChargeRoute extends RouteBuilder {
    private static final String ROUTE_ID = "generate-detail-charge-route";
    private static final String LOG_PROCESSING = "Processing detail charge generation";
    private static final String LOG_SUCCESS = "Detail charge generated successfully";
    
    @Override
    public void configure() throws Exception {
        from("direct:generateDetailCharge")
            .routeId(ROUTE_ID)
            .log(LOG_PROCESSING)
            .to("bean:detailChargeService?method=generate")
            .log(LOG_SUCCESS);
    }
}

// ❌ INCORRECTO - Strings literales
public class GenerateDetailChargeRoute extends RouteBuilder {
    @Override
    public void configure() throws Exception {
        from("direct:generateDetailCharge")
            .routeId("generate-detail-charge-route")  // String literal
            .log("Processing...");  // String literal
    }
}
```

**REGLA**: Declarar todos los strings como **constantes** en la clase

### 8. Inyección de Dependencias

```java
// ✅ CORRECTO - Constructor injection
public class ApplicationServiceRegistry {
    
    private final Main main;
    private final DataSource dataSource;
    private final SqlScriptConfig sqlScriptConfig;
    
    public ApplicationServiceRegistry(Main main, 
                                     DataSource dataSource, 
                                     SqlScriptConfig sqlScriptConfig) {
        this.main = main;
        this.dataSource = dataSource;
        this.sqlScriptConfig = sqlScriptConfig;
    }
}
```

### 9. Formato de Archivos

```java
package com.sura.mi.reportes.vidagrupo;

import static com.sura.constant.AppConstant.PATH;

import org.apache.camel.main.Main;

public class MicrointegratorMain {
    public static void main(String... args) throws Exception {
        var main = new Main();
        main.run();
    }
}
// FIN (sin líneas vacías)
```

**REGLAS OBLIGATORIAS**:
- **NO línea en blanco** inmediatamente después de la llave de apertura `{` de la clase (dentro de la clase)
- **NO líneas vacías** al inicio del archivo (antes del package)
- **NO líneas vacías** al final del archivo

---

## 🔧 **CONFIGURACIÓN DE HERRAMIENTAS**

### Guidewire - CodeNarc

```groovy
// Configuración interna de Guidewire
// CodeNarc se ejecuta automáticamente en el build
```

### Microservicios - SonarQube

```properties
# sonar-project.properties
sonar.projectKey=sura-microintegrador-vidagrupo
sonar.sources=src/main/java
sonar.tests=src/test/java
sonar.java.binaries=build/classes
sonar.coverage.jacoco.xmlReportPaths=build/reports/jacoco/test/jacocoTestReport.xml
```

### Gradle - Plugins de Calidad

```gradle
plugins {
    id 'java'
    id 'jacoco'
    id 'org.owasp.dependencycheck' version '7.0.4.1'
    id 'info.solidsoft.pitest' version '1.15.0'
}

jacoco {
    toolVersion = "0.8.8"
}

jacocoTestReport {
    reports {
        xml.enabled true
        html.enabled true
    }
}
```

---

## 📊 **MÉTRICAS Y CALIDAD**

### Umbrales de Calidad para Guidewire

| Métrica | Umbral Mínimo | Herramienta |
|---------|---------------|-------------|
| **Cobertura de Tests** | 70% | EESuraTestBase + Cobertura Gosu |
| **Complejidad Ciclomática** | < 10 | CodeNarc |
| **Adherencia a Estándares** | 100% | CodeNarc |

### Umbrales de Calidad para Microservicios

| Métrica | Umbral Mínimo | Herramienta |
|---------|---------------|-------------|
| **Cobertura de Tests** | 80% | JaCoCo |
| **Complejidad Ciclomática** | < 10 | SonarQube |
| **Duplicación de Código** | < 3% | SonarQube |
| **Code Smells** | 0 (A rating) | SonarQube |
| **Vulnerabilidades** | 0 | OWASP Dependency Check |
| **Mutation Testing** | > 60% | PIT |

---

## 🚀 **MEJORES PRÁCTICAS ESPECÍFICAS**

### Guidewire - Gosu

1. **NO usar comentarios** de documentación - código autoexplicativo
2. **NO usar strings literales** - declarar en constantes/DisplayKeys
3. **Usar Enhancements** para agregar funcionalidad a entidades
4. **DisplayKeys** siempre con prefijo `Sura.` para mensajes al usuario
5. **Query Builder** para todas las consultas a BD
6. **GX Model** para integraciones XML
7. **Gson** para integraciones JSON
8. **No usar variables estáticas mutables**
9. **Templates de Gosu** para concatenación de strings
10. **Validar nulos** antes de acceder propiedades
11. **Filtrar en BD**, no en código
12. **Plugins en scope estático** o application
13. **Integraciones síncronas**: Fachada + Localizador + Circuit Breaker
14. **Integraciones asíncronas**: RabbitMQ + Transport Plugin
15. **Caché con Guava** para evitar N llamadas a servicios externos

### Java - Apache Camel

1. **NO usar comentarios** de documentación - código autoexplicativo
2. **NO usar strings literales** - declarar todas las constantes
3. **Sin línea en blanco** dentro de la clase después de la llave de apertura `{`
4. **Usar var** cuando el tipo sea obvio
5. **Lombok** para reducir boilerplate
6. **Constructor injection** para dependencias
7. **Importaciones estáticas** para constantes
8. **Excepciones específicas** con mensajes en constantes
9. **Routes descriptivas** con routeId en constante
10. **Logging apropiado** con mensajes en constantes
11. **Properties externalizadas** en archivos de configuración
12. **DataSource en scope** de aplicación
13. **Tests con cobertura > 80%**

### Seguridad

#### Guidewire
- Validar permisos antes de ejecutar acciones críticas
- No exponer información sensible en logs
- Usar DisplayKeys para mensajes al usuario
- Validar entrada de usuario antes de procesar

#### Microservicios
- No hardcodear credenciales (usar variables de entorno)
- Validar input en controllers/routes
- Actualizar dependencias regularmente (OWASP Dependency Check)
- Usar HTTPS para comunicaciones externas
- Implementar circuit breakers (Hystrix/Resilience4j)

---

## 🔄 **PROCESO DE ACTUALIZACIÓN**

### Cuándo Actualizar estos Estándares

1. **Cambios de tecnología** - Nuevas versiones de Guidewire, Java, Camel
2. **Lecciones aprendidas** - Problemas recurrentes identificados en code reviews
3. **Revisión trimestral** - Evaluación de efectividad con el equipo
4. **Feedback de onboarding** - Comentarios de nuevos desarrolladores
5. **Nuevos componentes** - Incorporación de nuevas tecnologías al stack

### Proponer Cambios

1. Crear issue/ticket en el sistema de gestión
2. Describir problema y propuesta de solución
3. Discutir en reunión de equipo técnico
4. Aprobar por arquitectura
5. Actualizar este documento
6. Comunicar cambios a todos los equipos
7. Actualizar checklist de code review

---

## 📚 **REFERENCIAS Y RECURSOS**

### Documentación Oficial Guidewire

- [Gosu Reference Guide](guidewire-docs/gosu-reference)
- [PolicyCenter Developer Guide](guidewire-docs/pc-dev-guide)
- [BillingCenter Developer Guide](guidewire-docs/bc-dev-guide)
- [ClaimCenter Developer Guide](guidewire-docs/cc-dev-guide)
- [Gosu Best Practices](guidewire-docs/gosu-best-practices)
- [Integration Guide - Plugins, Messaging](guidewire-docs/integration-guide)
- [Circuit Breaker Pattern with Hystrix](guidewire-docs/hystrix-implementation)

### Documentación Apache Camel

- [Apache Camel Documentation](https://camel.apache.org/manual/)
- [Camel Routes](https://camel.apache.org/manual/routes.html)
- [Camel Components](https://camel.apache.org/components/latest/)

### Herramientas

- **Guidewire**:
  - Linting: CodeNarc (interno)
  - Testing: EESuraTestBase
  - IDE: IntelliJ IDEA con plugin Gosu
  
- **Java Microservicios**:
  - Build: Gradle 7+
  - Testing: JUnit 5, Mockito
  - Coverage: JaCoCo
  - Quality: SonarQube
  - Security: OWASP Dependency Check
  - Mutation Testing: PIT

### Recursos de Aprendizaje

- [Effective Java (Joshua Bloch)](https://www.oreilly.com/library/view/effective-java/9780134686097/)
- [Clean Code (Robert C. Martin)](https://www.oreilly.com/library/view/clean-code-a/9780136083238/)
- [Apache Camel in Action](https://www.manning.com/books/camel-in-action-second-edition)

---

## ✅ **CODE REVIEW CHECKLIST**

### Guidewire (Gosu)

- [ ] Código en inglés, comentarios en español
- [ ] Sin código comentado
- [ ] Sin comentarios de documentación (código autoexplicativo)
- [ ] Sin líneas vacías al inicio/final de archivos
- [ ] Sin strings literales (usar constantes/DisplayKeys)
- [ ] Nomenclatura correcta (paquetes, clases, métodos, variables)
- [ ] DisplayKeys con prefijo `Sura.` para mensajes al usuario
- [ ] Enhancements terminan en `_Ext`
- [ ] Sin variables públicas (usar propiedades)
- [ ] Sin variables estáticas mutables
- [ ] Manejo de errores con try-catch
- [ ] Validación de nulos
- [ ] Query Builder para consultas BD
- [ ] Tests con EESuraTestBase y @RunLevel(NONE)
- [ ] Tests con patrón AAA
- [ ] Cobertura > 70%
- [ ] Sin warnings de CodeNarc

**Integraciones Externas (si aplica)**:
- [ ] Integraciones síncronas usan Fachada + Localizador de Servicios
- [ ] Circuit Breaker Hystrix implementado (si aplica)
- [ ] Excepciones Business/Technical propagadas correctamente
- [ ] Caché con Guava implementado (si aplica)
- [ ] WService_Ext configurado con parámetros del servicio
- [ ] Prueba SOAPUI creada con FacadeTesterAPI
- [ ] Integraciones asíncronas usan RabbitMQ + Transport Plugin
- [ ] Colas RabbitMQ configuradas (main, retry, DLQ)
- [ ] Servicio registrado en Registry 8 de Sura

### Java Microservicios

- [ ] Código en inglés, comentarios en español
- [ ] Sin código comentado
- [ ] Sin comentarios de documentación (código autoexplicativo)
- [ ] Sin líneas vacías al inicio/final de archivos
- [ ] Sin línea en blanco dentro de la clase después de `{`
- [ ] Sin strings literales (usar constantes)
- [ ] Importaciones estáticas para constantes
- [ ] Imports organizados correctamente
- [ ] Uso apropiado de `var`
- [ ] Lombok para reducir boilerplate
- [ ] Constructor injection
- [ ] Manejo de excepciones específicas
- [ ] Routes con routeId descriptivo
- [ ] Properties externalizadas
- [ ] Tests con JUnit 5
- [ ] Cobertura JaCoCo > 80%
- [ ] Sin code smells en SonarQube
- [ ] Sin vulnerabilidades (OWASP)

---

**NOTA IMPORTANTE**: Estos estándares fueron generados analizando:
- Documentación oficial de estándares Guidewire Sura
- Código base existente en PolicyCenter, BillingCenter, ClaimCenter
- Código base de microservicios (MicroIntegradorVidaGrupo, MicroIntegradorReportesVidaGrupo)
- Configuraciones de herramientas (Gradle, CodeNarc, SonarQube)

Deben evolucionar con el proyecto y ser revisados periódicamente.

---

_Documento generado con Método Ceiba - Arquitecto_  
_Última actualización: 2025-11-18_  
_Versión: 1.0_
