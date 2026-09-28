# 11 · Cargar y modificar la carta

| Campo | Detalle |
|---|---|
| **Código** | HU-11 |
| **Actor** | Dueño / Admin |
| **Módulo** | Frontend · `src/frontend/dashboard-cocina` (pendiente de definir) |
| **Prioridad** | Media |
| **Estado** | Especificado |

## Historia de usuario

Como **dueño**, quiero cargar y modificar la carta, para mantener los productos
actualizados.

## Criterios de aceptación

- [ ] El dueño puede dar de alta un producto con nombre, descripción, precio y categoría.
- [ ] El dueño puede modificar un producto existente.
- [ ] El dueño puede quitar un producto de la carta.
- [ ] Los cambios se ven reflejados en la carta del cliente.
- [ ] La carta puede quedar vacía; en ese caso el cliente ve "menú no disponible".

## Requerimientos relacionados

- **RF-02**, **RF-17**
- **RNF-06**

## Casos de uso relacionados

### CU-05: Cargar y modificar la carta

| Campo | Detalle |
|---|---|
| **Actor** | Dueño/Admin |
| **Descripción** | El dueño gestiona los productos del menú. |
| **Flujo normal** | 1. El dueño ingresa al panel. 2. Da de alta un producto (nombre, precio, categoría). 3. El sistema lo guarda. 4. El producto aparece en la carta del cliente. |
| **Flujo alternativo** | 2a. Modificar o quitar un producto existente. |
| **Postcondición** | La carta refleja los cambios. |

## Nota de decisión

**Pendiente de definir:** la especificación de diseño (v3) declaraba el panel de
administración completo como **fuera del MVP**, y los productos se
cargaban directo en la base de datos. El RF-17 y esta HU existen, pero el MVP
de la Fase 1 arranca con los productos precargados.

Hay que decidir dónde vive este panel antes de implementarlo. Opciones: una
tercera app web `src/frontend/dashboard-admin/`, o una vista más del dashboard
del dueño. Queda como decisión abierta en la Fase 1.
