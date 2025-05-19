### 1️⃣ **Generar una clave SSH (si no tienes una):**

En tu máquina local:

```bash
ssh-keygen -t rsa -b 4096 -C "tu_correo@example.com"
```

* Cuando pregunte dónde guardarla, presiona **Enter** para usar la ubicación por defecto (`~/.ssh/id_rsa`).
* Puedes poner o no una passphrase (si no quieres que pida contraseña, déjalo vacío).

✅ Esto creará dos archivos:

* **\~/.ssh/id\_rsa** (la llave privada, NO la compartas).
* **\~/.ssh/id\_rsa.pub** (la llave pública).

---

### 2️⃣ **Copiar la llave pública al servidor Ubuntu:**

Usa el siguiente comando (reemplaza `miguel@192.168.1.59` o la IP correspondiente):

```bash
ssh-copy-id miguel@192.168.1.59
```

Si no tienes `ssh-copy-id`, puedes hacerlo manual:

```bash
cat ~/.ssh/id_rsa.pub | ssh miguel@192.168.1.59 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

✅ Esto agregará tu llave pública al archivo `~/.ssh/authorized_keys` en el servidor remoto.

---

### 3️⃣ **Probar la conexión sin contraseña:**

Haz SSH manual:

```bash
ssh miguel@192.168.1.59
```

👉 Si entra **sin pedir contraseña**, ¡funciona!

Ahora, **VS Code Remote SSH no volverá a pedir la clave** al abrir la terminal embebida o al navegar por carpetas.

---

## 📝 **Notas adicionales:**

✅ Si quieres que funcione también en las otras IPs (como `192.168.1.27` o `192.168.1.61`), debes repetir el paso de copiar la clave en cada máquina.

✅ Asegúrate que en `~/.ssh/config` tengas configuradas esas máquinas (opcional pero útil):

```ssh
Host ubuntu-59
    HostName 192.168.1.59
    User miguel

Host ubuntu-27
    HostName 192.168.1.27
    User miguel
```

Así puedes conectarte solo con:

```bash
ssh ubuntu-59
```

---

## 🟢 **¿Por qué con esto ya no pedirá contraseña en VS Code?**

Porque **VS Code Remote SSH usa OpenSSH detrás**, y una vez que tienes autenticación por llave pública, las sesiones pueden autenticarse automáticamente sin clave. También se evita que **la terminal embebida abra una sesión SSH nueva independiente** y pida la clave.

---

🔍 Me cuentas si quieres **habilitar `ControlMaster`/`ControlPersist`** para multiplexar sesiones (avanzado, pero con la clave pública no debería hacer falta).

¿Te gustaría también entender por qué VS Code abre nuevas sesiones SSH aunque ya estés conectado? 😊
