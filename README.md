![image](1.png)
![image](2.png)
![image](erd.png)

DDL

Creacion de la base de datos:

```mysql

mysql> CREATE DATABASE normalizacion;
Query OK, 1 row affected (0.04 sec)

mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| normalizacion      |
| performance_schema |
| sys                |
| work               |
+--------------------+
6 rows in set (0.00 sec)
```

Creacion de las tablas:

```mysql

CREATE TABLE pais(
    id_pais INT(11) NOT NULL AUTO_INCREMENT,
    PRIMARY KEY(id_pais),
    nombre VARCHAR(100) NOT NULL
    );

CREATE TABLE region(
    id_region INT(11) NOT NULL AUTO_INCREMENT,
    PRIMARY KEY(id_region),
    nombre VARCHAR(100) NOT NULL,
    id_pais INT(11) NOT NULL,
    CONSTRAINT FK_region_pais FOREIGN KEY (id_pais) REFER
ENCES pais(id_pais));

CREATE TABLE ciudad(
    id_ciudad INT(11) NOT NULL AUTO_INCREMENT,
    PRIMARY KEY(id_ciudad),
    nombre VARCHAR(100) NOT NULL,
    id_region INT(11) NOT NULL,
    CONSTRAINT FK_ciudad_region FOREIGN KEY (id_region) REFERENCES region(id_region)
);

CREATE TABLE direccion(
    id_direccion INT(11) NOT NULL AUTO_INCREMENT,
    linea_direccion1 VARCHAR(50) NOT NULL,
    linea_direccion2 VARCHAR(50),
    barrio VARCHAR(100) NOT NULL,
    codigo_postal VARCHAR(10),
    id_ciudad INT(11) NOT NULL,
    PRIMARY KEY(id_direccion),
    CONSTRAINT FK_direccion_ciudad FOREIGN KEY(id_ciudad) REFERENCES ciudad(id_ciudad)
);

CREATE TABLE proveedor (
    id_proveedor INT(11) NOT NULL,
    PRIMARY KEY (id_proveedor),
    telefono VARCHAR(20) NOT NULL,
    nombre VARCHAR(100) NOT NULL
);

CREATE TABLE proveedor_direccion (
    id_proveedor INT(11) NOT NULL,
    id_direccion INT(11) NOT NULL,
    PRIMARY KEY (id_proveedor, id_direccion),
    FOREIGN KEY (id_proveedor) REFERENCES proveedor(id_proveedor),
    FOREIGN KEY (id_direccion) REFERENCES direccion(id_direccion)
);

CREATE TABLE oficina (
    id_oficina INT(11) NOT NULL AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL,
    telefono VARCHAR(20),
    PRIMARY KEY (id_oficina)
);

CREATE TABLE oficina_direccion (
    id_oficina INT(11) NOT NULL,
    id_direccion INT(11) NOT NULL,
    PRIMARY KEY (id_oficina, id_direccion),
    FOREIGN KEY (id_oficina) REFERENCES oficina(id_oficina),
    FOREIGN KEY (id_direccion) REFERENCES direccion(id_direccion)
);

CREATE TABLE gama_producto(
    gama VARCHAR(50),
    PRIMARY KEY (gama),
    descripcion_texto TEXT,
    descripcion_html TEXT,
    imagen VARCHAR(256));

CREATE TABLE dimensiones(
    id_dimensiones INT(11) NOT NULL AUTO_INCREMENT,
    PRIMARY KEY (id_dimensiones),
    alto DECIMAL(5),
    ancho DECIMAL(5),
    profundidad INT(5),
    diametro DECIMAL(5),
    unidad_de_medida VARCHAR(10));

CREATE TABLE producto (
    id_producto INT(11) NOT NULL AUTO_INCREMENT,
    PRIMARY KEY (id_producto),
    nombre VARCHAR(100) NOT NULL,
    descripcion TEXT,
    precio_proveedor DECIMAL (12,5),
    precio_venta DECIMAL(12,5) NOT NULL,
    id_dimensiones INT(11),
    FOREIGN KEY (id_dimensiones) REFERENCES dimensiones(id_dimensiones),
    id_proveedor INT(11),
    FOREIGN KEY (id_proveedor) REFERENCES proveedor(id_proveedor),
    gama VARCHAR(50) NOT NULL,
    FOREIGN KEY (gama) REFERENCES gama_producto(gama)
);

CREATE TABLE inventario (
    id_inventario INT(11) NOT NULL AUTO_INCREMENT,
    id_producto INT(11) NOT NULL,
    cantidad_en_stock SMALLINT(6) NOT NULL,
    ubicacion VARCHAR(100), 
    PRIMARY KEY (id_inventario),
    FOREIGN KEY (id_producto) REFERENCES producto(id_producto)
);

CREATE TABLE empleado (
    id_empleado INT(11) NOT NULL,
    PRIMARY KEY (id_empleado),
    nombre VARCHAR(50) NOT NULL,
    apellido1 VARCHAR(50) NOT NULL,
    apellido2 VARCHAR(50) NOT NULL,
    extension VARCHAR(10) NOT NULL,
    email VARCHAR(100) NOT NULL,
    id_oficina INT(11) NOT NULL,
    CONSTRAINT FK_empleado_oficina FOREIGN KEY (id_oficina) REFERENCES 	 
    oficina(id_oficina),
    id_jefe INT(11),
    CONSTRAINT FK_empleado_jefe FOREIGN KEY (id_jefe) REFERENCES 
    empleado(id_empleado),
    puesto VARCHAR(50)
);

CREATE TABLE cliente (
    id_cliente INT(11) NOT NULL,
    nombre_cliente VARCHAR(50) NOT NULL,
    telefono VARCHAR(20),
    id_empleado_rep_ventas INT(11),
    limite_credito DECIMAL(15),
    PRIMARY KEY (id_cliente),
    CONSTRAINT FK_cliente_empleado_rep_ventas FOREIGN KEY 
    (id_empleado_rep_ventas) REFERENCES empleado(id_empleado)
);

CREATE TABLE cliente_direccion (
    id_cliente INT(11) NOT NULL,
    id_direccion INT(11) NOT NULL,
    PRIMARY KEY (id_cliente, id_direccion),
    FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente),
    FOREIGN KEY (id_direccion) REFERENCES direccion(id_direccion)
);

CREATE TABLE cliente_direccion (
    id_cliente INT(11) NOT NULL,
    id_direccion INT(11) NOT NULL,
    PRIMARY KEY (id_cliente, id_direccion),
    FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente),
    FOREIGN KEY (id_direccion) REFERENCES direccion(id_direccion)
);

CREATE TABLE contacto (
    id_contacto INT(11) NOT NULL AUTO_INCREMENT,
    nombre_contacto VARCHAR(30) NOT NULL,
    apellido_contacto VARCHAR(30) NOT NULL,
    id_cliente INT(11) NOT NULL,
    PRIMARY KEY (id_contacto),
    FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente)
);

CREATE TABLE pedido (
    id_pedido INT(11) NOT NULL AUTO_INCREMENT,
    PRIMARY KEY(id_pedido),
    fecha_pedido DATE NOT NULL,
    fecha_esperada DATE NOT NULL,
    fecha_entrega DATE,
    estado VARCHAR(15) NOT NULL,
    comentarios TEXT,
    id_cliente INT(11) NOT NULL,
    CONSTRAINT FK_pedido_cliente FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente)
);

CREATE TABLE detalle_pedido (
    id_pedido INT(11) NOT NULL,
    id_producto INT(11) NOT NULL,
    PRIMARY KEY (id_pedido, id_producto),
    cantidad INT(11),
    precio_unidad DECIMAL(15,2),
    numero_linea SMALLINT(6),
    FOREIGN KEY (id_pedido) REFERENCES pedido(id_pedido),
    FOREIGN KEY (id_producto) REFERENCES producto(id_producto)
);

CREATE TABLE pago(
id_transaccion VARCHAR(50) NOT NULL,
PRIMARY KEY (id_transaccion),
id_cliente INT(11) NOT NULL,
forma_pago VARCHAR(40) NOT NULL,
fecha_pago DATE NOT NULL,
total DECIMAL(15) NOT NULL,
CONSTRAINT FK_pago_cliente FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente)
);


```





