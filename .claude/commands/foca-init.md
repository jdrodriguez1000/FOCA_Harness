---
description: Bootstrap de un solo uso que ARRANCA la construcción del proyecto. Valida el scope aprobado, crea la estructura de carpetas de 1000_Project/ y de la iteración, inicializa los archivos de estado runtime del harness (ritual E10-A) y lanza la Fase 1 invocando al bdd-writer. Si el proyecto ya está iniciado, NO corre y redirige.
disable-model-invocation: true
allowed-tools: Read, Write, Glob, Grep, Agent, Bash(mkdir:*), Bash(ls:*), Bash(test:*), Bash(touch:*), Bash(printf:*)
---

# /foca-init — Inicio de la construcción del proyecto (un solo uso)

Eres la **sesión principal**, que en este harness **ES el Governor** (Governor A + Orchestrator B fusionados, D-026): el único que puede invocar subagentes. Este comando ejecuta el **ritual de inicio (E10-A)**: prepara el terreno y lanza la **Fase 1 (Specification)** del `tracer_bullet`.

> **Es de un solo uso.** Si el proyecto ya arrancó, NO debes ejecutarlo: detente en el Paso 0 y redirige.

Ejecuta los pasos **en orden**. Si un paso falla o no se cumple una precondición, **detente** y reporta; no continúes.

---

## Paso 0 — Guard de idempotencia (¿ya está iniciado?)

Comprueba si el proyecto ya arrancó. Señales (cualquiera ⇒ ya iniciado):
- Existe `990_iterations/00_tracer_bullet/`.
- Existe `800_persistence/harness-state.json`.
- Existe `1000_Project/foca/`.

```bash
test -d 990_iterations/00_tracer_bullet && echo "ITER_EXISTS"
test -f 800_persistence/harness-state.json && echo "STATE_EXISTS"
test -d 1000_Project/foca && echo "PKG_EXISTS"
```

**Si alguna señal aparece → DETENTE.** Responde algo como:

> ⛔ `/foca-init` no es útil aquí: el proyecto **ya está iniciado**. Este comando es solo para el arranque (una vez). Para retomar el trabajo usa **`/foca-next`** (te dice la fase actual y el siguiente paso), o invoca directamente al agente de la fase en curso (p. ej. `spec-writer` si ya hay `behavior.md` aprobado, `plan-writer` en Fase 2, etc.).

No crees ni modifiques nada en este caso.

**Si ninguna señal aparece →** continúa al Paso 1.

---

## Paso 1 — Validar que el scope esté aprobado

Lee `900_scope/scope_tracer_bullet.md` y verifica en la **§12 Aprobación** que el alcance esté **aprobado por el humano** (la casilla `[x] Revisado y aprobado…`).

- **Si NO está aprobado → DETENTE** y reporta: el alcance debe aprobarse antes de entrar a la Fase 1 (P5/D-012). No se construye sobre un scope en borrador.
- **Si está aprobado →** continúa. Extrae para más adelante: la **capacidad** (§1) y los **criterios de aceptación** (§9), que alimentarán el `sprint_contract`.

---

## Paso 2 — Crear la estructura de `1000_Project/` (solo carpetas)

Crea el **árbol de directorios** del producto según `970_documents/arquitectura.md §6`. **Solo carpetas + `.gitkeep`** — sin archivos `.py` ni código: el esqueleto de código es trabajo de la **Fase 3 (Execution, T-005)**, no de este comando.

```bash
mkdir -p 1000_Project/foca/config \
         1000_Project/foca/domain \
         1000_Project/foca/data \
         1000_Project/foca/pipeline/stages \
         1000_Project/foca/models \
         1000_Project/foca/decision \
         1000_Project/foca/reporting/templates \
         1000_Project/foca/io \
         1000_Project/tests/unit \
         1000_Project/tests/e2e \
         1000_Project/data
# Marcar las carpetas vacías para que git las versione (data/ queda como workspace gitignored)
for d in foca/config foca/domain foca/data foca/pipeline foca/pipeline/stages foca/models foca/decision foca/reporting foca/reporting/templates foca/io tests/unit tests/e2e; do
  touch "1000_Project/$d/.gitkeep"
done
```

> No crees `pyproject.toml`, `cli.py`, ni módulos `.py`: eso entra en Execution. Aquí solo dejas las **costuras** del layout listas.

---

## Paso 3 — Crear la estructura de la iteración

Crea la carpeta de la primera iteración con sus 5 subcarpetas de fase (convención de `CLAUDE.md` y `arquitectura_harness.md §7`):

```bash
mkdir -p 990_iterations/00_tracer_bullet/1_specification \
         990_iterations/00_tracer_bullet/2_planning \
         990_iterations/00_tracer_bullet/3_execution \
         990_iterations/00_tracer_bullet/4_verification \
         990_iterations/00_tracer_bullet/5_validation/eval
```

