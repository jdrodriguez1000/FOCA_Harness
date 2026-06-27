# 📊 PROGRESS — Estado y avance del proyecto FOCA_Harness

> **Memoria del proyecto.** Todo agente de Claude Code DEBE leer este archivo (y los demás de `800_persistence/`) al iniciar una sesión, y DEBE actualizarlo al terminar.

---

## 🧭 Índice

- [1. Resumen de una línea](#1-resumen-de-una-línea)
- [2. ¿En qué punto estamos?](#2-en-qué-punto-estamos)
- [3. Contexto del proyecto](#3-contexto-del-proyecto)
- [4. Marco de trabajo (iteraciones + harness)](#4-marco-de-trabajo-iteraciones--harness)
- [5. Pipeline de 11 etapas](#5-pipeline-de-11-etapas)
- [6. Hecho hasta el momento](#6-hecho-hasta-el-momento)
- [7. Próximo hito](#7-próximo-hito)
- [8. Bitácora de sesiones](#8-bitácora-de-sesiones)

---

## 1. Resumen de una línea

Plataforma interna **multi-tenant** (modelo *Service as a Software*) operada por los científicos de datos de **Triple S**, para pronosticar la **demanda** (no ventas) de empresas manufactureras ABC y entregarles recomendaciones accionables de producción/stock bajo costos asimétricos.

## 2. ¿En qué punto estamos?

**Fase actual:** Harness en construcción — **bloque de Especificación COMPLETO** (agentes `bdd-writer`, `reviewer`, `spec-writer` + skills `review-behavior`, `review-spec`) y **comandos de operación runtime** (`/foca-init`, `/foca-continue`) listos. La **Iteración 1 — `tracer_bullet`** ya está **arrancada (ritual E10-A vía `/foca-init`)**: estructura de `1000_Project/` (solo carpetas) e `990_iterations/00_tracer_bullet/` creadas, estado runtime inicializado. Entrando a la **Fase 1: Specification**; el `behavior.md` aún **no** se ha generado (el spawn del `bdd-writer` se difirió).

**Aún NO se ha escrito código de producto.** `1000_Project/` solo tiene el árbol de carpetas (los `.py` los escribe la Fase 3 Execution). El alcance del tracer_bullet (`900_scope/scope_tracer_bullet.md`) está **aprobado** por el humano (§12, 2026-06-26).

## 3. Contexto del proyecto

- **Negocio:** Service as a Software (SaaSw). Triple S entrega el **resultado** como servicio, no el software. El software + agentes de IA son el motor interno.
- **Usuarios de la plataforma:** científicos de datos de Triple S (NO los clientes ABC).
- **Objetivo del modelo:** predecir **DEMANDA** (lo que el cliente quería pedir), no **VENTAS** (lo despachado/censurado por quiebres de stock).
- **Costos asimétricos:** sobre-producción (sobre-inventario) vs sub-producción (quiebre de stock) tienen costos distintos → decisión tipo *newsvendor*.
- **Dos jerarquías cruzadas:**
  - Producto: `Familia → Categoría → Subcategoría → Producto`
  - Cliente/Geografía: `Región → País → Ciudad → Sede`
- **Grano atómico:** `Demanda(Producto, Sede, Periodo)`.
- **Multi-tenant:** múltiples empresas ABC con datos aislados.

## 4. Marco de trabajo (iteraciones + harness)

**Eje de iteraciones (alcance):**
`tracer_bullet → stab_1, stab_2… → MVP → evol_1, evol_2… → final`
> Nunca se salta de tracer_bullet a MVP directo; se sube de menos a más vía `stab_*`.

**Eje del harness (las 5 fases que pasa CADA iteración):**
1. **Specification** — qué debe lograr + criterios de aceptación.
2. **Planning** — cómo construirlo: tareas, alcance, riesgos.
3. **Execution** — construir.
4. **Verification** — **testing**: que lo construido funcione correctamente.
5. **Validation** — conformidad con la spec: que sea *exactamente* lo pactado (sin scope creep ni gaps).

## 5. Pipeline de 11 etapas

Fase de construcción (one-time por cliente): 1) Onboarding · 2) Carga de datos · 3) Salud de datos · 4) Limpieza · 5) EDA · 6) Ingeniería de características · 7) Entrenamiento y modelado.
Fase operativa (recurrente): 8) Inferencia · 9) Simulación Montecarlo (escenarios) · 10) Información al cliente · 11) Monitoreo (→ reentrenamiento/cambio de modelo).
> Paso a producción/despliegue: FUERA de alcance por ahora.

