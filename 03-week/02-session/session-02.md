# MediStock - Semana 3 - Sesion 2

## Diseno de modulos, datos y contratos

**Proyecto:** MediStock  
**Responsable:** Julian Alberto Trujillo Bonilla  
**Programa:** Ingenieria de Sistemas  
**Asignatura:** Sistemas Distribuidos  
**Rama de trabajo:** feature/week3-session2-modules-data-contracts-dev  
**Arquitectura definida:** Monolito modular  
**ADR relacionado:** `docs/adr/ADR-001-monolito-modular.md`

## 1. Objetivo de la sesion

Refinar el diseno de MediStock mediante la definicion de modulos internos, propiedad de datos, contratos conceptuales y modelo de datos preliminar.

Durante esta sesion se trabajan:

- modulos internos del monolito modular;
- propiedad de datos por modulo;
- contratos internos entre modulos;
- modelo de datos preliminar;
- reglas de integracion;
- preparacion para la implementacion futura.

Esta sesion todavia no crea el servidor de aplicacion. Su objetivo es dejar el diseno listo para iniciar la construccion en la Semana 4.

## 2. Punto de partida

En la Semana 3 - Sesion 1 se definio un modelo de dominio inicial con entidades, agregados candidatos, reglas por contexto, puertos de entrada, puertos de salida y adaptadores futuros.

La fuente de verdad inicial del inventario quedo orientada a lotes y movimientos, evitando duplicar sin control la cantidad total del medicamento.

La pregunta principal de esta sesion es:

**Como se deben organizar los modulos, datos y contratos para que MediStock pueda implementarse de forma ordenada en el monolito modular?**

## 3. Modulos internos propuestos

MediStock se organizara conceptualmente en los siguientes modulos internos:

```text
MediStock
+-- Acceso e identidad
+-- Catalogo
+-- Inventario
+-- Proveedores
+-- Alertas
+-- Reportes
```

Estos modulos pertenecen a una misma aplicacion, pero deben conservar responsabilidades separadas.

| Modulo | Responsabilidad principal |
| --- | --- |
| Acceso e identidad | Controlar usuarios, roles y permisos |
| Catalogo | Gestionar informacion descriptiva de medicamentos |
| Inventario | Gestionar lotes, entradas, salidas, existencias y movimientos |
| Proveedores | Gestionar informacion de proveedores |
| Alertas | Detectar condiciones de stock bajo y vencimiento |
| Reportes | Consultar informacion consolidada |

## 4. Propiedad de datos

Cada modulo debe tener claridad sobre los datos que controla. Esto evita que otro modulo modifique informacion sin respetar las reglas del contexto propietario.

| Modulo | Datos de los que es propietario |
| --- | --- |
| Acceso e identidad | usuarios, roles, permisos |
| Catalogo | medicamentos, categorias, laboratorios |
| Inventario | lotes, movimientos de inventario, cantidades por lote |
| Proveedores | proveedores |
| Alertas | alertas generadas, configuracion futura de alertas |
| Reportes | no es propietario principal; consulta informacion de otros modulos |

El modulo de reportes no debe modificar datos del dominio. Su responsabilidad es consultar y presentar informacion.

## 5. Reglas de propiedad

- El modulo propietario define como se crea, modifica o consulta su informacion.
- Ningun modulo debe modificar directamente datos de otro modulo.
- Las operaciones criticas deben pasar por casos de uso del modulo propietario.
- Inventario es el modulo responsable de validar existencias y movimientos.
- Catalogo no debe modificar lotes ni movimientos.
- Alertas puede leer informacion necesaria, pero no debe bloquear el registro de entradas o salidas.
- Reportes puede consultar informacion consolidada, pero no debe ejecutar reglas de negocio.

## 6. Modelo de datos preliminar

El modelo de datos preliminar se define para orientar la implementacion futura. No representa aun una base de datos creada fisicamente.

Tablas conceptuales:

| Tabla | Modulo propietario | Proposito |
| --- | --- | --- |
| usuarios | Acceso e identidad | Almacenar usuarios del sistema |
| roles | Acceso e identidad | Definir permisos por rol |
| medicamentos | Catalogo | Registrar informacion principal del medicamento |
| categorias | Catalogo | Clasificar medicamentos |
| laboratorios | Catalogo | Registrar fabricantes o laboratorios |
| proveedores | Proveedores | Registrar entidades proveedoras |
| lotes | Inventario | Controlar lote, vencimiento y cantidad disponible |
| movimientos_inventario | Inventario | Registrar entradas y salidas como historial |
| alertas | Alertas | Registrar alertas generadas por stock o vencimiento |

