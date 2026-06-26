# 💡 LESSONS — Lecciones aprendidas

> Aprendizajes del proyecto (técnicos, de proceso, de negocio). Sirven para no repetir errores y consolidar buenas prácticas. Todo agente añade lecciones al cerrar sesión cuando corresponda.

---

## 🧭 Índice

- [Cómo registrar una lección](#cómo-registrar-una-lección)
- [Lecciones de negocio/dominio](#lecciones-de-negociodominio)
- [Lecciones técnicas](#lecciones-técnicas)
- [Lecciones de proceso (harness/iteraciones)](#lecciones-de-proceso-harnessiteraciones)

---

## Cómo registrar una lección

Formato sugerido por entrada:
- **`L-###` — Título corto**
  - **Contexto:** qué pasaba.
  - **Lección:** qué aprendimos.
  - **Acción:** qué haremos distinto de aquí en adelante.

---

## Lecciones de negocio/dominio

- **L-001 — Demanda ≠ Ventas.**
  - **Contexto:** las ventas están "censuradas": si hubo quiebre de stock, la venta registrada es menor que la demanda real.
  - **Lección:** entrenar con ventas hace que el modelo aprenda a reproducir los propios quiebres y recomiende producir de menos (círculo vicioso).
  - **Acción:** el objetivo a predecir es la DEMANDA; hay que detectar y reconstruir periodos censurados.

## Lecciones técnicas

- _(sin entradas todavía)_

## Lecciones de proceso (harness/iteraciones)

- **L-002 — Cada iteración es delgada pero completa de punta a punta.**
  - **Contexto:** metodología tracer_bullet → stab_* → MVP.
  - **Lección:** no se salta de tracer_bullet a MVP; se engruesa de menos a más por `stab_*`.
  - **Acción:** cada iteración atraviesa TODO el pipeline (aunque sea mínimo) y pasa las 5 fases del harness.

- **L-003 — Verification ≠ Validation.**
  - **Contexto:** definición del harness por el usuario.
  - **Lección:** **Verification (4)** = testing, que funcione. **Validation (5)** = conformidad con la spec, que sea *exactamente* lo pactado (sin scope creep ni gaps).
  - **Acción:** la Validation se ejecuta como checklist contra la Specification de esa iteración.
