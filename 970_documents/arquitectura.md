# 🏛️ ARQUITECTURA del producto — FOCA_Harness

> Diseño técnico del **producto** (el pipeline que pronostica demanda). Distinto de la arquitectura del **harness de desarrollo** (agentes specifier/planner/…, ver T-018). Definido en la sesión S4 (2026-06-26). Decisiones asociadas: [D-019]–[D-024] en `800_persistence/decisions.md`.

---

## 🧭 Índice

- [1. Filosofía rectora](#1-filosofía-rectora)
- [2. Stack tecnológico](#2-stack-tecnológico)
- [3. Patrones de diseño](#3-patrones-de-diseño)
- [4. Arquitectura por capas](#4-arquitectura-por-capas)
- [5. Arquitectura de medallones](#5-arquitectura-de-medallones)
- [6. Estructura de directorios](#6-estructura-de-directorios)
- [7. Qué se implementa en el tracer_bullet](#7-qué-se-implementa-en-el-tracer_bullet)

---

## 1. Filosofía rectora

Tres decisiones vigentes dictan todo el diseño:

- **D-014** — grano desde el día 1 + pipeline que no se reescribe ⇒ código **config-driven** y **contratos estables** entre etapas.
- **D-013** — dos ejes independientes (sustancia × forma) ⇒ modelos intercambiables y datos parametrizables, **desacoplados**.
- **D-017 / D-001** — salida archivos+reporte hasta MVP, frontend en evol_1 ⇒ el núcleo **no conoce la UI**; la presentación es un adaptador reemplazable.

**Principio de oro:** construir todas las **costuras (seams)** desde el `tracer_bullet`, pero rellenar solo la **rebanada más delgada** (naïve). El tracer no implementa torneo ni newsvendor, pero deja el hueco donde entrarán sin reescribir nada.

## 2. Stack tecnológico

| Capa | Herramienta | ¿En tracer_bullet? |
|---|---|---|
| Entorno/deps | Python 3.12 + `uv` | ✅ |
| Config / contratos | Pydantic v2 (perillas) + Pandera (esquemas DataFrame) | ✅ |
| Datos | **pandas** | ✅ |
| Forecasting | Nixtla: `statsforecast`, `mlforecast`, `hierarchicalforecast`, `utilsforecast` | ⚠️ desde `stab_2` (tracer = naïve a mano) |
| Decisión | numpy/scipy (cuantil newsvendor + Montecarlo) | ❌ (MVP) |
| Reporte | Jinja2 → Markdown/HTML | ✅ (mínimo) |
| CLI | Typer | ✅ |
| Testing | pytest | ✅ |
| Calidad | ruff (lint+format) · mypy/pyright | ✅ ruff |
| Logs | stdlib `logging` + `rich` (consola = solo logs, D-017) | ✅ |
| Formatos | parquet (intermedio) · CSV/JSON (entregable) | ✅ |

**No se trae todavía** (evitar sobre-ingeniería): orquestadores (Airflow/Prefect/Dagster), base de datos, polars, frameworks de UI. Cada uno tiene su costura reservada.

## 3. Patrones de diseño

1. **Pipeline / Chain de etapas** — las 11 etapas como pasos con contrato `artefacto_entrada → artefacto_salida` (D-005). Idempotentes, resumibles.
2. **Strategy + Registry/Factory** — modelos de forecasting intercambiables; el **torneo** (D-015) registra retadores y corona campeón. El naïve es la primera Strategy.
3. **Ports & Adapters (hexagonal-lite)** — el núcleo no conoce de dónde vienen/van los datos: `DataSource`, `MedallionStore`, `ReportRenderer` son adaptadores. Habilita multi-tenant (evol_2) y frontend (evol_1) sin reescribir el núcleo.
4. **Config-driven / Parameter Object** — las perillas (D-014) como objeto Pydantic validado.
5. **Builder** — el generador sintético parametrizable.
6. **Repository / namespacing por tenant** — acceso por `tenant_id` desde el día 1 (degenerado `demo`).

## 4. Arquitectura por capas

Dependencias hacia adentro:

```
   CLI (typer)
        │
   pipeline/  ── orquestador propio + 11 etapas (lee/escribe artefactos)
    │     │
 models/  reporting/        ← strategies (torneo) · renderer (Jinja2)
    │
 domain/  ── grano, jerarquías  (lógica pura, sin I/O)
    ▲
   config/ (perillas) · data/ (generador + ports) · io/ (medallión + artifacts)
```

**Contrato de etapa** (la costura estable):

```python
class Stage(Protocol):
    name: str
    def run(self, ctx: RunContext) -> StageResult: ...
# RunContext = { config, tenant_id, run_id, paths, logger }
```

**Orquestación (D-022):** lista de etapas ejecutadas en orden por un orquestador propio mínimo, sin framework.

Cada capacidad futura entra **dentro** de una etapa existente sin tocar el orquestador:
des-censura (stab_1) → `s04_clean` · torneo (stab_2/3) → `s07_train` · reconciliación (stab_4/5) → entre `s07` y `s08` · newsvendor (MVP) → `s10`.

## 5. Arquitectura de medallones

Las capas de datos por tenant son los **contratos estables** del pipeline (refuerza D-014). Cada capa al grano `Demanda(Producto, Sede, Periodo)` desde el día 1.

| Capa | Etapas que la producen | Contenido | Esquema (Pandera) |
|---|---|---|---|
| 🥉 **Bronze** | 2 Carga | Crudo del cliente, **inmutable**, append-only, particionado por ingesta. *(El generador sintético escribe aquí.)* | Laxo |
| 🥈 **Silver** | 3 Salud · 4 Limpieza (+`stab_1` des-censura) | Demanda limpia, validada, al grano; demanda real reconstruida. Capa canónica. | Estricto |
| 🥇 **Gold** | 6 Ingeniería de características | Tablas de modelado (features + target) listas para entrenamiento. | Estricto |
| *(downstream)* | 7 Train · 8 Infer · 9 MC · 10 Report · 11 Monitor | Modelos/campeón, pronósticos, escenarios, entregables → `models/` y `runs/`. | — |

EDA (5) lee silver/gold y produce análisis (artefacto de corrida, no capa). Onboarding (1) es metadata/config, pre-bronze. Contratos por etapa: `s02→bronze`, `s04→silver`, `s06→gold`, `s07←gold`. Acceso vía Port `MedallionStore` (`read_bronze / write_silver / read_gold …`).

## 6. Estructura de directorios

```
FOCA_Harness/
├── CLAUDE.md
│
├── 800_persistence/  900_scope/  950_guideline/  970_documents/  iterations/   ← meta-proceso
│
└── 1000_Project/          # ── EL PRODUCTO (autocontenido) ──
    ├── pyproject.toml  uv.lock  .python-version  README.md
    ├── foca/              # paquete Python
    │   ├── config/    knobs.py (perillas Pydantic) · schemas.py (contratos Pandera)
    │   ├── domain/    grain.py · hierarchy.py            (lógica pura)
    │   ├── data/      generator.py (Builder) · sources.py (Port DataSource)
    │   ├── pipeline/  context.py · orchestrator.py (propio)
    │   │   └── stages/  s01_onboarding.py … s11_monitor.py
    │   ├── models/    base.py · naive.py · registry.py · tournament.py (stub)
    │   ├── decision/  newsvendor.py · montecarlo.py     (vacíos hasta MVP)
    │   ├── reporting/ renderer.py · templates/
    │   ├── io/        lake.py (MedallionStore) · artifact_store.py · paths.py
    │   └── cli.py     # `foca run --tenant demo …`
    ├── tests/  unit/  e2e/
    └── data/             # workspace (gitignored)
        └── tenants/<tenant_id>/         # degenerado: demo  (multi-tenant día 1)
            ├── bronze/   silver/   gold/   models/
            └── runs/<run_id>/  05_eda/ 08_inference/ 09_montecarlo/ 10_client_report/
                                 run_config.json   run.log
```

## 7. Qué se implementa en el tracer_bullet

Para no caer en scope creep (lo valida la fase 5 del harness):

- **Se rellena:** `config`, `domain`, `data/generator` (perillas en 1), las 11 `stages` en versión mínima, `models/naive`, `reporting` mínimo, `io` (incl. `MedallionStore` con bronze/silver/gold), `cli`.
- **Costura vacía / stub:** `models/tournament`, `models/registry`, `decision/*`.
- **Aún no entra:** Nixtla (desde `stab_2`), newsvendor/Montecarlo (MVP), frontend (`evol_1`), multi-tenant real (`evol_2`).