## 7. Relaciones conceptuales entre datos

Relaciones iniciales:

- un medicamento puede tener varios lotes;
- un lote pertenece a un medicamento;
- un lote puede estar asociado a un proveedor;
- un movimiento de inventario pertenece a un lote;
- un movimiento de inventario registra el usuario responsable;
- una alerta puede estar asociada a un medicamento o lote;
- un usuario tiene un rol;
- un reporte consulta informacion de medicamentos, lotes y movimientos.

Representacion conceptual:

```text
usuarios -> roles
medicamentos -> categorias
medicamentos -> laboratorios
medicamentos -> lotes
proveedores -> lotes
lotes -> movimientos_inventario
medicamentos -> alertas
lotes -> alertas
```

## 8. Fuente de verdad del stock

La fuente de verdad del stock debe evitar inconsistencias.

Decision preliminar:

**La cantidad real disponible se controlara desde lotes y movimientos de inventario.**

El stock total de un medicamento sera una consulta derivada:

```text
Stock total del medicamento = suma de cantidades disponibles de sus lotes activos
```

Esto evita mantener dos valores independientes que puedan contradecirse.

## 9. Contratos internos entre modulos

Los contratos internos definen que puede solicitar un modulo a otro sin romper su limite.

### Inventario consulta Catalogo

Inventario necesita validar que un medicamento exista y este activo antes de registrar lotes o movimientos.

Contrato conceptual:

```text
obtenerMedicamentoActivo(idMedicamento)
```

Resultado esperado:

- medicamento existente;
- medicamento activo;
- datos basicos necesarios para inventario.

### Inventario consulta Proveedores

Inventario puede asociar un lote o entrada a un proveedor.

Contrato conceptual:

```text
obtenerProveedorActivo(idProveedor)
```

Resultado esperado:

- proveedor existente;
- proveedor activo;
- datos basicos para trazabilidad.

### Inventario consulta Acceso e identidad

Inventario necesita validar que el usuario pueda realizar operaciones criticas.

Contrato conceptual:

```text
validarPermiso(usuarioId, operacion)
```

Resultado esperado:

- usuario autorizado;
- operacion permitida;
- registro del responsable.

### Alertas consulta Inventario

Alertas necesita conocer stock bajo o vencimientos proximos.

Contratos conceptuales:

```text
consultarLotesProximosAVencer()
consultarMedicamentosConStockBajo()
```

Resultado esperado:

- informacion suficiente para generar alertas;
- sin modificar existencias.

### Reportes consulta modulos

Reportes necesita leer datos de diferentes modulos.

Contratos conceptuales:

```text
consultarResumenInventario()
consultarMovimientosPorPeriodo()
consultarVencimientos()
```

Resultado esperado:

- datos consolidados;
- sin modificar reglas ni entidades del dominio.

## 10. Reglas de integracion

- Un modulo solo debe exponer operaciones necesarias para otros modulos.
- Los contratos deben ser pequenos y claros.
- Las consultas no deben permitir modificar datos indirectamente.
- Las reglas criticas deben ejecutarse en el modulo propietario.
- El modulo de inventario no debe confiar en datos enviados por otros modulos sin validacion.
- Los reportes deben tratarse como lectura.
- Las alertas pueden ejecutarse despues de los movimientos, sin bloquear la operacion principal.

## 11. Preparacion para estructura futura

Cuando se cree el servidor de aplicacion en la Semana 4, la estructura fisica debera reflejar esta separacion conceptual.

Una posible estructura futura podria ser:

```text
src/
+-- acceso/
+-- catalogo/
+-- inventario/
+-- proveedores/
+-- alertas/
+-- reportes/
```

Dentro de cada modulo se podrian separar responsabilidades:

```text
modulo/
+-- dominio/
+-- aplicacion/
+-- infraestructura/
```

Esta estructura es preliminar y podra adaptarse al lenguaje y marco de trabajo seleccionados para la implementacion.

## 12. Contratos para casos de uso principales

