# 💡 IDEA — Visión general del proyecto FOCA_Harness

> Documento de visión. Reúne la idea general de qué queremos construir y por qué. Es el "norte" del proyecto; los detalles de ejecución viven en las specs de cada iteración y en `800_persistence/`.

---

## 🧭 Índice

- [1. El problema](#1-el-problema)
- [2. La solución](#2-la-solución)
- [3. El modelo de negocio: Service as a Software](#3-el-modelo-de-negocio-service-as-a-software)
- [4. Quién usa la plataforma](#4-quién-usa-la-plataforma)
- [5. Conceptos clave del dominio](#5-conceptos-clave-del-dominio)
- [6. Las dos jerarquías](#6-las-dos-jerarquías)
- [7. El pipeline de 11 etapas](#7-el-pipeline-de-11-etapas)
- [8. Marco de construcción: iteraciones + harness](#8-marco-de-construcción-iteraciones--harness)
- [9. Qué está fuera de alcance (por ahora)](#9-qué-está-fuera-de-alcance-por-ahora)

---

## 1. El problema

Las empresas manufactureras B2B (las llamamos **empresas ABC**) sufren **volatilidad impredecible en los pedidos** de sus clientes. Esto genera dos costos asimétricos pero igualmente dañinos:

- **Sobre-producción → sobre-inventario:** capital inmovilizado, costos de almacenamiento, obsolescencia, mermas.
- **Sub-producción → quiebres de stock:** ventas perdidas, penalizaciones, erosión de confianza del cliente, urgencias internas (horas extra, fletes express), fricción con proveedores, clientes y áreas internas.

El costo de equivocarse es **asimétrico**: fallar por exceso no cuesta lo mismo que fallar por defecto.

## 2. La solución

Una plataforma que **predice la DEMANDA** de los productos de cada empresa ABC y entrega una **recomendación accionable** de cuánto producir / qué stock de seguridad mantener, considerando:

- **Bandas de incertidumbre** (no un número puntual, sino un rango con probabilidad).
- **Costos asimétricos** (decisión tipo *newsvendor*: sesgar la decisión según cuánto cuesta cada tipo de error).
- **Pronóstico jerárquico** coherente entre niveles (reconciliación).

> **Clave del enfoque:** predecimos **demanda**, NO **ventas**. Las ventas están "censuradas": cuando hubo quiebre de stock, lo vendido es menor que lo que el cliente realmente quería. Entrenar con ventas perpetúa los quiebres; por eso hay que detectar y reconstruir la demanda real.

## 3. El modelo de negocio: Service as a Software

Triple S **no entrega un software** al cliente para que lo opere. Triple S entrega el **resultado** como un servicio. El software y los agentes de IA son el **motor interno** que hace posible escalar y automatizar ese servicio.

El cliente deposita su confianza en Triple S de punta a punta, y Triple S se hace responsable de **todo el ciclo**: ingesta de datos, procesamiento, pronóstico y entrega de resultados.

## 4. Quién usa la plataforma

Los **científicos de datos de Triple S** — no los clientes ABC. La plataforma es una herramienta interna **multi-tenant**: soporta múltiples empresas ABC simultáneamente, con sus datos aislados.

## 5. Conceptos clave del dominio

- **Empresa ABC:** el cliente de Triple S. Una manufacturera que produce productos PRD1…PRDn.
- **Empresa XYZ:** los clientes de la empresa ABC (quienes le compran). Están estructurados geográficamente.
- **Demanda:** lo que los clientes XYZ realmente querían pedir. **Es lo que predecimos.**
- **Venta:** lo efectivamente despachado/facturado (puede estar censurado por quiebres).
- **Costos asimétricos:** quiebre de stock vs sobre-inventario tienen costos distintos.

## 6. Las dos jerarquías

**Eje PRODUCTO** (qué se pide):
```
Familia → Categoría → Subcategoría → Producto (PRD1, PRD2, …)
```

**Eje CLIENTE/GEOGRAFÍA** (quién pide y dónde — es la estructura de los clientes XYZ):
```
Región → País → Ciudad → Sede
```

**Grano atómico de la demanda:**
```
Demanda( Producto , Sede , Periodo ) = cantidad pedida
```

Desde el grano fino se agrega hacia arriba por cualquiera de los dos ejes. Esto es una *grouped/hierarchical time series* que requiere **reconciliación** para que todos los niveles cuadren.

## 7. El pipeline de 11 etapas

**Fase de construcción (one-time por cliente):**
1. **Onboarding** del cliente
2. **Carga** de datos del cliente
3. **Salud** de los datos
4. **Limpieza** de datos
5. **EDA** (análisis exploratorio)
6. **Ingeniería de características**
7. **Entrenamiento y modelado**

**Fase operativa (recurrente):**
8. **Inferencia** con datos nuevos
9. **Simulación Montecarlo** (especificación de escenarios → decisión bajo costos asimétricos)
10. **Información al cliente** (entrega del resultado)
11. **Monitoreo** (puede derivar en reentrenamiento o cambio de modelo)

## 8. Marco de construcción: iteraciones + harness

**Iteraciones (alcance, de menos a más):**
```
tracer_bullet → stab_1, stab_2… → MVP → evol_1, evol_2… → final
```
- **tracer_bullet:** probar que todo el cableado funciona end-to-end.
- **stab_\*:** estabilización incremental hasta el MVP (nunca se salta directo).
- **MVP:** lo mínimo que genera valor validable por el cliente.
- **evol_\*:** evolución con feedback del cliente.
- **final:** versión con las adiciones/cambios solicitados.

**Harness (las 5 fases que pasa CADA iteración):**
1. **Specification** — qué debe lograr + criterios de aceptación.
2. **Planning** — cómo construirlo (tareas, riesgos).
3. **Execution** — construir.
4. **Verification** — testing: que funcione correctamente.
5. **Validation** — conformidad con la spec: que sea exactamente lo pactado.

## 9. Qué está fuera de alcance (por ahora)

- Paso a producción / despliegue.
- UI self-service para los clientes ABC (el modelo SaaSw no la requiere como prioridad).
