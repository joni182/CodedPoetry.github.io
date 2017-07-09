---
layout: post
title: "Dockerfile Tips for Java"
date: 2017-07-09 20:48:43
description:
tags:
- docker
categories:
- tips
twitter_text:
---

## Configuration

### Mount Configuration files

Esta opción en realidad no es la más recomendable, pero podemos montar los archivos de configuración en los contenedores:

```shell
docker run -v /host/config/path/application.properties:/container/config/path/application.properties
```

En el Dockerfile, tendríamos que indicar la ruta del contenedor con los archivos de configuración:

```shell
CMD java -jar --spring.config.location=file:/container/config/path/application.properties my-service.jar ...
```

Como digo, en general es más recomendable inyectar la configuración como veremos en el siguiente apartado.

### Environment Variables

Los parámetros de los archivos de configuración de Spring Boot pueden sobreescribirse con variables de entorno. Por ejemplo: `SERVER_PORT=4444 java -jar my-service.jar` sobreescribirá el parámetro `server.port=3333` del archivo _application.properties_. Una práctica es crear variables de entornos más sencillas para hacer más legible y fácil la configuración:

```properties
# Discovery Server Access
eureka:
  instance:
    preferIpAddress: true
  client:
    serviceUrl:
        defaultZone: ${DISCOVERY_SERVER:'http://localhost:1111/eureka/'}
```

En este ejemplo, podemos usar la variable de entorno `DISCOVERY_SERVER` en lugar de la más farragosa `EUREKA_INSTANCE_CLIENT_SERVICEURL_DEFAULTZONE`. En este ejemplo podéis ver también como indicar un valor por defecto.


### Expose PORTS

Aunque no es obligatorio, es altamente recomendable exponer explícitamente los puertos que use nuestra aplicación:

```
EXPOSE 3333
```

### Logging

El uso habitual en la mayoría de orquestadores es recoger los logs del standar output, en este caso es buena práctica configurar tu framework de logs para escribir en `stdout`. 

En caso de querer escribir logs en archivos específicos, debes asegurarte de persistirlos montando un volumen y permitir la escritura en el mismo.

```
docker run -v /host/logs/:/container/logs/paths/ ...
```

Otra recomendación es estandarizar el formato de los logs, a la hora de centralizarlos y agregarlos, esto facilitará el análisis de los mismos.

## Performance

### Health Checks
Algunos orquestadores o herramientas como Docker-Compose usan HealthChecks de los contenedores para optimizar su funcionamiento. Un healthcheck notifica con un sencillo código el estado del contenedor, de forma que los orquestadores saben si deben matarlo para sustituirlo o no.
```
HEALTHCHECK --interval=5m --timeout=3s \
  CMD curl -f http://localhost:3333/health || exit 1
```

### Java Out Of Memory Errors

Podemos usar un [script como este](https://github.com/fabric8io-images/java/blob/master/images/jboss/openjdk8/jdk/container-limits) para calcular los límites de CPU y/o memoria en el contenedor. 

## Security

### Running User

Usar un usuario específico para ejecutar los procesos:

```dockerfile
RUN adduser -D java
USER java
```

### Security Image Scan

La imagen Docker que construyas puede contener vulnerabilidades debido a ciertas versiones de paquetes o librerías que uses. Existen servicios que te permiten escanear estáticamente las imágenes y te generan un informe con las posibles vulnerabilidades que estas contienen. [TwistLock](https://www.twistlock.com/) o algunas versiones de pago de [Docker Cloud](https://docs.docker.com/docker-cloud/builds/image-scan/) son algunas opciones.

## Useful Tools

### Maven Plugins

