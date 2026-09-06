# MediStock - Semana 1 - Sesion 2

## Fundamentos de ingenieria para construir MediStock

**Proyecto:** MediStock  
**Responsable:** Julian Alberto Trujillo Bonilla  
**Programa:** Ingenieria de Sistemas  
**Asignatura:** Sistemas Distribuidos  
**Arquitectura objetivo:** Monolito modular  
**Estado de la arquitectura:** Decision preliminar. La justificacion formal se realizara en una semana posterior mediante un ADR.

## 1. Objetivo de la sesion

Establecer una base de ingenieria para que MediStock pueda evolucionar de forma ordenada, mantenible y coherente con el dominio del problema.

Durante esta sesion se trabajan los siguientes temas:

- diseno orientado al dominio;
- arquitectura hexagonal;
- principios SOLID;
- estrategia inicial de pruebas;
- flujo de trabajo con Git;
- registro de decisiones mediante ADR;
- relacion entre buenas practicas de ingenieria y sistemas distribuidos.

El objetivo no es implementar codigo todavia, sino definir criterios que guiaran la implementacion futura.

## 2. Punto de partida

En la Semana 1 - Sesion 1 se identifico que MediStock debe considerar riesgos propios de sistemas distribuidos, como fallos parciales, mensajes duplicados, concurrencia, respuestas perdidas y diferentes necesidades de consistencia.

Tambien se definio que las operaciones de inventario requieren consistencia fuerte, mientras que las alertas pueden admitir consistencia eventual.

Esta segunda sesion complementa ese trabajo con una pregunta practica:

**Como se debe organizar MediStock para que esas decisiones puedan implementarse correctamente mas adelante?**

## 3. Dominio de MediStock

El dominio principal de MediStock es la gestion de inventario de medicamentos.

Esto incluye controlar:

- medicamentos;
- lotes;
- cantidades disponibles;
- entradas;
- salidas;
- proveedores;
- usuarios;
- roles;
- alertas;
- reportes.

El dominio no debe confundirse con detalles tecnicos como base de datos, controladores, rutas HTTP, contenedores o interfaces graficas. Esos elementos son importantes, pero no representan por si mismos las reglas del negocio.

## 4. Primer analisis con diseno orientado al dominio

El diseno orientado al dominio propone modelar el sistema a partir del lenguaje, reglas y procesos del negocio.

Para MediStock, las primeras entidades y conceptos del dominio son:

| Concepto | Descripcion inicial |
| --- | --- |
| Medicamento | Producto que puede ser registrado, consultado y gestionado |
| Lote | Conjunto de unidades de un medicamento con identificador y fecha de vencimiento |
| Movimiento de inventario | Registro de una entrada o salida de medicamentos |
| Proveedor | Entidad que suministra medicamentos |
| Usuario | Persona que utiliza el sistema segun permisos asignados |
| Rol | Conjunto de permisos asociados a un usuario |
| Alerta | Aviso generado por stock bajo o vencimiento proximo |
| Reporte | Consulta organizada sobre inventario, vencimientos o movimientos |

## 5. Reglas de negocio iniciales

Las reglas de negocio son condiciones que deben cumplirse sin importar la tecnologia usada.

En MediStock se identifican como reglas principales:

- un medicamento debe tener un nombre obligatorio;
- un medicamento puede tener varios lotes;
- cada lote debe tener un numero identificador;
- la cantidad disponible nunca debe ser negativa;
- no se debe permitir una salida mayor al inventario disponible;
- cada entrada y salida debe quedar registrada;
- solo usuarios autorizados pueden modificar inventario;
- los movimientos de inventario deben conservarse como historial;
- el sistema debe detectar stock bajo;
- el sistema debe detectar medicamentos proximos a vencer.

Estas reglas pertenecen al dominio y no deben quedar dispersas en capas tecnicas sin control.

## 6. Posibles modulos conceptuales

La arquitectura objetivo es un monolito modular. Por ahora los modulos son conceptuales; no se crearan carpetas de implementacion hasta que exista una razon tecnica y academica para hacerlo.

Primera division conceptual:

```text
MediStock
+-- Acceso e identidad
+-- Catalogo
+-- Inventario
+-- Proveedores
+-- Alertas
+-- Reportes
```

