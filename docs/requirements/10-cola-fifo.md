# 10 · Atender los avisos en orden de llegada

| Campo | Detalle |
|---|---|
| **Código** | HU-10 |
| **Actor** | Mozo, Sistema |
| **Módulo** | Backend · `src/backend/server` + `src/frontend/dashboard-mozo` |
| **Prioridad** | Alta |
| **Estado** | Especificado |

## Historia de usuario

Como **mozo**, quiero que los avisos se atiendan en orden de llegada, para no
olvidar ninguna mesa.

## Criterios de aceptación

- [ ] Los avisos se atienden en orden FIFO estricto: el primero en llegar es el
      primero en mostrarse.
- [ ] El dashboard muestra cuántos avisos quedan pendientes.
- [ ] Ningún aviso se pierde ni se sobrescribe cuando llegan varios a la vez.
- [ ] Si una mesa repite un aviso del mismo tipo con otro pendiente del mismo tipo,
      no se genera un duplicado.

## Requerimientos relacionados

- **RF-12**, **RF-13**
- **RNF-09**

## Casos de uso relacionados

### CU-10: Atender múltiples llamados simultáneos

| Campo | Detalle |
|---|---|
| **Actor** | Sistema, Mozo |
| **Descripción** | Cuando varias mesas llaman a la vez, el sistema los encola y el mozo los atiende en orden. |
| **Precondición** | Dos o más botoneras envían avisos. |
| **Flujo normal** | 1. Llegan N avisos de distintas mesas. 2. El servidor los ordena FIFO (por llegada). 3. El dashboard del mozo muestra el primero con contador de pendientes. 4. Al confirmar, pasa al siguiente. |
| **Flujo alternativo** | 2a. Si una mesa repite un aviso con otro pendiente del mismo tipo → no duplica. |
| **Postcondición** | Todos los avisos se atienden sin pérdida y sin sobrescribirse. |

## Notas de diseño

La cola FIFO es una responsabilidad del servidor, no del dashboard. Esto importa
porque el dashboard del mozo puede estar desconectado y reconectarse: al
reconectar recibe la cola completa en orden, sin depender de que el navegador
haya estado conectado en todo momento.

La deduplicación (RF-13) se implementa con una restricción de unicidad parcial
sobre `(mesa, tipo)` para los avisos en estado `pendiente`, no en la
aplicación.
