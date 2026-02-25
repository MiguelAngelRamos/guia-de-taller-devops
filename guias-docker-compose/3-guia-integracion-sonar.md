# Guía de Integración: Jenkins Host → SonarQube Docker

Esta guía asume que ya tienes el stack de monitoreo y calidad de código arriba mediante `docker compose up -d`.

## Paso 1: Generar el Token de Seguridad en SonarQube

Para que Jenkins pueda comunicarse con SonarQube, necesita una llave de acceso (Token).

1. Accede a la interfaz de SonarQube en `http://localhost:9000` (o la IP de tu Ubuntu Server).
2. Inicia sesión con las credenciales por defecto:
* **Usuario:** `admin`
* **Contraseña:** `admin` (se te pedirá cambiarla en el primer inicio).


3. Ve a tu perfil de usuario (círculo arriba a la derecha) y selecciona **My Account**.
4. Haz clic en la pestaña **Security**.
5. En la sección **Generate Token**:
* Dale un nombre (ej. `jenkins-token`).
* Selecciona el tipo: `User Token`.
* Haz clic en **Generate**.


6. **IMPORTANTE:** Copia el token generado inmediatamente. No podrás verlo de nuevo.

## Paso 2: Instalar el Plugin en Jenkins

Jenkins necesita un "traductor" para entenderse con SonarQube.

1. Abre tu Jenkins (normalmente en `http://localhost:8080`).
2. Ve a **Administrar Jenkins (Manage Jenkins)** > **Plugins**.
3. En la pestaña **Available plugins**, busca: `SonarQube Scanner`.
4. Selecciónalo e instálalo. Reinicia Jenkins si es necesario.

## Paso 3: Registrar el Token en Jenkins

Debes guardar el token de forma segura dentro de Jenkins.

1. Ve a **Administrar Jenkins** > **Credentials**.
2. Selecciona el dominio (o `(global)`) y haz clic en **Add Credentials**.
3. Configura lo siguiente:
* **Kind:** Secret text.
* **Secret:** Pega aquí el token generado en el Paso 1.
* **ID:** `sonarqube-token` (puedes elegir cualquier nombre).
* **Description:** Token para acceso a SonarQube en Docker.


4. Haz clic en **Create**.

## Paso 4: Configurar el Servidor SonarQube en el Sistema

Ahora Jenkins sabrá a qué dirección IP y con qué credenciales debe enviar los informes.

1. Ve a **Administrar Jenkins** > **System (Configurar el sistema)**.
2. Busca la sección **SonarQube servers**.
3. Haz clic en **Add SonarQube**.
4. Configura los campos:
* **Name:** `sonarqube` (este nombre se usará luego en tus pipelines).
* **Server URL:** `http://localhost:9000`. Dado que Jenkins está en el host y el contenedor de SonarQube expone el puerto `9000`, `localhost` funcionará correctamente.
* **Server authentication token:** Selecciona el token que guardaste en el Paso 3.


5. Haz clic en **Save**.

## Paso 5: Configurar la Herramienta Scanner

El "Scanner" es el binario que realmente analiza el código.

1. Ve a **Administrar Jenkins** > **Tools (Global Tool Configuration)**.
2. Busca la sección **SonarQube Scanner**.
3. Haz clic en **Add SonarQube Scanner**.
4. Configura:
* **Name:** `sonar-scanner` (recuerda este nombre).
* **Install automatically:** Déjalo marcado para que Jenkins descargue la versión más reciente según sea necesario.


5. Haz clic en **Save**.

---

## Verificación Final

Una vez completados estos pasos, puedes probar la integración en cualquier **Pipeline** de Jenkins agregando el siguiente bloque en tu `Jenkinsfile`:

```groovy
stage('SonarQube Analysis') {
    steps {
        script {
            // El nombre 'sonarqube' debe coincidir con el nombre dado en el Paso 4
            def scannerHome = tool 'sonar-scanner' // Nombre dado en el Paso 5
            withSonarQubeEnv('sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner"
            }
        }
    }
}

```

Al ejecutar el pipeline, los resultados aparecerán automáticamente en tu servidor de SonarQube.