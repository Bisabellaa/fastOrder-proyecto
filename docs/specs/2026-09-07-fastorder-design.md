# FastOrder — Documento de Diseño (Versión 4)

Fecha: 2026-09-07 (v4)
Estado: V4 para presentación al docente. Sobre la v3 cambia el hardware de
notificación al mozo: el llamador ESP32 (OLED + LEDs + buzzer + botón) se
reemplaza por un **dashboard web del mozo** en la red local. Se mantiene el
registro de llamados del día persistido en SQLite (costo cero).

## 1. Resumen

FastOrder es un sistema de autoservicio para restaurantes de sushi. Permite
que el cliente realice su pedido desde su propio celular escaneando un QR
pegado en su mesa, que el pedido llegue al instante a la cocina, y que una
botonera física con ESP32 (tres botones: hacer pedido, llamar mozo, pedir
cuenta) conecte la mesa física con el sistema digital. El mozo recibe los
avisos en un **dashboard web** abierto en la red local, con un color por
motivo y confirmación de cada aviso atendido.

Cada aviso del mozo queda registrado en la base del sistema (mesa, motivo,
hora, confirmación). Este registro es un indicador de calidad de atención y
permite medir los tiempos de respuesta del sistema.

Sistema de autogestión de pedidos: Cliente → Sistema → Cocina/Sala, sin
intermediarios manuales en la toma de órdenes.

Alcance de facturación: el sistema NO emite factura fiscal (sin AFIP).
Solo calcula y muestra el total de lo consumido por mesa.

## 2. Objetivos

### General

Desarrollar una aplicación de autogestión de pedidos para clientes mediante
una aplicación móvil (web responsiva) y una botonera integrada con ESP32,
permitiendo una atención autónoma, rápida y sin intermediarios manuales en la
toma de órdenes.

### Específicos

- Otorgar autonomía al cliente para realizar pedidos desde su dispositivo o
  mediante la interfaz física de la mesa.
- Eliminar las demoras causadas por la toma de pedidos manual del mozo.
- Reducir las confusiones al registrar órdenes (pedido estandarizado y directo).
- Entregar la orden a cocina en el instante en que el cliente la confirma
  (app o botonera).
- Permitir al cliente solicitar al mozo sin llamarlo reiteradamente.
- Mostrar al cliente el total de su consumo al pedir la cuenta (sin facturación fiscal).
- Que el alumno adquiera conocimientos de desarrollo (backend, frontend,
  base de datos, hardware, tiempo real, QA).
- Optimizar el personal de salón (entrega en lugar de toma de datos) y
  aumentar la rotación de mesas.

## 3. Problema que resuelve

La mala atención del personal, los tiempos de espera excesivos y la confusión
al tomar pedidos, permitiendo al cliente solicitar productos sin llamar
reiteradamente al mozo.

### Beneficiarios

- **Clientes:** atención rápida y sin interrupciones; control de su tiempo y
  de su consumo (ven su total).
- **Cocina:** pedidos estandarizados y directos del consumidor.
- **Mozos:** su rol se transforma a "gestores de experiencia y entrega";
  reciben llamados claros con número de mesa y motivo en su dashboard.
- **Dueño:** optimización del personal y (a futuro) métricas reales de consumo.

## 4. Alcance y MVP

### MVP (Producto Mínimo Viable)

Clientes escanean un QR, ven la carta, arman su pedido en la app y lo
confirman con el botón "Hacer pedido" de la botonera. La cocina ve el pedido
en vivo con el id de mesa. Los botones "Llamar mozo" y "Pedir cuenta" generan
avisos que aparecen en el dashboard del mozo con un color por motivo
(rojo = llaman, verde = cuenta, azul = pedido listo). Al pedir cuenta, el
cliente ve el total en su celular. Los avisos al mozo se manejan con cola FIFO
y confirmación desde la pantalla. Sesión persistente por mesa.

### Fuera del MVP (pos-pedido)

- Métricas y estadísticas del dueño (documentadas como crecimiento).
- Pago digital / división de cuenta.
- Combos, descuentos, múltiples menús.
- Panel de administración completo (productos se cargan directo en la base).
- Emisión de factura fiscal / integración AFIP (fuera de alcance por decisión
  del profesor).

## 5. Arquitectura

### Enfoque: Red local del restaurante (LAN)

El servidor corre en una laptop/computadora del local y toda la comunicación
ocurre por la red WiFi local. Funciona sin Internet.

