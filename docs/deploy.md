# Implementación

En esta sección se detallan instrucciones para poder implementar la
plataforma en producción.

Este documento apunta a *operadores y administradores de sistemas* que
necesiten poner en funcionamiento la plataforma en un servidor, y por lo
tanto se asumen conocimientos básciso de manejo de terminal, instalación de
paquetes y configuración de servicios.

En los repositorios se incluyen ejemplos de archivos de configuración cuando
es apropiado, pero siempre se deben tomar como ejemplos y ajustar según sea
necesario a su criterio.

!!! tip "Instalación en portátil"

    Si Ud. desea levantar una versión local de la plataforma en una
    computadora hogareña o portátil, se recomienda en cambio leer las
    instrucciones detalladas en la sección [Desarrollo](development.md).

!!! note "Requerimientos de SO"

    Se asume que la plataforma va a ejecutarse en un entorno con la
    distribución **Ubuntu 18.04** instalada. Para otros sistemas operativos,
    será necesario consultar la documentación correspondiente de las
    dependencias.


## Dependencias

La plataforma consiste en un backend, implementado sobre Python, y un
frontend implementado en Nodejs. Utiliza PostgreSQL como base de datos, y
Redis como cache y base de datos para la cola de trabajos.

A continuación se detallan instrucciones para instalar y configurar estas
dependencias.

### PostgreSQL y extensiones (TimescaleDB y PostGIS)

!!! note "Sólo para Ubuntu 18.04"

    Las instrucciones están pensadas para Ubuntu 18.04. Puede verificar la
    [guía de instalación de TimescaleDB][1] si está utilizando otro sistema
    operativo.

  [1]: https://docs.timescale.com/latest/getting-started/installation

Agregue el siguiente repositorio de PostgreSQL para obtener la últimas
versiones de los pquetes de PostgreSQL (esto es necesario para versiones de
Ubuntu menores a 19.04).

```bash
# `lsb_release -c -s` should return the correct codename of your OS
echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -c -s)-pgdg main" | sudo tee /etc/apt/sources.list.d/pgdg.list
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
```

Agregue el repositorio de TimescaleDB y luego instale TimescaleDB, que al
hacerlo automaticamente descargará e instalará también la versión correcta
del servidor de PostgreSQL.

```bash
# Add TimescaleDBs PPA
sudo add-apt-repository ppa:timescale/timescaledb-ppa
sudo apt-get update

# Now install appropriate package for PG version
sudo apt install timescaledb-postgresql-11
```

Es recomendable que utilice la herramienta `timescaledb-tune` para ajustar y
optimizar la base de datos para TimescaleDB.

```bash
sudo timescaledb-tune
```

Ahora, instale la extensión PostGIS 3 para esta versión de PostgreSQL.

```bash
sudo apt-get install postgresql-11-postgis-3
```

Finalmente, reinicie la instancia de PostgreSQL.

```bash
sudo service postgresql restart
```

### Python, Redis, GDAL

Instale Python y otras dependencias como GDAL y el servidor de Redis.

```bash
sudo apt-get install \
  build-essential \
  git \
  gdal-bin \
  gettext \
  libgdal-dev \
  libpq-dev \
  libproj-dev \
  python3 \
  python3-dev \
  python3-pip \
  redis-server
```


### Nginx

(...)


## Backend

### Instalación

Primero clone el repositorio del backend.

```bash
git clone https://github.com/undp/satlomas-back.git
cd satlomas-back/
```


