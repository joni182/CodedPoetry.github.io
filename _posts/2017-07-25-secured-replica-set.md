---
layout: post
title: "Secured Communication Between Replica Set Members"
author: pablo
date: 2017-07-25 19:00:00
description: ¡Despliega un replica set con comunicaciones seguras en  segundos!
tags: MongoDB Databases Security
categories: Scripting
twitter_text: Secured Communication Between Replica Set Members
---

En primer lugar muchas gracias a Fer por permitirme el inmenso honor de contribuir a su gran trabajo, espero estar a la altura :).

Puesto que estamos enfrascados en la obtención del MongoDB Certified DBA Associate Exam es muy común el tener que ir desplegando, configurando y volviendo a desplegar una y mil veces un replica set en Mongo para hacer nuestras pruebas. Para ello os propongo aquí un script en el que levantamos de manera sencilla y rápida un replica set básico formado por tres miembros: un primario, un secundario y un árbitro para romper los empates del **primary election**.

Para enriquecer aún más este despliegue vamos a crear este replica set con autenticación interna, es decir entre los miembros del replica set. Para ello utilizaremos **keyfiles** que compartirán todos los miembros del replica set como mecanismo de autenticación.

Los **keyfiles** utilizados por Mongo se basan en **SCRAM-SHA-1**, siguiendo el estándar [RFC 5802](https://tools.ietf.org/html/rfc5802).

Además crearemos un usuario root para poder administrar la base de datos y los usuarios que queramos que accedan a ella.

#### secured_replica_set.sh

```shell
#!/bin/bash
# Usage
#
# $ ./secured_replica_set.sh 27017 username	password
# Will deploy a replica set with one primary and two secondaries in port range [27017 - 27019].
# In addition second and third parameter is for creating a root user to administrate the replica set

MONGO_KEY_FILE='mongodb-keyfile'
REPL_SET_NAME='securedReplSet'

#Generating a Keyfile
openssl rand -base64 755 > $MONGO_KEY_FILE

#setting this file in only read mode
chmod 400 $MONGO_KEY_FILE

#creating directories for each member of the replica set
mkdir -p rs/member0/db rs/member1/db rs/member2/db

#creating replica set with this keyfile
for MEMBER in {0..2};
do

MONGO_PORTS[$MEMBER]=$(($1 + $MEMBER))

mongod --replSet $REPL_SET_NAME --dbpath ./rs/member$MEMBER/db --logpath ./rs/member$MEMBER/mongod.log --port ${MONGO_PORTS[$MEMBER]} --fork -keyFile $MONGO_KEY_FILE --auth

done

#Creating config for replica set
CONFIG="{_id: \"$REPL_SET_NAME\",version: 1,members: [{_id: 0, host: \"localhost:${MONGO_PORTS[0]}\"},{_id: 1, host: \"localhost:${MONGO_PORTS[1]}\"},{_id: 2, host: \"localhost:${MONGO_PORTS[2]}\", arbiterOnly: true} ]}"

#Initializing replica set
mongo --port $1 --eval "rs.initiate($CONFIG)"


#Creating root user
USER_CONFIG="{user: \"$2\", pwd: \"$3\", roles: [ { role: \"root\", db: \"admin\" } ] }"


echo "Waiting for replica set primary election"
sleep 15  

mongo admin --port $1 --eval "db.createUser($USER_CONFIG)"

echo "Root User Created"
```

Como podéis observar antes de crear nuestro usuario root hemos levantado los miembros del replica set con la opción **--auth** que activa el control de acceso a los usuarios. Si hemos activado esta opción .. ¿cómo hemos sido capaz de crear un usuario root?.

Muy sencillo! Gracias al **Localhost Exception** de Mongo. Para tener que evitar la tediosa tarea de levantar un servidor sin la seguridad activa, crear un usuario y volver a redesplegar con la seguridad activa, Mongo nos permite crear un usuario en la base de datos admin y a partir de ahí se activa la seguridad (eso sí, solo podemos hacerlo desde la máquina donde está desplegado el servidor).

Para conectarnos a nuestro replica set solo tenemos que usar nuestro username y password

```shell

mongo admin --port puerto -u username -p password

```

Espero que os sirva de ayuda!.
