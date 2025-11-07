# Best Practices MuleSoft - Guide de référence

Ce document détaille toutes les best practices implémentées dans ce template et comment les appliquer dans vos projets.

## 📚 Table des matières

1. [Organisation du code](#organisation-du-code)
2. [Nommage](#nommage)
3. [Gestion des erreurs](#gestion-des-erreurs)
4. [Logging](#logging)
5. [Configuration](#configuration)
6. [Sécurité](#sécurité)
7. [Performance](#performance)
8. [Tests](#tests)
9. [Documentation](#documentation)
10. [DataWeave](#dataweave)

## 📂 Organisation du code

### Structure des fichiers

✅ **À FAIRE :**
```
src/main/mule/
├── global.xml                    # Configuration globale
├── error-handlers.xml            # Gestion des erreurs
├── common-flows.xml              # Flows réutilisables
├── api-layer-experience.xml      # Couche Experience
├── api-layer-process.xml         # Couche Process
└── api-layer-system.xml          # Couche System
```

❌ **À ÉVITER :**
- Tout mettre dans un seul fichier XML
- Mélanger les responsabilités des couches
- Fichiers XML trop volumineux (> 500 lignes)

### Séparation des responsabilités

✅ **À FAIRE :**
```xml
<!-- Fichier séparé pour chaque responsabilité -->
<flow name="experience-customer-flow">
    <!-- Uniquement transformation pour le client -->
    <flow-ref name="process-customer-flow"/>
    <ee:transform><!-- Format client --></ee:transform>
</flow>
```

❌ **À ÉVITER :**
```xml
<!-- Tout dans un seul flow -->
<flow name="everything-flow">
    <!-- HTTP Listener -->
    <!-- Logique métier -->
    <!-- Accès DB -->
    <!-- Transformation -->
    <!-- Tout mélangé ! -->
</flow>
```

## 🏷️ Nommage

### Conventions de nommage

#### Flows

✅ **À FAIRE :**
```xml
<!-- Format: [layer]-[entity]-[action]-flow -->
<flow name="api-get-customers-flow">
<flow name="process-create-customer-flow">
<flow name="system-update-order-flow">
```

❌ **À ÉVITER :**
```xml
<flow name="flow1">
<flow name="getCustomers">
<flow name="Flow_For_Creating_Customers">
```

#### Sub-flows

✅ **À FAIRE :**
```xml
<!-- Format: [purpose]-subflow -->
<sub-flow name="validate-input-subflow">
<sub-flow name="generate-correlation-id-subflow">
<sub-flow name="enrich-response-subflow">
```

#### Variables

✅ **À FAIRE :**
```xml
<!-- camelCase descriptif -->
<set-variable variableName="correlationId"/>
<set-variable variableName="startTime"/>
<set-variable variableName="customerData"/>
```

❌ **À ÉVITER :**
```xml
<set-variable variableName="x"/>
<set-variable variableName="temp"/>
<set-variable variableName="var1"/>
```

#### Propriétés de configuration

✅ **À FAIRE :**
```yaml
# Format: category.subcategory.property
http:
  host: "localhost"
  port: "8082"

db:
  connection:
    url: "jdbc:postgresql://..."
```

## 🚨 Gestion des erreurs

### Error Handler global

✅ **À FAIRE :**
```xml
<!-- Créer un error handler réutilisable -->
<error-handler name="global-api-error-handler">
    <on-error-propagate type="APIKIT:BAD_REQUEST">
        <!-- Format JSON uniforme -->
        <ee:transform>
            {
                "error": {
                    "code": "BAD_REQUEST",
                    "message": "...",
                    "correlationId": vars.correlationId
                }
            }
        </ee:transform>
        <!-- Logging approprié -->
        <logger level="WARN" message="..."/>
    </on-error-propagate>
</error-handler>

<!-- Référencer dans chaque flow -->
<flow name="my-flow">
    ...
    <error-handler ref="global-api-error-handler"/>
</flow>
```

❌ **À ÉVITER :**
```xml
<!-- Error handler dupliqué dans chaque flow -->
<flow name="flow1">
    <error-handler>
        <on-error-propagate>
            <!-- Code dupliqué -->
        </on-error-propagate>
    </error-handler>
</flow>

<flow name="flow2">
    <error-handler>
        <on-error-propagate>
            <!-- Même code encore -->
        </on-error-propagate>
    </error-handler>
</flow>
```

### Format des erreurs

✅ **À FAIRE :**
```json
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "Message clair et descriptif",
        "details": "Informations supplémentaires",
        "timestamp": "2024-11-07T14:30:00Z",
        "correlationId": "abc-123"
    }
}
```

❌ **À ÉVITER :**
```json
{
    "error": "Something went wrong",
    "stack": "java.lang.NullPointerException..."  // ⚠️ Fuite d'information
}
```

## 📝 Logging

### Niveaux de log

✅ **À FAIRE :**
```xml
<!-- DEBUG : Informations détaillées pour le développement -->
<logger level="DEBUG" message="#['Processing customer: ' ++ payload.customerId]"/>

<!-- INFO : Informations importantes du flux -->
<logger level="INFO" message="#['[START] CorrelationId: ' ++ vars.correlationId]"/>

<!-- WARN : Situations anormales mais gérées -->
<logger level="WARN" message="#['Customer not found: ' ++ vars.customerId]"/>

<!-- ERROR : Erreurs nécessitant une attention -->
<logger level="ERROR" message="#['[ERROR] Database connection failed']"/>
```

### Format de log standardisé

✅ **À FAIRE :**
```
[NIVEAU] [PHASE] CorrelationId: ABC-123 | Context | Message
[INFO] [START] CorrelationId: ABC-123 | Method: POST | Path: /customers
[DEBUG] [PROCESS] CorrelationId: ABC-123 | Validating customer data
[INFO] [END] CorrelationId: ABC-123 | Status: 200 | Duration: 145ms
```

❌ **À ÉVITER :**
```
Processing...
Done
Error
```

### Correlation ID

✅ **À FAIRE :**
```xml
<!-- Générer ou préserver le Correlation ID -->
<flow-ref name="generate-correlation-id-subflow"/>

<!-- L'inclure dans tous les logs -->
<logger message="#['CorrelationId: ' ++ vars.correlationId ++ ' | ...']"/>

<!-- Le retourner dans la réponse -->
<http:response>
    <http:headers>
        #[{ "X-Correlation-ID": vars.correlationId }]
    </http:headers>
</http:response>
```

## ⚙️ Configuration

### Externalisation

✅ **À FAIRE :**
```xml
<!-- global.xml -->
<configuration-properties file="config.${env}.yaml"/>

<!-- Utilisation -->
<http:listener-connection host="${http.host}" port="${http.port}"/>
```

❌ **À ÉVITER :**
```xml
<!-- Valeurs hardcodées -->
<http:listener-connection host="localhost" port="8081"/>
<db:connection url="jdbc:postgresql://prod-db:5432/db"/>
```

### Configuration par environnement

✅ **À FAIRE :**
```
config.local.yaml   → Développement local
config.dev.yaml     → Environnement de développement
config.prod.yaml    → Production
```

Chaque fichier avec ses propres valeurs :
```yaml
# config.local.yaml
db:
  host: "localhost"
  
# config.prod.yaml
db:
  host: "prod-db.example.com"
```

## 🔒 Sécurité

### Gestion des secrets

✅ **À FAIRE :**
```yaml
# Développement (config.dev.yaml)
security:
  client:
    id: "dev-client-id"
    secret: "dev-client-secret"

# Production (config.prod.yaml)
security:
  client:
    id: "${secure::client.id}"
    secret: "${secure::client.secret}"
```

❌ **À ÉVITER :**
```yaml
# ⚠️ Secrets en clair dans le code
security:
  client:
    secret: "MyP@ssw0rd123!"  # ❌ Dangereux !
```

### Validation des inputs

✅ **À FAIRE :**
```xml
<flow-ref name="validate-input-subflow"/>

<choice>
    <when expression="#[vars.isValidInput == false]">
        <raise-error type="VALIDATION:INVALID_INPUT"/>
    </when>
</choice>
```

### Pas de données sensibles dans les logs

✅ **À FAIRE :**
```xml
<logger message="#['Processing transaction for customer: ' ++ payload.customerId]"/>
```

❌ **À ÉVITER :**
```xml
<!-- ⚠️ Fuite d'informations sensibles -->
<logger message="#['Credit card: ' ++ payload.creditCard]"/>
<logger message="#['Password: ' ++ payload.password]"/>
```

## 🚀 Performance

### Connection Pooling

✅ **À FAIRE :**
```xml
<http:request-config name="HTTP_Request_Config">
    <http:request-connection>
        <http:client-socket-properties>
            <reconnection>
                <reconnect frequency="3000" count="3"/>
            </reconnection>
        </http:client-socket-properties>
    </http:request-connection>
</http:request-config>
```

### Timeouts appropriés

✅ **À FAIRE :**
```xml
<http:request responseTimeout="${services.external.timeout}"/>
```

```yaml
services:
  external:
    timeout: "30000"  # 30 secondes
```

### Scatter-Gather pour parallélisme

✅ **À FAIRE :**
```xml
<!-- Appeler plusieurs services en parallèle -->
<scatter-gather timeout="30000">
    <route>
        <flow-ref name="service-a-flow"/>
    </route>
    <route>
        <flow-ref name="service-b-flow"/>
    </route>
</scatter-gather>
```

## 🧪 Tests

### Couverture de test

✅ **À FAIRE :**
- Tester chaque flow principal
- Tester les cas de succès
- Tester les cas d'erreur
- Tester la validation
- Mocker les dépendances externes

```xml
<munit:test name="test-success">
    <munit:behavior>
        <munit-tools:mock-when processor="flow-ref">
            <!-- Mock -->
        </munit-tools:mock-when>
    </munit:behavior>
    
    <munit:execution>
        <flow-ref name="my-flow"/>
    </munit:execution>
    
    <munit:validation>
        <munit-tools:assert-that expression="#[payload.result]"/>
    </munit:validation>
</munit:test>
```

### Tests d'erreur

✅ **À FAIRE :**
```xml
<munit:test name="test-invalid-input" 
            expectedErrorType="VALIDATION:INVALID_INPUT">
    <munit:execution>
        <munit:set-event>
            <munit:payload value="#[{}]"/>  <!-- Payload invalide -->
        </munit:set-event>
        <flow-ref name="my-flow"/>
    </munit:execution>
</munit:test>
```

## 📚 Documentation

### Documentation du code

✅ **À FAIRE :**
```xml
<!-- Utiliser l'attribut doc:name partout -->
<flow name="my-flow" doc:name="My Flow">
    <logger doc:name="Log Request Start"/>
    <ee:transform doc:name="Transform to System Format"/>
</flow>
```

### Documentation externe

✅ **À FAIRE :**
- README.md : Vue d'ensemble, installation, utilisation
- ARCHITECTURE.md : Détails de l'architecture
- BEST_PRACTICES.md : Ce document
- Diagrammes d'architecture
- Exemples d'utilisation

## 🔄 DataWeave

### Bonnes pratiques DataWeave

✅ **À FAIRE :**
```dataweave
%dw 2.0
output application/json

// Variables pour la lisibilité
var fullName = payload.firstName ++ " " ++ payload.lastName
var currentDate = now()

---
{
    // Commentaires clairs
    customer: {
        name: fullName,
        registeredAt: currentDate
    },
    // Gestion des valeurs par défaut
    email: payload.email default "no-email@example.com",
    // Transformation conditionnelle
    status: if (payload.isActive) "ACTIVE" else "INACTIVE"
}
```

❌ **À ÉVITER :**
```dataweave
%dw 2.0
output application/json
---
{
    // Tout sur une ligne, illisible
    customer: payload.firstName ++ " " ++ payload.lastName,
    // Pas de gestion des null
    email: payload.email,
    // Logique complexe sans variable
    x: if (payload.y == true and payload.z > 10 and ...) ...
}
```

### Réutilisation de modules DataWeave

✅ **À FAIRE :**
```dataweave
// src/main/resources/dwl/CustomerUtils.dwl
%dw 2.0
fun formatCustomerName(customer) = 
    upper(customer.firstName) ++ " " ++ upper(customer.lastName)
    
fun calculateAge(birthDate) = 
    (now() - birthDate as DateTime).years
```

```dataweave
%dw 2.0
import * from dwl::CustomerUtils
output application/json
---
{
    name: formatCustomerName(payload),
    age: calculateAge(payload.birthDate)
}
```

## 📋 Checklist de Best Practices

Avant de mettre en production, vérifiez :

### Architecture
- [ ] API-led Connectivity implémenté (Experience, Process, System)
- [ ] Séparation claire des responsabilités
- [ ] Flows réutilisables créés

### Configuration
- [ ] Aucune valeur hardcodée
- [ ] Configuration par environnement
- [ ] Secrets sécurisés en production

### Gestion des erreurs
- [ ] Error handler global réutilisé
- [ ] Format d'erreur uniforme
- [ ] Logging approprié des erreurs
- [ ] Pas de stacktrace en production

### Logging et traçabilité
- [ ] Correlation ID généré et utilisé partout
- [ ] Niveaux de log appropriés
- [ ] Format de log standardisé
- [ ] Pas de données sensibles dans les logs

### Sécurité
- [ ] Validation des inputs
- [ ] Authentification/autorisation
- [ ] Secrets sécurisés
- [ ] Pas d'information sensible exposée

### Performance
- [ ] Timeouts configurés
- [ ] Connection pooling utilisé
- [ ] Patterns de parallélisme quand approprié

### Tests
- [ ] Tests MUnit pour chaque flow principal
- [ ] Tests des cas d'erreur
- [ ] Mocking des dépendances
- [ ] Couverture > 80%

### Documentation
- [ ] README complet
- [ ] doc:name sur tous les composants
- [ ] Exemples d'utilisation
- [ ] Architecture documentée

## 🎯 Prochaines étapes

Pour approfondir, consultez :

1. **README.md** - Guide d'utilisation complet
2. **ARCHITECTURE.md** - Détails de l'architecture
3. **Code source** - Exemples concrets d'implémentation
4. [MuleSoft Best Practices](https://docs.mulesoft.com/mule-runtime/latest/intro-mule-best-practices)

---

**Maintenez ce document à jour** au fur et à mesure que vous découvrez de nouvelles best practices !

