# Directus

# <p align="center">Automatisation de directus avec guthub action</p>

Nous allons déployer directus avec MySQL. Pour cela, nous allons créer deux conteneurs docker ; un pour MySQL server et l’autre avec l’image de Directus. Dans un premier temps, nous utiliserons docker compose pour déployer ces conteneurs en locale et dans un second temps sur un serveur distant.
Le fichier de configuration docker-compose.yml se présente comme suit :
```
version: "3"

networks:
  directus:

services:
  mysql:
    image: mysql:5.7
    environment:
      MYSQL_DATABASE: "directus"
      MYSQL_USER: "directus"
      MYSQL_PASSWORD: "directus"
      MYSQL_ROOT_PASSWORD: "directus"
    ports:
      - "3306:3306"
    networks:
          - directus

  directus:
    image: directus/directus:v8-apache
    ports:
      - "8080:80"
    environment:
      DIRECTUS_APP_ENV: "production"
      DIRECTUS_AUTH_PUBLICKEY: "C69B8E7C437A4E6E16B7A35ACF2C3"
      DIRECTUS_AUTH_SECRETKEY: "8F14A3DB43B93AE97AB3C6151FE76"
      DIRECTUS_DATABASE_HOST: "mysql"
      DIRECTUS_DATABASE_PORT: "3306"
      DIRECTUS_DATABASE_NAME: "directus"      DIRECTUS_DATABASE_USERNAME: "directus"      DIRECTUS_DATABASE_PASSWORD: "directus"
      ADMIN_EMAIL: "kkayefekoua93@gmail.com"
      ADMIN_PASSWORD: "d1r3ctu5"
    volumes:
      - ./data/config:/var/directus/config
      - ./data/uploads:/var/directus/public/uploads 
    networks:
      - directus
 ```
 Ensuite, exécutez la commande suivante à partir de votre racine docker-compose :
 ``docker compose up``
 Allez maintenant sur ce lien, http://localhost:8080/ ou http://127.0.0.1:8080/  si le déploiement est réussi, nous pouvons voir la connexion à la page dans l'interface utilisateur Directus.
Une fois directus fonctionnel en local, nous allons maintenant le déployer sur le serveur distant en suivant les étapes suivantes :
1. Aller sur github et créez votre repository pour directus par exemple nom_repo
2. Créez votre workflow
3. Ouvrez votre éditeur de code placez-vous dans le dossier où se trouve votre fichier docker compose. Faites :
```
git remote add origin git@github.com:git_username/nom_repo.git
git branch -M main
git push -u origin main```
Vous allez ensuite faire un `git pull` pour cloner votre repository dans votre dossier local. A la racine de votre dossier créer un fichier .yml où vous allez définir les actions suivantes :
- Se connecter via ssh sur le serveur distant ;
- Créer un répertoire sur le serveur distant ;
- Copier le fichier docker-compose sur le serveur distant ;
- Et enfin lancer l’exécution du fichier docker-compose.
Notre fichier yml se présentera comme suite :

```
name: Directus

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
  workflow_dispatch:  
  
jobs:
  create-remote-folder:
    name: remote access
    runs-on: ubuntu-latest
    steps:
    - name: create folder on remote vm
      uses: appleboy/ssh-action@v1.0.0
      with:
        host: ${{ secrets.HOST }}
        username: ${{ secrets.USERNAME }}
        key: ${{ secrets.PRIVATE_KEY }}
        port: ${{ secrets.PORT }}
        script: |
          sudo mkdir -p /opt/applications/directus
          sudo chmod ugo+rwx /opt/applications/directus
  
  copy-configs-on-remote-vm:
    runs-on: ubuntu-latest
    needs: create-remote-folder
    steps:
    - uses: actions/checkout@v2
    - uses: a-was/scp-action@v1
      with:
        host: ${{ secrets.HOST }}
        port: ${{ secrets.PORT }}
        user: ${{ secrets.USERNAME }}
        key: ${{ secrets.PRIVATE_KEY }}
        source: 'docker-compose.yaml'
        destination: '/opt/applications/directus'
          
  run-directus:
    name: run-directus
    runs-on: ubuntu-latest
    needs: copy-configs-on-remote-vm
    steps:
    - name: run-directus
      uses: appleboy/ssh-action@v1.0.0
      with:
        host: ${{ secrets.HOST }}
        username: ${{ secrets.USERNAME }} 
        key: ${{ secrets.PRIVATE_KEY }}
        port: ${{ secrets.PORT }}
        script: sudo docker-compose -f /opt/applications/directus/docker-compose.yaml up -d
```