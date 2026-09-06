# MediStock - Semana 4 - Sesion 1

## Construccion del servicio y esqueleto funcional

**Proyecto:** MediStock  
**Responsable:** Julian Alberto Trujillo Bonilla  
**Programa:** Ingenieria de Sistemas  
**Asignatura:** Sistemas Distribuidos  
**Rama de trabajo:** feature/week4-session1-walking-skeleton-dev  
**Arquitectura definida:** Monolito modular  
**Tecnologia del servidor de aplicacion:** Java con Spring Boot  

## 1. Objetivo de la sesion

Crear el primer esqueleto funcional del servidor de aplicacion de MediStock, manteniendo una base coherente con el monolito modular y la arquitectura hexagonal conceptual definida en las semanas anteriores.

Durante esta sesion se trabaja:

- creacion del proyecto Spring Boot;
- configuracion inicial del servidor;
- endpoint minimo de salud;
- configuracion inicial de seguridad;
- preparacion conceptual de persistencia para fases posteriores;
- primera prueba automatizada;
- documentacion del avance tecnico.

## 2. Punto de partida

Hasta la Semana 3, MediStock se habia trabajado principalmente desde documentacion, analisis y diseno. Ya existen:

- problema del negocio;
- backlog inicial;
- decision de monolito modular;
- ADR-001;
- contextos delimitados;
- diseno de dominio;
- contratos conceptuales;
- modelo de datos preliminar.

En esta sesion se inicia la implementacion tecnica con un esqueleto funcional.

## 3. Tecnologia seleccionada

Para el servidor de aplicacion se utiliza:

| Elemento | Decision |
| --- | --- |
| Lenguaje | Java |
| Marco de trabajo | Spring Boot |
| Empaquetado | Jar |
| Gestor de dependencias | Maven |
| Configuracion | YAML |
| Base de datos | Pendiente para una fase posterior |
| Migraciones | Pendiente para una fase posterior |
| Pruebas | JUnit y MockMvc |

## 4. Dependencias iniciales

El proyecto incluye dependencias para construir una base tecnica inicial:

| Dependencia | Proposito |
| --- | --- |
| Spring Web MVC | Crear endpoints HTTP |
| Spring Boot Actuator | Exponer informacion de salud de la aplicacion |
| Validation | Preparar validaciones de entrada |
| Spring Security | Preparar seguridad de la aplicacion |

PostgreSQL, Flyway y Docker Compose quedan como decisiones pendientes para la fase donde se implemente persistencia y configuracion de contenedores.

## 5. Estructura creada

Estructura inicial relevante:

```text
MediStock/
+-- pom.xml
+-- src/
    +-- main/
    |   +-- java/
    |   |   +-- com/MediStock/MediStock/
    |   |       +-- MediStockApplication.java
    |   |       +-- health/
    |   |       |   +-- HealthController.java
    |   |       +-- security/
    |   |           +-- SecurityConfig.java
    |   +-- resources/
    |       +-- application.yaml
    +-- test/
        +-- java/
            +-- com/MediStock/MediStock/
                +-- MediStockApplicationTests.java
                +-- health/
                    +-- HealthControllerTests.java
```

## 6. Endpoint de salud

Se crea un endpoint minimo:

```text
GET /health
```

Respuesta esperada:

```json
{
  "status": "UP",
  "application": "MediStock",
  "timestamp": "fecha-hora"
}
```

Este endpoint permite verificar que el servidor responde correctamente.

## 7. Actuator

Tambien queda disponible el endpoint de salud de Actuator:

```text
GET /actuator/health
```

Este endpoint sera util para validaciones tecnicas, contenedores y monitoreo futuro.

## 8. Seguridad inicial

Como el proyecto incluye Spring Security, se agrega una configuracion minima para permitir acceso publico a:

- `/health`;
- `/actuator/health`.

El resto de endpoints queda protegido por defecto. La autenticacion real se implementara en una fase posterior.

## 9. Persistencia pendiente

El proyecto no implementa persistencia real en esta sesion. La base de datos, las migraciones y las entidades JPA se agregaran cuando el roadmap indique implementacion de datos.

Esta decision reduce riesgos del primer esqueleto funcional y permite validar primero que el servidor de aplicacion arranque y responda.

## 10. Pruebas iniciales

Se mantiene la prueba de carga de contexto generada por Spring Boot y se agrega una prueba para validar el endpoint `/health`.

La prueba verifica:

- codigo HTTP 200;
- estado `UP`;
- nombre de aplicacion `MediStock`.

## 11. Relacion con el diseno anterior

Esta sesion no implementa todavia los modulos de negocio completos. El objetivo es confirmar que el servidor de aplicacion puede iniciar, responder y aceptar crecimiento progresivo.

El esqueleto funcional respeta las decisiones previas:

- monolito modular como arquitectura inicial;
- separacion progresiva por responsabilidades;
- documentacion mediante ADR;
- pruebas desde etapas tempranas;
- preparacion para persistencia sin implementar todo el dominio aun.

## 12. Que no se implementa en esta sesion

Durante esta sesion no se implementa:

- autenticacion real;
- JWT;
- entidades JPA del dominio;
- repositorios reales;
- controladores de medicamentos;
- casos de uso de inventario;
- reglas completas de negocio;
- interfaz grafica;
- despliegue productivo.

## 13. Resultado de la sesion

Al finalizar la Semana 4 - Sesion 1 se cuenta con:

- proyecto Spring Boot creado;
- servidor de aplicacion inicial;
- endpoint `/health`;
- Actuator preparado;
- configuracion minima de seguridad;
- persistencia dejada como pendiente documentado;
- prueba automatizada del endpoint de salud;
- base lista para iniciar implementacion modular.

## 14. Que debo poder explicar al profesor

La idea principal de esta sesion es responder:

**Por que se crea primero un esqueleto funcional antes de implementar todo el dominio?**

Porque permite validar temprano que el proyecto arranca, responde y tiene una estructura tecnica minima. Esto reduce riesgos antes de agregar reglas de negocio, persistencia completa, seguridad real y modulos internos.

## 15. Conclusion

La Semana 4 - Sesion 1 marca el inicio de la implementacion tecnica de MediStock.

El proyecto pasa de documentacion y diseno a un servidor de aplicacion funcional con Spring Boot. Todavia no implementa las reglas completas del dominio, pero deja una base ejecutable, verificable y preparada para crecer segun la arquitectura definida.
