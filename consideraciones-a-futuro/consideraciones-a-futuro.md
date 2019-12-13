# Consideraciones a futuro 
En esta seccion se irá agregando los trabajos o consideraciones que se deben tener a futuro

[Volver a documento raíz](https://gitlab.unc.edu.ar/csirt/csirt-docs/tree/master#csirt-docs)

## Tabla de Contenidos

* [Elastic auth](#elastic-auth)

### Elastic auth
Esta opcion se probo en lo 

#### Guia de activación 

A partir de Elastic 6.8.0, la autenticación Elastic se incluye de forma gratuita en Elastic Features. Esto le permite asignar diferentes privilegios a diferentes usuarios en Kibana.

Para habilitar, simplemente ejecute solo `so-elastic-auth` en su servidor master y siga las instrucciones. `so-elastic-auth` hará lo siguiente:

* guiarlo a través del cambio de Elastic Features si es necesario
* habilita la autenticación en Elasticsearch, Logstash, Kibana, Curator y ElastAlert
* encuntra cualquier cuenta de usuario existente en la base de datos Sguil y cree las cuentas correspondientes en Elasticsearch con privilegio de solo lectura de forma predeterminada

Una vez que haya completado `so-elastic-auth`, debe:

* inicie sesión en Kibana con la `elastic` cuenta de superusuario
* establecer cualquier otro privilegio de cuenta según sea necesario
* distribuya las contraseñas temporales generadas por `so-elastic-auth` sus usuarios y haga que restablezcan sus contraseñas.

Tenga en cuenta que continuará autenticándose en Sguil, Squert y CapMe con su cuenta tradicional Sguil/Squert/CapMe.

link referencia:

* https://securityonion.readthedocs.io/en/latest/elastic-auth.html

#### Experiencia

Esta opcion se probo en servidor master. No se econtro la menera de limitar el acceso de usuarios a algunos indicies de Elasticsearch. Cualquier usurio con privilegios para ver indices, puede buscar agregar un nuevo patron de indice y de esta manera agregar indices que se supone que no deberia tener acceso.
