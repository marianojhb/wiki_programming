# Conectar Base de datos en con flask / sqlalchemy

## Clases que necesitamos:
`create_engine` (de sqlalchemy)
`scoped_session` (de sqlalchemy.orm)
`session_maker` (de sqlalchymy.orm)

## Pasos

1. Creamos la engine
Para ello, obtenemos la URL de la base de datos del entorno de variable. Esto es así para no revelar información privada cuando compartimos el código del programa.

`engine = create_engine(os.getenv('DATABASE_URL'))`


2. Unimos engine con la session maker, para crearla:

`sessionmaker(bind=engine)`


3. Unimos la session creada con una sesion temporal y grabamos todo en una variable, ejemplo `db`

`db = scoped_session(sessionmaker(bind=engine)`


4. Listo

