------------------------------------------------1. Diseño inicial y creación de la base de datos---------------------------------------------------------------
---------------------------------------------------------------------------------------------------------------------------------------------------------------

CREATE DATABASE gestion_academica_universidad;

---------------------------------------------------------------------------------------------------------------------------------------------------------------

CREATE TABLE estudiantes (
    ->     id_estudiante INT PRIMARY KEY AUTO_INCREMENT,
    ->     nombre_completo VARCHAR(100) NOT NULL,
    ->     correo_electronico VARCHAR(150) UNIQUE,
    ->     genero ENUM('Masculino','Femenino','Otro') NOT NULL,
    ->     identificacion VARCHAR(50) NOT NULL UNIQUE,
    ->     carrera VARCHAR(100) NOT NULL,
    ->     fecha_nacimiento DATE NOT NULL,
    ->     fecha_ingreso DATE NOT NULL
    -> );

--------------------------------------------------------------------------------------------------------------------------------------------------------------    

CREATE TABLE docente (
    -> id_docente INT PRIMARY KEY AUTO_INCREMENT,
    -> nombre_completo VARCHAR(100) NOT NULL,
    -> correo_institucional VARCHAR(100) NOT NULL UNIQUE,
    -> departamento_academico VARCHAR(100) NOT NULL,
    -> anios_experiencia INT NOT NULL
    -> );

--------------------------------------------------------------------------------------------------------------------------------------------------------------

CREATE TABLE cursos(
    ->      id_curso INT PRIMARY KEY AUTO_INCREMENT,
    ->      nombre VARCHAR (50) NOT NULL,
    ->      codigo VARCHAR (50) NOT NULL UNIQUE,
    ->      creditos INT NOT NULL,
    ->      semestre INT NOT NULL,
    ->      id_docente INT,
    ->      FOREIGN KEY(id_docente) REFERENCES docente(id_docente)
    ->      ON DELETE CASCADE);

--------------------------------------------------------------------------------------------------------------------------------------------------------------

CREATE TABLE inscripciones(
    -> id_inscripcion INT PRIMARY KEY AUTO_INCREMENT,
    -> id_estudiante INT NOT NULL,
    -> id_curso INT NOT NULL,
    -> fecha_inscripcion DATE NOT NULL,
    -> calificacion_final INT NOT NULL,
    -> FOREIGN KEY (id_estudiante)REFERENCES estudiantes(id_estudiante)
    -> ON DELETE CASCADE
    -> ON UPDATE CASCADE,
    -> FOREIGN KEY (id_curso)REFERENCES cursos(id_curso)
    -> ON DELETE CASCADE
    -> ON UPDATE CASCADE,
    -> UNIQUE (id_estudiante, id_curso));

-------------------------------------------------------------------------------------------------------------------------------------------------------------    
-------------------------------------------------------------------2. Inserción de datos---------------------------------------------------------------------


INSERT INTO estudiantes (nombre, correo_electronico,genero,identificacion,carrera, fecha_nacimiento, fecha_ingreso) VALUES ('Juan Pérez', 'juan.perez@gmail.com', 'Masculino', '1010', 'Ingeniería', '2000-05-12', '2022-01-10'),
    -> ('Ana Gómez', 'ana.gomez@gmail.com', 'Femenino', '1011', 'Medicina', '1999-08-21', '2021-09-05'),
    -> ('Luis Martínez', 'luis.martinez@gmail.com', 'Masculino', '1012', 'Arquitectura', '2001-03-30', '2022-02-15'),
    -> ('Carla Rodríguez', 'carla.rodriguez@gmail.com', 'Femenino', '1013', 'Derecho', '2000-11-17', '2021-08-20'),
    -> ('Sofía Torres', 'sofia.torres@gmail.com', 'Femenino', '1014', 'Ingeniería', '2002-01-05', '2022-03-12');

------------------------------------------------------------------------------------------------------------------------------------------------------------   

 INSERT INTO docente (nombre_completo, correo_institucional, departamento_academico, anios_experiencia)
    -> VALUES
    -> ('Karen López', 'karen.lopez@uni.edu', 'Matemáticas', 10),
    -> ('Kevin Ramírez', 'kevin.ramirez@uni.edu', 'Física', 8),
    -> ('Marta Sánchez', 'marta.sanchez@uni.edu', 'Química', 12);

