# Simulación de despliegue (LOCAL)

1. Clonar proyecto “VERTISOL-yaml” en una carpeta predefinida “app-mensajes” con comando `git clone https://github.com/IngLucioChavez/VERTISOL-yaml.git app-fintech`
2. Acceder a la carpeta generada “app-fintech”
3. Clonar proyecto “VERTISOL-front-end” con comando `git clone https://github.com/IngLucioChavez/VERTISOL-front-end.git front-end`
4. Clonar Proyecto “VERTISOL-back-end” con comando `git clone https://github.com/IngLucioChavez/VERTISOL-back-end.git back-end`
6. Ejecutar comando `docker compose build –no-cache` para construcción de images
7. Ejecutar comando `docker compose up -d` para levantar contenedores en puertos configurados
8. Se observan los contenedores levantados y accesibles.
9. Acceder a contenedor *LARAVEL-API-VERTISOL* con comando `docker container exec -it LARAVEL-API-VERTISOL bash`
11. Generar el archivo .env con las variables indicadas

```
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:RjKfCkoc5MqQB4wSOIK5hT5hx5tdSOHoyGnI2C+1Nu4=
APP_DEBUG=true
APP_URL=http://localhost

APP_LOCALE=en
APP_FALLBACK_LOCALE=en
APP_FAKER_LOCALE=en_US

APP_MAINTENANCE_DRIVER=file

BCRYPT_ROUNDS=12

LOG_CHANNEL=stack
LOG_STACK=single
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=MYSQL-VERTISOL
DB_PORT=3306
DB_DATABASE=vertisol_DB
DB_USERNAME=root
DB_PASSWORD=root123

SESSION_DRIVER=file
SESSION_LIFETIME=120
SESSION_ENCRYPT=false
SESSION_PATH=/
SESSION_DOMAIN=null

BROADCAST_CONNECTION=log
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync

CACHE_STORE=file

MEMCACHED_HOST=127.0.0.1

REDIS_CLIENT=phpredis
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=log
MAIL_SCHEME=null
MAIL_HOST=127.0.0.1
MAIL_PORT=2525
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

VITE_APP_NAME="${APP_NAME}"

JWT_SECRET=
```
11. En consola ejecutar comando `php artisan migrate` para ejecutar migraciones y `php artisan jwt:secret` para generar llave de validación JWT

# ARQUITECTURA ELEGIDA

## Descripción General

La arquitectura está basada en contenedores Docker utilizando Docker Compose para orquestar los servicios principales de la aplicación:

Frontend en Next.js + TypeScript
Backend en Laravel + PHP
Base de datos MySQL 8
Administrador visual de base de datos phpMyAdmin

Todos los servicios se comunican mediante una red privada Docker llamada app-network.

                    ┌──────────────────────┐
                    │      Navegador       │
                    │    Cliente Web       │
                    └──────────┬───────────┘
                               │
                http://localhost:5180
                               │
                               ▼
┌──────────────────────────────────────────────────┐
│             FRONTEND CONTAINER                   │
│           NEXT-TS-VERTISOL                       │
│--------------------------------------------------│
│ Next.js + TypeScript                             │
│ Puerto interno: 3000                             │
│ Puerto host: 5180                                │
└──────────────────────┬───────────────────────────┘
                       │
                       │ API Requests
                       ▼
┌──────────────────────────────────────────────────┐
│              BACKEND CONTAINER                   │
│             LARAVEL-API-VERTISOL                 │
│--------------------------------------------------│
│ Laravel + PHP                                    │
│ Puerto interno: 8000                             │
│ Puerto host: 8000                                │
│ Ejecuta: php artisan serve                       │
└──────────────────────┬───────────────────────────┘
                       │
                       │ Conexión MySQL
                       ▼
┌──────────────────────────────────────────────────┐
│               MYSQL CONTAINER                    │
│                 MYSQL-VERTISOL                   │
│--------------------------------------------------│
│ MySQL 8.0                                        │
│ Puerto interno: 3306                             │
│ Puerto host: 3306                                │
│ Persistencia mediante Docker Volume              │
└──────────────────────┬───────────────────────────┘
                       │
                       │ Administración visual
                       ▼
┌──────────────────────────────────────────────────┐
│             PHPMYADMIN CONTAINER                 │
│                  phpmyadmin                      │
│--------------------------------------------------│
│ phpMyAdmin                                       │
│ Puerto interno: 80                               │
│ Puerto host: 8080                                │
└──────────────────────────────────────────────────┘

