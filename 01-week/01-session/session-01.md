# MediStock - Semana 1 - Sesion 1

## Sistemas distribuidos: modelos, tiempo, consistencia y compromisos

**Proyecto:** MediStock  
**Responsable:** Julian Alberto Trujillo Bonilla  
**Programa:** Ingenieria de Sistemas  
**Asignatura:** Sistemas Distribuidos  
**Arquitectura objetivo:** Monolito modular  
**Estado de la arquitectura:** Decision preliminar. La justificacion formal se realizara durante las siguientes semanas.

## 1. Objetivo de la sesion

Comprender los fundamentos principales de los sistemas distribuidos y relacionarlos con las necesidades del proyecto MediStock.

Durante esta sesion se analizan especialmente:

- comunicacion mediante red;
- modelos de sistemas y fallos;
- tiempo y causalidad;
- consistencia;
- CAP y PACELC;
- replicacion y particionamiento;
- consenso;
- comunicacion sincrona y asincrona;
- semanticas de entrega;
- idempotencia.

El proposito no es implementar todavia estos mecanismos, sino identificar cuales seran relevantes para MediStock y justificar las primeras decisiones del proyecto.

## 2. Contexto del proyecto

MediStock es un sistema para la gestion y control de inventario de medicamentos.

El proyecto busca centralizar informacion relacionada con:

- medicamentos;
- lotes;
- cantidades disponibles;
- fechas de vencimiento;
- entradas de inventario;
- salidas de inventario;
- proveedores;
- alertas;
- usuarios;
- reportes.

Actualmente, muchos procesos de inventario pueden realizarse manualmente o mediante hojas de calculo. Esto puede ocasionar errores en las cantidades registradas, perdida de informacion, dificultad para detectar medicamentos proximos a vencer y problemas para determinar las existencias reales.

MediStock busca proporcionar una fuente confiable de informacion sobre el estado del inventario.

## 3. Responsable del proyecto

El proyecto sera desarrollado individualmente por:

**Julian Alberto Trujillo Bonilla**

Debido a que MediStock es un proyecto individual, las responsabilidades de analisis, desarrollo, arquitectura, pruebas y documentacion seran asumidas por el mismo desarrollador.

## 4. Problema principal

La gestion manual o poco centralizada del inventario de medicamentos puede generar inconsistencias entre las existencias registradas y las existencias reales.

Entre los principales problemas identificados se encuentran:

- desconocimiento de la cantidad real disponible;
- errores durante el registro de entradas y salidas;
- dificultad para identificar medicamentos proximos a vencer;
- falta de trazabilidad sobre los movimientos;
- dificultad para detectar niveles bajos de inventario;
- informacion dispersa;
- dificultad para generar reportes confiables.

MediStock busca reducir estos problemas mediante un sistema centralizado de gestion de inventario.

## 5. Que cambia cuando atravesamos una red

Un sistema distribuido esta formado por componentes independientes que necesitan comunicarse mediante mensajes a traves de una red.

Cuando una operacion atraviesa una frontera de red dejan de existir algunas garantias que normalmente se dan por sentadas dentro de un unico proceso. Principalmente debemos considerar estado, tiempo y fallos.

### Estado

Dos componentes diferentes no comparten automaticamente la misma memoria. Cada componente puede observar estados diferentes del sistema.

### Tiempo

No existe un reloj global perfectamente sincronizado entre todos los nodos. Por esta razon no siempre es seguro utilizar unicamente la hora del sistema para determinar el orden causal de eventos distribuidos.

### Fallos

Los componentes pueden fallar independientemente.

```text
Frontend disponible
Backend disponible
Base de datos no disponible
```

El sistema no necesariamente falla completamente cuando uno de sus componentes presenta un problema.

## 6. Falacias de los sistemas distribuidos

Al disenar MediStock no debemos asumir que:

- la red siempre estara disponible;
- la latencia sera cero;
- el ancho de banda sera infinito;
- la red sera completamente segura;
- la infraestructura permanecera siempre igual;
- todos los componentes seran administrados de la misma manera;
- la comunicacion sera gratuita;
- todos los sistemas seran homogeneos.

Estas suposiciones pueden provocar disenos poco tolerantes a fallos. Aunque MediStock inicialmente utilizara una arquitectura de monolito modular, estas consideraciones seran importantes cuando la aplicacion se comunique con otros procesos o recursos mediante red.

## 7. Modelo de sistema y modelo de fallos

Para MediStock se considera inicialmente un entorno de red **asincrono**.

Esto significa que no podemos garantizar un tiempo maximo fijo para:

