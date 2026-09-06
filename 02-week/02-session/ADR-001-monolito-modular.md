# ADR-001 - Uso de monolito modular para MediStock

## Estado

Aprobado

## Fecha

2026-09-06

## Contexto

MediStock es un sistema de gestion de inventario de medicamentos. El proyecto debe controlar medicamentos, lotes, entradas, salidas, proveedores, alertas, usuarios y reportes.

El dominio tiene reglas criticas, especialmente en el contexto de inventario:

- no permitir stock negativo;
- no permitir salidas superiores al inventario disponible;
- conservar historial de movimientos;
- controlar lotes y vencimientos;
- registrar usuarios responsables de operaciones importantes.

En sesiones anteriores se identifico que las operaciones de inventario requieren consistencia fuerte, mientras que las alertas pueden admitir consistencia eventual.

El proyecto sera desarrollado inicialmente por una sola persona, por lo que la arquitectura debe ser clara, mantenible y proporcional al alcance actual.

## Decision

MediStock se construira inicialmente como **monolito modular**.

Esto significa que la aplicacion se desplegara como una sola unidad, pero internamente se organizara por modulos o contextos delimitados con responsabilidades claras.

Los contextos iniciales son:

- Acceso e identidad;
- Catalogo;
- Inventario;
- Proveedores;
- Alertas;
- Reportes.

## Alternativas consideradas

### Monolito tradicional

Permite iniciar rapido y con bajo costo operativo, pero puede mezclar responsabilidades y dificultar el mantenimiento si el proyecto crece sin limites internos claros.

### Monolito modular

Permite mantener baja complejidad operativa y separar responsabilidades por contexto. Facilita proteger reglas del dominio sin distribuir internamente el sistema.

### Microservicios

Permiten despliegue y escalado independiente, pero agregan complejidad de red, observabilidad, consistencia distribuida, monitoreo y pruebas entre servicios. No se justifican para el estado actual de MediStock.

## Consecuencias positivas

- La aplicacion sera mas simple de construir y desplegar al inicio.
- Las reglas de negocio podran organizarse por contextos.
- Se reduce la complejidad operativa.
- Se evita introducir fallos distribuidos innecesarios entre componentes internos.
- Se facilita la implementacion progresiva del proyecto.
- Se conserva la posibilidad de evolucionar hacia integraciones o separaciones futuras si aparece una necesidad real.

## Consecuencias negativas

- Los modulos no se desplegaran de forma independiente al inicio.
- La separacion modular dependera de disciplina de diseno y revisiones.
- Si no se respetan los limites, el sistema puede degradarse a un monolito desordenado.

## Reglas derivadas

- El contexto de inventario concentrara las reglas criticas de entradas, salidas, lotes, existencias y movimientos.
- Las alertas no deben bloquear operaciones criticas de inventario.
- Los reportes deben consultar informacion sin modificar reglas del dominio.
- La infraestructura no debe controlar reglas de negocio.
- Las futuras decisiones importantes se documentaran mediante nuevos ADR.

## Resultado

La arquitectura oficial inicial de MediStock queda definida como **monolito modular**.

La implementacion fisica del backend se realizara en una fase posterior del roadmap, cuando corresponda construir el primer esqueleto funcional del sistema.
