# Uso

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
Observación Terrestre, como el mapa de Cobertura Verde o el mapa de Cobertura
de Loma Perdida, y por otro lado, al Mapa y el Dashboard del módulo de
Estaciones Meteorológicas.

![Captura de inicio](img/home.png)

## Observación terrestre

### Cobertura Verde

![](img/vi1.png)
![](img/vi2.png)
![](img/vi3.png)
![](img/vi4.png)
![](img/vi5.png)
![](img/vi6.png)
![](img/vi7.png)

### Cobertura de Loma Perdida

![](img/lomas1.png)

### Detección de Cambios

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