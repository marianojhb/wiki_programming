# Abrir un archivo CSV en python

## Pasos

1. Importamos la biblioteca CSV 

`import csv`


2. Abrimos el archivo y lo guardamos en una variable

`f = open('books.csv')`


3. Usamos el metodo reader de la clase csv

`fr = csv.reader(f)`

<em> Ahora ya tenemos cargado el archivo en la variable fr y podemos tomar todos los registros con fetchall o un registro con fetchone, etc</em>


