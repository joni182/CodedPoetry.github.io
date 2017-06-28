---
layout: post
title: "A Recursive Replace Text Script"
date: 2017-06-27 20:28:53
description:
tags:
- linux
- script
categories:
- Snippets
- Scripting
twitter_text: Renombrar carpetas, archivos y su contenido (Script)
---

Esta semana uno cortito: un pequeño script para renombrar carpetas, nombres de archivos y contenido de los mismos de manera recursiva, dentro de un directorio. Últimamente me ha hecho mucha falta en el curro y lo acabé escribiendo, espero que os sea útil a vosotros también :).

#### replace-text.sh
```bash
function replace_help() {

cat <<EOT
------------------------------------------------------------------------------
REPLACE TEXT - Recursively replaces text in folder names, file names and files
content in the current directory
------------------------------------------------------------------------------
Usage: ./replace-text.sh [options] <source-text-regex> <replacement>
Options:
  -h, --help        output instructions

Example:
  # will replace the regex "004" with text "010"
  ./replace-text.sh 004 010

------------------------------------------------------------------------------
EOT

}

# Show help
if [[ "${1}" == "-h" || "${1}" == "--help" ]]; then
    replace_help
    exit
fi

local SOURCE=$1
local TARGET=$2

shopt -s globstar
rename "s/$SOURCE/$TARGET/g" **
find . -type f -exec sed -i "s/$SOURCE/$TARGET/g" {} +

```
_(NOTA: la línea_ `shopt -s globstar` _sirve para poder utilizar el wildcard **, para iterar recursivamente sobre el directorio actual)_

Enjoy!!
