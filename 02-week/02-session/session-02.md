# MediStock - Semana 2 - Sesion 2

## Contextos delimitados y decision arquitectonica

**Proyecto:** MediStock  
**Responsable:** Julian Alberto Trujillo Bonilla  
**Programa:** Ingenieria de Sistemas  
**Asignatura:** Sistemas Distribuidos  
**Rama de trabajo:** feature/week2-session2-architecture-decision-dev  
**Arquitectura definida:** Monolito modular  
**ADR asociado:** `docs/adr/ADR-001-monolito-modular.md`

## 1. Objetivo de la sesion

Formalizar la decision arquitectonica principal de MediStock y definir los contextos delimitados iniciales que orientaran el diseno del sistema.

Durante esta sesion se trabajan:

- contextos delimitados;
- limites entre modulos;
- responsabilidades por area del sistema;
- comparacion final entre alternativas principales;
- decision de arquitectura;
- registro de decision arquitectonica mediante ADR.

## 2. Punto de partida

En la Semana 2 - Sesion 1 se analizaron varias alternativas arquitectonicas: monolito tradicional, monolito modular, cliente-servidor, arquitectura por capas, arquitectura orientada a servicios, microservicios y arquitectura orientada a eventos.

La recomendacion inicial fue continuar con monolito modular porque MediStock necesita separacion interna de responsabilidades, pero todavia no tiene una necesidad real de servicios independientes desplegados por separado.

Esta sesion convierte esa recomendacion en una decision formal.

## 3. Contexto del problema

MediStock administra informacion sensible del inventario de medicamentos. Las operaciones de entrada y salida afectan existencias reales, lotes, vencimientos, trazabilidad y reportes.

El sistema debe proteger reglas como:

- no permitir stock negativo;
- no permitir salidas superiores al inventario disponible;
- conservar historial de movimientos;
- identificar lotes proximos a vencer;
- generar alertas de stock bajo;
- controlar operaciones segun permisos de usuario.

Estas reglas requieren una arquitectura que mantenga el dominio ordenado y evite mezclar responsabilidades.

## 4. Contextos delimitados iniciales

Un contexto delimitado define un area del sistema donde un conjunto de conceptos, reglas y responsabilidades tiene significado propio.

Para MediStock se proponen los siguientes contextos iniciales:

| Contexto delimitado | Responsabilidad principal |
| --- | --- |
| Acceso e identidad | Gestionar usuarios, roles, autenticacion y autorizacion |
| Catalogo | Gestionar medicamentos, categorias, laboratorios y datos descriptivos |
| Inventario | Gestionar lotes, entradas, salidas, existencias y movimientos |
| Proveedores | Gestionar informacion de proveedores |
| Alertas | Detectar stock bajo y vencimientos proximos |
| Reportes | Consultar informacion consolidada del inventario |

Estos contextos son iniciales y pueden refinarse cuando se trabaje el diseno detallado del dominio.

## 5. Limites entre contextos

Los limites ayudan a evitar que una parte del sistema asuma responsabilidades de otra.

### Acceso e identidad

Debe encargarse de usuarios, roles y permisos. No debe modificar inventario directamente.

### Catalogo

Debe encargarse de la informacion descriptiva de medicamentos. No debe registrar entradas o salidas.

### Inventario

Debe concentrar las reglas criticas de existencias, lotes y movimientos. Es el contexto mas sensible para la consistencia.

### Proveedores

Debe administrar la informacion de abastecimiento. Puede relacionarse con entradas de inventario, pero no debe calcular existencias.

### Alertas

Debe detectar condiciones relevantes como stock bajo o vencimiento proximo. Puede depender de informacion de inventario, pero no debe bloquear operaciones criticas.

### Reportes

Debe consultar y presentar informacion consolidada. No debe modificar reglas del dominio.

## 6. Mapa conceptual inicial

```text
MediStock
+-- Acceso e identidad
|   +-- Usuarios
|   +-- Roles
|   +-- Permisos
+-- Catalogo
|   +-- Medicamentos
|   +-- Categorias
|   +-- Laboratorios
+-- Inventario
|   +-- Lotes
|   +-- Entradas
|   +-- Salidas
|   +-- Movimientos
+-- Proveedores
|   +-- Proveedores
+-- Alertas
|   +-- Stock bajo
|   +-- Vencimientos
+-- Reportes
    +-- Inventario
    +-- Movimientos
    +-- Vencimientos
```