| Caso de uso | Entrada conceptual | Salida conceptual | Modulo propietario |
| --- | --- | --- | --- |
| Registrar medicamento | Datos del medicamento | Medicamento registrado | Catalogo |
| Registrar lote | Medicamento, proveedor, lote, cantidad | Lote registrado | Inventario |
| Registrar entrada | Lote, cantidad, responsable | Movimiento de entrada registrado | Inventario |
| Registrar salida | Lote, cantidad, motivo, responsable | Movimiento de salida registrado | Inventario |
| Consultar inventario | Filtros de busqueda | Stock por medicamento o lote | Inventario |
| Generar alerta de stock | Stock actual y minimo | Alerta generada | Alertas |
| Generar alerta de vencimiento | Fecha de vencimiento | Alerta generada | Alertas |
| Consultar reporte | Filtros de consulta | Datos consolidados | Reportes |

## 13. Reglas de validacion futuras

Cuando exista implementacion, las siguientes validaciones deben respetarse:

- el medicamento debe existir antes de registrar un lote;
- el proveedor debe existir antes de asociarlo a una entrada;
- la cantidad de entrada debe ser mayor que cero;
- la cantidad de salida debe ser mayor que cero;
- la salida no puede superar la cantidad disponible del lote;
- un movimiento debe registrar usuario responsable;
- un lote vencido no deberia usarse para salidas normales;
- una peticion repetida no debe duplicar un movimiento;
- las alertas no deben modificar el inventario.

## 14. Riesgos de diseno

| Riesgo | Consecuencia | Mitigacion |
| --- | --- | --- |
| Duplicar stock en medicamento y lote | Inconsistencias de inventario | Usar lotes y movimientos como fuente de verdad |
| Permitir modificaciones desde reportes | Corrupcion de reglas del dominio | Mantener reportes como lectura |
| Acoplar modulos internamente | Monolito desordenado | Usar contratos claros |
| Mezclar infraestructura con dominio | Dificultad para probar reglas | Aplicar arquitectura hexagonal |
| Crear mensajeria antes de necesitarla | Complejidad innecesaria | Mantener alertas conceptuales hasta justificar tecnologia |

## 15. Decisiones de diseno

### D-001 - Cada modulo tendra propietario de datos

Los datos se modificaran mediante el modulo responsable para proteger reglas y consistencia.

### D-002 - Inventario controla lotes y movimientos

Inventario es responsable de validar entradas, salidas, disponibilidad y trazabilidad.

### D-003 - Reportes sera un modulo de lectura

Reportes no debe modificar datos de negocio ni ejecutar reglas criticas.

### D-004 - Alertas puede usar consistencia eventual

Las alertas pueden generarse despues de la operacion principal sin bloquear inventario.

### D-005 - La estructura fisica se creara en Semana 4

La sesion actual deja el diseno preparado, pero no crea codigo ni carpetas de implementacion.

## 16. Que no se implementa en esta sesion

Durante esta sesion no se crea:

- proyecto del servidor de aplicacion;
- codigo fuente;
- base de datos;
- contratos de codigo;
- controladores;
- repositorios reales;
- pruebas automatizadas;
- contenedores.

## 17. Resultado de la sesion

Al finalizar la Semana 3 - Sesion 2 se cuenta con:

- modulos internos refinados;
- propiedad de datos definida;
- reglas de propiedad documentadas;
- modelo de datos preliminar;
- relaciones conceptuales entre datos;
- contratos internos conceptuales;
- reglas de integracion;
- preparacion para estructura futura;
- contratos conceptuales para casos de uso;
- validaciones futuras identificadas;
- riesgos de diseno documentados.

## 18. Que debo poder explicar al profesor

La idea principal de esta sesion es responder:

**Por que es importante definir propietario de datos y contratos internos antes de implementar MediStock?**

Porque cada modulo debe proteger sus propias reglas. Si cualquier parte del sistema puede modificar datos de inventario, reportes, alertas o catalogo sin pasar por contratos claros, el monolito modular perderia sus limites y podria convertirse en un sistema desordenado.

## 19. Conclusion

La Semana 3 - Sesion 2 deja a MediStock preparado para pasar del diseno a la implementacion.

El proyecto ya cuenta con modulos internos, propiedad de datos, contratos conceptuales, modelo preliminar y reglas de integracion. Esto permite que la futura estructura tecnica se construya con una base clara, manteniendo la coherencia con el monolito modular y la arquitectura hexagonal.
