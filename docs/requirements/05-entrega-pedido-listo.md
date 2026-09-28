# 05 · Saber a qué mesa va cada pedido listo

| Campo | Detalle |
|---|---|
| **Código** | HU-05 |
| **Actor** | Mozo |
| **Módulo** | Frontend · `src/frontend/dashboard-mozo` |
| **Prioridad** | Alta |
| **Estado** | Especificado |

## Historia de usuario

Como **mozo**, quiero saber a qué mesa va cada pedido listo, para entregarlo rápido.

## Criterios de aceptación

- [ ] Cuando la cocina marca un pedido como "listo", el dashboard del mozo muestra
      un aviso con el número de mesa.
- [ ] El aviso de pedido listo se distingue visualmente de los avisos de llamada
      y de cuenta (color azul, ver RF-10).
- [ ] El mozo puede ver los pedidos listos pendientes aunque no haya ningún otro
      aviso en la cola.

## Requerimientos relacionados

- **RF-10**, **RF-16**
- **RNF-12**

## Casos de uso relacionados

Detalle en [CU-04](04-marcar-pedido-listo.md#caso-de-uso-cu-04), pasos 3 a 5.

## Notas de diseño

A diferencia de los avisos de "llamar mozo" y "pedir cuenta", que son
confirmables por el mozo (ver [09-confirmar-aviso.md](09-confirmar-aviso.md)),
el aviso de "pedido listo" **no** se confirma: se atiende entregando el pedido.
Por eso comparte la cola FIFO pero no se descarta al confirmarlo, sino cuando el
mozo marca la entrega.