**DML**

**INSERT**

```mysql

-- Países

INSERT INTO pais (nombre) 
VALUES 
    ('Estados Unidos'),
    ('Canadá'),
    ('México'),
    ('Brasil'),
    ('Argentina'),
    ('España'),
    ('Francia');


-- Regiones

INSERT INTO region (nombre, id_pais)
VALUES 
    ('Nueva Inglaterra', 1),
    ('Quebec', 2),            
    ('Distrito Federal', 3),  
    ('São Paulo', 4),         
    ('Buenos Aires', 5),      
    ('Comunidad de Madrid', 6),
    ('Île-de-France', 7);     

-- Ciudades

INSERT INTO ciudad (nombre, id_region)
VALUES 
    ('Boston', 1),              
    ('Montreal', 2),            
    ('Ciudad de México', 3),    
    ('São Paulo', 4),           
    ('Buenos Aires', 5),        
    ('Madrid', 6),              
    ('París', 7); 


-- Oficinas

INSERT INTO oficina (nombre, telefono)
VALUES 
    ('Oficina Central', '123-456-7890'),
    ('Sucursal Norte', '987-654-3210'),
    ('Sucursal Sur', '234-567-8901'),
    ('Sucursal Este', '345-678-9012'),
    ('Sucursal Oeste', '456-789-0123'),
    ('Oficina Principal', '567-890-1234'),
    ('Oficina Regional', '678-901-2345');


-- oficinas direccion

INSERT INTO oficina_direccion (id_oficina, id_direccion)
VALUES 
    (1, 1), -- Oficina Central - Boston, Estados Unidos
    (2, 2), -- Sucursal Norte - Montreal, Canadá
    (3, 3), -- Sucursal Sur - Ciudad de México, México
    (4, 4), -- Sucursal Este - São Paulo, Brasil
    (5, 5), -- Sucursal Oeste - Buenos Aires, Argentina
    (6, 6), -- Oficina Principal - Madrid, España
    (7, 7); -- Oficina Regional - París, Francia


-- gamas

INSERT INTO gama_producto (gama, descripcion_texto, descripcion_html, imagen)
VALUES 
    ('Gama Alta', 'Productos de alta calidad con características premium.', '<p>Productos de alta calidad con características premium.</p>', 'imagen_gama_alta.jpg'),
    ('Gama Media', 'Productos de calidad aceptable a precios accesibles.', '<p>Productos de calidad aceptable a precios accesibles.</p>', 'imagen_gama_media.jpg'),
    ('Gama Baja', 'Productos económicos para presupuestos ajustados.', '<p>Productos económicos para presupuestos ajustados.</p>', 'imagen_gama_baja.jpg');


-- Empleados

-- Clientes

-- Proveedores

-- Sucursales

-- Tipo de Teléfono

-- Inserts de teléfonos para proveedores

-- Inserts de teléfonos para oficinas

-- Inserts de teléfonos para clientes

-- Tipos de Pago

-- Pagos

INSERT INTO pago (id_transaccion, id_cliente, forma_pago, fecha_pago, total)
VALUES
    ('T001', 122334455, 'Tarjeta de crédito', '2023-05-10', 500.00),
    ('T002', 223344566, 'Transferencia bancaria', '2023-05-12', 750.00),
    ('T003', 334455677, 'Efectivo', '2023-05-15', 1000.00),
    ('T004', 445567788, 'Tarjeta de débito', '2023-05-18', 800.00),
    ('T005', 556678899, 'Cheque', '2023-05-20', 600.00);

-- Contactos

-- Estados de Pedido

-- Gamas de Producto

-- Dimensiones

-- Productos

-- Inventario

-- Pedidos


-- Detalles de Pedido

INSERT INTO detalle_pedido (id_pedido, id_producto, cantidad, precio_unidad, numero_linea)
VALUES 
    (1, 1, 2, 800.00, 1),  -- Detalle del pedido 1 (Carlos Pérez) - Producto 1 (Laptop HP)
    (2, 2, 3, 500.00, 1),  -- Detalle del pedido 2 (Ana García) - Producto 2 (Teléfono Samsung)
    (3, 3, 1, 250.00, 1),  -- Detalle del pedido 3 (Pedro Martínez) - Producto 3 (Tablet Lenovo)
    (4, 4, 2, 1200.00, 1), -- Detalle del pedido 4 (María Rodríguez) - Producto 4 (TV LG)
    (5, 5, 1, 1000.00, 1), -- Detalle del pedido 5 (Laura López) - Producto 5 (Refrigerador Whirlpool)
    (6, 6, 3, 50.00, 1),   -- Detalle del pedido 6 (Juan Hernández) - Producto 6 (Licuadora Oster)
    (7, 1, 2, 800.00, 1), -- Detalle del pedido 7 (Sofía Díaz) - Producto 7 (TV LG)
    (8, 1, 1, 800.00, 1);  -- Detalle del pedido 8 (Miguel Torres) - Producto 1 (Laptop HP)
```