| Modulo conceptual | Responsabilidad |
| --- | --- |
| Acceso e identidad | Gestionar usuarios, roles y permisos |
| Catalogo | Gestionar medicamentos, categorias y laboratorios |
| Inventario | Gestionar lotes, entradas, salidas y existencias |
| Proveedores | Gestionar informacion de proveedores |
| Alertas | Detectar stock bajo y vencimientos proximos |
| Reportes | Consultar informacion consolidada del inventario |

La separacion modular busca que cada parte tenga responsabilidades claras y que las reglas criticas del inventario no se mezclen con detalles externos.

## 7. Arquitectura hexagonal aplicada a MediStock

La arquitectura hexagonal propone proteger el nucleo del negocio frente a cambios externos.

En MediStock, el nucleo debe contener las reglas importantes, como evitar stock negativo, validar salidas y registrar movimientos. Los detalles externos, como la base de datos o una API HTTP, deben comunicarse con ese nucleo mediante puertos y adaptadores.

Estructura conceptual:

```text
Entradas externas
  |
  v
Adaptadores de entrada
  |
  v
Casos de uso
  |
  v
Dominio
  |
  v
Puertos de salida
  |
  v
Adaptadores de salida
```

Aplicado a MediStock:

| Capa conceptual | Ejemplo en MediStock |
| --- | --- |
| Dominio | Medicamento, lote, movimiento, reglas de inventario |
| Casos de uso | Registrar entrada, registrar salida, consultar inventario |
| Adaptadores de entrada | Controladores HTTP o interfaz de usuario |
| Puertos de salida | Contratos para guardar datos o consultar repositorios |
| Adaptadores de salida | Base de datos, mensajeria o servicios externos |

Durante esta sesion no se implementa la estructura fisica. La arquitectura hexagonal se registra como criterio de diseno para evitar que la logica del negocio dependa directamente de tecnologia externa.

## 8. Aplicacion de SOLID

Los principios SOLID ayudan a construir software mantenible. Para MediStock se interpretan de forma practica:

| Principio | Aplicacion en MediStock |
| --- | --- |
| Responsabilidad unica | Una clase o modulo no debe encargarse al mismo tiempo de validar reglas, guardar datos y responder peticiones |
| Abierto/cerrado | El sistema debe permitir agregar nuevas alertas sin modificar de forma riesgosa reglas existentes |
| Sustitucion de Liskov | Las abstracciones deben poder reemplazarse sin romper el comportamiento esperado |
| Segregacion de interfaces | Los contratos deben ser pequenos y enfocados en necesidades concretas |
| Inversion de dependencias | El dominio no debe depender directamente de la base de datos ni de detalles de infraestructura |

Estas ideas se aplicaran gradualmente cuando exista implementacion.

## 9. Estrategia inicial de pruebas

Aunque todavia no existe codigo para probar, se define una estrategia inicial de pruebas para orientar el desarrollo futuro.

| Tipo de prueba | Proposito | Ejemplo futuro |
| --- | --- | --- |
| Pruebas unitarias | Validar reglas de dominio de forma aislada | No permitir stock negativo |
| Pruebas de aplicacion | Validar casos de uso | Registrar una salida valida |
| Pruebas de integracion | Validar comunicacion con infraestructura | Guardar un movimiento en base de datos |
| Pruebas funcionales | Validar flujos completos | Registrar entrada y consultar inventario actualizado |

Primeras reglas que deberan probarse cuando exista codigo:

- no permitir salidas mayores al inventario disponible;
- no permitir cantidades negativas;
- registrar historial de movimientos;
- aplicar idempotencia en operaciones criticas;
- generar alertas cuando el stock este por debajo del minimo;
- identificar lotes proximos a vencer.

## 10. Flujo Git del proyecto

El repositorio trabajara con ramas principales y ramas de trabajo por sesion.

Flujo base:

```text
main
  |
QA
  |
Develop
  |
rama de trabajo por sesion
```

Uso esperado:

| Rama | Proposito |
| --- | --- |
| main | Version estable del proyecto |
| QA | Validacion antes de integrar a main |
| Develop | Integracion principal del trabajo en desarrollo |
| rama de sesion | Cambios especificos de una sesion o entregable |

Reglas de trabajo:

