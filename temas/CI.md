# Integración continua


## Planteamiento

Los sistemas de integración continua forman parte del flujo de trabajo
de un sistema de calidad, ejecutando automáticamente las pruebas que
se le configuren cada vez que se lleva a cabo un `push` o un `pull request`. 
A su vez, estos sistemas dan retroalimentación al alojamiento del
repositorio de forma que, en caso positivo, se realice el siguiente
paso en el pipeline, por ejemplo desplegar a producción.

## Al final de esta sesión

El estudiante entenderá los criterios para elegir un sistema de
integración continua, y sabrá configurar uno para poder ejecutar los
tests automáticamente en el mismo.

## Criterio de aceptación

El repositorio tiene que estar corriendo los tests en Travis, y esos
tests deben pasar.

## Pasando los tests automáticamente

A un primer nivel, la integración continua consiste en integrar los
cambios hechos por un miembro del equipo en el momento que estén y
pasen los tests. Pero eso, efectivamente, significa que deben pasar
los tests y para nosotros, consiste en crear una configuración para
una máquina externa que ejecute esos tests y nos diga cuáles han
pasado o cuales no. Estas máquinas más adelante se combinan con las de
despliegue continuo, no permitiendo el mismo si algún test no ha
pasado.

En general, la integración continua se hace *en la nube*; lo que no
quiere decir que se haga siempre en un servicio *cloud* contratado,
sino porque se suele hacer en máquinas dedicadas específicamente para
ello; es más general, sin embargo que una máquina
virtual se descarga los ficheros, ejecuta los tests
y crea un informe, enviando también un correo al autor indicándole el
resultado. Por tanto, para que haga esto tenemos que indicar en la
configuración todo lo necesario para ejecutar los tests y,
posiblemente, nuestro programa: aplicaciones, librerías que necesita
nuestro programa, aparte de la configuración que tendrá el programa en
sí con las librerías del lenguaje de programación en el que está
desarrollado.

Un sistema bastante popular de integración continua es
[Jenkins](https://jenkins.io/), pero está enfocado sobre todo a
Java y no tiene una web gratuita que se pueda usar. Jenkins lo puedes usar en la nube o instalarte tu propio
ordenador para hacerlo. Sin embargo, está enfocado sobre todo a Java
por lo que hay otros sistemas como [Travis](https://travis-ci.org) o
[Shippable](https://www.shippable.com/) que podemos usar también desde
la nube y, además, están preparados para más lenguajes de
programación.

Para trabajar con estos sistemas, generalmente hay que hacerlo en dos
pasos

1. Darse de alta. Muchos están conectados con GitHub por lo que puedes
   usar directamente el usuario ahí. A través de un proceso de
   autorización, acceder al contenido e incluso informar del resultado
   de los tests.

2. Activar el repositorio en el que se vaya a aplicar la
   integración continua. Travis permite hacerlo directamente desde tu
   configuración; en otros se dan de alta desde la web de GitHub.

3. Crear un fichero de configuración para que se ejecute la
   integración y añadirlo al repositorio.


Los ficheros de configuración de las máquinas de integración continua
corresponden, aproximadamente, a una configuración de una máquina
virtual que hiciera solo y exclusivamente la ejecución de los
tests. Para ello se provisiona una máquina virtual (o contenedor), se
le carga el sistema operativo y se instala lo necesario, indicado en
el fichero de configuración tal como este para Travis.

~~~~~YAML
	language: node_js
	node_js:
	  - "0.10"
	  - "0.11"
	before_install:
	  - npm install -g mocha
	  - cd src; npm install .
	script: cd src; mocha
~~~~~

Este fichero, denominado `.travis.yml`, contiene lo siguiente:

- `language` indica qué lenguaje se va a usar. Travis tiene
  [varios lenguajes](https://docs.travis-ci.com/user/getting-started/),
  incluyendo por supuesto nodejs. Las máquinas virtuales no suelen
  estar configuradas para lenguajes arbitrarios, aunque por supuesto
  se puede poner un lenguaje tal como C y luego descargar lo necesario
  para otro lenguaje.

- `node_js` en este caso indica las versiones que vamos a probar. Por
  el mismo precio podemos probar varias versiones, en este caso las
  dos últimas de node.

- `before_install` se ejecuta antes de la instalación de la aplicación
  (específica de cada lenguaje; por ejemplo en el caso de node.js
  sería `npm install .`. En nuestro caso tenemos que instalar `mocha`
  y además ejecutar este último paso en un subdirectorio que no es
  estándar.

- Finalmente, se ejecuta el script de prueba en sí (para el caso,
  cualquier cosa que quieras ejecutar). Una vez más, nos cambiamos al
  subdirectorio y ejecutamos `mocha` tal como lo hemos hecho
  anteriormente.

El resultado
[aparecerá en la web](https://travis-ci.org/JJ/desarrollo-basado-pruebas)
y también se enviará por correo electrónico. Y te da también un
*badge* que puedes poner en tu fichero para indicar que, por lo
pronto, todo funciona.

Si el informe indica que las pruebas son correctas, se puede proceder al despliegue. Pero eso
ya será en la siguiente clase.

Esta configuración es esencial por varias razones: primero, porque nos
permite ser conscientes de todo lo necesario para desplegar nuestra
aplicación. Segundo, porque al crear tests integramos el paso de
control de calidad en el desarrollo. Y, finalmente, porque la
integración continua y los tests correspondientes son un paso esencial
para el despliegue continuo, que se verá más adelante.

## Acelerando usando contenedores Docker

## Actividad

