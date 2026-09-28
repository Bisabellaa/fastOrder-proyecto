# Backend · `src/backend/`

Documento de referencia del lado servidor del sistema FastOrder. Es la parte del
proyecto que corre **en la laptop del restaurante** y que centraliza toda la
lógica: recibe pedidos, los totaliza, guarda todo en la base de datos y reparte
los avisos.

---

## 1. Qué hace

| Carpeta | Contenido | Responsabilidad |
|---|---|---|
| `server/` | Node.js | API HTTP + WebSockets + cola FIFO de avisos. Es el corazón del sistema. |
| `db/` | SQLite | Persistencia de carta, mesas, pedidos, sesiones y avisos. |
| `hardware/` | C++ (ESP32) | Firmware de la botonera de cada mesa: 3 botones + reporte "mesa N online". |

**Regla central:** el backend es el único que decide. El frontend solo muestra y
el hardware solo emite eventos.

---

## 2. Tecnologías

| Pieza | Tecnología | Motivo |
|---|---|---|
| Servidor | Node.js + JavaScript | WebSockets nativos y sencillos; mismo idioma que el frontend. |
| Base de datos | SQLite (vía ORM migrable a PostgreSQL) | Cero configuración; migrable sin reescribir código (RNF-06). |
| Botonera de mesa | C/C++ (ESP32, Arduino) | Es el lenguaje de la placa; se comunica por red con JSON sobre WebSocket. |
| Comunicación | WebSockets (JSON sobre WiFi local) | Tiempo real, sin depender de Internet (RNF-01, RNF-08). |

---

## 3. Modelo de datos

| Entidad | Campos |
|---|---|
| **Producto** | id, nombre, descripcion, precio, categoria |
| **Mesa** | id, numero |
| **Pedido** | id, mesa, estado, fecha |
| **ItemPedido** | id, pedido_id, producto_id, cantidad |
| **SesionMesa** | mesa_id, pedido_abierto_id, activa |
| **AvisoMozo** | id, mesa, tipo, estado, fecha, hora, hora_confirmacion |
| **EstadoMesa** | mesa_id, online |

**Estados de un pedido:** `recibido` → `en_preparacion` → `listo` → `entregado`
(además de `cancelado`).

**Tipos de aviso:** `llamar_mozo`, `pedir_cuenta`, `pedido_listo`.
**Estados de un aviso:** `pendiente`, `atendido`.

---

## 4. Reglas de negocio que el backend aplica

Estas reglas viven **solo** aquí. El frontend y el hardware no las conocen.

1. **Sesión por mesa (RF-14).** Al escanear el QR se crea o retoma el pedido
   abierto de esa mesa. El cliente puede cerrar la app sin perder su pedido.
2. **Cálculo del total (RF-07, RNF-10).** Se acumula en **enteros de centavos**,
   no en coma flotante, para que el total de 2 decimales sea exacto.
3. **Cola FIFO (RF-12, RNF-09).** El orden de los avisos se define en el
   servidor, no en la pantalla. El dashboard del mozo puede conectarse y
   desconectarse sin perder avisos.
4. **Deduplicación (RF-13).** Si una mesa repite un aviso del mismo tipo con otro
   pendiente del mismo tipo, no se crea un duplicado: se actualiza el pendiente.
   Se implementa con una restricción de unicidad parcial sobre
   `(mesa, tipo)` para los avisos en estado `pendiente`, no en la aplicación.
5. **Registro del día (RF-18, RF-19).** Cada aviso se guarda con mesa, motivo,
   fecha, hora y hora de confirmación. No requiere servicios externos.

---

## 5. Contrato con el resto del sistema

El frontend y la botonera se comunican con el backend por los mismos WebSockets.
El contrato completo de eventos está en [frontend.md](frontend.md), sección 4.

Detalle relevante del lado hardware: la botonera **no** guarda ni decide nada.
Cada botón envía un mensaje JSON y el servidor responde. Si la botonera está
offline, el pedido se puede enviar igual desde la app (flujo alternativo de
CU-01).

---

## 6. Requisitos que este lado cumple

- **RF-04**, **RF-07**, **RF-13** a **RF-19**: confirmación por botón, totales,
  cola de avisos, estado de mesas y registro histórico.
- **RNF-01**, **RNF-02**, **RNF-06**, **RNF-08**, **RNF-09**, **RNF-10**.

El listado completo está en
[docs/requirements/README.md](../docs/requirements/README.md).

---

## 7. Estado actual

Las tres carpetas están vacías. La carpeta `server/` arranca con el "hola mundo"
del plan de [docs/plans/](../docs/plans/).

| Fase | Qué se implementa de este lado |
|---|---|
| 0 | `server/server.js` mínimo (hola mundo) |
| 1 | `db/` — esquema SQLite y datos iniciales |
| 2 | `server/` — API + WebSockets + cola FIFO |
| 5 | `hardware/` — firmware de la botonera ESP32 |
