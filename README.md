# INSTALACIÓN Y CONFIGURACIÓN DE UN JENKINS EN UN CONTENEDOR DE DOCKER

## Levantar un contenedor con jenkins instalado

Se va a levantar un contenedor docker a partir de la imágen que se encuentra en dockerHub "jenkins/jenkins". Este contenedor escuchará de los puertos 8080 y 50000. Además se le asignará un volumen "jenkins_home_test" para tener persistencia de la configuración y los datos del mismo, para que estos datos no se pierdan en caso de apagar el contenedor o en caso de caída.

  docker run -p 9080:8080 -p 50001:50000 -d -v jenkins_home_test:/var/jenkins_home jenkins/jenkins:lts

Si accedemos ahora a http://localhost:9080 entraremos en la configuración inicial del jenkins.

Para encontrar la contraseña inicial debemos acceder a los logs del contendor a partir de su ID. Primero comprobamos el ID, y luego ejecutamos el comando que muestra dichos logs.

  docker ps
  docker logs <container_ID>

Para acceder a la carpeta de configuración en el sistema de archivos de windows, en caso de necesitarlo, basta con pegar esta sentencia en el pathing:

  \\wsl$\docker-desktop-data\data\docker\volumes

## SI EXISTE ERROR DE SSL Y CERTIFICADOS, HACER ESTOS PASOS

En caso de error con los certificados autofirmados y error de SSL, proseguir sin instalar plugins y configurar en el apartado de configuración avanzada de plugins el campo "Dirección para la actualización", cambiándo el valor a http://updates.jenkins.io/update-center.json

Descargar manualmente el plugin para saltar el chequeo de certificados e instalarlo de forma manual. Posteriormente se debe reiniciar la instancia o contenedor que contiene jenkins.
http://plugins.jenkins.io/skip-certificate-check/releases/

## Instalar plugins: 
Se instalarán los siguientes plugins de la lista, que son básicos para empezar a jugar con la herramienta:

- Docker Pipeline
- Pipeline Utility Steps
- Git
- Git client
- GitHub API
- GitHub
- GitHub Branch Source
- Git Server
- Pipeline: Job
- Pipeline: Groovy
- Pipeline: Basic Steps
- Pipeline: Stage Steps
- Pipeline: Declarative
- Mailer
- Email Extension


## Crear pipeline vinculada a repo:
https://github.com/delreina/devops_pipelines
