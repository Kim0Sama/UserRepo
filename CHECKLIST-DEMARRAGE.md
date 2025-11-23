# ✅ Checklist de Démarrage - UserService

Utilisez cette checklist pour vérifier que tout est prêt avant de démarrer les tests.

## 📋 Prérequis

### Logiciels Installés
- [ ] Java 17 ou supérieur installé
  ```bash
  java -version
  ```
- [ ] Maven 3.6+ installé
  ```bash
  mvn -version
  ```
- [ ] MySQL 8.0+ installé et démarré
  ```bash
  mysql --version
  ```
- [ ] Postman installé (ou extension REST Client pour VS Code)

### Configuration MySQL
- [ ] MySQL est démarré
- [ ] Base de données `plannoradb` créée
  ```sql
  CREATE DATABASE IF NOT EXISTS plannoradb;
  ```
- [ ] Utilisateur MySQL configuré (par défaut : root sans mot de passe)
- [ ] Port 3306 disponible

### Services Eureka et Auth
- [ ] EurekaService est présent et configuré
- [ ] AuthentificationService est présent et configuré
- [ ] Les deux services peuvent se connecter à MySQL

## 🔧 Configuration du UserService

### Fichiers Créés
- [ ] `pom.xml` existe
- [ ] `application.properties` existe
- [ ] Toutes les classes Java sont créées (17 fichiers)
- [ ] Structure des packages est correcte

### Configuration application.properties
- [ ] Port du service : 8083
- [ ] URL MySQL correcte : `jdbc:mysql://localhost:3306/plannoradb`
- [ ] Credentials MySQL corrects
- [ ] URL Eureka correcte : `http://localhost:8761/eureka/`
- [ ] Secret JWT configuré (même que dans AuthService)

## 🚀 Démarrage des Services

### 1. Démarrer Eureka Server
- [ ] Ouvrir un terminal
- [ ] Naviguer vers `EurekaService/eureka/eureka`
- [ ] Exécuter `mvn spring-boot:run`
- [ ] Attendre le message "Started EurekaApplication"
- [ ] Vérifier http://localhost:8761 dans le navigateur

### 2. Démarrer Authentication Service
- [ ] Ouvrir un nouveau terminal
- [ ] Naviguer vers `AuthentificationService/Authentification/authentification`
- [ ] Exécuter `mvn spring-boot:run`
- [ ] Attendre le message "Started AuthentificationApplication"
- [ ] Vérifier qu'il apparaît dans Eureka

### 3. Démarrer User Service
- [ ] Ouvrir un nouveau terminal
- [ ] Naviguer vers `UserService/user-service`
- [ ] Exécuter `mvn spring-boot:run`
- [ ] Attendre le message "Started UserServiceApplication"
- [ ] Vérifier qu'il apparaît dans Eureka
- [ ] Vérifier le message "Administrateur par défaut créé"

## ✅ Vérifications Post-Démarrage

### Eureka Dashboard
- [ ] Ouvrir http://localhost:8761
- [ ] Vérifier que `USER-SERVICE` est enregistré
- [ ] Vérifier que `AUTHENTIFICATION-SERVICE` est enregistré
- [ ] Les deux services ont le statut UP

### Base de Données
- [ ] Se connecter à MySQL
  ```bash
  mysql -u root -p plannoradb
  ```
- [ ] Vérifier que les tables sont créées
  ```sql
  SHOW TABLES;
  -- Doit afficher : utilisateurs, administrateurs, enseignants
  ```
- [ ] Vérifier l'admin par défaut
  ```sql
  SELECT * FROM utilisateurs;
  -- Doit afficher admin001
  ```

### Logs du Service
- [ ] Pas d'erreurs dans les logs
- [ ] Message "Administrateur par défaut créé" visible
- [ ] Message "Started UserServiceApplication" visible
- [ ] Connexion à Eureka réussie

## 🧪 Tests Basiques

### Test 1 : Authentification
- [ ] Ouvrir Postman
- [ ] Créer une requête POST
- [ ] URL : `http://localhost:8082/api/auth/login`
- [ ] Body (JSON) :
  ```json
  {
    "email": "admin@plannora.com",
    "password": "admin123"
  }
  ```
- [ ] Envoyer la requête
- [ ] Vérifier la réponse 200 OK
- [ ] Copier le token JWT

### Test 2 : Créer un Enseignant
- [ ] Créer une requête POST
- [ ] URL : `http://localhost:8083/api/utilisateurs/enseignant`
- [ ] Header : `Authorization: Bearer VOTRE_TOKEN`
- [ ] Header : `Content-Type: application/json`
- [ ] Body (JSON) :
  ```json
  {
    "email": "test@plannora.com",
    "mdp": "password123",
    "nomUser": "Test",
    "prenomUser": "Utilisateur",
    "telephone": "0600000000",
    "specialite": "Test",
    "departement": "Test"
  }
  ```