**Consultas sobre una tabla**

1. Devuelve un listado con el código de oficina y la ciudad donde hay oficinas.

   ```mysql
   
    SELECT o.id_oficina, c.nombre AS ciudad FROM oficina
    AS o INNER JOIN oficina_direccion AS od ON o.id_oficina = 
    od.id_oficina INNER JOIN direccion AS d ON od.id_direccion = 
    d.id_direccion INNER JOIN ciudad AS c ON d.id_ciudad = c.id_ciudad;
	+------------+-------------------+
	| id_oficina | ciudad            |
	+------------+-------------------+
	|          1 | Boston            |
	|          2 | Montreal          |
	|          3 | Ciudad de México  |
	|          4 | São Paulo         |
	|          5 | Buenos Aires      |
	|          6 | Madrid            |
	|          7 | París             |
	+------------+-------------------+
	7 rows in set (0.01 sec)
   ```

   

2. Devuelve un listado con la ciudad y el teléfono de las oficinas de España.

   ```mysql

	SELECT c.nombre AS ciudad, o.telefono
	FROM oficina AS o
	INNER JOIN oficina_direccion AS od ON o.id_oficina = od.id_oficina
	INNER JOIN direccion AS d ON od.id_direccion = d.id_direccion
	INNER JOIN ciudad AS c ON d.id_ciudad = c.id_ciudad
	INNER JOIN region AS r ON c.id_region = r.id_region
	INNER JOIN pais AS p ON r.id_pais = p.id_pais
	WHERE p.nombre = 'España';

	+--------+--------------+
	| ciudad | telefono     |
	+--------+--------------+
	| Madrid | 567-890-1234 |
	+--------+--------------+
	1 row in set (0.00 sec)
   
   ```

   
