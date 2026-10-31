# ATELIER

Sitio estático con funciones de IA en Netlify. El chat usa Gemini; la clave de Gemini y las credenciales de Firebase se configuran como variables privadas en Netlify, nunca en `index.html`.

## Preparar Firebase

1. En Firebase Authentication, activa el proveedor **Correo electrónico/contraseña**.
2. En Firestore, crea la base de datos. Para que cada usuario solo acceda a su propio documento `usuarios/{uid}`, usa reglas como:

   ```text
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /usuarios/{userId} {
         allow read, write: if request.auth != null && request.auth.uid == userId;
       }
     }
   }
   ```

3. Crea una clave de Gemini en Google AI Studio.
4. Genera una clave privada de cuenta de servicio en la configuración del proyecto Firebase. Guárdala de forma segura; no la subas al repositorio.

## Publicar en Netlify

1. Sube este proyecto a un repositorio Git y conéctalo a Netlify. Netlify usará `netlify.toml` para publicar la página y desplegar `netlify/functions/chat.mjs`.
2. En **Site configuration → Environment variables**, configura:
   - `GEMINI_API_KEY`: la clave de Google AI Studio.
   - `FIREBASE_SERVICE_ACCOUNT`: el contenido JSON completo de la clave de cuenta de servicio de Firebase.
   - `FIREBASE_PROJECT_ID`: el ID del proyecto Firebase (en este proyecto, `atelier-web11`).
3. Vuelve a desplegar el sitio después de configurar las variables.
4. Prueba crear una cuenta e iniciar sesión; luego abre el chat y envía un mensaje. La función verifica el token de Firebase antes de llamar a Gemini.

No pongas ninguna de esas credenciales en el HTML, en Git ni en variables `VITE_*`/`NEXT_PUBLIC_*`. La configuración de Firebase que aparece en el navegador identifica el proyecto, pero no sustituye las reglas de seguridad de Firestore.
