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

- **L-004 — Construir para el grano hace barato escalar la jerarquía.**
  - **Contexto:** crecimiento de los datos de "1 SKU × 1 sede" a jerarquías de producto y geografía.
  - **Lección:** si todo se construye sobre el grano `Demanda(Producto, Sede, Periodo)` desde el inicio, "1 SKU × 1 sede" es el caso degenerado (|P|=1, |S|=1). Escalar es más filas + metadatos de agrupación + reconciliación, **no** reescribir el pipeline. Un generador sintético parametrizable (perillas en 1 al inicio) hace que cada iteración solo "suba perillas".
  - **Acción:** respetar el grano desde el `tracer_bullet`; construir el generador con perillas desde el primer momento. Ver [decisions.md D-014].

- **L-006 — Construir las costuras completas, rellenar la rebanada mínima.**
  - **Contexto:** diseño de la arquitectura del producto sabiendo que crecerá en dos ejes (sustancia × forma) a lo largo de muchas iteraciones.
  - **Lección:** la forma de honrar "no reescribir el pipeline" (D-014) sin caer en sobre-ingeniería es separar *estructura* de *contenido*: las **costuras** (contrato `Stage`, Ports&Adapters, las 11 etapas, las capas medallón, el generador con perillas) existen completas desde el `tracer_bullet`; el **contenido** se rellena mínimo (naïve) y crece *dentro* de etapas existentes (torneo→s07, newsvendor→s10) sin tocar el orquestador. Los contratos estables son las capas medallón (bronze/silver/gold) y el `Stage`.
  - **Acción:** en cada iteración, preguntar "¿esto cabe dentro de una costura existente?" antes de crear estructura nueva. Stubs vacíos (tournament, decision) son legítimos en el tracer; lógica de más no. Ver [decisions.md D-020], [D-024] y `970_documents/arquitectura.md`.

## Lecciones de proceso (harness/iteraciones)

- **L-002 — Cada iteración es delgada pero completa de punta a punta.**
  - **Contexto:** metodología tracer_bullet → stab_* → MVP.
  - **Lección:** no se salta de tracer_bullet a MVP; se engruesa de menos a más por `stab_*`.
  - **Acción:** cada iteración atraviesa TODO el pipeline (aunque sea mínimo) y pasa las 5 fases del harness.

- **L-003 — Verification ≠ Validation.**
  - **Contexto:** definición del harness por el usuario.
  - **Lección:** **Verification (4)** = testing, que funcione. **Validation (5)** = conformidad con la spec, que sea *exactamente* lo pactado (sin scope creep ni gaps).
  - **Acción:** la Validation se ejecuta como checklist contra la Specification de esa iteración.

- **L-005 — Definir el alcance en términos de usuario antes de especificar.**
  - **Contexto:** sesión S3 — antes de escribir specs, se definió qué hace cada iteración desde la óptica del usuario.
  - **Lección:** describir el alcance como capacidades de usuario ("puedo hacer X") clarifica el valor de cada iteración, separa la *capacidad* (pipeline) de la *escala* (forma de datos) y evita scope creep. El detalle técnico viene después, en la spec.
  - **Acción:** mantener `970_documents/roadmap_iteraciones.md` como fuente de verdad del alcance; cada spec de iteración deriva de él.
