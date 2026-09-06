# MediStock - Semana 4 - Sesion 2

## Primeros endpoints de negocio

**Proyecto:** MediStock  
**Responsable:** Julian Alberto Trujillo Bonilla  
**Programa:** Ingenieria de Sistemas  
**Asignatura:** Sistemas Distribuidos  
**Rama de trabajo:** feature/week4-session2-medications-suppliers-api-dev  
**Arquitectura definida:** Monolito modular  
**Tecnologia del servidor de aplicacion:** Java con Spring Boot  

## 1. Objetivo de la sesion

Implementar los primeros endpoints de negocio de MediStock, pasando del esqueleto funcional creado en la Semana 4 - Sesion 1 a una API inicial que permita registrar y consultar informacion basica del dominio.

Durante esta sesion se implementan dos modelos funcionales:

- medicamentos;
- proveedores.

## 2. Punto de partida

La Semana 4 - Sesion 1 dejo un backend Spring Boot ejecutable con:

- endpoint `/health`;
- Actuator preparado;
- configuracion minima de seguridad;
- pruebas iniciales;
- persistencia marcada como pendiente.

En esta sesion se conserva la persistencia en memoria para evitar adelantar la complejidad de base de datos antes de que el dominio y los endpoints iniciales esten claros.

## 3. Alcance funcional

El alcance de esta sesion incluye:

- modelo de dominio `Medication`;
- modelo de dominio `Supplier`;
- puertos de repositorio para medicamentos y proveedores;
- servicios de aplicacion para crear, listar y consultar por ID;
- repositorios en memoria;
- controladores REST;
- objetos de request y response;
- validaciones basicas de entrada;
- manejo uniforme de errores;
- pruebas automatizadas de controladores;
- actualizacion de documentacion.

## 4. Endpoints implementados

### Medicamentos

```text
POST /api/medications
GET  /api/medications
GET  /api/medications/{id}
```

### Proveedores

```text
POST /api/suppliers
GET  /api/suppliers
GET  /api/suppliers/{id}
```

## 5. Medicamentos

El modelo `Medication` representa la informacion base de un medicamento dentro del catalogo.

Campos iniciales:

| Campo | Descripcion |
| --- | --- |
| `id` | Identificador interno |
| `code` | Codigo unico del medicamento |
| `name` | Nombre comercial o descriptivo |
| `activeIngredient` | Principio activo |
| `pharmaceuticalForm` | Forma farmaceutica |
| `concentration` | Concentracion |
| `unitOfMeasure` | Unidad de medida |
| `active` | Estado del medicamento |

Ejemplo de creacion:

```json
{
  "code": "MED-001",
  "name": "Acetaminofen 500 mg",
  "activeIngredient": "Acetaminofen",
  "pharmaceuticalForm": "Tableta",
  "concentration": "500 mg",
  "unitOfMeasure": "Caja"
}
```

## 6. Proveedores

El modelo `Supplier` representa un proveedor que puede participar posteriormente en entradas de inventario o abastecimiento de medicamentos.

Campos iniciales:

| Campo | Descripcion |
| --- | --- |
| `id` | Identificador interno |
| `documentNumber` | Numero de documento o NIT |
| `name` | Nombre del proveedor |
| `phone` | Telefono |
| `email` | Correo electronico |
| `address` | Direccion |
| `active` | Estado del proveedor |

Ejemplo de creacion:

```json
{
  "documentNumber": "900123456",
  "name": "Distribuidora Salud Total",
  "phone": "3001234567",
  "email": "ventas@saludtotal.com",
  "address": "Calle 10 # 15-20"
}
```

## 7. Validaciones aplicadas

Las peticiones validan que los campos obligatorios no lleguen vacios.

Para proveedores tambien se valida que el correo tenga formato valido.

Casos controlados:

- cuerpo invalido;
- campos obligatorios vacios;
- codigo de medicamento duplicado;
- documento de proveedor duplicado;
- consulta por ID inexistente.

## 8. Seguridad inicial

Como la autenticacion real todavia no hace parte del alcance, se ajusta la configuracion de seguridad para permitir pruebas por Postman sobre:

- `/health`;
- `/actuator/health`;
- `/api/medications/**`;
- `/api/suppliers/**`.

El resto de endpoints queda protegido por defecto.

## 9. Persistencia

La persistencia real sigue pendiente.

En esta sesion se usan repositorios en memoria para validar comportamiento HTTP, estructura modular y reglas basicas sin depender aun de base de datos.

La conexion con base de datos, JPA y migraciones se implementara en una fase posterior.

## 10. Pruebas automatizadas

Se agregan pruebas para:

- crear medicamentos;
- listar medicamentos;
- consultar medicamento por ID;
- rechazar medicamentos invalidos;
- crear proveedores;
- listar proveedores;
- consultar proveedor por ID;
- rechazar proveedores invalidos.

Resultado validado:

```text
Tests run: 6, Failures: 0, Errors: 0, Skipped: 0
Build: SUCCESS
```

## 11. Pruebas manuales sugeridas en Postman

Para ejecutar el backend:

```powershell
cd MediStock
.\mvnw.cmd spring-boot:run
```

Peticiones principales:

```text
GET  http://localhost:8080/health
POST http://localhost:8080/api/medications
GET  http://localhost:8080/api/medications
GET  http://localhost:8080/api/medications/1
POST http://localhost:8080/api/suppliers
GET  http://localhost:8080/api/suppliers
GET  http://localhost:8080/api/suppliers/1
```

## 12. Relacion con el diseno anterior

La implementacion respeta el monolito modular porque separa responsabilidades por paquetes:

- `catalog` para medicamentos;
- `suppliers` para proveedores;
- `shared` para validaciones y manejo de errores comunes.

Tambien mantiene una separacion basica inspirada en arquitectura hexagonal:

- dominio;
- aplicacion;
- puertos;
- infraestructura;
- interfaces REST.

## 13. Que no se implementa en esta sesion

Durante esta sesion no se implementa:

- base de datos;
- JPA;
- Flyway;
- JWT;
- login real;
- lotes;
- movimientos de inventario;
- stock disponible;
- relacion fisica entre medicamentos y proveedores;
- frontend.

## 14. Resultado de la sesion

Al finalizar la Semana 4 - Sesion 2 se cuenta con:

- API inicial de medicamentos;
- API inicial de proveedores;
- validaciones de request;
- respuestas HTTP estructuradas;
- manejo basico de errores;
- repositorios en memoria;
- pruebas automatizadas;
- contrato de API documentado;
- README actualizado.

## 15. Conclusion

La Semana 4 - Sesion 2 convierte el backend de MediStock en una API inicial de negocio.

El sistema todavia no implementa persistencia real ni reglas completas de inventario, pero ya permite probar operaciones basicas con dos recursos importantes del dominio: medicamentos y proveedores.
