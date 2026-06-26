---
description: Protocolo de inicio de sesión. Lee la memoria del proyecto (800_persistence) priorizando progreso y próximas tareas, y propone el siguiente paso.
allowed-tools: Read, Bash(git status), Bash(git log:*)
---

# /foca-next — Inicio de sesión: cargar contexto y proponer el siguiente paso

Eres un agente que **acaba de entrar al proyecto FOCA_Harness** y necesitas ponerte al día rápido. Sigue el protocolo de memoria definido en `CLAUDE.md`. **Usa los índices** de cada archivo para ir directo a lo que importa; NO leas archivos completos si el índice te lleva a la sección puntual.

## Paso 1 — Lo IMPRESCINDIBLE (leer siempre)

Lee, en este orden, priorizando estas secciones vía su índice:

1. **`800_persistence/progress.md`**
   - "¿En qué punto estamos?" — la fase actual.
   - "Hecho hasta el momento" — lo ya completado.
   - "Próximo hito" — hacia dónde vamos.
   - Última fila de la "Bitácora de sesiones".
2. **`800_persistence/tasks.md`**
   - "Tareas en progreso".
   - "Próximas tareas".
   > Estas dos cosas — **progreso** y **próximas tareas** — son lo más importante para arrancar.

## Paso 2 — A DEMANDA (NO leer ahora salvo que sea necesario)

No leas estos archivos completos al iniciar. Conoce solo su índice y consúltalos **solo cuando** la tarea concreta lo requiera:

- **`800_persistence/decisions.md`** — consúltalo antes de tomar o cambiar una decisión de diseño, para no contradecir una decisión vigente.
- **`800_persistence/lessons.md`** — consúltalo si vas a abordar algo donde ya pudo haber un aprendizaje (para no repetir errores).

## Paso 3 — Contexto de git (opcional, rápido)

```bash
git status
git log --oneline -5
```
Para saber si hay cambios sin commitear de una sesión anterior.

## Paso 4 — Reportar y proponer

Entrega al usuario un resumen breve:
- **Dónde estamos:** fase actual + iteración + fase del harness en curso.
- **Qué sigue:** las próximas 1–3 tareas (`T-###`) con su estado.
- **Propuesta concreta** del siguiente paso a ejecutar en esta sesión.
- Cualquier bloqueo o decisión pendiente que detectes (p. ej. confirmaciones del usuario).

Luego espera la confirmación del usuario antes de ejecutar.

> Recuerda el marco: cada iteración (`tracer_bullet → stab_* → MVP → evol_* → final`) pasa por las 5 fases del harness (Specification → Planning → Execution → Verification → Validation). No se codea sin Specification y Planning.
