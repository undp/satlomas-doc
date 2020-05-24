# Implementación

En esta sección se detallan instrucciones para poder implementar la
plataforma en producción.  

Este documento apunta a *operadores y administradores de sistemas* que
necesiten preparar la plataforma en un servidor, y por lo tanto se asumen
conocimientos básciso de manejo de terminal, instalación de paquetes y
configuración de servicios.  Si Ud. desea levantar una versión local de la
plataforma en una computadora hogareña o portátil, se recomienda en cambio
leer las instrucciones detalladas en la sección [Desarrollo](development.md).

En los repositorios se incluyen ejemplos de archivos de configuración cuando
es apropiado, pero siempre se deben tomar como ejemplos y ajustar si es
necesario según el entorno.

Se asume que la plataforma va a ejecutarse en un entorno con la distribución
**Ubuntu 18.04** instalada.


## Dependencias

La plataforma consiste en un backend, implementado sobre Python, y un
frontend implementado en Nodejs. Utiliza PostgreSQL como base de datos, y
Redis como cache y base de datos para la cola de trabajos.

A continuación se detallan instrucciones para instalar y configurar estas
dependencias.

### PostgreSQL y extensiones (TimescaleDB y PostGIS)

> **Nota**: Las instrucciones están pensadas para Ubuntu 18.04.  Puede
> verificar la [guía de instalación de TimescaleDB](https://docs.timescale.com/latest/getting-started/installation)
> si está utilizando otro sistema operativo.

Agregue el siguiente repositorio de PostgreSQL para obtener la últimas
versiones de los pquetes de PostgreSQL (esto es necesario para versiones de
Ubuntu menores a 19.04).

```sh
# `lsb_release -c -s` should return the correct codename of your OS
echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -c -s)-pgdg main" | sudo tee /etc/apt/sources.list.d/pgdg.list
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
```

Agregue el repositorio de TimescaleDB y luego instale TimescaleDB, que al
hacerlo automaticamente descargará e instalará también la versión correcta
del servidor de PostgreSQL.

```sh
# Add TimescaleDBs PPA
sudo add-apt-repository ppa:timescale/timescaledb-ppa
sudo apt-get update

# Now install appropriate package for PG version
sudo apt install timescaledb-postgresql-11
```

Ajuste (*tuning*) la base de datos para TimescaleDB.

```sh
sudo timescaledb-tune
```

Instale la extensión PostGIS 3 para esta versión de PostgreSQL.

```sh
sudo apt-get install postgresql-11-postgis-3
```

Reinicie la instancia de PostgreSQL:

```sh
sudo service postgresql restart
```

### Python, Redis, GDAL

Instale Python y otras dependencias como GDAL y el servidor de Redis.

```sh
sudo apt-get install \
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

## Configuración de base de datos

Cree un rol de superusuario para el usuario que está utilizando actualmente.
Si sabe que va a ejecutar PostgreSQL con otros usuario, por favor reemplaze
la variable `$USER` como corresponde.

```sh
sudo -u postgres createuser -s $USER
```

Cree la base de datos.

```sh
createdb satlomas
```

Confiure una contraseña para el usuario que acaba de crear. Por favor,
ejecute el siguiente comando *reemplazando* `foobar` por una contraseña
única, dificil de adivinar.

```sh
psql satlomas -c "ALTER USER $USER WITH PASSWORD 'foobar'"
```

Agregue las extensiones TimescaleDB y PostGIS a la base de datos creada.

```sh
psql satlomas -c "CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE"
psql satlomas -c "CREATE EXTENSION IF NOT EXISTS postgis CASCADE"
```

### Backend

(agregar instrucciones de clonar repositorio...)

Copie `env.sample` and edit it to suit your needs. See the Configuration
section above.

```sh
cp env.sample .env
```

Install Python dependencies using Pipenv. Install it first if you don't have it:

```
pip install --user -U pipenv
pipenv install
pipenv install django-anymail[mailgun] django-rest-auth[with_social] django-storages[google]
```

Then inside a pipenv shell (use `pipenv shell`) you should first do the following:

* Run migrations: `./manage.py migrate`
* Create superuser: `./manage.py createsuperuser`

### Frontend

(agregar instrucciones del repositorio frontend...)

### Nginx

(...)

### Configuración de servicios

(...)