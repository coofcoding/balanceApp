# Balance Local - proyecto del APK (Android)

La app está en `www/index.html`. Este proyecto la empaqueta con Capacitor
y GitHub compila el APK en la nube, sin instalar nada en tu computador.

## Pasos
1. Crea una cuenta gratis en https://github.com y un repositorio nuevo
   (puede ser privado), por ejemplo `mi-presupuesto`.
2. Descomprime este zip y sube TODO su contenido al repositorio
   (en la página del repositorio: "Add file" > "Upload files").
   Importante: sube también la carpeta oculta `.github`.
   Si no la ves al descomprimir, activa "mostrar archivos ocultos".
3. Entra a la pestaña **Actions**, elige **Compilar APK** y pulsa **Run workflow**.
   Tarda unos 5 a 8 minutos.
4. Cuando termine en verde, abre esa ejecución y descarga el archivo
   **BalanceLocal-apk** (sección Artifacts). Es un zip que contiene `BalanceLocal.apk`.
5. Pasa el APK a tu teléfono, ábrelo y permite "instalar apps de origen desconocido"
   cuando Android lo pida.

## Notas
- Los datos se guardan en el teléfono. Usa "Exportar copia" en Inicio para respaldarlos.
- Si quieres cambiar el diseño, edita `www/index.html` y vuelve a subirlo;
  GitHub genera un APK nuevo. Instalarlo encima conserva tus datos.
- El APK es de depuración (firmado con clave de prueba): sirve para uso personal.
  Para publicarlo en Google Play hay que firmarlo con tu propia clave.
- El ícono es tu logo. Para cambiarlo: pon una imagen de 1024x1024 en `assets/icon-only.png` y ejecuta `npx @capacitor/assets generate --android`.
- Para cambiar el nombre: `capacitor.config.json` (appName) y `android/app/src/main/res/values/strings.xml` (app_name y title_activity_main).
