---
layout: post
title: "Spring Boot - In Memory Database Configuration"
description: 'Ejemplo sencillo para configurar una base de datos H2 en memoria en Spring Boot. ¡Ideal para tests de integración!'
tags:
- Spring Boot
- h2
- database
categories:
- Snippets
twitter_text: 'Base de datos H2 en memoria con Spring Boot'
---

Para hacer pruebas a menudo nos viene bien utilizar una **base de datos en memoria**, y precargarla con un script _.sql_ que tenemos con nuestros datos de prueba predefinidos. Aqui tenemos en dos pasos cómo hacer esta configuración en Spring Boot:

### pom.xml:
{% highlight xml %}
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>compile</scope>
</dependency>
{% endhighlight %}
### application.properties
{% highlight properties %}

# DataSource

spring.jpa.hibernate.ddl-auto=none
spring.datasource.initialize=false

spring.datasource.url=jdbc:h2:mem:sample;MODE=MYSQL;INIT=RUNSCRIPT FROM 'classpath:database/world.sql';DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE

spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect
{% endhighlight %}

(en este ejemplo buscará el script de inicialización de la base de datos en el classpath: `src/main/resources/database/world.sql` )

### Tip!

Recordad que podéis tener varios perfiles de Spring. Podemos renombrar los archivos `application.properties` a `application-<profile_name>.properties` y utilizar distintas configuraciones que podemos ejecutar de esta manera:

{% highlight shell %}

#utilizará la configuración de 'application-h2.properties'
mvn spring-boot:run -Dspring.profiles.active=h2 

{% endhighlight %}