- recibir una peticion;
- procesarla;
- recibir una respuesta.

Para los fallos se consideran principalmente los siguientes escenarios.

### Recuperacion despues de caida

Un componente puede detenerse y posteriormente volver a funcionar.

```text
Base de datos
    |
Se detiene
    |
Se reinicia
    |
Vuelve a operar
```

### Fallo por omision

Un mensaje puede no llegar a su destino.

```text
Usuario
  |
  | Registrar salida
  v
MediStock
  |
  X Respuesta no recibida
```

El usuario podria intentar nuevamente la operacion. Este escenario es especialmente importante para las entradas y salidas de inventario.

## 8. Tiempo, causalidad y relojes logicos

En sistemas distribuidos no debe asumirse que el reloj fisico de diferentes nodos esta perfectamente sincronizado.

Para razonar sobre el orden de eventos se utiliza el concepto de **causalidad**.

```text
A -> B
```

Si un evento A provoca un evento B, entonces A ocurrio causalmente antes que B.

Los relojes logicos, como los relojes de Lamport, permiten representar este orden sin depender exclusivamente de la hora fisica.

MediStock no necesita implementar relojes de Lamport durante esta primera version. Sin embargo, comprender este concepto sera importante si posteriormente existen varios procesos generando o procesando eventos de inventario.

## 9. Consistencia

La consistencia determina que tan actualizado debe estar el estado observado por diferentes componentes del sistema. No todas las operaciones necesitan el mismo nivel de consistencia.

### Consistencia fuerte

Una operacion debe observar el estado mas reciente antes de tomar una decision. En MediStock esto es especialmente importante para las modificaciones de inventario.

```text
Stock disponible = 10
Salida solicitada = 8
```

Antes de registrar la salida, MediStock debe garantizar que las 10 unidades siguen disponibles. No seria aceptable permitir simultaneamente otra operacion que produzca un stock negativo.

Por esta razon las entradas y, especialmente, las salidas de inventario deben mantener consistencia fuerte.

### Consistencia eventual

Algunos datos pueden admitir una pequena demora antes de reflejar el ultimo estado.

```text
Stock actualizado
    |
Alerta de stock bajo
```

La alerta podria generarse inmediatamente o algunos segundos despues sin alterar la exactitud de la operacion que modifico el inventario.

## 10. CAP y PACELC

CAP establece que cuando existe una particion de red debe elegirse entre priorizar consistencia o disponibilidad. La tolerancia a particiones debe considerarse porque la comunicacion mediante red puede fallar.

Para operaciones criticas de MediStock, como registrar una salida, se priorizara la correccion del inventario. Es preferible rechazar temporalmente una operacion antes que registrar una salida utilizando informacion incorrecta.

PACELC amplia este razonamiento indicando que incluso cuando no existe una particion tambien existe un compromiso entre latencia y consistencia.

```text
Latencia <-> Consistencia
```

Para MediStock no todas las operaciones tendran necesariamente la misma eleccion.

## 11. Replicacion y particionamiento

La replicacion consiste en mantener varias copias de determinados datos para mejorar disponibilidad o capacidad de lectura. El particionamiento divide los datos para distribuirlos.

Durante la Semana 1 MediStock **no implementara replicacion ni particionamiento**. El volumen y necesidades actuales del proyecto todavia no justifican agregar esta complejidad.

Estos conceptos se conservaran como conocimiento de diseno para evaluar futuras necesidades de escalabilidad.

## 12. Consenso

El consenso permite que varios nodos lleguen a un acuerdo sobre un valor u orden de operaciones a pesar de ciertos fallos. Protocolos como Raft utilizan un lider y replicas para mantener un registro consistente entre varios nodos.

MediStock no implementara directamente un algoritmo de consenso durante esta etapa. Si en el futuro se utiliza infraestructura distribuida que requiera consenso, normalmente dicha responsabilidad sera proporcionada por la tecnologia seleccionada y no implementada manualmente dentro del dominio de MediStock.

## 13. Comunicacion sincrona y asincrona

### Comunicacion sincrona

El emisor realiza una peticion y espera una respuesta.

```text
Frontend
  |
  | HTTP
  v
MediStock
  |
  v
Respuesta
```

Este sera inicialmente el mecanismo natural para operaciones como:

- consultar medicamentos;
- registrar medicamentos;
- registrar entradas;
- registrar salidas;
- consultar inventario.

### Comunicacion asincrona

El emisor produce informacion sin necesitar que otro componente termine inmediatamente el procesamiento.

