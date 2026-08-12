Sistema de Gestión de Inventario de Medicamentos
1. Información general

Nombre del proyecto: MediStock
Tipo de sistema: Software de gestión de inventario
Objetivo: Permitir el registro, control y seguimiento de medicamentos dentro de una farmacia, clínica, hospital o institución de salud.

El sistema busca facilitar el control de existencias, fechas de vencimiento, entradas y salidas de medicamentos, proveedores y alertas de inventario.

2. Problema

Actualmente, algunos establecimientos pueden llevar el control de medicamentos de manera manual o utilizando hojas de cálculo. Esto puede generar problemas como:

No conocer exactamente cuántos medicamentos hay disponibles.
No detectar medicamentos próximos a vencer.
Dificultad para controlar entradas y salidas.
Pérdida de información.
Errores al registrar cantidades.
Dificultad para saber qué medicamentos necesitan reposición.
Falta de reportes sobre el movimiento del inventario.

Por esta razón, se propone desarrollar un sistema que centralice y automatice la gestión del inventario.

3. Objetivo general

Desarrollar un software que permita administrar y controlar el inventario de medicamentos, proporcionando información actualizada sobre cantidades, lotes, fechas de vencimiento, movimientos y proveedores.

3.1 Objetivos específicos
Registrar medicamentos.
Actualizar la información de los medicamentos.
Controlar las cantidades disponibles.
Registrar entradas y salidas.
Controlar lotes y fechas de vencimiento.
Generar alertas de bajo inventario.
Generar alertas de medicamentos próximos a vencer.
Gestionar proveedores.
Generar reportes.
Controlar el acceso de los usuarios según sus roles.
4. Usuarios del sistema

El sistema tendrá diferentes tipos de usuarios:

Usuario	Funciones principales
Administrador	Gestionar usuarios, medicamentos, proveedores y configuración
Encargado de inventario	Registrar entradas, salidas y consultar existencias
Supervisor	Consultar inventario y generar reportes
Usuario de consulta	Consultar información básica del inventario
5. Funcionalidades principales
   5.1 Inicio de sesión

El sistema debe permitir que los usuarios ingresen mediante:

Usuario o correo.
Contraseña.
Rol asignado.

El sistema debe mostrar únicamente las funciones permitidas para cada usuario.

5.2 Gestión de medicamentos

El usuario autorizado podrá:

Registrar medicamentos.
Modificar medicamentos.
Consultar medicamentos.
Desactivar medicamentos.

Cada medicamento tendrá información como:

ID.
Nombre.
Principio activo.
Presentación.
Concentración.
Laboratorio/fabricante.
Categoría.
Cantidad disponible.
Stock mínimo.
Estado.

Ejemplo:

Campo	Ejemplo
Nombre	Acetaminofén
Principio activo	Paracetamol
Presentación	Tabletas
Concentración	500 mg
Stock mínimo	20
Estado	Activo
6. Gestión de lotes

El sistema debe permitir asociar los medicamentos con diferentes lotes.

Cada lote tendrá:

Número de lote.
Medicamento.
Fecha de fabricación, si aplica.
Fecha de vencimiento.
Cantidad.
Proveedor.
Fecha de ingreso.

Esto permitirá conocer exactamente qué lotes están disponibles.

7. Control de inventario

El sistema debe mantener actualizada la cantidad de medicamentos disponibles.

Entrada de inventario

Cuando lleguen medicamentos, el usuario podrá registrar:

Medicamento.
Lote.
Cantidad recibida.
Proveedor.
Fecha de entrada.
Usuario responsable.

El sistema aumentará automáticamente la cantidad disponible.

Salida de inventario

Cuando se retire medicamento del inventario, se registrará:

Medicamento.
Lote.
Cantidad retirada.
Fecha.
Motivo de la salida.
Usuario responsable.

El sistema disminuirá automáticamente la cantidad disponible.

8. Alertas

Una de las funciones importantes será el sistema de alertas.

Alerta de bajo inventario

Si:

Cantidad disponible ≤ Stock mínimo

el sistema deberá mostrar una alerta.

Ejemplo:

⚠️ El medicamento Acetaminofén tiene un inventario bajo.
Cantidad disponible: 15
Stock mínimo: 20.

Alerta de vencimiento

El sistema debe identificar medicamentos próximos a vencer y mostrar una alerta.

Por ejemplo:

⚠️ El lote ABC123 del medicamento X está próximo a vencer.

La cantidad de días utilizada para generar la alerta podrá ser configurable por el administrador.

9. Gestión de proveedores

El sistema permitirá registrar:

ID del proveedor.
Nombre.
NIT o identificación empresarial.
Teléfono.
Correo.
Dirección.
Estado.

También se podrá consultar qué medicamentos han sido registrados provenientes de cada proveedor.

10. Búsqueda y filtros

El usuario podrá buscar medicamentos mediante:

Nombre.
Principio activo.
Categoría.
Laboratorio.
Número de lote.
Estado.

También podrá aplicar filtros como:

