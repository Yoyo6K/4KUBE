# Projet Kubernetes avec Helm

## Introduction
Ce projet déploie une application distribuée sur Kubernetes en utilisant Helm. L'application se compose de plusieurs microservices, dont un API Gateway, une base de données MongoDB, et d'autres services spécifiques.

## Prérequis
- Docker Desktop avec Kubernetes intégré
- Helm v3
- kubectl configuré pour communiquer avec Kubernetes sur Docker Desktop

## Structure du Répertoire

- `templates/` : Contient les templates pour les ressources Kubernetes. Ce répertoire inclut des templates pour les divers composants tels que les déploiements, les services et les volumes persistants.
- `values.yaml` : Fichier de configuration centralisé pour personnaliser le déploiement. Il contient des paramètres clés comme les images de conteneurs, les tags, les ports et d'autres configurations spécifiques à l'application.
- `secrets.yaml` : Ce fichier est utilisé pour définir les objets secrets de Kubernetes. Il contient des données sensibles telles que les variables d'environnement qui ne devraient pas être exposées publiquement.

## Architecture

Ce projet met en place une application distribuée composée de plusieurs microservices, déployée sur Kubernetes via Docker Desktop. L'architecture est conçue pour favoriser la scalabilité, la résilience et la modularité. Voici les principaux composants :

1. **API Gateway (fleetman-api-gateway)** :
   - Sert de point d'entrée unique pour les requêtes externes.
   - Route les requêtes vers les services appropriés.
   - Simplifie la complexité du réseau pour les clients.

2. **Base de Données MongoDB (fleetman-mongodb)** :
   - Stocke les données de l'application de manière persistante.
   - Utilisée par d'autres services pour la récupération et la persistance des données.

3. **Position Simulator (fleetman-position-simulator)** :
   - Génère des positions fictives pour simuler le mouvement des véhicules.
   - Envoie les données simulées à la queue pour traitement.

4. **Position Tracker (fleetman-position-tracker)** :
   - Consomme les positions de la queue.
   - Stocke ces informations dans MongoDB.
   - Fournit une API pour accéder aux données de position.

5. **Queue (fleetman-queue)** :
   - Queue Apache ActiveMQ qui sert de tampon entre le simulateur de position et le suivi de position.
   - Assure la livraison fiable des messages.

6. **Web App (fleetman-webapp)** :
   - Interface utilisateur pour interagir avec l'application.
   - Récupère les données via l'API Gateway.

## Déploiement

Le déploiement de cette application sur un environnement Kubernetes via Docker Desktop est facilité grâce à Helm, un gestionnaire de packages pour Kubernetes. Voici les étapes pour déployer l'application :

### Étapes de déploiement

1. **Clonage du projet** :
   - Clonez le dépôt Git contenant le projet sur votre machine locale.
   ```
     git clone https://github.com/Yoyo6K/4KUBE.git
   ```

2. **Configuration Helm** :
   - Naviguez jusqu'au dossier du projet cloné.
   ```
     cd [NOM_DU_DOSSIER_DU_PROJET]
   ```

3. **Génération des manifestes Kubernetes** :
   - Si les manifestes Kubernetes ne sont pas déjà présents dans le projet, générez-les en exécutant la commande suivante :
     ```
     helm template . > manifest.yaml
     ```
   - Cette commande crée un fichier `manifest.yaml` qui contient toutes les configurations Kubernetes nécessaires pour le déploiement.

4. **Déploiement avec Helm** :
   - Utilisez Helm pour déployer l'application dans votre cluster Kubernetes :
     ```
     helm install [NOM] .
     ```

5. **Vérification du déploiement** :
   - Vérifiez que tous les services et pods sont correctement déployés et en cours d'exécution :
     ```
     kubectl get pods
     ```
   - Vous devriez voir la liste des pods associées à votre déploiement.

### Nettoyage

Pour supprimer le déploiement de l'application :

- Exécutez la commande suivante :
   ```
   helm uninstall [NOM]
   ```

## Services et Ports

L'architecture de l'application est structurée autour de plusieurs services, chacun ayant un rôle spécifique dans le système global. Voici une brève description des services et des ports utilisés :

- **Fleetman API Gateway** : 
  - Port : 8080
  - Description : Sert de point d'entrée pour les requêtes API, agissant comme un proxy pour les autres services.

- **Fleetman Position Tracker** : 
  - Port : 8080
  - Description : Traite et stocke les positions des véhicules. Ce service reçoit des données du simulateur de position et les envoie à la base de données.

- **Fleetman Queue (ActiveMQ)** : 
  - Ports : 8161 (interface web), 61616 (communication AMQP)
  - Description : Gère la queue de messages pour l'application, utilisée pour la communication entre le simulateur de positions et le tracker.

- **Fleetman Position Simulator** : 
  - Pas de port exposé.
  - Description : Simule les données de position des véhicules et les envoie à la queue.

- **Fleetman MongoDB** : 
  - Port : 27017
  - Description : Base de données NoSQL utilisée pour stocker les informations de position des véhicules.

- **Fleetman Web App** : 
  - Port : 80
  - Description : Interface utilisateur web pour afficher les positions des véhicules et d'autres informations pertinentes.

Ces services communiquent entre eux à travers les ports définis, permettant un flux de données efficace et une bonne séparation des responsabilités dans l'architecture de l'application.

## Gestion des Secrets

Dans notre projet Kubernetes, la gestion des secrets est un aspect crucial pour la sécurité et l'intégrité des informations sensibles. Bien que l'application actuelle ne traite pas de données hautement confidentielles, l'implémentation d'un système de secrets est essentielle pour préparer l'infrastructure à des cas d'utilisation plus complexes et sécurisés.

### Cas d'Usage Hypothétique : MongoDB

Prenons l'exemple de MongoDB utilisé dans notre architecture. Si MongoDB nécessitait un nom d'utilisateur et un mot de passe pour l'accès, ces informations devraient idéalement être stockées en tant que secrets dans Kubernetes. Cela permettrait d'accéder à la base de données de manière sécurisée sans exposer les informations d'identification sensibles.
Voici un exemple de structure pour ce fichier :

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mongo-secret
type: Opaque
data:
  username: [NOM_UTILISATEUR_CRYPTÉ_EN_BASE_64]
  password: [MOT_DE_PASSE_CRYPTÉ_EN_BASE_64]
```

## Contact
Pour toute question ou demande concernant ce projet, vous pouvez me contacter directement :

- Nom : CASSIS Yoann
- Email : yoann.cassis@supinfo.com
- GitHub : https://github.com/Yoyo6K

---