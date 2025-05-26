# 🗭 Guía Completa CI/CD con Jenkins, Docker Compose, GitFlow y NGINX como Proxy Inverso (Actualizada)

---

## 🔹 Paso 1: Preparar VM1 – Jenkins y Conexión SSH

**Máquina:** `UbuntuServerDeploy`

### 1.1 Verifica Jenkins

* Jenkins debe tener instalados los siguientes plugins:

  * **Docker Pipeline**
  * **SSH Agent**
  * **Git**
  * **Pipeline Utility Steps**
  * **Credentials Binding**

### 1.2 Habilitar conexión SSH con la VM2

```bash
# En VM1 (UbuntuServerDeploy)
## Creamos llaves

```shell
ssh-keygen -t rsa -b 4096 -C “tu_email@gmail.com”
```

- Enter file in which to save the key (/c/Users/miguel/.ssh/id_rsa):
- Enter passphrase (empty for no passphrase):
- Enter same passphrase again:

- Your identifaction has been saved in /c/Users/miguel/.ssh/id_rsa.
- Your public key has been saved in /c/Users/miguel/.ssh/id_rsa.pub.


## Para comprobarlo escribimos el comando:

```shell
ls -al ~/.ssh
```
## Evaluar el servidor ssh ver si esta funcionando

```shell
eval $(ssh-agent -s)
```

## Agregar la llave (privada al servidor ssh)

```shell
ssh-add ~/.ssh/id_rsa
```
## Copiar tu llave publica puedes usar cat

```shell
cat ~/.ssh/id_rsa.pub
```


## Copiar la llave Publica en el servidor de Apps

cat ~/.ssh/id_rsa.pub | ssh miguel@192.168.1.59 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

> 🔐 Asegúrate de que Jenkins puede acceder sin contraseña.

---

## 🔹 Paso 2: Preparar VM2 – Docker, Docker Compose y NGINX

**Máquina:** `UbuntuServer`

### 2.1 Instalar Docker y Docker Compose

```bash
https://docs.docker.com/engine/install/ubuntu/


## Install using the apt repository🔗

Before you install Docker Engine for the first time on a new host machine, you need to set up the Docker repository. Afterward, you can install and update Docker from the repository.
Set up the repository

1. Update the apt package index and install packages to allow apt to use a repository over HTTPS:

```shell    
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
```

Certificados de Docker

2. Add Docker’s official GPG key:

```shell
 sudo install -m 0755 -d /etc/apt/keyrings

 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

 sudo chmod a+r /etc/apt/keyrings/docker.gpg
```
3. Use the following command to set up the repository: (agregar el repositorio de docker)
```shell
 echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```



## Install Docker Engine

1. Update the apt package index:
  ```
  sudo apt-get update
  ```
2. Install Docker Engine, containerd, and Docker Compose.

  To install the latest version, run:

  ```
  sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
  ```

3. Verify that the Docker Engine installation is successful by running the hello-world image.

  ```
  sudo docker run hello-world
  ```

This command downloads a test image and runs it in a container. When the container runs, it prints a confirmation message and exits.

You have now successfully installed and started Docker Engine.


## Ver la version de docker engine

  ```
  sudo docker -v
  ```

## Ver la version de docker compose
 
  ```
  sudo docker compose version
  ```

**nota en versiones anteriores se usaba docker-compose en las nuevas versiones ahora se usa docker compose "sin el guión"**

tips

1. Para no utilizar siempre la palabra sudo





## Post instalaccion
https://docs.docker.com/engine/install/linux-postinstall/


To create the docker group and add your user:

1. Create the docker group.

```
 sudo groupadd docker
```

2. Add your user to the docker group.

```
sudo usermod -aG docker $USER
```
### Rotacion de logs https://docs.docker.com/config/containers/logging/local/

Modificar la configuracion de docker engine en los logs por que se generan mucho hay que controlar la rotacion para no tener problema de almacenamiento que se llene el disco de logs.

con el comando (si no tienes el archivo se va a crear)

