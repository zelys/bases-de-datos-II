# ENUNCIADO CASO 1

El caso a desarrollar se enrola dentro del ámbito de compraventas de automóviles, ubicada en la región metropolitana donde se deben conocer datos e información de los vehículos que se transan (Venden o compran).

En este contexto existen diferentes problemas a solucionar en el entorno de programación bajo SQL, acorde al enunciado siguiente.

## DESARROLLO DEL CASO COMPRA VENTAS DE VEHICULOS

Existe un local que se dedica a la compra venta de vehículos usados y nuevos, donde diariamente se generan múltiples transacciones como ventas, tasaciones y otros.

Por esta razón se desea automatizar algunos reportes que se realizan manualmente dado que no existe una base de datos para la gestión de todas las acciones.

Todo se maneja en planillas en Excel. Dada la premura de la generación de un sistema le invitan a usted como ingeniero analista en programar algunas consultas sencillas y de urgente desarrollo.

El siguiente es un esquema resumido de parte de la información y datos que se manejan:

### PROPIETARIO

| **rut_cli** | nombre_cliente | direccion_cliente                |
| ----------- | -------------- | -------------------------------- |
| 13457305-9  | Ana Castro     | Av. Carmen 1680 Recoleta         |
| 20242591-K  | José Mora      | Alonso Ovalle 240 La Reina       |
| 18357596-6  | Luis Parodi    | Arturo Prat 4410 Santiago Centro |

### VEHICULO

| **patente** | marca     | modelo   | color  | rut_cli    |
| :---------: | --------- | -------- | ------ | ---------- |
|   VGRT22    | Suzuki    | Alto     | Negro  | 20242591-K |
|   LCSH97    | Subaru    | Forester | Gris   | 13457305-9 |
|   ANTH21    | Mahindra  | Scorpio  | Blanco | 13457305-9 |
|   LCER43    | Suzuki    | Alto     | Negro  | 20242591-K |
|   AGDW32    | Chevrolet | Spark II | Rojo   | 20242591-K |

### PREGUNTAS:

1. Usted debe crear las entidades PROPIETARIO Y VEHICULO definiendo las llaves primarias y foráneas, para luego insertar los datos a las entidades tal como se describen en la imagen.

```sql

/* Se crea la base de datos */
CREATE DATABASE IF NOT EXISTS compra_venta_vehiculos;

USE compra_venta_vehiculos;

/* Se crean las tablas */
CREATE TABLE propietario (
    id_cliente INT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    rut_cliente CHAR(10) NOT NULL UNIQUE,
    nombre_cliente VARCHAR(50) NOT NULL,
    direccion_cliente VARCHAR(100)
);

CREATE TABLE vehiculo (
    id_vehiculo INT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    patente CHAR(6) NOT NULL UNIQUE,
    marca VARCHAR(20) NOT NULL,
    modelo VARCHAR(20) NOT NULL,
    color VARCHAR(20),
    id_cliente INT UNSIGNED,
    FOREIGN KEY(id_cliente) REFERENCES propietario(id_cliente)
);

/* Se agregar los registros */
INSERT INTO propietario(rut_cliente, nombre_cliente, direccion_cliente)
VALUES
('13457305-9', 'Ana Castro', 'Av. Carmen 1680 Recoleta'),
('20242591-K', 'José Mora', 'Alonso Ovalle 240 La Reina'),
('18357596-6','Luis Parodi','Arturo Prat 4410 Santiago Centro');

INSERT INTO vehiculo(patente, marca, modelo, color, id_cliente)
VALUES
('VGRT22', 'Suzuki', 'Alto', 'Negro', 2),
('LCSH97', 'Subaru', 'Forester', 'Gris', 1),
('ANTH21', 'Mahindra', 'Scorpio', 'Blanco', 1),
('LCER43', 'Suzuki', 'Alto', 'Negro', 2),
('AGDW32', 'Chevrolet', 'Spark II', 'Rojo', 2);
```

2. Desarrollar las siguientes consultas:

- Crear una consulta o query mediante el comando select que permita el total de vehículos por marca, especificando el nombre de la marca y el total de dicha marca.

```sql
SELECT marca, COUNT(*) AS total FROM vehiculo GROUP BY marca;
```

```sh
+-----------+-------+
| marca     | total |
+-----------+-------+
| Suzuki    |     2 |
| Subaru    |     1 |
| Mahindra  |     1 |
| Chevrolet |     1 |
+-----------+-------+
```

- Obtener los datos de los vehículos del propietario José Mora, indicando patente, marca y modelo.

```sql
SELECT p.nombre_cliente, v.patente, v.marca, v.modelo FROM vehiculo v
JOIN propietario p ON v.id_cliente = p.id_cliente
WHERE p.nombre_cliente = 'José Mora'
GROUP BY v.patente;
```

```sh
+----------------+---------+-----------+----------+
| nombre_cliente | patente | marca     | modelo   |
+----------------+---------+-----------+----------+
| José Mora      | VGRT22  | Suzuki    | Alto     |
| José Mora      | LCER43  | Suzuki    | Alto     |
| José Mora      | AGDW32  | Chevrolet | Spark II |
+----------------+---------+-----------+----------+
```

3. Implementar una vista llamada Vista_reporte, de permita identificar de las marcas Mahindra y Suzuki quienes son sus respectivos propietarios. La vista debe señalar patente, marca, y nombre del dueño ordenado por patente (en forma ascendente)

```sql
CREATE OR REPLACE VIEW vista_reporte
AS SELECT v.patente, v.marca, p.nombre_cliente
FROM vehiculo v
JOIN propietario p ON v.id_cliente = p.id_cliente
WHERE v.marca = 'Mahindra' OR v.marca = 'Suzuki'
ORDER BY v.patente ASC;
```

```sh
+---------+----------+----------------+
| patente | marca    | nombre_cliente |
+---------+----------+----------------+
| ANTH21  | Mahindra | Ana Castro     |
| LCER43  | Suzuki   | José Mora      |
| VGRT22  | Suzuki   | José Mora      |
+---------+----------+----------------+
```