- [ ] Envoyer la requête
- [ ] Vérifier la réponse 201 Created
- [ ] Vérifier que l'utilisateur est retourné (sans mot de passe)

### Test 3 : Lister les Utilisateurs
- [ ] Créer une requête GET
- [ ] URL : `http://localhost:8083/api/utilisateurs`
- [ ] Header : `Authorization: Bearer VOTRE_TOKEN`
- [ ] Envoyer la requête
- [ ] Vérifier la réponse 200 OK
- [ ] Vérifier que la liste contient au moins 2 utilisateurs

### Test 4 : Sécurité
- [ ] Créer une requête GET sans token
- [ ] URL : `http://localhost:8083/api/utilisateurs`
- [ ] Envoyer la requête
- [ ] Vérifier la réponse 401 Unauthorized

## 🔍 Vérification dans MySQL

### Après avoir créé un enseignant
```sql
-- Voir tous les utilisateurs
SELECT * FROM utilisateurs;

-- Voir les enseignants
SELECT u.*, e.specialite, e.departement 
FROM utilisateurs u 
JOIN enseignants e ON u.id_user = e.id_user;

-- Compter les utilisateurs
SELECT role, COUNT(*) FROM utilisateurs GROUP BY role;
```

- [ ] L'enseignant créé apparaît dans `utilisateurs`
- [ ] L'enseignant apparaît aussi dans `enseignants`
- [ ] Le mot de passe est hashé (commence par $2a$)
- [ ] L'email est unique

## 📊 Tests Complets avec Postman

### Collection de Tests
- [ ] Importer `test-user-api.http` dans Postman
- [ ] Ou utiliser l'extension REST Client de VS Code
- [ ] Configurer la variable `token` dans Postman

### Scénarios à Tester
- [ ] Créer plusieurs enseignants
- [ ] Créer un administrateur
- [ ] Lister tous les utilisateurs
- [ ] Lister uniquement les enseignants
- [ ] Obtenir un utilisateur par ID
- [ ] Modifier un utilisateur
- [ ] Supprimer un utilisateur
- [ ] Tester les erreurs (email dupliqué, token invalide, etc.)

## ❌ Dépannage

### Problème : Service ne démarre pas
- [ ] Vérifier que le port 8083 est libre
- [ ] Vérifier les logs pour les erreurs
- [ ] Vérifier la connexion MySQL
- [ ] Vérifier que Eureka est démarré

### Problème : 401 Unauthorized
- [ ] Vérifier que le token est valide
- [ ] Vérifier le format du header : `Bearer TOKEN`
- [ ] Vérifier que le secret JWT est le même dans les deux services
- [ ] Se reconnecter pour obtenir un nouveau token

### Problème : 403 Forbidden
- [ ] Vérifier que vous êtes connecté en tant qu'ADMIN
- [ ] Vérifier le rôle dans le token JWT
- [ ] Seul l'ADMIN peut créer/modifier/supprimer

### Problème : Erreur de connexion MySQL
- [ ] Vérifier que MySQL est démarré
- [ ] Vérifier les credentials dans `application.properties`
- [ ] Vérifier que la base `plannoradb` existe
- [ ] Vérifier le port 3306

### Problème : Service non enregistré dans Eureka
- [ ] Attendre 30 secondes
- [ ] Vérifier l'URL Eureka dans `application.properties`
- [ ] Vérifier les logs pour les erreurs de connexion
- [ ] Redémarrer le service

## 📚 Documentation de Référence

- [ ] Lire `README.md` pour la documentation complète
- [ ] Consulter `DEMARRAGE-RAPIDE.md` pour un guide rapide
- [ ] Voir `GUIDE-TESTS-POSTMAN.md` pour les tests détaillés
- [ ] Utiliser `EXEMPLES-CURL.md` pour les tests en ligne de commande
- [ ] Consulter `ARCHITECTURE.md` pour comprendre l'architecture

## 🎯 Objectifs Atteints

Une fois tous les tests passés :
- [ ] ✅ Le UserService est opérationnel
- [ ] ✅ L'authentification fonctionne
- [ ] ✅ Les opérations CRUD fonctionnent
- [ ] ✅ La sécurité est en place (seul ADMIN peut CRUD)
- [ ] ✅ L'héritage JPA fonctionne (Utilisateur → Admin/Enseignant)
- [ ] ✅ La base de données unique `plannoradb` est utilisée
- [ ] ✅ Le service est prêt pour les tests avec Postman

## 🎉 Prêt pour la Production

- [ ] Tous les tests passent
- [ ] La documentation est complète
- [ ] Les logs sont propres
- [ ] Les données de test sont créées
- [ ] Le service est intégré avec Eureka
- [ ] La sécurité est fonctionnelle

## 📞 Support

Si vous rencontrez des problèmes :
1. Consultez la section Dépannage ci-dessus
2. Vérifiez les logs des services
3. Consultez la documentation dans les fichiers MD
4. Vérifiez la base de données MySQL

---

**Bonne chance avec vos tests ! 🚀**
