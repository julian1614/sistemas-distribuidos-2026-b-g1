# MediStock - Semana 2 - Sesion 1

## Estudio de alternativas arquitectonicas

**Proyecto:** MediStock  
**Responsable:** Julian Alberto Trujillo Bonilla  
**Programa:** Ingenieria de Sistemas  
**Asignatura:** Sistemas Distribuidos  
**Rama de trabajo:** feature/week2-session1-architecture-study-dev  
**Arquitectura objetivo:** Monolito modular  
**Estado de la decision:** Recomendacion inicial. La decision formal se documentara mediante ADR en una sesion posterior.

## 1. Objetivo de la sesion

Analizar diferentes alternativas arquitectonicas para MediStock y evaluar cual se ajusta mejor al problema, al alcance actual, al tamano del proyecto y a los riesgos identificados en las sesiones anteriores.

Durante esta sesion se estudian:

- monolito tradicional;
- monolito modular;
- arquitectura cliente-servidor;
- arquitectura por capas;
- arquitectura orientada a servicios;
- microservicios;
- arquitectura orientada a eventos;
- criterios de comparacion arquitectonica.

El objetivo no es implementar la arquitectura todavia, sino justificar por que MediStock no debe adoptar una arquitectura compleja antes de necesitarla.

## 2. Punto de partida

En la Semana 1 se definio que MediStock es un sistema de gestion de inventario de medicamentos. Tambien se identificaron reglas criticas del dominio, especialmente:

- no permitir stock negativo;
- no permitir salidas superiores al inventario disponible;
- conservar historial de movimientos;
- controlar lotes y vencimientos;
- restringir operaciones a usuarios autorizados;
- generar alertas de stock bajo y vencimiento.

Desde la perspectiva de sistemas distribuidos, se identifico que las operaciones de inventario necesitan consistencia fuerte y que los reintentos deben manejarse cuidadosamente para evitar movimientos duplicados.

La pregunta de esta sesion es:

**Que alternativa arquitectonica permite construir MediStock de forma clara, mantenible y justificable sin agregar complejidad innecesaria?**

## 3. Necesidades arquitectonicas de MediStock

MediStock necesita una arquitectura que permita:

- separar responsabilidades del negocio;
- proteger las reglas criticas del inventario;
- facilitar pruebas futuras;
- mantener trazabilidad de movimientos;
- permitir crecimiento gradual;
- conservar una estructura comprensible para un desarrollador individual;
- evitar sobreingenieria;
- permitir integracion futura con base de datos, API, contenedores o mensajeria si el proyecto lo requiere.

La arquitectura debe responder al contexto real del proyecto, no solamente a una moda tecnologica.

## 4. Criterios de evaluacion

Para comparar alternativas se utilizaran los siguientes criterios:

| Criterio | Descripcion |
| --- | --- |
| Simplicidad | Que tan facil es comprender, implementar y mantener la solucion |
| Separacion de responsabilidades | Que tan bien permite aislar reglas de negocio y detalles tecnicos |
| Escalabilidad futura | Que tan bien permite crecer sin redisenar todo el sistema |
| Consistencia | Que tan facil es proteger operaciones criticas del inventario |
| Costo operativo | Cantidad de infraestructura, despliegue y monitoreo requerido |
| Adecuacion al equipo | Que tan apropiada es para un proyecto desarrollado por una sola persona |
| Riesgo academico | Que tan defendible es la decision frente al contenido del curso |

## 5. Alternativa 1: monolito tradicional

Un monolito tradicional concentra toda la aplicacion en una sola unidad de despliegue. Puede ser simple al inicio, pero suele mezclar responsabilidades si no se disena cuidadosamente.

### Ventajas

- Es facil de iniciar.
- Requiere poca infraestructura.
- Permite desarrollar rapido en etapas tempranas.
- Facilita transacciones internas dentro de una misma aplicacion.

### Desventajas

- Puede mezclar reglas de negocio con controladores, base de datos e interfaz.
- Puede crecer de forma desordenada.
- Dificulta separar responsabilidades si no hay disciplina de diseno.
- Puede volverse dificil de mantener.

### Aplicacion a MediStock

MediStock podria comenzar como monolito tradicional, pero existe el riesgo de que las reglas de inventario queden mezcladas con detalles tecnicos. Esto seria delicado porque el dominio necesita proteger reglas como evitar stock negativo y conservar movimientos.

## 6. Alternativa 2: monolito modular

Un monolito modular tambien se despliega como una sola aplicacion, pero se organiza internamente por modulos con responsabilidades claras.

### Ventajas

- Mantiene baja complejidad operativa.
- Permite separar responsabilidades por dominio.
- Facilita evolucionar por partes.
- Es adecuado para un desarrollador individual.
- Permite aplicar arquitectura hexagonal dentro de cada modulo o area del sistema.
- Evita introducir comunicacion distribuida innecesaria entre componentes internos.

