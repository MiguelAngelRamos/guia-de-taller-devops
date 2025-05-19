### Pasos para instalar `nano`:

1. **Actualiza la lista de paquetes disponibles:**
   Ejecuta el siguiente comando para asegurarte de que tu sistema tiene la información más reciente sobre los paquetes disponibles:
   ```bash
   sudo apt-get update
   ```

2. **Instala `nano`:**
   Una vez que la lista de paquetes esté actualizada, instala `nano` con este comando:
   ```bash
   sudo apt-get install -y nano
   ```

3. **Verifica la instalación:**
   Para confirmar que `nano` se instaló correctamente, ejecuta:
   ```bash
   nano --version
   ```
   Esto debería mostrar la versión instalada de `nano`.

### Notas:
- Asegúrate de usar `sudo` para tener los permisos necesarios.
- Si encuentras problemas con archivos de bloqueo, puedes eliminarlos manualmente con:
  ```bash
  sudo rm -f /var/lib/dpkg/lock-frontend
  sudo rm -f /var/lib/apt/lists/lock
  ```
