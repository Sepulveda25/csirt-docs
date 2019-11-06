# Guía de administracion

## 1. Gestión de usuarios

Los usuarios se pueden administrar a través de la página `Administration` > `Users`. Solo los administradores pueden acceder a él. Cada usuario se identifica por su nombre de usuario, nombre completo y función.

![users](../images/thehive-user-management.png)

Tenga en cuenta que aún necesita crear cuentas de usuario si utiliza la autenticación LDAP o Active Directory. Esto es necesario para que TheHive recupere su rol y los autentique en la base de datos local, los directorios LDAP y/o AD.

Actualmente hay 4 roles:
 - `read` : altodos los datos no confidenciales se pueden leer. Con este rol, un usuario no puede hacer ningún cambio. No pueden agregar un caso, tarea, registro u observable. Tampoco pueden ejecutar analyzers;
 - `write`: crea, elimina y cambia datos de cualquier tipo. Este rol es para usuarios estándar. El rol `write` hereda los derechos de `read`;
 - `admin`: este rol está reservado para los administradores de TheHive. Los usuarios con este rol pueden administrar cuentas de usuario, métricas, crear plantillas de casos y tipos de datos observables. El rol `admin` los derechos de `write`;
 - `alert`: los usuarios con este rol solo pueden crear alertas;

**Advertencia**: tenga en cuenta que las cuentas de usuario no se pueden eliminar una vez que se han creado, de lo contrario, los registros de auditoría se referirán a un usuario desconocido. Sin embargo, las cuentas no deseadas o no utilizadas pueden bloquearse.

## 2. Gestión de plantillas de casos

Algunos casos pueden compartir la misma estructura (etiquetas, tareas, descripción, métricas). Las plantillas están para agregar automáticamente tareas, descripción, métricas y campos personalizados mientras se crea un nuevo caso. Un usuario puede elegir crear un caso vacío o basado en una plantilla registrada.

Para crear una plantilla, como _administrador_  ir al menú de administración y abrir el elemento "Case templates".

![template](../images/thehive-case-templates.png)

En esta pantalla, puede agregar, eliminar o cambiar la plantilla. Una plantilla contiene:
 * gravedad predeterminada
 * etiquetas predeterminadas
 * prefijo de título (puede cambiarlo el usuario al crear el caso)
 * TLP predeterminado
 * lista de tareas (título y descripción)
 * métrica
 * campos personalizados
 
Excepto por el prefijo del título, la lista de tareas y las métricas, el usuario puede cambiar los valores definidos en la plantilla.

## 3. Gestión de plantillas de informes

Cuando TheHive está conectado a un servidor Cortex, los observables se pueden analizar para obtener información adicional sobre ellos. Cortex genera informes en formato JSON. Para que los informes sean más legibles, puede configurar plantillas de informes. Las plantillas de informes convierten JSON en HTML utilizando el motor de plantillas AngularJS.

Para cada analyzer disponible en Cortex, puede definir dos tipos de plantillas: breve y largo. Un breve informe expone información sintética, que se muestra en la parte superior de la página observable. Con informes breves, puede ver un resumen de todos los analyzers de ejecución. Los informes largos muestran información detallada solo cuando el usuario selecciona el informe. Los datos sin formato en formato JSON siempre están disponibles.

Las plantillas de informes se pueden configurar en el menú `Admin` > `Report templates`. Ofrecemos plantillas de informes para analyzer Cortex predeterminados. Se puede descargar un paquete con todas las plantillas de informes en https://dl.bintray.com/thehive-project/binary/report-templates.zip y se puede inyectar usando el botón `Import templates`.

## 4. Gestión de métricas

Las métricas se han integrado para tener indicadores relevantes sobre los casos.

Las métricas son valores numéricos asociados a casos (por ejemplo, el número de usuarios afectados). Cada métrica tiene un _nombre_ , un _título_ y una _descripción_ , definidos por un administrador. Cuando se agrega una métrica a un caso, no se puede eliminar y se debe completar. Las métricas se utilizan para monitorear los indicadores, gracias a los gráficos.

Las métricas se definen globalmente. Para crear métricas, como administrador, vaya al menú de administración y abra el elemento "Case metrics.

![metrics](../images/thehive-case-metrics.png)


Las métricas se utilizan para crear estadísticas (elemento "Estadísticas" en el menú de perfil de usuario). Se pueden filtrar en intervalos de tiempo y en mayúsculas y minúsculas con etiquetas específicas.

Por ejemplo, puede mostrar las métricas del caso con la etiqueta "malspam" en enero de 2016:

![statistics](../images/thehive-statistics.png)

Para los gráficos basados ​​en el tiempo, el usuario puede elegir métricas para mostrar. Se agregan en intervalos de tiempo (por día, semana, mes del año) utilizando una función (suma, mínimo o máximo).

Algunas métricas están predefinidas (además de las definidas por el administrador) como la duración del manejo de casos (cuánto tiempo estuvo abierto el caso) y la cantidad de casos abiertos o cerrados.

