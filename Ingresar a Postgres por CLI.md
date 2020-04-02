# Ingreso a Postgres por terminal (CLI)

Postgres por defecto crea el usuario "`postgres`" y la clave "`admin`", y el puerto "`5433`". Utilizaremos estos datos para los ejemplos.

El comando para entrar a Postgres es 
```
$ psql
```

Si utilizamos el comando así a secas, sin modificadores, y sin configurar <em>variables de entorno</em>, normalmente recibiremos un error.

```
$ psql
psql: error: could not connect to server: FATAL:  database "xxxx" does not exist
```
Donde "xxxx" es nuestro usuario del sistema. 

<br>
Para poder ingresar a psql tenemos varias opciones:   

1. [A través de la línea de comandos directamente](#1-a-través-de-la-línea-de-comandos-directamente)
2. [A través de variables de entorno](#2-a-través-de-variables-de-entorno)
3. [A través del archivo `.pgpass`](#3-a-través-del-archivo-pgpass)

<br>


---

### 1. A través de la línea de comandos directamente  

  

Es necesario entonces usar modificadores:   
```
$ psql -U postgres -h localhost -p 5433
Password for user postgres: 
```
Donde:  
`-U` : **U**suario (`postgres`)  
`-h` : **h**ost (`localhost` o `127.0.0.1`)  
`-p` : **p**uerto (`5433`)  

Observamos que a diferencia de mysql, no podemos incluir la contraseña y entonces nos la pide.

También podríamos especificar la base de datos:
```
$ psql -U postgres -h localhost -p 5433 -d libros
Password for user postgres: 
``` 
`-d` : la **d**atabase (`libros`).  
<br>

---

### 2. A través de variables de entorno

Usando variables de entorno evitamos tener que escribir los datos cada vez que ingresamos a postgres.  
Las principales variables de entorno que utiliza postgres son:  
  
> `PUSER`: usuario  
> `PGPASSWORD`: constraseña  
> `PGHOST`: host  
> `PGPORT`: puerto  
> `PGDATABASE`: nombre de la base de datos  

Naturalmente podemos consultar las variables de entorno con `printenv` o `echo`. Ejemplos:

```
$ printenv PGUSER
```

```bash
$ echo $PGUSER
```

Seteamos las variables de entorno con el comando `export`.

```bash
$ export PGUSERNAME="postgres"
```
Es muy importante no dejar espacios alrededor del signo `=` porque si no el lenguaje script de bash no lo toma.


Las variables de entorno se pueden guardar en el directorio `home`, dentro del archivo `.bash_profile` y quedará seteado toda vez que se reinicie la terminal.  


```bash
...
export PGUSER='postgres'
export PGPASSWORD='admin'
export PGHOST='localhost'
export PGPORT='5433'
export PGDATABASE='libros'
...
```
Pero si tenemos más de una base de datos, lo conveniente es guardar un archivo `.env` dentro del directorio del proyecto, y llamarlo: 
```bash
$ source .env
```
 El archivo `.env` debe tener seteado el permiso 600 (user RW, group y other sin permiso). Y, por supuesto, agregarlo en `.gitignore` si utilizamos el sistema de versionado Git, para no revelar estos datos.

***Si seteamos las variables de entorno, con solo escribir `psql` en la línea de comandos ya podemos ingresar.***

```
$ psql
psql (12.1)
Type "help" for help.

postgres=# 
```

También podemos setear una la variable de entorno completa para usar en nuestros proyectos de esta manera.
```bash
...
DATABASE_URL='postgres://postgres:admin@elocalhost:5433/'
...
```
Esta opción no es para ingresar a psql por línea de comando, pero convenía recordar como era la URL.  

Por ejemplo en Python, la llamamos de esta manera:
```python
import os
DATABASE_URL = os.getenv("DATABASE_URL")
```
o
```python
import os
DATABASE_URL = os.environ.get("DATABASE_URL")  
```
<br>

---
### 3. A través del archivo `.pgpass`

Esta manera, en mi opinión, no tiene mucha utilidad.  
Funciona de esta manera:

1. Creamos el archivo `.pgpass` y lo guardamos en el directorio `home`.
    ```
    $ touch ~/.pgpass 
    ```
2. Editamos el archivo .pagpass con una cadena con el formato `hostname:port:database:username:password`:
    ```
    $ cat ~/.pgpass
    localhost:5433:libros:postgres:admin
    ```
    Presionamos `CTRL+C` para guardar.   

3. Setamos el permiso 600.
    ```
    $ chmod 600 ~/.pgpass
    ```
4. Agregamos a `.bash_profile` o sino a `.env` del proyecto:
    ```bash
    ...
    export PGPASSFILE="~/.pgpass"
    ...
    ```

Por qué no tiene mucha utilidad?   
*Porque cada vez que necesitamos usar la base de datos, tenemos que utilizar toda la secuencia salvo la contraseña.*   
Esta solución funciona comparando `usuario`, `host`, y `puerto` con la cadena creada en `.pgpass`, y luego busca la contraseña para no tener que escribirla.
