# Cómo escribir un responder 

## Tabla de contenido
  * [Escribir un responder](#escribir-un-responder)
    * [Programa](#programa)
    * [Archivos de interacción de servicio (sabores)](#archivos-interaccion-servicio-sabores)
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
El primer tipo de archivos de los que está hecho un responder es el programa principal que 
realiza acciones. Se puede escribir en cualquier lenguaje de programación compatible con Linux. 

Puede escribir sus respuestas en Python, Ruby, Perl o incluso Scala. Sin embargo, la muy útil biblioteca `Cortexutils` [descrita a continuación](#python-cortexutils-biblioteca) está en Python. Facilita enormemente el desarrollo del responder y también proporciona algunos métodos para darle formato rápidamente a la salida para que sea compatible con el esquema JSON esperado por The Hive.

### Service Interaction Files (Flavors)
A responder must have at least one service interaction file. Such files
contain key configuration information such as the responder's author
information, the datatypes (`thehive:case`, `thehive:alert`, ...) the responder accepts as
input and to which it applies to if used from TheHive, the TLP and PAP (or [Permissible Actions Protocol](https://www.misp-project.org/taxonomies.html#_pap)) above which it will refuse to execute to protect against data
leakage and to enforce sane OPSEC practices and so on.

A responder can have two or more service interaction files to allow it to
perform different actions.  We speak then of flavors. For example, a Mailer
responder can send message using several body templates.