```
                     ┌─ BOTONERA ESP32 (en cada mesa) ─┐
  CLIENTE            │   ▢ Hacer pedido (confirma app)  │
  (celular + QR) ───▶│   ▢ Llamar mozo                   │
  viaja el pedido    │   ▢ Pedir cuenta                  │
  de la app          └──────────┬───────────────────────┘
                               │ WiFi / WebSocket
                               ▼
                   ┌───── SERVIDOR (Node + SQLite) ─────┐
                   │  · pedidos + estados                │
                   │  · TOTAL de consumo (sin AFIP)      │
                   │  · cola FIFO de avisos              │
                   └────┬──────────────────┬────────────┘
                        │                  │
                        ▼                  ▼
               ┌── COCINA ──┐      ┌── MOZO ──┐      ┌── CLIENTE ──┐
               │ dashboard  │      │ dashboard│      │ celular     │
               │ web        │      │ web      │      │ ve TOTAL    │
               │ pedidos +  │      │ cola de  │      │ al pedir    │
               │ id de mesa │      │ avisos   │      │ cuenta      │
               │ marca      │      │ (mesa +  │      └─────────────┘
               │ "listo"    │      │ motivo + │
               └────────────┘      │ color)   │
                                   └──────────┘
```

Las dos pantallas del personal (cocina y mozo) son páginas web servidas por el
mismo servidor y abiertas en la red local del restaurante. La v3 de este
documento usaba para el mozo un dispositivo ESP32 aparte ("llamador") con
pantalla OLED, LEDs y buzzer; la v4 lo reemplaza por el dashboard web del mozo,
lo que deja un solo dispositivo de hardware por mesa.

### Flujo del sistema

1. Cliente sentado en la mesa escanea el QR (contiene dirección del servidor
   + número de mesa).
2. El celular abre la carta (web responsiva) sobre la red WiFi local.
   El servidor crea/retoma la sesión abierta de la mesa N.
3. La botonera ESP32, conectada al mismo WiFi, se presenta ante el servidor:
   "MESA N ONLINE".
4. El cliente arma su pedido en la app. Presiona el botón "Hacer pedido" de la
   botonera → el servidor confirma el pedido abierto de esa mesa y lo guarda
   en SQLite.
5. El dashboard de cocina muestra el pedido en vivo: "MESA N: productos", solo
   con id de mesa y en orden de llegada.
6. Cuando cocina marca "listo", el sistema actualiza el estado y avisa al mozo.
7. Botones de la botonera:
   - "Llamar mozo" → cola FIFO → dashboard del mozo: "MESA N" en color rojo.
   - "Pedir cuenta" → el cliente ve total en su celular + dashboard del mozo en
     color verde.
8. El mozo confirma cada aviso desde el dashboard → pasa al siguiente pendiente.
9. Cuando la cocina marca un pedido como "listo", el dashboard del mozo muestra
   ese pedido en color azul.

### Sesión de mesa (mejora 1)

El servidor mantiene una sesión/estado por mesa:

- Al escanear el QR, se crea (o retoma) el "pedido abierto de la mesa N".
- El botón "Hacer pedido" confirma el pedido abierto; "Pedir cuenta" calcula
  el total y cierra la cuenta.
- Si el cliente cierra la app o se reconecta, no pierde su pedido.

### Alerta sonora

El dashboard del mozo emite un sonido de alerta cuando llega un aviso y se
corta al confirmar. Está pensado para una sala ruidosa. Como es una página web,
el navegador exige que el usuario haya interactuado con la página antes de
permitir sonido: queda anotado como riesgo de QA en la Fase 7.

### Registro de llamados del día

Cada aviso (llamar mozo / pedir cuenta) queda guardado en la base del sistema
con mesa, motivo, fecha/hora y confirmación. Es el historial del día: permite
resolver quejas con evidencia, medir la carga de trabajo de los mozos y
validar los tiempos de respuesta del sistema (QA). No requiere ninguna
tecnología adicional (ni Telegram ni smartwatch): es persistencia de datos
que el servidor ya procesa.

### Módulos del sistema

| # | Módulo | Dónde vive | Qué hace | Depende de |
|---|--------|-------------|----------|------------|
| 1 | Base de datos (SQLite) | `src/backend/db` | Guarda carta, mesas, pedidos, sesiones, avisos | nada |
| 2 | Servidor central (Node.js) | `src/backend/server` | API + WebSockets + cola FIFO de avisos | base de datos |
| 3 | App del cliente (web) | `src/frontend/cliente` | QR → menú → carrito → confirmar con botón; ver total | servidor |
| 4 | Dashboard cocina (web) | `src/frontend/dashboard-cocina` | Muestra pedidos (id de mesa) en vivo; marca "listo" | servidor |
| 5 | Dashboard mozo (web) | `src/frontend/dashboard-mozo` | Cola de avisos (mesa + motivo + color); confirma cada uno | servidor |
| 6 | Botonera ESP32 (C++) | `src/backend/hardware` | 3 botones físicos; reporta "mesa N online" | servidor |

## 6. Tecnologías