```text
Movimiento de inventario
    |
Evento
    |
Generacion de alerta
```

Podria utilizarse posteriormente para funciones como generacion de alertas o reportes. Sin embargo, todavia no existe una necesidad que justifique incorporar Kafka, RabbitMQ u otro broker.

Esa decision se evaluara unicamente si aparece un problema concreto que requiera procesamiento asincrono.

## 14. Semanticas de entrega

En comunicacion distribuida pueden existir diferentes garantias.

### Como maximo una vez

Una operacion se procesa como maximo una vez, aunque existe la posibilidad de perderla.

### Al menos una vez

La operacion se intenta hasta conseguir procesarla, pero puede llegar mas de una vez.

### Efecto equivalente a exactamente una vez

En una red no debe suponerse ingenuamente que un mensaje sera entregado exactamente una vez.

En la practica se busca conseguir un efecto equivalente mediante mecanismos como:

- identificadores unicos;
- idempotencia;
- deduplicacion;
- transacciones.

## 15. Idempotencia en MediStock

La idempotencia sera especialmente importante para movimientos de inventario.

Supongamos:

```text
Stock inicial = 100
Salida solicitada = 10
Stock despues de procesar = 90
```

Si la respuesta se pierde debido a un problema de red, el usuario puede volver a enviar la misma peticion.

Sin idempotencia:

```text
100 -> 90 -> 80
```

Resultado incorrecto.

Con una clave de idempotencia:

```text
Movimiento: MOV-001
Primera peticion MOV-001 -> procesada
Segunda peticion MOV-001 -> ya existe -> no se vuelve a aplicar
```

Resultado correcto:

```text
Stock = 90
```

Por esta razon las operaciones criticas de inventario deberan disenarse para evitar efectos duplicados. La implementacion concreta se realizara en una etapa posterior.

## 16. Consistencia y entrega de las operaciones principales

| Operacion | Consistencia requerida | Comunicacion inicial | Consideracion de entrega | Justificacion |
| --- | --- | --- | --- | --- |
| Consultar medicamentos | Lectura actualizada | Sincrona | Reintento seguro | No modifica estado |
| Registrar medicamento | Fuerte | Sincrona | Evitar duplicados | No deben crearse medicamentos duplicados por reintentos |
| Registrar lote | Fuerte | Sincrona | Idempotente | Un reintento no debe duplicar el lote |
| Registrar entrada | Fuerte | Sincrona | Al menos una vez con idempotencia | Una peticion repetida no debe aumentar dos veces las existencias |
| Registrar salida | Fuerte | Sincrona | Al menos una vez con idempotencia | Evita dobles descuentos y stock negativo |
| Consultar inventario | Fuerte para decisiones operativas | Sincrona | Reintento seguro | El encargado necesita conocer existencias reales |
| Generar alerta de stock | Eventual aceptable | Potencialmente asincrona | Idempotente | Una demora pequena no altera el movimiento que produjo la alerta |
| Generar alerta de vencimiento | Eventual aceptable | Potencialmente asincrona | Idempotente | Puede calcularse sin bloquear operaciones principales |
| Consultar reportes | Segun el reporte | Sincrona | Reintento seguro | Normalmente es una operacion de lectura |
| Gestionar usuarios y roles | Fuerte | Sincrona | Evitar operaciones duplicadas | Los permisos deben reflejar correctamente el estado autorizado |

Estas decisiones son iniciales y podran evolucionar conforme se defina la arquitectura detallada del sistema.

## 17. Backlog inicial de MediStock

Durante esta sesion se establece un primer backlog del producto. Todavia no se realizaran estimaciones ni planificacion detallada de iteracion; estos aspectos se trabajaran cuando corresponda segun el contenido del curso.

| ID | Historia inicial | Resultado esperado |
| --- | --- | --- |
| PB-001 | Como usuario quiero iniciar sesion para acceder a las funciones permitidas segun mi rol | Acceso controlado al sistema |
| PB-002 | Como administrador quiero registrar medicamentos para mantener actualizado el catalogo | Medicamentos disponibles para gestionar |
| PB-003 | Como administrador quiero modificar informacion de medicamentos | Informacion actualizada |
| PB-004 | Como encargado quiero registrar lotes para controlar cantidades y vencimientos | Trazabilidad por lote |
| PB-005 | Como encargado quiero registrar entradas de medicamentos | Incremento controlado de existencias |
| PB-006 | Como encargado quiero registrar salidas de medicamentos | Disminucion controlada de existencias |
| PB-007 | Como usuario autorizado quiero consultar existencias | Conocer el inventario disponible |
| PB-008 | Como encargado quiero recibir alertas de stock bajo | Detectar necesidades de reposicion |
| PB-009 | Como encargado quiero identificar lotes proximos a vencer | Reducir perdidas por vencimiento |
| PB-010 | Como administrador quiero gestionar proveedores | Mantener informacion de abastecimiento |
| PB-011 | Como supervisor quiero consultar movimientos de inventario | Obtener trazabilidad |
| PB-012 | Como supervisor quiero generar reportes | Analizar el estado y movimientos del inventario |
| PB-013 | Como administrador quiero gestionar usuarios y roles | Controlar permisos |
| PB-014 | Como usuario quiero buscar medicamentos y lotes | Encontrar informacion rapidamente |

