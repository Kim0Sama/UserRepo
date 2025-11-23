# 👋 Bienvenue dans le UserService !

## 🎉 Félicitations !

Le **UserService** a été entièrement implémenté et est prêt à être utilisé !

## 🚀 Par où commencer ?

### 1️⃣ Première fois ici ?
Consultez **[INDEX.md](INDEX.md)** pour naviguer dans la documentation.

### 2️⃣ Vous voulez démarrer rapidement ?
Suivez le **[DEMARRAGE-RAPIDE.md](DEMARRAGE-RAPIDE.md)** (5 minutes).

### 3️⃣ Vous voulez tout comprendre ?
Lisez le **[README.md](README.md)** complet.

## 📋 Ce qui a été implémenté

✅ **Modèle de données** avec héritage (Utilisateur → Admin/Enseignant)  
✅ **CRUD complet** des utilisateurs  
✅ **Authentification JWT** et sécurité par rôle  
✅ **Base de données unique** (plannoradb)  
✅ **Validation** des données  
✅ **Gestion des erreurs**  
✅ **Documentation complète** (12 fichiers)  
✅ **Tests Postman** prêts à l'emploi  

## 🎯 Démarrage en 3 Étapes

### Étape 1 : Prérequis
```bash
# Vérifier Java
java -version  # Doit être 17+

# Créer la base de données
mysql -u root -p
CREATE DATABASE plannoradb;
```

### Étape 2 : Démarrer les Services
```bash
# Terminal 1 : Eureka (8761)
cd EurekaService/eureka/eureka
mvn spring-boot:run

# Terminal 2 : Auth Service (8082)
cd AuthentificationService/Authentification/authentification
mvn spring-boot:run

# Terminal 3 : User Service (8083)
cd UserService/user-service
mvn spring-boot:run
```

### Étape 3 : Tester avec Postman
```
1. POST http://localhost:8082/api/auth/login
   Body: {"email":"admin@plannora.com","password":"admin123"}
   
2. Copier le token JWT

3. POST http://localhost:8083/api/utilisateurs/enseignant
   Authorization: Bearer YOUR_TOKEN
   Body: {
     "email":"prof@plannora.com",
     "mdp":"password123",
     "nomUser":"Dupont",
     "prenomUser":"Jean",
     "telephone":"0612345678",
     "specialite":"Informatique",
     "departement":"Génie Logiciel"
   }
```

## 📚 Documentation Complète

| Document | Description | Temps |
|----------|-------------|-------|
| [INDEX.md](INDEX.md) | Navigation dans la doc | 2 min |
| [DEMARRAGE-RAPIDE.md](DEMARRAGE-RAPIDE.md) | Démarrage rapide | 5 min |
| [CHECKLIST-DEMARRAGE.md](CHECKLIST-DEMARRAGE.md) | Checklist de vérification | 10 min |
| [README.md](README.md) | Documentation complète | 20 min |
| [ARCHITECTURE.md](ARCHITECTURE.md) | Architecture détaillée | 15 min |
| [GUIDE-TESTS-POSTMAN.md](GUIDE-TESTS-POSTMAN.md) | Tests Postman | 15 min |
| [EXEMPLES-CURL.md](EXEMPLES-CURL.md) | Exemples cURL | 10 min |
| [FLUX-DONNEES.md](FLUX-DONNEES.md) | Diagrammes de flux | 10 min |
| [SYNTHESE-VISUELLE.md](SYNTHESE-VISUELLE.md) | Synthèse visuelle | 5 min |
| [IMPLEMENTATION-COMPLETE.md](IMPLEMENTATION-COMPLETE.md) | Résumé implémentation | 10 min |
| [RESUME-IMPLEMENTATION.md](RESUME-IMPLEMENTATION.md) | Résumé court | 5 min |
| [PROCHAINES-ETAPES.md](PROCHAINES-ETAPES.md) | Roadmap | 10 min |

## 🎓 Fonctionnalités Principales

### Pour les Administrateurs
- ✅ Créer des utilisateurs (Admin et Enseignants)
- ✅ Lister tous les utilisateurs
- ✅ Consulter un utilisateur
- ✅ Modifier un utilisateur
- ✅ Supprimer un utilisateur

