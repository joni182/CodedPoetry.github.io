---
layout: post
title: "Openstack Labs"
date: 2017-07-21 20:24:24
description: ¡Despliega un entorno de pruebas de Openstack en tu portátil!
tags: openstack IaaS
categories: Samples
twitter_text: Openstack Lab en 2 pasos
---

[Openstack](https://www.openstack.org/) es una plataforma OpenSource de Cloud Computing que te permite crear en tus propios servidores una nube privada para crear máquinas virtuales, discos duros, redes... etc bajo demanda. Para los que conozcan **AWS**, es el equivalente (más o menos) al servicio EC2.

Montar un clúster de Openstack es una tarea bastante desafiante... pero para trastear en local es muy fácil generarte un entorno de pruebas ¡en dos pasitos!

(**NOTA:** Este artículo no es más que un brevísimo resúmen de la info que podéis encontrar en [Openstack Training Labs](https://github.com/openstack/training-labs) con más detalle.)

## 0. Requisitos previos

Simplemente tener instalado **VirtualBox**.

## 1. Descarga la distribución de Openstack Training Labs
Aquí encontrarás la úlima versión de [Openstack Training Labs](https://docs.openstack.org/training_labs/). Yo la he probado con la versión **Liberty**.
```shell
wget http://tarballs.openstack.org/training-labs/dist/labs-stable-liberty.tgz

tar xvzf labs-stable-liberty.tgz
```

## Arranca y ¡a jugar!

```shell
cd labs/osbash/

./osbash.sh -g gui -b cluster
```

Tendrás que esperar un buen ratito... paciencia, al final valdrá la pena! :P Podrás acceder a la consola web de Openstack en [http://10.0.0.11/horizon](http://10.0.0.11/horizon). Las contraseña para el usuario _admin_ es *admin_user_secret* y para el usuario *demo_user_ es _demo_user_pass*.

Espero que os haya sido útil. **Enjoy!!** ;)