Este backlog es inicial y podra ser refinado durante las siguientes sesiones.

## 18. Analisis inicial de riesgos distribuidos

Aunque MediStock todavia no posee una implementacion distribuida completa, desde el diseno deben considerarse algunos escenarios.

| Riesgo | Ejemplo en MediStock | Consecuencia |
| --- | --- | --- |
| Peticion duplicada | Registrar dos veces una salida despues de un tiempo de espera agotado | Inventario incorrecto |
| Peticion perdida | Una entrada no llega al backend | Movimiento no registrado |
| Respuesta perdida | La operacion se ejecuta pero el usuario no recibe confirmacion | El usuario puede repetirla |
| Concurrencia | Dos usuarios retiran simultaneamente el mismo medicamento | Stock negativo |
| Dependencia caida | Base de datos temporalmente no disponible | Operacion no puede completarse |
| Datos desactualizados | Se consulta una existencia antigua | Decision incorrecta |

Estos escenarios deberan influir posteriormente en las decisiones de implementacion.

## 19. Decisiones tomadas durante la sesion

### D-001 - El inventario requiere consistencia fuerte

Las entradas y salidas modifican informacion critica del dominio. Especialmente las salidas deben impedir que las existencias terminen por debajo de cero.

### D-002 - Las operaciones de inventario deben disenarse considerando idempotencia

Una peticion repetida debido a fallos de red no debe producir el movimiento dos veces.

### D-003 - Las alertas pueden admitir consistencia eventual

La generacion de una alerta no debe bloquear innecesariamente la operacion principal de inventario.

### D-004 - No se implementaran tecnologias distribuidas innecesarias

Durante esta etapa no existe una justificacion suficiente para introducir Kafka, RabbitMQ, Redis o Kubernetes. Cualquier tecnologia adicional debera resolver una necesidad concreta.

### D-005 - La arquitectura objetivo continua siendo monolito modular

Esta decision es una restriccion inicial del proyecto. La comparacion formal con otras alternativas y su documentacion mediante ADR se realizara cuando el contenido correspondiente del curso sea abordado.

## 20. Resultado de la sesion

Al finalizar la Semana 1 - Sesion 1 se cuenta con:

- problema real definido: gestion de inventario de medicamentos;
- responsable definido: Julian Alberto Trujillo Bonilla;
- backlog inicial creado;
- operaciones principales identificadas;
- necesidades de consistencia analizadas;
- semanticas de entrega analizadas;
- riesgos de comunicacion distribuida identificados;
- arquitectura objetivo definida como monolito modular, pendiente de justificacion formal.

## 21. Que debo poder explicar al profesor

La idea principal de esta sesion es responder:

**Por que una salida de inventario necesita consistencia fuerte, pero una alerta puede ser eventual?**

Una salida incorrecta modifica el estado real del inventario y podria permitir stock negativo o cantidades inconsistentes. En cambio, una alerta puede aparecer con una pequena demora sin alterar la integridad del inventario.

## 22. Conclusion

La Semana 1 - Sesion 1 permite establecer la base conceptual de MediStock desde la perspectiva de Sistemas Distribuidos.

La principal conclusion es que distribuir un sistema introduce problemas que no aparecen de la misma manera dentro de un unico proceso: fallos parciales, mensajes duplicados, latencia, concurrencia, ausencia de un reloj global y diferentes necesidades de consistencia.

En MediStock, las operaciones relacionadas con las existencias requieren especial atencion. Registrar entradas y salidas debe preservar la integridad del inventario incluso frente a concurrencia, reintentos y posibles fallos de comunicacion.

Otros procesos, como la generacion de alertas, pueden tolerar una consistencia mas debil siempre que la informacion critica del inventario permanezca correcta.

Estas decisiones serviran de base para el diseno arquitectonico y la implementacion de los siguientes incrementos del proyecto.
