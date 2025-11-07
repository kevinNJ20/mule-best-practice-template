# Architecture du Projet - Mule Best Practice Template

## 📐 Vue d'ensemble de l'architecture

Ce document détaille l'architecture du template de référence MuleSoft, ses composants, et les décisions d'architecture prises.

## 🏗️ Architecture API-led Connectivity

### Concept

L'architecture API-led Connectivity est un pattern architectural qui organise les APIs en trois couches distinctes, chacune avec des responsabilités spécifiques.

```
┌─────────────────────────────────────────────────────────────┐
│                    CLIENTS / CONSUMERS                       │
│  (Mobile Apps, Web Apps, Partner Systems, etc.)             │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            │ HTTPS/REST
                            │
┌───────────────────────────▼─────────────────────────────────┐
│              EXPERIENCE LAYER (API Experience)               │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  • Transformation orientée client                    │   │
│  │  • Agrégation de données                            │   │
│  │  • Format adapté au canal (mobile, web, etc.)       │   │
│  │  • Gestion des requêtes HTTP                        │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                              │
│  Fichiers: api-layer-experience.xml                         │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            │ Appels internes
                            │
┌───────────────────────────▼─────────────────────────────────┐
│               PROCESS LAYER (API Process)                    │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  • Orchestration des services                       │   │
│  │  • Logique métier                                   │   │
│  │  • Validation des règles métier                     │   │
│  │  • Enrichissement des données                       │   │
│  │  • Composition de services                          │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                              │
│  Fichiers: api-layer-process.xml                            │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            │ Appels internes
                            │
┌───────────────────────────▼─────────────────────────────────┐
│                SYSTEM LAYER (API System)                     │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  • Intégration systèmes backends                    │   │
│  │  • Accès aux bases de données                       │   │
│  │  • Appels aux services externes                     │   │
│  │  • Abstraction des systèmes sources                 │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                              │
│  Fichiers: api-layer-system.xml                             │
└───────────────────────────┬─────────────────────────────────┘
                            │
            ┌───────────────┴───────────────┐
            │                               │
┌───────────▼──────────┐        ┌──────────▼──────────┐
│   DATABASE           │        │  EXTERNAL SERVICES   │
│   • PostgreSQL       │        │  • REST APIs         │
│   • MongoDB          │        │  • SOAP Services     │
│   • Oracle           │        │  • Legacy Systems    │
└──────────────────────┘        └─────────────────────┘
```

### Détail des couches

#### 1. Experience Layer (Couche d'expérience)

**Responsabilités :**
- Exposer les APIs aux clients finaux
- Transformer les données selon les besoins du canal (mobile, web, partenaires)
- Gérer les requêtes/réponses HTTP
- Agrégation de données de plusieurs sources

**Caractéristiques :**
- Une Experience API par canal ou type de client
- Format de données optimisé pour le consommateur
- Léger traitement métier
- Haute disponibilité et performance

**Exemple de flow :**
```xml
<flow name="api-get-customers-flow">
    <!-- Réception de la requête HTTP -->
    <http:listener path="/customers"/>
    
    <!-- Appel à la couche Process -->
    <flow-ref name="process-get-customers-flow"/>
    
    <!-- Transformation pour le client -->
    <ee:transform>
        <!-- Format optimisé pour l'affichage -->
        {
            customers: [...],
            totalCount: ...,
            metadata: {...}
        }
    </ee:transform>
</flow>
```

#### 2. Process Layer (Couche de processus)

**Responsabilités :**
- Orchestrer plusieurs System APIs
- Implémenter la logique métier
- Valider les règles métier
- Enrichir et transformer les données
- Gérer les transactions

**Caractéristiques :**
- Réutilisable par plusieurs Experience APIs
- Contient la logique métier centrale
- Orchestration de services
- Gestion des erreurs métier

**Exemple de flow :**
```xml
<flow name="process-create-customer-flow">
    <!-- Validation métier -->
    <ee:transform>
        <!-- Validation email -->
        <!-- Validation montant -->
    </ee:transform>
    
    <!-- Appel à plusieurs System APIs -->
    <flow-ref name="system-create-customer-flow"/>
    <flow-ref name="system-send-welcome-email-flow"/>
    
    <!-- Enrichissement -->
    <ee:transform>
        <!-- Ajout de données calculées -->
    </ee:transform>
</flow>
```

#### 3. System Layer (Couche système)

