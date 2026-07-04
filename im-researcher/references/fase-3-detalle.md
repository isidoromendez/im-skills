# Fase 3 — Análisis Profundo

Leer este archivo solo cuando `fase_actual == "analisis_profundo"`.
Requiere como insumo `fuentes_investigacion.md` (entregable de la Fase 2) y
`alcance_requerimientos.md` (Fase 1) — leerlos antes de empezar.

## Objetivo

Leer las fuentes validadas en la Fase 2, extraer toda la información
relevante para el proyecto (según los requerimientos de la Fase 1), y
organizarla en una wiki de trabajo.

## Flujo

1. Recorrer las fuentes listadas en `fuentes_investigacion.md`, agrupadas
   preferentemente por pregunta/tema de investigación de la Fase 1 (no
   necesariamente en el mismo orden en que aparecen en el documento de
   fuentes).
2. Para fuentes en otro idioma, extraer y volcar la información relevante en
   el idioma de trabajo del usuario (salvo que pida lo contrario), pero
   preservando términos técnicos originales cuando aporte precisión.
3. Cada hallazgo debe quedar trazable a su fuente de origen (cita del título/
   autor tal como figura en `fuentes_investigacion.md`).
4. Si alguna fuente resulta no aprovechable al leerla (contenido irrelevante,
   inaccesible, redundante), registrarlo en la sección correspondiente de la
   wiki en lugar de omitirlo silenciosamente — esto evita que la Fase 4 la
   busque de nuevo.
5. Iterar la wiki con el usuario: puede pedir profundizar en un tema, revisar
   una interpretación, o incorporar fuentes adicionales que hayan surgido.

## Entregable de la fase

`wiki_investigacion`, guardado en
`/research-lc-workflow/{nombre_proyecto}/iter-{n}/wiki_investigacion.md` (o,
si el volumen lo justifica, varios archivos `wiki_investigacion/<tema>.md`
dentro de una subcarpeta de `iter-{n}/` — en ese caso, mantener también un
índice en `wiki_investigacion.md`), usando como base
`assets/wiki_investigacion_template.md`: hallazgos organizados por pregunta/
tema, con su fuente, y una síntesis breve por tema.

Este documento es el insumo principal de la Fase 4.

Si `iteracion_global > 0`, la wiki de esta iteración puede referenciar
puntualmente hallazgos ya consolidados en `iter-{N-1}/wiki_investigacion.md`
cuando sigan siendo válidos, en lugar de reprocesar fuentes ya cubiertas — pero
el foco de esta fase es la información nueva o profundizada que motivó esta
vuelta (ver `alcance_requerimientos.md` de esta iteración).

## Antes de solicitar el gate

Confirmar con el usuario que la wiki cubre todas las preguntas de
investigación de la Fase 1 con evidencia suficiente, y que no hay vacíos
importantes sin señalar.
