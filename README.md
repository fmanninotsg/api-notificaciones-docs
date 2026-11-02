# Publicar la documentación y consola de pruebas

1. Crear un repositorio público en GitHub, por ejemplo api-notificaciones-docs.
2. Subir index.html, openapi.yaml y .nojekyll a la raíz del repositorio (no la carpeta contenedora ni el ZIP).
3. Ir a Settings > Pages.
4. Elegir Deploy from a branch, rama main y carpeta / (root). Guardar.
5. Esperar a que GitHub muestre el enlace publicado en esa misma sección.

La documentación es pública. Para probar la API cada persona ingresa una API Key válida en Authorize. No publicar claves en el repositorio ni en el YAML.

## Configuración del servidor

La página usa el servidor Supabase definido en openapi.yaml. El contrato por sí solo no verifica que ese servidor esté funcionando.

La función debe responder OPTIONS sin exigir API Key, permitir GET y POST y aceptar al menos Content-Type, X-API-Key e Idempotency-Key mediante CORS. Los encabezados CORS deben estar también en las respuestas de error. Configurar el origen real de GitHub Pages, por ejemplo https://TU_USUARIO.github.io (sin la ruta del repositorio).

Comprobar que la configuración de autenticación de la función permita el mecanismo X-API-Key definido en el contrato. Si una capa previa exige un JWT, el contrato y el acceso deben ajustarse de manera coherente; no alcanza con publicar Swagger UI.

## Prueba de aceptación

- Abrir la página y confirmar que aparecen las tres operaciones.
- Ingresar la clave del grupo y ejecutar GET /avisos.
- Crear un aviso a un correo propio con una Idempotency-Key nueva. Esperar 202.
- Consultar el id recibido hasta conocer el resultado.
- Repetir el POST con la misma clave de idempotencia y cuerpo: esperar 200 y creado=false.
- Consultar desde otro grupo ese aviso: esperar 404 según el contrato.

Los POST envían correos reales si el servicio está configurado. Para pruebas anónimas sin API Key, usar un entorno mock separado: esta página conserva la autenticación de la API real.

Swagger UI se carga desde un CDN y requiere conexión a internet. Las claves ingresadas no se conservan al recargar la página.
