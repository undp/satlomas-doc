# SatLomas - Documentación

Este repositorio contiene documentación acerca de SatLomas, la plataforma de
monitoreo satelital del ecosistema de las Lomas, desarrollada por [Dymaxion
Labs](https://dymaxionlabs.com) para el proyecto Conservación, gestión y
rehabilitación de los ecosistemas de lomas en Lima (EbA Lomas), que es
ejecutado por el Programa de las Naciones Unidas para el Desarrollo (Pnud),
bajo la dirección del Servicio Nacional de Áreas Naturales Protegidas por
el Estado (Sernanp) y el financiamiento del Fondo Mundial del Ambiente (GEF).

La documentación acutalizada se puede acceder desde:
https://undp.github.io/satlomas-doc/


## Instalación

Para poder regenerar y visualizar la documentación localmente, se necesita
instalar MkDocs.  Primero es recomendable crear un entorno virtual y activarlo:

```
virtualenv -p python3 .venv/
source .venv/bin/activate
```

Luego, ejecutar lo siguiente para instalar MkDocs y el theme:

```
pip install -r requirements.txt
```


## Uso

Para levantar un servidor de desarrollo y visualizar la documentación desde el
navegador, ejecutar:

```
mkdocs serve
```

Para publicar la última versión a GitHub Pages:

```
mkdocs gh-deploy
```


## Licencia

Ver [LICENSE.txt](LICENSE.txt) para más información.
