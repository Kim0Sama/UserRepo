# 🚀 Prochaines Étapes - UserService

Ce document liste les améliorations et fonctionnalités à implémenter pour le UserService.

## ✅ Implémenté

- [x] Modèle de données avec héritage (Utilisateur → Admin/Enseignant)
- [x] CRUD complet des utilisateurs
- [x] Authentification JWT
- [x] Contrôle d'accès par rôle (ADMIN uniquement)
- [x] Validation des données
- [x] Gestion des erreurs
- [x] Hashage des mots de passe (BCrypt)
- [x] Base de données unique (plannoradb)
- [x] Intégration avec Eureka
- [x] Documentation complète

## 🔄 Améliorations Prioritaires

### 1. Tests Automatisés

#### Tests Unitaires
- [ ] Tests des repositories
- [ ] Tests des services
- [ ] Tests des controllers
- [ ] Tests de sécurité
- [ ] Couverture de code > 80%

**Outils** : JUnit 5, Mockito, AssertJ

```java
@Test
void testCreerEnseignant() {
    // Given
    EnseignantDTO dto = new EnseignantDTO();
    dto.setEmail("test@plannora.com");
    // ...
    
    // When
    UtilisateurResponseDTO response = service.creerUtilisateur(dto);
    
    // Then
    assertThat(response.getEmail()).isEqualTo("test@plannora.com");
}
```

#### Tests d'Intégration
- [ ] Tests avec Testcontainers (MySQL)
- [ ] Tests des endpoints REST
- [ ] Tests de sécurité end-to-end
- [ ] Tests de performance

**Outils** : Spring Boot Test, Testcontainers, RestAssured

### 2. Pagination et Recherche

#### Pagination
- [ ] Ajouter la pagination pour `GET /api/utilisateurs`
- [ ] Ajouter la pagination pour `GET /api/utilisateurs/enseignants`
- [ ] Paramètres : page, size, sort

```java
@GetMapping
public ResponseEntity<Page<UtilisateurResponseDTO>> getAllUtilisateurs(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size,
    @RequestParam(defaultValue = "nomUser") String sort
) {
    // ...
}
```

#### Recherche
- [ ] Recherche par nom
- [ ] Recherche par email
- [ ] Recherche par département (enseignants)
- [ ] Filtres combinés

```java
@GetMapping("/search")
public ResponseEntity<List<UtilisateurResponseDTO>> searchUtilisateurs(
    @RequestParam(required = false) String nom,
    @RequestParam(required = false) String email,
    @RequestParam(required = false) String departement
) {
    // ...
}
```

### 3. Validation Avancée

- [ ] Validation du format du téléphone
- [ ] Validation de la force du mot de passe
- [ ] Validation des emails institutionnels uniquement
- [ ] Validation personnalisée pour les départements

```java
@Pattern(regexp = "^0[1-9][0-9]{8}$", message = "Format de téléphone invalide")
private String telephone;

@Pattern(regexp = ".*@plannora\\.com$", message = "Email doit être @plannora.com")
private String email;
```

### 4. Audit et Traçabilité

- [ ] Ajouter createdAt, updatedAt
- [ ] Ajouter createdBy, updatedBy
- [ ] Logger toutes les modifications
- [ ] Historique des changements

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
public class Utilisateur {
    @CreatedDate
    private LocalDateTime createdAt;
    
    @LastModifiedDate
    private LocalDateTime updatedAt;
    
    @CreatedBy
    private String createdBy;
    
    @LastModifiedBy
    private String lastModifiedBy;
}
```

### 5. Gestion des Profils

- [ ] Endpoint pour modifier son propre profil
- [ ] Endpoint pour changer son mot de passe
- [ ] Validation de l'ancien mot de passe
- [ ] Notification par email

```java
@PutMapping("/profile")
@PreAuthorize("isAuthenticated()")
public ResponseEntity<UtilisateurResponseDTO> updateOwnProfile(
    @AuthenticationPrincipal String userId,
    @Valid @RequestBody UpdateProfileDTO dto
) {
    // ...
}

@PutMapping("/password")
@PreAuthorize("isAuthenticated()")
public ResponseEntity<Void> changePassword(
    @AuthenticationPrincipal String userId,
    @Valid @RequestBody ChangePasswordDTO dto
) {
    // ...
}
```

## 🎯 Fonctionnalités Métier

### 6. Gestion des UE (Unités d'Enseignement)

- [ ] Relation Enseignant ↔ UE
- [ ] Endpoint pour assigner des UE à un enseignant
- [ ] Endpoint pour lister les UE d'un enseignant
- [ ] Validation : un enseignant ne peut pas avoir plus de X UE

```java
@Entity
public class Enseignant extends Utilisateur {
    @ManyToMany
    @JoinTable(
        name = "enseignant_ue",
        joinColumns = @JoinColumn(name = "enseignant_id"),
        inverseJoinColumns = @JoinColumn(name = "ue_id")
    )
    private List<UE> enseignements;
}
```

### 7. Gestion des Groupes d'Étudiants

- [ ] Créer l'entité Groupe
- [ ] Relation Groupe ↔ Enseignant
- [ ] CRUD des groupes
- [ ] Assigner des enseignants aux groupes

### 8. Emploi du Temps

- [ ] Endpoint pour consulter l'emploi du temps d'un enseignant
- [ ] Intégration avec le PlanningService
- [ ] Synchronisation avec systèmes externes

### 9. Notifications

- [ ] Notification lors de la création d'un compte
- [ ] Notification lors de la modification du profil
- [ ] Notification lors du changement de mot de passe
- [ ] Intégration avec le NotificationService

## 🔧 Améliorations Techniques

### 10. Cache

- [ ] Ajouter Redis pour le cache
- [ ] Cache des utilisateurs fréquemment consultés
- [ ] Cache des listes d'enseignants
- [ ] Invalidation du cache lors des modifications

```java
@Cacheable(value = "utilisateurs", key = "#id")
public UtilisateurResponseDTO getUtilisateurById(String id) {
    // ...
}