- crear una rama desde `Develop` para cada sesion;
- mantener commits en ingles;
- mantener archivos Markdown en espanol;
- abrir una solicitud de integracion hacia `Develop` cuando el entregable este listo;
- usar `QA` para revisiones o validaciones antes de promover cambios mas estables;
- no mezclar cambios de sesiones diferentes en una misma rama.

## 11. Uso de ADR

Un ADR es un registro de decision arquitectonica. Sirve para explicar que decision se tomo, por que se tomo, que alternativas se evaluaron y que consecuencias tiene.

MediStock utilizara ADR cuando existan decisiones importantes, por ejemplo:

- eleccion entre monolito tradicional, monolito modular y microservicios;
- seleccion de base de datos;
- uso o no uso de mensajeria asincrona;
- estrategia de idempotencia;
- organizacion de modulos;
- estrategia de despliegue.

Estructura propuesta para futuros ADR:

```text
Titulo
Estado
Contexto
Decision
Alternativas consideradas
Consecuencias
Fecha
```

En esta sesion no se crea el ADR final de arquitectura, porque la comparacion formal de alternativas se trabajara en una semana posterior.

## 12. Que se decide y que no se decide

| Tema | Decision actual |
| --- | --- |
| Dominio principal | Gestion de inventario de medicamentos |
| Arquitectura objetivo | Monolito modular |
| Arquitectura hexagonal | Se adopta como criterio de diseno conceptual |
| DDD | Se usara para entender reglas, entidades y limites del negocio |
| SOLID | Se usara como guia para evitar acoplamiento innecesario |
| Pruebas | Se define estrategia inicial, sin implementacion todavia |
| ADR | Se usara para decisiones arquitectonicas relevantes |
| Implementacion | No se inicia en esta sesion |
| Microservicios | No se adoptan por defecto |
| Mensajeria asincrona | No se incorpora sin necesidad concreta |

## 13. Relacion con sistemas distribuidos

Los fundamentos de ingenieria ayudan a enfrentar problemas distribuidos porque separan responsabilidades y evitan que las reglas criticas queden mezcladas con detalles tecnicos.

En MediStock esto es importante porque:

- las salidas de inventario necesitan consistencia fuerte;
- los reintentos deben manejarse sin duplicar movimientos;
- la aplicacion debe tolerar fallos parciales;
- las alertas pueden evolucionar hacia procesos asincronos;
- la arquitectura debe permitir cambios futuros sin romper el dominio.

Una buena separacion del dominio facilita que en etapas posteriores se agreguen base de datos, API, pruebas, contenedores o mensajeria sin reescribir las reglas principales.

## 14. Resultado de la sesion

Al finalizar la Semana 1 - Sesion 2 se cuenta con:

- dominio principal identificado;
- conceptos iniciales del dominio documentados;
- reglas de negocio iniciales organizadas;
- modulos conceptuales propuestos;
- arquitectura hexagonal explicada y aplicada de forma conceptual;
- principios SOLID relacionados con MediStock;
- estrategia inicial de pruebas definida;
- flujo Git documentado;
- uso futuro de ADR definido;
- limites claros sobre lo que aun no se implementara.

## 15. Que debo poder explicar al profesor

La idea principal de esta sesion es responder:

**Por que MediStock necesita una base de ingenieria antes de iniciar la implementacion?**

Porque el sistema maneja informacion critica de inventario. Si las reglas de negocio se mezclan con detalles tecnicos, sera mas dificil garantizar consistencia, trazabilidad, idempotencia y mantenimiento. Por eso se define primero una organizacion conceptual basada en dominio, arquitectura hexagonal, SOLID, pruebas, Git y ADR.

## 16. Conclusion

La Semana 1 - Sesion 2 establece la forma en que MediStock debe construirse para evitar una implementacion desordenada.

El proyecto continuara como monolito modular, pero con una separacion conceptual clara entre dominio, casos de uso e infraestructura. Esto permitira que las reglas principales del inventario permanezcan protegidas frente a cambios de tecnologia.

Tambien se define que las pruebas y los ADR seran herramientas importantes para validar comportamiento y justificar decisiones. De esta forma, MediStock no solo avanza como una idea funcional, sino como un proyecto documentado, mantenible y preparado para crecer por sesiones.