**Responsabilités :**
- Intégrer les systèmes backends
- Abstraire les spécificités des systèmes sources
- Fournir une interface standard
- Gérer les connexions aux systèmes externes

**Caractéristiques :**
- Une System API par système backend
- Interface stable et réutilisable
- Gestion des connexions et authentifications
- Transformation des formats propriétaires

**Exemple de flow :**
```xml
<flow name="system-get-customers-flow">
    <!-- Accès à la base de données -->
    <db:select>
        <db:sql>SELECT * FROM customers</db:sql>
    </db:select>
    
    <!-- Transformation au format standard -->
    <ee:transform>
        <!-- Format JSON standard -->
    </ee:transform>
</flow>
```

## 🔧 Composants transversaux

### 1. Gestion globale des erreurs (error-handlers.xml)

**Architecture :**

```
┌─────────────────────────────────────────────────────────┐
│           GLOBAL ERROR HANDLER                          │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌────────────────┐  ┌────────────────┐               │
│  │ Validation     │  │ Not Found      │               │
│  │ Errors         │  │ (404)          │               │
│  │ (400)          │  └────────────────┘               │
│  └────────────────┘                                    │
│                                                          │
│  ┌────────────────┐  ┌────────────────┐               │
│  │ Connectivity   │  │ Timeout        │               │
│  │ Errors         │  │ (504)          │               │
│  │ (503)          │  └────────────────┘               │
│  └────────────────┘                                    │
│                                                          │
│  ┌────────────────┐  ┌────────────────┐               │
│  │ Transformation │  │ Generic        │               │
│  │ Errors         │  │ Error          │               │
│  │ (500)          │  │ (500)          │               │
│  └────────────────┘  └────────────────┘               │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

**Caractéristiques :**
- Handler global réutilisable
- Codes HTTP standards
- Format JSON uniforme
- Logging approprié
- Traçabilité via Correlation ID

### 2. Flows communs (common-flows.xml)

**Sous-flows réutilisables :**

```
┌──────────────────────────────────────────────────┐
│         COMMON FLOWS (Sub-flows)                 │
├──────────────────────────────────────────────────┤
│                                                   │
│  • generate-correlation-id-subflow              │
│    → Génère ou préserve le Correlation ID       │
│                                                   │
│  • log-request-start-subflow                    │
│    → Log le début de la requête                 │
│                                                   │
│  • log-request-end-subflow                      │
│    → Log la fin avec durée                      │
│                                                   │
│  • validate-input-subflow                       │
│    → Validation des données d'entrée            │
│                                                   │
│  • enrich-response-subflow                      │
│    → Enrichit la réponse avec métadonnées       │
│                                                   │
│  • save-start-time-subflow                      │
│    → Sauvegarde le timestamp de début           │
│                                                   │
└──────────────────────────────────────────────────┘
```

### 3. Configuration globale (global.xml)

**Composants configurés :**

```
┌──────────────────────────────────────────────────┐
│         GLOBAL CONFIGURATION                     │
├──────────────────────────────────────────────────┤
│                                                   │
│  • Environment Properties                        │
│    → env variable                                │
│                                                   │
│  • Configuration Properties                      │
│    → config.${env}.yaml                          │
│                                                   │
│  • HTTP Listener Config                         │
│    → Endpoints entrants                          │
│                                                   │
│  • HTTP Request Config                          │
│    → Appels services externes                    │
│                                                   │
└──────────────────────────────────────────────────┘
```

## 🔄 Flux de traitement d'une requête

### Exemple : Création d'un client

```
1. CLIENT
   │
   │ POST /api/v1/customers
   │ { "firstName": "Marie", "lastName": "Dupont", ... }
   │
   ▼
2. EXPERIENCE LAYER (api-create-customer-flow)
   │
   ├─ Save Start Time
   ├─ Generate Correlation ID → ABC-123
   ├─ Log Request Start
   │
   ├─ Validate Input
   │  └─ Payload non vide? ✓
   │
   ├─ Call Process Layer ────────────┐
   │                                  │
   ▼                                  │
3. PROCESS LAYER                     │
   (process-create-customer-flow) ◄──┘
   │
   ├─ Business Validation
   │  ├─ Email valide? ✓
   │  └─ Règles métier? ✓
   │
   ├─ Transform to System Format
   │  { "emailAddress": "...", "createdAt": now(), ... }
   │
   ├─ Call System Layer ─────────────┐
   │                                  │
   ▼                                  │
