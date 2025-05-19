
# 🚀 Guía Completa: Instalar Jenkins en Ubuntu Server con Amazon Corretto 17

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

## 🔹 Paso 2: Instalar Amazon Corretto 17

### 2.1. Instalar requisitos base

```bash
sudo apt install -y wget gnupg
```

### 2.2. Importar la clave GPG del repositorio de Amazon

```bash
wget -O- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor -o /usr/share/keyrings/corretto-keyring.gpg
```

### 2.3. Agregar el repositorio de Corretto

```bash
echo "deb [signed-by=/usr/share/keyrings/corretto-keyring.gpg] https://apt.corretto.aws stable main" | \
  sudo tee /etc/apt/sources.list.d/corretto.list
```

### 2.4. Instalar Corretto 17

```bash
sudo apt update
sudo apt install -y java-17-amazon-corretto-jdk
```

### 2.5. Verificar la instalación

```bash
java -version
```

Debes ver algo como:

```bash
openjdk version "17.x.x" 202x-xx-xx LTS
OpenJDK Runtime Environment Corretto-17.x.x
OpenJDK 64-Bit Server VM Corretto-17.x.x
```

---

## 🔹 Paso 3: Instalar Jenkins (repositorio oficial)

### 3.1. Importar la clave GPG de Jenkins

```bash
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

### 3.2. Agregar el repositorio de Jenkins

```bash
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

### 3.3. Instalar Jenkins

```bash
sudo apt update
sudo apt install -y jenkins
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

