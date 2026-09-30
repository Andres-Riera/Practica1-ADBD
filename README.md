# Practica1-ADBD

## 1.

```sql
CREATE DATABASE biblioteca;

```

## 2.

**a.**

**i.**

```sql
CREATE ROLE admin_biblio WITH LOGIN PASSWORD 'adminpass';

```

**ii.**

```sql
CREATE ROLE usuario_biblio WITH LOGIN PASSWORD 'usuariopass';

```

**b.**

```sql
CREATE ROLE lectores;

```

```text
postgres=# GRANT CONNECT ON DATABASE biblioteca TO lectores;
GRANT
postgres=# GRANT USAGE ON SCHEMA public TO lectores;
GRANT
postgres=# GRANT SELECT ON ALL TABLES IN SCHEMA public TO lectores;
GRANT

```

**c.**

```text
postgres=# GRANT lectores TO usuario_biblio;
NOTICE:  role "usuario_biblio" has already been granted membership in role "lectores" by role "postgres"
GRANT ROLE

```

**d.**

```text
postgres=# SELECT rolname, rolcanlogin, rolsuper
postgres-# FROM pg_roles
postgres-# WHERE rolname IN ('admin_biblio', 'usuario_biblio', 'lectores');
    rolname     | rolcanlogin | rolsuper
----------------+-------------+----------
 admin_biblio   | t           | f
 lectores       | f           | f
 usuario_biblio | t           | f
(3 rows)

```

**e.**

```text
postgres=# ALTER USER usuario_biblio WITH PASSWORD '123';
ALTER ROLE

```

**f.**

```text
postgres=# REVOKE DELETE ON ALL TABLES IN SCHEMA public FROM usuario_biblio;
REVOKE

```

## 3.

**i.**

```sql
CREATE TABLE autores(
id_autor SERIAL PRIMARY KEY,
nombre TEXT NOT NULL,
nacionalidad TEXT
);

```

**ii.**

```text
biblioteca=# CREATE TABLE libros (
id_libro SERIAL PRIMARY KEY,
titulo TEXT NOT NULL,
año_publicacion INT,
id_autor INT NOT NULL,
CONSTRAINT clave_foranea_autor FOREIGN KEY (id_autor)
REFERENCES autores(id_autor)
ON DELETE CASCADE
);
CREATE TABLE

```

**iii.**

```text
biblioteca=# CREATE TABLE prestamos (
biblioteca(# id_prestamo SERIAL PRIMARY KEY,
biblioteca(# id_libro INT NOT NULL,
biblioteca(# fecha_prestamo DATE NOT NULL DEFAULT CURRENT_DATE,
biblioteca(# fecha_devolucion DATE,
biblioteca(# usuario_prestatario TEXT NOT NULL,
biblioteca(# CONSTRAINT clave_foranea_libro FOREIGN KEY (id_libro)
biblioteca(# REFERENCES libros(id_libro)
biblioteca(# ON DELETE CASCADE
biblioteca(# );
CREATE TABLE

```

## 4.

```text
biblioteca=# INSERT INTO autores (nombre, nacionalidad) VALUES
('Gabriel García Márquez', 'Colombia'),
('Miguel de Cervantes', 'España'),
('George Orwell', 'Inglaterra'),
('Isabel Allende', 'Chile'),
('Jorge Luis Borges', 'Argentina');
INSERT 0 5

biblioteca=# SELECT * FROM AUTORES;
 id_autor |         nombre         | nacionalidad
----------+------------------------+--------------
        1 | Gabriel García Márquez | Colombia
        2 | Miguel de Cervantes    | España
        3 | George Orwell          | Inglaterra
        4 | Isabel Allende         | Chile
        5 | Jorge Luis Borges      | Argentina
(5 rows)

```

