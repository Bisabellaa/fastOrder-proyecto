# Frontend · `src/frontend/`

Documento de referencia del lado cliente del sistema FastOrder. Es la parte del
proyecto que corre **en el navegador**: la app del cliente y los dos dashboards
del personal (cocina y mozo).

---

## 1. Qué hace

El frontend cubre las tres pantallas que usa el personal y el cliente del
restaurante:

| Carpeta | Pantalla | Quién la usa | Propósito |
|---|---|---|---|
| `cliente/` | App del cliente | Cliente | Escanea el QR, ve la carta, arma el pedido, confirma, ve su estado y su total |
| `dashboard-cocina/` | Dashboard de cocina | Cocina | Ve los pedidos en vivo ordenados por llegada y los marca como "listo" |
| `dashboard-mozo/` | Dashboard del mozo | Mozo | Ve la cola de avisos (mesa + motivo), confirma cada uno, cobra en mesa |

Las tres se sirven desde el mismo servidor y se comunican por WebSocket.

---

## 2. Tecnologías

| Pieza | Tecnología | Motivo |
|---|---|---|
| Las tres pantallas | HTML + CSS + JavaScript | Sin framework ni build step: es lo más simple de mantener y de defender en un proyecto académico, y alcanza para el volumen de un restaurante. |
| Comunicación con el servidor | WebSocket (JSON) | Tiempo real: un pedido confirmado llega a cocina en ~2 segundos (RNF-02) sin recargar la página (RNF-05, RNF-12). |
| App del cliente | Web responsiva | El cliente entra desde el QR con el celular, sin instalar nada (RNF-04). |

**Regla del proyecto:** JavaScript para todo lo que corre en computadoras y
celulares; C++ para las placas ESP32. La comunicación entre frontend y backend
es por red, con mensajes JSON, no por llamada directa.

---

## 3. Rutas de la aplicación

| Ruta | Pantalla |
|---|---|
| `/m/:mesa` | App del cliente para la mesa N (destino del QR) |
| `/cocina` | Dashboard de cocina |
| `/mozo` | Dashboard del mozo |

La ruta `/m/:mesa` es la que viaja dentro del QR pegado en la mesa.

---

## 4. Contrato con el backend

El frontend no guarda nada. Todo lo persiste el backend. Los mensajes son JSON.

**El frontend escucha:**

| Evento | Cuándo | Qué trae |
|---|---|---|
| `carta` | Al abrir la app del cliente | Lista de productos con precio y categoría |
| `pedido:actualizado` | Cuando cambia el estado de un pedido | Id de pedido, mesa, estado, productos |
| `aviso:nuevo` | Cuando entra un aviso en la cola | Id, mesa, tipo, color |
| `aviso:atendido` | Cuando se confirma un aviso | Id del aviso que salió de la cola, y el siguiente |
| `mesa:estado` | Cuando una botonera se conecta o desconecta | Número de mesa, `online` |

**El frontend envía:**

| Evento | Quién lo envía | Qué trae |
|---|---|---|
| `pedido:crear` | App del cliente | Mesa, lista de productos y cantidades |
| `pedido:listo` | Dashboard de cocina | Id del pedido |
| `aviso:confirmar` | Dashboard del mozo | Id del aviso |
| `aviso:pedir_cuenta` | App del cliente | Mesa |

**Tipos de aviso y su color en pantalla:**

| Tipo | Color | Origen |
|---|---|---|
| `llamar_mozo` | rojo | Botón "Llamar mozo" de la botonera |
| `pedir_cuenta` | verde | Botón "Pedir cuenta" de la botonera |
| `pedido_listo` | azul | Cocina marca un pedido como listo |

---

## 5. Requisitos que este lado cumple

- **RF-01** a **RF-06**: la carta, el carrito y los dos dashboards.
- **RF-07** y **RF-08**: el total que ve el cliente al pedir la cuenta.
- **RF-09** a **RF-12**: la cola de avisos y los colores del dashboard del mozo.
- **RNF-01**, **RNF-04**, **RNF-05**, **RNF-12**.

El listado completo está en
[docs/requirements/README.md](../docs/requirements/README.md).

---

## 6. Estado actual

Las tres carpetas están vacías. Se implementan a partir de la Fase 1, siguiendo
el plan de [docs/plans/](../docs/plans/).

| Fase | Qué se implementa de este lado |
|---|---|
| 1 | Nada todavía (solo base de datos) |
| 2 | Conexión WebSocket de prueba |
| 3 | `cliente/` — QR → menú → carrito → ver total |
| 4 | `dashboard-cocina/` — pedidos en vivo + "listo" |
| 6 | `dashboard-mozo/` — avisos, colores, cola y confirmación |
