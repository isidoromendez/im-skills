# Fase 2 — Búsqueda de Fuentes

Leer este archivo solo cuando `fase_actual == "busqueda_fuentes"`.
Requiere como insumo `alcance_requerimientos.md` (entregable de la Fase 1) —
leerlo antes de proponer nada.

## Objetivo

Antes de leer o analizar ningún contenido, decidir junto con el usuario qué
tipos de fuentes se van a buscar y por qué. Esta fase es especialmente
valiosa cuando el usuario no conoce de antemano qué material existe sobre el
tema, o cuando ese material está en otros idiomas.

## Flujo

1. **Proponer y sugerir.** A partir de `alcance_requerimientos.md`, proponer
   categorías y candidatos concretos de fuentes: papers académicos, libros
   especializados, autores de referencia, blogs reconocidos del área,
   informes técnicos/institucionales, normativa, u otras categorías que
   apliquen al tema. Ajustar la mezcla propuesta al tema y a los idiomas
   disponibles/aceptados según lo definido en la Fase 1.
2. **Validar con el usuario.** Qué tipos de fuente priorizar, cuáles excluir,
   preferencia de idioma o de autores/instituciones específicas, y si hay
   fuentes que el usuario ya conoce y quiere incluir.
3. **Compilar el listado real.** Con lo acordado, identificar las fuentes
   concretas (usando búsqueda web si está disponible) con su referencia o
   enlace y una nota de por qué se seleccionó cada una.
4. **Iterar.** El listado se ajusta con el usuario (agregar, quitar,
   reclasificar) tantas veces como haga falta antes de solicitar el gate.

## Entregable de la fase

`documento_fuentes`, guardado en
`/research-lc-workflow/{nombre_proyecto}/iter-{n}/fuentes_investigacion.md`,
usando como base `assets/fuentes_investigacion_template.md` de este skill:
listado de fuentes por categoría, con referencia/enlace y justificación de
cada una, más las notas de validación (qué se acordó, qué se excluyó y por
qué).

Este documento es el insumo principal de la Fase 3.

Si `iteracion_global > 0`, tener en cuenta que puede haber fuentes ya
identificadas en `iter-{N-1}/fuentes_investigacion.md` que sigan siendo
válidas para esta vuelta (sobre todo si el alcance de la Fase 1 de esta
iteración retoma preguntas anteriores). Está bien ofrecerle al usuario
reutilizar/ampliar ese listado en lugar de rehacerlo desde cero, pero eso lo
decide el usuario, no se asume automáticamente.

## Antes de solicitar el gate

Confirmar con el usuario que el mix de tipos de fuente y el listado concreto
cubren razonablemente las preguntas de investigación de la Fase 1, y que no
falta ningún tipo de fuente que el usuario considere importante (por ejemplo,
fuentes en un idioma específico que todavía no se buscaron).
