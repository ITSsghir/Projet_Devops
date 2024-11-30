# 🐳 **Projet Docker Orchestration** : Déploiement d'une Application Multi-Tiers

## 📖 Description du Projet

Ce projet vise à déployer une application multi-tiers en utilisant Docker pour la conteneurisation. L'infrastructure comprend plusieurs services interconnectés, dont :

- **Application Spring** (2 versions : `spring-v1` et `spring-v2`)
- **Base de données MySQL**
- **PhpMyAdmin** pour superviser MySQL
- **Reverse Proxy Nginx** pour diriger le trafic vers les différentes versions de l'application
- **Sauvegarde automatique de la base de données MySQL**
- **SonarQube** pour l'analyse de la qualité du code (optionnel)

Le tout est orchestré avec Docker Compose pour faciliter la gestion des conteneurs et de leurs réseaux.

## 🛠️ Services Déployés

Voici la liste des services Docker utilisés dans ce projet :

### 1️⃣ **Applications Spring (V1 et V2)** 🌐
   - **But** : Déployer deux versions de l'application Spring dans des conteneurs séparés.
   - **Accès** : Ces services sont accessibles via le reverse proxy Nginx à l'adresse :
     - `http://localhost:10000/v1` pour `spring-v1`
     - `http://localhost:10000/v2` pour `spring-v2`

### 2️⃣ **Base de données MySQL (db)** 💾
   - **But** : Conteneur MySQL pour stocker les données des applications.
   - **Dépendances** : Utilise le script `monannuaire_mysql.sql` pour initialiser la base de données à partir du fichier SQL.
   - **Accès** : Accédez à la base via PhpMyAdmin ou directement depuis l'application.

### 3️⃣ **PhpMyAdmin** 💻
   - **But** : Interface Web pour la gestion de MySQL.
   - **Accès** : `http://localhost:8081`

### 4️⃣ **Reverse Proxy Nginx** 🔄
   - **But** : Rediriger les requêtes vers les différentes versions des applications Spring.
   - **Accès** : Le reverse proxy est exposé sur `http://localhost:10000`.

### 5️⃣ **Sauvegarde Automatique MySQL (db-backup)** 📂
   - **But** : Sauvegarder la base de données MySQL toutes les 12 heures à l'aide d'une tâche Cron.
   - **Accès** : Les sauvegardes sont stockées localement dans `./db-dump`.

### 6️⃣ **SonarQube** 📊
   - **But** : Analyser la qualité du code source des applications Spring.
   - **Accès** : `http://localhost:9000`
   - **Remarque** : L'intégration de SonarQube est optionnelle et vous pouvez l'utiliser pour suivre la qualité du code au fil du développement.

## 🌐 Réseaux Docker

### 1️⃣ **Réseau Principal (app-network)** 🌐
   - Utilisé pour les services de l'application : `spring-v1`, `spring-v2`, `nginx`, et `db`.
   - Ces services peuvent communiquer entre eux via ce réseau.

### 2️⃣ **Réseau de Sauvegarde (backup-network)** 💾
   - Utilisé pour la sauvegarde des données MySQL.
   - **`db-backup`** et **`db`** sont connectés à ce réseau.

### 3️⃣ **Réseau d'Administration (admin-network)** 🛠️
   - Utilisé pour la supervision de la base de données et de l'analyse du code.
   - **`phpmyadmin`** et **`sonarqube`** sont connectés à ce réseau.

## 📦 Volumes Docker

Les volumes Docker permettent de rendre les données persistantes, même lorsque les conteneurs sont arrêtés ou supprimés.

### 1️⃣ **Volume pour MySQL (db_data)** 💾
   - Contient les données persistantes de MySQL.
   - Associé au conteneur `db`.

### 2️⃣ **Volume pour SonarQube** 📊
   - **`sonarqube_data`** : Stocke les données persistantes de SonarQube.
   - **`sonarqube_extensions`** : Extensions SonarQube.
   - **`sonarqube_logs`** : Logs de SonarQube.
   - **`sonarqube_temp`** : Fichiers temporaires.

## 🔄 Flux entre les Services

Les services interagissent les uns avec les autres via des flux, définis par des réseaux et des volumes Docker.

### Flux via **Réseau** 🔄 :
Les conteneurs sur le même réseau peuvent communiquer entre eux via leurs noms de conteneur ou leurs adresses IP internes.

### Flux via **Volumes** 📂 :
Les volumes permettent de partager des données persistantes entre les conteneurs, comme les sauvegardes de MySQL.

### Exemple de Flux :
- **Spring-v1** et **Spring-v2** : Ces applications sont redirigées via Nginx.
- **MySQL** : Les données sont stockées dans un volume persistant (`db_data`), et des sauvegardes automatiques sont créées toutes les 12 heures dans `./db-dump`.

## 🚀 Comment Lancer le Projet ?

### Prérequis :
Assurez-vous que les outils suivants sont installés :
- Docker
- Docker Compose
- Git
- Maven 3.6+ (optionnel pour SonarQube)
- Java 17+

### Étapes pour Lancer le Projet :

1. Cloner le Répertoire :
   ```bash
   git clone https://github.com/younest9/tp-orchestration-docker.git
   cd tp-orchestration-docker
   ```
2. Configurez le fichier `.env` :
    Pour cela on'a choisi de faire un fichier .env qui vas contenire le detaille de notre bd

   ```dotenv
   MYSQL_ROOT_PASSWORD=mySecurePassword123 # A modifier si nécessaire
   CRON_TIME="0 */12 * * *" # Sauvegarde toutes les 12 heures
   MYSQL_DATABASE=devopsDB
   ```

