# Ejercicio 1

> Realizar una aplicación básica que use express para devolver alguna estructura de datos del modelo que se viene usando en el curso.

En nuestro caso se ha usado Java con el framework Spring Boot.

El codigo de nuestra aplicación es:

~~~ Java
@RestController
public class mesaController {
    private int idcount=0;
    private List<mesa> mesas=new ArrayList<mesa>();

    @GetMapping(value= "/verpedido/{id}")
    public String getPedido(@PathVariable(value= "id") int id) {
        return mesas.get(id).getPedido().toString();
    }
}
~~~

Esto nos devolvera un String en el que se encuentra el JsonArray procediente de esta clase:

~~~ Java
public class mesa {

	private final int  nummesa;
	JSONArray pedido;
	 
	public mesa(int id) {
		this.nummesa=id;
		pedido = new JSONArray();
		pedido.put(new Integer(this.nummesa));
	 }
	public int getNummesa() {
		return nummesa;
	}
	
	public JSONArray getPedido(){
		return this.pedido;
	}
	
	public void aniadirPlato(String nombre,double precio,int cantidad) throws JSONException {
		JSONObject nuevoped = new JSONObject();
		nuevoped.put("Plato", nombre);
		nuevoped.put("Precio", precio);
		nuevoped.put("Cantidad", cantidad);
		pedido.put(nuevoped);
		
	}

	public void limpiarPlatos(){
		pedido = new JSONArray();
		pedido.put(new Integer(this.nummesa));
	}
	
}
~~~

# Ejercicio 2

> Programar un microservicio en express (o el lenguaje y marco elegido) que incluya variables como en el caso anterior.

Completaremos la clase MesaController

~~~ Java
>@RestController
public class mesaController {
    private int idcount=0;
    private List<mesa> mesas=new ArrayList<mesa>();

    @GetMapping(value= "/verpedido/{id}")
    public String getPedido(@PathVariable(value= "id") int id) {
        return mesas.get(id).getPedido().toString();
    }
    @PostMapping(value= "/hacerpedido")
    public ResponseEntity<String> postPedido(@RequestBody plato pedido) throws JSONException { 
        mesas.get(pedido.getIDmesa()).aniadirPlato(pedido.getNombre(),pedido.getPrecio(),pedido.getCantidad());
        return ResponseEntity.status(HttpStatus.CREATED).build();
    }
    @PostMapping(value= "/crearmesa")
    public ResponseEntity<String> crearMesa() throws JSONException { 
        mesa mesa = new mesa(idcount);
        mesas.add(mesa);
        idcount++;
        return ResponseEntity.status(HttpStatus.CREATED).build();
    }
    @DeleteMapping(value= "/borrapedido/{id}")
    public void deletePedido(@PathVariable(value= "id") int id){
        mesas.get(id).limpiarPlatos();
    }
    public int getIdCount(){
        return idcount;
    }
}
~~~

Adicionalmente también sera necesaria la clase plato que servira como @RequestedBody de la funcion hacer pedido. 

~~~Java
public class plato{

    private int idmesa;
    private String nombre;
    private double precio;
    private int cantidad;

    public plato(int idmesa,String nombre,double precio,int cantidad){
        this.idmesa=idmesa;
        this.nombre=nombre;
        this.precio=precio;
        this.cantidad=cantidad;
    }

    public plato(){}

    public int getIDmesa(){
        return idmesa;
    }
    public String getNombre(){
        return this.nombre;
    }
    public double getPrecio(){
        return this.precio;
    }
    public int getCantidad(){
        return this.cantidad;
    }

    public void setIDmesa(int idmesa){
        this.idmesa=idmesa;
    }
    public void setNombre(String nombre){
        this.nombre=nombre;
    }
    public void setPrecio(double precio){
        this.precio=precio;
    }
    public void setCantidad(int cantidad){
        this.cantidad=cantidad;
    }
}
~~~

Con esto hemos implementado todas las funciones de la clase Mesa para nuestro microservicio REST. Como se puede ver tenemos como opciones para comunicarnos con este microservicio GET, POST y DELETE.

# Ejercicio 3 

> Crear pruebas para las diferentes rutas de la aplicación.

Los test creados para nuestro microservicio son los siguientes:

~~~ Java
package Mesas;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.test.context.SpringBootTest.WebEnvironment;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;

import static org.junit.Assert.*;

import org.json.JSONException;

import static org.hamcrest.CoreMatchers.*;

import Mesas.mesaController;
import Mesas.plato;

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
public class mesaControllerTest {
    
    @LocalServerPort
    private int port;
    
    private mesaController controllerTest;

    @Autowired
    private TestRestTemplate restTemplate;

    @Before
    public void setup() {
        controllerTest = new mesaController();
    }

    @Test
    public void testCrearMesa() throws JSONException {
        controllerTest.crearMesa();
        assertThat("IdCount cambiado",1,is(controllerTest.getIdCount()));
        assertThat("Rellenado Correctamente","[0]",is(controllerTest.getPedido(0)));
    }
    @Test
    public void testpostPedido() throws JSONException {
        controllerTest.crearMesa();
        plato platoAux = new plato(0,"PolloFrito", 1.2, 1);
        controllerTest.postPedido(platoAux);
        assertThat("Json rellenado correctamente","[0,{\"Cantidad\":1,\"Plato\":\"PolloFrito\",\"Precio\":1.2}]",is(controllerTest.getPedido(0)));
    }
    @Test
    public void testGetPedido() throws JSONException {
        controllerTest.crearMesa();
        plato platoAux = new plato(0,"PolloFrito", 1.2, 1);
        controllerTest.postPedido(platoAux);
        assertThat("Se devuelve el Json esperado","[0,{\"Cantidad\":1,\"Plato\":\"PolloFrito\",\"Precio\":1.2}]",is(controllerTest.getPedido(0)));
    }
    @Test 
    public void testDeletePedido() throws JSONException {
        controllerTest.crearMesa();
        plato platoAux = new plato(0,"PolloFrito", 1.2, 1);
        controllerTest.postPedido(platoAux);
        controllerTest.deletePedido(0);
        assertThat("Borrado Correctamente","[0]",is(controllerTest.getPedido(0)));
    }
    @Test
    public void testGetIdCount() throws JSONException {
        controllerTest.crearMesa();
        assertThat("Numero de Mesas correcto",1,is(controllerTest.getIdCount()));
    }
    @Test
    public void testConetions() throws Exception {

        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_JSON);

        HttpEntity<String> request = 
        new HttpEntity<String>("", headers);

        this.restTemplate.postForObject("http://localhost:" + port + "/crearmesa",request,
                String.class);

        assertThat("Crea la mesa por HTTP",this.restTemplate.getForObject("http://localhost:" + port + "/verpedido/0",String.class)
                    ,is("[0]"));

        request = 
                new HttpEntity<String>("{\"idmesa\":0,\"nombre\":\"plato0\", \"precio\":1.4, \"cantidad\":1}", headers);
        
        this.restTemplate.postForObject("http://localhost:" + port + "/hacerpedido",request,
            String.class);

        assertThat("Inserta el plato por HTTP",this.restTemplate.getForObject("http://localhost:" + port + "/verpedido/0",String.class)
        ,is("[0,{\"Cantidad\":1,\"Plato\":\"plato0\",\"Precio\":1.4}]"));
        
        this.restTemplate.delete("http://localhost:" + port + "/borrapedido/0");

        assertThat("Borra el pedido por HTTP",this.restTemplate.getForObject("http://localhost:" + port + "/verpedido/0",String.class)
        ,is("[0]"));
    }
}
~~~

## Faltan por hacer ejecicios 4 y 5