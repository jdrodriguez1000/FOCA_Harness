# 🗺️ ROADMAP de iteraciones — FOCA_Harness

> Define el **alcance de cada iteración** en términos de usuario. Dos ejes de crecimiento independientes: **sustancia del pipeline** (qué tan capaz es) × **forma de los datos** (cuántos SKU y cuánta profundidad geográfica). Sesión S3 (2026-06-26).

---

## 🧭 Índice

- [1. Usuario y destinatario](#1-usuario-y-destinatario)
- [2. Los dos ejes de crecimiento](#2-los-dos-ejes-de-crecimiento)
- [3. Roadmap consolidado](#3-roadmap-consolidado)
- [4. Alcance por iteración (términos de usuario)](#4-alcance-por-iteración-términos-de-usuario)
- [5. Principios de diseño](#5-principios-de-diseño)
- [6. Reglas de salida](#6-reglas-de-salida)

---

## 1. Usuario y destinatario

- **Usuario de la plataforma:** el **científico de datos de Triple S** (el operador). Es quien lanza y supervisa el proceso.
- **Destinatario del resultado:** el **cliente ABC** (empresa manufacturera). Recibe el entregable final, **no** opera el software (modelo *Service as a Software*).

## 2. Los dos ejes de crecimiento

1. **Sustancia del pipeline:** qué tan capaz es el proceso (naïve → des-censura → torneo de modelos → reconciliación → decisión bajo costos asimétricos → operación). Crece la *capacidad*.
2. **Forma de los datos:** cuántos SKU y cuánta profundidad geográfica maneja (1 SKU × 1 sede → varios SKU → jerarquía de producto → jerarquía geográfica). Crece la *escala/estructura*.

Son **perillas independientes**: el pipeline se construye una vez para el grano atómico y los datos se escalan subiendo perillas del generador sintético.

## 3. Roadmap consolidado

| # | Iteración | Qué construimos (usuario) | Producto | Geografía |
|---|---|---|---|---|
| 0 | `tracer_bullet` | "Corro el proceso completo de punta a punta." (naïve) | 1 SKU | 1 sede |
| 1 | `stab_1` | "Predigo demanda real, no ventas censuradas." (des-censura) | 1 SKU | 1 sede |
| 2 | `stab_2` | "Lanzo un **torneo de campeones** sobre una serie y el sistema corona al mejor modelo." | 1 SKU | 1 sede |
| 3 | `stab_3` | "Escalo el torneo a **muchas series** independientes." | varios SKU planos | 1 sede |
| 4 | `stab_4` | "Mis pronósticos de producto **cuadran entre niveles** (familia = suma de SKU)." | jerarquía de producto | 1 sede |
| 5 | `stab_5` | "Cuadran también **entre geografías**, reconciliados de forma cruzada." | jerarquía producto | jerarquía geo |
| 6 | `MVP` | "Entrego una **recomendación accionable** bajo costos asimétricos." (newsvendor + escenarios) | grano completo | grano completo |
| 7 | `evol_1` | "Tengo un **workbench** para comparar modelos, revisar pronósticos y lanzar corridas." (primer frontend) | — | — |
| 8 | `evol_2` | "Opero **varios clientes ABC** con datos aislados." (multi-tenant) | — | — |
| 9 | `evol_3` | "Sé cuándo el modelo se **degrada** y vuelvo a correr el torneo." (monitoreo/drift) | — | — |
| 10 | `final` | "Tengo un entorno **robusto y documentado**." | — | — |

> Progresión limpia: primero *qué modelo gana* (1 serie) → *a escala* (muchas series) → *que cuadren* (producto y luego geo) → *que decida* (newsvendor) → *operación* (workbench, multi-tenant, monitoreo).

## 4. Alcance por iteración (términos de usuario)

### `tracer_bullet` — "Hago correr el proceso completo"
Ejecutar todo el flujo de punta a punta sobre datos de prueba con un solo comando y obtener un entregable, aunque el pronóstico (naïve) aún no sea bueno. **Forma:** 1 SKU × 1 sede (una sola serie).

### `stab_1` — "Predigo demanda real, no ventas censuradas"
El sistema detecta periodos con quiebre de stock y reconstruye la demanda, para no entrenar con ventas subestimadas. **Forma:** 1 SKU × 1 sede.

### `stab_2` — "Corono al mejor modelo en una serie"
Montar el mecanismo del torneo: el naïve es la línea base a vencer, entran retadores ML/estadísticos, un *backtesting* evalúa y **corona al campeón**, todo sobre **una sola serie**. **Forma:** 1 SKU × 1 sede.

### `stab_3` — "Escalo el torneo a muchas series"
El mismo torneo, ahora sobre **varios SKU planos** (sin agrupar), corriendo y seleccionando campeón por serie. **Forma:** varios SKU × 1 sede.

### `stab_4` — "Mis números de producto cuadran entre niveles"
Agrupar los SKU en Subcategoría→Categoría→Familia y **reconciliar** el eje de producto (familia = suma de sus SKU). **Forma:** jerarquía de producto × 1 sede.

### `stab_5` — "Cuadran también entre geografías"
Agregar varias sedes → Ciudad→País→Región y **reconciliar de forma cruzada** producto × geografía. **Forma:** jerarquía producto × jerarquía geo.

### `MVP` — "Entrego una recomendación accionable"
Traducir el pronóstico en una **decisión de cuánto producir/stockear** bajo costos asimétricos (quiebre vs sobre-stock), con escenarios Montecarlo. Es el primer entregable con valor de negocio real. **Salida:** reporte de recomendación para el cliente ABC.

### `evol_1` — "Tengo un workbench" (primer frontend)
UI interactiva para el científico de datos: comparar modelos del torneo, revisar pronósticos, lanzar corridas.

### `evol_2` — "Opero varios clientes aislados" (multi-tenant)
Onboarding de múltiples empresas ABC con aislamiento garantizado de datos.

### `evol_3` — "Sé cuándo el modelo se degrada" (monitoreo/drift)
Vigilancia de salud del pronóstico, alertas de drift y disparo de re-torneo / reentrenamiento.

### `final` — "Entorno robusto y documentado"
Robustez, documentación y pulido general. *(Despliegue a producción sigue fuera de alcance.)*

## 5. Principios de diseño

- **Grano desde el día 1:** todo se construye sobre `Demanda(Producto, Sede, Periodo)`. "1 SKU × 1 sede" es el **caso degenerado** (|Producto|=1, |Sede|=1), no un caso especial. Escalar la jerarquía = más filas + metadatos de agrupación + lógica de reconciliación; **no** se reescribe el pipeline.
- **Generador sintético parametrizable** (se construye una sola vez en `tracer_bullet`) con perillas: `n_familias, n_categorias, n_subcategorias, n_skus, n_regiones, n_paises, n_ciudades, n_sedes, tasa_de_quiebres, n_periodos, …`. El tracer las pone en 1; cada stab sube perillas.
- **El torneo conecta con el monitoreo:** cuando `evol_3` detecta drift, se **vuelve a correr el torneo** (`stab_2`/`stab_3`) y puede destronar al campeón.

## 6. Reglas de salida

- **`tracer_bullet` → `MVP`:** datos en **CSV/JSON** + **reporte autogenerado** (Markdown/HTML) como entregable de la etapa 10. La **consola es solo para logs/resumen de ejecución**, nunca el entregable.
- **Primer frontend en `evol_1`** (workbench del científico de datos). Sin UI antes del MVP (mínima complejidad, E4).
- **El cliente ABC nunca recibe una app:** su entregable es un **reporte/archivo** (coherente con *Service as a Software*).
