# Kontrola - proyecto del APK (Android)

La app está en `www/index.html`. Este proyecto la empaqueta con Capacitor y GitHub compila el APK en la nube, sin instalar nada en tu computador.

## Pasos para compilar
1. Crea una cuenta gratis en https://github.com y un repositorio nuevo (puede ser privado).
2. Sube TODO el contenido de este zip al repositorio ("Add file" > "Upload files"), incluida la carpeta oculta `.github`.
3. En la pestaña **Actions** elige **Compilar APK** y pulsa **Run workflow** (tarda 5 a 8 minutos).
4. Descarga el archivo **Kontrola-apk** (sección Artifacts); dentro está `Kontrola.apk`.
5. Pásalo al teléfono e instálalo (permite "instalar apps de origen desconocido"). Si ya tenías la app, instálalo encima para conservar tus datos.

## Firebase (cuentas, token de acceso y sincronización)
En la consola de Firebase del proyecto kontrola-data:
- Authentication > Método de acceso: activa **Correo electrónico/contraseña**.
- Firestore Database: créala en modo de producción.
- Firestore > Reglas: pega las reglas de abajo, cambia PEGA_AQUI_TU_ADMIN_UID por tu UID de administrador (lo muestra la app Kontrola Admin) y publica.

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    function isAdmin() {
      return request.auth != null && request.auth.uid == 'PEGA_AQUI_TU_ADMIN_UID';
    }
    function hasLicense() {
      return exists(/databases/$(database)/documents/licenses/$(request.auth.uid));
    }
    match /tokens/{tokenId} {
      allow get: if request.auth != null;
      allow list, delete: if isAdmin();
      allow create: if isAdmin() && request.resource.data.used == false;
      allow update: if request.auth != null
        && resource.data.used == false
        && request.resource.data.used == true
        && request.resource.data.usedBy == request.auth.uid
        && request.resource.data.usedAt == request.time
        && request.resource.data.diff(resource.data).affectedKeys()
             .hasOnly(['used','usedBy','usedName','usedAt','place','device']);
    }
    match /licenses/{uid} {
      allow get: if request.auth != null && (request.auth.uid == uid || isAdmin());
      allow list: if isAdmin();
      allow create: if request.auth != null && request.auth.uid == uid
        && getAfter(/databases/$(database)/documents/tokens/$(request.resource.data.token)).data.usedBy == request.auth.uid;
    }
    match /users/{uid}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == uid && hasLicense();
    }
  }
}
```

## Registro con token
- Para crear una cuenta hace falta un token de 16 caracteres, de un solo uso, que genera la app **Kontrola Admin** (proyecto aparte).
- Las cuentas existentes sin licencia pedirán un token al iniciar sesión.
- Al canjear un token se guardan fecha, hora, ubicación aproximada y modelo del teléfono.

## Cambiar nombre o ícono
- Nombre: `capacitor.config.json` (appName) y `android/app/src/main/res/values/strings.xml` (app_name y title_activity_main).
- Ícono: pon una imagen de 1024x1024 en `assets/icon-only.png` y ejecuta `npx @capacitor/assets generate --android`.
