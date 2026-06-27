---
description: Continúa la ejecución del harness leyendo el ESTADO RUNTIME (harness-state.json, execution-state.json, project-progress.txt). Diagnostica en qué paso del orchestration_plan va la iteración y conduce el siguiente paso (spawnear el agente que toca, presentar un gate humano, o re-spawnear por un rechazo). Repetible: cada corrida avanza un paso. Distinto de /foca-next (que lee la memoria curada para reorientar al humano).
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write, Edit, Agent, Bash(ls:*), Bash(test:*), Bash(tail:*), Bash(cat:*)
---

# /foca-continue — Continuar la ejecución del harness desde el estado runtime

Eres la **sesión principal**, que **ES el Governor** (D-026): el único que spawnea subagentes. Este comando **retoma una iteración en curso** leyendo su **estado de máquina** y ejecuta **el siguiente paso** del flujo.

> **Diferencia con `/foca-next`:** `/foca-next` lee la memoria **curada** (`progress.md`, `tasks.md`) para que un humano se reoriente. `/foca-continue` lee el estado **runtime** (`*-state.json`, `project-progress.txt`) para **mover la máquina**: decide y ejecuta el siguiente agente o gate. Úsalo cuando ya hay una iteración arrancada (con `/foca-init`) y quieres avanzarla.

Ejecuta los pasos en orden. Cada invocación avanza **un** paso (o presenta **un** gate); el humano vuelve a llamar al comando para el siguiente.

---

## Paso 0 — Guard: ¿hay algo que continuar?

```bash
test -f 800_persistence/harness-state.json && echo "STATE_OK"
```

**Si NO existe `harness-state.json` → DETENTE.** El proyecto aún no ha arrancado:

> ⛔ No hay estado runtime que continuar. Si es el inicio del proyecto, corre **`/foca-init`** primero. Si solo quieres reorientarte, usa **`/foca-next`**.

**Si existe →** continúa.

---

## Paso 1 — Leer el estado runtime

1. **`800_persistence/harness-state.json`** → `current_iteration`, `current_phase`, `sprint_contract`, `approvals`, `change_requests`, `status`.
2. **Localiza la carpeta de la iteración** (su nombre termina en `current_iteration`):
   ```bash
   ls -d 990_iterations/*"$(echo current_iteration)"* 2>/dev/null
   ```
   Léela como `990_iterations/<nn>_<current_iteration>/`.
3. **`990_iterations/<nn>_<current_iteration>/execution-state.json`** → `orchestration_plan`, `agents` (estado de cada uno), `checkpoints`, `early_eval`.
4. **`800_persistence/project-progress.txt`** → últimas entradas, para el contexto narrativo:
   ```bash
   tail -n 15 800_persistence/project-progress.txt
   ```

> Estos archivos son la **fuente de verdad** del avance. Si se contradicen entre sí, prioriza `execution-state.json` para el detalle técnico y `harness-state.json` para fase/aprobaciones; reporta la inconsistencia al humano.

---

## Paso 2 — Diagnóstico: ¿en qué punto va?

Construye un cuadro de situación:
- **Iteración y fase** actuales.
- **Pasos del `orchestration_plan`**: cuáles están hechos y cuál es el **primer paso pendiente**. Cruza con los `status` de `agents` y con la **existencia real de los artefactos** en disco (verifica el path; no confíes solo en el JSON).
- **Gates humanos**: ¿hay un gate pendiente cuya aprobación **no** figura en `harness-state.approvals`?
- **Rechazos**: ¿algún `review_*.json` tiene `verdict: "changes_requested"` sin que su agente escritor se haya vuelto a correr?
- **Change requests** abiertos en `harness-state.change_requests`.

Reporta este diagnóstico brevemente antes de actuar.

---

## Paso 3 — Decidir la próxima acción (árbol de decisión)

Aplica la **primera** regla que coincida:

1. **¿Hay un `review_*.json` con `changes_requested` pendiente?**
   → **Re-spawnea al agente escritor** responsable (`bdd-writer` / `spec-writer` / `plan-writer`) pasándole el path del documento y el path del informe de rechazo, para que **corrija solo lo señalado** (methodology §12.4). Luego se re-revisa.