```
sudo nano /etc/docker/daemon.json
```

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3" 
  }
}

```
***El tamaño maximo de los archivos van hacer 10m y cuando tengamos 3 archivos de log los va renombrando (los elimina), con esto ya tenemos una rotación de logs adecuada***

## Comandos Docker

Ver los contenedores en cualquier estado
```
docker ps -a 
```

### 2.2 Instalar NGINX

```bash
sudo apt install nginx -y
```

### 2.3 Configurar NGINX como proxy inverso para múltiples entornos

Crear tres archivos para los entornos `develop`, `test` y `prod`:
```sh
sudo nano /etc/nginx/sites-available/laravel-dev
sudo nano /etc/nginx/sites-available/laravel-test
sudo nano /etc/nginx/sites-available/laravel-prod
```
#### `/etc/nginx/sites-available/laravel-dev`

```nginx
server {
    listen 80;
    server_name dev.midominio.local;

    location / {
        proxy_pass http://127.0.0.1:8001;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

#### `/etc/nginx/sites-available/laravel-test`

```nginx
server {
    listen 80;
    server_name test.midominio.local;

    location / {
        proxy_pass http://127.0.0.1:8002;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

#### `/etc/nginx/sites-available/laravel-prod`

```nginx
server {
    listen 80;
    server_name app.midominio.local;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Activar los sitios:

```bash
sudo ln -s /etc/nginx/sites-available/laravel-* /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

---

## 🔹 Paso 3: Estructura de Proyecto y Archivos Docker Compose

### Estructura recomendada:

```
/
├── docker-compose.yml
├── docker-compose.prod.yml
├── docker-compose.test.yml
├── docker-compose.dev.yml
├── .env
```

### `.env` ejemplo:

```env
APP_ENV=production
APP_PORT=8000
DB_HOST=mysql
DB_DATABASE=laravel
DB_USERNAME=laravel
DB_PASSWORD=secret
```

### `docker-compose.yml` (https://github.com/MiguelAngelRamos/docker-compose-app)

```yaml
version: '3.8'

services:
  app:
    image: mramoscli/laravel-mysql:${TAG:-latest}
    env_file:
      - .env
    networks:
      - laravel_net

networks:
  laravel_net:
```

### Archivos adicionales por entorno

#### `docker-compose.prod.yml`

```yaml
services:
  app:
    environment:
      APP_ENV: production
    command: php artisan serve --host=0.0.0.0 --port=8000
    ports:
      - "8000:8000"
```

#### `docker-compose.test.yml`

```yaml
services:
  app:
    environment:
      APP_ENV: testing
    command: php artisan serve --host=0.0.0.0 --port=8002
    ports:
      - "8002:8000"
```

#### `docker-compose.dev.yml`

```yaml
services:
  app:
    environment:
      APP_ENV: development
    command: php artisan serve --host=0.0.0.0 --port=8001
    ports:
      - "8001:8000"
```

---

## 🔹 Paso 4: GitFlow y su Relación con Entornos

| Rama Git    | Entorno    | Archivo Compose           | Puerto Contenedor |
| ----------- | ---------- | ------------------------- | ----------------- |
| `main`      | Producción | `docker-compose.prod.yml` | 8000              |
| `develop`   | Desarrollo | `docker-compose.dev.yml`  | 8001              |
| `feature/*` | Testing    | `docker-compose.test.yml` | 8002              |
| `hotfix/*`  | Producción | `docker-compose.prod.yml` | 8000              |

---

## 🔹 Paso 5: Jenkinsfile Adaptado

```groovy
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'mramoscli/laravel-mysql'
        BRANCH       = "${env.GIT_BRANCH}".replaceAll('origin/', '')
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {

        stage('Preparar entorno Laravel') {
            agent {
                docker {
                    image 'laravelsail/php82-composer:latest'
                    args  '-u root'
                }
            }
            steps {
                sh '''
                    echo "🔧 Preparando entorno Laravel…"
                    cp .env.example .env
                    composer install
                    php artisan key:generate
                '''
            }
        }

        stage('Pruebas y Cobertura') {
            agent {
                docker {
                    image 'laravelsail/php82-composer:latest'
                    args  '-u root'
                }
            }
            steps {
                sh '''
                    echo "🧪 Ejecutando pruebas con SQLite en memoria…"

                    apt-get update -qq
                    apt-get install -y sqlite3 libsqlite3-dev git unzip curl pkg-config libzip-dev build-essential autoconf

                    yes "" | pecl install xdebug
                    docker-php-ext-enable xdebug
                    printf "xdebug.mode=coverage\\n" > /usr/local/etc/php/conf.d/xdebug.ini

                    docker-php-ext-install pdo pdo_sqlite

                    composer install
                    cp .env.testing .env
                    php artisan config:clear
                    php artisan migrate --seed

                    mkdir -p storage/logs
                    php artisan test \
                        --log-junit=storage/logs/junit.xml \
                        --coverage-cobertura=coverage.xml
                '''

                junit allowEmptyResults: true, testResults: 'storage/logs/junit.xml'
                archiveArtifacts artifacts: 'storage/logs/*.xml', fingerprint: true
            }
        }

        stage('Build Docker') {
            steps {
                script {
                    def tag = BRANCH == 'main' ? 'prod' :
                              BRANCH == 'develop' ? 'dev' : 'test'
                    env.TAG = tag
                }
                sh 'docker build -t $DOCKER_IMAGE:$TAG .'
            }
        }

        stage('Push a Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds',
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $DOCKER_IMAGE:$TAG
                    '''
                }
            }
        }

        stage('Desplegar en Servidor de App (VM2)') {
                    steps {
                        sshagent (credentials: ['vm2-ssh']) {
                            sh '''
                                ssh -o StrictHostKeyChecking=no miguel@192.168.1.94 "
                                    echo '🔄 Actualizando imagen y ejecutando contenedor…'
                                    docker pull $DOCKER_IMAGE:$TAG
                                    cd /home/miguel/docker-compose
                                    TAG=$TAG docker compose \
                                        -f docker-compose.yml \
                                        -f docker-compose.$TAG.yml \
                                        up -d --remove-orphans
                                "
                            '''
                        }
                    }
                }

        stage('Migraciones en Producción') {
            when {
                expression { env.TAG == 'prod' }
            }
            steps {
                sshagent (credentials: ['vm2-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no miguel@192.168.1.61 '
                            echo "📦 Ejecutando migraciones en producción"
                            docker exec docker-compose-app-1 php artisan migrate --force
                        '
                    '''
                }
            }
        }
    }

    // post {
    //     always {
    //         echo '📦 Archivando cobertura y artefactos globales…'
    //         archiveArtifacts artifacts: 'storage/logs/coverage.xml', fingerprint: true
    //         recordCoverage tools: [
    //             [parser: 'COBERTURA', pattern: 'storage/logs/coverage.xml']
    //         ], sourceCodeRetention: 'NEVER'
    //     }
    // }
}

```

---

## 🔹 Paso 6: Exponer VM2 por el Puerto 80

### Alternativas a ngrok:

#### Opcion 1: **Red en modo puente (Bridged Adapter)**

* Asigna una IP fija a `UbuntuServer` desde tu red local.
* Accede por subdominios como `http://dev.midominio.local`, etc.

#### Opcion 2: **Port Forwarding en VirtualBox**

* Red NAT + redirección de puertos en el host.

#### Opcion 3: **LocalXpose o Cloudflare Tunnel**

* Permiten exponer puertos locales de forma segura.

---

## 🔹 Paso 7: Buenas prácticas

✔️ Usar variables de entorno con `.env`
✔️ Volúmenes para persistencia de MySQL
✔️ Separar archivos Compose por entorno
✔️ Usar `--remove-orphans` en despliegues
✔️ Jenkins con conexión segura vía SSH a la VM2
✔️ NGINX configurado para enrutar por puerto/subdominio

---
