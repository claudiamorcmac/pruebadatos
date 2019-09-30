# pruebadatos

-----En SQL Developer, Creamos la tabla datoscuentas en la BD baseprueba para el programa

CREATE TABLE datoscuentas (
cuenta INTEGER CONSTRAINT personas_pk PRIMARY KEY, 
monto INTEGER CONSTRAINT personas_monto_nn NOT NULL, 
fecha DATE CONSTRAINT personas_fecha_nn NOT NULL, 
operacion INTEGER  CONSTRAINT personas_operacion_nn NOT NULL CONSTRAINT personas_operacion_cc CHECK (operacion IN (0, 1) ) ) ;

-------Creamos Archivo carga_archivo.ctl


OPTIONS (SKIP=1)
LOAD DATA
INFILE 'C:\Examen_Dev_JR\Ejercicio.csv'
INTO TABLE datoscuentas
INSERT
FIELDS TERMINATED BY ';'
TRAILING NULLCOLS
(cuenta,monto,operacion,fecha)


-----Realizamos la carga de datos de la siguiente forma:

sqlldr userid=claudia/baseprueba control= C:\Examen_Dev_JR\carga_archivo.ctl data=C:\Examen_Dev_JR\Ejercicio.csv

log=C:\Examen_Dev_JR\datos.log



-----*****Otra Forma de realizar la carga de datos :*****
-----En SQL Developer nos posicionamos en la tabla creada, damos click derecho y damos "importar datos",
-----aparece una pantalla en la parte superior con la opción: "Archivo de Datos de Importación", damos
-----click en el boton "Examinar", le damos, Abrir y nos aparece el contenido del archivo Ejercicio.csv y
-----le indicamos que el método de importación es insertar, despues, en la parte Inferior Derecha dar en el
-----botón "Siguiente". En la pantalla aparece las columnas seleccionadas. Verificamis que se encuentren 
-----todos los campos y damos clck en el botón siguiente, damos siguiente otra vez y nuevamente click en terminar.



----- *************** 
----- Programa 
----- ***************


DECLARE

   COD_MES NUMBER(2) NOT NULL:=&MES;  --- SOLICITA A USUARIO INDICAR EL MES
   CARGOS NUMBER(15);  ----- SI OPERACION ES 0
   ABONOS NUMBER (15); ----- SI OPERACION ES 1
   TOTAL  NUMBER (15); ----- RESULTADO DE ABONOS - CARGOS
    
BEGIN 
	SELECT SUM(monto) INTO ABONOS FROM datoscuentas WHERE to_char(fecha, 'MM')  =COD_MES
	AND operacion=1;

	SELECT SUM(monto) INTO CARGOS FROM datoscuentas WHERE to_char(fecha, 'MM')  =COD_MES
	AND operacion=0;

	TOTAL:= ABONOS - CARGOS;

	DBMS_OUTPUT.PUT_LINE("Mes : "||TO_CHAR(COD_MES));
	DBMS_OUTPUT.PUT_LINE("Total Cargos : "||TO_CHAR(CARGOS));
	DBMS_OUTPUT.PUT_LINE("Total Abonos : "||TO_CHAR(ABONOS));
	DBMS_OUTPUT.PUT_LINE("Total Total : "||TO_CHAR(TOTAL));

	set echo off
	set feedback off
	set pagesize 0
	set colsep ";"
 
	Spool C:\Examen_Dev_JR\Archivo_de_salida.csv;
  	  select cuenta,sum(monto) from datoscuentas
		WHERE to_char(fecha, 'MM')  =COD_MES;
	Spool off;
   
	set echo off
	set feedback off
	set pagesize 0
	set colsep ";"
 
	Spool C:\Examen_Dev_JR\Archivo_de_salida_Punto_Extra.csv;
   	 select cuenta,sum(monto) from datoscuentas
   	 WHERE to_char(fecha, 'MM')  =COD_MES
   	 order by cuenta;
	Spool off;
 

     TOTAL:= NULL;
     ABONOS:= NULL;
     CARGOS:= NULL;
     COD_MES:= NULL;
END;











