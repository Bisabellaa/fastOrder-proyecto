# FAST ORDER — Análisis de Requerimientos, Historias de Usuario y Casos de Uso

**Proyecto:** FastOrder — Plataforma móvil de autogestión de pedidos para restaurantes de sushi
**Equipo:** Isabella Brenda (Desarrolladora y Líder de Proyecto)
**Docente:** Lic. Walter Néstor Mancuso
**Fecha:** 2026
**Versión del documento:** 3.0

---

## 1. Introducción y objetivos

### 1.1 Resumen del proyecto

FastOrder es una plataforma móvil diseñada para optimizar la gestión de
pedidos y la comunicación interna en restaurantes de sushi. El cliente puede
realizar su pedido desde su propio celular (escaneando un QR en su mesa) o
mediante una botonera física con ESP32 fija en la mesa. El sistema entrega la
orden a la cocina en el instante exacto en que el cliente la confirma, sin
intermediarios manuales.

Un segundo dispositivo ESP32 funciona como llamador inalámbrico del mozo: le
avisa qué mesa lo llama y por qué motivo, mediante pantalla OLED, LEDs de
color y un buzzer.

### 1.2 Objetivo general

Desarrollar una aplicación de autogestión de pedidos para clientes mediante
una aplicación móvil y una botonera integrada con Arduino (ESP32), permitiendo
una atención autónoma, rápida y sin intermediarios manuales en la toma de
órdenes.

### 1.3 Objetivos específicos

1. Otorgar autonomía al cliente para realizar pedidos desde su dispositivo o
   mediante la interfaz física de la mesa.
2. Eliminar las demoras causadas por la toma de pedidos manual del mozo.
3. Reducir las confusiones al registrar órdenes.
4. Entregar la orden a cocina en el instante en que el cliente la confirma.
5. Permitir al cliente solicitar al mozo sin llamarlo reiteradamente.
6. Mostrar al cliente el total de su consumo al pedir la cuenta.
7. Adquirir conocimientos sobre desarrollo de software y estudio técnico (QA).
8. Optimizar el personal de salón y aumentar la rotación de mesas.

### 1.4 Resultados esperados

El cliente obtiene una atención eficaz y certera. El sistema procesa los
pedidos a mayor velocidad, eliminando errores de interpretación del mozo y
permitiendo que la cocina reciba la orden en el instante exacto en que el
cliente presiona el botón o confirma en la app.

### 1.5 Justificación

Se optimiza la comunicación y se quitan los obstáculos que hacen que el
cliente pierda tiempo. Al eliminar el paso intermedio de la toma de pedido
manual, se minimizan las demoras y confusiones, brindando al cliente una
sensación de control y rapidez que mejora su experiencia.

### 1.6 Alcance de facturación

El sistema NO emite factura fiscal (sin integración AFIP). Solo calcula y
muestra el **total de lo que el usuario va a consumir**, ni más ni menos.

---

## 2. Actores del sistema

| Actor | Descripción | Interacción principal |
|---|---|---|
| **Cliente** | Persona sentada en una mesa del restaurante | Escanea QR, arma pedido en la app, usa los botones de la botonera, ve el total de su consumo |
| **Cocina** | Equipo que prepara los pedidos | Ve los pedidos en el dashboard (solo productos e id de mesa) y los marca como "listo" |
| **Mozo** | Atención en sala | Recibe avisos en el llamador (OLED + LED + buzzer), confirma cada aviso, entrega pedidos y cobra en mesa |
| **Dueño / Admin** | Responsable del restaurante | Carga y modifica la carta, consulta historial |
| **Botonera ESP32** | Hardware fijo en cada mesa | Captura los 3 botones físicos y los envía al servidor; reporta "mesa N online" |
| **Llamador ESP32** | Hardware que lleva el mozo | Muestra los avisos en cola (OLED), con colores y sonido |
| **Sistema (FastOrder)** | Software central | Recibe pedidos, los totaliza, encola avisos, notifica a cocina y al mozo |

---

## 3. Historias de usuario

Formato: *"Como [rol], quiero [acción], para [beneficio]."*

### Pedido (app + botonera)