3. Devuelve un listado con el nombre, apellidos y email de los empleados cuyo jefe tiene un código de jefe igual a 7.


   ```mysql

   SELECT e.nombre, e.apellido1, e.apellido2, e.email
   FROM empleado AS e
   INNER JOIN empleado AS j ON e.id_jefe = j.id_empleado
   WHERE j.id_empleado = 7;

   Empty set (0.00 sec)
   ```

   
4. Devuelve el nombre del puesto, nombre, apellidos y email del jefe de la
   empresa.

   

   ```mysql

   SELECT e.nombre, e.apellido1, e.apellido2, e.puesto
   FROM empleado AS e WHERE e.puesto = 'Gerente';
	+--------+-----------+-----------+---------+
	| nombre | apellido1 | apellido2 | puesto  |
	+--------+-----------+-----------+---------+
	| Juan   | González  | López     | Gerente |
	+--------+-----------+-----------+---------+
	1 row in set (0.00 sec)

   ```

   

5. Devuelve un listado con el nombre, apellidos y puesto de aquellos
   empleados que no sean representantes de ventas.

   ```mysql

	SELECT e.nombre, e.apellido1, e.apellido2, e.puesto
	FROM empleado AS e
	WHERE e.puesto != 'Representante de Ventas';
	+--------+-----------+------------+-----------------------+
	| nombre | apellido1 | apellido2  | puesto                |
	+--------+-----------+------------+-----------------------+
	| Juan   | González  | López      | Gerente               |
	| María  | Martínez  | García     | Asistente de Gerencia |
	| Carlos | Pérez     | Fernández  | Analista de Ventas    |
	| Laura  | Díaz      | Rodríguez  | Contador              |
	| Ana    | Sánchez   | López      | Recepcionista         |
	+--------+-----------+------------+-----------------------+
	5 rows in set (0.00 sec)
   ```

   

6. Devuelve un listado con el nombre de los todos los clientes españoles.

   ```mysql
   
   SELECT DISTINCT c.nombre_cliente FROM cliente AS c INNER JOIN 
   cliente_direccion AS cd ON c.id_cliente = cd.id_cliente INNER JOIN 
   direccion AS d ON cd.id_direccion = d.id_direccion INNER JOIN ciudad AS 
   ci ON d.id_ciudad = ci.id_ciudad INNER JOIN region AS r ON ci.id_region 
   = r.id_region INNER
   JOIN pais AS p ON r.id_pais = p.id_pais WHERE p.nombre = 'España';

	+-----------------+
	| nombre_cliente  |
	+-----------------+
	| Juan Hernández  |
	+-----------------+
	1 row in set (0.02 sec)

   ```

   

7. Devuelve un listado con los distintos estados por los que puede pasar un
   pedido.

   ```mysql
   
   SELECT DISTINCT estado
   FROM pedido;
	+------------+
	| estado     |
	+------------+
	| En Proceso |
	| Entregado  |
	| Rechazado  |
	+------------+
	3 rows in set (0.00 sec)
   ```

   

8. Devuelve un listado con el código de cliente de aquellos clientes que
   realizaron algún pago en 2008. Tenga en cuenta que deberá eliminar
   aquellos códigos de cliente que aparezcan repetidos. Resuelva la consulta:
   • Utilizando la función YEAR de MySQL.
   • Utilizando la función DATE_FORMAT de MySQL.
   • Sin utilizar ninguna de las funciones anteriores.

   ```mysql
   
   - SELECT DISTINCT id_cliente
     FROM pago
     WHERE YEAR(fecha_pago) = 2008;
     Empty set (0.00 sec)

   - SELECT DISTINCT id_cliente
     FROM pago
     WHERE DATE_FORMAT(fecha_pago, '%Y') = '2008';
     Empty set (0.00 sec)

   - SELECT DISTINCT id_cliente
     FROM pago
     WHERE fecha_pago >= '2008-01-01' AND fecha_pago < '2009-01-01';
     Empty set (0.00 sec)
   
   ```

   

9. Devuelve un listado con el código de pedido, código de cliente, fecha
   esperada y fecha de entrega de los pedidos que no han sido entregados a
   tiempo.

   ```mysql
   
   SELECT id_pedido, id_cliente, fecha_esperada, fecha_entrega
   FROM pedido
   WHERE fecha_entrega > fecha_esperada;
   Empty set (0.00 sec)
   ```

   

10. Devuelve un listado con el código de pedido, código de cliente, fecha
    esperada y fecha de entrega de los pedidos cuya fecha de entrega ha sido al menos dos días antes de la fecha esperada.
    • Utilizando la función ADDDATE de MySQL.
    • Utilizando la función DATEDIFF de MySQL.
    • ¿Sería posible resolver esta consulta utilizando el operador de suma 
    + o resta -?

    

    ```mysql

    - SELECT id_pedido, id_cliente, fecha_esperada, fecha_entrega
      FROM pedido
      WHERE fecha_entrega < ADDDATE(fecha_esperada, -2);
      Empty set (0.01 sec)

    - SELECT id_pedido, id_cliente, fecha_esperada, fecha_entrega
      FROM pedido
      WHERE DATEDIFF(fecha_esperada, fecha_entrega) >= 2;
      Empty set (0.01 sec)

    - SELECT id_pedido, id_cliente, fecha_esperada, fecha_entrega
      FROM pedido
      WHERE fecha_entrega < fecha_esperada - INTERVAL 2 DAY;
      Empty set (0.01 sec)
    
    ```

    