---

## Paso 4 — Inicializar los archivos de estado runtime (Governor, ritual E10-A)

Como Governor, **crea e inicializa** los tres archivos de estado runtime definidos en `arquitectura_harness.md §5`. Usa la herramienta `Write`. Convierte fechas relativas a la fecha de hoy.

**(a) `800_persistence/harness-state.json`** (dueño: Governor A). Rellena `sprint_contract` con los criterios de aceptación del scope §9:
```json
{
  "current_iteration": "tracer_bullet",
  "current_phase": "1_specification",
  "sprint_contract": {
    "definition_of_done": "<capacidad del scope §1, en una frase>",
    "acceptance_criteria": ["<criterio §9 #1>", "<criterio §9 #2>", "..."]
  },
  "approvals": [
    {"gate": "scope", "by": "human", "at": "<fecha de aprobación del scope §12>"}
  ],
  "change_requests": [],
  "status": "in_progress"
}
```

**(b) `990_iterations/00_tracer_bullet/execution-state.json`** (dueño: Governor B). El `orchestration_plan` es el flujo de la Fase 1 (E12, persistido antes de spawnear):
```json
{
  "iteration": "tracer_bullet",
  "orchestration_plan": [
    {"step": 1, "agent": "bdd-writer", "produces": "1_specification/behavior.md"},
    {"step": 2, "agent": "reviewer", "skill": "review-behavior", "produces": "1_specification/review_behavior.json"},
    {"step": 3, "gate": "human", "approves": "behavior.md"},
    {"step": 4, "agent": "spec-writer", "produces": "1_specification/spec.md"},
    {"step": 5, "agent": "reviewer", "skill": "review-spec", "produces": "1_specification/review_spec.json"},
    {"step": 6, "gate": "human", "approves": "spec.md"}
  ],
  "agents": [
    {"name": "bdd-writer", "status": "pending", "artifact_path": null},
    {"name": "reviewer", "status": "pending", "artifact_path": null},
    {"name": "spec-writer", "status": "pending", "artifact_path": null}
  ],
  "checkpoints": [],
  "early_eval": {}
}
```

**(c) `800_persistence/project-progress.txt`** (dueño: instancia activa; append-only). Inícialo con una cabecera y la primera entrada con timestamp:
```
# PROJECT-PROGRESS (runtime, append-only) — bitácora granular del harness
# No sustituye a progress.md (resumen curado al cierre). Solo paths/IDs, nunca contenido (E6).

[<YYYY-MM-DD HH:MM>] /foca-init — Bootstrap E10-A. Estructura 1000_Project/ y 990_iterations/00_tracer_bullet/ creada. Estado runtime inicializado. Scope aprobado verificado. Entrando a Fase 1 (Specification).
```

> A medida que avances, **anexa** (no reescribas) entradas a `project-progress.txt` y actualiza el `status` de cada agente en `execution-state.json`.

---

## Paso 5 — Lanzar la Fase 1: invocar al `bdd-writer`

Como Governor (único spawner), invoca al subagente **`bdd-writer`** con la herramienta `Agent`. Pásale instrucciones explícitas y **solo paths** (E6):

- **Insumo:** `900_scope/scope_tracer_bullet.md` (alcance aprobado).
- **Destino:** debe escribir `990_iterations/00_tracer_bullet/1_specification/behavior.md`.
- **Tarea:** derivar los escenarios de comportamiento (Gherkin, términos de usuario) del scope, con trazabilidad a sus criterios §9, según su propio contrato.

Antes de spawnear, marca en `execution-state.json` el `bdd-writer` como `in_progress`. Cuando devuelva, registra el `artifact_path` y su estado `done`, y anexa una entrada a `project-progress.txt`.

> El `bdd-writer` devuelve **solo el path** del `behavior.md` y las ambigüedades detectadas. **No** continúes automáticamente con el `reviewer` ni los gates: este comando solo **arranca** la Fase 1. El siguiente paso (revisión + gate humano) lo conduces ya en el flujo normal del Governor.

---

## Paso 6 — Reportar al humano

Resume:
- ✅ Estructura creada (`1000_Project/` solo carpetas, `990_iterations/00_tracer_bullet/`).
- ✅ Estado runtime inicializado (los 3 archivos, con sus paths).
- ✅ `behavior.md` generado por el `bdd-writer` (su path) + ambigüedades/supuestos que reportó.
- 🔜 **Siguiente paso:** correr el `reviewer` (skill `review-behavior`) sobre el `behavior.md` y luego presentarte el **gate humano** para aprobarlo. Pregúntale al humano si quiere continuar con la revisión ahora.
