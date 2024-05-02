# FUNCIONES Y CURSORES

# Funciones

## Crea las siguientes funciones:

### 1. Realiza las funciones suma y divide en PostgreSQL.

#### Suma

```postgresql
CREATE OR REPLACE
FUNCTION suma(a integer, b integer)
RETURNS integer 
 AS $$
    BEGIN
        RETURN a + b;
    END;
    $$ LANGUAGE plpgsql;
```

#### Divide

```postgresql
CREATE OR REPLACE
FUNCTION suma(a float, b float)
RETURNS float 
 AS $$
    BEGIN
        RETURN a / b;
    END;
    $$ LANGUAGE plpgsql;
```
<br>

### 2. Función que devuelva la lista de facturas entre dos fechas.

```postgresql
CREATE OR REPLACE
FUNCTION facturas_entre_fechas(fecha1 date, fecha2 date)
RETURNS SETOF facturas
 AS $$
    BEGIN
        RETURN QUERY SELECT * 
        FROM facturas 
        WHERE fecha BETWEEN fecha1 AND fecha2;
    END;
    $$ LANGUAGE plpgsql;
```
<br>

### 3. Función llamada Login que indique si un usuario es válido tras introducir su id y su contraseña.

```postgresql
CREATE OR REPLACE
FUNCTION login(id integer, password text)
RETURNS usuarios
 AS $$
    BEGIN
        RETURN SELECT * 
        FROM usuarios 
        WHERE id = id AND password = password;
    END;
    $$ LANGUAGE plpgsql;
```
<br>

### 4. Función que calcule la letra del DNI.

```postgresql
CREATE OR REPLACE
FUNCTION calcular_letra_DNI(dni integer)
RETURNS char
 AS $$
    DECLARE
        letras char[] = ARRAY['T', 'R', 'W', 'A', 'G', 'M', 'Y', 'F', 'P', 'D', 'X', 'B', 'N', 'J', 'Z', 'S', 'Q', 'V', 'H', 'L', 'C', 'K', 'E'];
    BEGIN
        RETURN letras[dni % 23 + 1];
    END;
    $$ LANGUAGE plpgsql;
```
<br>

### 5. Una función que devuelva la concatenación de 2 cadenas que se le pasarán como parámetro.

```postgresql
CREATE OR REPLACE
FUNCTION concatenacion_cadenas(cadena1 text, cadena2 text)
RETURNS text
 AS $$
    BEGIN
        RETURN cadena1 || cadena2;
    END;
    $$ LANGUAGE plpgsql;
```
<br><br>




# CURSORES

## Realiza los siguientes ejercicios de cursores:

### 1. Incorpora FOR UPDATE/SHARE, realiza una actualización en el cursor y comprueba que ahora el cursor es sensitive.

```postgresql
CREATE OR REPLACE
FUNCTION actualizar_salario()
RETURNS VOID
 AS $$
    DECLARE
        emp_record empleados%ROWTYPE;
        emp_cursor CURSOR FOR SELECT * FROM empleados FOR UPDATE;
    BEGIN
        OPEN emp_cursor;
        LOOP
            FETCH emp_cursor INTO emp_record;
            EXIT WHEN NOT FOUND;
            UPDATE empleados SET salario = salario * 1.1 WHERE CURRENT OF emp_cursor;
        END LOOP;
        CLOSE emp_cursor;
    END;
    $$ LANGUAGE plpgsql;
```
<br>

### 2. Piensa en un caso en el que sea necesario utilizar un cursor. Intenta programarlo. 

```postgresql
CREATE OR REPLACE
FUNCTION calcular_ventas_por_cliente()
RETURNS VOID
 AS $$
    DECLARE
        cliente_id_temporal INT;
        total_venta NUMERIC;
        cursor_pedidos CURSOR FOR SELECT cliente_id, SUM(monto) AS total_ventas FROM pedidos GROUP BY cliente_id;
    BEGIN
        OPEN cursor_pedidos;
        LOOP
            FETCH cursor_pedidos INTO cliente_id_temporal, total_venta;
            EXIT WHEN NOT FOUND;
            RAISE NOTICE 'Cliente % - Total de ventas: %', cliente_id_temporal, total_venta;
        END LOOP;
        CLOSE cursor_pedidos;
    END;
    $$ LANGUAGE plpgsql;
```
<br>

### 3. Investiga si existe algún tipo de estructura de bucle alternativa que permita el recorrido de un cursor.

```postgresql
CREATE OR REPLACE
FUNCTION calcular_ventas_por_cliente()
RETURNS VOID
 AS $$
    DECLARE
        cliente_id_temporal INT;
        total_venta NUMERIC;
    BEGIN
        FOR cliente_id_temporal, total_venta IN SELECT cliente_id, SUM(monto) AS total_ventas FROM pedidos GROUP BY cliente_id
        LOOP
            RAISE NOTICE 'Cliente % - Total de ventas: %', cliente_id_temporal, total_venta;
        END LOOP;
    END;
    $$ LANGUAGE plpgsql;
```
