# Laboratorio #

Este ejemplo es para mostrar como desplegar una aplicación en un servidore DigitalOcean.
Está tomado del ejemplo oficial de Digtal Ocean abajo las instrucciones en Inglés. Al finalizar el laboratorio tendrá una app Django ejecutando en Digital Ocean conectada a una base de datos postgresql.

Se han modificado las instrucciones para reflejar la actual UI de Digital Ocean Apps.

## Implantando la Aplicación ##

1. Visitar https://cloud.digitalocean.com/apps (si no has ingresado puedes ver un mensaje de error, visita https://cloud.digitalocean.com/login directamente y autentícate, luego intenta de nuevo).
1. Click en "Launch Your App" o "Create App"
1. Elegir GitHub y autenticarse con las credenciales de GitHub.
1. Bajo Repository, elegir este repository (e.g. `<your-org>/deploy-digitalocean-django`), elegir la rama (branch) main, marcar la checkbox que dice autodeploy code changes y click en **Next**.
1. En la siguiente pantalla aparecen los parámetros de configuracion de la app. En este punto debes hacer click en "Edit" al lado de donde dice "Environment Variables":
   1. Al expandirse esta sección agregar lo siguiente:
        1. Si quieres lanzar la app en modo de depuración define `DEBUG` a `True`
        1. Setear `DJANGO_ALLOWED_HOSTS` a `${APP_DOMAIN}` para permitir que  `allowed_hosts` apunte al dominio default provisto por DigitalOcean.
        1. Setear `DATABASE_URL` a `${<YOUR_DB_NAME>.DATABASE_URL}`. Vamos a setear el nombre de la base de datos en un próximo paso. Por simplicidad lo llamaremos  `db` asi que esta variable debería verse así: `${db.DATABASE_URL}`
        1. Hay más información sobre las variables de ambiente [aquí](#variables-de-ambiente).  
1. Modifica el **Run Command** para apuntar a la aplicación. En este ejemplo, el proyect se llama `mysite`. Así que el comando modificado debería ser `gunicorn --worker-tmp-dir /dev/shm mysite.wsgi`. 
1. Haz click en **"Add Database"**, aparece una popup, asegurate que Choose Name contenga el valor `db` y haz click en el botón azul que dice "Add Database". Esto genera una base de datos Postgresql. Luego haz click en el botón **Next**.
1. Luego aparece una pantalla solicitando nombrar el servicio web, acá pueden cambiar el nombre que tendrá la aplicación. Tambien eligen la región, dejaremos "New York" y presionamos **Next**.
1. Luego sale una pantalla con el título **Finalize and launch**. Seleccionamos la opción **Basic** y presinona el botón que dice  **Launch Basic/Pro App**.
1. Verás un mensaje "Building..." con un indicado de progreso. Si haces click en  "Deployments"→"Details" puedes ver más detalles del proceso de "building".
1. Este proceso toma entre  5 a 10 minutos to compilar esta app, aí que se paciente. Puedes ver la bitácora para obtener feedback durante la fase de despliegue de la app
1. Una vez que el build finalize exitosamente haz click en el link "Live App" y verás una página con instrucciones adicionales.
1. Tenemos que realizar las siguientes tareas para finalizar de setear nuestra app:
    1. Navegar al tab "console.
    1. Ejecutar `python manage.py migrate` para realizar la migración inicial de la base de datos. Esto sólo se hace la primera vez que desplieguen su app Django.
    1. Ejecutar `python manage.py createsuperuser` y crear un super usuario para acceder a  `/admin`.
1. Después de configurar el superuser ingresa a la url que te provee DigitalOcean agregando `/admin` al final y configura una encuesta, agregando una pregunta en el model `Question`. 
1. Finalmente al dominio que te entrega DigitalOcean agregale `/polls` al final e ingresa a la encuesta que acabas de crear.

## Variables de Ambiente ##

Muchos de los settings de una app  Django han sido expuestas como variables de ambiente. Abajo hay una lista de estas y que habilitan:

* *DEBUG* - Coloca el modo de debug de  Django. Por defecto es `False`.
* *DJANGO_ALLOWED_HOSTS* - Los hostnames django desde los cuales Djaango puede recibir requests. Por defecto este es  `127.0.0.1,localhost`. Es una lista separada con una coma. Para más información ver  `allowed_hosts` en la [django documentation](https://docs.djangoproject.com/en/3.1/ref/settings/#allowed-hosts).
* *DEVELOPMENT_MODE* - Esto determina si vamos a usar una base de datos Postgres db o sqlite. Por defecto es  `False` por lo tanto requieres una base  Postgres db
* *DATABASE_URL* - La url de conexión incluyend puerto, username, and password to connect to a postgres db. This is provided by App Platform. Required if *DEVELOPMENT_MODE* is `False`.

## Realizar Cambios ##

Mientras esté habilitada la opción Autodpeply cuando lanzaste la app, puedes realizar cambios y verlos reflejados automáticamete en tu aplicación en vivo. Durante el proceso de building tu aplicación no hará pausas ni dejará de servir requests, porque esta plataforma ofreces "zero-downtime deployments".


As long as you left the default Autodeploy option enabled when you first launched this app, you can now make code changes and see them automatically reflected in your live application. During these automatic deployments, your application will never pause or stop serving request because the App Platform offers zero-downtime deployments.

## Aprender más ##

Más información sobre administrar y actualizar tu aplicación se encuentra en https://www.digitalocean.com/docs/apps/.


## Eliminar la App #

Cuando no necesites más la plicación puedes borrarla siguiente estos pasos:

1. Visitar el plane de control de Apps en  https://cloud.digitalocean.com/apps
1. Navigar hasta la app
1. Elegir "Settings"->"Destroy"

Esto eliminará la app y destruirá los recursos respectivos

**Nota: Si no eliminas la app  DigitalOcean cobrará por los servicios respectivos.**
