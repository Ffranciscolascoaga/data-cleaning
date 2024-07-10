 # limpieza de datos con SQL
 
create database if not exists clean;

use clean;

select * from limpieza limit 10;

# para evitar copiar el select * from limpieza, creo un STORE PROCEDURE. 

select * from limpieza;
DELIMITER // 
CREATE PROCEDURE limp()
BEGIN
   select * from limpieza;
END//
DELIMITER;

CALL limp(); #cada vez que haga este llamado me va a salir el select * from limpieza;

#UNA DE LAS TAREAS QUE DEBO REALIZAR ES RENOMBRAR LOS NOMBRE DE LAS COLUMNAS

#cambio el ID
ALTER TABLE limpieza CHANGE COLUMN `ï»¿Id?empleado` ID_emp varchar(20) null;
#cambio GENERO 
ALTER TABLE limpieza CHANGE COLUMN `gÃ©nero` Genero varchar(20) null;

#Me fijo si hay valores duplicados, en ese caso los remuevo --  USO GROUP BY Y HAVING


SELECT ID_emp, count(*) as cantidad_duplicados
from limpieza
group by id_emp
having count(*) >1;

#cuento cuanto registros hay duplicados

select count(*) as cantidad_duplicados
from (
SELECT ID_emp, count(*) as cantidad_duplicados
from limpieza
group by id_emp
having count(*) >1 
) as subquery; 

#ELIMINO DUPLICADOS: 1) renombro la tabla, 2) creo una tabla temporal, 3) convierto la tabla temporal en permanente

rename table limpieza to conduplicados;

create temporary table temp_limpieza as 
select distinct * from conduplicados;

select count(*) as original from conduplicados;
#en esta tabla me arroja ('22223') registros
select count(*) as original from temp_limpieza;
#esta consulta me arroja ('22214'), siendo esta la que esta sin duplicados y la voy a pasar de forma permanente

create table limpieza as select * from temp_limpieza;

call limp();

select count(*) from limpieza;

# Elimino la tabla con duplicados

drop table conduplicados;

set sql_safe_updates = 0;
set sql_safe_updates = 1;

alter table limpieza change column `Apellido` last_name varchar(50) null;

alter table limpieza change column `star_date`start_date varchar(50) null; 

alter table limpieza change column `Genero` Gender varchar (50) null;

#Propiedades de la tabla
 DESCRIBE limpieza;
 
 # las columnas en formato texto las paso a formato varchar, y luego las columnas que van con fecha que estan en texto las paso a formato fecha
 
 call limp();
 
 #TRIM Funciona para eliminar los espacios que estan antes o despues del texto
 
select name from limpieza
where length(name) - length(trim(name)) > 0;

select name, trim(name) as name 
from limpieza
where length(name) - length(trim(name)) > 0;

update limpieza set name = trim(name)
where length(name) - length(trim(name)) > 0;


select last_name from limpieza
where length(last_name) - length(trim(last_name)) > 0;

select last_name, trim(last_name) as last_name 
from limpieza
where length(last_name) - length(trim(last_name)) > 0;

update limpieza set last_name = trim(last_name)
where length(last_name) - length(trim(last_name)) > 0; 

# VOY A INTRODUCIR ESPACIOS A PROPOSITO PARA LUEGO REMOVERLOS

update limpieza set area = replace(area," ","    ");
call limp();

# para identificar si hay dos o mas espacios entre las palabras

select area from limpieza where area regexp '\\s{2,}';  #el 2 indica la cantidad de espacios

select area, trim(regexp_replace(area,'\\s+',' ')) as ensayo from limpieza;

#actualizo mi tabla de manera definitiva

update limpieza set area = trim(regexp_replace(area,'\\s+',' '));

call limp();

# todo mi set esta en ingles, pero genero me aparece en español. por lo tanto lo voy a pasar a ingles.

select gender,
case
   when gender = "hombre" then "male"
   when gender = "mujer" then "female"
   else "other"
end as gender1 from limpieza;

update limpieza set gender = CASE
 when gender = "hombre" then "male"
   when gender = "mujer" then "female"
   else "other"
END;

call limp();

# LA COLUMNA TYPE MUESTRA VALORES BOOLEANOS, ALGUIEN EXTERNO QUE NO SABE NADA ACERCA DE ESTA BASE DE DATOS ESTA COLUMNA LE VA A RESULTAR IRRELEVANTE
# TYPE HACE REFERENCIA AL TIPO DE CONTRATO QUE TIENE EL EMPLEADO
# O = TIENE UN TRABAJO HIBRIDO
# 1 = TIENE TRABAJO DE TIPO REMOTO

DESCRIBE limpieza;

#type esta en formato int, lo voy a pasar a texto.

alter table limpieza modify column type text;

select type,
case
   when type = 1 then "Remote"
   when type = 0 then "Hybrid"
   else "other"
end as ejemplo 
from limpieza;

update limpieza 
set type =
case
   when type = 1 then "Remote"
   when type = 0 then "Hybrid"
   else "other"
end;

call limp();

describe limpieza;

#dar formato de numer a un texto, ya que salario esta en text y es int
#Ensayo
select salary, 
cast(trim(replace(replace(salary, "$",""),",","")) as decimal (15,2)) as salary1 from limpieza;

update limpieza set salary = cast(trim(replace(replace(salary, "$",""),",","")) as decimal (15,2));
call limp();

alter table limpieza modify column salary int null;

