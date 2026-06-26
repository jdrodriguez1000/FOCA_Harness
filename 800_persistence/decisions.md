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
**Estado:** 🕐 Propuesta (pendiente confirmación) · 2026-06-26
Propuesto: **Python + `uv`** por el ecosistema de forecasting jerárquico (pandas, statsforecast, hierarchicalforecast de Nixtla, simulación Montecarlo). Pendiente confirmación del usuario.

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
