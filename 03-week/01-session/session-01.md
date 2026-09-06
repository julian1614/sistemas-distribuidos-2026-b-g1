# MediStock - Semana 3 - Sesion 1

## Diseno de dominio y arquitectura hexagonal

**Proyecto:** MediStock  
**Responsable:** Julian Alberto Trujillo Bonilla  
**Programa:** Ingenieria de Sistemas  
**Asignatura:** Sistemas Distribuidos  
**Rama de trabajo:** feature/week3-session1-domain-hexagonal-design-dev  
**Arquitectura definida:** Monolito modular  
**ADR relacionado:** `docs/adr/ADR-001-monolito-modular.md`

## 1. Objetivo de la sesion

Definir un diseno inicial del dominio de MediStock y relacionarlo con la arquitectura hexagonal, tomando como base los contextos delimitados y la decision arquitectonica documentada en la Semana 2.

Durante esta sesion se trabajan:

- modelo de dominio inicial;
- entidades principales;
- agregados candidatos;
- reglas de negocio por contexto;
- casos de uso principales;
- puertos y adaptadores conceptuales;
- decisiones iniciales de diseno.

Esta sesion todavia no crea el servidor de aplicacion fisico. La implementacion se realizara en la Semana 4.

## 2. Punto de partida

En la Semana 2 se definio que MediStock usara monolito modular como arquitectura inicial. Tambien se identificaron contextos delimitados:

- Acceso e identidad;
- Catalogo;
- Inventario;
- Proveedores;
- Alertas;
- Reportes.

Ahora se empieza a convertir esa division conceptual en un diseno de dominio mas concreto.

La pregunta principal de esta sesion es:

**Como se deben organizar las reglas y conceptos del dominio para que MediStock pueda implementarse sin mezclar negocio e infraestructura?**

## 3. Modelo de dominio inicial

El modelo de dominio representa los conceptos importantes del negocio. En MediStock, estos conceptos se relacionan con la gestion de inventario de medicamentos.

Conceptos iniciales:

| Concepto | Contexto | Descripcion |
| --- | --- | --- |
| Medicamento | Catalogo | Producto que se registra y consulta dentro del sistema |
| Lote | Inventario | Grupo de unidades de un medicamento con numero de lote y fecha de vencimiento |
| Movimiento de inventario | Inventario | Registro historico de una entrada o salida |
| Proveedor | Proveedores | Entidad que suministra medicamentos |
| Usuario | Acceso e identidad | Persona que utiliza el sistema |
| Rol | Acceso e identidad | Conjunto de permisos asignados a un usuario |
| Alerta | Alertas | Aviso generado por stock bajo o vencimiento proximo |
| Reporte | Reportes | Consulta organizada sobre el estado del inventario |

## 4. Lenguaje ubicuo inicial

El lenguaje ubicuo permite que los terminos del proyecto tengan el mismo significado en documentacion, diseno e implementacion.

| Termino | Significado en MediStock |
| --- | --- |
| Stock | Cantidad disponible de un medicamento o lote |
| Stock minimo | Cantidad limite para generar alerta de reposicion |
| Entrada | Movimiento que aumenta existencias |
| Salida | Movimiento que disminuye existencias |
| Lote vencido | Lote cuya fecha de vencimiento ya paso |
| Lote proximo a vencer | Lote que se acerca a su fecha de vencimiento |
| Movimiento | Registro auditable de una entrada o salida |
| Responsable | Usuario que realiza una operacion |
| Alerta | Aviso derivado del estado del inventario |

Este lenguaje debe mantenerse consistente en documentos, nombres de casos de uso y futura implementacion.

## 5. Entidades principales

### Medicamento

Representa el producto base que se administra en el sistema.

Atributos iniciales:

- identificador;
- nombre;
- principio activo;
- presentacion;
- concentracion;
- laboratorio;
- categoria;
- stock minimo;
- estado.

Reglas asociadas:

- debe tener nombre obligatorio;
- debe estar activo para recibir operaciones normales;
- puede tener varios lotes;
- puede generar alerta cuando su inventario este por debajo del minimo.

### Lote

Representa un conjunto de unidades asociadas a un medicamento.

Atributos iniciales:

- identificador;
- numero de lote;
- medicamento asociado;
- fecha de vencimiento;
- cantidad disponible;
- proveedor;
- fecha de ingreso;
- estado.

Reglas asociadas:

- debe tener numero de lote;
- debe estar asociado a un medicamento;
- no puede tener cantidad negativa;
- debe permitir identificar vencimientos.

### Movimiento de inventario

