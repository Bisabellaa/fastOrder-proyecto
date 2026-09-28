# Requerimientos de FastOrder

Análisis de requerimientos, historias de usuario y casos de uso de FastOrder.

**Proyecto:** FastOrder — Plataforma móvil de autogestión de pedidos para restaurantes de sushi
**Equipo:** Isabella Brenda (Desarrolladora y Líder de Proyecto)
**Docente:** Lic. Walter Néstor Mancuso
**Fecha:** 2026
**Versión del documento:** 4.0

---

## 1. Cómo se organiza esta carpeta

Cada historia de usuario (HU) tiene su propio archivo, numerado y con nombre corto.
Los archivos siguen la convención `NN-nombre-en-palabras.md`.

| Archivo | HU | Actor | Título |
|---|---|---|---|
| [01-ver-carta-qr.md](01-ver-carta-qr.md) | HU-01 | Cliente | Ver la carta escaneando el QR de la mesa |
| [02-hacer-pedido.md](02-hacer-pedido.md) | HU-02 | Cliente | Armar y confirmar el pedido con la botonera |
| [03-pedidos-en-vivo.md](03-pedidos-en-vivo.md) | HU-03 | Cocina | Ver los pedidos en vivo por orden de llegada |
| [04-marcar-pedido-listo.md](04-marcar-pedido-listo.md) | HU-04 | Cocina | Marcar un pedido como listo |
| [05-entrega-pedido-listo.md](05-entrega-pedido-listo.md) | HU-05 | Mozo | Saber a qué mesa va cada pedido listo |
| [06-llamar-mozo.md](06-llamar-mozo.md) | HU-06 | Cliente | Llamar al mozo desde la botonera |
| [07-pedir-cuenta.md](07-pedir-cuenta.md) | HU-07 | Cliente | Pedir la cuenta y ver el total |
| [08-aviso-en-dashboard-mozo.md](08-aviso-en-dashboard-mozo.md) | HU-08 | Mozo | Ver en su pantalla qué mesa lo llama y por qué |
| [09-confirmar-aviso.md](09-confirmar-aviso.md) | HU-09 | Mozo | Confirmar cada aviso atendido |
| [10-cola-fifo.md](10-cola-fifo.md) | HU-10 | Mozo | Atender los avisos en orden de llegada |
| [11-gestion-carta.md](11-gestion-carta.md) | HU-11 | Dueño / Admin | Cargar y modificar la carta |

Las secciones 2 a 5 de este archivo son las tablas maestras: cada historia de
usuario referencia sus códigos en vez de repetirlos.

---

## 2. Actores del sistema

| Actor | Descripción | Interacción principal |
|---|---|---|
| **Cliente** | Persona sentada en una mesa del restaurante | Escanea QR, arma pedido en la app, usa los botones de la botonera, ve el total de su consumo |
| **Cocina** | Equipo que prepara los pedidos | Usa el dashboard de cocina: ve los pedidos solo con productos e id de mesa y los marca como "listo" |
| **Mozo** | Atención en sala | Usa el dashboard del mozo: ve la cola de avisos (mesa + motivo), confirma cada uno y cobra en mesa |
| **Dueño / Admin** | Responsable del restaurante | Carga y modifica la carta, consulta el historial del día |
| **Botonera ESP32** | Hardware fijo en cada mesa | Captura los 3 botones físicos y los envía al servidor; reporta "mesa N online" |
| **Sistema (FastOrder)** | Software central | Recibe pedidos, los totaliza, encola avisos, notifica a cocina y al mozo |

---

## 3. Requerimientos funcionales (RF)