4. SYSTEM LAYER                      │
   (system-create-customer-flow) ◄───┘
   │
   ├─ Database Insert
   │  INSERT INTO customers (...)
   │
   ├─ Return Customer ID
   │  { "id": "CUST-999", ... }
   │
   └─ Return to Process ─────────────┐
                                      │
   ┌──────────────────────────────────┘
   │
   ▼
5. PROCESS LAYER (retour)
   │
   └─ Return to Experience ──────────┐
                                      │
   ┌──────────────────────────────────┘
   │
   ▼
6. EXPERIENCE LAYER (retour)
   │
   ├─ Create Response
   │  {
   │    "message": "Client créé",
   │    "customerId": "CUST-999",
   │    "correlationId": "ABC-123"
   │  }
   │
   ├─ Set HTTP Status 201
   ├─ Add Location Header
   ├─ Log Request End (duration: 145ms)
   │
   └─ Return to Client ──────────────┐
                                      │
   ┌──────────────────────────────────┘
   │
   ▼
7. CLIENT
   {
     "message": "Client créé avec succès",
     "customerId": "CUST-999",
     "correlationId": "ABC-123",
     "timestamp": "2024-11-07T14:30:00Z"
   }
```

## 📊 Patterns d'intégration

### 1. Scatter-Gather

**Principe :** Appeler plusieurs services en parallèle et agréger les résultats

```
           ┌─────────────┐
           │   Request   │
           └──────┬──────┘
                  │
        ┌─────────┴─────────┐
        │  Scatter-Gather   │
        └─────────┬─────────┘
                  │
      ┌───────────┼───────────┐
      │           │           │
┌─────▼─────┐ ┌──▼──┐ ┌──────▼──────┐
│ Service A │ │ SVC │ │  Service C  │
│ (Customer)│ │  B  │ │  (Loyalty)  │
└─────┬─────┘ └──┬──┘ └──────┬──────┘
      │          │           │
      └──────────┼───────────┘
                 │
         ┌───────▼────────┐
         │   Aggregate    │
         │    Results     │
         └───────┬────────┘
                 │
           ┌─────▼─────┐
           │  Response │
           └───────────┘
```

**Avantages :**
- Réduction du temps de réponse
- Traitement parallèle
- Résilience (gestion des erreurs par route)

### 2. Content-Based Routing

**Principe :** Router les messages selon leur contenu

```
      ┌─────────────┐
      │   Request   │
      │  {type: X}  │
      └──────┬──────┘
             │
     ┌───────▼────────┐
     │     Choice     │
     │  (Content-     │
     │   Based)       │
     └───┬─────┬──────┘
         │     │
    ┌────┘     └────┐
    │               │
┌───▼───┐     ┌────▼────┐
│ Type  │     │  Type   │
│   A   │     │    B    │
│Process│     │ Process │
└───┬───┘     └────┬────┘
    │              │
    └──────┬───────┘
           │
     ┌─────▼─────┐
     │  Response │
     └───────────┘
```

**Cas d'usage :**
- Routage par type de transaction
- Sélection de processeur selon les données
- Multi-tenancy

### 3. Idempotent Filter

**Principe :** Éviter le traitement en double

```
┌─────────────┐
│  Request    │
│ messageId=X │
└──────┬──────┘
       │
┌──────▼──────────┐
│  Check Cache/DB │
│  ID exists?     │
└──────┬──────────┘
       │
    ┌──┴──┐
    │     │
   Yes    No
    │     │
    │  ┌──▼──────────┐
    │  │   Process   │
    │  │   Message   │
    │  └──┬──────────┘
    │     │
    │  ┌──▼──────────┐
    │  │  Save ID    │
    │  │  in Cache   │
    │  └──┬──────────┘
    │     │
    └──┬──┘
       │
  ┌────▼────┐
  │ Response│
  └─────────┘
```

### 4. Batch Processing

**Principe :** Traiter les données par lots

```
┌──────────────────┐
│  Large Dataset   │
│  [1000 records]  │
└────────┬─────────┘
         │
┌────────▼─────────┐
│  Split into      │
│  Batches         │
│  (size: 100)     │
└────────┬─────────┘
         │
    ┌────┴────┐
    │         │
┌───▼───┐ ┌──▼──┐
│Batch 1│ │Batch│...
│  100  │ │  2  │
└───┬───┘ └──┬──┘
    │        │
    └────┬───┘
         │
