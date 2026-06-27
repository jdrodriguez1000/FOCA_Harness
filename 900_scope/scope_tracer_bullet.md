# 🎯 SCOPE — `tracer_bullet`

> **Insumo de alcance aprobado por el humano** (D-012) que define qué construye esta iteración, **antes** de redactar la `spec.md`. Derivado de `970_documents/roadmap_iteraciones.md` (fuente de verdad del alcance) y de `970_documents/arquitectura.md`. El alcance se expresa **en términos de usuario**; las tareas técnicas van en la `plan.md`.

---

## 🧭 Índice

- [0. Metadatos](#0-metadatos)
- [1. Capacidad en términos de usuario](#1-capacidad-en-términos-de-usuario)
- [2. Posición en el roadmap (ejes de crecimiento)](#2-posición-en-el-roadmap-ejes-de-crecimiento)
- [3. Alcance IN — qué SÍ se construye](#3-alcance-in--qué-sí-se-construye)
- [4. Fuera de alcance — qué NO se construye](#4-fuera-de-alcance--qué-no-se-construye)
- [5. Pendientes heredados que se resuelven en esta iteración](#5-pendientes-heredados-que-se-resuelven-en-esta-iteración)
- [6. Pendientes que esta iteración deja para futuras](#6-pendientes-que-esta-iteración-deja-para-futuras)
- [7. Perillas del generador sintético](#7-perillas-del-generador-sintético)
- [8. Entregable / salida esperada](#8-entregable--salida-esperada)
- [9. Criterios de aceptación (alto nivel)](#9-criterios-de-aceptación-alto-nivel)
- [10. Supuestos y riesgos](#10-supuestos-y-riesgos)
- [11. Decisiones relevantes](#11-decisiones-relevantes)
- [12. Aprobación](#12-aprobación)

---

## 0. Metadatos

| Campo | Valor |
|---|---|
| **Iteración** | `tracer_bullet` |
| **Posición en la secuencia** | `0` de la secuencia D-018 (primera iteración) |
| **Fecha** | 2026-06-26 |
| **Redactado por** | Claude Code (sesión S6) |
| **Aprobado por (humano)** | Triple S (jdrodriguez1000) |
| **Estado** | ✅ Aprobado |

## 1. Capacidad en términos de usuario

**"Como científico de datos de Triple S, puedo correr el proceso completo de punta a punta sobre datos de prueba con un solo comando y obtener un entregable, aunque el pronóstico (naïve) todavía no sea bueno."**

El objetivo del `tracer_bullet` es **probar que el hilo conductor de las 11 etapas existe y conecta de extremo a extremo** (la "bala trazadora" que atraviesa toda la arquitectura), no producir un pronóstico de calidad.

Destinatario del resultado: el **cliente ABC recibe** un reporte/archivo con un pronóstico de demanda naïve para su única serie (1 SKU en 1 sede). No recibe ninguna app.

## 2. Posición en el roadmap (ejes de crecimiento)

| Eje | Esta iteración | Iteración anterior | Qué cambia |
|---|---|---|---|
| **Sustancia** (capacidad del pipeline) | **naïve** (pronóstico de línea base, sin des-censura ni torneo) | — (primera) | Se establece el hilo de extremo a extremo: el pipeline completo corre, aunque el modelo sea trivial. |
| **Forma** (escala/estructura de datos) | **1 SKU × 1 sede** (una sola serie; caso degenerado del grano) | — (primera) | Se fija el grano atómico `Demanda(Producto, Sede, Periodo)` con todas las perillas del generador en 1. |

## 3. Alcance IN — qué SÍ se construye

> Cada capacidad debe ser verificable. Recuerda el principio de costuras (L-006): una costura vacía **no** cuenta como alcance construido — cada etapa tocada debe hacer algo real, por mínimo que sea.

- [ ] **Generador de datos sintéticos parametrizable** (se construye una sola vez aquí, D-014) con todas las perillas; en esta iteración todas en 1 salvo `n_periodos` y `tasa_de_quiebres`. Inyecta censura (quiebres de stock) aunque el pipeline aún no la corrija.
- [ ] **Orquestador mínimo propio** (D-022) que encadena las 11 etapas en una sola corrida (`run_id`).
- [ ] **Recorrido completo de las 11 etapas** sobre la única serie, cada una haciendo lo mínimo real (ver tabla).
- [ ] **Contratos de medallón** bronze → silver → gold (D-024) materializados como archivos, aun si las transformaciones son casi identidad.
- [ ] **Modelo naïve** (p. ej. último valor / media simple) como pronóstico.
- [ ] **Entregable de la etapa 10**: archivos (CSV/JSON) + reporte autogenerado (Markdown/HTML) para el cliente ABC.
- [ ] **Un solo comando** (CLI Typer) que dispara la corrida completa de punta a punta.

**Etapas del pipeline (1–11) tocadas en esta iteración** y cómo:

| Etapa | ¿Se toca? | Qué se hace / capa medallón afectada |
|---|---|---|
| 1 Onboarding | sí | Registro mínimo del tenant ABC de prueba y de su config de corrida (1 SKU, 1 sede). |
| 2 Carga | sí | Lee los datos del generador y los deposita como **🥉 bronze** (crudo, tal cual). |
| 3 Salud | sí | Chequeos mínimos (filas presentes, periodos contiguos, tipos). Reporta, no corrige. |
| 4 Limpieza | sí | Normalización mínima → **🥈 silver** (sin des-censura todavía: queda fuera). |
| 5 EDA | sí | Estadísticos descriptivos básicos de la serie (volcados al reporte/run). |
| 6 Ingeniería de características | sí | Features mínimos (p. ej. lags triviales / calendario) → **🥇 gold**. |
| 7 Entrenamiento/modelado | sí | Ajuste del modelo **naïve** sobre la serie. |
| 8 Inferencia | sí | Pronóstico naïve para el horizonte definido. |
| 9 Montecarlo | sí | Versión mínima: una banda de incertidumbre trivial / passthrough (costura real pero simple). |
| 10 Información al cliente | sí | Genera el entregable (CSV/JSON + reporte MD/HTML). |
| 11 Monitoreo | sí | Registro mínimo de la corrida (metadatos del `run_id`, métricas naïve). Sin drift ni re-torneo. |

## 4. Fuera de alcance — qué NO se construye

- ❌ **Des-censura de demanda** (corregir periodos con quiebre) → `stab_1`.
- ❌ **Torneo de modelos / retadores / backtesting / coronación de campeón** → `stab_2`.
- ❌ **Múltiples series / varios SKU** → `stab_3`.
- ❌ **Jerarquía de producto y reconciliación entre niveles** → `stab_4`.
- ❌ **Jerarquía geográfica y reconciliación cruzada** → `stab_5`.
- ❌ **Decisión newsvendor bajo costos asimétricos / escenarios Montecarlo reales** → `MVP`.
- ❌ **Frontend / workbench** → `evol_1`.
- ❌ **Multi-tenant real con aislamiento** → `evol_2` (aquí solo un tenant de prueba).
- ❌ **Monitoreo / drift / disparo de re-torneo** → `evol_3`.
- ❌ **Despliegue a producción** → fuera de alcance del proyecto por ahora.

## 5. Pendientes heredados que se resuelven en esta iteración

**N/A — primera iteración, sin pendientes heredados.**

## 6. Pendientes que esta iteración deja para futuras

| ID | Pendiente | Iteración destino sugerida | Razón de posponerlo |
|---|---|---|---|
| `P-tracer-01` | La censura inyectada por el generador **no se corrige** (se entrena con ventas censuradas). | `stab_1` | El tracer solo prueba el hilo; la des-censura es la sustancia siguiente. |
| `P-tracer-02` | El pronóstico es **naïve** sin línea base competitiva ni selección de modelo. | `stab_2` | El torneo es la sustancia que sigue a la des-censura. |
| `P-tracer-03` | Etapa 9 (Montecarlo) queda como **passthrough/banda trivial**, no escenarios reales. | `MVP` | Los escenarios cobran sentido con la decisión newsvendor. |
| `P-tracer-04` | Etapa 11 (Monitoreo) solo **registra** la corrida; sin drift ni alertas. | `evol_3` | El monitoreo operativo es etapa tardía del roadmap. |
| `P-tracer-05` | Un único tenant de prueba **sin aislamiento garantizado**. | `evol_2` | Multi-tenancy real es evolución, no estabilización. |

## 7. Perillas del generador sintético

| Perilla | Valor | Comentario |
|---|---|---|
| `n_familias` | 1 | Caso degenerado del eje producto. |
| `n_categorias` | 1 | |
| `n_subcategorias` | 1 | |
| `n_skus` | 1 | Una sola serie de producto. |
| `n_regiones` | 1 | Caso degenerado del eje geo. |
| `n_paises` | 1 | |
| `n_ciudades` | 1 | |
| `n_sedes` | 1 | Una sola sede. |
| `tasa_de_quiebres` | > 0 (p. ej. 0.1) | Censura inyectada **pero no corregida** en esta iteración (ver `P-tracer-01`). |
| `n_periodos` | suficiente para entrenar+pronosticar (p. ej. 36 periodos) | Se afina en la `spec.md`. |
| `semilla` | fija | Reproducibilidad de la corrida. |

## 8. Entregable / salida esperada

- **Formato:** CSV/JSON + reporte autogenerado en Markdown/HTML (D-017). Consola **solo** para logs/resumen de ejecución, nunca como entregable.
- **Ubicación:** `1000_Project/data/tenants/<tenant>/runs/<run_id>/10_client_report/` (más artefactos intermedios de cada capa medallón en sus carpetas del `run_id`).
- **Contenido mínimo del entregable:**
  - Pronóstico naïve de demanda para la serie (Producto, Sede, Periodos futuros).
  - Reporte legible para el cliente ABC con el pronóstico y un resumen mínimo.
  - Metadatos de la corrida (`run_id`, perillas usadas, modelo = naïve).

## 9. Criterios de aceptación (alto nivel)

> Semilla de los criterios detallados de la `spec.md` y de la checklist de Validation (fase 5).

- [ ] Con **un solo comando**, el científico de datos lanza una corrida de punta a punta que **termina sin error**.
- [ ] La corrida **atraviesa las 11 etapas** y cada una produce su artefacto real (no costuras vacías).
- [ ] Quedan materializadas las **tres capas medallón** (bronze, silver, gold) como archivos del `run_id`.
- [ ] Se genera el **entregable de la etapa 10** (CSV/JSON + reporte MD/HTML) en la ubicación esperada.
- [ ] El entregable contiene un **pronóstico naïve** para la única serie y los metadatos de la corrida.
- [ ] La corrida es **reproducible** (misma semilla → mismos resultados).

## 10. Supuestos y riesgos

- **Supuestos:**
  - El stack confirmado (D-019: Python 3.12 + `uv` + pandas + Pydantic/Pandera + Typer + Jinja2 + pytest + ruff) está disponible. **Nixtla no se usa aún** (entra en `stab_2`).
  - El grano `Demanda(Producto, Sede, Periodo)` se respeta desde el día 1; "1 SKU × 1 sede" es el caso degenerado, no un caso especial.
- **Riesgos:**
  - *Costuras vacías* (L-006): que una etapa "exista" sin hacer nada real → mitigación: cada etapa entrega un artefacto verificable (criterio de aceptación).
  - *Sobre-ingeniería del tracer* → mitigación: todo al mínimo viable; lo demás explícitamente fuera de alcance (§4).
  - *Acoplar el pipeline al caso 1×1* → mitigación: construir sobre el grano atómico para que escalar perillas no obligue a reescribir (principio de diseño del roadmap).

## 11. Decisiones relevantes

- **D-012** — el alcance lo aprueba el humano antes de la spec (este documento).
- **D-013** — dos ejes independientes (sustancia × forma); aquí: naïve × 1×1.
- **D-014** — generador sintético parametrizable, se construye una sola vez aquí.
- **D-016** — grano desde el día 1; 1 SKU × 1 sede = caso degenerado.
- **D-017** — reglas de salida (CSV/JSON + reporte; sin frontend).
- **D-018** — secuencia de iteraciones; este es el #0.
- **D-019** — stack técnico (Nixtla diferido a `stab_2`).
- **D-020 a D-024** — arquitectura del producto (capas, patrones, orquestador propio, layout `1000_Project/`, medallón bronze/silver/gold).
- Decisiones nuevas a registrar: **ninguna** prevista en el alcance (las concretas de implementación se decidirán en la `plan.md`).

## 12. Aprobación

> El alcance NO pasa a Specification hasta que el humano lo apruebe.

- [x] Revisado y **aprobado** por Triple S (jdrodriguez1000) el 2026-06-26.
- Notas de la revisión: aprobado sin cambios. Habilita la Fase 1 (Specification) del tracer_bullet.