| Código | Requerimiento |
|---|---|
| **RF-01** | El sistema debe abrir la carta digital al escanear el QR de la mesa. |
| **RF-02** | El sistema debe mostrar la carta con productos, precios y categorías (ej. Rolls, Niguiris, Bebidas). |
| **RF-03** | El cliente debe poder armar un pedido (seleccionar productos y cantidades) en la app. |
| **RF-04** | El botón "Hacer pedido" de la botonera debe confirmar y enviar a cocina el pedido armado en la app de esa mesa. |
| **RF-05** | El dashboard de cocina debe mostrar los pedidos en tiempo real, ordenados por llegada, solo con productos e id de mesa. |
| **RF-06** | La cocina debe poder cambiar el estado de un pedido a "listo". |
| **RF-07** | El sistema debe calcular y mostrar el total de lo consumido por mesa, con 2 decimales, sin redondeos erróneos. Sin facturación fiscal. |
| **RF-08** | El botón "Pedir cuenta" debe mostrar el detalle y el total al cliente en su celular, y avisar al mozo. |
| **RF-09** | El botón "Llamar mozo" debe notificar al dashboard del mozo con el número de mesa. |
| **RF-10** | El dashboard del mozo debe mostrar el aviso (mesa + motivo) con un código de color: rojo = llaman, verde = cuenta, azul = pedido listo. |
| **RF-11** | El dashboard del mozo debe emitir una alerta sonora cuando llega un aviso, y esta debe silenciarse al confirmar. |
| **RF-12** | Los avisos al mozo deben manejarse en cola FIFO (orden de llegada); el mozo los confirma desde el dashboard para ver el siguiente. |
| **RF-13** | Si la misma mesa repite el mismo tipo de aviso con uno pendiente, no debe crear duplicados: actualiza el pendiente. |
| **RF-14** | El sistema debe mantener una sesión por mesa: al escanear el QR se retoma el pedido abierto de esa mesa (no se pierde al cerrar la app). |
| **RF-15** | La botonera ESP32 debe reportar "mesa N online" al servidor cuando está activa en la red. |
| **RF-16** | El sistema debe actualizar el estado del pedido y avisar al mozo cuando la cocina marca un pedido como listo. |
| **RF-17** | El dueño debe poder cargar, modificar y quitar productos de la carta. |
| **RF-18** | El sistema debe registrar cada aviso del mozo (llamar mozo / pedir cuenta) con mesa, motivo, fecha, hora y confirmación, en la base de datos local (SQLite). |
| **RF-19** | El registro de avisos debe permitir consultar el historial diario, sin requerir servicios externos. |

---

## 4. Requerimientos no funcionales (RNF)

| Código | Requerimiento |
|---|---|
| **RNF-01** | El sistema debe funcionar sobre la red WiFi local del restaurante, sin depender de Internet. |
| **RNF-02** | El envío de un pedido de cliente a cocina no debe demorar más de ~2 segundos (WebSockets). |
| **RNF-03** | El sistema debe soportar al menos ~10 mesas operando simultáneamente sin degradarse. |
| **RNF-04** | La interfaz del cliente debe verse correctamente en celulares (diseño responsivo). |
| **RNF-05** | El dashboard de cocina debe actualizarse solo, sin que el usuario recargue la página. |
| **RNF-06** | Los datos (carta, pedidos, mesas, avisos) deben persistir en SQLite aunque se reinicie el servidor. |
| **RNF-07** | El código debe estar organizado por módulos y versionado con Git. |
| **RNF-08** | La comunicación con las placas ESP32 debe ser por red, usando JSON sobre WebSocket. |
| **RNF-09** | Los avisos del mozo deben atenderse en orden (FIFO) sin pérdida de llamados. |
| **RNF-10** | El total del consumo debe calcularse con exactitud de 2 decimales, sin errores de redondeo. |
| **RNF-11** | La botonera debe ser fácil de usar: cada botón claramente identificable (etiquetas). |
| **RNF-12** | El dashboard del mozo debe actualizarse solo, sin que el usuario recargue la página. |

---

## 5. Casos de uso

| Código | Caso de uso | Archivo de HU relacionado |
|---|---|---|
| **CU-01** | Realizar pedido desde la mesa | 02-hacer-pedido.md |
| **CU-02** | Comunicar el pedido a la cocina | 03-pedidos-en-vivo.md |
| **CU-03** | Ver estado del pedido | 02-hacer-pedido.md |
| **CU-04** | Marcar pedido como listo | 04-marcar-pedido-listo.md |
| **CU-05** | Cargar y modificar la carta | 11-gestion-carta.md |
| **CU-06** | Reportar mesa activa (Botonera ESP32) | 06-llamar-mozo.md |
| **CU-07** | Llamar al mozo | 06-llamar-mozo.md |
| **CU-08** | Pedir la cuenta | 07-pedir-cuenta.md |
| **CU-09** | Confirmar aviso atendido (dashboard del mozo) | 09-confirmar-aviso.md |
| **CU-10** | Atender múltiples llamados simultáneos | 10-cola-fifo.md |

El detalle completo de cada caso de uso (actor, precondición, flujo normal, flujo
alternativo y postcondición) está dentro del archivo de la HU que lo origina.

---

## 6. Contexto del proyecto

Resumen del proyecto, objetivos, justificación, beneficiarios y cronograma:
[docs/01-idea-proyecto.md](../01-idea-proyecto.md)

Diseño de arquitectura, tecnologías y modelo de datos:
[docs/specs/2026-09-07-fastorder-design.md](../specs/2026-09-07-fastorder-design.md)
