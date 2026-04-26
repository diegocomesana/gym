# Conocimiento deportivo (personal)

Repo para **guardar lo que vas aprendiendo** y **atarlo a lo que realmente hacés**, para que las ideas no queden sueltas y puedas ver sentido con el tiempo. No está pensado como bitácora ni log de entrenamientos.

## Para qué es este repo (y el agente en Cursor)

1. **Capacitación:** preguntas rápidas sobre preparación física y entrenamiento — respuestas **cortas y al pie** primero; **más detalle** si lo pedís. **Por defecto** el agente responde **sin** colgar todo de tu entrenamiento: primero el concepto claro; **si pedís**, relaciona con `contexto.md` y lo que hacés.
2. **Plan vivo:** ir **armando y mejorando** el entrenamiento y **afinarlo según resultados** cuando **lo pidas** (energía, estancamiento, lesiones, etc.).
3. **Docs técnicos:** las preguntas **técnicas** se vuelcan en [`docs/`](./docs/README.md) **por temas**, ordenadas y coherentes con lo ya escrito (no un solo archivo mezclado).

La misma idea para el asistente queda en [`.cursor/rules/gym-repo-y-agente.mdc`](.cursor/rules/gym-repo-y-agente.mdc) (`alwaysApply`).

## Cómo usarlo

1. **`docs/`** — Conceptos y referencias **por carpeta/tema** (fisiología, metodología, métricas, planes tipo). Es la “biblioteca” técnica del repo.
2. **`contexto.md`** — Tu situación: qué hacés, ritmo, salud, objetivos. Para **relacionar** lo aprendido con tu vida cuando **vos** decidís hacer ese puente (o cuando se lo pedís al agente).
3. **Relación personal** — Podés anotar en `contexto.md` cómo un doc de `docs/` aplica a vos; los docs en sí se mantienen **lo más generales posible**.

## Estructura

```
docs/             # documentación técnica por tema (ver docs/README.md)
contexto.md       # tu marco personal y enlaces a temas que te interesan
```

## Convenciones sugeridas (todas opcionales)

- Nuevos temas técnicos → nuevo archivo bajo la carpeta de `docs/` que corresponda.  
- En `contexto.md`, viñetas que apunten a rutas: [`docs/fisiologia-y-energia/...`](./docs/fisiologia-y-energia/energia-aerobico-umbrales-lactato.md).  
- Si dos docs se relacionan, enlace relativo entre ellos.

---

Si una convención te frena, ignorala. El objetivo es que acumular conocimiento te organice la cabeza, no que mantengas un diario.