## 6. Hecho hasta el momento

- ✅ Encuadre completo del problema, negocio, jerarquías, pipeline, iteraciones y harness.
- ✅ Creada la carpeta `800_persistence/` (memoria del proyecto) con sus 4 archivos.
- ✅ Creado `970_documents/idea.md` (visión general del proyecto).
- ✅ Creado `CLAUDE.md` raíz con el protocolo de memoria.
- ✅ Creada estructura `.claude/` (settings.json, settings.local.json) y comandos de proyecto `/foca-progress` y `/foca-next`.
- ✅ Repositorio git inicializado y publicado en GitHub (`origin/main`).
- ✅ **Alcance por iteración definido en términos de usuario** → `970_documents/roadmap_iteraciones.md` (dos ejes: sustancia del pipeline × forma de los datos).
- ✅ Decisiones de alcance registradas (D-012 a D-018): torneo de campeones (stab_2 1 SKU → stab_3 varios SKU), reconciliación producto (stab_4) antes que geografía (stab_5), grano desde día 1 + generador parametrizable, salida archivos+reporte hasta MVP, frontend en evol_1.
- ✅ Esbozada la arquitectura de agentes del harness (specifier → [human review] → planner → tester → executer → evaluator), alineada al ciclo SDD+TDD de `950_guideline/`.
- ✅ **Stack técnico CONFIRMADO** (D-019): Python 3.12 + `uv` + pandas + Pydantic/Pandera + Nixtla (desde stab_2) + Typer + Jinja2 + pytest + ruff.
- ✅ **Arquitectura del producto definida** (D-020 a D-024) → `970_documents/arquitectura.md`: capas hexagonal-lite, patrones (Pipeline/Strategy/Ports&Adapters/Config-driven/Builder/Repository), orquestador propio mínimo, layout en `1000_Project/`, y **arquitectura de medallones** (bronze/silver/gold) como contratos estables del pipeline.
- ✅ **Plantilla de alcance** (D-025) → `980_templates/scope_template.md`: alcance en términos de usuario, Alcance IN / Fuera de alcance, y trazabilidad de pendientes entre iteraciones (§5 heredados / §6 diferidos).
- ✅ **Bloque de Especificación del harness construido** (T-022): agentes `bdd-writer` (scope→behavior.md), `reviewer` (gate genérico de documentos) y `spec-writer` (behavior+arquitectura→spec.md) en `.claude/agents/`.
- ✅ **Skill-checklists del reviewer** (D-030): `.claude/skills/review-behavior/` y `review-spec/` (rúbricas largas externalizadas como Skills, invocadas on-demand; subagentes SÍ pueden usar skills → L-008). Corrección de roster: `reviewer` usa `Write`+`Skill`.
- ✅ **Comandos de operación runtime** (D-031): `/foca-init` (bootstrap E10-A de un solo uso, T-026) y `/foca-continue` (avanza el harness paso a paso leyendo el estado runtime, T-027).
- ✅ **`tracer_bullet` arrancado (E10-A)**: corrido `/foca-init` — `1000_Project/` (solo carpetas + `.gitkeep`) e `990_iterations/00_tracer_bullet/` (5 subcarpetas de fase) creadas; estado runtime inicializado (`harness-state.json`, `execution-state.json`, `project-progress.txt`). `bdd-writer` queda `pending` (spawn diferido por el humano).

## 7. Próximo hito

