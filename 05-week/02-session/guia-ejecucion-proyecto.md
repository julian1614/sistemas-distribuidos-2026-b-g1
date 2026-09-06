# MediStock - Guia de ejecucion del proyecto

## 1. Proposito

Este documento explica el paso a paso para ejecutar MediStock en otro equipo usando Docker.

La forma recomendada de ejecucion es levantar el backend y la base de datos PostgreSQL con Docker Compose. De esta manera, el companero que reciba el proyecto no necesita instalar PostgreSQL manualmente.

## 2. Requisitos previos

Antes de ejecutar el proyecto se debe contar con:

- Git instalado;
- Docker Desktop instalado y en ejecucion;
- Java 17 o superior, solo si se desea ejecutar el backend fuera de Docker;
- pgAdmin 4, solo si se desea visualizar la base de datos;
- Postman, Thunder Client o una herramienta similar para probar la API.

Para validar que Docker esta disponible:

```powershell
docker --version
docker compose version
```

## 3. Clonar el repositorio

Desde una terminal:

```powershell
git clone <url-del-repositorio>
cd Julian-Alberto-Trujillo-Bonilla-
```

Si se va a trabajar sobre la rama de desarrollo:

```powershell
git checkout Develop
```

## 4. Ubicarse en la carpeta del backend

El proyecto Spring Boot se encuentra dentro de la carpeta `MediStock`.

```powershell
cd MediStock
```

Desde esta carpeta se ejecutan los comandos de Maven y Docker Compose.

## 5. Ejecutar el proyecto con Docker

Para construir la imagen del backend y levantar backend + PostgreSQL:

```powershell
docker compose up --build
```

Para ejecutarlo en segundo plano:

```powershell
docker compose up --build -d
```

Servicios expuestos:

| Servicio | Uso | URL o puerto |
| --- | --- | --- |
| Backend | API REST | `http://localhost:8080` |
| PostgreSQL | Base de datos | `localhost:5433` |

## 6. Verificar que los contenedores estan activos

Desde la carpeta `MediStock`:

```powershell
docker compose ps
```

Se deben visualizar los servicios:

```text
medistock-backend
medistock-postgres
```

El contenedor de PostgreSQL debe aparecer como `healthy`.

## 7. Probar el backend

Con los contenedores activos, abrir Postman y ejecutar:

```text
GET http://localhost:8080/health
```

Respuesta esperada:

```json
{
  "application": "MediStock",
  "status": "UP"
}
```

Tambien se pueden probar los endpoints iniciales:

```text
POST http://localhost:8080/api/medications
GET  http://localhost:8080/api/medications
POST http://localhost:8080/api/suppliers
GET  http://localhost:8080/api/suppliers
```

## 8. Conectarse a PostgreSQL desde pgAdmin 4

Con Docker Compose activo, abrir pgAdmin 4 y registrar un nuevo servidor.

En la pestana `General`:

| Campo | Valor |
| --- | --- |
| Name | `MediStock Docker` |

En la pestana `Connection`:

| Campo | Valor |
| --- | --- |
| Host name/address | `localhost` |
| Port | `5433` |
| Maintenance database | `medistock` |
| Username | `medistock` |
| Password | `medistock` |

Tablas esperadas despues de iniciar el backend:

```text
medications
suppliers
```

## 9. Detener el proyecto

Para detener los contenedores sin eliminar los datos:

```powershell
docker compose down
```

Para detener los contenedores y eliminar tambien el volumen de datos:

```powershell
docker compose down -v
```

Se debe usar `docker compose down -v` solo cuando se quiera reiniciar la base de datos desde cero.

## 10. Ejecutar pruebas automatizadas

Las pruebas automatizadas usan H2 en memoria, por lo que no dependen de PostgreSQL ni de Docker.

Desde la carpeta `MediStock`:

```powershell
.\mvnw.cmd test
```

Resultado esperado:

```text
BUILD SUCCESS
```

## 11. Ejecutar el backend sin Docker

Si se desea correr el backend desde Maven, primero se debe asegurar que PostgreSQL este activo.

Opcion recomendada: dejar activo solo PostgreSQL con Docker Compose y detener el backend de Docker si esta usando el puerto `8080`.

Si el backend ya esta corriendo en Docker, no se debe ejecutar al mismo tiempo:

```powershell
.\mvnw.cmd spring-boot:run
```

porque ambos intentarian usar el puerto `8080`.

Para apagar Docker Compose completo:

```powershell
docker compose down
```

Luego iniciar manualmente el backend:

```powershell
.\mvnw.cmd spring-boot:run
```

## 12. Errores comunes

### Puerto 8080 en uso

Mensaje posible:

```text
Web server failed to start. Port 8080 was already in use.
```

Causa:

El backend ya esta ejecutandose en Docker o existe otro proceso usando el puerto `8080`.

Solucion:

```powershell
docker compose down
```

Luego volver a ejecutar solo una forma de arranque:

```powershell
docker compose up --build
```

o:

```powershell
.\mvnw.cmd spring-boot:run
```

### No se puede conectar desde pgAdmin

Verificar que se este usando:

```text
Host: localhost
Port: 5433
Database: medistock
Username: medistock
Password: medistock
```

Tambien confirmar que PostgreSQL este activo:

```powershell
docker compose ps
```

### Conflicto con un contenedor manual anterior

Si existe un contenedor llamado `medistock-postgres` creado manualmente, puede bloquear Docker Compose.

Solucion:

```powershell
docker stop medistock-postgres
docker rm medistock-postgres
docker compose up --build
```

## 13. Comando recomendado para un companero

Si el repositorio ya esta clonado, el flujo mas simple es:

```powershell
cd MediStock
docker compose up --build
```

Luego probar:

```text
GET http://localhost:8080/health
```

Con eso debe quedar activo el backend de MediStock y la base de datos PostgreSQL.
