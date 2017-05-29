---
layout: post
title: "JIRA Datacenter with Docker Compose"
date: 2017-05-29 22:08:47
description: Un script de Docker Compose para crear un entorno de pruebas de JIRA Datacenter
tags:
- jira
- docker
categories:
- Samples
twitter_text: JIRA Datacenter with Docker Compose
---

Este va para los artesanos de Atlassian ;). He creado un pequeño [proyecto en Github](https://github.com/fllaca/jira-datacenter-docker) para generar un entorno de pruebas para [JIRA Datacenter](https://confluence.atlassian.com/adminjiraserver071/installing-jira-data-center-802592197.html), el "setup" de JIRA para montar un entorno escalado, balanceado y que garantize alta disponibilidad. Este proyecto levanta todos los componentes de la arquitectura en contenedores y está pensado para probar desarrollos de plugins, integraciones o whatever fácilmente en local.

## Listo para usarlo

Simplemente clona el repositorio en local y ejecuta un script incluido en el mismo para preparar el directorio de trabajo:

```bash
git clone https://github.com/fllaca/jira-datacenter-docker

cd jira-datacenter-docker
# this init script is necessary to change the permissions of the JIRA home folders so the JIRA daemon can write to them
./init.sh
docker-compose up -d
```

Ya puedes acceder a [http://localhost:9090](http://localhost:9090) y hacer la configuración inicial de JIRA con tus datos:

![Initial JIRA setup](/assets/img/jira-setup.png)

Y ¡listo!, solo nos quedaría reiniciar el nodo 2 para que recargue los cambios en la configuración: `docker-compose restart node2 `. El balanceador está configurado para escuchar por el puerto 80, por lo que para acceder al datacenter de JIRA solo tienes que acceder a [http://localhost](http://localhost). Puedes modificar los puertos editando el archivo `docker-compose.yml`. Enjoy!
