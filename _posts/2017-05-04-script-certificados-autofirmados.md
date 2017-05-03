---
layout: post
title: "Certificados autofirmados en un comando!"
image: '/assets/img/'
description: 'Os dejo un script para generar certificados autofirmados en un solo comando '
tags:
- linux 
- certificates
categories:
- Snippets
twitter_text: 'Genera certificados autofirmados en un solo comando!'
---

Crear certificados autofirmados para usar en pruebas o redes privadas suele ser bastante engorroso. Con este script podréis generarlos fácilmente con una sola línea de comando: `./gen-cert.sh mydomain.test`

### Script: gen-cert.sh
{% highlight shell %}

# Usage
#
# $ ./gen-cert.sh myweb 
# Will generate "myweb.crt" and "myweb.key"

CERT_NAME=$1

# Generate a Private Key

openssl genrsa -des3 -out $CERT_NAME.key 1024

# Generate a CSR (Certificate Signing Request)

openssl req -new -key $CERT_NAME.key -out $CERT_NAME.csr


# Remove Passphrase from Key

cp $CERT_NAME.key $CERT_NAME.key.org
openssl rsa -in $CERT_NAME.key.org -out $CERT_NAME.key

# Generating a Self-Signed Certificate

openssl x509 -req -days 365 -in $CERT_NAME.csr -signkey $CERT_NAME.key -out $CERT_NAME.crt

echo "Generated Certificates: "
echo "   - $CERT_NAME.key"
echo "   - $CERT_NAME.crt"
echo

{% endhighlight %}