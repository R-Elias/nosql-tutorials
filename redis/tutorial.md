# **Tutoriel : Installer Redis avec Docker sur Debian et l'utiliser efficacement**

## **Partie 1 : Installation et Mise en Place**

### **Étape 1 : Installer Docker**
(Si Docker n'est pas encore installé, suivez ces étapes)

1. **Mettre à jour les paquets existants** :
   ```bash
   sudo apt update
   ```
2. **Installer les dépendances nécessaires** :
   ```bash
   sudo apt install apt-transport-https ca-certificates curl software-properties-common
   ```
3. **Ajouter la clé GPG officielle de Docker** :
   ```bash
   curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   ```
4. **Ajouter le dépôt Docker à la liste des sources APT** :
   ```bash
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```
5. **Mettre à jour les paquets à nouveau** :
   ```bash
   sudo apt update
   ```
6. **Installer Docker** :
   ```bash
   sudo apt install docker-ce
   ```
7. **Vérifier que Docker est bien installé** :
   ```bash
   sudo systemctl status docker
   ```

---

### **Étape 2 : Tirer l'image Docker de Redis**
1. **Télécharger l'image officielle de Redis** :
   ```bash
   sudo docker pull redis
   ```

---

### **Étape 3 : Lancer un conteneur Redis**
1. **Lancer un conteneur Redis simple** :
   ```bash
   sudo docker run --name redis -d -p 6379:6379 redis
   ```
   - `--name redis` : Nomme le conteneur `redis`.
   - `-d` : Lance le conteneur en arrière-plan.
   - `-p 6379:6379` : Expose Redis sur le port **6379** (port par défaut).

2. **Vérifier que le conteneur fonctionne** :
   ```bash
   sudo docker ps
   ```

---

## **Partie 2 : Accéder à Redis et Utiliser ses Fonctionnalités**

### **1. Se connecter à Redis**
1. **Ouvrir un shell Redis à l'intérieur du conteneur** :
   ```bash
   sudo docker exec -it redis redis-cli
   ```
2. **Vérifier la connexion en exécutant une commande simple** :
   ```bash
   PING
   ```
   Redis doit répondre :
   ```
   PONG
   ```

---

### **2. Manipuler les clés et valeurs**
- **Créer une clé** :
  ```bash
  SET message "Hello Redis!"
  ```
- **Lire une clé** :
  ```bash
  GET message
  ```
- **Mettre à jour une clé** :
  ```bash
  SET message "New value"
  ```
- **Supprimer une clé** :
  ```bash
  DEL message
  ```
- **Vérifier si une clé existe** :
  ```bash
  EXISTS message
  ```
  Réponse :
  - `1` : La clé existe
  - `0` : La clé n'existe pas

---

### **3. Définir une durée de vie pour une clé**
- **Définir une durée de vie (en secondes)** :
  ```bash
  EXPIRE message 60
  ```
- **Vérifier le temps restant avant expiration** :
  ```bash
  TTL message
  ```
- **Supprimer la durée de vie d'une clé** :
  ```bash
  PERSIST message
  ```

---

### **4. Opérations sur les nombres**
- **Incrémenter une valeur** :
  ```bash
  INCR compteur
  ```
- **Décrémenter une valeur** :
  ```bash
  DECR compteur
  ```

---

### **5. Manipuler des listes**
- **Ajouter un élément en début de liste** :
  ```bash
  LPUSH tasks "Task 1"
  ```
- **Ajouter un élément en fin de liste** :
  ```bash
  RPUSH tasks "Task 2"
  ```
- **Lire les éléments d’une liste** :
  ```bash
  LRANGE tasks 0 -1
  ```
- **Supprimer et récupérer le premier élément** :
  ```bash
  LPOP tasks
  ```
- **Supprimer et récupérer le dernier élément** :
  ```bash
  RPOP tasks
  ```

---

### **6. Manipuler des ensembles (Sets)**
- **Ajouter des éléments dans un ensemble** :
  ```bash
  SADD users "Alice"
  ```
- **Lister les éléments d’un ensemble** :
  ```bash
  SMEMBERS users
  ```
- **Supprimer un élément d’un ensemble** :
  ```bash
  SREM users "Alice"
  ```

---

### **7. Manipuler les Sets Ordonnés (Sorted Sets)**
- **Ajouter un élément avec un score** :
  ```bash
  ZADD leaderboard 100 "Alice"
  ```
- **Récupérer les éléments triés par score** :
  ```bash
  ZRANGE leaderboard 0 -1 WITHSCORES
  ```
- **Récupérer les éléments triés en ordre décroissant** :
  ```bash
  ZREVRANGE leaderboard 0 -1 WITHSCORES
  ```

---

### **8. Utiliser les Hashes**
- **Créer un utilisateur avec plusieurs champs** :
  ```bash
  HSET user:1 name "Alice" age 30 email "alice@example.com"
  ```
- **Lire toutes les informations d’un utilisateur** :
  ```bash
  HGETALL user:1
  ```
- **Lire un champ spécifique** :
  ```bash
  HGET user:1 age
  ```
- **Incrémenter un champ numérique** :
  ```bash
  HINCRBY user:1 age 5
  ```

---

### **9. Utiliser Pub/Sub pour les messages en temps réel**
1. **S'abonner à un canal** :
   ```bash
   SUBSCRIBE news
   ```
2. **Publier un message sur un canal** :
   ```bash
   PUBLISH news "Breaking News: Redis is awesome!"
   ```
3. **S’abonner à plusieurs canaux avec un motif** :
   ```bash
   PSUBSCRIBE mes*
   ```
4. **Publier un message sur un canal correspondant au motif** :
   ```bash
   PUBLISH mes_cours "Nouvelle formation disponible !"
   ```

---

### **10. Commandes supplémentaires de gestion des données**
- **Publier un message sur un autre canal** :
  ```bash
  PUBLISH mescours "Un nouveau cours sur MongoDB"
  ```
- **Publier un message sur un canal spécifique** :
  ```bash
  publish user:1 "Bonjour user 1!"
  ```
- **S'abonner à plusieurs canaux** :
  ```bash
  SUBSCRIBE mescours user:1
  ```
- **Utiliser un motif pour s'abonner à des canaux** :
  ```bash
  PSUBSCRIBE mes*
  ```
- **Manipuler les champs d'un utilisateur** :
  ```bash
  HSET user:11 username "syoucef"
  HSET user:11 age 31
  HSET user:11 email samir.youcef@polytechnancy.fr
  HMSET user:4 username "Augustin" age 5 email augustin@gmail.fr
  ```
- **Lire toutes les informations d'un utilisateur** :
  ```bash
  HGETALL user:4
  ```
- **Incrémenter une valeur dans un champ** :
  ```bash
  HINCRBY user:4 4
  HINCRBY user:4 age 4
  ```
- **Lire un champ spécifique** :
  ```bash
  HGET user:4 age
  ```
- **Manipuler les sets ordonnés** :
  ```bash
  ZADD score4 19 "Augustin"
  ZADD score4 18 "Ines"
  ZADD score4 10 "Samir"
  ZADD score4 8 "Philippe"
  ZRANGE score4 0 -1
  ZRANGE score4 0 1
  ZREVRANGE score4 0 -1
  ZRANK score4 "Augustin"
  ```
- **Manipuler les ensembles** :
  ```bash
  SUNION utilisateurs autresUtilisateurs
  SADD utilisateurs "Ines"
  SADD utilisateurs "Samir"
  SADD utilisateurs "Marc"
  SADD utilisateurs "Marc"
  SMEMBERS utilisateurs
  SREM "Marc"
  SREM utilisateurs "Marc"
  SMEMBERS utilisateurs
  ```
- **Manipuler les clés avec expiration** :
  ```bash
  SET macle mavaleur
  TTL macle
  EXPIRE macle 120
  TTL macle
  DEL macle
  ```
- **Manipuler les listes** :
  ```bash
  RPUSH mesCours "BDA"
  RPUSH mesCours "Services Web"
  GET mesCours
  LRANGE mesCours 0 -1
  LRANGE mesCours 0 0
  LRANGE mesCours 0 stop
  ```
- **Manipuler les valeurs** :
  ```bash
  SET demo "Bonjour"
  SET user:1234 "Samir"
  ```

---

## **Partie 3 : Gestion et Persistance**

### **Arrêter et Redémarrer Redis**
1. **Arrêter le conteneur Redis** :
   ```bash
   sudo docker stop redis
   ```
2. **Redémarrer Redis** :
   ```bash
   sudo docker start redis
   ```

---