3. Construisez les images Docker :

   ```bash
   docker-compose build
   ```


4. Lancez les conteneurs avec Docker Compose :

   ```bash
   docker-compose up -d
   ```

5. Accédez aux services :
   - **Application V1** : `http://localhost:10000/v1`
   - **Application V2** : `http://localhost:10000/v2`
   - **PhpMyAdmin** : `http://localhost:8081`
   - **SonarQube** : `http://localhost:9000`

## Tests

- **Tester les points d'entrée Spring** :

  ```bash
  curl http://localhost:10000/v1/api/persons
  curl http://localhost:10000/v2/api/persons
  ```
- **Vérifier les sauvegardes MySQL** :
Les sauvegardes seront générées toutes les 12 heures dans le dossier ./db-dump sous forme de fichiers .sql.

- **Vérifier l'analyse de la qualité du code avec SonarQube** :
- **`Accédez à SonarQube`** : http://localhost:9000
- **`Connectez-vous avec les identifiants par défaut (admin / admin).`**
- **`Créez un nouveau projet pour analyser le code source`**
- **Vérifier l'interface de PhpMyAdmin** :
Accédez à PhpMyAdmin : http://localhost:8081
Connectez-vous avec root / root pour gérer la base de données.

## 🗂️ Structure du Projet

```text
tp-orchestration-docker/
├── spring/                 # Code source de l'application Spring
├── db/                     # Fichiers liés à la base de données
│   ├── monannuaire_mysql.sql
├── nginx/                  # Configuration pour Nginx
│   └── nginx.conf
├── db-dump/                # Répertoire pour les sauvegardes MySQL (généré automatiquement)
├── docker-compose.yml      # Définition des services Docker
├── .env                    # Variables d'environnement (à personnaliser)
├── architecture.png        # Schéma de l'architecture
└── README.md               # Documentation du projet

```
## Ce qui a été implémenté (et pourquoi)

Dans ce projet, plusieurs pratiques ont été mises en place pour assurer l'efficacité, la sécurité et la pérennité du système. Voici un aperçu détaillé de chaque aspect de l'infrastructure :

### 1. 🌐 **Isolation des Réseaux Docker**
Nous avons créé des réseaux Docker dédiés pour chaque service, permettant ainsi une isolation optimale. Cela garantit que chaque service (comme les applications Spring ou la base de données MySQL) fonctionne indépendamment des autres, tout en permettant une communication fluide au sein du même réseau.

- **Pourquoi ?** Cette approche permet de limiter les interférences entre les services, d'augmenter la sécurité et de gérer plus facilement les permissions et les connexions entre services.
- **Réseaux utilisés** : Par exemple, les services Spring et MySQL utilisent le réseau `spring-network`, tandis que des réseaux comme `backup-network` sont réservés aux services de sauvegarde.
- **Réseaux gérés par Docker Compose** : Ces réseaux sont créés automatiquement lors de l'exécution du fichier `docker-compose.yml`.

### 2. 💾 **Persistances des Données**
Pour garantir la conservation des données même lorsque les conteneurs sont arrêtés, des volumes Docker sont utilisés. 

- **Pourquoi ?** Les volumes Docker assurent que les données stockées dans des conteneurs comme MySQL persistent sur l'hôte, ce qui est crucial pour une base de données.
- **Méthode utilisée** : Nous avons utilisé des **bind mounts** pour les sauvegardes de la base de données MySQL et des **volumes Docker** pour stocker les données persistantes.
- **Avantage** : En cas de redémarrage ou de suppression des conteneurs, les données sont toujours disponibles.

### 3. ⏳ **Sauvegarde Automatique de la Base de Données**
Pour protéger les données contre toute perte, nous avons configuré un service de sauvegarde MySQL automatique. 

- **Pourquoi ?** Les sauvegardes régulières permettent de récupérer les données en cas de panne ou de corruption.
- **Planification** : Les sauvegardes sont effectuées toutes les 12 heures via une tâche Cron, stockées dans un dossier dédié (`./db-dump`).
- **Outil utilisé** : Nous avons utilisé un conteneur spécialisé, `mysql-cron-backup`, pour gérer cette tâche automatiquement.

### 4. 🔄 **Reverse Proxy avec Nginx**
Un reverse proxy Nginx a été configuré pour gérer le trafic entrant et rediriger les requêtes vers les versions appropriées des applications Spring.

- **Pourquoi ?** Nginx permet de séparer les versions de l'application tout en maintenant une seule interface d'accès pour l'utilisateur.
- **Configuration** : Les requêtes vers `http://localhost:10000/v1` sont redirigées vers l’application Spring version 1, et celles vers `/v2` vont vers la version 2 de l’application.
- **Avantage** : Cela permet une gestion simplifiée de plusieurs versions d’une même application, et d'ajouter une couche de sécurité et de performance.

### 5. 🛠️ **Administration et Supervision**
Pour la gestion et la supervision de la base de données MySQL et de la qualité du code, des outils comme **PhpMyAdmin** et **SonarQube** ont été intégrés.

- **PhpMyAdmin** : Il fournit une interface web accessible via le port `8081` pour interagir avec la base de données MySQL. Cela permet une gestion facile des tables, des utilisateurs et des requêtes SQL.
- **SonarQube** : SonarQube analyse la qualité du code source des applications Spring et fournit des rapports détaillés sur la couverture du code, les vulnérabilités et les erreurs. Il est accessible via le port `9000`.

---

## 🔒 License

Ce projet est réalisé dans le cadre du module **Pilotage des Projets DevOps** du parcours **Master MIAGE** à l'**Université Paul Sabatier** (Toulouse).

Créé par : **SGHIR Anas** et **Ameni Necib**.