11. Devuelve un listado de todos los pedidos que fueron rechazados en 2009.

    ```mysql
    
    SELECT id_pedido, fecha_pedido, fecha_esperada, fecha_entrega, estado
    FROM pedido
    WHERE estado = 'rechazado' AND YEAR(fecha_pedido) = 2009;
    Empty set (0.00 sec)
    ```

    

12. Devuelve un listado de todos los pedidos que han sido entregados en el
    mes de enero de cualquier año.

    ```mysql
    
      SELECT id_pedido, fecha_pedido, fecha_esperada, fecha_entrega
      FROM pedido
      WHERE MONTH(fecha_entrega) = 1;
      Empty set (0.00 sec)
    ```

    

13. Devuelve un listado con todos los pagos que se realizaron en el
    año 2008 mediante Paypal. Ordene el resultado de mayor a menor.

    ```mysql

    SELECT *
    FROM pago
    WHERE YEAR(fecha_pago) = 2008 AND forma_pago = 'Paypal'
    ORDER BY total DESC;
    Empty set (0.00 sec)

    ```

    

14. Devuelve un listado con todas las formas de pago que aparecen en la
    tabla pago. Tenga en cuenta que no deben aparecer formas de pago
    repetidas.

    ```mysql
    SELECT DISTINCT forma_pago
    FROM pago;
	+------------------------+
	| forma_pago             |
	+------------------------+
	| Tarjeta de crédito     |
	| Transferencia bancaria |
	| Efectivo               |
	| Tarjeta de débito      |
	| Cheque                 |
	+------------------------+
	5 rows in set (0.00 sec)
    ```

    

15. Devuelve un listado con todos los productos que pertenecen a la
    gama Ornamentales y que tienen más de 100 unidades en stock. El listado
    deberá estar ordenado por su precio de venta, mostrando en primer lugar
    los de mayor precio.

    ```mysql

    SELECT * FROM producto WHERE gama = (SELECT gama FROM gama_producto 
    WHERE gama = 'Ornamentales')   AND id_producto IN (SELECT id_producto 
    FROM inventario WHERE cantidad_en_stock > 100) ORDER BY precio_venta 
    DESC;
    Empty set (0.01 sec)

    ```

    

16. Devuelve un listado con todos los clientes que sean de la ciudad de Madrid y cuyo representante de ventas tenga el código de empleado 11 o 30.

    ```mysql

    SELECT *
    FROM cliente
    WHERE id_cliente IN (
    SELECT id_cliente
    FROM cliente_direccion
    WHERE id_direccion IN (SELECT id_direccion FROM direccion WHERE 
    id_ciudad IN (SELECT id_ciudad FROM ciudad WHERE nombre = 'Madrid'))
    ) AND id_empleado_rep_ventas IN (11, 30);

    Empty set (0.01 sec)
    ```

    

**Consultas multitabla (Composición interna)**
**Resuelva todas las consultas utilizando la sintaxis de SQL1 y SQL2. Las consultas con**
**sintaxis de SQL2 se deben resolver con INNER JOIN y NATURAL JOIN.**

1. Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.
   
   ```mysql

  	SELECT cliente.nombre_cliente, empleado.nombre, empleado.apellido
        FROM cliente, empleado
        WHERE cliente.id_empleado_rep_ventas = empleado.id_empleado;

	+-------------------+--------+-----------+
	| nombre_cliente    | nombre | apellido1 |
	+-------------------+--------+-----------+
	| Carlos Pérez      | Juan   | González  |
	| Ana García        | María  | Martínez  |
	| Pedro Martínez    | Carlos | Pérez     |
	| María Rodríguez   | Laura  | Díaz      |
	| Laura López       | Juan   | González  |
	| Juan Hernández    | María  | Martínez  |
	| Sofía Díaz        | Carlos | Pérez     |
	| Miguel Torres     | Laura  | Díaz      |
	+-------------------+--------+-----------+
	8 rows in set (0.00 sec)

   	SELECT cliente.nombre_cliente, empleado.nombre, empleado.apellido
	FROM cliente
	INNER JOIN empleado ON cliente.id_empleado_rep_ventas = 
        empleado.id_empleado;

 	+-------------------+--------+-----------+
	| nombre_cliente    | nombre | apellido1 |
	+-------------------+--------+-----------+
	| Carlos Pérez      | Juan   | González  |
	| Ana García        | María  | Martínez  |
	| Pedro Martínez    | Carlos | Pérez     |
	| María Rodríguez   | Laura  | Díaz      |
	| Laura López       | Juan   | González  |
	| Juan Hernández    | María  | Martínez  |
	| Sofía Díaz        | Carlos | Pérez     |
	| Miguel Torres     | Laura  | Díaz      |
	+-------------------+--------+-----------+
	8 rows in set (0.00 sec)

   ```

   

