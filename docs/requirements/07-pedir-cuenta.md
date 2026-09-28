# 07 · Pedir la cuenta y ver el total

| Campo | Detalle |
|---|---|
| **Código** | HU-07 |
| **Actor** | Cliente (inicia), Mozo (recibe), Sistema |
| **Módulo** | Frontend · `src/frontend/cliente` + Backend |
| **Prioridad** | Alta |
| **Estado** | Especificado |

## Historia de usuario

Como **cliente**, quiero presionar "Pedir cuenta" para ver el total de lo consumido
en mi celular y que el mozo se acerque a cobrar.

## Criterios de aceptación

- [ ] Al presionar "Pedir cuenta", el sistema calcula el total de lo consumido por
      esa mesa con exactitud de 2 decimales y sin errores de redondeo.
- [ ] El cliente ve en su celular el detalle de los ítems consumidos y el total.
- [ ] El sistema encola un aviso de tipo `pedir_cuenta` para la mesa N.
- [ ] El dashboard del mozo muestra el aviso con color verde.
- [ ] El total queda registrado en la base de datos para el historial del día.
- [ ] Si la mesa no tiene consumos, el cliente ve "no hay consumos para facturar".

## Criterios de no aceptación

- El sistema **no emite factura fiscal**. No hay integración con AFIP.
- El total mostrado es solo informativo: el cobro lo realiza el mozo en mesa.

## Requerimientos relacionados

- **RF-07**, **RF-08**, **RF-18**, **RF-19**
- **RNF-10**

## Casos de uso relacionados

### CU-08: Pedir la cuenta

| Campo | Detalle |
|---|---|
| **Actor** | Cliente (inicia), Mozo (recibe), Sistema |
| **Descripción** | El cliente presiona "Pedir cuenta"; el sistema muestra el total en su celular y avisa al mozo. |
| **Precondición** | La mesa tiene un pedido/sesión con ítems consumidos. |
| **Flujo normal** | 1. El cliente presiona "Pedir cuenta". 2. La botonera envía el evento. 3. El sistema calcula el total (con exactitud de 2 decimales, sin AFIP). 4. El cliente ve en su celular el detalle y el total. 5. El sistema encola el aviso "pedir_cuenta" (mesa N) y el dashboard del mozo lo muestra con color verde. 6. El mozo se acerca a cobrar. |
| **Flujo alternativo** | 3a. Mesa sin consumos → mensaje "no hay consumos para facturar". · 5a. El dashboard del mozo desconectado → el aviso queda en cola. |
| **Postcondición** | El cliente conoce su total y el mozo fue notificado. |

## Notas de diseño

El cálculo del total se hace con enteros en centavos para evitar el error de
redondeo en coma flotante (ver RNF-10). "Consumido" incluye todo lo que la mesa
pidió, esté entregado o no.
