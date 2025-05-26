## 🧠 ¿Por qué hacerlo?

Porque `app.midominio.local` no es un dominio real de Internet, sino uno **ficticio/local**, y Windows no sabrá resolverlo a menos que se lo indiques explícitamente.

---

## 🛠️ Pasos para editar el archivo `hosts` en Windows

### 🗂️ Ruta del archivo:

```
C:\Windows\System32\drivers\etc\hosts
```

### 📝 Cómo editarlo:

1. **Abre el Bloc de notas como administrador**:

   * Haz clic en el botón de **Inicio**.
   * Escribe `Bloc de notas`.
   * Haz clic derecho y elige **"Ejecutar como administrador"**.

2. En el Bloc de notas, abre el archivo:

   ```
   C:\Windows\System32\drivers\etc\hosts
   ```

   * Asegúrate de que el tipo de archivo esté en “Todos los archivos (*.*)” para que aparezca el archivo `hosts`.

3. Agrega esta línea al final del archivo:

   ```txt
   192.168.1.61   app.midominio.local
   ```

   (Y si quieres, también:)

   ```txt
   192.168.1.61   dev.midominio.local
   192.168.1.61   test.midominio.local
   ```

4. Guarda el archivo.

---

## 🧪 Luego prueba desde tu navegador (en Windows):

```
http://app.midominio.local
```

