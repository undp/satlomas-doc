# Guía de Uso

En esta sección se describen las diferentes vistas que conforman el frontend
de la plataforma, y de qué manera se pueden utilizar las herramientas
provistas para visualizar y analizar los resultados de los procesos
automáticos aplicados sobre imágenes satelitales y de las estaciones
meteorológicas.

Este texto está dirigido tanto para usuarios anónimos, como para usuarios
registrados y aquellos con el rol de administrador.

## Inicio

En la página de inicio se listan diferentes componentes que forman parte de
la plataforma. Desde aquí se puede acceder a los mapas del módulo de
Observación Terrestre, como el mapa de **Cobertura Verde** o el mapa de **Cobertura
de Loma Perdida**, y por otro lado, al Mapa y el **Dashboard** del módulo de
Estaciones Meteorológicas.

![Captura de inicio](img/home.png)

## Observación terrestre

### Cobertura Verde

El mapa de cobertura verde presenta la capa de cobertura de vegetación del
ecosistema de las Lomas, basada en el producto de MODIS de índices de
vegetación (MOD13Q1).

![](img/vi1.png)

En la esquina superior izquierda se encuentra el control de Filtro, que
permite filtrar los resultados de la serie de tiempo para un ámbito y un
rango de fechas específico:

![](img/vi2.png)

Desde el selector de Tipo de Ámbito se puede seleccionar un tipo de ámbito.
Al seleccionar uno, en el mapa se actualizará la capa de ámbitos del tipo
seleccionado.

![](img/vi4.png)

También se puede ajustar la fecha de comienzo y fin de la serie de tiempo.
Por defecto, se toma la primer fecha y última fecha con datos cargados en la
base de datos.

![](img/vi5.png)

Por defecto se visualiza la capa NDVI, pero también es posible visualizar en
simultaneo una máscara de vegetación, resultado de la umbralización de la
capa NDVI, (*Vegetation mask*) y una máscara de nubes (*Cloud Mask*),
extraída del mismo producto de MODIS. Estas capas se pueden activar o
desactivar haciendo clic en el ícono de Capas en la esquina inferior
izquierda, y en cada casillero.

![](img/vi6.png)

También es posible ajustar la opacidad de cada capa ajustando el control
deslizante al lado del nombre de la capa.

Debajo del ícono de Capas, se encuentra el control de Selección de Períodos,
que permite seleccionar una fecha en particular, para visualizar las capas de
ese período en el mapa.

![](img/vi7.png)

!!! note "Nota"

    Esta selección de fecha sólo afecta a las capas visualizadas en el mapa,
    no a la serie de tiempo. Para ajustar las fechas de la serie de tiempo,
    debe hacerlo desde el control de Filtro.

### Cobertura de Loma Perdida

La vista de cobertura de Loma Perdida funciona de manera similar al mapa de
Cobertura Verde.  Las capas disponibles en este caso son:

* *Loss mask*: Máscara de loma perdida. Resultado del producto de clasificación
  de cobertura de loma perdida.
* *Sentinel-1*: Imagen en color falso del mosaico de Sentinel-1 para el período
  actual. Las bandas utilizadas fueron VV, VH y la proporción VV/VH como
  rojo, verde y azul respectivamente.
* *Sentinel-2*: Imagen en color verdadero del mosaico de Sentinel-2 para el
  período actual.

![](img/lomas1.png)

### Detección de Cambios

En esta vista se visualiza el producto del modelo de detección de objetos
para escenas del satélite PeruSat-1.

![](img/objects1.png)

## Estaciones Meteorológicas

### Mapa

![](img/stations-map1.png)
![](img/stations-map2.png)
![](img/stations-map3.png)

### Dashboard

![](img/stations-dashboard1.png)
![](img/stations-dashboard2.png)
![](img/stations-dashboard3.png)
![](img/stations-dashboard4.png)
![](img/stations-data1.png)

## Usuarios

### Reglas

#### Reglas de Parámetro

![](img/admin-parameter-rule1.png)
![](img/admin-parameter-rule2.png)

#### Reglas de Tipo de Ámbito

![](img/admin-scope-type-rule1.png)
![](img/admin-scope-type-rule2.png)

#### Reglas de Ámbito

![](img/admin-scope-rule1.png)
![](img/admin-scope-rule2.png)

### Alertas

#### Diagrama de verificación de regla

```mermaid
graph TB;
    inicio([Inicio])-->esAbs{{es absoluto?}};
    esAbs-- sí -->valAbs[Toma como valor <br />la medicion actual];
    esAbs-- no -->valRel[Toma como valor <br />la diferencia <br />entre medicion actual y <br />medicion anterior];
    valAbs-->esValMenorMin{{es valor menor <br />al mínimo valido?}};
    valRel-->esValMenorMin;
    esValMenorMin-- sí -->crear[Crear alerta];
    esValMenorMin-- no -->esValMayorMax[es valor mayor <br />al máximo válido?];
    esValMayorMax-- sí -->crear;
    esValMayorMax-- no -->fin([Fin]);
    crear-->debeNotificar{{debe notificar por mail?}};
    debeNotificar-- sí -->notificar[Enviar notificación por mail];
    debeNotificar-- no -->fin;
    notificar-->fin;
```

#### Diagrama para reglas de parámetro

```mermaid
graph LR;
    inicio([Inicio])-->todas{{en todas las estaciones?}};
    todas-- sí -->fin([Fin]);
    todas-- no -->filtrar[Filtra por estación <br />definida en regla];
    filtrar-->fin;
```

#### Diagrama para reglas de tipo de ámbito

```mermaid
graph LR;
    inicio([Inicio])-->todos{{en todos los tipos de ámbito?}};
    todos-- sí -->fin([Fin]);
    todos-- no -->filtrar[Filtra por tipo de ámbito <br />definido en regla];
    filtrar-->fin;
```

#### Diagrama para reglas de ámbito

```mermaid
graph LR;
    inicio([Inicio])-->todos{{en todos los ámbitos del tipo?}};
    todos-- sí -->fin([Fin]);
    todos-- no -->filtrar[Filtra por ámbito <br />definido en regla];
    filtrar-->fin;
```

![](img/admin-alerts1.png)

### Imágenes

![](img/admin-images1.png)
![](img/admin-images2.png)

### Perfil

![](img/admin-profile1.png)

## Administradores

### Panel de administración

![](img/backend-admin1.png)

### Usuarios

![](img/backend-admin2.png)
![](img/backend-admin3.png)
![](img/backend-admin4.png)

### Ámbitos

![](img/backend-admin5.png)
![](img/backend-admin6.png)

### Estaciones meteorológicas

![](img/backend-stations1.png)
![](img/backend-stations2.png)

### Auditoría

![](img/backend-auditlog1.png)