# Guía de Configuracion de Logstash

En esta guia contiene la configuracion para logstash

[Volver a documento raíz](https://gitlab.unc.edu.ar/csirt/csirt-docs/tree/master#csirt-docs)

## Tabla de Contenidos
  * [Configuracion general](#configuracion-general)
  * [Filtros](#filtros)
    - [Expresiones regulares](#expresiones-regulares)

### Configuracion general
Estas son algunas de las configuraciones que puede necesitar para ajustar `/etc/logstash/logstash.yml`.

* pipeline.batch.size

El número máximo de eventos que un solo hilo recopilará de las entradas antes de intentar ejecutar sus filtros y salidas. Los tamaños de lote más grandes son generalmente más eficientes, pero tienen el costo de una mayor sobrecarga de memoria.

* pipeline.workers

El número de hilos que, en paralelo, ejecutarán las etapas de filtro y salida del pipeline. Si encuentra que los eventos se están respaldando o que la CPU no está saturada, considere aumentar este número para utilizar mejor la potencia de procesamiento de la máquina.

Para obtener más información, consulte https://www.elastic.co/guide/en/logstash/current/logstash-settings-file.html.

* Logstash Heap

De forma predeterminada, si la memoria total disponible es de 8 GB o más, el tamaño de almacenamiento dinámico de Logstash `/etc/logstash/jvm.options` configura (durante la configuración) para que sea igual al 25% de la memoria disponible, pero no mayor que 4 GB.

Consulte https://www.elastic.co/guide/en/elasticsearch/guide/current/heap-sizing.html#compressed_oops para obtener más detalles.

Es posible que deba ajustar el valor en función del rendimiento de su sistema (ejecutar `sudo so-logstash-restart` después)

### Filtros
Por defecto Security Onion trae filtros que funcionan para varios tipos de entradas (como suricata, beats, etc) ademas se pueden colocar archivos de configuración para el análisis personalizado en el directorio `/etc/logstash/custom`. Estos se copiarán automáticamente `/etc/logstash/conf.d` durante el inicio de Logstash.
Si se desea modificar un regla que ya existente en el directorio `/etc/logstash/conf.d/` se debe eliminar el link simbolico previamente de la regla que se quiere modificar. Por ejemplo si se quiere modificar el filtro de suricata se debe ejecutar:
```bash
sudo unlink /etc/logstash/conf.d/0005_input_suricata.conf 
```
Luego lo recomendable es copiar el archivo ya existente en `/etc/logstash/conf.d.available/` al directorio `/etc/logstash/custom` para despues editar lo necesario sin alterar la estructura. Tambien puede haber casos en que ya se tenga un archivo modificado, en esos casos el directorio destino es el mismo. Por ejemplo:
```bash
sudo cp /etc/logstash/conf.d.available/0006_input_beats.conf /etc/logstash/custom/0006_input_beats_custom.conf
```
Un vez copiado el archivo al directorio `/etc/logstash/custom` ya puede ser modificado.
Después de agregar sus archivos de configuración personalizados o de haber modificado un archivo ya esxistente, reinicie Logstash y verifique los registros en busca de errores:

```bash
sudo so-logstash-restart && sudo tail -f /var/log/logstash/logstash.log
```

#### Expresiones regulares
Los fitros de logstash usan un plug in grok para realizar el parseo de la informacion. Links de ayuda:
* https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html
* http://grokdebug.herokuapp.com/
* http://grokconstructor.appspot.com/
