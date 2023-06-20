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