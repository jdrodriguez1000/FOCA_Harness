---
name: review-spec
description: Checklist de revisión para un spec.md (artefacto del spec-writer, Fase 1 del harness). La invoca el agente reviewer para auditar completitud, realización del comportamiento, conformidad con la arquitectura del producto, ausencia de scope creep/gaps, validez de contratos de datos, verificabilidad de los criterios y trazabilidad contra behavior.md y arquitectura.md. Devuelve hallazgos estructurados.
user-invocable: false
allowed-tools: Read, Glob, Grep
---

# Checklist — Revisión de `spec.md`

Auditas un `spec.md` contra sus dos **fuentes de verdad**: el `behavior.md` aprobado (qué debe realizar) y `970_documents/arquitectura.md` (a qué debe conformar). Solo **lees**; no editas el documento. Tu salida alimenta el `review_spec.json` del reviewer.

## Insumos
1. El `spec.md` a revisar (path dado por el reviewer).
2. El `behavior.md` del que deriva (misma carpeta `1_specification/`). **Obligatorio.**
3. `970_documents/arquitectura.md` (stack, patrones, capas, medallón, layout). **Obligatorio.**

Lee los tres antes de juzgar.

## Dimensiones de revisión

Evalúa cada una `pass` / `warn` / `fail`. Un `fail` es **bloqueante** (verdict = `changes_requested`).

| # | Dimensión | Qué verificar | Falla si… |
|---|---|---|---|
| D1 | **Completitud estructural** | Están todas las secciones (Objetivo, Alcance, Realización, Contratos de datos, Interfaces, Costuras vs rebanada, No funcionales, Criterios, Trazabilidad, Riesgos) y el índice. | Falta una sección o el índice. |
| D2 | **Realización del comportamiento** | **Cada** escenario del `behavior.md` tiene componente(s)/etapa(s) que lo realizan y un artefacto observable (§3 + §9 de la spec). | Algún escenario sin realización técnica (gap). |
| D3 | **Conformidad con arquitectura** | Stack, patrones, capas, layout y medallón coinciden con `arquitectura.md`; no inventa herramientas/estructuras ni contradice decisiones vigentes. | Introduce stack/patrón/estructura ajeno o contradice la arquitectura. |
| D4 | **Sin scope creep** | No especifica capacidades fuera del `behavior.md`/scope; lo diferido queda como **stub/costura vacía**, no implementado. | Especifica lógica de una capacidad diferida (creep). |
| D5 | **Sin gaps** | Todo el comportamiento y cada criterio del scope quedan cubiertos por algún criterio técnico (§8). | Falta cubrir comportamiento o un criterio de aceptación. |
| D6 | **Contratos de datos válidos** | Las capas medallón tocadas están **al grano `Demanda(Producto, Sede, Periodo)`**, con esquema Pandera (laxo/estricto correcto) y path del layout. | Contrato fuera de grano, sin esquema, o path inconsistente con el layout. |
| D7 | **Interfaces conformes** | Contrato de etapa (`Stage`/`RunContext`), ports y orden del orquestador son coherentes con `arquitectura.md §3–§5`. | Interfaz inventada o incompatible con los patrones definidos. |
| D8 | **Costuras vs rebanada explícita (L-006)** | La spec distingue claramente qué se **rellena** de qué queda **stub**; coherente con `arquitectura.md §7` si aplica. | No distingue, o pretende rellenar una costura que debe quedar vacía (o viceversa). |
| D9 | **Criterios verificables** | Cada criterio de aceptación técnico (§8) es comprobable por un test concreto (unit/integración/e2e), no subjetivo. | Criterio no verificable o sin forma de comprobarse. |
| D10 | **No funcionales** | Reproducibilidad (semilla), determinismo, idempotencia y logging están especificados cuando el comportamiento los exige. | Falta un no-funcional exigido por el comportamiento. |
| D11 | **Trazabilidad correcta** | La tabla §9 mapea escenarios↔criterios↔componentes sin huérfanos ni referencias inventadas. | Trazabilidad incompleta o incorrecta. |
| D12 | **Nivel de abstracción** | Especifica **contratos/interfaces**, no algoritmos/implementación detallada (eso es del `plan.md`). | Cierra decisiones de implementación que deben ir al plan, o queda tan vago que no es accionable. |

## Procedimiento
1. Lee `behavior.md` (escenarios + frontera) y `arquitectura.md` (stack, patrones, capas, medallón, layout, §7).
2. Lee `spec.md` y mapea cada escenario a su realización y cada elemento de diseño a su origen en la arquitectura.
3. Recorre D1–D12. Por cada hallazgo: dimensión, severidad (`blocker` si la dimensión falla, `minor` si es warn), **ubicación precisa** (sección/tabla/fila), problema y **corrección sugerida accionable**.
4. Veredicto: `changes_requested` si hay ≥1 `fail`; si todo es `pass`/`warn`, `approved`.

## Salida (qué devuelves al reviewer)
Mismo formato estructurado que vuelca el reviewer a `review_spec.json`:

```json
{
  "verdict": "approved | changes_requested",
  "dimensions": [
    {"id": "D3", "name": "Conformidad con arquitectura", "status": "pass|warn|fail", "notes": "…"}
  ],
  "findings": [
    {"id": "F1", "dimension": "D6", "severity": "blocker|minor",
     "location": "§4 contrato silver",
     "issue": "El esquema silver no está al grano Demanda(Producto, Sede, Periodo).",
     "suggested_fix": "Definir las columnas de grano y el esquema Pandera estricto correspondiente."}
  ],
  "summary": "1–2 frases con el balance de la revisión."
}
```

No apruebes por defecto: la conformidad con la arquitectura y la verificabilidad son innegociables. Ante la duda, exige la evidencia faltante (E3).