Representa una entrada o salida del inventario.

Atributos iniciales:

- identificador;
- tipo de movimiento;
- medicamento;
- lote;
- cantidad;
- fecha;
- motivo;
- usuario responsable;
- clave de idempotencia futura.

Reglas asociadas:

- todo movimiento debe conservarse como historial;
- una salida no puede superar la cantidad disponible;
- una entrada aumenta existencias;
- una salida disminuye existencias;
- una peticion repetida no debe duplicar el efecto del movimiento.

### Proveedor

Representa la entidad que suministra medicamentos.

Atributos iniciales:

- identificador;
- nombre;
- NIT o identificacion;
- telefono;
- correo;
- direccion;
- estado.

### Usuario y rol

Representan el acceso al sistema.

Reglas asociadas:

- solo usuarios autorizados pueden modificar inventario;
- los permisos dependen del rol;
- las operaciones importantes deben registrar el usuario responsable.

### Alerta

Representa un aviso derivado del estado del inventario.

Tipos iniciales:

- alerta de stock bajo;
- alerta de vencimiento proximo.

Las alertas pueden admitir consistencia eventual porque no modifican directamente las existencias.

## 6. Agregados candidatos

Un agregado agrupa entidades y reglas que deben mantenerse consistentes.

Para MediStock se proponen los siguientes agregados candidatos:

| Agregado candidato | Entidades relacionadas | Regla principal |
| --- | --- | --- |
| Medicamento | Medicamento | Mantener informacion descriptiva y stock minimo |
| Lote | Lote, medicamento relacionado | Controlar cantidad disponible y vencimiento |
| Movimiento de inventario | Movimiento, lote, usuario responsable | Registrar entradas y salidas sin perder trazabilidad |
| Usuario | Usuario, rol | Controlar permisos de operacion |

Esta propuesta puede cambiar en la siguiente sesion, cuando se trabaje el diseno de datos y contratos.

## 7. Fuente de verdad del inventario

Una decision importante es definir donde vive la cantidad real disponible.

El documento inicial menciona cantidad disponible en medicamento y cantidad por lote. Esto puede generar inconsistencias si ambos valores se modifican de forma independiente.

Ejemplo de riesgo:

```text
Medicamento.stock = 100
Lote A = 40
Lote B = 30
Total por lotes = 70
```

Para evitar esta inconsistencia, la fuente de verdad inicial propuesta sera:

**El stock real se calcula y controla desde los lotes y movimientos de inventario.**

El valor total de un medicamento puede consultarse como una suma derivada de sus lotes disponibles.

Esta decision protege la consistencia y evita duplicar estado critico.

## 8. Reglas por contexto

### Acceso e identidad

- Validar usuarios.
- Asociar roles y permisos.
- Restringir operaciones criticas.
- Registrar responsable de operaciones importantes.

### Catalogo

- Registrar medicamentos.
- Mantener informacion descriptiva.
- Evitar medicamentos duplicados.
- Definir stock minimo.

### Inventario

- Registrar lotes.
- Registrar entradas.
- Registrar salidas.
- Validar disponibilidad.
- Evitar stock negativo.
- Conservar movimientos.
- Proteger operaciones repetidas mediante idempotencia futura.

### Proveedores

- Registrar proveedores.
- Asociar proveedores con lotes o entradas.
- Mantener informacion de abastecimiento.

### Alertas

- Detectar stock bajo.
- Detectar vencimientos proximos.
- No bloquear operaciones criticas de inventario.

### Reportes

- Consultar inventario.
- Consultar vencimientos.
- Consultar movimientos.
- No modificar datos del dominio.

## 9. Casos de uso principales

Los casos de uso representan acciones del sistema orientadas al negocio.

| Caso de uso | Contexto | Resultado esperado |
| --- | --- | --- |
| Registrar medicamento | Catalogo | Medicamento disponible en el catalogo |
| Registrar lote | Inventario | Lote asociado a un medicamento |
| Registrar entrada | Inventario | Existencias aumentadas y movimiento registrado |
| Registrar salida | Inventario | Existencias disminuidas y movimiento registrado |
| Consultar inventario | Inventario | Existencias disponibles por medicamento o lote |
| Generar alerta de stock bajo | Alertas | Aviso de reposicion |
| Generar alerta de vencimiento | Alertas | Aviso de lote proximo a vencer |
| Gestionar proveedor | Proveedores | Proveedor disponible para entradas |
| Gestionar usuario y rol | Acceso e identidad | Acceso controlado por permisos |
| Consultar reportes | Reportes | Informacion consolidada del sistema |

