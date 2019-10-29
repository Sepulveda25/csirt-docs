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
 
## Escribir un respondedor 
Un respondedor es un programa que toma la entrada JSON y realiza una acción y produce un resultado básico de eso acción. Los respondedores son muy similares a los analizadores, aunque tienen diferentes propósitos. Los respondedores están formados por al menos 2 tipos de archivos:

- El programa en sí 
- Uno o varios archivos de interacción de servicio o sabores 
- Un archivo de requisitos de Python, que solo es necesario si el respondedor está 
escrito en Python. 