| Código | Historia |
|---|---|
| **HU-01** | Como **cliente**, quiero escanear el QR de mi mesa para ver la carta sin esperar al mozo. |
| **HU-02** | Como **cliente**, quiero armar mi pedido en el celular y confirmarlo con el botón "Hacer pedido" de la mesa, para que llegue directo a cocina. |
| **HU-03** | Como **cocina**, quiero ver los pedidos ordenados por llegada con el id de mesa, para prepararlos apenas entran. |
| **HU-04** | Como **cocina**, quiero marcar un pedido como "listo", para que el mozo sepa que está para llevar. |
| **HU-05** | Como **mozo**, quiero saber a qué mesa va cada pedido listo, para entregarlo rápido. |

### Botonera física (llamar mozo / pedir cuenta)

| Código | Historia |
|---|---|
| **HU-06** | Como **cliente**, quiero presionar "Llamar mozo" para que el mozo sepa qué mesa lo llama, sin tener que gritar. |
| **HU-07** | Como **cliente**, quiero presionar "Pedir cuenta" para ver el total de lo consumido en mi celular y que el mozo se acerque a cobrar. |
| **HU-08** | Como **mozo**, quiero recibir en mi llamador qué mesa me llama y por qué motivo, para atender rápido. |
| **HU-09** | Como **mozo**, quiero confirmar cada aviso con un botón, para pasar al siguiente llamado pendiente. |
| **HU-10** | Como **mozo**, quiero que los avisos se atiendan en orden de llegada, para no olvidar ninguna mesa. |

### Menú y administración

| Código | Historia |
|---|---|
| **HU-11** | Como **dueño**, quiero cargar y modificar la carta, para mantener los productos actualizados. |

---

## 4. Requerimientos funcionales (RF)

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
| **RF-09** | El botón "Llamar mozo" debe notificar al llamador del mozo con el número de mesa. |
| **RF-10** | El llamador del mozo debe mostrar el aviso en pantalla OLED (mesa + motivo) y con LED de color. |
| **RF-11** | El llamador del mozo debe sonar (buzzer) cuando llega un aviso y silenciarse al confirmar. |
| **RF-12** | Los avisos al mozo deben manejarse en cola FIFO (orden de llegada); el mozo los confirma con un botón para ver el siguiente. |
| **RF-13** | Si la misma mesa repite el mismo tipo de aviso con uno pendiente, no debe crear duplicados: actualiza el pendiente. |
| **RF-14** | El sistema debe mantener una sesión por mesa: al escanear el QR se retoma el pedido abierto de esa mesa (no se pierde al cerrar la app). |
| **RF-15** | La botonera ESP32 debe reportar "mesa N online" al servidor cuando está activa en la red. |
| **RF-16** | El sistema debe actualizar el estado del pedido y avisar al mozo cuando la cocina marca un pedido como listo. |
| **RF-17** | El dueño debe poder cargar, modificar y quitar productos de la carta. |
| **RF-18** | El sistema debe registrar cada aviso del mozo (llamar mozo / pedir cuenta) con mesa, motivo, fecha, hora y confirmación, en la base de datos local (SQLite). |
| **RF-19** | El registro de avisos debe permitir consultar el historial diario, sin requerir servicios externos (ni Telegram ni smartwatch). |

---

## 5. Requerimientos no funcionales (RNF)

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

---

## 6. Casos de uso

### CU-01: Realizar pedido desde la mesa

| Campo | Detalle |
|---|---|
| **Actor** | Cliente (con apoyo de la Botonera ESP32) |
| **Descripción** | El cliente arma su pedido en el celular y lo confirma con el botón físico "Hacer pedido" de su mesa. |
| **Precondición** | Cliente sentado en la mesa, red WiFi local activa, botonera "mesa N online". |
| **Flujo normal** | 1. El cliente escanea el QR de la mesa. 2. El servidor abre/retoma la sesión de la mesa N y muestra la carta. 3. El cliente selecciona productos y cantidades. 4. Presiona el botón "Hacer pedido" de la botonera. 5. La botonera envía el evento al servidor. 6. El servidor guarda el pedido con estado "recibido" y lo envía a cocina. |
| **Flujo alternativo** | 4a. El cliente envía el pedido directo desde la app (sin botón). · 3a. Carta vacía → mensaje "menú no disponible". · 5a. Botonera offline → el pedido se envía igual desde la app, la botonera se reconecta luego. |
| **Postcondición** | El pedido queda registrado con la sesión de la mesa N, estado "recibido" y visible en cocina. |

