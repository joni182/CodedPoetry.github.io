---
layout: post
title: "Run Docker Registry Script"
date: 2017-05-19 18:46:42
image: '/assets/img/'
description: Como ejecutar un "Docker registry" privado en dos pasos para usar en entornos locales
tags:
- docker
categories: 
- Snippets
twitter_text: "Run Docker Registry Script"
--- 

## 1. Obtener el certificado
Lo ideal es usar un certificado para securizar el registry. Si queremos usar un certificado auto-firmando puedes usar el [script que hicimos para generarlos fácilmente]({{site.url}}/script-certificados-autofirmados/).

## 2. Ejecutar el registry

Una vez tenemos el certificado y la clave secreta (_mydomain.crt_ y _mydomain.key_, respectivamente), lanzaremos el registry usando este comando:

```sh
docker run -d -p 5000:5000 --restart always --name registry \
-v $(pwd)/data:/var/lib/registry 
-v $(pwd)/certs:/certs \
-e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/mydomain.crt \
-e REGISTRY_HTTP_TLS_KEY=/certs/mydomain.key \
registry:2
```

Puedes sustituir `$(pwd)` por la ruta donde quieras almacenar los datos de tu registry privado.

# 3. Distribuir el certificado a los clientes
Todas aquellas máquinas con Docker quieran usar el registry deberán tener el certificado para garantizar que la comunicación es segura. Para ello, copiemos el archivo 'mydomain.crt' en el directorio `/etc/docker/certs.d/mydomain.com:5000/ca.crt`

# 4. ¡Usa el registry!

Ya puedes usar el registry desde cualquier máquina con docker:

```shell
docker pull alpine
docker tag alpine mydomain.com:5000/alpine
docker push mydomain.com:5000/alpine
docker pull mydomain.com:5000/alpine
```