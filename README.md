# Contabilidad Gerencial — plataforma UADE

Versión inicial para GitHub Pages + Firebase Authentication + Cloud Firestore. Incluye 51 consignas de la práctica, imágenes de estados contables y comparación, respuestas explicadas, intentos sucesivos, registro de respuestas/cambios/consultas y panel docente con exportación CSV.

## 1. Confirmar el dominio institucional

No se ha supuesto cuál es el dominio oficial de correo estudiantil de UADE. **Antes de publicar**, reemplazar `DOMINIO-INSTITUCIONAL.edu.ar` por el dominio exacto autorizado en **dos archivos**: `config.js` y `firestore.rules` (en las reglas, escapar cada punto como `\\.`). La comprobación del navegador es orientativa: la regla de Firestore es el control efectivo sobre los datos. Si hay más de un dominio autorizado, adaptar la regla de seguridad y la lista del navegador de forma consistente.

## 2. Crear Firebase

1. Abrir https://console.firebase.google.com/ y crear un proyecto.
2. En Configuración del proyecto > Tus apps, registrar una aplicación web. Copiar el objeto `firebaseConfig`.
3. Duplicar `config.example.js` como **`config.js`** y completar apiKey, authDomain, projectId y appId. No poner claves de cuentas de servicio ni secretos privados en GitHub.
4. En Authentication > Sign-in method, habilitar **Correo electrónico/contraseña**. No habilitar la opción de enlace por correo como único método para esta versión.
5. En Authentication > Settings > Authorized domains, agregar `TU-USUARIO.github.io` y, si corresponde, tu dominio personalizado. En algunos proyectos `localhost` debe agregarse manualmente para probar localmente.
6. En Firestore Database, crear la base de datos en modo producción y seleccionar una región adecuada. En la pestaña **Rules**, pegar el contenido de `firestore.rules` con el dominio institucional ya corregido y pulsar Publicar.

## 3. Habilitar el panel docente

1. Registrarte en la aplicación con **tu propio correo institucional** y verificarlo.
2. En Firebase > Authentication > Users, copiar el **UID** de tu cuenta.
3. En Firestore > Data, crear una colección `admins`, y dentro un documento cuyo **ID sea exactamente tu UID**. Agregar un campo de texto `role` con valor `teacher` (el contenido del campo es informativo; la presencia del documento habilita la función).
4. Ingresar a `docente.html`. El permiso se valida también en Firestore; ocultar un botón no es una medida de seguridad.

**Importante:** no crear documentos de administrador para estudiantes. Los documentos `admins` solo se gestionan desde la consola Firebase por alguien con permisos del proyecto.

## 4. Publicar en GitHub Pages

1. Crear un repositorio nuevo, preferentemente **privado si el plan y la configuración de Pages lo permiten**; de todos modos, la página publicada y sus archivos de frontend son públicos. No subir datos de estudiantes ni credenciales administrativas.
2. Subir `index.html`, `docente.html`, `app.js`, `docente.js`, `style.css`, `questions.json`, `config.js` y la carpeta `assets/`. `firestore.rules` y `README.md` pueden quedar en el repositorio para mantener la configuración documentada.
3. Ir a Settings > Pages > Build and deployment > Deploy from a branch > `main` / root y guardar.
4. Esperar a que se publique `https://TU-USUARIO.github.io/NOMBRE-REPOSITORIO/`.
5. Probar con **dos cuentas de prueba distintas**, una estudiante y otra docente, antes de enviar el enlace al curso.

## 5. Qué registra y qué no

- Perfil: nombre, apellido, comisión, correo y UID.
- Por intento: fecha de inicio, estado abierto/cerrado y respuestas guardadas.
- Por pregunta: opción elegida o texto, revisiones y fecha de actualización.
- Eventos: comienzo/cierre de intento, navegación entre temas, respuestas, guardado de textos y consulta de respuestas orientativas.
- El tiempo mostrado en el panel se basa en eventos y **no representa una medición fiable del tiempo efectivo de estudio**.
- Las respuestas abiertas no tienen nota automática. Las opciones múltiples y V/F sí muestran corrección inmediata.
- Los datos se guardan en Firebase, no en GitHub. El panel puede exportarlos a CSV.

## 6. Privacidad, costos y límites

- Informar al estudiantado de la finalidad académica del registro, los datos recopilados, quién puede acceder y el plazo de conservación; establecer una política de eliminación al finalizar el período académico.
- La aplicación es una **herramienta formativa**, no un sistema de examen seguro: las respuestas correctas viajan en `questions.json` y pueden inspeccionarse en el navegador.
- Las reglas restringen lectura/escritura por UID y acceso docente; el cliente por sí solo no es confiable. Para un examen calificado con respuestas ocultas, mover la corrección al servidor (por ejemplo, Cloud Functions).
- El uso gratuito de Firebase está sujeto a cuotas vigentes y puede generar costos si se superan o si se habilitan servicios de pago. El panel actual hace lecturas por estudiante e intento, por lo que es apto para un curso moderado pero debería paginarse para cohortes grandes.
- La interfaz permite correo institucional verificado, **no SSO/SAML de UADE**. Para SSO real hay que coordinar con el área de sistemas de la universidad.
- Firebase Authentication puede permitir crear cuentas de otros dominios, pero no podrán leer/escribir perfiles o respuestas en Firestore por las reglas. Para impedir también el alta de cuentas ajenas a la institución se requiere un bloqueo de registro del lado servidor (p. ej. Identity Platform blocking functions).

## 7. Estructura

- `index.html` / `app.js`: estudiantes.
- `docente.html` / `docente.js`: panel docente.
- `questions.json`: preguntas y soluciones públicas para autoevaluación.
- `assets/`: imágenes del caso y la comparación.
- `firestore.rules`: permisos de Firestore.
- `config.example.js`: plantilla de configuración Firebase.

## 8. Pruebas recomendadas

1. Intentar registrar un correo no institucional: la interfaz debe rechazarlo.
2. Registrar correo institucional, verificar y completar perfil.
3. Responder una opción múltiple y una pregunta abierta; actualizar la página y confirmar que persisten.
4. Iniciar un segundo intento y verificar que el primero queda en el panel.
5. Abrir `docente.html` con cuenta estudiante: debe denegar acceso a los datos.
6. Abrir `docente.html` con cuenta docente autorizada: deben verse respuestas y funcionar la exportación.
7. Revisar la consola del navegador y Firestore para detectar reglas denegadas inesperadamente.

**Estado de entrega:** código preparado y validado estáticamente. Falta completar el dominio institucional, las credenciales públicas del proyecto Firebase, desplegar reglas, habilitar la cuenta docente y probar en el entorno real. No se ha creado ni publicado ningún proyecto GitHub/Firebase desde esta entrega.
