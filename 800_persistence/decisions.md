# 🧩 DECISIONS — Decisiones tomadas en el proyecto

> Registro de decisiones (estilo ADR ligero). Cada decisión queda con su razón. Todo agente añade nuevas decisiones y NO borra las anteriores (si una se revierte, se marca como *Reemplazada* y se enlaza la nueva).

**Estados:** `✔️ Vigente` · `🔄 Reemplazada` · `🕐 Propuesta (pendiente confirmación)`

---

## 🧭 Índice

- [D-001 — Modelo de negocio: Service as a Software](#d-001--modelo-de-negocio-service-as-a-software)
- [D-002 — Predecir DEMANDA, no ventas](#d-002--predecir-demanda-no-ventas)
- [D-003 — Dos jerarquías cruzadas](#d-003--dos-jerarquías-cruzadas)
- [D-004 — Plataforma multi-tenant operada por científicos de datos](#d-004--plataforma-multi-tenant-operada-por-científicos-de-datos)
- [D-005 — Pipeline de 11 etapas](#d-005--pipeline-de-11-etapas)
- [D-006 — Metodología de iteraciones](#d-006--metodología-de-iteraciones)
- [D-007 — Harness de 5 fases](#d-007--harness-de-5-fases)
- [D-008 — Memoria del proyecto en 800_persistence](#d-008--memoria-del-proyecto-en-800_persistence)
- [D-009 — Stack técnico (propuesto)](#d-009--stack-técnico-propuesto)
- [D-010 — Comandos de proyecto para el protocolo de memoria](#d-010--comandos-de-proyecto-para-el-protocolo-de-memoria)
- [D-011 — Repositorio y rama por defecto](#d-011--repositorio-y-rama-por-defecto)
- [D-012 — Alcance por iteración definido en términos de usuario (roadmap)](#d-012--alcance-por-iteración-definido-en-términos-de-usuario-roadmap)
- [D-013 — Dos ejes de crecimiento: sustancia × forma de datos](#d-013--dos-ejes-de-crecimiento-sustancia--forma-de-datos)
- [D-014 — Grano desde el día 1 y generador sintético parametrizable](#d-014--grano-desde-el-día-1-y-generador-sintético-parametrizable)
- [D-015 — Torneo de campeones: stab_2 (1 SKU) y stab_3 (varios SKU)](#d-015--torneo-de-campeones-stab_2-1-sku-y-stab_3-varios-sku)
- [D-016 — Reconciliación: producto (stab_4) antes que geografía (stab_5)](#d-016--reconciliación-producto-stab_4-antes-que-geografía-stab_5)
- [D-017 — Reglas de salida: archivos+reporte hasta MVP, frontend en evol_1](#d-017--reglas-de-salida-archivosreporte-hasta-mvp-frontend-en-evol_1)
- [D-018 — Secuencia de iteraciones de estabilización y evolución](#d-018--secuencia-de-iteraciones-de-estabilización-y-evolución)
- [D-019 — Stack técnico confirmado](#d-019--stack-técnico-confirmado)
- [D-020 — Arquitectura del producto (hexagonal-lite por capas)](#d-020--arquitectura-del-producto-hexagonal-lite-por-capas)
- [D-021 — Patrones de diseño](#d-021--patrones-de-diseño)
- [D-022 — Orquestación propia mínima (sin framework)](#d-022--orquestación-propia-mínima-sin-framework)
- [D-023 — Layout del repositorio (producto en 1000_Project)](#d-023--layout-del-repositorio-producto-en-1000_project)
- [D-024 — Arquitectura de medallones para los datos](#d-024--arquitectura-de-medallones-para-los-datos)
- [D-025 — Plantilla de alcance en 980_templates con trazabilidad de pendientes](#d-025--plantilla-de-alcance-en-980_templates-con-trazabilidad-de-pendientes)
- [D-026 — Topología del harness bajo la restricción de spawning de Claude Code](#d-026--topología-del-harness-bajo-la-restricción-de-spawning-de-claude-code)
- [D-027 — Agentes como roles reusables por especialidad (no por iteración)](#d-027--agentes-como-roles-reusables-por-especialidad-no-por-iteración)
- [D-028 — Flujo del harness por iteración: scope→behavior→spec→plan→exec→verif→valid](#d-028--flujo-del-harness-por-iteración-scopebehaviorspecplanexecverifvalid)
- [D-029 — Archivos de persistencia runtime del harness](#d-029--archivos-de-persistencia-runtime-del-harness)
- [D-030 — Rúbricas del reviewer como skill-checklists (Claude Code Skills)](#d-030--rúbricas-del-reviewer-como-skill-checklists-claude-code-skills)
- [D-031 — Comandos de operación runtime: /foca-init y /foca-continue](#d-031--comandos-de-operación-runtime-foca-init-y-foca-continue)

---

## D-001 — Modelo de negocio: Service as a Software
**Estado:** ✔️ Vigente · 2026-06-26
Triple S entrega el **resultado** como servicio, no el software. El software + agentes de IA son el motor interno que permite escalar. Implicación: la prioridad de ingeniería es el pipeline interno robusto, no una UI self-service para el cliente.

## D-002 — Predecir DEMANDA, no ventas
**Estado:** ✔️ Vigente · 2026-06-26
El objetivo es la demanda real (lo que el cliente quería pedir), no las ventas censuradas por quiebres de stock. Ver [lessons.md L-001].

## D-003 — Dos jerarquías cruzadas
**Estado:** ✔️ Vigente · 2026-06-26
Producto (`Familia → Categoría → Subcategoría → Producto`) y Cliente/Geo (`Región → País → Ciudad → Sede`). La geografía ES la estructura del cliente. Grano atómico: `Demanda(Producto, Sede, Periodo)`. Implica forecasting jerárquico con reconciliación.

## D-004 — Plataforma multi-tenant operada por científicos de datos
**Estado:** ✔️ Vigente · 2026-06-26
Los usuarios del software son los científicos de datos de Triple S, no los clientes ABC. Soporta múltiples empresas ABC con datos aislados.

## D-005 — Pipeline de 11 etapas
**Estado:** ✔️ Vigente · 2026-06-26
Construcción: 1 Onboarding, 2 Carga, 3 Salud de datos, 4 Limpieza, 5 EDA, 6 Ingeniería de características, 7 Entrenamiento/modelado. Operación: 8 Inferencia, 9 Montecarlo, 10 Información al cliente, 11 Monitoreo. Producción: fuera de alcance por ahora.

## D-006 — Metodología de iteraciones
**Estado:** ✔️ Vigente · 2026-06-26
`tracer_bullet → stab_1, stab_2… → MVP → evol_1, evol_2… → final`. No se salta de tracer_bullet a MVP; se sube de menos a más por iteraciones de estabilización `stab_*`. Entre MVP y final, iteraciones de evolución `evol_*`.

## D-007 — Harness de 5 fases
**Estado:** ✔️ Vigente · 2026-06-26
Cada iteración pasa por: 1) Specification, 2) Planning, 3) Execution, 4) **Verification** (testing — que funcione), 5) **Validation** (conformidad con la spec — que sea exactamente lo pactado, sin scope creep ni gaps).

## D-008 — Memoria del proyecto en 800_persistence
**Estado:** ✔️ Vigente · 2026-06-26
Carpeta `800_persistence/` con `progress.md`, `tasks.md`, `lessons.md`, `decisions.md`, cada uno con índice navegable. Protocolo: todo agente LEE estos archivos al iniciar sesión y los ACTUALIZA al terminar.

## D-009 — Stack técnico (propuesto)
**Estado:** 🔄 Reemplazada por [D-019] · 2026-06-26
Propuesto: **Python + `uv`** por el ecosistema de forecasting jerárquico (pandas, statsforecast, hierarchicalforecast de Nixtla, simulación Montecarlo). Confirmado y ampliado en [D-019].

## D-010 — Comandos de proyecto para el protocolo de memoria
**Estado:** ✔️ Vigente · 2026-06-26
Dos comandos de Claude Code **a nivel de proyecto** (`.claude/commands/`, versionados): `/foca-next` (inicio de sesión: carga progreso y próximas tareas vía índice; decisions/lessons a demanda) y `/foca-progress` (cierre de sesión: actualiza la memoria, hace commit y push). `settings.local.json` queda fuera de versión (`.gitignore`).

## D-011 — Repositorio y rama por defecto
**Estado:** ✔️ Vigente · 2026-06-26
Repositorio remoto: `https://github.com/jdrodriguez1000/FOCA_Harness.git`. Rama por defecto: `main`. Commits con co-autoría de Claude y mensajes en español.

## D-012 — Alcance por iteración definido en términos de usuario (roadmap)
**Estado:** ✔️ Vigente · 2026-06-26
El alcance de cada iteración se define **en términos de usuario** (qué puede hacer el científico de datos / qué recibe el cliente ABC), documentado en `970_documents/roadmap_iteraciones.md`. Es la fuente de verdad del alcance; el detalle fino de cada iteración vivirá en su `1_specification/`.

## D-013 — Dos ejes de crecimiento: sustancia × forma de datos
**Estado:** ✔️ Vigente · 2026-06-26
Las iteraciones crecen en **dos ejes independientes**: (a) *sustancia del pipeline* (naïve → des-censura → torneo → reconciliación → newsvendor → operación) y (b) *forma de los datos* (1 SKU×1 sede → varios SKU → jerarquía producto → jerarquía geo). Son perillas distintas.

## D-014 — Grano desde el día 1 y generador sintético parametrizable
**Estado:** ✔️ Vigente · 2026-06-26
Todo se construye sobre el grano `Demanda(Producto, Sede, Periodo)` desde el `tracer_bullet`. "1 SKU × 1 sede" es el caso degenerado (|P|=1, |S|=1), no un caso especial. El **generador sintético** se construye una sola vez con perillas (`n_familias, n_categorias, n_subcategorias, n_skus, n_regiones, n_paises, n_ciudades, n_sedes, tasa_de_quiebres, n_periodos…`); cada iteración solo sube perillas, sin reescribir el pipeline. Ver [D-003].

## D-015 — Torneo de campeones: stab_2 (1 SKU) y stab_3 (varios SKU)
**Estado:** ✔️ Vigente · 2026-06-26
El paso de naïve a ML se hace vía **torneo de campeones** (retadores compiten, *backtesting* corona al mejor). Se introduce en dos pasos: **`stab_2` = torneo sobre 1 SKU** (montar el mecanismo de coronación en una serie) y **`stab_3` = escalar a varios SKU planos**. El naïve queda como línea base a vencer. Conecta con monitoreo (D-018/evol_3): drift → re-torneo.

## D-016 — Reconciliación: producto (stab_4) antes que geografía (stab_5)
**Estado:** ✔️ Vigente · 2026-06-26
La reconciliación jerárquica se introduce en dos pasos separados, de menos a más: **`stab_4` = jerarquía de producto** (familia = suma de SKU) con 1 sede, y **`stab_5` = jerarquía geográfica + reconciliación cruzada** producto × geo. Producto primero, geografía después.

## D-017 — Reglas de salida: archivos+reporte hasta MVP, frontend en evol_1
**Estado:** ✔️ Vigente · 2026-06-26
De `tracer_bullet` a `MVP` la salida son **archivos planos (CSV/JSON) + reporte autogenerado (Markdown/HTML)**; la consola solo para logs/resumen, nunca como entregable. El **primer frontend (workbench para el científico de datos) entra en `evol_1`** (no antes — mínima complejidad E4). El **cliente ABC nunca recibe app**: su entregable es un reporte/archivo (coherente con D-001).

## D-018 — Secuencia de iteraciones de estabilización y evolución
**Estado:** ✔️ Vigente · 2026-06-26
Secuencia acordada: `tracer_bullet` → `stab_1` (des-censura) → `stab_2` (torneo 1 SKU) → `stab_3` (torneo varios SKU) → `stab_4` (reconc. producto) → `stab_5` (reconc. geo/cruzada) → `MVP` (newsvendor) → `evol_1` (workbench) → `evol_2` (multi-tenant) → `evol_3` (monitoreo/drift) → `final`. Concreta y reordena la metodología genérica de [D-006].

## D-019 — Stack técnico confirmado
**Estado:** ✔️ Vigente · 2026-06-26 · confirma [D-009]
**Python 3.12 + `uv`**. DataFrames con **pandas** (compatibilidad con el ecosistema Nixtla; descartado polars por fricción/sobre-ingeniería para la escala actual). Config/perillas con **Pydantic v2**; contratos de DataFrame con **Pandera**. Forecasting con **Nixtla** (`statsforecast`, `mlforecast`, `hierarchicalforecast`, `utilsforecast`) — entra desde `stab_2`, no en el tracer. Decisión newsvendor/Montecarlo con **numpy/scipy** (MVP). CLI con **Typer**. Reporte con **Jinja2 → Markdown/HTML**. Tests con **pytest**. Lint/format con **ruff**. Logs con stdlib `logging` + `rich`. Formatos: **parquet** intermedio, **CSV/JSON** entregable. Detalle completo en `970_documents/arquitectura.md`.

## D-020 — Arquitectura del producto (hexagonal-lite por capas)
**Estado:** ✔️ Vigente · 2026-06-26
Capas con dependencias hacia adentro: **domain** (lógica pura: grano, jerarquías) · **pipeline** (orquestador + 11 etapas con contrato `Stage`) · **models** (estrategias de forecasting) · adaptadores de I/O (**Ports & Adapters**: `DataSource`, `MedallionStore`, `ReportRenderer`) · **config** (perillas). Principio rector: **construir todas las costuras (seams) desde el `tracer_bullet` pero rellenar solo la rebanada mínima** (naïve). Cada capacidad futura entra *dentro* de una etapa existente sin tocar el orquestador (des-censura→`s04`, torneo→`s07`, reconciliación→entre `s07` y `s08`, newsvendor→`s10`). Apoya [D-001] (núcleo sin UI) y [D-014]. Ver `970_documents/arquitectura.md`.

## D-021 — Patrones de diseño
**Estado:** ✔️ Vigente · 2026-06-26
(1) **Pipeline/Chain** — las 11 etapas como pasos con contrato `artefacto_entrada → artefacto_salida`. (2) **Strategy + Registry/Factory** — modelos intercambiables; el torneo ([D-015]) registra retadores y corona campeón. (3) **Ports & Adapters** — núcleo aislado de I/O (datos y reporte reemplazables sin tocar el núcleo → habilita evol_1/evol_2 sin reescritura). (4) **Config-driven / Parameter Object** — las perillas de [D-014] como objeto Pydantic validado. (5) **Builder** — el generador sintético parametrizable. (6) **Repository / namespacing por tenant** — acceso a datos por `tenant_id` desde el día 1 (degenerado `demo`).

## D-022 — Orquestación propia mínima (sin framework)
**Estado:** ✔️ Vigente · 2026-06-26
Las 11 etapas se ejecutan con un **orquestador propio mínimo**: una lista de etapas con contrato `Stage(ctx) -> StageResult`, ejecutadas en orden, idempotentes y resumibles. **Cero frameworks** (Airflow/Prefect/Dagster) hasta que una iteración de operación lo justifique; la costura del contrato `Stage` permite cambiarlo después sin reescribir las etapas.

## D-023 — Layout del repositorio (producto en 1000_Project)
**Estado:** ✔️ Vigente · 2026-06-26
El **producto** vive autocontenido en **`1000_Project/`** (incluye `pyproject.toml`, el paquete `foca/`, `tests/` y `data/`). El **meta-proceso** sigue en las carpetas numéricas de la raíz (`800_persistence/`, `900_scope/`, `950_guideline/`, `970_documents/`, `990_iterations/`). Los **datos por tenant** usan arquitectura de medallones (ver [D-024]), no carpetas por-etapa; el aislamiento multi-tenant es a nivel de ruta (`data/tenants/<tenant_id>/`) desde el día 1.

## D-024 — Arquitectura de medallones para los datos
**Estado:** ✔️ Vigente · 2026-06-26
Las capas de datos por tenant siguen el patrón **medallón**: 🥉 **bronze** (crudo inmutable del cliente, lo produce la etapa 2 Carga; el generador sintético escribe aquí) → 🥈 **silver** (limpio, validado y des-censurado al grano, etapas 3 Salud + 4 Limpieza, +`stab_1`) → 🥇 **gold** (tablas de features listas para modelar, etapa 6). Las capas **son los contratos estables** entre grupos de etapas (refuerza [D-014]); cada una al grano `Demanda(Producto, Sede, Periodo)` desde el `tracer_bullet`. Validación de esquema (**Pandera**) en cada promoción (bronze laxo, silver/gold estricto). Acceso vía Port **`MedallionStore`** ([D-021]). Lo downstream (modelos, pronósticos, escenarios, reporte) no es capa de datos → va a `models/` y a `runs/<run_id>/`.

## D-025 — Plantilla de alcance en 980_templates con trazabilidad de pendientes
**Estado:** ✔️ Vigente · 2026-06-26
El insumo de alcance por iteración ([D-012], `900_scope/scope_<iteracion>.md`) se redacta a partir de una plantilla versionada en **`980_templates/scope_template.md`**. La plantilla expresa el alcance **en términos de usuario** y separa **Alcance IN / Fuera de alcance** (anti scope-creep, fase 5). Incluye un **mecanismo de trazabilidad de pendientes entre iteraciones** con dos secciones espejo: **§5 Pendientes heredados que se resuelven aquí** (apuntan a la §6 de la iteración de origen) y **§6 Pendientes que esta iteración deja para futuras**. La §5 **no aplica al `tracer_bullet`** (primera iteración, sin herencia → "N/A"). La plantilla también recoge perillas del generador ([D-014]), capa medallón por etapa ([D-024]), reglas de salida ([D-017]) y aprobación humana previa a Specification.

## D-026 — Topología del harness bajo la restricción de spawning de Claude Code
**Estado:** ✔️ Vigente · 2026-06-26
Restricción dura: en Claude Code **solo la sesión principal puede invocar subagentes** (tool `Agent`); un subagente es una *hoja* y no puede spawnear a otro. Esto rompe la cadena de dos niveles `A ▶ B ▶ Workers` de `methodology.md`. **Decisión:** **fusionar Governor (A) + Orchestrator (B) en la sesión principal** (único spawner), y dejar como **subagentes de contexto fresco** solo lo que necesita aislamiento (agentes de trabajo + `reviewer` + `evaluator`). Se preservan P3 (evaluador/reviewer independientes: leen artefactos, no evalúan lo propio), P4/P2/E6 (cada subagente fresco escribe a FS y reporta solo paths). Concesión documentada: A y B comparten contexto; mitigación = fuente de verdad en filesystem + context reset entre fases (E2). Coherente con E4. **Interacción humana solo vía el Governor** (un subagente no puede pedir aprobación). Detalle en `970_documents/arquitectura_harness.md`.

## D-027 — Agentes como roles reusables por especialidad (no por iteración)
**Estado:** ✔️ Vigente · 2026-06-26
Los agentes del harness son **roles estables por especialidad (P1)**, definidos **una sola vez** en `.claude/agents/` y reutilizados en **todas** las iteraciones; lo que cambia entre iteraciones son sus **insumos** (`scope_<iter>.md`, `behavior.md`, `spec.md`, `plan.md`), no el agente. Crear "un agente por iteración" multiplicaría piezas sin razón (viola E4). Roster: `bdd-writer`, `spec-writer`, `plan-writer`, `reviewer` (único, invocado en cada gate de documento con contexto fresco), `tester`, `dev-backend`, `dev-optimizer`, `verificator`, `evaluator`. El **Executor es una familia**: `dev-backend` cubre tracer_bullet→MVP; **`dev-frontend` se suma en `evol_1`** (workbench, coherente con [D-017]). Modelos por rol ([methodology §9]): Opus para writers/reviewer/evaluator, Sonnet para tester/dev/verificator.

## D-028 — Flujo del harness por iteración: scope→behavior→spec→plan→exec→verif→valid
**Estado:** ✔️ Vigente · 2026-06-26
El patrón A/B/C **envuelve toda la iteración** (un solo veredicto del `evaluator` al final, no un gate de C por fase — opción simple, E4). Flujo: el **Governor** determina la iteración y lee `scope_<iter>.md` (ya aprobado, [D-012]); **Fase 1 Specification** → `bdd-writer` escribe `behavior.md` (capa **BDD** nueva, escenarios de comportamiento desde el scope), `reviewer` lo audita y 👤 humano lo aprueba; luego `spec-writer` escribe `spec.md` (desde behavior + `arquitectura.md`), `reviewer` + 👤 humano. **Fase 2 Planning** → `plan-writer` escribe `plan.md`, `reviewer` + 👤 humano. **Fase 3 Execution** (TDD) → `tester` (RED) → `dev-backend` (GREEN) → `dev-optimizer` (REFACTOR). **Fase 4 Verification** → `verificator` re-ejecuta unit+integración+e2e de forma independiente. **Fase 5 Validation** → `evaluator` valida conformidad con el scope (sin creep ni gaps) + métricas del harness, y el Governor decide "Avanzar o Repetir". **Gates humanos:** 3 por iteración (behavior, spec, plan) + el de scope ([D-012]) = 4 puntos de control antes de codear; verificación y validación son gates automáticos. Patrón de gate: `reviewer` automático **antes** del humano (no presentar borradores incompletos). Detalle en `970_documents/arquitectura_harness.md`.

## D-029 — Archivos de persistencia runtime del harness
**Estado:** ✔️ Vigente · 2026-06-26
Dos planos de memoria complementarios: **(a) memoria curada humana** `800_persistence/*.md` ([D-008], cadencia: cierre de sesión vía `/foca-progress`); **(b) estado runtime del harness** escrito por los agentes durante una corrida, con **Single Writer Rule**: `harness-state.json` (dueño **Governor**, ubicación `800_persistence/harness-state.json`, estado estratégico global: iteración/fase/sprint contract/aprobaciones/CRs) · `execution-state.json` (dueño **Governor como Orchestrator B**, ubicación **por iteración** `990_iterations/<nn>_<nombre>/execution-state.json`, estado técnico: `orchestration_plan` E12 / agentes / checkpoints E5 / `early_eval` E9) · **`project-progress.txt`** (renombrado desde `claude-progress.txt`; dueño: instancia activa; ubicación `800_persistence/project-progress.txt`; bitácora narrativa runtime append-only). `project-progress.txt` (granular) no sustituye a `progress.md` (resumen curado): distinta cadencia y audiencia. Se aplica E6 (referencias ligeras: paths/IDs, no contenido). Estos archivos se crean al arrancar la primera corrida (ritual E10-A), no antes. Esquemas mínimos en `970_documents/arquitectura_harness.md`.

## D-030 — Rúbricas del reviewer como skill-checklists (Claude Code Skills)
**Estado:** ✔️ Vigente · 2026-06-26
El `reviewer` ([D-027]) audita documentos de distinto tipo (`behavior.md`, `spec.md`, `plan.md`), cada uno con una rúbrica **distinta y larga**. **Decisión:** externalizar esas rúbricas como **Claude Code Skills** en `.claude/skills/` (`review-behavior`, `review-spec`, `review-plan`), con `user-invocable: false` (maquinaria del harness, ocultas del menú humano). El `reviewer` invoca la skill que corresponde al documento con la herramienta `Skill`, de modo que **solo carga la checklist del documento que revisa** (el contenido de una skill se inyecta solo al usarse → coste casi nulo hasta entonces; ideal para material de referencia largo). El **cuerpo del agente** lleva las comprobaciones *transversales* (conformidad con insumos/arquitectura, trazabilidad E6, autocontención); **cada skill** lleva las *específicas* del documento. Se construyen incrementalmente junto a su agente escritor (E4/§8): `review-behavior` con `bdd-writer`; `review-spec` con `spec-writer`; `review-plan` con `plan-writer`. **Corrección de roster:** el `reviewer` requiere `Write` y `Skill` además de `Read, Glob, Grep` (la tabla de `arquitectura_harness.md §2` solo listaba lectura). Sustento técnico: los subagentes de Claude Code SÍ pueden usar skills, vía precarga (`skills:` en frontmatter) o invocación on-demand (tool `Skill`) — ver [lessons.md L-008].

## D-031 — Comandos de operación runtime: /foca-init y /foca-continue
**Estado:** ✔️ Vigente · 2026-06-26
Se añaden dos comandos de proyecto que **operan el harness en runtime** (complementan a `/foca-next` y `/foca-progress` de [D-010], que gestionan la memoria curada): **`/foca-init`** (un solo uso) ejecuta el **ritual de arranque E10-A** — guard de idempotencia, valida el `scope_<iter>.md` aprobado, crea la estructura de `1000_Project/` (solo carpetas + `.gitkeep`; el código `.py` es de Execution, no del bootstrap) y de `990_iterations/<nn>_<iter>/`, inicializa los 3 archivos de estado runtime ([D-029]) y lanza la Fase 1 spawneando al `bdd-writer`. **`/foca-continue`** (repetible) lee el estado runtime (`harness-state.json`, `execution-state.json`, `project-progress.txt`), diagnostica el paso en curso del `orchestration_plan` y **conduce el siguiente** (spawnear el agente que toca, presentar un gate humano sin auto-aprobar, re-spawnear por un rechazo `changes_requested`, o avanzar de fase — bloqueándose si el agente requerido aún no existe, E4). Ambos `disable-model-invocation: true` (manuales). El Governor (sesión principal, [D-026]) es quien los ejecuta y media los gates humanos.
