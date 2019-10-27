# Ejercicios Tema 1

## Ejercicio 1
>*Buscar una aplicación de ejemplo, preferiblemente propia, y deducir qué patrón es el que usa.¿Qué habría que hacer para evolucionar a un patrón tipo microservicios?*

En 2018 creé junto con una compañera, como proyecto de una asignatura, un web sobre un museo arqueológico de Andalucia. Esta web tenía una arquitectura por capas del tipo cliente servidor. Los lenguajes usados fueron PHP y JavaScript para la parte del servidor y HTML y CSS para el modelado de las vistas. Los datos se guardaban en una base de datos MySQL.

Para transformarla a una arquitectura de microservicios tendriamos que identificar todas aquellas funciones que la web poseía y convertirlas en microservicios desplegados individualmente. Esto le aportaría una mayor escalabilidad, permitiendo así incluir nuevos servicios rapidamente sin necesidad de modificar los ya existentes.

Algunas de las funcionalidades que se podrían convertir en microservicios sería la gestión de usuarios del sistema. Convirtiendo el sistema de login en un microservicio.
Otra sería el control de obras teniendo este microservicio todas las obras que se exiben en la pagina.
Las colecciones de Obras también podrían ser separadas en microservicios. Este microservicio tendría que comunicarse con obras para poder tener las coleciones ordenadas por culturas.
Por ultimo el sistema de comentarios también podria pasar a ser un microservicio y descargaría de trabajo a la base de datos principal.
## Ejercicio 2
>*En la aplicación que se ha usado como ejemplo en el ejercicio anterior,¿podría usar diferentes lenguajes?¿Qué almacenes de datos serían los más convenientes?*

Cada servicio podría contar con un lenguaje diferente sin afectar lo mas mínimo al funcionamiento de la aplicación.

Debido a la naturaleza de los datos con los que trabajaba la web seria conveniente continuar usando las bases de datos de MySQL. Sin embargo también podria mejorar algunos aspectos como la sección de comentarios usando una base de datos en tiempo real que actualizara los comentarios sin necesidad de recargar la página.
