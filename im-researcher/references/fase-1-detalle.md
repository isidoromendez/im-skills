# Fase 1 — Mapeo y Alcance

Leer este archivo solo cuando `fase_actual == "mapeo_y_alcance"`.

## Objetivo

Partir de una idea (a veces muy poco desarrollada, a veces ya refinada por una
iteración global previa) y, con un ciclo extenso de preguntas de alto valor,
llegar junto con el usuario a un documento de requerimientos claro. En
`iter-0` esta fase también es responsable de fijar el nombre del proyecto (ver
directiva 1 en `SKILL.md`).

## Paso 0 — Nombre de proyecto (bloqueante, solo en `iter-0`)

Si `iteracion_global == 0` y todavía no está definido, es la primera pregunta
de esta fase. Sin nombre de proyecto no se crea ningún archivo. En
iteraciones globales posteriores (`iter-N` con `N>0`) el nombre ya existe —
no volver a preguntarlo.

## Paso 0bis — Si `iteracion_global > 0`: leer la síntesis anterior primero

Antes de hacer cualquier pregunta, leer con `filesystem:read_text_file` el
archivo indicado en `entrada_iteracion_anterior` del
`estado_investigacion.json` de esta iteración (la `sintesis_final.md` de
`iter-{N-1}`). Esa lectura es obligatoria y reemplaza la necesidad de repasar
todo el historial de conversación de la iteración anterior.

Con esa síntesis en mano, las preguntas de esta fase deben orientarse a:
- Qué de la síntesis anterior el usuario considera resuelto/cerrado (no hace
  falta seguir investigando sobre eso).
- Qué quiere profundizar, expandir, actualizar o corregir en esta nueva
  vuelta.
- Si aparecen preguntas de investigación nuevas que no estaban en el alcance
  original.
- Si cambia algo del alcance u restricciones definidas en `iter-0` (idioma,
  tipo de fuentes, criterios de éxito), o se mantienen igual.

El documento de alcance de esta iteración (`alcance_requerimientos.md`) debe
dejar explícito, en una sección propia, qué toma de la síntesis anterior como
punto de partida y qué es nuevo en esta vuelta.

## Guía general para las preguntas de alto valor (aplica siempre, con o sin
iteración anterior)

- Priorizar preguntas que más reducen la ambigüedad del proyecto, no
  preguntas triviales o de relleno.
- Ir de lo general a lo específico:
  1. Motivación / problema real detrás de la idea — ¿por qué esta
     investigación (o esta nueva vuelta), qué decisión o necesidad la
     dispara?
  2. Objetivo concreto — ¿qué se quiere poder responder o producir al final?
  3. Alcance — qué está explícitamente dentro y qué está explícitamente
     fuera.
  4. Restricciones — idioma(s) de las fuentes, plazos, nivel de profundidad
     esperado (exploratorio vs. exhaustivo), tipo de fuentes aceptables o
     vetadas.
  5. Criterios de éxito — cómo va a saber el usuario que esta iteración
     cumplió su objetivo.
- No forzar todas las preguntas en un solo mensaje si el usuario todavía se
  está formando una idea. Está bien iterar en varias rondas dentro de esta
  misma fase (ver ciclo de iteración de fase en `SKILL.md` sección 3).
- Evitar jerga innecesaria; adaptar el nivel de detalle técnico a como hable
  el usuario.

## Entregable de la fase

Un documento de alcance/requerimientos, guardado en
`/research-lc-workflow/{nombre_proyecto}/iter-{n}/alcance_requerimientos.md`,
con como mínimo:
- Tema y motivación
- Objetivo de la investigación (de esta iteración)
- Preguntas de investigación concretas
- Alcance: qué se cubre y qué explícitamente no
- Restricciones (idioma, plazos, tipo de fuentes, profundidad esperada)
- Criterios de éxito
- (Solo si `iteracion_global > 0`) Qué se retoma de la síntesis anterior y qué
  es nuevo en esta vuelta, con referencia a `iter-{N-1}/sintesis_final.md`

Este documento es el insumo principal de la Fase 2 (para proponer tipos de
fuente) y de la Fase 4 (para verificar que la síntesis final de esta
iteración responde a lo pedido acá).

## Antes de solicitar el gate

Confirmar con el usuario que el documento de alcance refleja fielmente lo que
quiere investigar en esta vuelta (y, si aplica, que capturó bien qué toma de
la síntesis anterior). En `iter-0`, confirmar también que el nombre de
proyecto y la carpeta creada le parecen correctos. Recién ahí ofrecer
solicitar el gate (nunca asumirlo).