2. Muestra el nombre de los clientes que hayan realizado pagos junto con el
   nombre de sus representantes de ventas.

   ```mysql

	SELECT cliente.nombre_cliente, empleado.nombre, empleado.apellido
	FROM cliente, empleado, pago
	WHERE cliente.id_empleado_rep_ventas = empleado.id_empleado
	AND cliente.id_cliente = pago.id_cliente;

	+-------------------+--------+-----------+
	| nombre_cliente    | nombre | apellido1 |
	+-------------------+--------+-----------+
	| Carlos Pérez      | Juan   | González  |
	| Ana García        | María  | Martínez  |
	| Pedro Martínez    | Carlos | Pérez     |
	| María Rodríguez   | Laura  | Díaz      |
	| Laura López       | Juan   | González  |
	+-------------------+--------+-----------+
	5 rows in set (0.01 sec)


	SELECT cliente.nombre_cliente, empleado.nombre, empleado.apellido
	FROM cliente
	INNER JOIN empleado ON cliente.id_empleado_rep_ventas = 		empleado.id_empleado
	INNER JOIN pago ON cliente.id_cliente = pago.id_cliente;

   	+-------------------+--------+-----------+
	| nombre_cliente    | nombre | apellido1 |
	+-------------------+--------+-----------+
	| Carlos Pérez      | Juan   | González  |
	| Ana García        | María  | Martínez  |
	| Pedro Martínez    | Carlos | Pérez     |
	| María Rodríguez   | Laura  | Díaz      |
	| Laura López       | Juan   | González  |
	+-------------------+--------+-----------+
	5 rows in set (0.00 sec)
   ```

   

3. Muestra el nombre de los clientes que no hayan realizado pagos junto con
   el nombre de sus representantes de ventas.

   ```mysql

   SELECT cliente.nombre_cliente, empleado.nombre, empleado.apellido1
   FROM cliente, empleado
   WHERE cliente.id_empleado_rep_ventas = empleado.id_empleado
   AND cliente.id_cliente NOT IN (SELECT id_cliente FROM pago);

        +-----------------+--------+-----------+
	| nombre_cliente  | nombre | apellido1 |
	+-----------------+--------+-----------+
	| Juan Hernández  | María  | Martínez  |
	| Sofía Díaz      | Carlos | Pérez     |
	| Miguel Torres   | Laura  | Díaz      |
	+-----------------+--------+-----------+
	3 rows in set (0.00 sec)

   SELECT cliente.nombre_cliente, empleado.nombre, empleado.apellido1
   FROM cliente
   INNER JOIN empleado ON cliente.id_empleado_rep_ventas = 
   empleado.id_empleado
   LEFT JOIN pago ON cliente.id_cliente = pago.id_cliente
   WHERE pago.id_transaccion IS NULL;

	+-----------------+--------+-----------+
	| nombre_cliente  | nombre | apellido1 |
	+-----------------+--------+-----------+
	| Juan Hernández  | María  | Martínez  |
	| Sofía Díaz      | Carlos | Pérez     |
	| Miguel Torres   | Laura  | Díaz      |
	+-----------------+--------+-----------+
	3 rows in set (0.00 sec)
   ```

   

4. Devuelve el nombre de los clientes que han hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

   ```mysql

   ```

   

5. Devuelve el nombre de los clientes que no hayan hecho pagos y el nombre
   de sus representantes junto con la ciudad de la oficina a la que 
   pertenece el representante.

   ```mysql
   
   ```

   

6. Lista la dirección de las oficinas que tengan clientes en Fuenlabrada.

   ```mysql
   
   SELECT direccion.linea_direccion1, direccion.linea_direccion2, 
   direccion.barrio, direccion.codigo_postal
   FROM direccion
   NATURAL JOIN ciudad
   NATURAL JOIN oficina
   NATURAL JOIN cliente
   WHERE ciudad.nombre = 'Fuenlabrada';
   
   Empty set (0.01 sec)
   ```

   

7. Devuelve el nombre de los clientes y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

   ```mysql

   SELECT c.nombre_cliente, e.nombre AS nombre_representante, e.apellido1 AS apellido_representante, o_ciudad.nombre AS
   ciudad_oficina FROM cliente c INNER JOIN empleado e ON c.id_empleado_rep_ventas = e.id_empleado INNER JOIN oficina o ON e.id_oficina = o.id_oficina INNER JOIN 
   oficina_direccion od ON o.id_oficina = od.id_oficina INNER JOIN direccion d ON od.id_direccion = d.id_direccion INNER JOIN ciudad o_ciudad ON d.id_ciudad = 
   o_ciudad.id_ciudad;

	+-------------------+----------------------+------------------------+-------------------+
	| nombre_cliente    | nombre_representante | apellido_representante | ciudad_oficina    |
	+-------------------+----------------------+------------------------+-------------------+
	| Carlos Pérez      | Juan                 | González               | Boston            |
	| Laura López       | Juan                 | González               | Boston            |
	| Ana García        | María                | Martínez               | Montreal          |
	| Juan Hernández    | María                | Martínez               | Montreal          |
	| Pedro Martínez    | Carlos               | Pérez                  | Ciudad de México  |
	| Sofía Díaz        | Carlos               | Pérez                  | Ciudad de México  |
	| María Rodríguez   | Laura                | Díaz                   | Montreal          |
	| Miguel Torres     | Laura                | Díaz                   | Montreal          |
	+-------------------+----------------------+------------------------+-------------------+
   ```

   

