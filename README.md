# INSTALACIÓN Y CONFIGURACIÓN DE UN JENKINS EN UN CONTENEDOR DE DOCKER

## PRE-REQUISITOS

- WSL (Windows Subsystem for Linux)
Con este comando se puede en principio instalar la distribución de Ubuntu para wsl de forma sencilla:

        wsl --install -d Ubuntu

- Docker (Docker Desktop es el más fácil desde la página oficial del proveedor) 

## Levantar un contenedor con jenkins instalado

Se va a levantar un contenedor docker a partir de una imagen customizada a partir de la imagen de dockerHub "jenkins/jenkins" que simplemente tiene instalado maven.

Crearemos entonces un archivo con el nombre "Dockerfile" en el que introduciremos este código para posteriormente construir la imagen a raíz de este archivo (podemos encontrar el Dockerfile en la carpeta "jenkins" del repositorio gitHub si no se quiere hacer manualmente):

    FROM jenkins/jenkins:lts
    USER root
    RUN apt-get --fix-missing update && apt-get install -y maven
    USER jenkins

Este contenedor escuchará de los puertos 8080 y 50000. Además se le asignará un volumen "jenkins_home_test" para tener persistencia de la configuración y los datos del mismo, para que estos datos no se pierdan en caso de apagar el contenedor o en caso de caída. Hay que taguear la imagen ejecutando el comando build de docker en el directorio donde se encuentra el Dockerfile. También se puede indicar el path en vez de usar el ".", tanto con una ruta dinámica como absoluta.

    docker build -t jenkins-modified-maven .
    docker run -p 9080:8080 -p 50001:50000 -d -v jenkins_home_test:/var/jenkins_home jenkins-modified-maven

Si accedemos ahora a http://localhost:9080 entraremos en la configuración inicial del jenkins.

Para encontrar la contraseña inicial para proceder con la configuración de jenkins debemos acceder a los logs del contendor a partir de su ID. Primero comprobamos el ID con "docker ps", y luego ejecutamos el comando que muestra dichos logs.

    docker ps
    docker logs <container_ID>

Para acceder a la carpeta de configuración en el sistema de archivos de windows, en caso de necesitarlo, basta con pegar esta sentencia en el pathing:

    \\wsl$\

## SI EXISTE ERROR DE SSL Y CERTIFICADOS, HACER ESTOS PASOS

En caso de error con los certificados autofirmados y error de SSL, proseguir sin instalar plugins y configurar en el apartado de configuración avanzada de plugins el campo "Dirección para la actualización", cambiándo el valor a http://updates.jenkins.io/update-center.json y presionando el botón "Enviar". 

Descargar también manualmente el plugin para saltar el chequeo de certificados e instalarlo de forma manual. Posteriormente se debe reiniciar la instancia o contenedor que contiene jenkins para que se apliquen los cambios correctamente.
http://plugins.jenkins.io/skip-certificate-check/releases/

## Instalar plugins: 
Si todo ha ido correctamente, en la administración de jenkins, en el apartado de plugins, podremos ver los disponibles para descargar e instalar. Se instalarán los siguientes plugins de la lista, que son básicos para empezar a jugar con la herramienta:

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

# DESPLIEGUE DE UN CONTENEDOR DOCKER CON UN APACHE TOMCAT PARA DESPLEGAR APLICACIONES EN SU INTERIOR

Paralelamente a lo realizado en pasos previos, vamos a proceder a montar un tomcat en un contenedor, por lo que vamos a repetir los pasos anteriores pero con otra imagen customizada que podemos encontrar en la carpeta "tomcat" de nuestro repositorio. Crearemos el Dockerfile, crearemos la imagen haciendo un docker build y desplegaremos la imagen en un contenedor con docker run. Este dockerfile es algo más complejor pero se puede buscar información de cómo montar estos Dockerfile en google en la documentación oficial de Docker.

        FROM alpine:latest
        USER root
        RUN apk update && apk upgrade
        RUN apk fetch openjdk8 && apk add openjdk8
        RUN cd tmp && wget http://dlcdn.apache.org/tomcat/tomcat-9/v9.0.76/bin/apache-tomcat-9.0.76.tar.gz
        RUN tar -zxvf /tmp/apache-tomcat-9.0.76.tar.gz -C /usr/local
        COPY ./tomcat-user.xml /usr/local/apache-tomcat-9.0.76
        ## Renombrar la carpeta descomprimida a "tomcat"
        RUN cd /usr/local && mv apache-tomcat-9.0.76 tomcat
        ## Eliminar instaladores descargados
        RUN cd /tmp && rm -rf *
        ## Exponer puerto 
        EXPOSE 8081
        ## Iniciar tomcat 
        CMD sh /usr/local/tomcat/bin/catalina.sh start && tail -f /usr/local/tomcat/logs/catalina.out

Para montar la imagen ejecutaremos. En este caso no haremos uso de la persistencia con volumenes para hacerlo más sencillo (tener cuidado con no exponer en el mismo puerto en el que está jenkins expuesto, ya que se generarán conflictos. En mi caso tengo libre el 8081 por lo que expondré ese puerto):

        docker build -t tomcat-custom .
        docker run -p 8081:8080 -d tomcat-custom

Ahora podremos acceder a nuestra instancia de tomcat accediendo a la url: http://localhost:8081, o modificándola dependiendo del puerto expuesto.


# PARA PROFUNDIZAR

Aquí se encuentra un repositorio con un taller más avanzado para curios@s, en el que se hacen uso de diferentes herramientas:

https://github.com/deors/workshop-pipelines
