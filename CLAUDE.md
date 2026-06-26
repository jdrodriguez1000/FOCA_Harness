# CLAUDE.md — Instrucciones para agentes de Claude Code

Este archivo gobierna cómo todo agente de Claude Code debe trabajar en el proyecto **FOCA_Harness**. Léelo completo antes de hacer cualquier cosa.

---

## 🧠 PROTOCOLO DE MEMORIA (obligatorio)

El proyecto tiene una **memoria persistente** en `800_persistence/`. Es la fuente de verdad sobre el estado del proyecto.

### Al INICIAR cada sesión — LEER siempre:
1. `800_persistence/progress.md` — en qué punto estamos y lo hecho hasta ahora.
2. `800_persistence/tasks.md` — tareas realizadas, en progreso y próximas (con estado).
3. `800_persistence/decisions.md` — decisiones tomadas (no las contradigas sin justificar).
4. `800_persistence/lessons.md` — lecciones aprendidas (no repitas errores ya documentados).

> Cada archivo tiene un **índice** al inicio. Úsalo para saltar a lo que necesitas en vez de leer todo el archivo completo.

### Al TERMINAR cada sesión — ACTUALIZAR siempre:
- **`progress.md`**: actualiza la fase/punto actual y añade una fila a la **bitácora de sesiones**.
- **`tasks.md`**: cambia el estado de las tareas tocadas (`✅ Completada` · `🏗️ En progreso` · `🔜 Pendiente` · `⛔ Bloqueada` · `❌ Descartada`); agrega nuevas con su `T-###`.
- **`decisions.md`**: registra toda decisión nueva (estilo ADR ligero `D-###`). No borres decisiones; si una se revierte, márcala `🔄 Reemplazada` y enlaza la nueva.
- **`lessons.md`**: añade lecciones (`L-###`) cuando algo enseñe algo útil.

**Regla de oro:** si trabajaste, la memoria DEBE reflejarlo. Una sesión sin actualizar la memoria es una sesión incompleta.

---

## 📌 Contexto del proyecto (resumen)

- **Negocio:** Triple S — modelo **Service as a Software**. Entregamos el *resultado* (pronóstico de demanda + recomendación), no el software.
- **Usuarios de la plataforma:** científicos de datos de Triple S (NO los clientes ABC).
- **Objetivo:** predecir la **DEMANDA** (no las ventas) de empresas manufactureras ABC, bajo **costos asimétricos** (quiebre vs sobre-stock).
- **Multi-tenant:** múltiples empresas ABC con datos aislados.
- **Dos jerarquías cruzadas:** Producto (`Familia→Categoría→Subcategoría→Producto`) y Cliente/Geo (`Región→País→Ciudad→Sede`). Grano: `Demanda(Producto, Sede, Periodo)`.

> Visión completa: `970_documents/idea.md`.

---

## 🔧 Marco de trabajo

### Iteraciones (alcance, de menos a más)
```
tracer_bullet → stab_1, stab_2… → MVP → evol_1, evol_2… → final
```
Nunca se salta de `tracer_bullet` a `MVP` directo: se sube por iteraciones de estabilización `stab_*`. Entre MVP y final van iteraciones de evolución `evol_*`.

### Harness (las 5 fases que pasa CADA iteración, en orden)
1. **Specification** — qué debe lograr + criterios de aceptación.
2. **Planning** — cómo construirlo (tareas, riesgos).
3. **Execution** — construir.
4. **Verification** — **testing**: que lo construido funcione correctamente.
5. **Validation** — conformidad con la spec: que sea **exactamente** lo pactado (sin scope creep ni gaps).

**No se codea antes de tener Specification y Planning.** Cada iteración deja su rastro en `iterations/<nn>_<nombre>/` con una subcarpeta por fase del harness.

### Pipeline funcional de 11 etapas
Construcción: 1) Onboarding · 2) Carga · 3) Salud de datos · 4) Limpieza · 5) EDA · 6) Ingeniería de características · 7) Entrenamiento/modelado.
Operación: 8) Inferencia · 9) Montecarlo · 10) Información al cliente · 11) Monitoreo.
> Paso a producción: **fuera de alcance** por ahora.

---

## 🗂️ Estructura del repositorio

```
FOCA_Harness/
├── CLAUDE.md               # este archivo
├── 800_persistence/        # memoria del proyecto (LEER/ACTUALIZAR siempre)
│   ├── progress.md
│   ├── tasks.md
│   ├── lessons.md
│   └── decisions.md
├── 970_documents/          # documentos de visión y referencia
│   └── idea.md
└── iterations/             # una carpeta por iteración (se crea al iniciar cada una)
    └── <nn>_<nombre>/
        ├── 1_specification/
        ├── 2_planning/
        ├── 3_execution/
        ├── 4_verification/
        └── 5_validation/
```

---

## ✍️ Convenciones

- **Idioma:** español para documentación y comunicación.
- **No contradigas** decisiones vigentes en `decisions.md` sin discutirlo y registrarlo.
- **Respeta el alcance** de cada iteración: ni de más (scope creep) ni de menos (gaps) — eso es lo que valida la fase 5.
- Mantén los **índices** de los archivos de `800_persistence/` actualizados si agregas secciones.
