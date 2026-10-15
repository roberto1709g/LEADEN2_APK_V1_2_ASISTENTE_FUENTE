# LEADEN2 APK V1.2 — Asistente de voz

Proyecto Android nativo basado en la versión estable V1.1. Esta versión incorpora registro local de usuario, agenda local, reconocimiento de voz de una consulta por activación, clasificación de las tres primeras palabras, comunicación con el webhook LEADEN2 y lectura hablada de la respuesta `output`.

## Funciones

- Perfil local: nombre, cédula, celular y correo.
- Agenda local independiente de la PWA: nombre y teléfono por contacto. Para eliminar un contacto, mantén pulsada la lista y selecciona el contacto.
- No solicita ni valida licencias en esta etapa.
- Webhook: `https://n8n.serviapps.duckdns.org/webhook/asistente-leaden2`.
- Payload común: `nombre`, `cedula`, `celular`, `correo` y `text`.
- Si “análisis” aparece entre las tres primeras palabras, añade `"analisis": "analisis"`.
- Si “mensaje” aparece entre las tres primeras palabras, añade `contactos` como arreglo de objetos con `nombre` y `telefono`.
- Si no aparece ninguna palabra clave entre las tres primeras palabras, no añade ninguno de esos campos.
- Si las dos palabras clave aparecen entre las tres primeras, se utiliza la que aparece primero.
- La respuesta JSON puede ser un arreglo con el primer objeto o un objeto directo; la APK lee `output` y lo reproduce con TextToSpeech.
- Mantiene la sesión multimedia Media3 y el audio `importando.mp3` como señal antes de iniciar el reconocimiento cuando se activa mediante las gafas.

## Privacidad y datos

El perfil y la lista de contactos se guardan en las preferencias privadas de la aplicación en el teléfono. La lista de contactos no se importa automáticamente desde Android ni desde el `localStorage` de la PWA. Cuando se detecta “mensaje”, los contactos guardados se incluyen en el payload enviado a n8n. El texto reconocido también se envía al webhook. No se imprimen los datos personales ni el payload completo en Logcat.

La validación de licencia no está implementada. No se modifican la PWA, Nginx, la base de datos de licencias ni el workflow de LEADEN1.

## Compilación mediante GitHub Actions

1. Conserva una copia del ZIP original V1.1.
2. Descomprime este proyecto y sube el contenido de la carpeta `LEADEN2_APK_V1_FUNCIONAL` a la raíz de un repositorio GitHub independiente.
3. En GitHub, abre **Actions** y selecciona **Build LEADEN2 APK V1.2 ASISTENTE**.
4. Pulsa **Run workflow**.
5. Cuando termine correctamente, descarga el artifact `LEADEN2-APK-V1-2-ASISTENTE`.
6. Dentro del artifact estará `app-debug.apk`.

La compilación real debe confirmarse en GitHub Actions. Este entorno de trabajo no dispone de Android SDK/Gradle para afirmar que el APK ya compiló.

## Pruebas recomendadas en el Redmi 12C (Android 14)

1. Instala el APK compilado.
2. Introduce los cuatro datos del perfil y pulsa **GUARDAR REGISTRO LOCAL**.
3. Añade dos o más contactos de prueba y confirma que aparecen en la lista.
4. Pulsa **ACTIVAR LEADEN2** y concede el permiso de micrófono. Mantén la aplicación abierta hasta que se reproduzca el audio inicial.
5. Prueba consultas con las palabras clave al inicio, por ejemplo: “análisis de una ley”, “envía un mensaje a Roberto” y “qué tiempo hará mañana”. Verifica las ejecuciones del webhook `asistente-leaden2` y que la respuesta `output` se lea por audio.
6. Con LEADEN2 activado, bloquea la pantalla y pulsa el botón central de las gafas. Verifica por separado si el reconocimiento comienza y si la respuesta se reproduce.
7. Repite las pruebas mientras otra aplicación está en primer plano.

## Limitaciones importantes

- El reconocimiento de voz depende del proveedor de reconocimiento instalado en Android y puede necesitar conexión a Internet.
- Android 14 restringe el inicio de servicios de micrófono desde segundo plano. Por eso la APK inicia el servicio de micrófono en primer plano cuando el usuario pulsa **ACTIVAR LEADEN2** con la aplicación visible. Aun así, el comportamiento con pantalla bloqueada y las políticas de batería de HyperOS deben comprobarse en el teléfono real; no se garantiza hasta probarlo.
- La entrega del comando multimedia a esta sesión depende de qué sesión elija Android y debe volver a verificarse después de instalar esta versión.
- La APK procesa el campo `output` de n8n. El contenido exacto de esa respuesta depende del workflow.

## Configuración

- Paquete: `com.leaden2.app`
- `versionCode`: 2
- `versionName`: 1.2
- `compileSdk`: 35
- `targetSdk`: 35
- `minSdk`: 23
- AGP: 8.6.1
- Gradle: 8.7
- JDK: 17
- Media3: 1.5.1
