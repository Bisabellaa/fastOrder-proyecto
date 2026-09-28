# 06 · Llamar al mozo desde la botonera

| Campo | Detalle |
|---|---|
| **Código** | HU-06 |
| **Actor** | Cliente (inicia), Mozo (recibe) |
| **Módulo** | Backend · `src/backend/hardware` + `src/backend/server` |
| **Prioridad** | Alta |
| **Estado** | Especificado |

## Historia de usuario

Como **cliente**, quiero presionar "Llamar mozo" para que el mozo sepa qué mesa
lo llama, sin tener que gritar.

## Criterios de aceptación

- [ ] Al presionar el botón "Llamar mozo", la botonera envía el evento al servidor.
- [ ] El servidor encola un aviso de tipo `llamar_mozo` con el número de mesa.
- [ ] El dashboard del mozo muestra "MESA N · te llama" con color rojo.
- [ ] Si la mesa N ya tiene un aviso de `llamar_mozo` pendiente, no se crea un
      duplicado: se mantiene el pendiente (RF-13).
- [ ] Si el dashboard del mozo está desconectado, el aviso queda en la cola del
      servidor y aparece cuando se reconecte.
- [ ] La botonera reporta "mesa N online" al conectarse al servidor.

## Requerimientos relacionados

- **RF-09**, **RF-13**, **RF-15**, **RF-18**
- **RNF-08**, **RNF-09**

## Casos de uso relacionados

### CU-06: Reportar mesa activa (Botonera ESP32)

| Campo | Detalle |
|---|---|
| **Actor** | Botonera ESP32 |
| **Descripción** | La botonera de la mesa se conecta y anuncia su presencia. |
| **Precondición** | La botonera tiene corriente y está en la red WiFi. |
| **Flujo normal** | 1. La botonera se conecta al WiFi. 2. Envía "mesa N online" al servidor. 3. El servidor registra la mesa como activa. |
| **Flujo alternativo** | 2a. La botonera se desconecta → el servidor marca la mesa "offline". |
| **Postcondición** | El sistema conoce el estado físico de la mesa. |

### CU-07: Llamar al mozo

| Campo | Detalle |
|---|---|
| **Actor** | Cliente (inicia), Mozo (recibe) |
| **Descripción** | El cliente presiona el botón "Llamar mozo" de la botonera. |
| **Precondición** | Botonera "mesa N online". |
| **Flujo normal** | 1. El cliente presiona "Llamar mozo". 2. La botonera envía el evento al servidor. 3. El servidor encola el aviso (tipo: llamar_mozo, mesa N). 4. El dashboard del mozo muestra "MESA N · te llama" con color rojo. 5. El mozo confirma. |
| **Flujo alternativo** | 3a. La mesa N ya tiene un "llamar mozo" pendiente → no se duplica, se mantiene el pendiente. · 4a. Dashboard del mozo desconectado → el aviso queda en la cola del servidor; cuando se reconecta lo recibe. |
| **Postcondición** | El aviso queda en la cola hasta ser confirmado por el mozo. |

## Notas de diseño

La botonera tiene tres botones y ninguna pantalla. Cada botón envía un mensaje
JSON por WebSocket al servidor. La botonera **no** guarda ni decide nada: es un
emisor de eventos. Toda la lógica de cola y deduplicación vive en el servidor.
