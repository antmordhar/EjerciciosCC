**Ejercicio 1**
>Instalar alguno de los entornos virtuales de node.js (o de cualquier otro lenguaje con el que se esté familiarizado) y, con ellos, instalar la última versión existente, la versión minor más actual de la 4.x y lo mismo para la 0.11 o alguna impar (de desarrollo).

Instalar nvm y Nodejs
```
1. sudo apt install curl

2. curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash

3. nvm ls-remote

4. nvm install v12.13.0

5. nvm install v0.11.16

6. nvm use v12.13.0
```

**Ejercicio 2**
>Crear una descripción del módulo usando package.json. En caso de que se trate de otro lenguaje, usar el método correspondiente.

```
1. npm init
2. npm install sqlite --save-dev
3. npm install -g grunt-cli 
4. npm install grunt --save-dev
4. npm install docco grunt-docco --save-dev

```
Contenido del json
```
{
  "name": "test",
  "version": "1.0.0",
  "description": "json de prueba ",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+ssh://git@github.com/antmordhar/EjerciciosCC.git"
  },
  "author": "antmordhar",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/antmordhar/EjerciciosCC/issues"
  },
  "homepage": "https://github.com/antmordhar/EjerciciosCC#readme",
  "devDependencies": {
    "docco": "^0.8.0",
    "grunt": "^1.0.4",
    "grunt-docco": "^0.5.0",
    "sqlite": "^3.0.3"
  }
}
```
Documentar el codigo.
```
1. touch Gruntfile.js
2. nano Gruntfile.js
3. grunt docco
```
Contenido del GruntFile
```
'use strict';

module.exports = function(grunt) {

  // Configuración del proyecto
  grunt.initConfig({
  pkg: grunt.file.readJSON('package.json'),
  docco: {
	  debug: {
	  src: ['*.js'],
	  options: {
		  output: 'docs/'
	  }
	  }
  }
  });

  // Carga el plugin de grunt para hacer esto
  grunt.loadNpmTasks('grunt-docco');

  // Tarea por omisión: generar la documentación
  grunt.registerTask('default', ['docco']);
};
```
**Ejercicio 3**
>Descargar el repositorio de ejemplo anterior, instalar las herramientas necesarias (principalmente Scala y sbt) y ejecutar el ejemplo desde sbt. Alternativamente, buscar otros marcos para REST en Scala tales como Finatra o Scalatra y probar los ejemplos que se incluyan en el repositorio.

Instalar Scala y sbt
```
1. git clone git@github.com:JJ/spray-test.git

2. sudo wget www.scala-lang.org/files/archive/scala-2.11.8.deb

3. sudo dpkg -i scala-2.11.8.deb

4. echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list

5. sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2EE0EA64E40A89B84B2DF73499E82A75642AC823

6. sudo apt-get update

7. sudo apt-get install sbt
```
Para que se pueda usar sbt con el proyecto clonado hay que cambiar la linea sbt.version en build.propierties

```
sbt.version=0.13.17
```
Esto hara que el proyecto sea compatible con nuestra versión de java.

Ahora hacemos las pruebas con el proyecto

```
1. sbt 

2. test

3. re-start

```
Ahora en otra terminal

```
1. http://localhost:8080

2. curl -X PUT http://localhost:8080/0/0/Uno 

3. curl -X PUT http://localhost:8080/0/1/Otro

4. curl -X PUT http://localhost:8080/3/1/Aquel

5. curl http://localhost:8080/Aquel 
```

Ahora en la terminal anterior paramos la ejecucion

```
1. re-stop
```

**Ejercicio 4**

> Para la aplicación que se está haciendo, escribir una serie de aserciones y probar que efectivamente no fallan. Añadir tests para una nueva funcionalidad, probar que falla y escribir el código para que no lo haga. A continuación, ejecutarlos desde mocha (u otro módulo de test de alto nivel), usando descripciones del test y del grupo de test de forma correcta. Si hasta ahora no has subido el código que has venido realizando a GitHub, es el momento de hacerlo, porque lo vamos a necesitar un poco más adelante.

Vamos a crear un grupo de imposiciones para una clase en java con JUnit.

``` package Mesas;

import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertNotEquals;

import org.json.JSONArray;
import org.json.JSONObject;
import org.junit.jupiter.api.Test;
import org.skyscreamer.jsonassert.JSONAssert;
import org.skyscreamer.jsonassert.JSONCompareMode;

public class testmesa {
	private mesa mesatest;
	@Test
	public void testConstruction() {
		mesatest= new mesa();
		assertNotEquals("ID mesa Correcto",0,mesatest.getNummesa());
		
		int nummesatest=mesatest.getNummesa();
		JSONArray Jtest = new JSONArray();
		Jtest.put(nummesatest);
		JSONAssert.assertEquals(Jtest,mesatest.getPedido(),  JSONCompareMode.LENIENT);
	}
	@Test
	public void testGetNummesa() {
		mesatest= new mesa();
		assertNotEquals("ID mesa Correcto",0,mesatest.getNummesa());
	}
	@Test
	public void testGetPedido() {
		mesatest= new mesa();
		JSONArray Jtest = new JSONArray();
		Jtest.put(mesatest.getNummesa());
		JSONAssert.assertEquals(Jtest,mesatest.getPedido(),  JSONCompareMode.LENIENT);
	}
	@Test
	public void testaniadirPlato() {
		mesatest= new mesa();
		mesatest.aniadirPlato("PolloFrito", 5, 2);
		JSONArray Jtest = new JSONArray();
		Jtest.put(mesatest.getNummesa());
		JSONObject nuevoped = new JSONObject();
		nuevoped.put("plato", "PolloFrito");
		nuevoped.put("precio", 5);
		nuevoped.put("Cantidad", 2);
		Jtest.put(nuevoped);
		JSONAssert.assertEquals(Jtest,mesatest.getPedido(),  JSONCompareMode.LENIENT);
	}
	
}
```
Y esta es la clase que Testea el anterior JUnit

```
package Mesas;

import org.json.JSONArray;
import org.json.JSONObject;

public class mesa {

	private int  nummesa;
	JSONArray pedido;
	 
	public mesa() {
		this.nummesa=(int)(Math.random() * 50 + 1);;
		pedido = new JSONArray();
		pedido.put(new Integer(this.nummesa));
	 }
	public int getNummesa() {
		return nummesa;
	}
	
	public JSONArray getPedido(){
		return this.pedido;
	}
	
	public void aniadirPlato(String nombre,float precio,int cantidad) {
		JSONObject nuevoped = new JSONObject();
		nuevoped.put("plato", nombre);
		nuevoped.put("precio", precio);
		nuevoped.put("Cantidad", cantidad);
		pedido.put(nuevoped);
		
	}
	
}

```
**Ejercicio 5**
>Añadir integracion continual proyecto de Cloud Computing.

Realizaremos esta tarea con travis. Entramos a travis y autorizamos la union con git hub. Despues activamos travis en nuetro proyecto.

Por ultimo creamos la configuración en travis.yml para este:
```
language: java
sudo: false
jdk:
  - oraclejdk8
  - oraclejdk9
os:
  - linux
```