describe limpieza;

#voy a trabjar con las fechas, birth date, start date y finish date estan en text y tienen que ir en formato fecha.

select birth_date from limpieza;
/*lo primero que hago es usar la funcion str_to_date que lo que hace es convertir el valor de una cadena en formato mes año a un formato de fecha aceptado por sql
la otra funcion es date_format que formatea el objeto de fecha en una cadena con el formato año, mes, dia.*/

select birth_date, case
when birth_date like "%/%" then date_format(str_to_date(birth_date,"%m/%d/%y"),"%Y-%m-%d")
when birth_date like "%-%" then date_format(str_to_date(birth_date,"%m-%d-%y"),"%Y-%m-%d")
else null
end as new_birth_date 
from limpieza;

UPDATE limpieza
SET birth_date = 
    CASE 
        WHEN birth_date LIKE "%/%" THEN DATE_FORMAT(STR_TO_DATE(birth_date, "%m/%d/%Y"), "%Y-%m-%d")
        WHEN birth_date LIKE "%-%" THEN DATE_FORMAT(STR_TO_DATE(birth_date, "%m-%d-%Y"), "%Y-%m-%d")
        ELSE NULL 
    END;

select brith_date from limpieza
where birth_date = "06/04/1991";

call limp();

alter table limpieza modify column birth_date date;

#hago lo mismo para las columnas START_DATE Y FINISH_DATE

select start_date, case
when start_date like "%/%" then date_format(str_to_date(start_date,"%m/%d/%y"),"%Y-%m-%d")
when start_date like "%-%" then date_format(str_to_date(start_date,"%m-%d-%y"),"%Y-%m-%d")
else null
end as new_start_date
from limpieza;

update limpieza 
set start_date = 
    case 
        when start_date like "%/%" then date_format(str_to_date(start_date,"%m/%d/%y"),"%Y-%m-%d")
        when start_date like "%-%" then date_format(str_to_date(start_date,"%m-%d-%y"),"%Y-%m-%d")
	    else null
    END;
UPDATE limpieza
SET start_date = 
    CASE 
        WHEN start_date LIKE "%/%" THEN DATE_FORMAT(STR_TO_DATE(start_date, "%m/%d/%Y"), "%Y-%m-%d")
        WHEN start_date LIKE "%-%" THEN DATE_FORMAT(STR_TO_DATE(start_date, "%m-%d-%Y"), "%Y-%m-%d")
        ELSE NULL 
    END;
    
    alter table limpieza modify column start_date date;
    
    describe limpieza;
    


select finish_date, STR_TO_DATE(finish_date, '%Y-%m-%d %H:%i:%s') as fecha from limpieza;

select finish_date, date_format(STR_TO_DATE(finish_date, '%Y-%m-%d %H:%i:%s'),'%Y-%m-%d') as fecha from limpieza;

update limpieza set finish_date = str_to_date(finish_date, '%Y-%m-%d %H:%i:%s UTC')
where finish_date <> '';

alter table limpieza
    add column fecha date,
    add column hora time;
    
call limp();



update limpieza
set fecha = date(finish_date),
    hora = time(finish_date)
where finish_date is not null and finish_date <> '';

update limpieza set finish_date = null where finish_date = '';

alter table limpieza modify column finish_date datetime;

describe limpieza;

alter table limpieza add column age int;

call limp();

update limpieza 
set age = timestampdiff(year, birth_date, curdate());  #calculo la edad de los empleados

select avg(salary) from limpieza;  #el salario promedio es de 125.230

select name, age, salary  from limpieza
where salary >= 125230;   #empleados que cobran por encima de la media

select max(salary) from limpieza;


use clean;

select * from limpieza;

# SALARIO PROMEDIO

select name, last_name, area, salary from limpieza;

# SALARIOS POR ENCIMA DE LA MEDIA

SELECT last_name, Name, area, salary
FROM limpieza
WHERE salary >= (select avg(salary) from limpieza);

# CANTIDAD DE EMPLEADOS QUE GANAN MAS DE LA MEDIA
select count(*) from limpieza
where salary >= (select avg(salary) from limpieza);

# SON 11177 LOS QUE GANAN MAS QUE LA MEDIA

call limp();
# EMPLEADO MAS VIEJO

select name, last_name,birth_date, age, area, salary
from limpieza
where birth_date = (select min(birth_date) from limpieza);

# EMPLEADO MAS JOVENES

select name, last_name, birth_date, age, area, salary
from limpieza
where birth_date = (select max(birth_date) from limpieza);

# TOTAL DE MUJERES = 10321
select count(*) from limpieza
where gender = "Female";

# TOTAL DE HOMBRES = 11893
select count(*) from limpieza
where gender = "male";

select name, last_name, area, salary, age
from limpieza
where gender = "Female" and salary > (select max(salary)
from limpieza
where gender = "male");

select area from limpieza;

call limp(); 
# EMPLEADOS HIBRIDOS 
select name, last_name, area, salary, age, type
from limpieza
where type = "Hybrid"; 
# LOS CUENTO. TOTAL = 1111
select count(*) from limpieza
where type = "Hybrid";

# EMPLEADOS REMOTO
select name, last_name, area, salary, age, type
from limpieza
where type = "Remote"; 

#TOTAL  = 11103
select count(*) from limpieza
where type = "Remote";

select area, name, salary from limpieza
where salary = (select max(salary) from limpieza);

call limp();
