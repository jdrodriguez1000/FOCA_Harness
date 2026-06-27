# 🏗️ ARQUITECTURA DEL HARNESS — FOCA_Harness

> **Cómo está construido el harness** (la máquina de gobernanza + ejecución que hace pasar cada iteración por sus 5 fases). Se rige por `950_guideline/principles.md` (P1–P8, E1–E12) y `950_guideline/methodology.md`, adaptados a las restricciones reales de Claude Code. Complementa `970_documents/arquitectura.md` (que describe el **producto**), no lo reemplaza: este documento describe el **proceso que construye el producto**.

---

## 🧭 Índice

- [1. Restricción de plataforma y topología resultante](#1-restricción-de-plataforma-y-topología-resultante)
- [2. Roster de agentes reusables](#2-roster-de-agentes-reusables)
- [3. Flujo por iteración (las 5 fases)](#3-flujo-por-iteración-las-5-fases)
- [4. Gates: reviewer automático + aprobación humana](#4-gates-reviewer-automático--aprobación-humana)
- [5. Persistencia y estado](#5-persistencia-y-estado)
- [6. Rúbrica del evaluador](#6-rúbrica-del-evaluador)
- [7. Ubicación de artefactos por iteración](#7-ubicación-de-artefactos-por-iteración)
- [8. Orden de construcción (mínima complejidad E4)](#8-orden-de-construcción-mínima-complejidad-e4)

---

## 1. Restricción de plataforma y topología resultante

**Restricción dura de Claude Code:** solo la **sesión principal** puede invocar subagentes (tool `Agent`). Un subagente es una *hoja*: no puede spawnear a su vez. Esto **rompe** la cadena de dos niveles de `methodology.md` (`A ▶ B ▶ Workers`), porque B —un subagente— no podría spawnear Workers.

**Reconciliación (D-026):** se **fusionan A (Governor) y B (Orchestrator) en la sesión principal**, que es el único spawner. Se dejan como **subagentes de contexto fresco** exactamente las piezas que necesitan aislamiento e independencia: los **agentes de trabajo** y los **revisores/evaluadores**.

```
SESIÓN PRINCIPAL  =  Governor (A) + Orchestrator (B)   ← único que spawnea
   │  • determina en qué iteración estamos (lee harness-state.json + iterations/)
   │  • persiste orchestration_plan (E12) antes de spawnear
   │  • escribe harness-state.json y execution-state.json
   │  • MEDIA todas las aprobaciones humanas (un subagente no puede preguntarte)
   │
   └─▶ spawnea, en secuencia, subagentes hoja (contexto fresco, escriben a FS):
        bdd-writer · spec-writer · plan-writer · reviewer ·
        tester · dev-backend · dev-optimizer · verificator · evaluator
```

**Qué se preserva de los principios pese a la fusión:**
- **P3 (evaluador independiente):** intacto. `reviewer` y `evaluator` corren con contexto limpio y solo **leen** artefactos; nunca evalúan lo que ellos escribieron.
- **P4 (context resets) / P2 (handoff por archivos) / E6 (outputs al filesystem):** cada subagente es fresco, escribe su artefacto a disco y reporta al Governor **solo el path**, nunca el contenido.
- **Concesión documentada:** A y B comparten contexto (la sesión principal lleva el sombrero estratégico y el táctico). Mitigación: la **fuente de verdad es el filesystem**; se hace *context reset* entre fases grandes (E2). Coherente con E4 (menos piezas móviles).

> **Interacción humana:** únicamente el Governor habla con el humano. Los subagentes (incl. `reviewer`) devuelven su veredicto al Governor; el Governor lo presenta y solicita la aprobación.

## 2. Roster de agentes reusables

Los agentes son **roles estables por especialidad (P1)**, definidos **una sola vez** en `.claude/agents/` y reutilizados en **todas** las iteraciones (D-027). Lo que cambia entre iteraciones no es el agente, sino sus **insumos** (`scope_<iter>.md`, `behavior.md`, `spec.md`, `plan.md`).

| Agente | Rol | Lee | Escribe | Herramientas (P7) | Modelo (§9) |
|---|---|---|---|---|---|
| `bdd-writer` | escenarios de comportamiento (BDD) | `scope_<iter>.md` | `behavior.md` | Read, Write, Glob, Grep | Opus |
| `spec-writer` | especificación técnica | `behavior.md`, `arquitectura.md` | `spec.md` | Read, Write, Glob, Grep | Opus |
| `plan-writer` | plan de ejecución de la iteración | `spec.md` | `plan.md` | Read, Write, Glob, Grep | Opus |
| `reviewer` | gate de calidad de documentos (completitud, sin vacíos, sin ambigüedad, conformidad con arquitectura) | el doc + sus insumos | `review_<doc>.json` | Read, Glob, Grep | Opus |
| `tester` | escribe pruebas (RED del TDD) | `spec.md`, `plan.md` | `tests/…` | Read, Write, Edit, Bash | Sonnet |
| `dev-backend` | código backend (GREEN) | `plan.md`, tests | código en `1000_Project/` | Read, Write, Edit, Bash | Sonnet |
| `dev-optimizer` | refactor (REFACTOR) sin cambiar comportamiento | código + tests | código refactorizado | Read, Write, Edit, Bash | Sonnet |
| `verificator` | verificación independiente: re-ejecuta unit + integración + e2e | tests + código | `verification_report.json` | Read, Bash, Glob, Grep | Sonnet |
| `evaluator` | validación: conformidad con el scope (ni de más ni de menos) + métricas del harness | scope/behavior/spec + artefactos | `/eval/verdict.json`, `/eval/metrics_summary.json` | Read, Glob, Grep, Bash | Opus |

> El **Executor es una familia**: `dev-backend` cubre tracer_bullet → MVP (todo el pipeline Python); **`dev-frontend`** se suma en `evol_1` (workbench). El `reviewer` es **un único agente reusable** invocado en cada gate de documento (behavior, spec, plan) con contexto fresco cada vez.

## 3. Flujo por iteración (las 5 fases)

El patrón A/B/C **envuelve toda la iteración** (un solo veredicto de `evaluator` al final), no por fase — opción simple, alineada a E4. Dentro, las 5 fases del harness ([D-007]) se realizan como pasos del ciclo SDD+TDD.

```
GOVERNOR arranca → determina iteración → lee scope_<iter>.md (ya aprobado, D-012)

┌─ FASE 1 · Specification ────────────────────────────────────────────────┐
│  ▶ bdd-writer   : scope.md            → behavior.md                       │
│  ▶ reviewer     : behavior.md         → review_behavior.json             │
│  ⮑ 👤 Gate humano: aprobar behavior.md                                   │
│  ▶ spec-writer  : behavior + arquitectura → spec.md                      │
│  ▶ reviewer     : spec.md             → review_spec.json                 │
│  ⮑ 👤 Gate humano: aprobar spec.md                                       │
└──────────────────────────────────────────────────────────────────────────┘
┌─ FASE 2 · Planning ──────────────────────────────────────────────────────┐
│  ▶ plan-writer  : spec.md             → plan.md                          │
│  ▶ reviewer     : plan.md             → review_plan.json                 │
│  ⮑ 👤 Gate humano: aprobar plan.md                                       │
└──────────────────────────────────────────────────────────────────────────┘
┌─ FASE 3 · Execution  (ciclo TDD) ────────────────────────────────────────┐
│  ▶ tester        : escribe pruebas (unit/integración/e2e)      [RED]      │
│  ▶ dev-backend   : código mínimo que las hace pasar            [GREEN]    │
│  ▶ dev-optimizer : refactor sin cambiar comportamiento         [REFACTOR] │
└──────────────────────────────────────────────────────────────────────────┘
┌─ FASE 4 · Verification ──────────────────────────────────────────────────┐
│  ▶ verificator  : re-ejecuta unit + integración + e2e → verification_report│
└──────────────────────────────────────────────────────────────────────────┘
┌─ FASE 5 · Validation ────────────────────────────────────────────────────┐
│  ▶ evaluator    : conformidad con scope (sin creep/gaps) + métricas       │
│                   → /eval/verdict.json + /eval/metrics_summary.json       │
│  ⮑ Governor lee verdict → "Avanzar o Repetir" → informa al humano         │
└──────────────────────────────────────────────────────────────────────────┘
```

**Mapeo 5 fases ↔ SDD+TDD (methodology §7):** Specification = SPEC (behavior + spec) + HUMAN REVIEW · Planning = plan + gate · Execution = RED+GREEN+REFACTOR · Verification = re-run independiente de tests · Validation = EVAL (Instancia C).

## 4. Gates: reviewer automático + aprobación humana

Patrón en cada documento de Fase 1–2: **`reviewer` (filtro automático) → 👤 humano**. El reviewer evita presentarte borradores incompletos; el humano aprueba la intención y el alcance (P5).

- **Gates humanos por iteración:** 3 (behavior, spec, plan), **más** el de `scope.md` ya aprobado fuera del ciclo (D-012) → 4 puntos de control humano antes de codear.
- **Verificación (Fase 4) y Validación (Fase 5)** son gates **automáticos**: `verificator` (los tests pasan) y `evaluator` (rúbrica ≥ umbral, conformidad con scope). El Governor reporta el resultado; el avance a la siguiente iteración lo confirma el humano.
- **Rechazo:** si `reviewer`/`verificator`/`evaluator` rechazan, el Governor re-spawnea al agente responsable pasándole la referencia al informe de rechazo, que re-ejecuta solo lo fallido (methodology §12.4).

## 5. Persistencia y estado

Dos planos de memoria, complementarios:

**(a) Memoria curada (humana) — `800_persistence/*.md`** (D-008): `progress.md`, `tasks.md`, `decisions.md`, `lessons.md`. Cadencia: se actualiza al **cierre de sesión** (vía `/foca-progress`). Es la narrativa que un humano lee para reorientarse.

**(b) Estado runtime del harness (máquina)** — escrito por los agentes **durante** una corrida (D-029):

| Archivo | Dueño (Single Writer) | Ubicación | Propósito |
|---|---|---|---|
| `harness-state.json` | **Governor (A)** | `800_persistence/harness-state.json` | Estado estratégico global: iteración actual, fase, sprint contract, aprobaciones, CRs. |
| `execution-state.json` | **Governor como Orchestrator (B)** | `iterations/<nn>_<nombre>/execution-state.json` | Estado técnico **por iteración**: `orchestration_plan` (E12), estado de cada agente, checkpoints (E5), `early_eval` (E9). |
| `project-progress.txt` | **instancia activa** (la que ejecuta) | `800_persistence/project-progress.txt` | Bitácora narrativa runtime, append-only, con timestamps, checkpoints y bloqueos. |

> `project-progress.txt` (granular, append-only por los agentes) **no sustituye** a `progress.md` (resumen curado al cierre): distinta cadencia y audiencia. **Regla de referencias ligeras (E6):** los agentes registran en estos archivos **paths/IDs**, nunca el contenido de los artefactos.

**Esquema mínimo `harness-state.json`:** `{ current_iteration, current_phase, sprint_contract: {definition_of_done, acceptance_criteria}, approvals: [{gate, by, at}], change_requests: [], status }`.
**Esquema mínimo `execution-state.json`:** `{ iteration, orchestration_plan: [...], agents: [{name, status, artifact_path}], checkpoints: [...], early_eval: {} }`.

> Estos archivos **se crean cuando arranca la primera corrida** del harness sobre una iteración (ritual de inicio E10-A), no ahora — este documento solo define su contrato.

## 6. Rúbrica del evaluador

Para evitar lenidad sistémica (E3 / methodology §3), el `evaluator` (Instancia C) usa una rúbrica **0.0–1.0** con dimensiones, anclas y ejemplos few-shot. Esqueleto base (cada iteración puede añadir dimensiones de dominio):

| Dimensión | Qué mide | Peso |
|---|---|---|
| **Conformidad con el alcance** | Hace exactamente lo del `scope.md`/`spec.md`: sin scope creep ni gaps (Fase 5, D-007) | 0.30 |
| **Completitud** | Todos los criterios de aceptación cubiertos y verificables | 0.25 |
| **Corrección verificada** | Tests (unit/integración/e2e) pasan de forma reproducible | 0.25 |
| **Calidad de artefactos/trazabilidad** | Artefactos consistentes entre sí (behavior↔spec↔plan↔código) y al filesystem | 0.10 |
| **Eficiencia de herramientas** | Uso razonable de pasos/tokens vs. complejidad (P6) | 0.10 |

**Anclas:** `1.0` = criterio cumplido sin observaciones · `0.5` = parcial, corrección menor · `0.0` = ausente/incumplido → causa de rechazo directo. **Few-shot:** ≥2 ejemplos calibrados (uno aceptable ≥0.7, uno rechazado <0.5) — se redactan al construir el `evaluator` (pendiente, Fase 5).

## 7. Ubicación de artefactos por iteración

Coherente con la estructura de `CLAUDE.md` (subcarpeta por fase del harness):

```
iterations/<nn>_<nombre>/
├── execution-state.json          # estado técnico de la iteración (Governor-as-B)
├── 1_specification/
│   ├── behavior.md               # bdd-writer
│   ├── spec.md                   # spec-writer
│   ├── review_behavior.json      # reviewer
│   └── review_spec.json          # reviewer
├── 2_planning/
│   ├── plan.md                   # plan-writer
│   └── review_plan.json          # reviewer
├── 3_execution/                  # rastro de ejecución (código vive en 1000_Project/)
├── 4_verification/
│   └── verification_report.json  # verificator
└── 5_validation/
    └── eval/
        ├── verdict.json          # evaluator
        └── metrics_summary.json  # evaluator
```

> El `scope_<iter>.md` vive aparte en `900_scope/` (insumo pre-iteración, aprobado por humano antes de entrar a Fase 1). El **código del producto** vive en `1000_Project/` (D-023), no dentro de `iterations/`.

## 8. Orden de construcción (mínima complejidad E4)

No se crean los 9 agentes de golpe. Cada agente codifica una suposición sobre una limitación del modelo y se construye cuando su fase lo requiere:

1. **Bloque Especificación** (lo que hace avanzar el `tracer_bullet` ahora): `bdd-writer`, `reviewer`, `spec-writer` → produce `behavior.md` + `spec.md`.
2. **Bloque Planning:** `plan-writer`.
3. **Bloque Ejecución:** `tester`, `dev-backend`, `dev-optimizer`.
4. **Bloque Verificación/Validación:** `verificator`, `evaluator` (con su rúbrica calibrada).

Cada bloque se valida con evaluación temprana (E9, ~muestra representativa) antes de construir el siguiente. `dev-frontend` no existe hasta `evol_1`.