Medicamentos con stock bajo.
Medicamentos próximos a vencer.
Medicamentos agotados.
Medicamentos activos.
11. Reportes

El sistema deberá generar diferentes reportes.

Reporte de inventario

Mostrar:

Medicamento.
Cantidad disponible.
Stock mínimo.
Estado.
Reporte de vencimientos

Mostrar:

Medicamento.
Lote.
Fecha de vencimiento.
Cantidad disponible.
Reporte de movimientos

Mostrar:

Medicamento.
Tipo de movimiento.
Cantidad.
Fecha.
Usuario responsable.
Reporte de proveedores

Mostrar los proveedores registrados y los medicamentos asociados.

12. Requisitos funcionales
    Código	Requisito
    RF01	El sistema debe permitir iniciar sesión.
    RF02	El sistema debe permitir registrar medicamentos.
    RF03	El sistema debe permitir modificar medicamentos.
    RF04	El sistema debe permitir consultar medicamentos.
    RF05	El sistema debe permitir registrar lotes.
    RF06	El sistema debe registrar entradas de inventario.
    RF07	El sistema debe registrar salidas de inventario.
    RF08	El sistema debe actualizar automáticamente las existencias.
    RF09	El sistema debe generar alertas de stock bajo.
    RF10	El sistema debe generar alertas de vencimiento.
    RF11	El sistema debe permitir gestionar proveedores.
    RF12	El sistema debe generar reportes.
    RF13	El sistema debe registrar el usuario responsable de cada movimiento.
    RF14	El sistema debe permitir buscar y filtrar medicamentos.
    RF15	El administrador debe poder gestionar usuarios y roles.
13. Requisitos no funcionales
    Seguridad
    Las contraseñas deben almacenarse de manera segura.
    Cada usuario tendrá permisos según su rol.
    Las operaciones importantes deben quedar registradas.
    Rendimiento
    Las consultas de medicamentos deben responder rápidamente.
    El sistema debe soportar múltiples registros de medicamentos y movimientos.
    Disponibilidad

El sistema debe estar disponible durante el horario de operación de la institución.

Usabilidad

La interfaz debe ser sencilla y permitir que un usuario pueda encontrar rápidamente un medicamento.

Escalabilidad

El sistema debe permitir agregar posteriormente nuevas funcionalidades, como módulos de compras, proveedores o estadísticas.

14. Reglas de negocio

RN01. Un medicamento debe tener un nombre obligatorio.

RN02. Un medicamento puede tener varios lotes.

RN03. Cada lote debe tener un número identificador.

RN04. La cantidad disponible nunca debe ser negativa.

RN05. No se debe permitir registrar una salida superior a la cantidad disponible.

RN06. Cada entrada y salida debe quedar registrada.

RN07. El sistema debe identificar medicamentos cuyo inventario esté por debajo del stock mínimo.

RN08. El sistema debe identificar medicamentos próximos a vencer.

RN09. Solo los usuarios autorizados pueden modificar el inventario.

RN10. Los movimientos de inventario no deben eliminarse directamente; deben conservarse como historial.

15. Casos de uso principales
    SISTEMA DE INVENTARIO DE MEDICAMENTOS

Administrador ────────> Iniciar sesión
│
├───────────────> Gestionar usuarios
├───────────────> Gestionar medicamentos
├───────────────> Gestionar proveedores
└───────────────> Consultar reportes

Encargado de inventario
│
├───────────────> Registrar entrada
├───────────────> Registrar salida
├───────────────> Consultar inventario
└───────────────> Consultar alertas

Supervisor
│
├───────────────> Consultar inventario
├───────────────> Consultar vencimientos
└───────────────> Generar reportes
16. Flujo principal: entrada de medicamento
    El encargado inicia sesión.
    Selecciona "Entrada de inventario".
    Busca el medicamento.
    Selecciona o registra el lote.
    Ingresa la cantidad recibida.
    Selecciona el proveedor.
    El sistema valida la información.
    El sistema registra el movimiento.
    El sistema actualiza la cantidad disponible.
    El sistema muestra un mensaje de confirmación.

Resultado:

Entrada registrada correctamente.
Medicamento: Acetaminofén
Cantidad ingresada: 100 unidades.

17. Flujo principal: salida de medicamento
    El encargado inicia sesión.
    Selecciona "Salida de inventario".
    Busca el medicamento.
    Selecciona el lote correspondiente.
    Ingresa la cantidad.
    El sistema verifica que exista suficiente inventario.
    Registra la salida.
    Actualiza la cantidad disponible.
    Guarda el usuario responsable.
    Muestra la confirmación.
18. Entidades principales de la base de datos

Para el desarrollo del sistema se podrían utilizar las siguientes tablas:

USUARIOS
│
├── ROLES
│
└── MOVIMIENTOS_INVENTARIO
│
└── LOTES
│
└── MEDICAMENTOS
│
├── CATEGORIAS
│
└── PROVEEDORES

Tablas sugeridas:

usuarios
roles
medicamentos
categorias
laboratorios
proveedores
lotes
movimientos_inventario
tipos_movimiento
alertas