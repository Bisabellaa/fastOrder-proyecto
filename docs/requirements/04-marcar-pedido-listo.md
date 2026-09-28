# 04 · Marcar un pedido como listo

| Campo | Detalle |
|---|---|
| **Código** | HU-04 |
| **Actor** | Cocina |
| **Módulo** | Frontend · `src/frontend/dashboard-cocina` |
| **Prioridad** | Alta |
| **Estado** | Especificado |

## Historia de usuario

Como **cocina**, quiero marcar un pedido como "listo", para que el mozo sepa que
está para llevar.

## Criterios de aceptación

- [ ] La cocina puede seleccionar un pedido en preparación y marcarlo "listo".
- [ ] El cambio de estado se guarda en la base de datos.
- [ ] El dashboard del mozo recibe un aviso de "pedido listo" para esa mesa.
- [ ] Un pedido que el cliente canceló aparece como "cancelado" y no se puede marcar.

## Requerimientos relacionados

- **RF-06**, **RF-16**
- **RNF-06**

## Casos de uso relacionados

### CU-04: Marcar pedido como listo

| Campo | Detalle |
|---|---|
| **Actor** | Cocina |
| **Descripción** | La cocina avisa que el pedido está terminado. |
| **Precondición** | Existe un pedido en estado "en preparación". |
| **Flujo normal** | 1. La cocina selecciona el pedido. 2. Lo marca como "listo". 3. El sistema actualiza el estado y avisa al mozo que hay un pedido listo para la mesa N. |
| **Flujo alternativo** | 1a. Pedido cancelado por el cliente → la cocina ve "cancelado". |
| **Postcondición** | El pedido pasa a "listo" y el mozo sabe a qué mesa llevarlo. |

## Notas de diseño

Este es el único RF que genera un aviso de color **azul** en el dashboard del
mozo (ver [RF-10](README.md#3-requerimientos-funcionales-rf)). Los otros dos
colores —rojo y verde— los generan los botones de la botonera.
