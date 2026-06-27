---
name: review-behavior
description: Checklist de revisión para un behavior.md (artefacto del bdd-writer, Fase 1 del harness). La invoca el agente reviewer para auditar completitud, ausencia de vacíos/costuras vacías, ausencia de ambigüedad, fidelidad al alcance (sin scope creep ni gaps), términos de usuario y trazabilidad contra el scope_<iter>.md. Devuelve hallazgos estructurados.
user-invocable: false
allowed-tools: Read, Glob, Grep
---

# Checklist — Revisión de `behavior.md`

Auditas un `behavior.md` contra su **fuente de verdad**: el `scope_<iter>.md` aprobado. Solo **lees**; no editas el documento. Tu salida alimenta el `review_behavior.json` del reviewer.

## Insumos
1. El `behavior.md` a revisar (path dado por el reviewer).
2. El `scope_<iter>.md` del que deriva (`900_scope/scope_<iter>.md`). **Obligatorio**: sin él no hay vara de medición.

Lee ambos completos antes de juzgar (son cortos).

## Dimensiones de revisión

Evalúa cada una como `pass` / `warn` / `fail`. Un `fail` es **bloqueante** (verdict = `changes_requested`); un `warn` no bloquea pero se reporta.

| # | Dimensión | Qué verificar | Falla si… |
|---|---|---|---|
| D1 | **Completitud estructural** | Están todas las secciones (Metadatos, Resumen, Actores, Escenarios, Frontera, Trazabilidad, Ambigüedades) y el índice. | Falta una sección o el índice. |
| D2 | **Cobertura de criterios** | **Cada** criterio de aceptación del scope (§9) mapea a ≥1 escenario en la tabla de trazabilidad (§5). | Algún criterio §9 sin escenario (gap). |
| D3 | **Cobertura de Alcance IN** | Cada ítem del §3 del scope (incl. las 11 etapas tocadas) tiene un escenario con resultado observable. | Una etapa/ítem del §3 sin escenario, o con escenario que no afirma resultado. |
| D4 | **Sin scope creep** | Ningún escenario describe algo del §4 *Fuera de alcance* del scope. | Hay un escenario que prueba capacidad diferida (creep). |
| D5 | **Sin costuras vacías (L-006)** | Ningún `Entonces` se limita a "la etapa existe/corre"; todos afirman un **artefacto/valor observable**. | Hay un `Entonces` que solo afirma ejecución, no resultado. |
| D6 | **Términos de usuario** | Ningún escenario nombra clases, funciones, librerías, módulos o rutas de archivos internas. | Hay lenguaje de implementación en un escenario. |
| D7 | **Observabilidad / no ambigüedad** | Cada `Entonces` es comprobable por inspección (archivo, valor, mensaje, conteo). Sin términos vagos no acotados ("bueno", "rápido", "suficiente") salvo que el scope los declare fuera de alcance. | Hay aserción subjetiva o no verificable. |
| D8 | **Forma Gherkin** | Cada escenario tiene Dado/Cuando/Entonces bien formados y un título claro; los `Y` cuelgan de un paso válido. | Escenario mal estructurado o sin acción/resultado. |
| D9 | **Trazabilidad correcta** | La tabla §5 es consistente: cada escenario rastrea a un §3/§9 real del scope; no hay referencias inventadas. | Referencia cruzada incorrecta o escenario huérfano (sin origen en el scope). |
| D10 | **Consistencia interna** | Metadatos coherentes (iteración, conteo de escenarios = nº real), numeración E1..En sin huecos ni duplicados, frontera marcada con §4. | Inconsistencias internas. |

## Procedimiento
1. Lee `scope_<iter>.md` y extrae: ítems del §3, lista de §4, criterios del §9.
2. Lee `behavior.md` y mapea cada escenario a su origen.
3. Recorre D1–D10. Por cada hallazgo registra: dimensión, severidad (`blocker` si la dimensión falla, `minor` si es warn), **ubicación precisa** (p. ej. "Escenario E4", "§5 fila 3", "falta sección 4"), descripción del problema y **corrección sugerida accionable**.
4. Determina el veredicto: `changes_requested` si hay ≥1 `fail`; si todo es `pass`/`warn`, `approved`.

## Salida (qué devuelves al reviewer)
Entrega un bloque estructurado que el reviewer volcará a `review_behavior.json` con esta forma:

```json
{
  "verdict": "approved | changes_requested",
  "dimensions": [
    {"id": "D1", "name": "Completitud estructural", "status": "pass|warn|fail", "notes": "…"}
  ],
  "findings": [
    {"id": "F1", "dimension": "D5", "severity": "blocker|minor",
     "location": "Escenario E7", "issue": "El Entonces solo afirma que la etapa 9 corre, no su artefacto.",
     "suggested_fix": "Afirmar la banda de incertidumbre escrita en el run (resultado observable)."}
  ],
  "summary": "1–2 frases con el balance de la revisión."
}
```

No apruebes por defecto: si dudas entre `warn` y `fail`, exige la evidencia que falta. La lenidad sistémica (E3) es el riesgo a evitar.