@CacheEvict(value = "utilisateurs", key = "#id")
public void deleteUtilisateur(String id) {
    // ...
}
```

### 11. Monitoring et Observabilité

- [ ] Ajouter Spring Boot Actuator
- [ ] Métriques Prometheus
- [ ] Logs structurés (JSON)
- [ ] Tracing distribué (Zipkin/Jaeger)

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### 12. Documentation API

- [ ] Ajouter Swagger/OpenAPI
- [ ] Documenter tous les endpoints
- [ ] Exemples de requêtes/réponses
- [ ] Schémas de validation

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.0.0</version>
</dependency>
```

### 13. Sécurité Avancée

- [ ] Rate limiting (limitation du nombre de requêtes)
- [ ] Protection contre les attaques par force brute
- [ ] Verrouillage de compte après X tentatives
- [ ] Authentification à deux facteurs (2FA)
- [ ] Rotation des tokens JWT

### 14. Performance

- [ ] Optimisation des requêtes JPA
- [ ] Index sur les colonnes fréquemment recherchées
- [ ] Lazy loading pour les relations
- [ ] Connection pooling optimisé

```sql
CREATE INDEX idx_utilisateurs_email ON utilisateurs(email);
CREATE INDEX idx_enseignants_departement ON enseignants(departement);
```

### 15. Gestion des Erreurs Avancée

- [ ] Codes d'erreur personnalisés
- [ ] Messages d'erreur internationalisés
- [ ] Logging détaillé des erreurs
- [ ] Alertes pour les erreurs critiques

```java
public enum ErrorCode {
    USER_NOT_FOUND("USR001", "Utilisateur non trouvé"),
    EMAIL_ALREADY_EXISTS("USR002", "Email déjà utilisé"),
    INVALID_PASSWORD("USR003", "Mot de passe invalide");
    
    private final String code;
    private final String message;
}
```

## 🌐 Intégration

### 16. Intégration avec les Autres Services

- [ ] Communication avec PlanningService
- [ ] Communication avec NotificationService
- [ ] Communication avec ReportingService
- [ ] Events asynchrones (Kafka/RabbitMQ)

### 17. Import/Export

- [ ] Import d'utilisateurs depuis CSV
- [ ] Export des utilisateurs en CSV/Excel
- [ ] Import depuis LDAP/Active Directory
- [ ] Synchronisation avec systèmes RH

## 🚀 Déploiement

### 18. Containerisation

- [ ] Créer un Dockerfile
- [ ] Docker Compose pour l'environnement complet
- [ ] Optimisation de l'image Docker
- [ ] Multi-stage build

```dockerfile
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY target/user-service.jar app.jar
EXPOSE 8083
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### 19. CI/CD

- [ ] Pipeline GitHub Actions
- [ ] Tests automatiques
- [ ] Build automatique
- [ ] Déploiement automatique

### 20. Configuration Externalisée

- [ ] Spring Cloud Config
- [ ] Variables d'environnement
- [ ] Secrets management (Vault)
- [ ] Configuration par environnement (dev, test, prod)

## 📊 Reporting

### 21. Statistiques

- [ ] Nombre d'utilisateurs par rôle
- [ ] Nombre d'enseignants par département
- [ ] Utilisateurs actifs/inactifs
- [ ] Graphiques et tableaux de bord

### 22. Logs et Audit

- [ ] Logs d'accès
- [ ] Logs de modifications
- [ ] Logs de sécurité
- [ ] Rapports d'audit

## 🎨 UX/UI

### 23. Interface d'Administration

- [ ] Interface web pour gérer les utilisateurs
- [ ] Formulaires de création/modification
- [ ] Tableaux de bord
- [ ] Recherche et filtres

## 📱 API Mobile

### 24. Optimisation Mobile

- [ ] Endpoints optimisés pour mobile
- [ ] Réponses allégées
- [ ] Support des notifications push
- [ ] Synchronisation offline

## 🔒 Conformité

### 25. RGPD

- [ ] Consentement utilisateur
- [ ] Droit à l'oubli
- [ ] Export des données personnelles
- [ ] Anonymisation des données

### 26. Accessibilité

- [ ] API accessible
- [ ] Documentation accessible
- [ ] Support des technologies d'assistance

## 📝 Priorités

### Court Terme (1-2 semaines)
1. Tests unitaires et d'intégration
2. Pagination et recherche
3. Validation avancée
4. Documentation API (Swagger)

### Moyen Terme (1 mois)
1. Gestion des UE
2. Cache (Redis)
3. Monitoring (Actuator)
4. Audit et traçabilité

### Long Terme (2-3 mois)
1. Gestion des groupes
2. Intégration avec autres services
3. Import/Export
4. Interface d'administration

## 🎯 Objectifs de Qualité

- [ ] Couverture de tests > 80%
- [ ] Performance : < 200ms pour les requêtes simples
- [ ] Disponibilité : > 99.9%
- [ ] Sécurité : Aucune vulnérabilité critique
- [ ] Documentation : 100% des endpoints documentés

## 📚 Ressources

- [Spring Boot Documentation](https://spring.io/projects/spring-boot)
- [Spring Security](https://spring.io/projects/spring-security)
- [Spring Data JPA](https://spring.io/projects/spring-data-jpa)
- [JWT Best Practices](https://tools.ietf.org/html/rfc8725)
- [REST API Best Practices](https://restfulapi.net/)

---

**Ce document sera mis à jour au fur et à mesure de l'avancement du projet.** 🚀
