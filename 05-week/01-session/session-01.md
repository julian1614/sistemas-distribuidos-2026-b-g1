# MediStock - Semana 5 - Sesion 1

## Persistencia real con base de datos

**Proyecto:** MediStock  
**Responsable:** Julian Alberto Trujillo Bonilla  
**Programa:** Ingenieria de Sistemas  
**Asignatura:** Sistemas Distribuidos  
**Rama de trabajo:** feature/week5-session1-database-persistence-dev  
**Arquitectura definida:** Monolito modular  
**Tecnologia del servidor de aplicacion:** Java con Spring Boot  
**Base de datos objetivo:** PostgreSQL  

## 1. Objetivo de la sesion

Implementar persistencia real para los modelos funcionales creados en la Semana 4 - Sesion 2.

La sesion conecta medicamentos y proveedores con JPA, dejando al backend preparado para almacenar informacion en una base de datos PostgreSQL.

## 2. Punto de partida

La Semana 4 - Sesion 2 dejo disponibles los primeros endpoints de negocio:

```text
POST /api/medications
GET  /api/medications
GET  /api/medications/{id}

POST /api/suppliers
GET  /api/suppliers
GET  /api/suppliers/{id}
```

Hasta esa fase, los datos se almacenaban en memoria. Esto permitia validar la API, pero la informacion se perdia al reiniciar la aplicacion.

## 3. Alcance funcional

El alcance de esta sesion incluye:

- agregar Spring Data JPA;
- agregar driver de PostgreSQL;
- configurar datasource principal para PostgreSQL;
- crear entidades JPA para medicamentos;
- crear entidades JPA para proveedores;
- crear repositorios Spring Data;
- crear adaptadores JPA para los puertos existentes;
- conservar repositorios en memoria bajo perfil `memory`;
- agregar configuracion H2 para pruebas automatizadas;
- agregar prueba de integracion de persistencia;
- actualizar contrato de API;
- actualizar README.

## 4. Dependencias agregadas

| Dependencia | Proposito |
| --- | --- |
| Spring Data JPA | Persistencia mediante repositorios y entidades JPA |
| PostgreSQL Driver | Conexion del backend con PostgreSQL |
| H2 Database | Base de datos en memoria para pruebas automatizadas |
| Spring Data JPA Test | Soporte de pruebas de persistencia |

## 5. Configuracion principal

La aplicacion queda configurada para usar PostgreSQL mediante variables de entorno:

```yaml
spring:
  datasource:
    url: ${DB_URL:jdbc:postgresql://localhost:5433/medistock}
    username: ${DB_USERNAME:medistock}
    password: ${DB_PASSWORD:medistock}
    driver-class-name: org.postgresql.Driver
```

Valores por defecto:

| Variable | Valor por defecto |
| --- | --- |
| `DB_URL` | `jdbc:postgresql://localhost:5433/medistock` |
| `DB_USERNAME` | `medistock` |
| `DB_PASSWORD` | `medistock` |

## 6. Tablas esperadas

Hibernate queda configurado temporalmente con `ddl-auto: update` para facilitar el desarrollo inicial.

Tablas esperadas:

```text
medications
suppliers
```

Esta configuracion permite que las tablas se creen o actualicen durante desarrollo. En una fase posterior se recomienda migrar a Flyway para controlar cambios de esquema.

## 7. Persistencia de medicamentos

Se agrega la entidad JPA `MedicationJpaEntity`, asociada a la tabla:

```text
medications
```

Campos persistidos:

- `id`;
- `code`;
- `name`;
- `activeIngredient`;
- `pharmaceuticalForm`;
- `concentration`;
- `unitOfMeasure`;
- `active`.

El adaptador `JpaMedicationRepository` implementa el puerto `MedicationRepositoryPort` y evita que la capa de aplicacion dependa directamente de Spring Data.

## 8. Persistencia de proveedores

Se agrega la entidad JPA `SupplierJpaEntity`, asociada a la tabla:

```text
suppliers
```

Campos persistidos:

- `id`;
- `documentNumber`;
- `name`;
- `phone`;
- `email`;
- `address`;
- `active`.

El adaptador `JpaSupplierRepository` implementa el puerto `SupplierRepositoryPort` y mantiene la separacion entre aplicacion e infraestructura.

## 9. Repositorios en memoria

Los repositorios en memoria no se eliminan. Se conservan con el perfil:

```text
memory
```

Esto permite usarlos en escenarios controlados o pruebas aisladas, mientras que el perfil normal de la aplicacion utiliza los repositorios JPA.

## 10. Pruebas automatizadas

Para pruebas se agrega configuracion H2 en:

```text
MediStock/src/test/resources/application.yaml
```

Esto permite validar JPA sin depender de una base de datos externa.

Se agrega una prueba de integracion que confirma:

- creacion de medicamento por API;
- persistencia del medicamento mediante JPA;
- listado de medicamentos desde almacenamiento persistente;
- creacion de proveedor por API;
- persistencia del proveedor mediante JPA;
- listado de proveedores desde almacenamiento persistente.

Resultado validado:

```text
Tests run: 8, Failures: 0, Errors: 0, Skipped: 0
Build: SUCCESS
```

## 11. Pruebas manuales sugeridas

Para probar manualmente con PostgreSQL local se debe tener una base de datos disponible con:

```text
Database: medistock
Username: medistock
Password: medistock
Port: 5433
```

Luego ejecutar:

```powershell
cd MediStock
.\mvnw.cmd spring-boot:run
```

Peticiones principales:

```text
POST http://localhost:8080/api/medications
GET  http://localhost:8080/api/medications
POST http://localhost:8080/api/suppliers
GET  http://localhost:8080/api/suppliers
```

## 12. Relacion con Docker

En esta sesion no se implementa Docker Compose.

La aplicacion queda lista para conectarse a PostgreSQL, pero la ejecucion de base de datos y backend en contenedores se implementara en una fase posterior.

## 13. Que no se implementa en esta sesion

Durante esta sesion no se implementa:

- Docker Compose;
- contenedor del backend;
- contenedor de PostgreSQL;
- Flyway;
- JWT;
- login real;
- lotes;
- stock disponible;
- movimientos de inventario;
- frontend.

## 14. Resultado de la sesion

Al finalizar la Semana 5 - Sesion 1 se cuenta con:

- persistencia JPA para medicamentos;
- persistencia JPA para proveedores;
- configuracion PostgreSQL;
- configuracion H2 para pruebas;
- adaptadores JPA para los puertos existentes;
- pruebas automatizadas de persistencia;
- contrato de API actualizado;
- README actualizado.

## 15. Conclusion

La Semana 5 - Sesion 1 permite que MediStock avance de una API en memoria hacia una API conectada a persistencia real.

El proyecto todavia no ejecuta backend y base de datos con Docker, pero ya tiene la base tecnica necesaria para hacerlo en la siguiente fase.
