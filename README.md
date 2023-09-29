# Directus

# <p align="center">Automatisation de directus avec guthub action</p>
    
Nous allons déployer directus avec MySQL. Pour cela, nous allons créer deux conteneurs docker ; un pour MySQL server et l’autre avec l’image de Directus. Dans un premier temps, nous utiliserons docker compose pour déployer ces conteneurs en locale et dans un second temps sur un serveur distant.
Le fichier de configuration docker-compose.yml se présente comme suit :
`version: "3"`
`networks:`
  `directus:`

`services:`
  `mysql:`
    `image: mysql:5.7`
    `environment:`
      `MYSQL_DATABASE: "directus"`
      `MYSQL_USER: "directus"`
      `MYSQL_PASSWORD: "directus"`
      `MYSQL_ROOT_PASSWORD: "directus"`
    `ports:`
      `- "3306:3306"`
    `networks:`
          `- directus`

  `directus:`
    `image: directus/directus:v8-apache`
    `ports:`
      `- "8080:80"`
    `environment:`
      `DIRECTUS_APP_ENV: "production"`
      `DIRECTUS_AUTH_PUBLICKEY: "C69B8E7C437A4E6E16B7A35ACF2C3"`
      `DIRECTUS_AUTH_SECRETKEY: "8F14A3DB43B93AE97AB3C6151FE76"`
      `DIRECTUS_DATABASE_HOST: "mysql"`
      `DIRECTUS_DATABASE_PORT: "3306"`
      `DIRECTUS_DATABASE_NAME: "directus"`      `DIRECTUS_DATABASE_USERNAME: "directus"`      `DIREeCTUS_DATABASE_PASSWORD: "directus"`

      `ADMIN_EMAIL: "kkayefekoua93@gmail.com"`
      `ADMIN_PASSWORD: "d1r3ctu5"`
    `volumes:``
      `- ./data/config:/var/directus/config`
      `- ./data/uploads:/var/directus/public/uploads`   
    `networks:`
      `- directus`
 