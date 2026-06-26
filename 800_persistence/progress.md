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

**Fase actual:** Encuadre conceptual COMPLETO. A punto de iniciar la **Iteración 1 — `tracer_bullet`**, comenzando por su **Fase 1 del harness: Specification**.

**Aún NO se ha escrito código.** Estamos respetando la regla del harness: no codear antes de especificar y planear.

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
- ⏳ Stack técnico: PROPUESTO Python + `uv` (pendiente confirmación del usuario).

## 7. Próximo hito

Con el alcance ya definido (roadmap), los siguientes pasos son: (a) diseñar la **arquitectura de agentes del harness** (T-018) y (b) crear `900_scope/scope_tracer_bullet.md` (T-019, insumo de alcance que aprueba el humano), para luego producir el `spec.md` del tracer_bullet (T-003) vía el agente *specifier*.

## 8. Bitácora de sesiones

| Fecha | Sesión | Qué se hizo |
|---|---|---|
| 2026-06-26 | S1 | Encuadre conceptual del proyecto. Definición de iteraciones + harness. Creación de `800_persistence/`, `970_documents/idea.md` y `CLAUDE.md`. |
| 2026-06-26 | S2 | Tooling: estructura `.claude/` (settings), comandos `/foca-progress` y `/foca-next`. Git inicializado + remoto + `.gitignore` + push inicial a GitHub. |
| 2026-06-26 | S3 | Lectura de `950_guideline/` (metodología + principios). Definición del **alcance por iteración en términos de usuario** → `970_documents/roadmap_iteraciones.md`. Decisiones D-012 a D-018 (torneo de campeones, reconciliación producto→geo, grano desde día 1 + generador parametrizable, reglas de salida, frontend en evol_1). Esbozo de arquitectura de agentes del harness. |
