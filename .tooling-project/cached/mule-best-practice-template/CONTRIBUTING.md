# Guide de contribution

Merci de votre intérêt pour contribuer au Mule Best Practice Template ! 🎉

## 📋 Table des matières

- [Code de conduite](#code-de-conduite)
- [Comment contribuer](#comment-contribuer)
- [Standards de code](#standards-de-code)
- [Process de Pull Request](#process-de-pull-request)
- [Rapporter des bugs](#rapporter-des-bugs)
- [Proposer des améliorations](#proposer-des-améliorations)

## 🤝 Code de conduite

Ce projet adhère à un code de conduite. En participant, vous vous engagez à respecter ce code.

- Soyez respectueux et inclusif
- Acceptez les critiques constructives
- Concentrez-vous sur ce qui est meilleur pour la communauté
- Faites preuve d'empathie envers les autres membres

## 💡 Comment contribuer

### Types de contributions acceptées

- 🐛 **Bug fixes** - Corrections de bugs
- ✨ **Nouvelles fonctionnalités** - Ajout de nouvelles best practices
- 📝 **Documentation** - Amélioration de la documentation
- 🧪 **Tests** - Ajout ou amélioration des tests
- 🎨 **Patterns** - Nouveaux patterns MuleSoft
- ♻️ **Refactoring** - Amélioration du code existant

### Avant de commencer

1. Vérifiez qu'une issue n'existe pas déjà pour votre contribution
2. Pour les changements majeurs, ouvrez d'abord une issue pour discussion
3. Fork le repository
4. Créez une branche depuis `main`

## 📝 Standards de code

### Conventions de nommage

#### Flows
```xml
<!-- Format: [layer]-[action]-[entity]-flow -->
<flow name="api-get-customers-flow">
<flow name="process-create-order-flow">
<flow name="system-update-inventory-flow">
```

#### Sub-flows
```xml
<!-- Format: [purpose]-subflow -->
<sub-flow name="validate-customer-input-subflow">
<sub-flow name="transform-to-external-format-subflow">
```

#### Variables
```xml
<!-- camelCase, noms descriptifs -->
<set-variable variableName="correlationId"/>
<set-variable variableName="customerData"/>
<set-variable variableName="startTime"/>
```

### Structure des fichiers

- **global.xml** - Configuration globale uniquement
- **error-handlers.xml** - Gestion des erreurs
- **common-flows.xml** - Sub-flows réutilisables
- **api-layer-*.xml** - Flows par couche API-led

### Documentation

✅ Obligatoire :
```xml
<flow name="my-flow" doc:name="My Flow Description">
    <logger doc:name="Log Start"/>
    <ee:transform doc:name="Transform Customer Data"/>
    <http:request doc:name="Call External Service"/>
</flow>
```

### DataWeave

✅ Bonnes pratiques :
```dataweave
%dw 2.0
output application/json

// Utiliser des variables pour la lisibilité
var currentDate = now()
var fullName = payload.firstName ++ " " ++ payload.lastName

---
{
    // Commentaires pour la logique complexe
    customer: {
        name: fullName,
        registeredAt: currentDate
    },
    // Gestion des valeurs par défaut
    email: payload.email default "no-email@example.com"
}
```

### Tests

Chaque nouvelle fonctionnalité doit inclure :

```xml
<munit:test name="test-success-case">
    <munit:behavior>
        <!-- Setup et mocks -->
    </munit:behavior>
    
    <munit:execution>
        <!-- Appel du flow -->
    </munit:execution>
    
    <munit:validation>
        <!-- Assertions -->
    </munit:validation>
</munit:test>

<munit:test name="test-error-case" expectedErrorType="...">
    <!-- Test des cas d'erreur -->
</munit:test>
```

## 🔄 Process de Pull Request

### 1. Préparation

```bash
# Fork et clone
git clone https://github.com/VOTRE-USERNAME/mule-best-practice-template.git
cd mule-best-practice-template

# Créer une branche
git checkout -b feature/ma-nouvelle-fonctionnalite
```

### 2. Développement

```bash
# Faire vos modifications
# Suivre les standards de code
# Ajouter des tests

# Tester localement
mvn clean test
mvn clean install -Plocal
```

### 3. Commit

```bash
# Commits clairs et descriptifs
git add .
git commit -m "feat: Ajouter pattern Circuit Breaker

- Implémentation du pattern Circuit Breaker
- Tests MUnit complets
- Documentation mise à jour
- Exemple d'utilisation ajouté"
```

**Format des messages de commit :**
- `feat:` - Nouvelle fonctionnalité
- `fix:` - Correction de bug
- `docs:` - Documentation uniquement
- `test:` - Ajout/modification de tests
- `refactor:` - Refactoring sans changement de fonctionnalité
- `style:` - Formatage, point-virgules manquants, etc.
- `perf:` - Amélioration de performance

### 4. Push et Pull Request

```bash
# Push vers votre fork
git push origin feature/ma-nouvelle-fonctionnalite
```

Puis créez une Pull Request avec :

**Titre clair :**
```
[Feature] Ajout du pattern Circuit Breaker
```

**Description complète :**
```markdown
## Description
Ajout d'un nouveau pattern pour implémenter le Circuit Breaker pattern.

## Type de changement
- [x] Nouvelle fonctionnalité
- [ ] Correction de bug
- [ ] Documentation
- [ ] Refactoring

## Checklist
- [x] Code suit les conventions du projet
- [x] Auto-review effectué
- [x] Commentaires ajoutés pour le code complexe
- [x] Documentation mise à jour
- [x] Tests ajoutés/modifiés
- [x] Tous les tests passent
- [x] Changements testés localement

## Tests effectués
- [x] Tests MUnit (100% coverage)
- [x] Test local avec profil `local`
- [x] Test avec profil `dev`

## Screenshots (si applicable)
[Ajouter des captures d'écran si pertinent]

## Notes additionnelles
[Informations complémentaires]
```

## 🐛 Rapporter des bugs

### Template d'issue pour bug

```markdown
**Titre :** [BUG] Description courte du problème

**Description du bug**
Description claire et concise du bug.

**Pour reproduire**
Étapes pour reproduire le comportement :
1. Aller à '...'
2. Cliquer sur '....'
3. Voir l'erreur

**Comportement attendu**
Description claire de ce qui devrait se passer.

**Comportement actuel**
Ce qui se passe actuellement.

**Screenshots**
Si applicable, ajoutez des captures d'écran.

**Environnement:**
 - OS: [ex: Windows 10]
 - Mule Runtime: [ex: 4.10.0]
 - Java: [ex: JDK 17]
 - Maven: [ex: 3.8.1]

**Logs/Stack trace**
```
[Coller les logs pertinents]
```

**Contexte additionnel**
Toute autre information pertinente.
```

## 💡 Proposer des améliorations

### Template d'issue pour feature request

```markdown
**Titre :** [FEATURE] Description de la fonctionnalité

**Problème à résoudre**
Description claire du problème que cette fonctionnalité résoudrait.

**Solution proposée**
Description de la solution que vous aimeriez voir.

**Alternatives considérées**
Autres solutions ou fonctionnalités que vous avez considérées.

**Exemples de code (si applicable)**
```xml
<!-- Exemple d'utilisation proposée -->
<flow name="example-flow">
    ...
</flow>
```

**Bénéfices**
- Amélioration 1
- Amélioration 2

**Contexte additionnel**
Toute autre information pertinente.
```

## ✅ Checklist avant soumission

Avant de soumettre votre Pull Request, vérifiez :

### Code
- [ ] Le code suit les conventions du projet
- [ ] Les noms sont descriptifs et cohérents
- [ ] Pas de valeurs hardcodées
- [ ] Pas de code commenté inutile
- [ ] Pas de TODO dans le code

### Tests
- [ ] Tests MUnit ajoutés pour la nouvelle fonctionnalité
- [ ] Tests des cas de succès
- [ ] Tests des cas d'erreur
- [ ] Tous les tests passent (`mvn test`)
- [ ] Couverture de code maintenue ou améliorée

### Documentation
- [ ] Attribut `doc:name` sur tous les composants
- [ ] README.md mis à jour si nécessaire
- [ ] ARCHITECTURE.md mis à jour si changements architecturaux
- [ ] BEST_PRACTICES.md mis à jour si nouvelles best practices
- [ ] Commentaires ajoutés pour la logique complexe
- [ ] Exemples d'utilisation fournis

### Configuration
- [ ] Nouvelles propriétés ajoutées à tous les fichiers config (local, dev, prod)
- [ ] Valeurs par défaut sensées
- [ ] Documentation des nouvelles propriétés

### Performance
- [ ] Pas d'impact négatif sur les performances
- [ ] Timeouts appropriés configurés
- [ ] Connection pooling utilisé si applicable

### Sécurité
- [ ] Pas de secrets dans le code
- [ ] Validation des inputs
- [ ] Pas de données sensibles dans les logs
- [ ] Gestion appropriée des erreurs

## 🔍 Review Process

### Ce qui sera vérifié

1. **Qualité du code**
   - Respect des conventions
   - Lisibilité
   - Maintenabilité

2. **Tests**
   - Couverture suffisante
   - Tests significatifs
   - Tous les tests passent

3. **Documentation**
   - Documentation à jour
   - Commentaires pertinents
   - Exemples clairs

4. **Fonctionnalité**
   - Fonctionne comme décrit
   - Pas d'effets de bord
   - Compatible avec le reste

### Timeline

- Les PRs sont généralement revues dans les 48-72 heures
- Des commentaires peuvent être ajoutés pour demander des modifications
- Une fois approuvée, la PR sera mergée

## 📞 Besoin d'aide ?

- 💬 Ouvrez une [Discussion](../../discussions)
- 🐛 Créez une [Issue](../../issues)
- 📧 Contactez les mainteneurs

## 🙏 Remerciements

Merci à tous les contributeurs qui font de ce projet une référence pour la communauté MuleSoft !

---

**Note :** Ce guide de contribution peut évoluer. Consultez régulièrement pour les mises à jour.