Para instalar las dependencias correctas del backend, debe instalar el
manejador de paquetes [Pipenv](https://pipenv.pypa.io/en/latest/).

```bash
pip install --user -U pipenv
```

Luego, ejecute lo siguiente para instalar todas las dependencias necesarias.
Esto creará un entorno virtual para este proyecto.

```bash
pipenv install
```

Una vez que finalize la instalación, estará listo para configurar el backend.

### Configuración

#### Base de datos

Cree un rol de superusuario para el usuario que está utilizando actualmente.
Si sabe que va a ejecutar PostgreSQL con otros usuario, por favor reemplaze
la variable `$USER` como corresponde.

```bash
sudo -u postgres createuser -s $USER
```

Cree la base de datos.

```bash
createdb satlomas
```

Configure una contraseña para el usuario que acaba de crear. Ejecute el
siguiente comando *reemplazando* `foobar` por una contraseña única:

!!! warning "Contraseña"

    Es muy importante que defina una contraseña única y dificil de adivinar.


```bash
psql satlomas -c "ALTER USER $USER WITH PASSWORD 'foobar'"
```

Agregue las extensiones TimescaleDB y PostGIS a la base de datos creada.

```bash
psql satlomas -c "CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE"
psql satlomas -c "CREATE EXTENSION IF NOT EXISTS postgis CASCADE"
```

#### Registración en Copernicus Open Access Hub

Para que la plataforma pueda descargar los productos del satélite Sentinel-1
y Sentinel-2 de la ESA, es necesario estar registrado en [Copernicus Open
Access Hub][1].

Para registrase, ingrese [aquí][2]. Tome nota del **nombre de usuario** y
**contraseña** dado que será necesario al momento de configurar el backend.

  [1]: https://scihub.copernicus.eu/
  [2]: https://scihub.copernicus.eu/dhus/#/self-registration


#### Registración en NASA EarthData

Para descargar los productos de MODIS VI, es necesario estar registrado en
[NASA EarthData][2].

Puede registrarse ingresando [aquí][2]. Tome nota del **nombre de usuario** y
**contraseña** dado que será necesario al momento de configurar el backend.

  [1]: https://urs.earthdata.nasa.gov/
  [2]: https://urs.earthdata.nasa.gov/users/new


#### Variables de entorno

Copie `env.sample` y guarde un archivo nuevo llamada `.env`.

```bash
cp env.sample .env
```

Ahora edite `.env` según sus necesidades. El archivo `env.sample` es un
ejemplo con valores por defecto para algunas variables, pero algunas de ellas
debe completarlas. A continuación se describen las variables que deben ser
configuradas para el funcionamiento de la plataforma.

| Variable      | Descripción |
| ------------- | --------------------------------------------------- |
| `SECRET_KEY`  | String único de caracteres alfanuméricos, utilizado para firmas criptográficas. Puede generar uno [aquí][1].
| `ALLOWED_HOSTS` | Lista de *hosts* habilitados. Debería ingresar el dominio y/o IP pública del servidor |
| `DB_USER` | Usuario de la BD (debería ser el nombre del usuario actual) |
| `DB_PASSWORD` | Contraseña de la BD (la contraseña que definió antes) |
| `SCIHUB_USER` | Usuario de [SciHub][2] |
| `SCIHUB_PASS` | Contraseña de [SciHub][2] |
| `MODIS_USER`  | Usuario de [EarthData][3] |
| `MODIS_PASS`  | Contraseña de [EarthData][3] |

  [1]: https://djecrety.ir/
  [2]: #registracion-en-copernicus-open-access-hub
  [3]: #registracion-en-nasa-earthdata

### Inicialización

Ingrese al entorno virtual creado por Pipenv, y proceda con la inicialización
de la base de datos.

```bash
pipenv shell
./manage.py migrate   # Correr las migraciones
```

Luego, cree un *superusuario* para SatLomas. Este será el primer usuario
administrador, con el que podrá registrar nuevos administradores o usuarios.

```bash
./manage.py createsuperuser
```

Luego de seguir las instrucciones del comando, tendrá un usuario y contraseña
para ingresar al administrador del backend.


## Frontend

### Instalación

Primero debe instalar Node v12. Ejecute los siguientes comandos:

```bash
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt-get install -y nodejs
```

Luego, clone el repositorio del frontend:

```bash
git clone https://github.com/undp/satlomas-front.git
cd satlomas-front/
```

Instale todas las dependencias con `npm`:

```bash
npm install
```

Finalmente, ejecute el siguiente comando para generar un *build* del sitio
web:

```bash
npm run build
```
