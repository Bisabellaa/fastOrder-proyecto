# 01 · Ver la carta escaneando el QR de la mesa

| Campo | Detalle |
|---|---|
| **Código** | HU-01 |
| **Actor** | Cliente |
| **Módulo** | Frontend · `src/frontend/cliente` |
| **Prioridad** | Alta |
| **Estado** | Especificado |

## Historia de usuario

Como **cliente**, quiero escanear el QR de mi mesa para ver la carta sin esperar
al mozo.

## Criterios de aceptación

- [ ] Al escanear el QR de la mesa, el sistema abre la carta digital de esa mesa.
- [ ] La carta muestra cada producto con nombre, descripción, precio y categoría.
- [ ] El servidor crea la sesión de la mesa N, o retoma la que ya estaba abierta.
- [ ] Si la carta no tiene productos cargados, el cliente ve "menú no disponible".
- [ ] La interfaz se ve correctamente en pantalla de celular.
- [ ] Todo funciona sobre la red WiFi local, sin Internet.

## Criterios de no aceptación

- El cliente no tiene que pedirle la carta a nadie.
- El cliente no ve datos de otras mesas (RF-14).

## Requerimientos relacionados

- **RF-01**, **RF-02**, **RF-14**, **RF-17**
- **RNF-01**, **RNF-04**

## Casos de uso relacionados

Ninguno asignado. Es el punto de entrada de [CU-01](02-hacer-pedido.md#caso-de-uso-cu-01),
que se activa en cuanto el cliente tiene la carta abierta.

## Notas de diseño

El QR contiene la dirección del servidor en la red local más el número de mesa.
No apunta a un sitio público: es una URL de la LAN del restaurante
(por ejemplo `http://192.168.1.20:3000/m/7`).
