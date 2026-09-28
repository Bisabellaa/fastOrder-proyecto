# 02 · Armar y confirmar el pedido con la botonera

| Campo | Detalle |
|---|---|
| **Código** | HU-02 |
| **Actor** | Cliente (con apoyo de la Botonera ESP32) |
| **Módulo** | Frontend · `src/frontend/cliente` + Backend |
| **Prioridad** | Alta |
| **Estado** | Especificado |

## Historia de usuario

Como **cliente**, quiero armar mi pedido en el celular y confirmarlo con el botón
"Hacer pedido" de la mesa, para que llegue directo a cocina.

## Criterios de aceptación

- [ ] El cliente puede seleccionar productos y cantidades y ver el subtotal.
- [ ] Al presionar el botón físico "Hacer pedido", el servidor confirma el pedido
      abierto de esa mesa y lo guarda con estado "recibido".
- [ ] El pedido queda asociado a la sesión de la mesa N.
- [ ] El cliente puede consultar en qué estado está su pedido.
- [ ] Si el cliente cierra la app o se queda sin señal, al volver a escanear el QR
      recupera su pedido abierto.
- [ ] El flujo alternativo funciona: el cliente también puede enviar el pedido
      directo desde la app, sin usar el botón.

## Requerimientos relacionados

- **RF-03**, **RF-04**, **RF-14**
- **RNF-02**, **RNF-06**

## Casos de uso relacionados

### CU-01: Realizar pedido desde la mesa

| Campo | Detalle |
|---|---|
| **Actor** | Cliente (con apoyo de la Botonera ESP32) |
| **Descripción** | El cliente arma su pedido en el celular y lo confirma con el botón físico "Hacer pedido" de su mesa. |
| **Precondición** | Cliente sentado en la mesa, red WiFi local activa, botonera "mesa N online". |
| **Flujo normal** | 1. El cliente escanea el QR de la mesa. 2. El servidor abre/retoma la sesión de la mesa N y muestra la carta. 3. El cliente selecciona productos y cantidades. 4. Presiona el botón "Hacer pedido" de la botonera. 5. La botonera envía el evento al servidor. 6. El servidor guarda el pedido con estado "recibido" y lo envía a cocina. |
| **Flujo alternativo** | 4a. El cliente envía el pedido directo desde la app (sin botón). · 3a. Carta vacía → mensaje "menú no disponible". · 5a. Botonera offline → el pedido se envía igual desde la app, la botonera se reconecta luego. |
| **Postcondición** | El pedido queda registrado con la sesión de la mesa N, estado "recibido" y visible en cocina. |

### CU-03: Ver estado del pedido

| Campo | Detalle |
|---|---|
| **Actor** | Cliente |
| **Descripción** | El cliente consulta en qué estado está su pedido. |
| **Flujo normal** | 1. El cliente toca "ver mi pedido". 2. El sistema muestra el estado actual (recibido / en preparación / listo / entregado). |
| **Postcondición** | El cliente conoce el estado de su pedido. |

## Notas de diseño

El botón "Hacer pedido" de la botonera confirma lo que el cliente ya armó en la
app. La botonera no tiene pantalla ni carrito: es un disparador. Por eso el
RF-04 dice "el pedido **armado en la app** de esa mesa", no "un pedido nuevo".
