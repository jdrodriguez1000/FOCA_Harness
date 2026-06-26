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
