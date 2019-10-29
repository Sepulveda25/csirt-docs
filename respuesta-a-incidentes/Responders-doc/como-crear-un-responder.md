# Cómo escribir un responder 

## Tabla de contenido
  * [Escribir un responder](#escribir-un-responder)
    * [Programa](#programa)
    * [Archivos de interacción de servicio (sabores)](#archivos-interacción-servicio-sabores)
    * [Requisitos de Python](#requisitos-python)
    * [Ejemplo: Archivos de respuesta de correo electrónico](#archivos-de-respuesta-de-ejemplo-correo-electrónico)
    * [Entrada](#entrada)
    * [Elementos de configuración de interacción de servicio](#servicio-interacción-configuración-elementos)
    * [Configuración de respuesta en el archivo de configuración global](#respuesta-configuración-en-el-archivo-configuración-global)
    * [Salida](#salida)
    * [Python Cortexutils Biblioteca](#python-cortexutils-biblioteca)
 
## Escribir un responder
Un responder es un programa que toma la entrada JSON y realiza una acción y produce un resultado básico de esa acción. Los respondedores son muy similares a los analizadores, aunque tienen diferentes propósitos. Los respondedores están formados por al menos 2 tipos de archivos:

- El programa en sí
- Uno o varios archivos de interacción de servicio o sabores
- Un archivo de requisitos de Python, que solo es necesario si el respondedor está 
escrito en Python.

### Programa
El primer tipo de archivos de los que está hecho un responder es el programa principal que  realiza acciones. Se puede escribir en cualquier lenguaje de programación compatible con Linux. 

Puede escribir sus responders en Python, Ruby, Perl o incluso Scala. Sin embargo, la muy útil biblioteca `Cortexutils` [descrita a continuación](#python-cortexutils-biblioteca) está en Python. Facilita enormemente el desarrollo del responder y también proporciona algunos métodos para darle formato rápidamente a la salida para que sea compatible con el esquema JSON esperado por The Hive.


### Archivos de interacción de servicio (sabores) 
Un responder debe tener al menos un archivo de interacción de servicio. Dichos archivos contienen información de configuración clave, como la información del autor del responder, los tipos de datos (`thehive: case`,` thehive: alert`, ...) que el responder acepta como entrada y a la que se aplica si se usa desde TheHive, el TLP y PAP (o [Protocolo de Acciones Permisibles](https://www.misp-project.org/taxonomies.html#_pap)) por encima del cual se negará a ejecutar para proteger contra la fuga de datos y para hacer cumplir prácticas sensatas de OPSEC, etc. 

Un responder puede tener dos o más archivos de interacción de servicio para permitirle realizar diferentes acciones. Hablamos entonces de sabores. Por ejemplo, un responder de correo puede enviar mensajes usando varias plantillas de cuerpo.