8. Devuelve un listado con el nombre de los empleados junto con el nombre
   de sus jefes.

   ```mysql

   SELECT e.nombre AS nombre_empleado, e.apellido1 AS apellido_empleado, 
   j.nombre AS nombre_jefe, j.apellido1 AS apellido_jefe
   FROM empleado AS e
   INNER JOIN empleado AS j ON e.id_jefe = j.id_empleado;
	+-----------------+-------------------+-------------+---------------+
	| nombre_empleado | apellido_empleado | nombre_jefe | apellido_jefe |
	+-----------------+-------------------+-------------+---------------+
	| María           | Martínez          | Juan        | González      |
	| Carlos          | Pérez             | Juan        | González      |
	| Laura           | Díaz              | Juan        | González      |
	| Ana             | Sánchez           | Juan        | González      |
	+-----------------+-------------------+-------------+---------------+
	4 rows in set (0.01 sec)
   ```


9. Devuelve un listado que muestre el nombre de cada empleados, el nombre
   de su jefe y el nombre del jefe de sus jefe.

   ```mysql
   
   SELECT e.nombre AS nombre_empleado, e.apellido1 AS apellido_empleado,
   j.nombre AS nombre_jefe, j.apellido1 AS apellido_jefe,
   jj.nombre AS nombre_jefe_jefe, jj.apellido1 AS 
   apellido_jefe_jefe
   FROM empleado AS e
   INNER JOIN empleado AS j ON e.id_jefe = j.id_empleado
   INNER JOIN empleado AS jj ON j.id_jefe = jj.id_empleado;
   
   Empty set (0.01 sec)
   ```

10. Devuelve el nombre de los clientes a los que no se les ha entregado a
    tiempo un pedido.

    ```mysql

    SELECT DISTINCT c.nombre_cliente
    FROM cliente AS c
    INNER JOIN pedido AS p ON c.id_cliente = p.id_cliente
    WHERE p.fecha_entrega > p.fecha_esperada;
    
    Empty set (0.02 sec)
    ```

    

11. Devuelve un listado de las diferentes gamas de producto que ha comprado
    cada cliente.

    ```mysql

    SELECT c.nombre_cliente, GROUP_CONCAT(DISTINCT p.gama) AS 
    gamas_compradas
    FROM cliente AS c
    INNER JOIN pedido AS pe ON c.id_cliente = pe.id_cliente
    INNER JOIN detalle_pedido AS dp ON pe.id_pedido = dp.id_pedido
    INNER JOIN producto AS p ON dp.id_producto = p.id_producto
    GROUP BY c.nombre_cliente;

        +-------------------+-----------------+
	| nombre_cliente    | gamas_compradas |
	+-------------------+-----------------+
	| Ana García        | Gama Media      |
	| Carlos Pérez      | Gama Alta       |
	| Juan Hernández    | Gama Baja       |
	| Laura López       | Gama Media      |
	| María Rodríguez   | Gama Alta       |
	| Miguel Torres     | Gama Alta       |
	| Pedro Martínez    | Gama Baja       |
	| Sofía Díaz        | Gama Alta       |
	+-------------------+-----------------+
	8 rows in set (0.03 sec)
    ```
    
Consultas multitabla (Composición externa)
Resuelva todas las consultas utilizando las cláusulas LEFT JOIN, RIGHT JOIN, NATURAL LEFT JOIN y NATURAL RIGHT JOIN.

    
1. Devuelve un listado que muestre solamente los clientes que no han
    realizado ningún pago.

    

    ```mysql
    
    ```

    

12. Devuelve un listado que muestre solamente los clientes que no han
    realizado ningún pedido.

    

    ```mysql
    
    ```

    

13. Devuelve un listado que muestre los clientes que no han realizado ningún
    pago y los que no han realizado ningún pedido.

    

    ```mysql
    
    ```

    

14. Devuelve un listado que muestre solamente los empleados que no tienen
    una oficina asociada.

    

    ```mysql
    
    ```

    

15. Devuelve un listado que muestre solamente los empleados que no tienen un
    cliente asociado.

    

    ```mysql
    
    ```

    

16. Devuelve un listado que muestre solamente los empleados que no tienen un
    cliente asociado junto con los datos de la oficina donde trabajan.

    

    ```mysql
    
    ```

    

17. Devuelve un listado que muestre los empleados que no tienen una oficina
    asociada y los que no tienen un cliente asociado.

    

    ```mysql
    
    ```

    

18. Devuelve un listado de los productos que nunca han aparecido en un
    pedido.

    

    ```mysql
    
    ```

    

