# Tutoriel : Installer MongoDB avec Docker sur Debian et Importer des Données JSON

# Tutoriel : Installer MongoDB avec Docker sur Debian et Importer des Données JSON

## Partie 1 : Installation et Mise en Place

- Installer Docker
- Tirer l'image Docker de MongoDB
- Lancer un conteneur MongoDB
- Vérifier que MongoDB fonctionne
- Configurer MongoDB (facultatif)
- Arrêter et redémarrer le conteneur MongoDB
- Importer des données JSON dans MongoDB
- Vérifier l'importation

## Partie 2 : Utilisation de la Base de Données

- Introduction : Utilisation de la base de données
- Vérifier que les données ont été importées
- Comprendre la structure d'un document
- Afficher la liste des films d'action
- Afficher le nombre de films d'action
- Afficher les films d'action produits en France
- Afficher les films d'action produits en France en 1963
- Afficher les films d'action réalisés en France sans les grades
- Ne pas afficher les identifiants
- Afficher les titres et les grades des films d'action réalisés en France sans leurs identifiants
- Afficher les titres et les notes des films d'action réalisés en France et ayant obtenu une note supérieure à 10
- Afficher les films d'action réalisés en France ayant obtenu que des notes supérieures à 10
- Afficher les différents genres de la base `lesfilms`
- Afficher les différents grades attribués
- Afficher tous les films dans lesquels joue au moins un des artistes suivants ["artist:4","artist:18","artist:11"]
- Afficher tous les films qui n'ont pas de résumé
- Afficher tous les films tournés avec Leonardo DiCaprio en 1997
- Afficher les films tournés avec Leonardo DiCaprio ou en 1997


## Partie 1 : Installation et Mise en Place

### Étape 1 : Installer Docker

1. **Mettre à jour les paquets existants :**
   ```bash
   sudo apt update
   ```

2. **Installer les dépendances nécessaires :**
   ```bash
   sudo apt install apt-transport-https ca-certificates curl software-properties-common
   ```

3. **Ajouter la clé GPG officielle de Docker :**
   ```bash
   curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   ```

