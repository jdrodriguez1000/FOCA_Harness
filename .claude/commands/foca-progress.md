---
description: Actualiza los 4 archivos de memoria (800_persistence) con lo realizado en la sesión, hace commit y push al repositorio remoto.
allowed-tools: Read, Edit, Write, Bash(git status), Bash(git add:*), Bash(git commit:*), Bash(git push:*), Bash(git pull:*), Bash(git remote:*), Bash(git log:*), Bash(git diff:*), Bash(git branch:*), Bash(git init)
---

# /foca-progress — Cierre de sesión: actualizar memoria + commit + push

Eres responsable de **cerrar la sesión correctamente** según el protocolo de memoria definido en `CLAUDE.md`. Ejecuta estos pasos en orden y NO te saltes ninguno.

## Repositorio remoto
`https://github.com/jdrodriguez1000/FOCA_Harness.git`

---

## Paso 1 — Revisar lo realizado en la sesión

1. Revisa la conversación de esta sesión y lista los cambios concretos: archivos creados/modificados, decisiones tomadas, tareas avanzadas, lecciones aprendidas.
2. Lee el estado actual de los 4 archivos de memoria para no duplicar ni contradecir:
   - `800_persistence/progress.md`
   - `800_persistence/tasks.md`
   - `800_persistence/lessons.md`
   - `800_persistence/decisions.md`
   > Usa el índice al inicio de cada archivo para ubicarte rápido.

## Paso 2 — Actualizar la memoria (`800_persistence/`)

Actualiza SOLO lo que cambió en esta sesión:

- **`progress.md`**
  - Actualiza la sección "¿En qué punto estamos?" y "Hecho hasta el momento" si avanzó la fase.
  - Agrega una fila nueva a la **Bitácora de sesiones** con la fecha de hoy, el id de sesión siguiente (S2, S3, …) y un resumen de lo hecho.
- **`tasks.md`**
  - Cambia el estado de las tareas tocadas (`✅ Completada` · `🏗️ En progreso` · `🔜 Pendiente` · `⛔ Bloqueada` · `❌ Descartada`).
  - Agrega tareas nuevas con su `T-###` (incremental, no reusar ids).
  - Mueve las completadas a su sección, con la fecha.
- **`decisions.md`**
  - Registra cada decisión nueva como `D-###` (estado `✔️ Vigente`). Si se revirtió una, márcala `🔄 Reemplazada` y enlaza la nueva. Nunca borres decisiones.
- **`lessons.md`**
  - Agrega lecciones `L-###` si algo enseñó algo útil (negocio / técnica / proceso).

> Mantén actualizados los **índices** de cada archivo si agregas secciones nuevas.

## Paso 3 — Commit y push

1. Verifica que exista repositorio git y remoto:
   ```bash
   git rev-parse --is-inside-work-tree 2>/dev/null || git init
   git remote get-url origin 2>/dev/null || git remote add origin https://github.com/jdrodriguez1000/FOCA_Harness.git
   ```
2. Revisa qué cambió:
   ```bash
   git status
   git diff --stat
   ```
3. Agrega y haz commit con un mensaje claro en español que resuma la sesión:
   ```bash
   git add -A
   git commit -m "<resumen de la sesión>"
   ```
   El mensaje del commit debe terminar con:
   ```
   Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>
   ```
4. Sube al remoto (la primera vez puede requerir `-u origin <rama>`):
   ```bash
   git push -u origin HEAD
   ```

## Paso 4 — Reportar

Resume al usuario:
- Qué archivos de memoria se actualizaron y con qué.
- El hash y mensaje del commit.
- Resultado del push (éxito o, si falló, el motivo — p. ej. falta de credenciales/autenticación a GitHub).

> Si el push falla por autenticación, NO inventes credenciales: informa al usuario que debe autenticarse (p. ej. `gh auth login` o configurar un token) y deja el commit hecho localmente.
