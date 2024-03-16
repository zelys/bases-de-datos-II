# ENUNCIADO CASO 3

El caso a desarrollar se enrola dentro del ámbito educativo, donde al interior de un colegio se evalúan a los alumnos de Cuarto Medio que ya están por egresar de dicha institución.

En este contexto existen diferentes consultas a programar, acorde al enunciado con el fin de hacer una mejor gestión para gestionar pruebas o ayudantías remediales, dado que el colegio realiza la metodología 2x1 para recuperar y obtener la enseñanza media completada.

## DESARROLLO DEL CASO COLEGIO

En el contexto del Colegio, los alumnos rinden pruebas parciales en cada asignatura llevándose un control manual de todas las pruebas rendidas por semestre, razón por la cual le entregan a usted un esquema simple de trabajo que se detalla a continuación. Donde la entidad Persona detalla a los alumnos de este liceo que es una institución educativa 2x1 (Para recuperar enseñanza media).

### PERSONA

| rut        | nombre       | domicilio               | fec_nac    | edad |
| ---------- | ------------ | ----------------------- | ---------- | ---- |
| 10357596-6 | Aldo Catalán | Av. Holanda #455 Ñuñoa  | 03/11/1969 | 42   |
| 14225233-K | Oscar Rivas  | Av. Ossa #1250 La Reina | 05/06/1988 | 24   |
| 20212322-7 | Ana Sánchez  | Pasaje 34 La Cisterna   | 22/07/1977 |      |

### NOTAS

| nom_asig  | notas | fecha_nota | profesor_asig | rut        |
| --------- | :---: | ---------- | ------------- | ---------- |
| Ingles II |  6,4  | 06/08/2011 | Miss Olga R.  | 20212322-7 |
| Etica     |  5,7  | 07/05/2010 | H.Moraga      | 10357596-6 |
| Ingles II |   7   | 06/09/2011 | Miss Olga R.  | 20212322-7 |
| Ingles II |   5   | 07/10/2011 | Miss Olga R.  | 20212322-7 |
| Etica     |   6   | 07/06/2010 | P.Gómez       | 14225233-K |

En base al modelo expuesto, usted debe programas diferentes consultas que son requeridas por los usuarios del sistema, como docentes, directores y apoderados, para la cual usted debe desarrollar los siguientes ejercicios:

### PREGUNTAS

1. Crear la base de datos respectiva identificando los tipos de datos pertinentes junto con determinar las llaves primarias y foráneas.

```sql
CREATE DATABASE IF NOT EXISTS colegio;

USE colegio;

/* Se crean las tablas */

CREATE TABLE alumno (
    rut CHAR(10) UNIQUE PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL UNIQUE,
    domicilio VARCHAR(100),
    fecha_nac DATE,
    edad INT UNSIGNED
);

CREATE TABLE notas (
    fecha_nota DATE NOT NULL PRIMARY KEY,
    nom_asig VARCHAR (20) NOT NULL,
    notas FLOAT(2,1) UNSIGNED NOT NULL,
    profesor_asig VARCHAR(100),
    rut CHAR(10),
    FOREIGN KEY(rut) REFERENCES alumno(rut)
);

INSERT INTO alumno(rut, nombre, domicilio, fecha_nac, edad)
VALUES
('10357596-6', 'Aldo Catalán', 'Av. Holanda #455 Ñuñoa', '1969-11-03', 42),
('14225233-K', 'Oscar Rivas', 'Av. Ossa #1250 La Reina', '1988-06-05', 24);

INSERT INTO alumno(rut, nombre, domicilio, fecha_nac)
VALUES
('20212322-7', 'Ana Sánchez', 'Pasaje 34 La Cisterna', '1977-07-22');

INSERT INTO notas(fecha_nota, nom_asig, notas, profesor_asig, rut)
VALUES
('2011-08-06', 'Ingles II', 6.4, 'Miss Olga R.', '20212322-7'),
('2010-05-07', 'Etica', 5.7, 'H.Moraga', '10357596-6'),
('2011-09-06', 'Ingles II', 7, 'Miss Olga R.', '20212322-7'),
('2011-10-07', 'Ingles II', 5, 'Miss Olga R.', '20212322-7'),
('2010-06-07', 'Etica', 6, 'P.Gómez', '14225233-K');
```

2. Se necesita listar el Rut y nombre de todos los alumnos con la asignatura, nota y fecha de la nota respectiva.

```sql
SELECT a.rut, a.nombre, n.nom_asig, n.notas, n.fecha_nota
FROM alumno a
JOIN notas n ON a.rut = n.rut;
```

```sh
+------------+---------------+-----------+-------+------------+
| rut        | nombre        | nom_asig  | notas | fecha_nota |
+------------+---------------+-----------+-------+------------+
| 10357596-6 | Aldo Catalán  | Etica     |   5.7 | 2010-05-07 |
| 20212322-7 | Ana Sánchez   | Ingles II |   6.4 | 2011-08-06 |
| 20212322-7 | Ana Sánchez   | Ingles II |   7.0 | 2011-09-06 |
| 20212322-7 | Ana Sánchez   | Ingles II |   5.0 | 2011-10-07 |
| 14225233-K | Oscar Rivas   | Etica     |   6.0 | 2010-06-07 |
+------------+---------------+-----------+-------+------------+
```

Además de conocer el promedio (con dos decimales) de Ana Sánchez en la asignatura de inglés II.

```sql
SELECT a.nombre, ROUND(AVG(n.notas), 2) AS Promedio
FROM notas n
JOIN alumno a ON a.rut = n.rut
WHERE a.nombre = 'Ana Sánchez'
GROUP BY a.nombre;
```

```sh
+--------------+----------+
| nombre       | Promedio |
+--------------+----------+
| Ana Sánchez  |     6.13 |
+--------------+----------+
```

3. Crear una vista que entregue el nombre del alumno (persona) y el promedio obtenido en cada asignatura.

```sql
CREATE OR REPLACE VIEW promedio_asig
AS SELECT a.nombre, n.nom_asig, ROUND(AVG(n.notas), 2) AS Promedio
FROM notas n
JOIN alumno a ON a.rut = n.rut
GROUP BY a.nombre, n.nom_asig;
```

```sh
mysql> SELECT * FROM promedio_asig;
+---------------+-----------+----------+
| nombre        | nom_asig  | Promedio |
+---------------+-----------+----------+
| Aldo Catalán  | Etica     |      5.7 |
| Ana Sánchez   | Ingles II |     6.13 |
| Oscar Rivas   | Etica     |        6 |
+---------------+-----------+----------+
```
