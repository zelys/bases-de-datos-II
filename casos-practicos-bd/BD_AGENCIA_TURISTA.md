# ENUNCIADO CASO 2

El caso a desarrollar se enrola dentro del ámbito turístico donde se desean conocer los principales atractivos de las ciudades que pertenecen a regiones claves para los turistas que continuamente viajan al o interior del país.

En este contexto existen diferentes problemas a solucionar, acorde al enunciado y esquema planteado.

## DESARROLLO DEL CASO AGENCIA TURISTA

Existe un desorden administrativo desde hace 6 meses, dentro de una agencia de turismo donde la perdida de información se ha vuelto recurrente, pues no existe un medio de homogeneizar la información que utilizan.

La idea del proyecto es entregar una solución que permita identificar servicios turísticos por región acorde a las ciudades que entregan lugares para conocer o atractivos gastronómicos.

Para inicia esta idea de solución como primera fase se desea construir algunas entidades que contengan información de las regiones, capitales y otros. El siguiente esbozo es una base para futura consultas hacia una base de datos más poderosa.

### CIUDAD

| COD_CIUDAD | NOMBRE_CIUDAD |
| ---------: | ------------- |
|          1 | VIÑA DEL MAR  |
|          2 | CONCON        |
|          3 | RANCAGUA      |
|          4 | CHUQUICAMATA  |
|          5 | ELQUI         |
|          6 | TALCAHUANO    |

### REGION

| COD_REGION | NOMBRE_REGION                  |
| ---------- | ------------------------------ |
| I          | TARAPACA                       |
| II         | ANTOFAGASTA                    |
| III        | ATACAMA                        |
| IV         | COQUIBO                        |
| V          | VALPARAISO                     |
| VI         | LIBERTADOR BERNARDO O'HIGGINNS |

### CIUDAD_REGION

| COD_CIUDAD | COD_REGION |
| ---------: | ---------- |
|          5 | IV         |
|          2 | V          |
|          1 | V          |
|          3 | VI         |
|          4 | II         |
|          6 | VIII       |

En base a esta estructura dada, se pide resolver las siguientes preguntas:

### PREGUNTAS

1. Crear y definir la estructura dada e insertar los registros con la información dada en la imagen. Identificando las llaves primarias y foráneas respectivas.

```sql
/* Se crea la base de datos */
CREATE DATABASE IF NOT EXISTS agencia_turista;

USE agencia_turista;

/* Se crean las tablas */
CREATE TABLE ciudad (
    cod_ciudad INT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    nombre_ciudad VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE region (
    cod_region CHAR(5) NOT NULL UNIQUE PRIMARY KEY,
    nombre_region VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE ciudad_region (
  cod_ciudad INT UNSIGNED,
  cod_region CHAR(5),
  FOREIGN KEY(cod_ciudad) REFERENCES ciudad(cod_ciudad),
  FOREIGN KEY(cod_region) REFERENCES region(cod_region)
);

/* Se agregar los registros */
INSERT INTO ciudad(nombre_ciudad)
VALUES
("VIÑA DEL MAR"),
("CONCON"),
("RANCAGUA"),
("CHUQUICAMATA"),
("ELQUI"),
("TALCAHUANO");

INSERT INTO region(cod_region, nombre_region)
VALUES
("I","TARAPACA"),
("II","ANTOFAGASTA"),
("III","ATACAMA"),
("IV","COQUIBO"),
("V","VALPARAISO"),
("VI","LIBERTADOR BERNARDO O'HIGGINNS");

INSERT INTO ciudad_region(cod_ciudad, cod_region)
VALUES
(5, 'IV'),
(2, 'V'),
(1, 'V'),
(3, 'VI'),
(4, 'II'),
(6, 'VIII');
```

2. Usted debe programar y responde las siguientes consultas en SQL:

- En el caso de la ciudad de Talcahuano como puede asociarla a la VIII región si no existe el registro respectivo en la entidad REGION?

Según la estructura propuesta en el caso, la entidad ciudad_region asocia dos tablas a través de la clave foránea, por lo que el sistema gestor de base de datos no nos permitirá insertar un registro que no existe en la tabla region.

Para solucionar esto podemos insertar un nuevo registro en la tabla region para la región del BIOBÍO, sin que esto afecte a las demás tablas.

```sql
INSERT INTO region(cod_region, nombre_region)
VALUES("VIII","BIOBÍO");
```

De esta manera el SGBD nos permitirá insertar el registro en la tabla ciudad_region.

```sql
INSERT INTO ciudad_region(cod_ciudad, cod_region)
VALUES (6, 'VIII');
```

- Listar el nombre de todas las ciudades pertenecientes a la región de Valparaíso.

```sql
SELECT c.nombre_ciudad
FROM ciudad c
JOIN ciudad_region cr ON c.cod_ciudad = cr.cod_ciudad
JOIN region r ON r.cod_region = cr.cod_region
WHERE r.nombre_region = "VALPARAISO";
```

```sh
+---------------+
| nombre_ciudad |
+---------------+
| CONCON        |
| VIÑA DEL MAR  |
+---------------+
```

3. Finalmente se necesita diseñar una vista llamada VISTA_001 que permita contabilizar el total de ciudades por Región, existentes en esta base de datos.

```sql
CREATE OR REPLACE VIEW vista_001
AS SELECT r.nombre_region, COUNT(*) AS total_ciudades
FROM region r
JOIN ciudad_region cr ON r.cod_region = cr.cod_region
GROUP BY r.nombre_region;
```

```sh
mysql> SELECT * FROM vista_001;
+--------------------------------+----------------+
| nombre_region                  | total_ciudades |
+--------------------------------+----------------+
| ANTOFAGASTA                    |              1 |
| COQUIBO                        |              1 |
| VALPARAISO                     |              2 |
| LIBERTADOR BERNARDO O'HIGGINNS |              1 |
| BIOBÍO                         |              1 |
+--------------------------------+----------------+
```