```text
biblioteca=# INSERT INTO libros (titulo, año_publicacion, id_autor) VALUES
('Cien años de soledad', 1967, 1),
('El amor en los tiempos del cólera', 1985, 1),
('Don Quijote de la Mancha', 1605, 2),
('1984', 1949, 3),
('Rebelión en la granja', 1945, 3),
('La casa de los espíritus', 1982, 4),
('Ficciones', 1944, 5),
('El Aleph', 1949, 5);
INSERT 0 8

biblioteca=# SELECT * FROM LIBROS;
 id_libro |               titulo              | año_publicacion | id_autor
----------+-----------------------------------+-----------------+----------
        1 | Cien años de soledad              |            1967 |        1
        2 | El amor en los tiempos del cólera |            1985 |        1
        3 | Don Quijote de la Mancha          |            1605 |        2
        4 | 1984                              |            1949 |        3
        5 | Rebelión en la granja             |            1945 |        3
        6 | La casa de los espíritus          |            1982 |        4
        7 | Ficciones                         |            1944 |        5
        8 | El Aleph                          |            1949 |        5
(8 rows)

```

```text
biblioteca=# INSERT INTO prestamos (id_libro, fecha_prestamo, fecha_devolucion, usuario_prestatario) VALUES
(1, '2026-09-01', '2026-09-15', 'Carlos Pérez'),
(4, '2026-09-10', NULL, 'Ana Gómez'),
(3, '2026-09-12', '2026-09-20', 'Laura Torres'),
(1, '2026-09-22', NULL, 'Carlos Pérez'),
(5, '2026-09-25', NULL, 'Marcos Ruiz');
INSERT 0 5

biblioteca=# SELECT * FROM PRESTAMOS;
 id_prestamo | id_libro | fecha_prestamo | fecha_devolucion | usuario_prestatario
-------------+----------+----------------+------------------+---------------------
           1 |        1 | 2026-09-01     | 2026-09-15       | Carlos Pérez
           2 |        4 | 2026-09-10     |                  | Ana Gómez
           3 |        3 | 2026-09-12     | 2026-09-20       | Laura Torres
           4 |        1 | 2026-09-22     |                  | Carlos Pérez
           5 |        5 | 2026-09-25     |                  | Marcos Ruiz
(5 rows)

```

## 5.

**a.**

```text
biblioteca=# SELECT l.titulo, a.nombre FROM
biblioteca-# libros as l natural join autores as a;
               titulo              |         nombre
-----------------------------------+------------------------
 Cien años de soledad              | Gabriel García Márquez
 El amor en los tiempos del cólera | Gabriel García Márquez
 Don Quijote de la Mancha          | Miguel de Cervantes
 1984                              | George Orwell
 Rebelión en la granja             | George Orwell
 La casa de los espíritus          | Isabel Allende
 Ficciones                         | Jorge Luis Borges
 El Aleph                          | Jorge Luis Borges
(8 rows)

```

**b.**

```text
biblioteca=# SELECT * FROM prestamos
biblioteca-# WHERE fecha_devolucion IS NULL;
 id_prestamo | id_libro | fecha_prestamo | fecha_devolucion | usuario_prestatario
-------------+----------+----------------+------------------+---------------------
           2 |        4 | 2026-09-10     |                  | Ana Gómez
           4 |        1 | 2026-09-22     |                  | Carlos Pérez
           5 |        5 | 2026-09-25     |                  | Marcos Ruiz
(3 rows)

```

**c.**

```text
biblioteca=# SELECT a.nombre
biblioteca-# FROM autores AS a NATURAL JOIN libros as l
biblioteca-# GROUP BY a.id_autor, a.nombre
biblioteca-# HAVING COUNT(l.id_libro) > 1;
         nombre
------------------------
 George Orwell
 Jorge Luis Borges
 Gabriel García Márquez
(3 rows)

```

## 6.

**a.**

```text
biblioteca=# SELECT COUNT(*)
biblioteca-# FROM prestamos;
 count
-------
     5
(1 row)

```

**b.**

```text
biblioteca=# SELECT usuario_prestatario, COUNT(*)
FROM prestamos
GROUP BY usuario_prestatario
;
 usuario_prestatario | count
---------------------+-------
 Ana Gómez           |     1
 Carlos Pérez        |     2
 Laura Torres        |     1
 Marcos Ruiz         |     1
(4 rows)

```

## 7.

**a.**

