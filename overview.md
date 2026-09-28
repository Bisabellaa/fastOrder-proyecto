# FastOrder

Plataforma de autogestión de pedidos para restaurantes de sushi. El cliente
escanea el QR de su mesa, arma su pedido en el celular y lo confirma con un botón
físico de la mesa. El pedido llega al instante a la cocina y los avisos al mozo
se atienden en orden de llegada, sin intermediarios manuales.

Este archivo es el punto de entrada: resume el proyecto y enlaza a toda la
documentación.

---

## 1. Índice de la documentación

### Contexto del proyecto

| Documento | Qué contiene |
|---|---|
| [docs/01-idea-proyecto.md](docs/01-idea-proyecto.md) | Idea del proyecto, objetivos, justificación, beneficiarios y duración estimada |

### Requerimientos

| Documento | Qué contiene |
|---|---|
| [docs/requirements/README.md](docs/requirements/README.md) | Índice de historias de usuario + tablas maestras de actores, RF, RNF y casos de uso |
| [docs/requirements/](docs/requirements/) | Un archivo por historia de usuario (HU-01 a HU-11), numerados |

### Diseño

| Documento | Qué contiene |
|---|---|
| [docs/specs/2026-09-07-fastorder-design.md](docs/specs/2026-09-07-fastorder-design.md) | Documento de diseño: arquitectura, tecnologías, modelo de datos, cronograma y riesgos |

### Código

| Documento | Qué contiene |
|---|---|
| [src/frontend.md](src/frontend.md) | Qué hace el frontend, con qué tecnologías, sus rutas y su contrato con el backend |
| [src/backend.md](src/backend.md) | Qué hace el backend, con qué tecnologías, el modelo de datos y las reglas de negocio |

### Diagramas y planes

| Documento | Qué contiene |
|---|---|
| [docs/diagrams/diagrama-casos-de-uso.drawio](docs/diagrams/diagrama-casos-de-uso.drawio) | Diagrama de casos de uso (archivo de draw.io, editable) |
| [docs/plans/](docs/plans/) | Planes de desarrollo, uno por fase |

---

## 2. Resumen del proyecto

**Objetivo general.** Desarrollar una aplicación de autogestión de pedidos para
clientes mediante una aplicación móvil y una botonera integrada con Arduino
(ESP32), permitiendo una atención autónoma, rápida y sin intermediarios manuales
en la toma de órdenes.

**Cómo funciona.** El cliente escanea el QR de su mesa y ve la carta digital.
Arma su pedido en el celular. Al presionar el botón físico "Hacer pedido" de la
mesa, el pedido se confirma y aparece en el dashboard de cocina al instante.
Los otros dos botones de la mesa, "Llamar mozo" y "Pedir cuenta", generan avisos
que el mozo ve en su dashboard con un color por motivo. El botón "Pedir cuenta"
además le muestra al cliente el detalle y el total de lo consumido.

**Alcance de facturación.** El sistema **no emite factura fiscal** (sin
integración con AFIP). Solo calcula y muestra el total de lo que el cliente va a
consumir.

**Cómo funciona la red.** Todo corre sobre la red WiFi local del restaurante,
sin Internet. El servidor es una laptop en el local; el celular del cliente y las
pantallas del personal se conectan a esa red.

---

## 3. Componentes del sistema

| Componente | Tipo | Dónde vive | Qué hace |
|---|---|---|---|
| App del cliente | Web responsiva | `src/frontend/cliente` | QR → carta → carrito → confirmar → ver total |
| Dashboard cocina | Web | `src/frontend/dashboard-cocina` | Pedidos en vivo ordenados por llegada; marcar "listo" |
| Dashboard mozo | Web | `src/frontend/dashboard-mozo` | Cola de avisos (mesa + motivo + color); confirmar cada uno |
| Servidor central | Node.js | `src/backend/server` | API + WebSockets + cola FIFO de avisos |
| Base de datos | SQLite | `src/backend/db` | Carta, mesas, pedidos, sesiones y avisos |
| Botonera de mesa | ESP32 (C++) | `src/backend/hardware` | 3 botones por mesa + reporte "mesa N online" |

Las tres pantallas web se sirven desde el mismo servidor y se comunican por
WebSocket. La botonera envía eventos JSON por la misma red.

---

## 4. Estructura de carpetas

```
fast_oder/
├── overview.md                  ← este archivo: índice y resumen
├── docs/                        ← toda la documentación
│   ├── 01-idea-proyecto.md
│   ├── requirements/            ← requerimientos, un archivo por HU
│   │   ├── README.md            ← índice + tablas de RF, RNF y casos de uso
│   │   ├── 01-ver-carta-qr.md
│   │   └── ... 11 archivos
│   ├── specs/                   ← documentos de diseño
│   │   └── 2026-09-07-fastorder-design.md
│   ├── plans/                   ← planes de desarrollo por fase
│   │   └── 2026-09-07-fase-0-preparacion.md
│   └── diagrams/                ← diagramas
│       └── diagrama-casos-de-uso.drawio
└── src/                         ← todo el código
    ├── frontend.md              ← documentación del frontend
    ├── backend.md               ← documentación del backend
    ├── frontend/                ← lo que corre en el navegador
    │   ├── cliente/
    │   ├── dashboard-cocina/
    │   └── dashboard-mozo/
    └── backend/                 ← lo que corre en la laptop del restaurante
        ├── server/              ← Node.js
        ├── db/                  ← SQLite
        └── hardware/            ← firmware C++ de la botonera ESP32
```

Las carpetas de código están vacías: el proyecto entra ahora en la Fase 0
(ver [docs/plans/](docs/plans/)). Los `.gitkeep` están para que Git rastree las
carpetas vacías.

---

## 5. Estado del proyecto

| Fase | Qué se construye | Estado |
|---|---|---|
| 0 | Documentación de entrega + preparación del entorno | En curso |
| 1 | Base de datos: carta, mesas, pedidos, sesiones, avisos | Pendiente |
| 2 | Servidor central: API + WebSockets + cola FIFO | Pendiente |
| 3 | App del cliente: QR → menú → carrito → ver total | Pendiente |
| 4 | Dashboard cocina: pedidos en vivo + "listo" | Pendiente |
| 5 | Botonera ESP32: 3 botones + "mesa N online" | Pendiente |
| 6 | Dashboard mozo: cola de avisos + color + confirmación | Pendiente |
| 7 | QA + pruebas + ajustes | Pendiente |

Duración estimada total: **8 a 9 semanas**.

---

## 6. Decisiones de diseño que conviene conocer

- **El mozo usa un dashboard web, no un dispositivo de hardware.** Las versiones
  v2 y v3 del diseño usaban un segundo ESP32 con pantalla OLED, LEDs y buzzer
  ("llamador del mozo"). La v4 lo reemplaza por el dashboard web del mozo: el
  aviso se muestra en la red local con un color por motivo y una alerta sonora.
  Queda un solo dispositivo de hardware por mesa.
- **El servidor es el único que decide.** La botonera no guarda nada y el
  frontend no persiste nada. La cola FIFO, el cálculo de totales y la
  deduplicación de avisos viven en el backend.
- **La cola FIFO es del servidor, no de la pantalla.** Por eso el dashboard del
  mozo puede desconectarse y reconectarse sin perder avisos.
- **No hay facturación fiscal.** Solo el total de lo consumido.

---

## 7. Equipo

| Persona | Rol |
|---|---|
| Isabella Brenda | Desarrolladora y Líder de Proyecto |
| Lic. Walter Néstor Mancuso | Docente |