## 10. Arquitectura hexagonal conceptual

La arquitectura hexagonal permite separar el dominio de los detalles externos.

En MediStock se propone la siguiente separacion conceptual:

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

## 11. Puertos de entrada

Los puertos de entrada representan operaciones que el sistema ofrece.

Puertos candidatos:

- registrar medicamento;
- registrar lote;
- registrar entrada de inventario;
- registrar salida de inventario;
- consultar inventario;
- consultar movimientos;
- generar alertas;
- gestionar proveedor;
- gestionar usuarios y roles.

En la implementacion futura, estos puertos podran ser invocados desde controladores HTTP u otros adaptadores de entrada.

## 12. Puertos de salida

Los puertos de salida representan necesidades del dominio hacia el exterior.

Puertos candidatos:

- guardar medicamento;
- consultar medicamento;
- guardar lote;
- consultar lotes disponibles;
- guardar movimiento de inventario;
- consultar movimientos;
- guardar proveedor;
- consultar usuario y permisos;
- guardar alerta;
- consultar informacion para reportes.

En la implementacion futura, estos puertos podran conectarse con base de datos u otros mecanismos de persistencia.

## 13. Adaptadores futuros

Los adaptadores son detalles tecnicos que se conectan con los puertos.

Adaptadores de entrada futuros:

- controladores HTTP;
- interfaz web;
- comandos administrativos.

Adaptadores de salida futuros:

- repositorios de base de datos;
- generador de reportes;
- servicio de notificaciones;
- almacenamiento de auditoria;
- mensajeria asincrona si llega a justificarse.

## 14. Decisiones de diseno

### D-001 - El inventario concentra las reglas criticas

Las reglas de entradas, salidas, lotes, existencias y movimientos deben estar en el contexto de inventario.

### D-002 - El stock total no debe duplicarse sin control

La cantidad total de un medicamento debe derivarse de lotes y movimientos, o mantenerse sincronizada mediante una regla clara. La propuesta inicial es derivarla desde los lotes.

### D-003 - Los movimientos son historial

Los movimientos de inventario no deben eliminarse directamente porque sirven como trazabilidad.

### D-004 - Las alertas no bloquean inventario

Las alertas pueden generarse despues de la operacion principal, siempre que el inventario quede correcto.

### D-005 - El dominio no depende de infraestructura

La base de datos, los controladores y otros detalles tecnicos deben depender de contratos definidos por la aplicacion o el dominio, no al contrario.

## 15. Relacion con la arquitectura definida

El diseno de esta sesion respeta el ADR-001 porque mantiene a MediStock como monolito modular.

Cada contexto delimitado puede convertirse mas adelante en un modulo interno del monolito. Esto permite organizar el sistema sin crear microservicios prematuros.

La arquitectura hexagonal ayuda a proteger el dominio dentro de cada modulo o area funcional.

## 16. Que no se implementa en esta sesion

Durante esta sesion no se crea:

- proyecto del servidor de aplicacion;
- estructura de codigo fuente;
- base de datos;
- controladores;
- repositorios reales;
- pruebas automatizadas;
- contenedores.

La implementacion fisica del servidor de aplicacion se realizara en la Semana 4.

## 17. Resultado de la sesion

Al finalizar la Semana 3 - Sesion 1 se cuenta con:

- modelo de dominio inicial;
- lenguaje ubicuo inicial;
- entidades principales documentadas;
- agregados candidatos;
- propuesta de fuente de verdad del inventario;
- reglas de negocio por contexto;
- casos de uso principales;
- puertos de entrada conceptuales;
- puertos de salida conceptuales;
- adaptadores futuros identificados;
- decisiones iniciales de diseno.

## 18. Que debo poder explicar al profesor

La idea principal de esta sesion es responder:

**Por que el dominio de MediStock debe separarse de la infraestructura?**

Porque las reglas criticas del negocio, como evitar stock negativo, registrar movimientos y validar salidas, deben mantenerse correctas sin depender de la base de datos, controladores o interfaz. La arquitectura hexagonal permite proteger esas reglas y conectar los detalles tecnicos mediante puertos y adaptadores.

## 19. Conclusion

La Semana 3 - Sesion 1 convierte la decision arquitectonica en un diseno de dominio mas concreto.

MediStock empieza a organizar sus reglas, entidades, casos de uso y puertos conceptuales sin adelantar implementacion. Esto prepara el proyecto para disenar datos, contratos y estructura tecnica en las siguientes sesiones, manteniendo coherencia con el monolito modular aprobado en el ADR-001.
