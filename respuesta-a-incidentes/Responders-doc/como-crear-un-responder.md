# Cómo escribir un responder 

## Tabla de contenido 
  * [Escribir un responder] (#escribir-un-responder)
    * [Programa] (#programa)
    * [Archivos de interacción de servicio (sabores)] (#archivos-interaccion-servicio-sabores)
    * [Requisitos de Python] (#requisitos-python)
    * [Ejemplo: Archivos de respuesta de correo electrónico] (#archivos-de-respuesta-de-ejemplo-correo-electrónico)
    * [Entrada] (#entrada)
    * [Elementos de configuración de interacción de servicio ] (#servicio-interacción-configuración-elementos)
    * [Configuración de respuesta en el archivo de configuración global] (#respuesta-configuración-en-el-archivo-configuración-global)
    * [Salida] (# salida)
    * [The Python Cortexutils Biblioteca] (# the-python-cortexutils-library)

## Table of Contents
  * [Writing a Responder](#writing-an-responder)
    * [The Program](#the-program)
    * [Service Interaction Files (Flavors)](#service-interaction-files-flavors)
    * [Python Requirements](#python-requirements)
    * [Example: Mailer Responder Files](#example-mailer-responder-files)
    * [Input](#input)
    * [Service Interaction Configuration Items](#service-interaction-configuration-items)
    * [Responder Configuration in the Global Configuration File](#responder-configuration-in-the-global-configuration-file)
    * [Output](#output)
    * [The Cortexutils Python Library](#the-cortexutils-python-library)
  * [Submitting a Responder](#submitting-an-responder)
    * [Check Existing Issues](#check-existing-issues)
    * [Open an Issue](#open-an-issue)
    * [Review your Service Interaction File(s)](#review-your-service-interaction-files)
    * [Provide the List of Requirements](#provide-the-list-of-requirements)
    * [Verify Execution](#verify-execution)
    * [Create a Pull Request](#create-a-pull-request)
  * [Need Help?](#need-help)

 
## Escribir un respondedor 
Un respondedor es un programa que toma la entrada JSON y realiza una acción y produce un resultado básico de eso acción. Los respondedores son muy similares a los analizadores, aunque tienen diferentes propósitos. Los respondedores están formados por al menos 2 tipos de archivos:

- El programa en sí 
- Uno o varios archivos de interacción de servicio o sabores 
- Un archivo de requisitos de Python, que solo es necesario si el respondedor está 
escrito en Python. 