Este mapa no representa todavia carpetas de codigo. Representa responsabilidades conceptuales.

## 7. Comparacion final de alternativas principales

| Alternativa | Resultado para MediStock |
| --- | --- |
| Monolito tradicional | Simple al inicio, pero con riesgo de mezclar responsabilidades |
| Monolito modular | Mantiene simplicidad operativa y permite separar el dominio por contextos |
| Microservicios | Agrega complejidad distribuida prematura para el alcance actual |

La opcion seleccionada es monolito modular.

## 8. Decision arquitectonica

MediStock se construira como **monolito modular**.

La aplicacion se desplegara inicialmente como una sola unidad, pero internamente se organizara por modulos o contextos con responsabilidades claras.

La decision se registra formalmente en:

```text
docs/adr/ADR-001-monolito-modular.md
```

## 9. Justificacion

El monolito modular es adecuado para MediStock porque:

- el proyecto sera desarrollado por una sola persona;
- el dominio aun esta en etapa de refinamiento;
- las reglas de inventario necesitan consistencia fuerte;
- no existe necesidad actual de despliegue independiente por modulo;
- se reduce la complejidad operativa;
- se permite aplicar arquitectura hexagonal y buenas practicas por areas;
- se evita introducir fallos distribuidos innecesarios entre componentes internos.

## 10. Consecuencias de la decision

### Consecuencias positivas

- Menor complejidad inicial.
- Mayor facilidad para implementar y probar.
- Reglas de negocio mas protegidas.
- Menor costo de despliegue.
- Evolucion gradual por modulos.

### Consecuencias negativas

- Los modulos no escalaran de forma independiente al inicio.
- Se requiere disciplina para evitar acoplamiento interno.
- La separacion modular debe mantenerse mediante convenciones y revisiones.

## 11. Reglas de arquitectura iniciales

- El dominio no debe depender directamente de infraestructura.
- Las reglas de inventario deben vivir en el contexto de inventario.
- Los reportes no deben modificar datos.
- Las alertas no deben bloquear operaciones criticas de inventario.
- La comunicacion entre modulos debe evitar acoplamientos innecesarios.
- Las decisiones importantes deben documentarse mediante ADR.

## 12. Relacion con sistemas distribuidos

Aunque MediStock no adopta microservicios, sigue siendo relevante para Sistemas Distribuidos porque eventualmente puede tener comunicacion entre cliente, aplicacion, base de datos y otros recursos externos.

La decision evita agregar distribucion interna innecesaria, pero conserva los conceptos aprendidos:

- consistencia fuerte para inventario;
- consistencia eventual aceptable para alertas;
- cuidado con reintentos e idempotencia;
- tolerancia a fallos parciales;
- analisis de latencia y disponibilidad.

## 13. Que no se implementa en esta sesion

Durante esta sesion no se implementa codigo fuente.

Tampoco se crean:

- proyecto backend;
- base de datos;
- controladores;
- servicios;
- contenedores;
- mensajeria asincrona;
- microservicios.

La implementacion iniciara cuando el roadmap llegue a la fase correspondiente.

## 14. Resultado de la sesion

Al finalizar la Semana 2 - Sesion 2 se cuenta con:

- contextos delimitados iniciales;
- limites conceptuales entre modulos;
- comparacion final de alternativas principales;
- decision formal de monolito modular;
- ADR-001 creado;
- reglas iniciales de arquitectura;
- base preparada para el diseno detallado de la Semana 3.

## 15. Que debo poder explicar al profesor

La idea principal de esta sesion es responder:

**Por que MediStock usara monolito modular como arquitectura inicial?**

Porque permite separar responsabilidades y proteger reglas criticas del inventario sin introducir la complejidad operativa de microservicios. Para el alcance actual, el tamano del equipo y las necesidades del dominio, el monolito modular ofrece el mejor equilibrio entre orden, simplicidad y posibilidad de evolucion.

## 16. Conclusion

La Semana 2 - Sesion 2 formaliza una decision clave para MediStock: construir el sistema como monolito modular.

Esta decision no ignora los sistemas distribuidos. Al contrario, reconoce sus riesgos y evita introducir distribucion interna antes de necesitarla. El proyecto podra evolucionar gradualmente, manteniendo limites claros entre contextos y documentando decisiones importantes mediante ADR.