2. **¿El siguiente paso pendiente es un agente (writer o reviewer)?**
   → **Spawnéalo** (Paso 4). Para un `reviewer`, indícale el documento a revisar y su skill-checklist (`review-behavior`/`review-spec`/`review-plan`).

3. **¿El siguiente paso pendiente es un gate humano y no está aprobado?**
   → **NO lo apruebes tú.** Presenta al humano un resumen del artefacto (path + hallazgos del `reviewer`) y **pídele explícitamente la aprobación**. Detente hasta su respuesta. Cuando apruebe, registra `{gate, by:"human", at:<fecha>}` en `harness-state.approvals` y deja listo el siguiente paso para la próxima corrida.

4. **¿Se agotó el `orchestration_plan` de la fase actual?**
   → **Avanza de fase**: actualiza `current_phase` en `harness-state.json`, **extiende** `orchestration_plan` con los pasos de la nueva fase y deja el primer agente listo.
   - Si el agente que toca **aún no existe** (p. ej. `plan-writer`, `tester`, `verificator`, `evaluator` no construidos todavía) → **DETENTE** y repórtalo como bloqueo: hay que construir ese agente antes de continuar (orden de construcción E4, `arquitectura_harness.md §8`).

5. **¿La iteración llegó al final (Fase 5, `evaluator` con veredicto)?**
   → Lee `5_validation/eval/verdict.json`. Reporta "Avanzar o Repetir" al humano. Si avanza, el arranque de la siguiente iteración es otra historia (su propio `scope_<iter>.md` aprobado + nueva entrada en `execution-state`).

---

## Paso 4 — Ejecutar (spawnear el agente que toca)

Como Governor, invoca el subagente con la herramienta `Agent`, pasándole **solo paths** (E6) e instrucciones precisas según su contrato:

| Agente | Insumo(s) a pasarle | Debe escribir |
|---|---|---|
| `bdd-writer` | `900_scope/scope_<iter>.md` | `1_specification/behavior.md` |
| `reviewer` (review-behavior) | `1_specification/behavior.md` + su scope | `1_specification/review_behavior.json` |
| `spec-writer` | `1_specification/behavior.md` (aprobado) + `970_documents/arquitectura.md` | `1_specification/spec.md` |
| `reviewer` (review-spec) | `1_specification/spec.md` + `behavior.md` + `arquitectura.md` | `1_specification/review_spec.json` |
| `plan-writer`* | `1_specification/spec.md` (aprobado) | `2_planning/plan.md` |
| `reviewer` (review-plan)* | `2_planning/plan.md` + `spec.md` | `2_planning/review_plan.json` |

\* Cuando esos agentes/skills existan (hoy puede no ser el caso → regla 4 del árbol).

Antes de spawnear: marca el agente como `in_progress` en `execution-state.json`. Cuando devuelva: registra su `artifact_path` y `status: "done"`.

---

## Paso 5 — Actualizar el estado runtime (single-writer)

Respeta los dueños (`arquitectura_harness.md §5`) y la **regla de referencias ligeras (E6)** — paths/IDs, nunca el contenido:
- **`execution-state.json`** (Governor-B): estado de los `agents`, `artifact_path`, `checkpoints`.
- **`harness-state.json`** (Governor-A): `current_phase`, `approvals`, `change_requests`, `status`.
- **`project-progress.txt`** (instancia activa): **anexa** una entrada con timestamp describiendo el paso ejecutado y el resultado. **No reescribas** lo anterior.

---

## Paso 6 — Reportar al humano

Resume en pocas líneas:
- **Dónde iba** (iteración · fase · paso del plan).
- **Qué hice** en esta corrida (agente spawneado y su artefacto, o gate presentado, o re-spawn por rechazo).
- **Qué sigue** y qué espera de ti (¿aprobar un gate? ¿volver a correr `/foca-continue`? ¿construir un agente faltante?).

> Mantén al humano en cada **gate** (P5): nunca apruebas en su nombre. Tu trabajo es dejar la máquina exactamente un paso más adelante y con el estado runtime fiel a lo ocurrido.
