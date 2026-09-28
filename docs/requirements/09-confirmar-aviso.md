# 09 · Confirmar cada aviso atendido

| Campo | Detalle |
|---|---|
| **Código** | HU-09 |
| **Actor** | Mozo |
| **Módulo** | Frontend · `src/frontend/dashboard-mozo` + Backend |
| **Prioridad** | Alta |
| **Estado** | Especificado |

## Historia de usuario

Como **mozo**, quiero confirmar cada aviso atendido para pasar al siguiente
pendiente.

## Criterios de aceptación

- [ ] El dashboard muestra un botón de confirmar sobre el aviso actual.
- [ ] Al confirmar, el servidor marca el aviso como "atendido" y registra la hora
      de confirmación.
- [ ] La alerta sonora se silencia al confirmar.
- [ ] Inmediatamente después de confirmar, el dashboard muestra el siguiente aviso
      de la cola.
- [ ] Si no hay más avisos, el dashboard queda en reposo: sin color, sin sonido.
- [ ] La confirmación queda guardada en el historial del día (RF-18).

## Requerimientos relacionados

- **RF-11**, **RF-12**, **RF-18**
- **RNF-09**

## Casos de uso relacionados

### CU-09: Confirmar aviso atendido (dashboard del mozo)

| Campo | Detalle |
|---|---|
| **Actor** | Mozo |
| **Descripción** | El mozo confirma que atendió el aviso actual y pasa al siguiente pendiente. |
| **Precondición** | Existe al menos un aviso en la cola. |
| **Flujo normal** | 1. El dashboard muestra el aviso actual (mesa + motivo + color + sonido). 2. El mozo presiona el botón "confirmar". 3. El navegador envía la confirmación al servidor. 4. El servidor marca el aviso como "atendido" y muestra el siguiente de la cola. 5. La alerta sonora se silencia. |
| **Flujo alternativo** | 2a. No hay más avisos → el dashboard queda en reposo (sin color ni sonido). |
| **Postcondición** | Se atienden los avisos uno por uno en orden de llegada. |

## Nota de decisión

El paso 3 pasó de "el llamador envía la confirmación" (botón físico del ESP32) a
"el navegador envía la confirmación" (clic en el dashboard). El resto del flujo
no cambia: la cola FIFO y el registro de la hora de confirmación siguen siendo
responsabilidad del servidor.
