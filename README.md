# Directus

# <p align="center">Automatisation de directus avec guthub action</p>

Pour cela, nous allons créer deux conteneurs docker ; un pour MySQL server et l’autre avec l’image de Directus. 

Dans un premier temps, nous utiliserons docker compose pour déployer ces conteneurs en locale et dans un second temps sur un serveur distant.  Ensuite, exécutez la commande suivante à partir de votre racine docker-compose :
 ``docker compose up``  Allez maintenant sur ce lien, http://localhost:8080/ ou http://127.0.0.1:8080/  si le déploiement est réussi, nous pouvons voir la connexion à la page dans l'interface utilisateur Directus.  Une fois directus fonctionnel en local, nous allons maintenant le déployer sur le serveur distant en suivant ces étapes:  
1. Aller sur github et créez votre repository pour directus par exemple nom_repo
2. Créez votre workflow
3. Ouvrez votre éditeur de code placez-vous dans le dossier où se trouve votre fichier docker compose. Faites :
```
git remote add origin git@github.com:git_username/nom_repo.git
git branch -M main
git push -u origin main
```
Vous allez ensuite faire un `git pull` pour cloner votre repository dans votre dossier local. A la racine de votre dossier créer un fichier .yml où vous allez définir les actions suivantes :
- Se connecter via ssh sur le serveur distant ;
- Créer un répertoire sur le serveur distant ;
- Copier le fichier docker-compose sur le serveur distant ;
- Et enfin lancer l’exécution du fichier docker-compose.