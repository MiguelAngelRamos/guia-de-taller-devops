
# 🚀 Guía Completa: Instalar Jenkins en Ubuntu Server con Amazon Corretto 21

> ✅ Compatible con Ubuntu 20.04 y 22.04
> ☕ Utiliza Amazon Corretto 17 (OpenJDK mantenido por AWS)
> 🔐 Incluye configuración de seguridad básica y ajustes recomendados
> 💡 Pensada para servidores locales, virtualizados (VirtualBox) o en la nube

---

## 🔹 Paso 1: Actualizar el sistema

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 🔹 Paso 2: Instalar Amazon Corretto 21

### 1. Actualizar e instalar dependencias

Primero, asegúrate de que tu sistema y las herramientas necesarias (`wget` y `gnupg`) estén actualizados.

```bash
sudo apt-get update && sudo apt-get install -y wget gnupg

```

### 2. Agregar la clave GPG y el repositorio de Corretto

Amazon firma sus paquetes para garantizar su autenticidad. Vamos a descargar su clave y añadir el repositorio a tu lista de fuentes.

```bash
# Descargar la clave y guardarla en el llavero del sistema
wget -O - https://apt.corretto.aws/corretto.key | sudo gpg --dearmor -o /usr/share/keyrings/corretto-keyring.gpg

# Añadir el repositorio a las fuentes de apt
echo "deb [signed-by=/usr/share/keyrings/corretto-keyring.gpg] https://apt.corretto.aws stable main" | sudo tee /etc/apt/sources.list.d/corretto.list

```

### 3. Instalar Corretto 21

Ahora actualizamos la lista de paquetes nuevamente e instalamos la versión 21.

```bash
sudo apt-get update
sudo apt-get install -y java-21-amazon-corretto-jdk

```

### 4. Verificar la instalación

Comprueba que se instaló correctamente ejecutando:

```bash
java -version

```

*Deberías ver una salida que mencione "Corretto-21".*

---

### Paso Extra Importante: Configurar como predeterminado

Si tienes otras versiones de Java instaladas en ese Ubuntu, es posible que el sistema no use Corretto 21 por defecto. Para asegurarte de que Jenkins use esta versión, ejecuta:

```bash
sudo update-alternatives --config java

```

Aparecerá una lista. Escribe el **número** correspondiente a la ruta que diga `/usr/lib/jvm/java-21-amazon-corretto-jdk/...` y presiona Enter.

---

## 🔹 Paso 3: Instalar Jenkins (repositorio oficial)

### 3.1. Importar la clave GPG de Jenkins

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian/jenkins.io-2026.key
```

### 3.2. Agregar el repositorio de Jenkins

```bash
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

### 3.3. Instalar Jenkins

```bash
sudo apt update
sudo apt install jenkins
```

---

## 🔹 Paso 4: Iniciar Jenkins como servicio

```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

Verifica que esté corriendo:

```bash
sudo systemctl status jenkins
```

---

## 🔹 Paso 5: Configurar acceso en el firewall (si UFW está activo)

```bash
sudo ufw allow 8080
sudo ufw enable
sudo ufw status
```

---

## 🔹 Paso 6: Acceder desde el navegador

Accede a Jenkins desde:

```
http://TU_IP:8080
```

Ejemplo:

```
http://localhost:8080
http://192.168.56.101:8080
```

---

## 🔹 Paso 7: Desbloquear Jenkins

Obtén la contraseña inicial:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Pega esa clave en la interfaz web para iniciar la configuración.

---

## 🔹 Paso 8: Configurar desde la interfaz

1. Instala los **plugins recomendados**.
2. Crea el **usuario administrador**.
3. ¡Listo! Ya puedes crear tus primeros pipelines.

---

## 🔸 Ubicaciones útiles

| Elemento                   | Ruta                                         |
| -------------------------- | -------------------------------------------- |
| Archivos de Jenkins        | `/var/lib/jenkins/`                          |
| Configuración del servicio | `/etc/default/jenkins`                       |
| Logs                       | `/var/log/jenkins/jenkins.log`               |
| Puerto por defecto         | `8080` (configurable en el archivo anterior) |

---

## ✅ Confirmación Final de Java (Corretto activo)

Puedes verificar qué versión de Java está usando Jenkins con:

```bash
sudo cat /var/lib/jenkins/config.xml | grep java
```

O más seguro aún:

```bash
ps aux | grep jenkins | grep java
```

