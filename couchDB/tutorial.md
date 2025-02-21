# Tutoriel : Installer CouchDB avec Docker sur Debian et Utiliser l'Interface Web

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

### Étape 2 : Tirer l'image Docker de CouchDB

1. **Tirer l'image officielle de CouchDB :**
   ```bash
   sudo docker pull couchdb
   ```

### Étape 3 : Lancer un conteneur CouchDB

1. **Lancer un conteneur CouchDB avec un utilisateur et un mot de passe :**
   ```bash
   docker run -d --name couchdbdemo -e COUCHDB_USER=youcef -e COUCHDB_PASSWORD=samir -p 5984:5984 couchdb
   ```

2. **Vérifier que le conteneur CouchDB est en cours d'exécution :**
   ```bash
   docker ps
   ```

### Étape 4 : Vérifier que CouchDB fonctionne

1. **Accéder à l'interface web de CouchDB :**
   ```
   http://localhost:5984/_utils
   ```

2. **Tester la connexion à CouchDB via cURL :**
   ```bash
   curl -X GET http://youcef:samir@localhost:5984
   ```

## Partie 2 : Utilisation de la Base de Données CouchDB

### Créer une Base de Données

```bash
curl -X PUT http://youcef:samir@localhost:5984/films
```

### Ajouter et Gérer des Documents

- **Ajouter un document dans `films`**
  ```bash
  curl -X POST http://youcef:samir@localhost:5984/films \
       -H "Content-Type: application/json" \
       -d '{"title": "Inception", "year": 2010, "genre": "Science-Fiction"}'
  ```

- **Insérer une collection de documents**
  ```bash
  curl -X POST http://youcef:samir@localhost:5984/films/_bulk_docs -d @films_couchdb.json -H "Content-Type: application/json"
  ```

### Utilisation de MapReduce pour extraire des données

- **Extraire l'année de sortie et le titre des films**
  ```javascript
  function (doc) {
    emit(doc.year, doc.title);
  }
  ```

- **Compter le nombre de films par année avec une fonction Reduce**
  ```javascript
  function (keys, values) {
    return values.length;
  }
  ```

### Calculer le nombre de films par acteur

- **Fonction Map pour récupérer les films par acteur**
  ```javascript
  function(doc) {
    for (i = 0; i < doc.actors.length; i++) {
      emit({"prénom": doc.actors[i].first_name, "nom": doc.actors[i].last_name}, doc.title);
    }
  }
  ```

- **Fonction Reduce pour compter les films par acteur**
  ```javascript
  function (keys, values) {
    return values.length;
  }
  ```

### Calculer le nombre de films par réalisateur

- **Fonction Map pour regrouper les films par réalisateur**
  ```javascript
  function (doc) {
    emit(doc.director._id, doc.title);
  }
  ```

- **Fonction Reduce pour compter les films réalisés**
  ```javascript
  function (keys, values) {
    return values.length;
  }
  ```

---

