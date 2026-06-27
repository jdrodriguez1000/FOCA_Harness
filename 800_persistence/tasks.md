# ✅ TASKS — Tareas realizadas y próximas

> Registro de tareas del proyecto. Cada tarea tiene un **estado**. Todo agente actualiza este archivo al avanzar.

**Leyenda de estados:** `✅ Completada` · `🏗️ En progreso` · `🔜 Pendiente` · `⛔ Bloqueada` · `❌ Descartada`

---

## 🧭 Índice

- [Convenciones](#convenciones)
- [Tareas en progreso](#tareas-en-progreso)
- [Próximas tareas](#próximas-tareas)
- [Tareas completadas](#tareas-completadas)
- [Backlog / ideas futuras](#backlog--ideas-futuras)

---

## Convenciones

- **ID:** `T-###` (incremental, no reusar).
- Cada tarea indica, si aplica, la **iteración** (`tracer_bullet`, `stab_1`, …) y la **fase del harness** (spec/plan/exec/verif/valid).

---

## Tareas en progreso

| ID | Tarea | Iteración | Fase harness | Estado |
|---|---|---|---|---|
| — | (ninguna por ahora) | | | |

---

## Próximas tareas

| ID | Tarea | Iteración | Fase harness | Estado |
|---|---|---|---|---|
| T-022 | Construir los agentes del **bloque Especificación** en `.claude/agents/` (`bdd-writer`, `reviewer`, `spec-writer`) según `arquitectura_harness.md` | — | — | 🔜 Pendiente |
| T-023 | Construir el agente `plan-writer` (bloque Planning) | — | — | 🔜 Pendiente |
| T-024 | Construir los agentes del bloque Ejecución (`tester`, `dev-backend`, `dev-optimizer`) | — | — | 🔜 Pendiente |
| T-025 | Construir `verificator` y `evaluator` (+ rúbrica calibrada con few-shot, E3) | — | — | 🔜 Pendiente |
| T-003 | Redactar `spec.md` del tracer_bullet (objetivo, alcance, criterios de aceptación, fuera de alcance) | tracer_bullet | 1. Specification | 🔜 Pendiente |
| T-004 | Redactar `plan.md` del tracer_bullet (tareas, riesgos) | tracer_bullet | 2. Planning | 🔜 Pendiente |
| T-005 | Crear esqueleto del proyecto + `core/` | tracer_bullet | 3. Execution | 🔜 Pendiente |
| T-006 | Generador de datos sintéticos jerárquicos (con censura/quiebres inyectados) | tracer_bullet | 3. Execution | 🔜 Pendiente |
| T-007 | Implementar el hilo delgado de las 11 etapas (mínimo, modelo naïve) | tracer_bullet | 3. Execution | 🔜 Pendiente |
| T-008 | Producir artefacto de salida del paso 10 (resultado al cliente) | tracer_bullet | 3. Execution | 🔜 Pendiente |
| T-009 | Tests end-to-end del tracer_bullet | tracer_bullet | 4. Verification | 🔜 Pendiente |
| T-010 | Checklist de conformidad contra la spec | tracer_bullet | 5. Validation | 🔜 Pendiente |

---

## Tareas completadas

| ID | Tarea | Iteración | Fase harness | Estado | Fecha |
|---|---|---|---|---|---|
| T-001 | Crear `800_persistence/` (memoria del proyecto) con sus 4 archivos indexados | — | — | ✅ Completada | 2026-06-26 |
| T-011 | Crear `970_documents/idea.md` (visión general del proyecto) | — | — | ✅ Completada | 2026-06-26 |
| T-012 | Crear `CLAUDE.md` raíz con el protocolo de memoria | — | — | ✅ Completada | 2026-06-26 |
| T-013 | Crear estructura `.claude/` (settings.json + settings.local.json) | — | — | ✅ Completada | 2026-06-26 |
| T-014 | Crear comando de proyecto `/foca-progress` (cierre de sesión: memoria + commit + push) | — | — | ✅ Completada | 2026-06-26 |
| T-015 | Inicializar git + remoto `origin` + `.gitignore` + commit/push inicial | — | — | ✅ Completada | 2026-06-26 |
| T-016 | Crear comando de proyecto `/foca-next` (inicio de sesión: cargar contexto) | — | — | ✅ Completada | 2026-06-26 |
| T-017 | Definir alcance por iteración en términos de usuario y crear `970_documents/roadmap_iteraciones.md` | — | — | ✅ Completada | 2026-06-26 |
| T-002 | Confirmar stack técnico (Python 3.12 + `uv` + pandas) | — | — | ✅ Completada | 2026-06-26 |
| T-020 | Diseñar arquitectura del producto (stack, patrones, capas, medallón, layout) y crear `970_documents/arquitectura.md` | — | — | ✅ Completada | 2026-06-26 |
| T-021 | Crear plantilla de alcance `980_templates/scope_template.md` (con secciones de pendientes heredados/diferidos) | — | — | ✅ Completada | 2026-06-26 |
| T-019 | Crear `900_scope/scope_tracer_bullet.md` (insumo de alcance, aprobado por humano) | tracer_bullet | 1. Specification | ✅ Completada | 2026-06-26 |
| T-018 | Diseñar la arquitectura de agentes del harness (topología, roster, flujo, persistencia, rúbrica) → `970_documents/arquitectura_harness.md` + D-026 a D-029 | — | — | ✅ Completada | 2026-06-26 |

---

## Backlog / ideas futuras

- Reconciliación de pronósticos jerárquicos (MinT / bottom-up / top-down). → desde `stab_*`.
- Des-censura real de demanda (reconstrucción de periodos con quiebre). → desde `stab_*`.
- Decisión newsvendor con costos asimétricos reales. → MVP.
- Modelos serios de forecasting (statsforecast / hierarchicalforecast de Nixtla). → `stab_*` / MVP.
- Multi-tenancy real con aislamiento de datos. → MVP.
- Workbench interno para los científicos de datos. → evol_*.
- Monitoreo / drift / reentrenamiento (etapa 11). → evol_*.
- Paso a producción / despliegue. → FUERA de alcance actual.