-------------------------------------------------------------------------------------------------------------------------------------------------------------

 INSERT INTO cursos (nombre, codigo, creditos, semestre, id_docente)
    -> VALUES
    -> ('Cálculo I', 'MAT101', 4, 1, 1),
    -> ('Física I', 'FIS101', 3, 1, 2),
    -> ('Química General', 'QUI101', 4, 1, 3),
    -> ('Álgebra Lineal', 'MAT102', 3, 2, 1);

-------------------------------------------------------------------------------------------------------------------------------------------------------------

 INSERT INTO inscripciones (id_estudiante, id_curso, fecha_inscripcion, calificacion_final)
    -> VALUES
    -> (1, 1, '2022-01-15', 4.5),
    -> (1, 2, '2022-01-16', 3.8),
    -> (2, 1, '2021-09-10', 4.0),
    -> (2, 3, '2021-09-12', 3.7),
    -> (3, 2, '2022-02-20', 4.2),
    -> (3, 4, '2022-02-22', 4.8),
    -> (4, 1, '2021-08-25', 3.9),
    -> (5, 4, '2022-03-15', 4.6);


-------------------------------------------------------------------------------------------------------------------------------------------------------------
------------------------------------------------------------3. Consultas básicas y manipulación--------------------------------------------------------------

    SELECT 
        e.id_estudiante,
        e.nombre AS nombre_estudiante,
        c.id_curso AS curso_id,
        c.nombre AS nombre_curso,
        i.fecha_inscripcion,
        i.calificacion_final
    FROM 
        inscripciones i
    JOIN                                                       
        estudiantes e ON i.id_estudiante = e.id_estudiante      
    JOIN                                                        
        cursos c ON i.id_curso = c.id_curso                     
    ORDER BY                                                    
        e.id_estudiante, c.id_curso;                            
--------------------------------------------------------------------------------------------------------------------------------------------------------------

SELECT 
    c.id_curso,
    c.nombre AS nombre_curso,
    c.codigo,
    c.creditos,
    c.semestre,
    d.id_docente,
    d.nombre_completo AS docente,
    d.anios_experiencia
FROM 
    cursos c
JOIN 
    docente d ON c.id_docente = d.id_docente
WHERE 
    d.anios_experiencia > 5
ORDER BY 
    d.anios_experiencia DESC, c.id_curso;

-------------------------------------------------------------------------------------------------------------------------------------------------------------

SELECT 
    c.id_curso,
    c.nombre AS nombre_curso,
    AVG(i.calificacion_final) AS promedio_calificacion
FROM 
    inscripciones i
JOIN 
    cursos c ON i.id_curso = c.id_curso
GROUP BY 
    c.id_curso, c.nombre
ORDER BY 
    promedio_calificacion DESC;

------------------------------------------------------------------------------------------------------------------------------------------------------------

SELECT 
    e.id_estudiante,
    e.nombre,
    COUNT(i.id_curso) AS cantidad_cursos
FROM 
    estudiantes e
JOIN 
    inscripciones i ON e.id_estudiante = i.id_estudiante
GROUP BY 
    e.id_estudiante, e.nombre
HAVING 
    COUNT(i.id_curso) > 1
ORDER BY 
    cantidad_cursos DESC;

-----------------------------------------------------------------------------------------------------------------------------------------------------------

ALTER TABLE estudiantes
ADD COLUMN estado_academico ENUM('Activo', 'Inactivo') NOT NULL DEFAULT 'Activo';

-----------------------------------------------------------------------------------------------------------------------------------------------------------

DELETE FROM docente
WHERE id_docente = 2;

VERIFICAR EL ON DELETE

SELECT * FROM cursos;
SELECT * FROM docente;

DELETE FROM docente where id_docente = 3;

SELECT * FROM docente;
SELECT * FROM cursos;

----------------------------------------------------------------------------------------------------------------------------------------------------------

SELECT 
    c.id_curso,
    c.nombre AS nombre_curso,
    COUNT(i.id_estudiante) AS cantidad_estudiantes
FROM 
    cursos c
JOIN 
    inscripciones i ON c.id_curso = i.id_curso
GROUP BY 
    c.id_curso, c.nombre
HAVING 
    COUNT(i.id_estudiante) > 2
ORDER BY 
    cantidad_estudiantes DESC;


-----------------------------------------------------------------------------------------------------------------------------------------------------------
------------------------------------------------------------3. Consultas básicas y manipulación------------------------------------------------------------