Con el bloque de Especificación construido y el `tracer_bullet` arrancado, el siguiente paso es **correr la Fase 1**: invocar `/foca-continue` para que el Governor spawnee al `bdd-writer` (paso 1 del `orchestration_plan` → `behavior.md`), luego el `reviewer` (skill `review-behavior`) y el **gate humano** de aprobación; después `spec-writer` + `review-spec` + gate. Esto sirve además como **prueba temprana (E9)** del bloque recién construido. En paralelo o a continuación, **construir el bloque Planning** (`plan-writer` + skill `review-plan` → T-023) para no bloquearse al cerrar la Fase 1. Bloques posteriores: Ejecución (T-024) y Verificación/Validación (T-025).

## 8. Bitácora de sesiones

| Fecha | Sesión | Qué se hizo |
|---|---|---|
| 2026-06-26 | S1 | Encuadre conceptual del proyecto. Definición de iteraciones + harness. Creación de `800_persistence/`, `970_documents/idea.md` y `CLAUDE.md`. |
| 2026-06-26 | S2 | Tooling: estructura `.claude/` (settings), comandos `/foca-progress` y `/foca-next`. Git inicializado + remoto + `.gitignore` + push inicial a GitHub. |
| 2026-06-26 | S3 | Lectura de `950_guideline/` (metodología + principios). Definición del **alcance por iteración en términos de usuario** → `970_documents/roadmap_iteraciones.md`. Decisiones D-012 a D-018 (torneo de campeones, reconciliación producto→geo, grano desde día 1 + generador parametrizable, reglas de salida, frontend en evol_1). Esbozo de arquitectura de agentes del harness. |
| 2026-06-26 | S4 | **Arquitectura del producto:** stack confirmado (Python+uv+pandas+Pydantic/Pandera+Nixtla+Typer), patrones (Pipeline/Strategy/Ports&Adapters/Config-driven/Builder/Repository), orquestador propio mínimo, layout en `1000_Project/` y **arquitectura de medallones** (bronze/silver/gold). Decisiones D-019 a D-024. Creado `970_documents/arquitectura.md`. Lección L-006. |
| 2026-06-26 | S5 | Creada la **plantilla de alcance** `980_templates/scope_template.md` (alcance en términos de usuario, Alcance IN / Fuera de alcance, trazabilidad de pendientes heredados §5 / diferidos §6). Decisión D-025; tarea T-021. |
| 2026-06-26 | S6 | Creado **`900_scope/scope_tracer_bullet.md`** (T-019, en borrador, pendiente aprobación humana). **Diseñada la arquitectura de agentes del harness** (T-018) → `970_documents/arquitectura_harness.md`: topología bajo restricción de spawning de Claude Code (Governor=A+B en sesión principal; agentes como subagentes hoja), roster de 9 agentes reusables (incl. capa BDD `bdd-writer`→`behavior.md`), flujo por iteración con gates `reviewer`+humano, persistencia runtime (`harness-state.json`/`execution-state.json`/`project-progress.txt`) y rúbrica del evaluador. Decisiones D-026 a D-029. Nuevas tareas T-022 a T-025 (construcción incremental de agentes, E4). |
| 2026-06-26 | S8 | **Gap detectado:** el Sprint Contract falta como artefacto explícito en el flujo del harness. El Governor debe generarlo y obtener aprobación humana **antes** de spawnear al `bdd-writer`. Registrado como **T-028** (ajuste a `/foca-init` o `/foca-continue`). Lección **L-009**. |
| 2026-06-26 | S7 | **Construido el bloque de Especificación** (T-022): agentes `bdd-writer`, `reviewer`, `spec-writer` + skills `review-behavior`, `review-spec` (patrón de skill-checklists, **D-030**; verificado que subagentes pueden usar skills, **L-008**). **Comandos de operación runtime** (**D-031**): `/foca-init` (bootstrap E10-A, T-026) y `/foca-continue` (avance paso a paso, T-027). **Corrido `/foca-init`**: estructura `1000_Project/`+`990_iterations/00_tracer_bullet/` creada, estado runtime inicializado; `bdd-writer` spawn diferido por el humano (revertido a `pending`). Fix `.gitignore` (anclar `data/` al workspace; no ignorar `foca/data` ni `foca/models`). **Renombrada `iterations/` → `990_iterations/`** (convención de prefijo numérico; todas las referencias actualizadas, D-023). |
