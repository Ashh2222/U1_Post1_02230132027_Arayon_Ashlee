# Laboratorio:Refactorización con SOLID


## Parte 1:Análisis del Código Legacy

### Paso 3: Identificación de Violaciones SOLID

Se analizó el codigo con el fin de identificar las violaciones a los principios SOLID que se cometieron en su realización y documentarlas. 

Al revisarlo, se pudo encontrar que el codigo contiene diversas violaciones en los siguientes principios de diseño:


#### 1. Single Responsibility Principle

Este principio se incumple ya que la clase "OrderManager" tiene muchas responsabilidades dentro de la misma. La clase ademas de crear pedido, tambien tiene la funcion de guardar en archivo,  enviar notificación, calcular impuesto y generar reporte.

Esta situación hace que existan varios motivos por las que se modificaria la clase,lo cual contradice el principio SRP "Una clase debe tener una unica razón para ser modificada"

#### 2. Open / Closed Principle

En este caso se incumple el principio OCP ya que en la linea 14 y la linea 15 del codigo se define la logica de descuentos directamente en el metodo mediante condiciones como:

 `if(total > 1000) total *= 0.9; //10% descuento` 
 
 `if(total > 5000) total *= 0.85; //15% descuento adicional`
 
Donde si se decidiera agregar otro tipo de descuento o modificar algo de los que ya estan puestos se tendria que cambiar el codigo de la clase "OrderManager" incumpliendo el principio Open/Closed "Abierto para extensión, cerrado para modificación".
 

#### 3. Interface Segregation Principle

Otra de las violaciones encontradas en el codigo es la del principio ISP, ya que todas las funciones estan implementadas en una sola clase. Si una clase externa a esta solo necesita usar una de esas funciones independientemente de la que sea, dependera de los metodos de los cuales no hacen uso.

Esta situacion va en contra del principio de interface segregation principle "Una clase no debe depender de metodos que no usa".
   

### 4. Dependency Inversion Principle 

En este caso se incumple el principio DIP ya que en el codigo, la clase OrderMananger depende directamente de la implementacion `java.io.FileWriter fw = new java.io.FileWriter("orders.txt", true);` cuando guarda la informacion en archivo.Entonces OrderManager  (modulo de alto nivel) esta dependiendo directamente de FileWriter (modulo de bajo nivel). 

Ademas de esto la clase también gestiona directamente el envío de notificaciones mediante `System.out.println`, lo que implica que la lógica de negocio depende de un mecanismo especifico de notificación.

Donde si en el futuro se deseara modificar donde se guarda o la forma en la que se envian los mensajes se tendria que modificar la clase OrderManager violando el principio de Dependency Inversion Principle "Los modulos de alto nivel no deben depender de modulos de bajo nivel".


## Parte 2: Refactorización - Separación de Responsabilidades

En esta parte del laboratorio se le aplicaron modificaciones al codigo original para emplear de manera correcta los principios SOLID y documentar el por que se realizo cada cambio.

### Paso 4: Crear la clase Order

Se creo en el package refactored una clase llamada Order para representar el pedido.

En esta clase se inicializaron los atributos id, customer, product, total y status por medio del constructor. A diferencia del codigo original, donde los pedidos se almacenaban como arreglo String[] dentro de un ArrayList, lo cual dificultaba la comprension del codigo. 

Este cambio se hizo para iniciar el cumplimiento del principio SRP, ya que la representación del pedido queda separada de la lógica de negocio, dandole unicamente la funcion de representar datos.


### Paso 5: Extraer la lógica de descuentos (Strategy Pattern + OCP)

Se creo la interfaz DiscountStrategy junto con sus implementaciones StandardDiscount y VIPDiscount. Esto permite agregar diferentes tipos de descuento sin modificar el codigo ya existente, cumpliendo el principio OCP.

Esto soluciono el problema que tenia el codigo original, donde la clase OrderManager debia ser modificada cada que se queria modificar la logica del descuento ya que esta estaba definida directamente en condiciones dentro del metodo. 

### Paso 6: Extraer persistencia (DIP)

Se creo la interfaz OrderRepository y su implementación FileOrderRepository para separar la lógica de negocio de la funcion de almacenamiento. Ahora el sistema depende de una abstracción y no directamente de FileWriter como en el codigo original, cumpliendo el principio DIP.

### Paso 7: Extraer notificaciones (DIP + ISP)
En el paso 7  creó la interfaz NotificationService y su implementación EmailNotificationService, evitando que la lógica principal dependa directamente de System.out. Esto mejora la asignacion una tarea para cada clase y refuerza el cumplimiento del principio DIP.

### Paso 8: Crear el OrderService refactorizado
En este paso la clase OrderService se creo para centralizar la creación de pedidos utilizando inyección de dependencias por constructor. De esta manera, el servicio no crea sus dependencias directamente, mejorando la mantenibilidad del sistema y este volviendose el "OrderManager" del codigo refactorizado.

### Paso 9: Aplicar Clean Code y Paso 10: Crear la clase Main de prueba

Por ultimo se verificó que el código mantuviera nombres descriptivos, metodos con una unica responsabilidad y una estructura clara, se creó la clase Main para probar el funcionamiento del sistema refactorizado, confirmando que la aplicación crea pedidos correctamente con distintas estrategias de descuento y con mejores resultados que el codigo original.















