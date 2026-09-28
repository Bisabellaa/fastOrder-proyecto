# FastOrder — Fase 0: Documentación de entrega + Preparación del entorno — Plan

> **Para agentes:** Este es un plan de aprendizaje colaborativo. Se ejecuta en
> conjunto con la alumna, que escribe cada paso y verifica cada resultado.
> **REQUIRED SUB-SKILL:** no aplica (no es un proyecto delegado a subagentes).

**Goal:** Entregar la documentación inicial (spec, análisis, diagrama de casos
de uso) y dejar la computadora lista y el proyecto iniciado con estructura de
carpetas y control de versiones Git, sin escribir lógica de negocio aún.

**Architecture:** Fase cero del proyecto FastOrder (versión 4 del diseño).
La documentación de entrega (spec v4, `docs/requirements/`,
`docs/diagrams/diagrama-casos-de-uso.drawio`) ya está generada. En esta fase se
prepara el entorno (Node.js, VS Code, Git — ya instalados), se crea la estructura
de carpetas del proyecto acorde al diseño v4 (que incluye la botonera ESP32 de
mesa y un dashboard web para el mozo), se inicializa Git y se verifica que todo
funciona con un "hola mundo" mínimo del servidor Node. No hay módulos de negocio
todavía.

**Tech Stack:** Node.js 22, npm 10, VS Code, Git 2.46, PowerShell.

**Spec:** `docs/specs/2026-09-07-fastorder-design.md`

## Global Constraints

- Todo el proyecto trabaja sobre la red WiFi local (no Internet) — Enfoque B del spec v4.
- El repo debe estar inicializado en la raíz `fast_oder/` y commiteado por fase.
- El stack no cambia: JavaScript para servidor y frontends; C++ para la botonera
  ESP32 de cada mesa (Fase 5). No hay segundo dispositivo de hardware: el mozo
  usa un dashboard web.
- La documentación de entrega (spec, `docs/requirements/` con RF/RNF/HU/CU,
  `docs/diagrams/diagrama-casos-de-uso.drawio`) se considera completa y debe
  conservarse versionada desde esta fase.
- La estructura del proyecto es `docs/` (documentación) y `src/` (código,
  dividido en `frontend/` y `backend/`). Referencia: `overview.md`.
- No se avanza a la Fase 1 sin completar y verificar todos los pasos de esta fase.
- Sistema operativo: Windows; shell: PowerShell 5.1.

---

### Task 0.1: Verificar que las herramientas ya instaladas responden

**Files:**
- Test: ninguno (verificación del sistema)

**Interfaces:**
- Consumes: nada
- Produces: confirmación de que `node`, `npm`, `git` y `code` responden correctamente

- [ ] **Step 1: Verificar Node.js**

Abrí una ventana de PowerShell y ejecutá:

```powershell
node --version
```

Expected: algo como `v22.15.0`. Si muestra una versión, Node funciona.

- [ ] **Step 2: Verificar npm**

```powershell
npm --version
```

Expected: `10.9.2` (o similar). 

- [ ] **Step 3: Verificar Git**

```powershell
git --version
```

Expected: `git version 2.46.0.windows.1` (o similar).

- [ ] **Step 4: Verificar VS Code**

```powershell
code --version
```

Expected: `1.136.1` (o similar).

- [ ] **Step 5: Si alguna de las 4 herramientas no respondió**

Instalala desde su página oficial antes de seguir.
- Node.js: https://nodejs.org (descargar LTS e instalar)
- Git: https://git-scm.com
- VS Code: https://code.visualstudio.com

Después de instalar, cerrá y reabrí la ventana de PowerShell y repetí los pasos 1-4.

---

### Task 0.2: Crear la estructura de carpetas del proyecto

**Files:**
- Create: `fast_oder/` (raíz del proyecto — ya existe)
- Create: `fast_oder/src/frontend/cliente/` — web del cliente (menú)
- Create: `fast_oder/src/frontend/dashboard-cocina/` — web del dashboard de cocina
- Create: `fast_oder/src/frontend/dashboard-mozo/` — web del dashboard del mozo
- Create: `fast_oder/src/backend/server/` — código del servidor Node.js
- Create: `fast_oder/src/backend/db/` — archivos de base de datos SQLite
- Create: `fast_oder/src/backend/hardware/` — código C++ de la botonera de mesa (Fase 5)
- Existe: `fast_oder/docs/` — documentación (spec, requerimientos, diagramas, planes)

**Interfaces:**
- Consumes: Task 0.1 (entorno verificado)
- Produces: la estructura de carpetas que todas las fases siguientes usan

- [ ] **Step 1: Crear las carpetas**

En PowerShell, ubicada en `C:\Users\brend\Desktop\fast_oder`, ejecutá:

```powershell
New-Item -ItemType Directory -Force -Path src\frontend\cliente, src\frontend\dashboard-cocina, src\frontend\dashboard-mozo, src\backend\server, src\backend\db, src\backend\hardware
```

