```
permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock
```

indica que **el usuario que ejecuta Jenkins no tiene permisos para usar Docker** en la VM `192.168.1.69`.



### 🔧 Opción 1: Agregar el usuario `jenkins` al grupo `docker`

1. Conéctate a la VM `UbuntuServerDeploy` (192.168.1.69):

```bash
sudo usermod -aG docker jenkins
```

2. Luego **reinicia el servicio Jenkins** para que los cambios surtan efecto:

```bash
sudo systemctl restart jenkins
```

3. También es recomendable reiniciar sesión o reiniciar la máquina si el problema persiste.

> 📌 Verifica luego con:

```bash
id jenkins
```

Debe incluir `docker` en la lista de grupos.

---

### 🔒 Opción 2 (menos recomendada): Dar permisos directos al socket Docker

```bash
sudo chmod 666 /var/run/docker.sock
```

⚠️ Esto es **inseguro** para entornos de producción y no se recomienda para ambientes sensibles.

---

### 🧪 Validación

Después de aplicar los cambios, prueba ejecutar:

```bash
sudo su - jenkins
docker ps
```

Si ves la lista de contenedores sin errores, Jenkins ya tiene permisos sobre Docker.

---