┌────────▼─────────┐
│  Process Each    │
│  Batch           │
└────────┬─────────┘
         │
┌────────▼─────────┐
│  Aggregate       │
│  Results         │
└──────────────────┘
```

## 🔐 Architecture de sécurité

### Couches de sécurité

```
┌─────────────────────────────────────────┐
│  1. Transport Layer Security (HTTPS)    │
│     • TLS 1.2+                          │
│     • Certificats SSL                   │
└─────────────────┬───────────────────────┘
                  │
┌─────────────────▼───────────────────────┐
│  2. Authentication & Authorization      │
│     • OAuth 2.0 / JWT                   │
│     • API Keys                          │
│     • Basic Auth (dev only)             │
└─────────────────┬───────────────────────┘
                  │
┌─────────────────▼───────────────────────┐
│  3. API Gateway Policies                │
│     • Rate Limiting                     │
│     • IP Whitelisting                   │
│     • Client ID Enforcement             │
└─────────────────┬───────────────────────┘
                  │
┌─────────────────▼───────────────────────┐
│  4. Application Layer Security          │
│     • Input Validation                  │
│     • Error Handling (no data leak)     │
│     • Secure Properties                 │
└─────────────────────────────────────────┘
```

## 📈 Scalabilité et performance

### Considérations de scalabilité

1. **Horizontal Scaling**
   - Ajout de workers/instances
   - Load balancing automatique
   - État distribué

2. **Caching**
   - Cache des données fréquemment accédées
   - Object Store pour l'état partagé
   - TTL appropriés

3. **Async Processing**
   - Utilisation de queues (VM, JMS)
   - Traitement asynchrone des tâches longues
   - Pattern publish-subscribe

4. **Connection Pooling**
   - Pool de connexions DB
   - Pool de connexions HTTP
   - Réutilisation des connexions

## 🔍 Observabilité

### Architecture de monitoring

```
┌──────────────────────────────────────────┐
│        MULE APPLICATION                  │
│                                          │
│  ├─ Correlation ID Generation           │
│  ├─ Structured Logging                  │
│  ├─ Duration Tracking                   │
│  └─ Error Tracking                      │
└──────────────┬───────────────────────────┘
               │
               ├──────────────┐
               │              │
┌──────────────▼─┐    ┌──────▼────────────┐
│  Application   │    │   Anypoint        │
│  Logs          │    │   Monitoring      │
│  • Info        │    │   • Metrics       │
│  • Errors      │    │   • Dashboards    │
│  • Debug       │    │   • Alerts        │
└────────────────┘    └───────────────────┘
```

### Métriques collectées

- Nombre de requêtes
- Temps de réponse moyen
- Taux d'erreur
- Throughput
- Utilisation des ressources

## 🎯 Décisions d'architecture

### ADR (Architecture Decision Records)

#### ADR-001 : Choix d'API-led Connectivity

**Contexte :** Besoin de structurer les APIs de manière maintenable et évolutive

**Décision :** Adopter le pattern API-led Connectivity en 3 couches

**Conséquences :**
- ✅ Séparation des responsabilités
- ✅ Réutilisabilité accrue
- ✅ Maintenance facilitée
- ⚠️ Complexité initiale

#### ADR-002 : Configuration par environnement

**Contexte :** Besoin de déployer sur plusieurs environnements

**Décision :** Utiliser des fichiers YAML par environnement avec Maven profiles

**Conséquences :**
- ✅ Configuration claire par environnement
- ✅ Pas de valeurs hardcodées
- ✅ Facile à maintenir
- ⚠️ Gestion des secrets à sécuriser en prod

#### ADR-003 : Gestion globale des erreurs

**Contexte :** Besoin d'uniformiser la gestion des erreurs

**Décision :** Créer un error handler global réutilisable

**Conséquences :**
- ✅ Format d'erreur uniforme
- ✅ Maintenance centralisée
- ✅ Logging cohérent
- ✅ Traçabilité améliorée

## 📚 Références

- [MuleSoft Architecture Guidelines](https://docs.mulesoft.com/general/architecture-intro)
- [API-led Connectivity](https://www.mulesoft.com/resources/api/api-led-connectivity)
- [Integration Patterns](https://www.enterpriseintegrationpatterns.com/)
- [Microservices Patterns](https://microservices.io/patterns/)

---

**Version :** 1.0  
**Dernière mise à jour :** 2024-11-07