Nota: estas carpetas ya existen (se crearon junto con la documentación), así que
`-Force` no hace daño. El comando queda anotado por si hay que rehacerlo.

- [ ] **Step 2: Verificar que existen**

```powershell
Get-ChildItem -Recurse -Directory src
```

Expected: se ven `src\frontend` (con `cliente`, `dashboard-cocina`,
`dashboard-mozo`) y `src\backend` (con `server`, `db`, `hardware`).

- [ ] **Step 3: Commit de estructura inicial**

```powershell
git add .
git commit -m "chore: estructura inicial de carpetas del proyecto FastOrder"
```

---

### Task 0.3: Inicializar el proyecto Node.js en la carpeta del servidor

**Files:**
- Create: `fast_oder/src/backend/server/package.json`

**Interfaces:**
- Consumes: Task 0.2 (carpetas creadas)
- Produces: `src/backend/server/package.json` — el "documento de identidad" del
  servidor, donde se listan dependencias y scripts

- [ ] **Step 1: Inicializar el proyecto Node en el servidor**

```powershell
Set-Location src/backend/server
npm init -y
```

Explicación: `npm init -y` crea `package.json` con valores por defecto.
Es donde npm guarda qué librerías usa tu servidor.

- [ ] **Step 2: Verificar**

```powershell
Get-Content package.json
```

Expected: un JSON con `"name": "server"` y una `"version"`.

---

### Task 0.4: Servidor mínimo "hola mundo" que responde en el navegador

**Files:**
- Create: `fast_oder/src/backend/server/server.js`

**Interfaces:**
- Consumes: Task 0.3 (package.json creado)
- Produces: `server.js` corriendo en el puerto 3000; verificación del flujo
  "celular → laptop" por la red local (mismo principio que usará todo el
  sistema), aunque sea contra `localhost` por ahora.

- [ ] **Step 1: Escribir el archivo `server.js`**

Creá `src/backend/server/server.js` con este contenido (y NO lo pegues sin
entenderlo — preguntame cada línea que no entiendas):

```javascript
const http = require('http');

const port = 3000;

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain; charset=utf-8' });
  res.end('FastOrder: el servidor esta vivo!');
});

server.listen(port, () => {
  console.log(`FastOrder escuchando en http://localhost:${port}`);
});
```

- [ ] **Step 2: Correr el servidor**

```powershell
Set-Location src/backend/server
node server.js
```

Expected: en la consola aparece `FastOrder escuchando en http://localhost:3000`.

- [ ] **Step 3: Probarlo en el navegador**

Abrí Chrome y andá a `http://localhost:3000`.
Expected: aparece el texto `FastOrder: el servidor esta vivo!`.
Para detener el servidor: `Ctrl + C` en la consola.

- [ ] **Step 4: Commit**

```powershell
git add src/backend/server/server.js
git commit -m "feat: servidor minimo hola-mundo FastOrder"
```

---

### Task 0.5: Probar el acceso desde el celular por la red local (preparación)

**Files:**
- Test: ninguno (verificación de red)

**Interfaces:**
- Consumes: Task 0.4 (servidor corriendo)
- Produces: la base del flujo final: celular → laptop por WiFi. En Fase 2
  este mismo truco servirá para el QR de la mesa.

- [ ] **Step 1: Encontrar la IP local de tu laptop**

En PowerShell:

```powershell
ipconfig
```

Buscar `Dirección IPv4` de la adaptadora de red activa. Normalmente algo como
`192.168.1.x` o `192.168.0.x`. Anotá ese número.

- [ ] **Step 2: Correr el servidor y probar con el celular**

1. En la laptop: `node server.js` (debe estar corriendo, no cerrar la consola).
2. Conectá el celular a la **misma red WiFi** que la laptop.
3. En el navegador del celular ingresá `http://<IP-de-tu-laptop>:3000`
   (ejemplo: `http://192.168.1.20:3000`).

Expected: el celular muestra `FastOrder: el servidor esta vivo!`.

Si no funciona, verificá: mismo WiFi, marcar `http://` (no https), el firewall
de Windows puede estar bloqueando el puerto 3000 (podemos verlo juntos).

- [ ] **Step 3: Anotar resultado**

Anotá en `docs/setup-notes.md` (crealo) la IP local y si el celular accedió.
No hacen falta más cambios de código en esta tarea.

---

## Self-Review de este plan

1. **Spec coverage:** Este plan cubre la Fase 0 del cronograma del spec
   ("Preparación: Node, editor, Git, estructura del proyecto — compu lista,
   proyecto iniciado"). Las fases 1-7 se planifican por separado cuando llega
   su momento.
2. **Placeholder scan:** sin TBD/TODO; todos los pasos tienen comandos
   concretos y resultados esperados.
3. **Type consistency:** el puerto 3000 es consistente en Task 0.4 y 0.5; la
   estructura de carpetas de Task 0.2 coincide con los módulos del spec v4 y
   con lo documentado en `overview.md` y en `src/frontend.md` / `src/backend.md`.