### Pour les Enseignants
- ✅ Consulter la liste des enseignants
- ⏳ Consulter leur emploi du temps (à venir)
- ⏳ Gérer leurs UE (à venir)

## 🔐 Sécurité

- **JWT** : Authentification par tokens
- **BCrypt** : Mots de passe hashés
- **Contrôle d'accès** : Seul l'ADMIN peut CRUD les utilisateurs
- **Validation** : Toutes les entrées sont validées

## 🗄️ Base de Données

**Base unique** : `plannoradb`

**Tables** :
- `utilisateurs` (parent)
- `administrateurs` (enfant)
- `enseignants` (enfant)

**Compte par défaut** :
- Email : admin@plannora.com
- Mot de passe : admin123

## 🧪 Tests

### Collection Postman
Utilisez le fichier **[test-user-api.http](test-user-api.http)**

### Exemples cURL
Consultez **[EXEMPLES-CURL.md](EXEMPLES-CURL.md)**

## 🎯 Endpoints Disponibles

| Méthode | Endpoint | Rôle | Description |
|---------|----------|------|-------------|
| POST | `/api/utilisateurs` | ADMIN | Créer un utilisateur |
| POST | `/api/utilisateurs/enseignant` | ADMIN | Créer un enseignant |
| GET | `/api/utilisateurs` | ADMIN | Lister tous |
| GET | `/api/utilisateurs/enseignants` | ADMIN/ENSEIGNANT | Lister enseignants |
| GET | `/api/utilisateurs/{id}` | ADMIN | Obtenir un utilisateur |
| PUT | `/api/utilisateurs/{id}` | ADMIN | Modifier |
| DELETE | `/api/utilisateurs/{id}` | ADMIN | Supprimer |

## 🛠️ Technologies

- Spring Boot 3.5.7
- Spring Security + JWT
- Spring Data JPA
- MySQL
- Lombok
- Spring Cloud Eureka

## 📊 Statistiques

- **17 fichiers Java** créés
- **12 fichiers de documentation** créés
- **7 endpoints REST** implémentés
- **3 tables MySQL** créées
- **~1000+ lignes de code**

## 💡 Conseils

1. **Commencez par** [DEMARRAGE-RAPIDE.md](DEMARRAGE-RAPIDE.md)
2. **En cas de problème**, consultez [CHECKLIST-DEMARRAGE.md](CHECKLIST-DEMARRAGE.md)
3. **Pour comprendre l'architecture**, lisez [ARCHITECTURE.md](ARCHITECTURE.md)
4. **Pour tester**, utilisez [GUIDE-TESTS-POSTMAN.md](GUIDE-TESTS-POSTMAN.md)

## 🎨 Diagrammes

Consultez :
- [ARCHITECTURE.md](ARCHITECTURE.md) - Diagrammes de classes
- [FLUX-DONNEES.md](FLUX-DONNEES.md) - Diagrammes de flux
- [SYNTHESE-VISUELLE.md](SYNTHESE-VISUELLE.md) - Synthèse visuelle

## 🚀 Prochaines Étapes

Consultez [PROCHAINES-ETAPES.md](PROCHAINES-ETAPES.md) pour :
- Tests unitaires et d'intégration
- Pagination et recherche
- Gestion des UE
- Cache et monitoring
- Et bien plus !

## 📞 Besoin d'Aide ?

1. Consultez [INDEX.md](INDEX.md) pour trouver la bonne documentation
2. Vérifiez [CHECKLIST-DEMARRAGE.md](CHECKLIST-DEMARRAGE.md) pour le dépannage
3. Lisez les logs des services
4. Vérifiez la base de données MySQL

## 🎉 Prêt à Commencer !

Le UserService est **entièrement fonctionnel** et **prêt à être testé** !

**Commencez par** :
1. Lire [DEMARRAGE-RAPIDE.md](DEMARRAGE-RAPIDE.md)
2. Démarrer les services
3. Tester avec Postman
4. Créer vos premiers utilisateurs !

---

**Bon développement ! 🚀**

*Pour toute question, consultez la documentation ou les logs des services.*