### Desventajas

- Requiere disciplina para respetar limites internos.
- Si los modulos se acoplan demasiado, puede degradarse a un monolito desordenado.
- No escala equipos o despliegues de forma independiente como los microservicios.

### Aplicacion a MediStock

Esta alternativa se ajusta bien porque MediStock tiene areas diferenciables:

```text
MediStock
+-- Acceso e identidad
+-- Catalogo
+-- Inventario
+-- Proveedores
+-- Alertas
+-- Reportes
```

El modulo de inventario podria concentrar las reglas mas criticas: entradas, salidas, lotes, movimientos y consistencia de existencias.

## 7. Alternativa 3: cliente-servidor

La arquitectura cliente-servidor separa un cliente que solicita operaciones y un servidor que procesa reglas, datos y respuestas.

### Ventajas

- Es una base natural para aplicaciones web.
- Separa la interfaz del procesamiento principal.
- Permite que varios usuarios accedan al sistema desde diferentes clientes.

### Desventajas

- Introduce una frontera de red.
- Puede aparecer latencia, perdida de respuestas o reintentos.
- Requiere manejar errores de comunicacion.

### Aplicacion a MediStock

MediStock probablemente utilizara una forma cliente-servidor cuando tenga interfaz o API. Sin embargo, esto describe la comunicacion externa, no reemplaza la decision interna sobre como organizar el servidor de aplicacion.

Por eso, cliente-servidor puede coexistir con monolito modular:

```text
Cliente
  |
  | HTTP
  v
MediStock como monolito modular
  |
  v
Base de datos
```

## 8. Alternativa 4: arquitectura por capas

La arquitectura por capas organiza el sistema en niveles como presentacion, aplicacion, dominio e infraestructura.

### Ventajas

- Es facil de entender.
- Ayuda a separar responsabilidades.
- Es comun en sistemas empresariales.
- Puede combinarse con arquitectura hexagonal.

### Desventajas

- Si se aplica de forma rigida, puede producir dependencias incorrectas.
- A veces la logica de negocio termina dispersa en servicios o controladores.
- No define por si sola limites de dominio.

### Aplicacion a MediStock

La arquitectura por capas puede ser util como guia, siempre que el dominio no dependa de infraestructura. Para MediStock, sera mejor combinarla con ideas de arquitectura hexagonal y modulos conceptuales.

## 9. Alternativa 5: arquitectura orientada a servicios

La arquitectura orientada a servicios propone organizar el sistema alrededor de servicios que exponen capacidades del negocio.

### Ventajas

- Permite pensar en capacidades del negocio.
- Puede facilitar integraciones futuras.
- Ayuda a separar responsabilidades si se disena correctamente.

### Desventajas

- Puede introducir complejidad de comunicacion.
- Puede requerir contratos, versionamiento y coordinacion entre servicios.
- No siempre es necesaria para proyectos pequenos o medianos.

### Aplicacion a MediStock

MediStock puede aprender de esta alternativa para identificar capacidades como inventario, catalogo o reportes. Sin embargo, no se justifica separar esas capacidades en servicios independientes durante esta etapa.

## 10. Alternativa 6: microservicios

Los microservicios dividen el sistema en servicios pequenos, desplegables de forma independiente y comunicados mediante red.

### Ventajas

- Permiten despliegue independiente.
- Pueden escalar partes especificas del sistema.
- Facilitan autonomia entre equipos grandes.
- Pueden aislar fallos si se disenan y operan correctamente.

### Desventajas

- Aumentan mucho la complejidad operativa.
- Requieren observabilidad, monitoreo, despliegue y pruebas distribuidas.
- Introducen fallos de red entre componentes internos.
- Dificultan transacciones y consistencia entre servicios.
- Pueden ser excesivos para un proyecto individual.

### Aplicacion a MediStock

MediStock no necesita microservicios en esta etapa. El proyecto sera desarrollado por una sola persona y todavia no existe una carga, escala o necesidad organizacional que justifique separar el sistema en servicios independientes.

Adoptar microservicios demasiado pronto podria empeorar justamente los riesgos ya identificados:

- mas fronteras de red;
- mas mensajes duplicados;
- mas latencia;
- mas fallos parciales;
- mayor dificultad para garantizar consistencia en inventario.

## 11. Alternativa 7: arquitectura orientada a eventos

La arquitectura orientada a eventos permite que partes del sistema reaccionen a hechos ocurridos, como movimientos de inventario o cambios de stock.

### Ventajas

- Facilita procesos asincronos.
- Puede desacoplar la generacion de alertas.
- Permite reaccionar a eventos del dominio.
- Puede ser util para reportes o integraciones futuras.

### Desventajas

- Requiere disenar eventos, consumidores y manejo de fallos.
- Puede introducir consistencia eventual.
- Puede complicar trazabilidad si no se documenta bien.
- Puede requerir infraestructura adicional.