```text
biblioteca=# UPDATE prestamos
biblioteca-# SET fecha_devolucion = '2026-09-29'
biblioteca-# WHERE id_prestamo = 2;
UPDATE 1

biblioteca=# SELECT * FROM prestamos
biblioteca-# ;
 id_prestamo | id_libro | fecha_prestamo | fecha_devolucion | usuario_prestatario
-------------+----------+----------------+------------------+---------------------
           1 |        1 | 2026-09-01     | 2026-09-15       | Carlos Pérez
           3 |        3 | 2026-09-12     | 2026-09-20       | Laura Torres
           4 |        1 | 2026-09-22     |                  | Carlos Pérez
           5 |        5 | 2026-09-25     |                  | Marcos Ruiz
           2 |        4 | 2026-09-10     | 2026-09-29       | Ana Gómez
(5 rows)

```

**b.**

```text
biblioteca=# DELETE FROM libros WHERE id_libro = 5;
DELETE 1

biblioteca=# SELECT * FROM PRESTAMOS
biblioteca-# WHERE id_libro = 5;
 id_prestamo | id_libro | fecha_prestamo | fecha_devolucion | usuario_prestatario
-------------+----------+----------------+------------------+---------------------
(0 rows)

```

## 8.

**a.**

```text
biblioteca=# CREATE VIEW vista_libros_prestados AS
SELECT l.titulo, a.nombre AS autor, p.usuario_prestatario
FROM prestamos p
NATURAL JOIN AUTORES AS a
NATURAL JOIN libros AS l
;
CREATE VIEW

biblioteca=# SELECT * FROM vista_libros_prestados;
          titulo          |         autor          | usuario_prestatario
--------------------------+------------------------+---------------------
 Cien años de soledad     | Gabriel García Márquez | Carlos Pérez
 Don Quijote de la Mancha | Miguel de Cervantes    | Laura Torres
 Cien años de soledad     | Gabriel García Márquez | Carlos Pérez
 1984                     | George Orwell          | Ana Gómez
(4 rows)

```

**b.**

```text
biblioteca=# REVOKE ALL ON vista_libros_prestados FROM PUBLIC;
REVOKE
biblioteca=# GRANT SELECT ON vista_libros_prestados TO usuario_biblio;
GRANT

```

## 9.

**a.**

```text
biblioteca=# CREATE OR REPLACE FUNCTION libros_por_autor(nombre_autor TEXT)
RETURNS TABLE (
    id_libro INT,
    titulo TEXT,
    año_publicacion INT
) AS $$
BEGIN
    RETURN QUERY
    SELECT l.id_libro, l.titulo, l.año_publicacion
    FROM libros AS l NATURAL JOIN autores AS a WHERE a.nombre = nombre_autor;
END;
$$ LANGUAGE plpgsql;
CREATE FUNCTION

biblioteca=# SELECT libros_por_autor('Miguel de Cervantes');
         libros_por_autor
-------------------------------------
 (3,"Don Quijote de la Mancha",1605)
(1 row)

```

**b.**

```text
biblioteca=# SELECT l.titulo, COUNT(p.id_prestamo) as numero_prestamos
FROM libros AS l
NATURAL JOIN prestamos AS p
GROUP BY l.id_libro, l.titulo
ORDER BY numero_prestamos DESC
LIMIT 3;
          titulo          | numero_prestamos
--------------------------+------------------
 Cien años de soledad     |                2
 Don Quijote de la Mancha |                1
 1984                     |                1
(3 rows)

```

## 10.

**a.**

```text
biblioteca=# \copy libros TO '/tmp/libros_exportados.csv' WITH (FORMAT CSV, HEADER);
COPY 7

```

**b.**

```text
biblioteca=# \copy autores (nombre, nacionalidad) FROM 'autores.csv' WITH (FORMAT CSV, HEADER);
COPY 2

biblioteca=# select * from autores;
 id_autor |         nombre         | nacionalidad
----------+------------------------+--------------
        1 | Gabriel García Márquez | Colombia
        2 | Miguel de Cervantes    | España
        3 | George Orwell          | Inglaterra
        4 | Isabel Allende         | Chile
        5 | Jorge Luis Borges      | Argentina
        6 | 'Sofía'                | 'México'
        7 | 'Daniel'               | 'Venezuela'
(7 rows)

```