### CU-02: Comunicar el pedido a la cocina

| Campo | Detalle |
|---|---|
| **Actor** | Sistema (automático) |
| **Descripción** | El pedido confirmado llega al instante al dashboard de cocina. |
| **Precondición** | Existe un pedido con estado "recibido". |
| **Flujo normal** | 1. El sistema recibe el pedido. 2. Lo valida. 3. Lo muestra en el dashboard de cocina en tiempo real con el id de mesa. 4. La cocina lo marca "en preparación". |
| **Flujo alternativo** | 3a. Dashboard no conectado → el pedido queda guardado y aparece cuando se reconecta. |
| **Postcondición** | El pedido está visible en cocina con su estado. |

### CU-03: Ver estado del pedido

| Campo | Detalle |
|---|---|
| **Actor** | Cliente |
| **Descripción** | El cliente consulta en qué estado está su pedido. |
| **Flujo normal** | 1. El cliente toca "ver mi pedido". 2. El sistema muestra el estado actual (recibido / en preparación / listo / entregado). |
| **Postcondición** | El cliente conoce el estado de su pedido. |

### CU-04: Marcar pedido como listo

| Campo | Detalle |
|---|---|
| **Actor** | Cocina |
| **Descripción** | La cocina avisa que el pedido está terminado. |
| **Precondición** | Existe un pedido en estado "en preparación". |
| **Flujo normal** | 1. La cocina selecciona el pedido. 2. Lo marca como "listo". 3. El sistema actualiza el estado y avisa al mozo que hay un pedido listo para la mesa N. |
| **Flujo alternativo** | 1a. Pedido cancelado por el cliente → la cocina ve "cancelado". |
| **Postcondición** | El pedido pasa a "listo" y el mozo sabe a qué mesa llevarlo. |

### CU-05: Cargar y modificar la carta

| Campo | Detalle |
|---|---|
| **Actor** | Dueño/Admin |
| **Descripción** | El dueño gestiona los productos del menú. |
| **Flujo normal** | 1. El dueño ingresa al panel. 2. Da de alta un producto (nombre, precio, categoría). 3. El sistema lo guarda. 4. El producto aparece en la carta del cliente. |
| **Flujo alternativo** | 2a. Modificar o quitar un producto existente. |
| **Postcondición** | La carta refleja los cambios. |

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
| **Actor** | Cliente (inicia), Mozo (recibe), Llamador ESP32 |
| **Descripción** | El cliente presiona el botón "Llamar mozo" de la botonera. |
| **Precondición** | Botonera "mesa N online", llamador del mozo conectado. |
| **Flujo normal** | 1. El cliente presiona "Llamar mozo". 2. La botonera envía el evento al servidor. 3. El servidor encola el aviso (tipo: llamar_mozo, mesa N). 4. El llamador del mozo muestra "MESA N · te llama" en OLED, LED rojo y buzzer. 5. El mozo confirma. |
| **Flujo alternativo** | 3a. La mesa N ya tiene un "llamar mozo" pendiente → no se duplica, se mantiene el pendiente. · 4a. Llamador offline → el aviso queda en la cola del servidor; cuando se reconecta lo recibe. |
| **Postcondición** | El aviso queda en la cola hasta ser confirmado por el mozo. |

### CU-08: Pedir la cuenta

| Campo | Detalle |
|---|---|
| **Actor** | Cliente (inicia), Mozo (recibe), Sistema |
| **Descripción** | El cliente presiona "Pedir cuenta"; el sistema muestra el total en su celular y avisa al mozo. |
| **Precondición** | La mesa tiene un pedido/sesión con ítems consumidos. |
| **Flujo normal** | 1. El cliente presiona "Pedir cuenta". 2. La botonera envía el evento. 3. El sistema calcula el total (con exactitud de 2 decimales, sin AFIP). 4. El cliente ve en su celular el detalle y el total. 5. El sistema encola el aviso "pedir_cuenta" (mesa N) y el llamador lo muestra (LED verde). 6. El mozo se acerca a cobrar. |
| **Flujo alternativo** | 3a. Mesa sin consumos → mensaje "no hay consumos para facturar". · 5a. El llamador offline → el aviso queda en cola. |
| **Postcondición** | El cliente conoce su total y el mozo fue notificado. |

