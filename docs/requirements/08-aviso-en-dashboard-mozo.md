# 08 · Ver en su pantalla qué mesa lo llama y por qué

| Campo | Detalle |
|---|---|
| **Código** | HU-08 |
| **Actor** | Mozo |
| **Módulo** | Frontend · `src/frontend/dashboard-mozo` |
| **Prioridad** | Alta |
| **Estado** | Especificado |

## Historia de usuario

Como **mozo**, quiero que en mi pantalla aparezca qué mesa me llama y por qué
motivo, para atender rápido.

## Criterios de aceptación

- [ ] El dashboard del mozo muestra el número de mesa y el motivo del aviso actual.
- [ ] Cada motivo tiene un color propio:
      - **rojo** = `llamar_mozo`
      - **verde** = `pedir_cuenta`
      - **azul** = `pedido listo`
- [ ] Al llegar un aviso se emite una alerta sonora.
- [ ] El dashboard se actualiza solo, sin recargar la página.
- [ ] El dashboard funciona en la red local del restaurante, sin Internet.

## Requerimientos relacionados

- **RF-09**, **RF-10**, **RF-11**
- **RNF-01**, **RNF-12**

## Casos de uso relacionados

### CU-07: Llamar al mozo — paso 4

Ver [06-llamar-mozo.md](06-llamar-mozo.md#cu-07-llamar-al-mozo).

## Nota de decisión

**Antes:** el mozo recibía los avisos en un dispositivo ESP32 ("llamador") con
pantalla OLED, LED de color y buzzer.

**Ahora:** el mozo recibe los avisos en un **dashboard web** (`src/frontend/dashboard-mozo`)
abierto en la red local del restaurante.

El cambio no altera los requisitos observables —el mozo sigue sabiendo qué mesa
lo llama y por qué— sino el dispositivo donde se muestran. Se conservan los tres
colores como señal visual y la alerta sonora pasa a ser el sonido del navegador.

**Consecuencia técnica:** al ser una página web, la alerta sonora requiere que
el navegador tenga permiso de audio concedido (el navegador la bloquea en la
primera visita hasta que el usuario interactúa). Queda como riesgo Known en la
Fase 7 (QA).