| Pieza | Tecnología | Motivo |
|---|---|---|
| Servidor central | Node.js + JavaScript | Tiempo real (WebSockets) nativo y sencillo; mismo idioma en todo el stack |
| Dashboard cocina | HTML + CSS + JavaScript | Misma tecnología que el servidor |
| Dashboard mozo | HTML + CSS + JavaScript | Misma tecnología que el servidor |
| App del cliente | Web responsiva (PWA) | Un solo idioma en todo el sistema; funciona para un restaurante real |
| Base de datos | SQLite (vía ORM migrable a PostgreSQL) | Cero configuración; migrable sin reescribir código |
| Botonera mesa | C/C++ (ESP32, Arduino) | Lenguaje de la placa; comunicación por red |
| Comunicación | WebSockets (JSON sobre WiFi local) | Tiempo real; MQTT documentado como evolución futura |

Regla del proyecto: JavaScript para todo lo que corre en computadoras y
celulares; C++ para las placas. La comunicación entre piezas es por la red
(mensajes JSON), no por lenguaje.

### Modelo de datos (básico)

- **Producto:** id, nombre, descripcion, precio, categoria.
- **Mesa:** id (o codigo QR), numero.
- **Pedido:** id, mesa, estado, fecha.
- **ItemPedido:** id, pedido_id, producto_id, cantidad.
- **SesionMesa:** mesa_id, pedido_abierto_id, activa.
- **AvisoMozo:** id, mesa, tipo (llamar_mozo | pedir_cuenta | pedido_listo), estado (pendiente/atendido), fecha, hora, hora_confirmacion.
- **EstadoMesa:** mesa_id, online (enviado por ESP32).

## 7. Cronograma (actualizado por la v4)

| Fase | Qué construimos | Resultado visible | Duración aprox. |
|---|---|---|---|
| 0 | Documentación de entrega + preparación del entorno | Documento + compu lista | 1 semana |
| 1 | Base de datos: carta, mesas, pedidos, sesiones, avisos | "Puedo cargar productos" | 1 semana |
| 2 | Servidor central: API + WebSockets + cola FIFO | "Pruebo pedidos y avisos con Postman" | 1.5 semanas |
| 3 | App del cliente: QR → menú → carrito → ver total | "Pido sushi desde el celular" | 1.5-2 semanas |
| 4 | Dashboard cocina: pedidos en vivo + "listo" | "La cocina ve el pedido al instante" | 1 semana |
| 5 | Botonera ESP32: 3 botones + "mesa N online" | "Los botones envían al sistema" | 1.5 semanas |
| 6 | Dashboard mozo: cola de avisos + color + confirmación | "El mozo recibe avisos con mesa y motivo" | 1 semana |
| 7 | QA + pruebas + ajustes | Sistema probado de punta a punta | 1 semana |

Total estimado: 8-9 semanas. Sobre la v3 se ahorra media semana: la fase 6 ya
no es construir un dispositivo ESP32 con OLED, LEDs y buzzer, sino una pantalla
web que reaprovecha la infraestructura de la fase 4.

## 8. Arquitectura de crecimiento (documentada, no construida)

Camino de escalabilidad del sistema, para integrar en la memoria/defensa:

- **Crecimiento de mesas (escalabilidad natural):** cada botonera es un ESP32
  autónomo; agregar mesas = agregar dispositivos, no reescribir nada.
- **Más dispositivos en red:** la v4 ya no necesita evolucionar a MQTT por
  cantidad de dispositivos: queda un ESP32 por mesa. El salto a MQTT queda
  documentado para una fase posterior de crecimiento, con el mismo modelo JSON.
- **Servidor y datos:** migrar de laptop local + SQLite a servidor dedicado
  (Raspberry Pi/PC) o nube, con PostgreSQL vía el ORM (cambio de configuración,
  sin reescribir lógica).
- **Métricas del dueño (fase opcional):** pantalla con total por mesa,
  productos más pedidos, mesas más activas, consumo por horario.

## 9. Riesgos y decisiones pendientes

- Dependencia de red WiFi local de calidad en la demo.
- El QR por mesa requiere generar códigos QR (simple, se resuelve al crear
  las mesas).
- Si el profesor exige MySQL/PostgreSQL, migrar es un cambio de configuración
  del ORM.
- La comunicación del ESP32 dependerá del modelo de placa (ESP32 con WiFi).
- Compra/stock de componentes: botones táctiles, cables, fuente y placa ESP32 por
  mesa. Ya no se necesitan OLED, LEDs ni buzzer (se eliminaron al pasar el aviso
  del mozo a un dashboard web).
- El navegador puede bloquear la alerta sonora del dashboard del mozo hasta que
  el usuario interactúa con la página: hay que probarlo en la Fase 7.
- El total se calcula sin errores de redondeo (2 decimales).

## 10. Metodología de trabajo

- Construcción incremental módulo a módulo, de abajo hacia arriba.
- Cada módulo se prueba de forma aislada antes de integrarse con el anterior.
- Uso de Git para versionado y control de cambios desde la Fase 0.
- Fases 1 a 6 siguen un ciclo de desarrollo guiado, explicando qué se hace y
  por qué antes de escribir cada parte.
- QA al final (fase 7): pruebas de salón, estrés y puesta en marcha.