### Aplicacion a MediStock

MediStock podria usar eventos mas adelante para alertas o reportes. Por ejemplo:

```text
Movimiento de inventario registrado
  |
  v
Evaluar alerta de stock bajo
```

Sin embargo, durante esta etapa no se justifica incorporar mensajeria asincrona. Las alertas pueden analizarse conceptualmente como procesos eventuales, pero su implementacion se decidira despues.

## 12. Comparacion de alternativas

| Alternativa | Simplicidad | Separacion | Costo operativo | Adecuacion actual | Observacion |
| --- | --- | --- | --- | --- | --- |
| Monolito tradicional | Alta | Baja a media | Bajo | Media | Facil de iniciar, pero riesgoso si crece sin limites |
| Monolito modular | Alta | Alta | Bajo | Alta | Mejor equilibrio para MediStock |
| Cliente-servidor | Media | Media | Medio | Alta | Necesario como forma de comunicacion externa |
| Arquitectura por capas | Alta | Media | Bajo | Alta | Util como apoyo, pero no suficiente sola |
| Servicios | Media | Alta | Medio | Media | Conceptualmente util, pero no necesaria aun |
| Microservicios | Baja | Alta | Alto | Baja | Excesiva para el estado actual del proyecto |
| Eventos | Media | Alta | Medio a alto | Media | Posible uso futuro para alertas o reportes |

## 13. Recomendacion inicial

La recomendacion inicial para MediStock es continuar con **monolito modular**, apoyado por conceptos de arquitectura hexagonal y separacion por dominio.

Esta opcion permite:

- mantener una sola unidad de despliegue;
- reducir complejidad operativa;
- separar responsabilidades internas;
- proteger reglas criticas del inventario;
- facilitar pruebas futuras;
- preparar el sistema para evolucionar sin adoptar microservicios prematuramente.

La decision formal se registrara posteriormente mediante un ADR, despues de completar la comparacion arquitectonica que corresponda en la siguiente sesion.

## 14. Riesgos y compromisos

| Riesgo | Impacto | Mitigacion |
| --- | --- | --- |
| Convertir el monolito modular en monolito desordenado | Alto | Definir limites de modulos y reglas de dependencia |
| Mezclar dominio con infraestructura | Alto | Aplicar arquitectura hexagonal |
| Adoptar microservicios demasiado pronto | Alto | Exigir justificacion concreta antes de separar servicios |
| Duplicar logica entre modulos | Medio | Centralizar reglas en el dominio correspondiente |
| No documentar decisiones | Medio | Registrar decisiones importantes mediante ADR |

## 15. Que se decide y que no se decide

| Tema | Estado |
| --- | --- |
| Evaluar alternativas arquitectonicas | Realizado |
| Recomendar monolito modular | Realizado |
| Adoptar microservicios | No recomendado en esta etapa |
| Incorporar mensajeria asincrona | No decidido |
| Crear ADR final de arquitectura | Pendiente para la siguiente sesion |
| Implementar estructura fisica del proyecto | Pendiente |
| Seleccionar base de datos | Pendiente |

## 16. Resultado de la sesion

Al finalizar la Semana 2 - Sesion 1 se cuenta con:

- alternativas arquitectonicas identificadas;
- criterios de evaluacion definidos;
- analisis de monolito tradicional;
- analisis de monolito modular;
- analisis de cliente-servidor;
- analisis de arquitectura por capas;
- analisis de servicios;
- analisis de microservicios;
- analisis de eventos;
- comparacion inicial de alternativas;
- recomendacion inicial de monolito modular;
- riesgos arquitectonicos documentados.

## 17. Que debo poder explicar al profesor

La idea principal de esta sesion es responder:

**Por que MediStock no debe iniciar con microservicios si la asignatura es Sistemas Distribuidos?**

Porque los microservicios agregan complejidad distribuida real: comunicacion entre servicios, fallos parciales, observabilidad, despliegues independientes, consistencia distribuida y pruebas mas dificiles. MediStock todavia no tiene escala, equipo ni necesidades operativas que justifiquen esa complejidad.

Un monolito modular permite aplicar buenas practicas de arquitectura, separar responsabilidades y preparar el proyecto para crecer, sin introducir fronteras de red innecesarias entre partes internas del sistema.

## 18. Conclusion

La Semana 2 - Sesion 1 permite comparar alternativas arquitectonicas y ubicar a MediStock en una decision proporcional a su contexto.

El proyecto necesita orden, separacion de responsabilidades y proteccion de reglas criticas de inventario, pero no necesita complejidad distribuida prematura. Por eso, la recomendacion inicial continua siendo monolito modular.

Esta sesion deja preparada la base para documentar formalmente la decision arquitectonica mediante un ADR en la siguiente sesion.
