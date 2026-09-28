# 03 · Ver los pedidos en vivo por orden de llegada

| Campo | Detalle |
|---|---|
| **Código** | HU-03 |
| **Actor** | Cocina |
| **Módulo** | Frontend · `src/frontend/dashboard-cocina` |
| **Prioridad** | Alta |
| **Estado** | Especificado |

## Historia de usuario

Como **cocina**, quiero ver los pedidos ordenados por llegada con el id de mesa,
para prepararlos apenas entran.

## Criterios de aceptación

- [ ] Cada pedido aparece en el dashboard apenas el cliente lo confirma.
- [ ] Los pedidos se listan ordenados por hora de llegada, el más nuevo último.
- [ ] Cada pedido muestra los productos y el número de mesa, nada más.
- [ ] El dashboard se actualiza solo, sin recargar la página.
- [ ] Si el dashboard se desconecta, al reconectar muestra los pedidos que entraron
      durante la desconexión.

## Criterios de no aceptación

- La cocina **no** ve nombres de clientes, teléfonos ni detalles de pago.
- La cocina **no** ve los avisos de llamada al mozo: eso es del dashboard del mozo.

## Requerimientos relacionados

- **RF-05**
- **RNF-02**, **RNF-05**

## Casos de uso relacionados

### CU-02: Comunicar el pedido a la cocina

| Campo | Detalle |
|---|---|
| **Actor** | Sistema (automático) |
| **Descripción** | El pedido confirmado llega al instante al dashboard de cocina. |
| **Precondición** | Existe un pedido con estado "recibido". |
| **Flujo normal** | 1. El sistema recibe el pedido. 2. Lo valida. 3. Lo muestra en el dashboard de cocina en tiempo real con el id de mesa. 4. La cocina lo marca "en preparación". |
| **Flujo alternativo** | 3a. Dashboard no conectado → el pedido queda guardado y aparece cuando se reconecta. |
| **Postcondición** | El pedido está visible en cocina con su estado. |

## Notas de diseño

Se usa WebSocket en lugar de polling para cumplir el RNF-02 (~2 segundos entre
la confirmación del cliente y la aparición en la pantalla de cocina).
