# 🎯 SCOPE — `<iteracion>` (`tracer_bullet` · `stab_n` · `MVP` · `evol_n` · `final`)

> **Plantilla de alcance de una iteración.** Es el insumo **aprobado por el humano** (ver `decisions.md` D-012) que define qué construye esta iteración, **antes** de redactar la `spec.md`. Se deriva de `970_documents/roadmap_iteraciones.md` (fuente de verdad del alcance) y de `970_documents/arquitectura.md`.
>
> **Cómo usarla:** copia este archivo a `900_scope/scope_<iteracion>.md`, rellena cada `<placeholder>` y borra las notas en *cursiva*/blockquote. El alcance se expresa **en términos de usuario** ("el científico de datos puede X" / "el cliente ABC recibe Y"), no en tareas técnicas (esas van en la `plan.md`).

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
| **Iteración** | `<iteracion>` |
| **Posición en la secuencia** | `<n>` de la secuencia D-018 |
| **Fecha** | `<YYYY-MM-DD>` |
| **Redactado por** | `<agente / persona>` |
| **Aprobado por (humano)** | `<nombre>` |
| **Estado** | `🕐 Borrador` · `✅ Aprobado` · `🔄 Reemplazado` |

## 1. Capacidad en términos de usuario

> Una frase en primera persona desde la óptica del usuario de la plataforma (el científico de datos de Triple S). Debe coincidir con la fila del roadmap.

**"<Como científico de datos, puedo …>"**

Destinatario del resultado y qué recibe (si aplica): `<el cliente ABC recibe …>`

## 2. Posición en el roadmap (ejes de crecimiento)

> Los dos ejes independientes (D-013): *sustancia del pipeline* × *forma de los datos*.

| Eje | Esta iteración | Iteración anterior | Qué cambia |
|---|---|---|---|
| **Sustancia** (capacidad del pipeline) | `<naïve / des-censura / torneo / reconciliación / newsvendor / operación>` | `<…>` | `<…>` |
| **Forma** (escala/estructura de datos) | `<1 SKU×1 sede / varios SKU / jerarquía producto / jerarquía geo>` | `<…>` | `<…>` |

## 3. Alcance IN — qué SÍ se construye

> Lista de capacidades concretas que entran. Cada una debe ser verificable. Recuerda el principio de costuras (L-006): lo que existe como costura vacía no es alcance "construido".

- [ ] `<capacidad 1>`
- [ ] `<capacidad 2>`
- [ ] `<…>`

**Etapas del pipeline (1–11) tocadas en esta iteración** y cómo:

| Etapa | ¿Se toca? | Qué se hace / capa medallón afectada |
|---|---|---|
| 1 Onboarding | `<sí/no>` | `<…>` |
| 2 Carga | `<sí/no>` | `<… → 🥉 bronze>` |
| 3 Salud | `<sí/no>` | `<…>` |
| 4 Limpieza | `<sí/no>` | `<… → 🥈 silver>` |
| 5 EDA | `<sí/no>` | `<…>` |
| 6 Ingeniería de características | `<sí/no>` | `<… → 🥇 gold>` |
| 7 Entrenamiento/modelado | `<sí/no>` | `<…>` |
| 8 Inferencia | `<sí/no>` | `<…>` |
| 9 Montecarlo | `<sí/no>` | `<…>` |
| 10 Información al cliente | `<sí/no>` | `<…>` |
| 11 Monitoreo | `<sí/no>` | `<…>` |

## 4. Fuera de alcance — qué NO se construye

> Explicitar los **gaps deliberados** evita scope creep (lo valida la fase 5 del harness). Indica a qué iteración futura corresponde cada cosa.

- ❌ `<cosa que NO se hace>` → corresponde a `<iteración futura>`.
- ❌ `<…>` → `<…>`.

## 5. Pendientes heredados que se resuelven en esta iteración

> Puntos que **iteraciones anteriores dejaron pendientes** (en su sección 6) y que **esta iteración cierra**. Trazabilidad: cada uno apunta a la iteración y el ítem de origen.
>
> ⚠️ **No aplica al `tracer_bullet`** (es la primera iteración; no hereda nada). En ese caso, escribir explícitamente: *"N/A — primera iteración, sin pendientes heredados."*

| ID heredado | Origen (iteración · sección 6) | Pendiente | Cómo se resuelve aquí |
|---|---|---|---|
| `<P-origen-##>` | `<stab_x · §6>` | `<descripción>` | `<resolución>` |

## 6. Pendientes que esta iteración deja para futuras

> Lo contrario de la sección 5: deuda/decisiones que **esta** iteración pospone a propósito, para que una futura las recoja en **su** sección 5. Mantiene la cadena de trazabilidad entre iteraciones.

| ID | Pendiente | Iteración destino sugerida | Razón de posponerlo |
|---|---|---|---|
| `<P-<iteracion>-01>` | `<descripción>` | `<stab_x / MVP / evol_x>` | `<…>` |

## 7. Perillas del generador sintético

> Valores concretos del generador parametrizable (D-014) para esta iteración. El `tracer_bullet` las pone casi todas en 1; cada `stab_*` sube las que toque.

| Perilla | Valor | Comentario |
|---|---|---|
| `n_familias` | `<1>` | |
| `n_categorias` | `<1>` | |
| `n_subcategorias` | `<1>` | |
| `n_skus` | `<1>` | |
| `n_regiones` | `<1>` | |
| `n_paises` | `<1>` | |
| `n_ciudades` | `<1>` | |
| `n_sedes` | `<1>` | |
| `tasa_de_quiebres` | `<…>` | censura inyectada |
| `n_periodos` | `<…>` | |
| `<otra…>` | `<…>` | |

## 8. Entregable / salida esperada

> Según las reglas de salida (D-017): de `tracer_bullet` a `MVP` → archivos CSV/JSON + reporte autogenerado (Markdown/HTML); consola solo para logs. Frontend desde `evol_1`. El cliente ABC nunca recibe app.

- **Formato:** `<CSV/JSON + reporte MD/HTML | workbench | …>`
- **Ubicación:** `<1000_Project/data/tenants/<tenant>/runs/<run_id>/10_client_report/ …>`
- **Contenido mínimo del entregable:** `<…>`

## 9. Criterios de aceptación (alto nivel)

> En términos de usuario y verificables. Son la semilla de los criterios detallados de la `spec.md` y de la checklist de Validation (fase 5).

- [ ] `<el usuario puede … y obtiene …>`
- [ ] `<…>`

## 10. Supuestos y riesgos

- **Supuestos:** `<…>`
- **Riesgos:** `<riesgo → mitigación>`

## 11. Decisiones relevantes

> Enlaces a `800_persistence/decisions.md` que condicionan esta iteración (no contradecir sin registrar). Si esta iteración requiere una decisión nueva, anótala aquí como pendiente de registrar.

- `<D-0xx>` — `<por qué aplica>`
- Decisiones nuevas a registrar: `<… o "ninguna">`

## 12. Aprobación

> El alcance NO pasa a Specification hasta que el humano lo apruebe.

- [ ] Revisado y **aprobado** por `<humano>` el `<YYYY-MM-DD>`.
- Notas de la revisión: `<…>`