CON IN ---------------

SELECT 
    e.id_estudiante,
    e.nombre,
    AVG(i.calificacion_final) AS promedio_estudiante
FROM 
    estudiantes e
JOIN 
    inscripciones i ON e.id_estudiante = i.id_estudiante
GROUP BY 
    e.id_estudiante, e.nombre
HAVING 
    AVG(i.calificacion_final) > (
        SELECT AVG(calificacion_final) 
        FROM inscripciones
    )
ORDER BY 
    promedio_estudiante DESC;

------------------------------------------------------------------------------------------------------------------------------------------------------------

CON  EXISTS -------------

SELECT DISTINCT e.carrera
FROM estudiantes e
WHERE EXISTS (
    SELECT 1
    FROM inscripciones i
    JOIN cursos c ON i.id_curso = c.id_curso
    WHERE i.id_estudiante = e.id_estudiante
      AND c.semestre >= 2
);

------------------------------------------------------------------------------------------------------------------------------------------------------------

1.Promedio general de las calificaciones  
SELECT ROUND(AVG(calificacion_final), 2) AS promedio_general
FROM inscripciones;


2.Suma total de creditos cursados por todos los estudiantes
SELECT SUM(c.creditos) AS total_creditos
FROM inscripciones i
JOIN cursos c ON i.id_curso = c.id_curso;


3.Maxima y mínima calificación obtenida
SELECT 
    MAX(calificacion_final) AS calificacion_max,
    MIN(calificacion_final) AS calificacion_min
FROM inscripciones;


4.Cantidad de estudiantes inscritos en cada curso
SELECT 
    c.nombre AS nombre_curso,
    COUNT(i.id_estudiante) AS cantidad_estudiantes
FROM cursos c
JOIN inscripciones i ON c.id_curso = i.id_curso
GROUP BY c.id_curso, c.nombre
ORDER BY cantidad_estudiantes DESC;


5.Indicador combinado por curso
SELECT 
    c.nombre AS nombre_curso,
    COUNT(i.id_estudiante) AS cantidad_estudiantes,
    ROUND(AVG(i.calificacion_final),2) AS promedio_calificaciones,
    MAX(i.calificacion_final) AS calif_max,
    MIN(i.calificacion_final) AS calif_min,
    SUM(c.creditos) AS total_creditos
FROM cursos c
JOIN inscripciones i ON c.id_curso = i.id_curso
GROUP BY c.id_curso, c.nombre
ORDER BY promedio_calificaciones DESC;


-------------------------------------------------------------------------------------------------------------------------------------------------------------
-----------------------------------------------------------------5. Creación de una vista--------------------------------------------------------------------

CREATE VIEW vista_historial_academico AS
SELECT
    e.nombre AS nombre_estudiante,
    c.nombre AS nombre_curso,
    d.nombre_completo AS nombre_docente,
    c.semestre,
    i.calificacion_final
FROM
    inscripciones i
JOIN
    estudiantes e ON i.id_estudiante = e.id_estudiante
JOIN
    cursos c ON i.id_curso = c.id_curso
JOIN
    docente d ON c.id_docente = d.id_docente;

CONSULTAR LA VISTA 
SELECT * FROM vista_historial_academico
WHERE semestre = 2
ORDER BY nombre_estudiante;

-------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------6. Control de acceso y transacciones-----------------------------------------------------------

1.Crear el rol 
CREATE ROLE IF NOT EXISTS revisor_academico;

2.Dar permiso 
GRANT SELECT ON vista_historial_academico TO revisor_academico;

3.Asignar rol a un user
GRANT revisor_academico TO juan;

4.activar el rol cuando se conecta 
SET ROLE revisor_academico;

PERO NECESITO CREAR EL USER PARA ASIGNAR EL ROL
CREATE USER 'juan'@'localhost' IDENTIFIED BY '1234';

AHORA SI LE ASIGNO EL ROLE
GRANT revisor_academico TO 'juan'@'localhost';

COMO PROBAR:
1.PONGO EXIT 
2.mysql -u juan -p ME CONECTO Y PONGO LA CLAVE QUE DEFINI 1234 (si mostramos las bases de datos "SHOW DATABASES" no saldra la vista)
3.Pero debemos usar el comando SET ROLE revisor_academico; para obtener ese permiso o ese rol y poder ver la vista
4.SELECT * FROM vista_historial_academico;

