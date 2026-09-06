# MediStock - Semana 5 - Sesion 2

## Dockerizacion del backend y base de datos

**Proyecto:** MediStock  
**Responsable:** Julian Alberto Trujillo Bonilla  
**Programa:** Ingenieria de Sistemas  
**Asignatura:** Sistemas Distribuidos  
**Rama de trabajo:** feature/week5-session2-dockerized-backend-dev  
**Arquitectura definida:** Monolito modular  
**Tecnologia del servidor de aplicacion:** Java con Spring Boot  
**Base de datos:** PostgreSQL  
**Contenedores:** Docker y Docker Compose  

## 1. Objetivo de la sesion

Dockerizar el backend de MediStock y preparar un entorno con Docker Compose para ejecutar en conjunto la aplicacion Spring Boot y la base de datos PostgreSQL.

Esta sesion permite que otro desarrollador pueda levantar el entorno completo sin configurar manualmente PostgreSQL en su equipo.

## 2. Punto de partida

La Semana 5 - Sesion 1 agrego persistencia JPA para:

- medicamentos;
- proveedores.

Hasta esa fase, PostgreSQL podia ejecutarse manualmente y el backend se iniciaba desde Maven. En esta sesion se agrega el soporte para ejecutar ambos componentes mediante Docker.

## 3. Alcance funcional

El alcance de esta sesion incluye:

- crear `Dockerfile` para el backend;
- crear `.dockerignore`;
- crear `compose.yaml`;
- levantar PostgreSQL desde Docker Compose;
- construir la imagen del backend;
- ejecutar el backend en contenedor;
- conectar el backend con PostgreSQL usando la red interna de Docker Compose;
- exponer PostgreSQL en el puerto `5433` para pgAdmin;
- exponer el backend en el puerto `8080` para Postman;
- documentar comandos de ejecucion.

## 4. Servicios definidos

Docker Compose define dos servicios principales:

| Servicio | Responsabilidad | Puerto host |
| --- | --- | --- |
| `postgres` | Base de datos PostgreSQL de MediStock | `5433` |
| `backend` | Aplicacion Spring Boot de MediStock | `8080` |

## 5. Dockerfile del backend

El backend se construye usando una imagen de Maven con Java 17 y luego se ejecuta con una imagen JRE.

Flujo:

```text
codigo fuente -> mvn package -> app.jar -> contenedor backend
```

El contenedor expone el puerto:

```text
8080
```

## 6. Configuracion de PostgreSQL

El servicio `postgres` usa:

```text
POSTGRES_DB=medistock
POSTGRES_USER=medistock
POSTGRES_PASSWORD=medistock
```

Mapeo de puertos:

```text
localhost:5433 -> postgres:5432
```

Se usa `5433` en el equipo local para evitar conflictos con instalaciones locales de PostgreSQL que normalmente usan `5432`.

## 7. Conexion interna del backend

Dentro de Docker Compose, el backend no se conecta a `localhost`.

El backend usa el nombre del servicio:

```text
jdbc:postgresql://postgres:5432/medistock
```

Esto funciona porque Docker Compose crea una red interna donde el servicio `backend` puede resolver el servicio `postgres` por nombre.

## 8. Comandos de ejecucion

Desde la carpeta `MediStock`:

```powershell
docker compose up --build
```

Para ejecutar en segundo plano:

```powershell
docker compose up --build -d
```

Para detener el entorno:

```powershell
docker compose down
```

Para detener y eliminar tambien el volumen de datos:

```powershell
docker compose down -v
```

## 9. Consideracion sobre contenedores manuales

Si previamente existe un contenedor creado manualmente con el nombre:

```text
medistock-postgres
```

puede existir conflicto con Docker Compose.

En ese caso se puede eliminar el contenedor manual antes de usar Compose:

```powershell
docker stop medistock-postgres
docker rm medistock-postgres
```

Luego ejecutar:

```powershell
docker compose up --build
```

## 10. Pruebas manuales con Postman

Con los contenedores activos, se pueden probar:

```text
GET  http://localhost:8080/health
POST http://localhost:8080/api/medications
GET  http://localhost:8080/api/medications
POST http://localhost:8080/api/suppliers
GET  http://localhost:8080/api/suppliers
```

## 11. Visualizacion en pgAdmin 4

Para conectarse desde pgAdmin 4:

| Campo | Valor |
| --- | --- |
| Name | `MediStock Docker` |
| Host name/address | `localhost` |
| Port | `5433` |
| Maintenance database | `medistock` |
| Username | `medistock` |
| Password | `medistock` |

Tablas esperadas:

```text
medications
suppliers
```

## 12. Pruebas automatizadas

Las pruebas automatizadas del backend se siguen ejecutando con:

```powershell
.\mvnw.cmd test
```

Las pruebas usan H2 en memoria para no depender obligatoriamente de Docker durante la validacion automatizada.

## 13. Validacion realizada

Durante la sesion se valido:

- configuracion de Docker Compose con `docker compose config`;
- construccion de la imagen del backend con `docker compose build backend`;
- ejecucion del entorno completo con `docker compose up --build -d`;
- estado saludable de PostgreSQL dentro de Docker Compose;
- respuesta del backend desde Docker en `GET http://localhost:8080/health`;
- ejecucion de pruebas automatizadas con resultado exitoso.

Resultado de pruebas automatizadas:

```text
Tests run: 8, Failures: 0, Errors: 0, Skipped: 0
```

## 14. Que no se implementa en esta sesion

Durante esta sesion no se implementa:

- Flyway;
- JWT;
- login real;
- control de lotes;
- stock disponible;
- movimientos de inventario;
- frontend;
- pipeline CI/CD.

## 15. Resultado de la sesion

Al finalizar la Semana 5 - Sesion 2 se cuenta con:

- backend dockerizado;
- PostgreSQL ejecutable por Docker Compose;
- backend y base de datos conectados en red interna de Docker;
- puertos expuestos para Postman y pgAdmin;
- documentacion de ejecucion;
- base preparada para pruebas manuales completas.

## 16. Conclusion

La Semana 5 - Sesion 2 facilita la ejecucion del proyecto en otros equipos.

El backend y la base de datos ya pueden levantarse juntos mediante Docker Compose, reduciendo configuraciones manuales y preparando el camino para validaciones mas completas en QA.