### CU-09: Confirmar aviso atendido (Llamador del mozo)

| Campo | Detalle |
|---|---|
| **Actor** | Mozo |
| **Descripción** | El mozo confirma que atendió el aviso actual y pasa al siguiente pendiente. |
| **Precondición** | Existe al menos un aviso en la cola. |
| **Flujo normal** | 1. El llamador muestra el aviso actual (mesa + motivo + color + sonido). 2. El mozo presiona el botón "confirmar". 3. El llamador envía la confirmación al servidor. 4. El servidor marca el aviso como "atendido" y muestra el siguiente de la cola. 5. El buzzer se silencia. |
| **Flujo alternativo** | 2a. No hay más avisos → el llamador queda en reposo (sin LED ni sonido). |
| **Postcondición** | Se atienden los avisos uno por uno en orden de llegada. |

### CU-10: Atender múltiples llamados simultáneos

| Campo | Detalle |
|---|---|
| **Actor** | Sistema, Mozo |
| **Descripción** | Cuando varias mesas llaman a la vez, el sistema los encola y el mozo los atiende en orden. |
| **Precondición** | Dos o más botoneras envían avisos. |
| **Flujo normal** | 1. Llegan N avisos de distintas mesas. 2. El servidor los ordena FIFO (por llegada). 3. El llamador muestra el primero con contador de pendientes. 4. Al confirmar, pasa al siguiente. |
| **Flujo alternativo** | 2a. Si una mesa repite un aviso con otro pendiente del mismo tipo → no duplica. |
| **Postcondición** | Todos los avisos se atienden sin pérdida y sin sobrescribirse. |

---

## 7. Arquitectura de crecimiento (documentación complementaria)

El proyecto se presenta como MVP para un local y ~10 mesas, pero la arquitectura
define un camino claro de crecimiento:

### 7.1 Crecimiento de mesas (escalabilidad natural)

Cada botonera es un ESP32 autónomo. Agregar mesas equivale a agregar
dispositivos a la red: no se reescribe código del servidor.

### 7.2 Más dispositivos en red (evolución a MQTT)

Con más de ~30 dispositivos, se recomienda evolucionar de WebSockets a MQTT,
el estándar de comunicación IoT, manteniendo el mismo modelo de datos JSON.

### 7.3 Servidor y datos (de SQLite a PostgreSQL)

Se migra de laptop local + SQLite a un servidor dedicado (Raspberry Pi/PC) o
nube con PostgreSQL. Gracias al uso del ORM desde el inicio, el cambio es de
configuración y no de lógica.

### 7.4 Métricas del dueño (fase opcional)

Pantalla de estadísticas: total por mesa, productos más pedidos, mesas más
activas, consumo por horario. Cierra el círculo del objetivo de negocio.

---

## 8. Cronograma de desarrollo

| Fase | Descripción | Duración aprox. |
|---|---|---|
| 0 | Documentación de entrega + preparación del entorno | 1 semana |
| 1 | Base de datos (carta, mesas, pedidos, sesiones, avisos) | 1 semana |
| 2 | Servidor central (API + WebSockets + cola FIFO) | 1.5 semanas |
| 3 | App del cliente (QR → menú → carrito → ver total) | 1.5-2 semanas |
| 4 | Dashboard cocina (pedidos en vivo + "listo") | 1 semana |
| 5 | Botonera ESP32 (3 botones + "mesa N online") | 1.5 semanas |
| 6 | Llamador ESP32 mozo (OLED + LEDs + buzzer + cola) | 1.5 semanas |
| 7 | QA + pruebas + puesta en marcha | 1 semana |
| **Total** | | **9-10 semanas** |

---

## 9. Beneficiarios

- **Clientes:** atención rápida y sin interrupciones; control de su tiempo y consumo.
- **Personal de cocina:** reciben pedidos estandarizados y directos del consumidor.
- **Dueño del restaurante:** optimización del personal, registro de llamados del día (calidad de atención) y (a futuro) métricas reales de consumo.
- **Mozos:** su rol se transforma a "gestores de experiencia y entrega", reduciendo su carga operativa.