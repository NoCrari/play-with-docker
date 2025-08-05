# 🐳 Play with Docker

Un projet d'apprentissage pratique pour découvrir Docker et les concepts de conteneurisation à travers la création d'une architecture de microservices.

## 📋 Table des matières

- [Vue d'ensemble](#vue-densemble)
- [Architecture](#architecture)
- [Prérequis](#prérequis)
- [Installation](#installation)
- [Utilisation](#utilisation)
- [Tests](#tests)
- [Structure du projet](#structure-du-projet)
- [Technologies utilisées](#technologies-utilisées)
- [Concepts Docker appris](#concepts-docker-appris)

## 🎯 Vue d'ensemble

Ce projet implémente une architecture de microservices complète utilisant Docker et Docker Compose. Il comprend :

- **API Gateway** - Point d'entrée unique pour toutes les requêtes
- **Service Movies** (Inventory) - Gestion du catalogue de films
- **Service Billing** - Traitement des commandes via RabbitMQ
- **Bases de données PostgreSQL** - Stockage persistant
- **RabbitMQ** - File de messages asynchrone

## 🏗️ Architecture

![architecture](./screenshots/play-with-containers-py.png)

## 📦 Services

### API Gateway (Port 3000)
- Point d'entrée unique pour toutes les requêtes
- Route les requêtes vers les services appropriés
- Gère l'envoi de messages vers RabbitMQ

### Movies Service (Inventory)
- Gestion du catalogue de films
- Base de données PostgreSQL dédiée
- API REST complète (CRUD)

### Billing Service
- Traitement asynchrone des commandes
- Consommation des messages RabbitMQ
- Stockage des commandes en base

### RabbitMQ
- File de messages pour le traitement asynchrone
- Interface web disponible sur le port 15672

## 🛠️ Prérequis

- **Docker** (version 20.10+)
- **Docker Compose** (version 2.0+)
- **Git**
- **Postman** (pour les tests)

## 🚀 Installation

### 1. Cloner le projet
```bash
git clone <votre-repo-url>
cd play-with-containers
```

### 2. Configurer l'environnement
Créez un fichier `.env` à la racine du projet :

```env
# Database Configuration
INVENTORY_DB_USER=...
INVENTORY_DB_NAME=...
INVENTORY_DB_PASSWORD=...

BILLING_DB_USER=...
BILLING_DB_NAME=...
BILLING_DB_PASSWORD=...

# Application Ports
INVENTORY_APP_PORT=...
BILLING_APP_PORT=...
APIGATEWAY_PORT=..

# RabbitMQ Configuration
RABBITMQ_USER=...
RABBITMQ_PASSWORD=...
RABBITMQ_PORT=...
RABBITMQ_QUEUE=...
```

### 3. Lancer l'application
```bash
# Construire et démarrer tous les services
docker compose up --build

# Ou en arrière-plan
docker compose up --build -d

# Ou voir le dossier Makefile pour les alias ' make '...' '
```

### 4. Vérifier le déploiement
```bash
# Voir les logs de tous les services
docker compose logs 'service_name'

# Voir les services actifs
docker compose ps
```

## 🧪 Utilisation

### Endpoints disponibles

#### Movies Service (via API Gateway)
```http
# Lister tous les films
GET http://localhost:3000/api/movies

# Créer un nouveau film
POST http://localhost:3000/api/movies
Content-Type: application/json

{
  "title": "Inception",

}

### Récupérer un film par ID
GET http://localhost:3000/api/movies/{id}

### Mettre à jour un film
PUT http://localhost:3000/api/movies/{id}

### Supprimer un film
DELETE http://localhost:3000/api/movies/{id}
```

### Billing Service (via RabbitMQ)
```http
# Créer une commande (envoyée via RabbitMQ)
POST http://localhost:3000/api/billing/
Content-Type: application/json

{
  "movie_id": 1,
  "quantity": 2,
  "customer_email": "client@example.com",
  "price": 15.99
}
```

### Interface RabbitMQ
Accédez à l'interface web RabbitMQ :
- URL : http://localhost:15672
- Username : `admin` (ou valeur de `RABBITMQ_USER`)
- Password : `admin123` (ou valeur de `RABBITMQ_PASSWORD`)

## 🧪 Tests

### Tests avec Postman

1. **Importer la collection** (si disponible) ou créer les requêtes manuellement
2. **Tester le service Movies** :
   ```
   GET http://localhost:3000/api/movies
   ```
3. **Tester le service Billing** :
   ```
   POST http://localhost:3000/api/billing/
   ```

### Tests de connectivité
```bash
# Tester la connectivité des services
curl http://localhost:3000/api/movies
curl -X POST http://localhost:3000/api/billing/ \
  -H "Content-Type: application/json" \
  -d '{"movie_id":1,"quantity":1,"customer_email":"test@test.com","price":10.99}'
```

### Surveillance des logs
```bash
# Logs en temps réel de tous les services
docker compose logs -f

# Logs spécifiques
docker compose logs -f api-gateway
docker compose logs -f billing-app
docker compose logs -f rabbit-queue
```

## 📁 Structure du projet

![tree](./screenshots/tree.png)

## 🛠️ Technologies utilisées

- **Docker & Docker Compose** - Conteneurisation et orchestration
- **Python Flask** - Framework web pour les API
- **PostgreSQL** - Base de données relationnelle
- **RabbitMQ** - Message broker pour le traitement asynchrone
- **Alpine Linux** - Image de base légère pour les conteneurs
- **Nginx** (optionnel) - Reverse proxy et load balancer

## 📚 Concepts Docker appris

### Conteneurs
- Création et gestion de conteneurs Docker
- Isolation des processus et des ressources
- Communication inter-conteneurs

### Images Docker
- Construction d'images personnalisées avec Dockerfile
- Optimisation des layers pour réduire la taille
- Utilisation d'images de base légères (Alpine)

### Réseaux Docker
- Création de réseaux personnalisés
- Communication entre services par nom de conteneur
- Isolation réseau

### Volumes Docker
- Persistance des données
- Partage de données entre conteneurs
- Séparation des données et de l'application

### Docker Compose
- Orchestration multi-conteneurs
- Définition de services, réseaux et volumes
- Gestion des dépendances entre services
- Variables d'environnement et fichiers .env

## 🔧 Commandes utiles

```bash
# Démarrer les services
docker compose up -d

# Reconstruire les images
docker compose up --build

# Arrêter les services
docker compose down

# Voir les logs
docker compose logs -f [service_name]

# Entrer dans un conteneur
docker compose exec [service_name] sh

# Voir l'état des services
docker compose ps

# Supprimer tout (conteneurs, réseaux, volumes)
docker compose down -v --remove-orphans
```

## 🚨 Dépannage

### Problèmes courants

1. **Erreur de connexion RabbitMQ**
   ```bash
   # Vérifier les logs RabbitMQ
   docker compose logs rabbit-queue
   
   # Redémarrer le service
   docker compose restart rabbit-queue
   ```

2. **Base de données non accessible**
   ```bash
   # Vérifier le healthcheck
   docker compose ps
   
   # Vérifier les logs de la DB
   docker compose logs inventory-db
   ```

3. **Port déjà utilisé**
   ```bash
   # Modifier les ports dans le fichier .env
   # Ou arrêter les services qui utilisent le port
   ```

## 📝 Notes importantes

- **Sécurité** : Ne commitez jamais le fichier `.env` contenant vos mots de passe
- **Performance** : Les conteneurs utilisent des images optimisées (Alpine)
- **Monitoring** : Surveillez les logs pour identifier les problèmes
- **Scalabilité** : L'architecture permet facilement l'ajout de nouveaux services

## Bonus ()
Use your crud-master services for the solution of this project.

If you complete the mandatory part successfully, and you still have free time, you can implement anything that you feel deserves to be a bonus.

Challenge yourself!


Does the README.md file contain all the required information to run and manage the solution (prerequisites, configuration, setup, usage, etc)?


What are containers and what are their advantages?

What is the difference between containers and virtual machines?

What is Docker and what is it used for?

What is a microservices' architecture?

Why do we use microservices architecture?

What is a queue and what is it used for?

What is RabbitMQ?

What is a Dockerfile?

Explain the instructions used on the Dockerfile.