19. Devuelve un listado de los productos que nunca han aparecido en un
    pedido. El resultado debe mostrar el nombre, la descripción y la imagen del
    producto.

    

    ```mysql
    
    ```

    

20. Devuelve las oficinas donde no trabajan ninguno de los empleados que
    hayan sido los representantes de ventas de algún cliente que haya realizado
    la compra de algún producto de la gama Frutales.

    

    ```mysql
    
    ```

    

21. Devuelve un listado con los clientes que han realizado algún pedido pero no
    han realizado ningún pago.

    

    ```mysql
    
    ```

    

22. Devuelve un listado con los datos de los empleados que no tienen clientes
    asociados y el nombre de su jefe asociado.
    Consultas resumen

    

    ```mysql
    
    ```

    

23. ¿Cuántos empleados hay en la compañía?

    

    ```mysql
    
    ```

    

24. ¿Cuántos clientes tiene cada país?

    

    ```mysql
    
    ```

    

25. ¿Cuál fue el pago medio en 2009?

    

    ```mysql
    
    ```

    

26. ¿Cuántos pedidos hay en cada estado? Ordena el resultado de forma
    descendente por el número de pedidos.

    

    ```mysql
    
    ```

    

27. Calcula el precio de venta del producto más caro y más barato en una
    misma consulta.

    

    ```mysql
    
    ```

    

28. Calcula el número de clientes que tiene la empresa.

    

    ```mysql
    
    ```

    

29. ¿Cuántos clientes existen con domicilio en la ciudad de Madrid?

    

    ```mysql
    
    ```

    

30. ¿Calcula cuántos clientes tiene cada una de las ciudades que empiezan
    por M?

    

    ```mysql
    
    ```

    

31. Devuelve el nombre de los representantes de ventas y el número de clientes
    al que atiende cada uno.

    

    ```mysql
    
    ```

    

32. Calcula el número de clientes que no tiene asignado representante de
    ventas.

    

    ```mysql
    
    ```

    

33. Calcula la fecha del primer y último pago realizado por cada uno de los
    clientes. El listado deberá mostrar el nombre y los apellidos de cada cliente.

    

    ```mysql
    
    ```

    

34. Calcula el número de productos diferentes que hay en cada uno de los
    pedidos.

    

    ```mysql
    
    ```

    

35. Calcula la suma de la cantidad total de todos los productos que aparecen en
    cada uno de los pedidos.

    

    ```mysql
    
    ```

    

36. Devuelve un listado de los 20 productos más vendidos y el número total de
    unidades que se han vendido de cada uno. El listado deberá estar ordenado
    por el número total de unidades vendidas.

    

    ```mysql
    
    ```

    

37. La facturación que ha tenido la empresa en toda la historia, indicando la
    base imponible, el IVA y el total facturado. La base imponible se calcula
    sumando el coste del producto por el número de unidades vendidas de la
    tabla detalle_pedido. El IVA es el 21 % de la base imponible, y el total la
    suma de los dos campos anteriores.

    

    ```mysql
    
    ```

    

38. La misma información que en la pregunta anterior, pero agrupada por
    código de producto.

    

    ```mysql
    
    ```

    

39. La misma información que en la pregunta anterior, pero agrupada por
    código de producto filtrada por los códigos que empiecen por OR.

    

    ```mysql
    
    ```

    

40. Lista las ventas totales de los productos que hayan facturado más de 3000
    euros. Se mostrará el nombre, unidades vendidas, total facturado y total
    facturado con impuestos (21% IVA).

    

    ```mysql
    
    ```

    

41. Muestre la suma total de todos los pagos que se realizaron para cada uno
    de los años que aparecen en la tabla pagos.

    

    ```mysql
    
    ```

    

**Consultas variadas**

1. Devuelve el listado de clientes indicando el nombre del cliente y cuántos
   pedidos ha realizado. Tenga en cuenta que pueden existir clientes que no
   han realizado ningún pedido.

   

   ```mysql
   
   ```

   

2. Devuelve un listado con los nombres de los clientes y el total pagado por
   cada uno de ellos. Tenga en cuenta que pueden existir clientes que no han
   realizado ningún pago.

   

   ```mysql
   
   ```

   

3. Devuelve el nombre de los clientes que hayan hecho pedidos en 2008
   ordenados alfabéticamente de menor a mayor.

   

   ```mysql
   
   ```

   

4. Devuelve el nombre del cliente, el nombre y primer apellido de su
   representante de ventas y el número de teléfono de la oficina del                                                                                                                    representante de ventas, de aquellos clientes que no hayan realizado ningún
   pago.

   

   ```mysql
   
   ```

   

5. Devuelve el listado de clientes donde aparezca el nombre del cliente, el
   nombre y primer apellido de su representante de ventas y la ciudad donde
   está su oficina.

   

   ```mysql
   
   ```

   

6. Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos
   empleados que no sean representante de ventas de ningún cliente.

   

   ```mysql
   
   ```

   

7. Devuelve un listado indicando todas las ciudades donde hay oficinas y el
   número de empleados que tiene.

   

   ```mysql
   
   ```

   
