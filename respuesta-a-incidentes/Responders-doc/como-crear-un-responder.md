# Cómo escribir un responder 

## Tabla de contenido
  * [Escribir un responder](#escribir-un-responder)
    * [Programa](#programa)
    * [Archivos de interacción de servicio (sabores)](#archivos-de-interacción-de-servicio-sabores)
    * [Requisitos de Python](#requisitos-python)
    * [Ejemplo: Archivos de respuesta de correo electrónico](#archivos-de-respuesta-de-ejemplo-correo-electrónico)
    * [Entrada](#entrada)
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

### Requisitos de Python 
Si el responder está escrito en Python, se debe proporcionar un `require.txt` con la lista de todas las dependencias. 

### Ejemplo: Archivos de respuesta de correo electrónico
A continuación se muestra una lista de directorio de los archivos correspondientes a un responder que usa correo electrónico para notificar. 

```bash
responders/Mailer
|-- Mailer.json
|-- requirements.txt
`-- mailer.py
```
### Entrada 
La entrada de un responder puede ser cualquier dato JSON, incluso un simple string. El remitente debe enviar datos con la estructura esperada por el programa. Los tipos de datos aceptables descritos en los archivos de interacción de servicio indican qué tipo de datos se espera. 
Por ejemplo, si el programa requiere un `thehive: case` (es decir, se aplica a nivel de caso en TheHive), la entrada debe cumplir con el caso de TheHive. Debajo de un ejemplo de entrada `thehive: case`. 

```json
	{
		"caseId":1484,
		"title":"Invoice mail",
		"description":"...",
		"startDate":1532586900000,
		"customFields":{},
		"pap":2,
		"flag":false,
		"status":"Open",
		"owner":"me",
		"severity":2,
		"tlp":2,
		"tags":["phishing"],
		"metrics":{},
		"createdBy":"me",
		"createdAt":1532586924589,
		"updatedBy":"me",
		"updatedAt":1532697739274,
		"_type":"case",
		"_routing":"AWTVTJzWpVfF5eRPi-qd",
		"_parent":null,
		"_id":"AWTVTJzWpVfF5eRPi-qd",
		"_version":3,
		"id":"AWTVTJzWpVfF5eRPi-qd",
		"config":{
			"key":"1234567890abcdef",
			"max_tlp":1,
			"check_tlp":true,
			"max_pap":1,
			"check_pap":true,
			[..]
		},
		"proxy":{
			"http":"http://myproxy:8080",
			"https":"https://myproxy:8080"
		}
	}
```

Además de la entrada enviada por el remitente, Cortex agrega la sección `config`, que es la configuración específica del responder proporcionada por un usuario `orgAdmin` cuando el responder está habilitado en la interfaz de usuario de Cortex. 

#### Ejemplo: archivo de interacción de servicio para el responder de correo 
El signo `<==` y todo lo que sigue son comentarios que no aparecen en el 
archivo original. 

```json
{
  "name": "Mailer",
  "version": "1.0",
  "author": "CERT-BDF",
  "url": "https://github.com/TheHive-Project/Cortex-Analyzers",
  "license": "AGPL-V3",
  "description": "Send an email with information from a TheHive case or alert",
  "dataTypeList": ["thehive:case", "thehive:alert"],
  "command": "Mailer/mailer.py", <== Program to run when invoking the responder
  "baseConfig": "Mailer", <== name of base config in Cortex responder config page
  "config": {
    <== any configuration items
  },
  "configurationItems": [ <== list of configuration items the responder needs to operate (api key etc.)
    {
      "name": "from",
      "description": "email address from which the mail is send",
      "type": "string", <== defines what kind of data type the configuration item is (string, number)
      "multi": false, <== setting multi to true allows to pass a list of items
      "required": true 
    },
    {
      "name": "smtp.host",
      "description": "SMTP server used to send mail",
      "type": "string",
      "multi": false,
      "required": true,
      "defaultValue": "localhost"
    }
  ]
}
```
### Salida 
La salida de un responder depende del éxito o fracaso de su ejecución.

Si el respondedor **falla** al ejecutar: 

```json
{
    "success": false,
    "errorMessage":".."
}
```

-   Cuando `success` se establece en` false`, indica que algo salió mal durante la ejecución.
-   `errorMessage` es texto libre, generalmente el mensaje de salida de error.


