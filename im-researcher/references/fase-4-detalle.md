# Fase 4 — Síntesis de Datos

Leer este archivo solo cuando `fase_actual == "sintesis_datos"`.
Requiere como insumo `wiki_investigacion.md` (Fase 3) y
`alcance_requerimientos.md` (Fase 1), ambos de esta misma iteración
(`iter-{n}`) — leerlos antes de empezar.

## Objetivo

Consolidar los hallazgos de la wiki en conclusiones y/o el entregable final
de esta iteración (informe, resumen ejecutivo, recomendación), verificando
explícitamente que responde a los requerimientos definidos en la Fase 1 de
esta misma iteración. Este documento es, además, el insumo obligatorio de la
Fase 1 de la próxima iteración global (si el usuario decide iniciar una).

## Flujo

1. Repasar `alcance_requerimientos.md` (de esta iteración) y confirmar que
   cada pregunta de investigación y cada criterio de éxito tiene respuesta en
   la wiki. Si algo quedó sin cubrir, señalarlo al usuario en lugar de
   rellenarlo con suposiciones.
2. Si `iteracion_global > 0`: repasar también `entrada_iteracion_anterior`
   (la síntesis de `iter-{N-1}`) y dejar explícito en la nueva síntesis qué
   se confirmó, qué se corrigió/actualizó, y qué se agregó respecto de esa
   versión anterior — no reescribir la síntesis anterior de cero como si no
   existiera.
3. Redactar la síntesis en el formato que el usuario prefiera (informe
   narrativo, resumen ejecutivo, lista de recomendaciones, etc. — preguntar
   si no fue definido en la Fase 1).
4. Citar o referenciar de dónde viene cada conclusión relevante (wiki /
   fuentes de esta iteración, y síntesis anterior si corresponde), sin
   necesidad de repetir el detalle completo.
5. Iterar la síntesis con el usuario tantas veces como pida.

## Entregable de la fase

`sintesis_final`, guardado en
`/research-lc-workflow/{nombre_proyecto}/iter-{n}/sintesis_final.md`.

## Antes de solicitar el gate

Confirmar con el usuario que la síntesis final responde a los criterios de
éxito definidos en la Fase 1 de esta iteración.

## Al aprobarse este gate

`fase_actual` pasa a `"finalizada"` y esta iteración global se considera
cerrada. Esto dispara el procedimiento de cierre de iteración global descrito
en `SKILL.md` sección 4bis:
- Anexar `CIERRE_INVESTIGACION` en `iter-{n}/audit.md`.
- Anexar `ITERACION_GLOBAL_CERRADA` en `historial_global.md`, citando la ruta
  de esta `sintesis_final.md`.
- Actualizar `proyecto.json` (estado de esta iteración = `"finalizada"`).
- Preguntarle explícitamente al usuario si quiere iniciar una nueva
  iteración global (que usará esta síntesis como insumo) o dejar la
  investigación cerrada así. Nunca arrancar `iter-{n+1}` sin que lo pida.
