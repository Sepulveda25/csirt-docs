# Guía de Administración de Security Onion

En la siguiente guia se detalla la informacion para gestionar y administrar las herramientas del sistema operativo

[Volver a documento raíz](https://gitlab.unc.edu.ar/csirt/csirt-docs/tree/master#csirt-docs)

## Tabla de Contenidos

* [Actualizacion](#actualización)
* [Salt](#salt)
* [Nada](#actualización)
* [Mas](#actualización)

### Actualización

Se recomienda utilizar los scripts propios de Security Onion para actualizar el sistema.

[Link a guia de actualizacion](https://securityonion.readthedocs.io/en/latest/upgrade.html)


### Salt

Security Onion ofrece una herramienta para administrar multiples nodos forward con facilidad: Salt.

Salt ofrece un bus de comunicación dinámico para infraestructuras que se puede utilizar para orquestación, ejecución remota, gestión de configuración, etc.
La instalacion debe realizarse tanto en el nodo master como tambien en cada uno de los nodos forward.

Este paquete es totalmente opcional, en el caso de querer utilizarlo debe instalar el paquete correspondiente (ver guía) y luego acceder al script de ansible ubicado en sonion-config-ansible/host_vars/template_forward.yml o sonion-config-ansible/host_vars/template_master.yml y modificar la linea:

```bash
INSTALL_SALT: 'yes'
```

Para mas informacion, puede leer la guía oficial:

[Guía de Salt](https://securityonion.readthedocs.io/en/latest/salt.html)