4. **Ajouter le dépôt Docker à la liste des sources APT :**
   ```bash
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

5. **Mettre à jour les paquets à nouveau :**
   ```bash
   sudo apt update
   ```

6. **Installer Docker :**
   ```bash
   sudo apt install docker-ce
   ```

7. **Vérifier que Docker est installé correctement :**
   ```bash
   sudo systemctl status docker
   ```

### Étape 2 : Tirer l'image Docker de MongoDB

1. **Tirer l'image officielle de MongoDB :**
   ```bash
   sudo docker pull mongo
   ```

### Étape 3 : Lancer un conteneur MongoDB

1. **Créer un répertoire pour les données MongoDB (facultatif mais recommandé) :**
   ```bash
   sudo mkdir -p /data/db
   ```

2. **Lancer un conteneur MongoDB :**
   ```bash
   sudo docker run --name mongodb -v /data/db:/data/db -d mongo
   ```
   - `--name mongodb` : Nomme le conteneur `mongodb`.
   - `-v /data/db:/data/db` : Monte le répertoire local `/data/db` dans le conteneur pour persister les données.
   - `-d` : Lance le conteneur en mode détaché.

### Étape 4 : Vérifier que MongoDB fonctionne

1. **Vérifier que le conteneur MongoDB est en cours d'exécution :**
   ```bash
   sudo docker ps
   ```

2. **Se connecter à MongoDB à partir du conteneur :**
   ```bash
   sudo docker exec -it mongodb mongosh
   ```
   Cela vous connectera à l'interface shell de MongoDB.

### Étape 5 : Configurer MongoDB (facultatif)

Si vous avez besoin de configurer des utilisateurs, des bases de données, ou d'autres paramètres, vous pouvez le faire directement depuis l'interface shell de MongoDB.

### Étape 6 : Arrêter et redémarrer le conteneur MongoDB

1. **Arrêter le conteneur MongoDB :**
   ```bash
   sudo docker stop mongodb
   ```

2. **Redémarrer le conteneur MongoDB :**
   ```bash
   sudo docker start mongodb
   ```

### Étape 7 : Importer des données JSON dans MongoDB

1. **Préparer le fichier JSON**

   Assurez-vous que le fichier `films.json` est accessible sur votre machine hôte.

2. **Copier le fichier JSON dans le conteneur**
   ```bash
   sudo docker cp films.json mongodb:/films.json
   ```

3. **Exécuter la commande `mongoimport`**
   ```bash
   sudo docker exec -it mongodb mongoimport --db lesfilms --collection films /films.json --jsonArray
   ```
   - `sudo docker exec -it mongodb` : Exécute une commande dans le conteneur `mongodb`.
   - `mongoimport --db lesfilms --collection films /films.json --jsonArray` : Importe les données du fichier `films.json` dans la base de données `lesfilms` et la collection `films`.

### Étape 8 : Vérifier l'importation

1. **Se connecter à MongoDB pour vérifier l'importation :**
   ```bash
   sudo docker exec -it mongodb mongosh
   ```

2. **Sélectionner la base de données `lesfilms` :**
   ```bash
   use lesfilms
   ```

3. **Vérifier les documents dans la collection `films` :**
   ```bash
   db.films.find().pretty()
   ```
   Cela devrait afficher les documents importés depuis le fichier `films.json`.

## Partie 2 : Utilisation de la Base de Données

### Introduction : Utilisation de la base de données

Avant de commencer, il est important de comprendre comment interagir avec la base de données MongoDB. Les commandes suivent généralement le format `db.<collection>.<fonction>()`, où `db` représente la base de données actuelle, `<collection>` est le nom de la collection (ici, `films`), et `<fonction>` est l'opération que vous souhaitez effectuer (comme `find`, `countDocuments`, etc.).

### 1 : Vérifier que les données ont été importées

1. **Compter les documents dans la collection `films` :**
   ```sh
   db.films.countDocuments()
   ```
   - `countDocuments` permet de compter le nombre total de documents dans la collection `films`. C'est une fonction d'agrégation qui retourne le nombre de documents correspondant à un filtre donné (ou à tous les documents si aucun filtre n'est spécifié).

### 2 : Comprendre la structure d'un document

1. **Récupérer un seul document pour comprendre sa structure :**
   ```sh
   db.films.findOne()
   ```
   - `findOne` récupère un seul document de la collection `films`, ce qui permet de visualiser la structure des documents. Cette fonction est utile pour comprendre les champs et les types de données présents dans la collection.

### 3 : Afficher la liste des films d'action

1. **Afficher les films d'action :**
   ```sh
   db.films.find({ genre: "Action" })
   ```
   - `find` avec un filtre permet de récupérer les documents correspondant à ce filtre. Ici, nous filtrons les films du genre "Action". Un filtre est une condition qui doit être satisfaite pour qu'un document soit inclus dans les résultats. Les filtres sont construits en utilisant des paires clé-valeur où la clé est le nom du champ et la valeur est la condition à satisfaire.

### 4 : Afficher le nombre de films d'action

1. **Compter les films d'action :**
   ```sh
   db.films.countDocuments({ genre: "Action" })
   ```
   - `countDocuments` avec un filtre permet de compter les documents correspondant à ce filtre. Ici, nous comptons les films du genre "Action".

### 5 : Afficher les films d'action produits en France

1. **Afficher les films d'action produits en France :**
   ```sh
   db.films.find({ genre: "Action", country: "FR" })
   ```
   - Utilisation de plusieurs critères dans le filtre pour affiner la recherche. Ici, nous filtrons les films du genre "Action" produits en France (`country: "FR"`). Les filtres peuvent combiner plusieurs conditions en utilisant des objets imbriqués.

### 6 : Afficher les films d'action produits en France en 1963

1. **Afficher les films d'action produits en France en 1963 :**
   ```sh
   db.films.find({ genre: "Action", country: "FR", year: 1963 })
   ```
   - Ajout d'un critère supplémentaire pour affiner encore plus la recherche. Ici, nous filtrons les films du genre "Action" produits en France en 1963.

### 7 : Afficher les films d'action réalisés en France sans les grades

1. **Afficher les films d'action réalisés en France sans les grades :**
   ```sh
   db.films.find({ genre: "Action", country: "FR" }, { grades: 0 })
   ```
   - Utilisation de la projection pour exclure certains champs des résultats. Ici, nous excluons le champ `grades` des résultats. Une projection permet de spécifier les champs à inclure ou à exclure dans les résultats. En définissant un champ à `0`, on l'exclut des résultats.

### 8 : Ne pas afficher les identifiants

1. **Afficher les films d'action réalisés en France sans les identifiants :**
   ```sh
   db.films.find({ genre: "Action", country: "FR" }, { _id: 0 })
   ```
   - Exclusion du champ `_id` pour ne pas afficher les identifiants. Ici, nous excluons le champ `_id` des résultats.

### 9 : Afficher les titres et les grades des films d'action réalisés en France sans leurs identifiants

1. **Afficher les titres et les grades des films d'action réalisés en France sans leurs identifiants :**
   ```sh
   db.films.find({ genre: "Action", country: "FR" }, { _id: 0, title: 1, grades: 1 })
   ```
   - Projection pour inclure uniquement les champs `title` et `grades`. Ici, nous incluons uniquement les champs `title` et `grades` dans les résultats. En définissant un champ à `1`, on l'inclut dans les résultats.

### 10 : Afficher les titres et les notes des films d'action réalisés en France et ayant obtenu une note supérieure à 10

1. **Afficher les titres et les notes des films d'action réalisés en France et ayant obtenu une note supérieure à 10 :**
   ```sh
   db.films.find({ genre: "Action", country: "FR", "grades.note": { $gt: 10 } }, { _id: 0, title: 1, grades: 1 })
   ```
   - Utilisation de l'opérateur `$gt` pour filtrer les notes supérieures à 10. Ici, nous filtrons les films ayant une note supérieure à 10. Les opérateurs de comparaison comme `$gt` (greater than) permettent de filtrer les documents en fonction de la valeur d'un champ.

### 11 : Afficher les films d'action réalisés en France ayant obtenu que des notes supérieures à 10

1. **Afficher les films d'action réalisés en France ayant obtenu que des notes supérieures à 10 :**
   ```sh
   db.films.find({ genre: "Action", country: "FR", grades: { $not: { $elemMatch: { note: { $lte: 10 } } } } }, { _id: 0, title: 1, grades: 1 })
   ```
   - Utilisation de `$elemMatch` et `$not` pour filtrer les documents où toutes les notes sont supérieures à 10. `$elemMatch` permet de filtrer les éléments d'un tableau imbriqué, et `$not` permet d'inverser la condition. Ici, nous filtrons les films où toutes les notes sont supérieures à 10.

### 12 : Afficher les différents genres de la base `lesfilms`

1. **Afficher les différents genres :**
   ```sh
   db.films.distinct("genre")
   ```
   - `distinct` permet de récupérer les valeurs uniques d'un champ. Ici, nous récupérons les genres uniques de la collection `films`.

### 13 : Afficher les différents grades attribués

1. **Afficher les différents grades attribués :**
   ```sh
   db.films.distinct("grades.grade")
   ```
   - `distinct` sur un champ imbriqué pour récupérer les valeurs uniques. Ici, nous récupérons les grades uniques attribués aux films.

### 14 : Afficher tous les films dans lesquels joue au moins un des artistes suivants ["artist:4","artist:18","artist:11"]

1. **Afficher tous les films dans lesquels joue au moins un des artistes suivants :**
   ```sh
   db.films.find({ "actors._id": { $in: ["artist:4", "artist:18", "artist:11"] } })
   ```
   - Utilisation de `$in` pour filtrer les documents où un champ imbriqué contient une des valeurs spécifiées. Ici, nous filtrons les films où au moins un des acteurs a un `_id` dans la liste spécifiée.

### 15 : Afficher tous les films qui n'ont pas de résumé

1. **Afficher tous les films qui n'ont pas de résumé :**
   ```sh
   db.films.find({ summary: { $exists: false } })
   ```
   - Utilisation de `$exists` pour filtrer les documents où un champ n'existe pas. Ici, nous filtrons les films où le champ `summary` n'existe pas.

### 16 : Afficher tous les films tournés avec Leonardo DiCaprio en 1997

1. **Afficher tous les films tournés avec Leonardo DiCaprio en 1997 :**
   ```sh
   db.films.find({ "actors.last_name": "DiCaprio", "actors.first_name": "Leonardo", year: 1997 })
   ```
   - Filtrage basé sur des critères imbriqués et un critère de niveau supérieur. Ici, nous filtrons les films où Leonardo DiCaprio est un acteur et l'année de production est 1997.

### 17 : Afficher les films tournés avec Leonardo DiCaprio ou en 1997

1. **Afficher les films tournés avec Leonardo DiCaprio ou en 1997 :**
   ```sh
   db.films.find({ $or: [{ "actors.last_name": "DiCaprio", "actors.first_name": "Leonardo" }, { year: 1997 }] })
   ```
   - Utilisation de `$or` pour combiner plusieurs critères de filtrage. Ici, nous filtrons les films où Leonardo DiCaprio est un acteur ou l'année de production est 1997.
