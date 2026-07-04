---
name: investigacion-por-fases
description: Ejecuta un workflow de investigación en cuatro fases obligatorias e iterables (Mapeo y Alcance, Búsqueda de Fuentes, Análisis Profundo, Síntesis de Datos), repetible indefinidamente como "iteraciones globales" (iter-0, iter-1, ... iter-n) donde cada nueva vuelta parte de la síntesis final de la anterior. Persiste estado en disco vía MCP filesystem bajo /research-lc-workflow/{nombre_proyecto}/iter-N, con un archivo de detalle por fase y control estricto human-in-the-loop. Usar SIEMPRE ante pedidos de iniciar, continuar, retomar, re-iterar o auditar una "investigación" o "research" formal, o mención de `research-lc-workflow`, `estado_investigacion.json`, "gate de aprobación", "fuentes de investigación", "nueva iteración", "iter-0/iter-n". También activar ante "en qué fase estamos", "aprueba para continuar", "quiero otra vuelta de esta investigación", o al retomar un proyecto previo por nombre. No usar para consultas puntuales de un solo turno sin seguimiento de estado.
---

# Investigación por Fases (Gated Research Workflow)

Skill para conducir investigaciones multi-etapa con estado persistente en disco,
un nombre de proyecto obligatorio, aprobación humana obligatoria entre fases,
un archivo de detalle específico por fase (progressive disclosure), y la
posibilidad de repetir el workflow completo indefinidamente en "iteraciones
globales" encadenadas.

**Dos niveles de iteración — no confundir:**
- **Iteración de fase** (ya existía): rondas de ajuste sobre el borrador de
  UNA fase, antes de que se abra su gate de aprobación. Vive dentro de
  `fases.<fase>.iteraciones` en `estado_investigacion.json` (ver sección 3).
- **Iteración global** (nueva en esta versión): una corrida COMPLETA de las
  cuatro fases, de punta a punta. Cada iteración global vive en su propia
  carpeta `iter-N/` y, a partir de `iter-1`, arranca usando como insumo la
  `sintesis_final.md` de la iteración global anterior (ver sección 4bis).

**Requisito de portabilidad:** este skill es agnóstico al sistema operativo.
Todas las operaciones de disco se hacen exclusivamente a través de las
herramientas del servidor MCP `filesystem` (o un servidor MCP equivalente que
el entorno tenga conectado). **Está prohibido usar una herramienta de
shell/bash** para leer, crear, anexar o modificar cualquier archivo de este
workflow, ni para obtener la fecha/hora.

---

## 1. Directivas Maestras (System Prompt del agente)

1. **Nombre de proyecto obligatorio, determinado en Fase 1 de `iter-0`.**
   Ninguna investigación puede arrancar sin un nombre de proyecto. Si el
   usuario no lo dio, preguntarlo explícitamente al arrancar `iter-0`. No se
   crea ningún archivo en disco hasta tener este nombre. El nombre de
   proyecto es fijo para todas las iteraciones globales futuras de esa
   investigación.

2. **Ruta raíz fija y derivada del nombre de proyecto, con subcarpeta por
   iteración global:**
   ```
   /research-lc-workflow/{nombre_proyecto_snake_case}/iter-{n}/
   ```
   donde `n` arranca en `0` y se incrementa en 1 en cada nueva iteración
   global (nunca se reutiliza ni se rellena un número salteado). Ver sección
   2.4 para snake_case, sección 2.5 para resolución de directorios permitidos,
   y sección 4bis para el ciclo completo de iteraciones globales.

3. **Fuente única de verdad, por iteración y a nivel de proyecto.** El estado
   de una iteración global es SIEMPRE `iter-N/estado_investigacion.json`. El
   estado global del proyecto (cuántas iteraciones hay, cuál es la vigente)
   es SIEMPRE `proyecto.json` en la raíz del proyecto (fuera de cualquier
   `iter-N/`). Ninguno de los dos se infiere de la memoria de la conversación.

4. **Cuatro fases, orden estricto, dentro de cada iteración global:**
   - `mapeo_y_alcance`
   - `busqueda_fuentes`
   - `analisis_profundo`
   - `sintesis_datos`

5. **El agente solo carga el detalle de la fase activa.** Cada fase tiene su
   propio archivo de referencia `references/fase-N-detalle.md` (bundled en
   este skill, no en la carpeta del proyecto). El agente no lee el detalle de
   otras fases, salvo para usar el *entregable* (no las instrucciones) de una
   fase anterior como insumo de la fase activa. Esto es lo mismo dentro de
   cualquier iteración global.

6. **Toda fase es iterable antes de su aprobación** (iteración de fase, ver
   directiva de terminología arriba y sección 3). El usuario decide cuántas
   rondas de ajuste necesita.

7. **El workflow completo es iterable indefinidamente** (iteración global,
   ver sección 4bis). Al llegar `sintesis_datos` a `"finalizada"`, el agente
   NUNCA arranca automáticamente una nueva iteración global — se lo ofrece al
   usuario y espera que lo pida explícitamente.

8. **Gate de aprobación humana.** Al terminar (a satisfacción del usuario, no
   antes) el entregable de la fase activa, el agente marca
   `gate_pendiente: true`, se detiene, y espera una aprobación explícita.
   Mensajes ambiguos ("dale", "ok", "seguí") NUNCA se interpretan como
   aprobación. Un pedido de más cambios es una iteración de fase, no un
   rechazo; el gate simplemente no se abrió todavía.

9. **Nunca escribir cambios de fase ni de iteración global sin aprobación/
   confirmación registrada.** `fase_actual` solo cambia después de una
   aprobación explícita registrada en `iter-N/audit.md`. Una nueva iteración
   global (`iter-{n+1}`) solo se crea después de que el usuario la pida
   explícitamente, registrado en `historial_global.md`.

10. **Todo hito relevante se audita** (append-only): a nivel de iteración
    global en `iter-N/audit.md` (inicio de fase, iteración de fase, gate,
    aprobación/rechazo), y a nivel de proyecto en `historial_global.md`
    (inicio/cierre de cada iteración global). Ver secciones 2.2 y 6.

11. **Timestamps en ISO 8601, obtenidos sin shell** (ver sección 2.3).

12. **Condensación de contexto al cerrar cada fase y al cerrar cada iteración
    global, y recomendación de conversación nueva.** El agente no tiene forma
    de borrar el historial de la conversación actual — ninguna herramienta de
    este entorno lo permite. Lo que sí debe hacer:
    - Al aprobar el gate de cada fase: actualizar `iter-N/contexto_condensado.md`
      (resumen breve para retomar esa iteración global, ver
      `assets/contexto_condensado_template.md`).
    - Al cerrar una iteración global completa (Fase 4 aprobada): además,
      anexar a `historial_global.md` un resumen de esa iteración (qué cambió
      respecto de la anterior, ruta de su `sintesis_final.md`).
    - En ambos casos, decirle al usuario que puede abrir una conversación
      nueva para continuar (fase siguiente, o próxima iteración global) sin
      perder nada, porque todo el contexto necesario quedó en disco. Esto es
      una sugerencia, nunca una acción automática.

13. **Persistencia exclusiva en disco vía MCP filesystem**, sin depender de
    memoria de conversación ni de shell/bash.

---

## 2. Declaración de Herramientas (Tools) y mecánica común

### 2.1 Mapeo de operaciones a herramientas MCP

| Operación                                            | Herramienta MCP a usar                       |
|-------------------------------------------------------|-----------------------------------------------|
| Confirmar directorios permitidos                      | `filesystem:list_allowed_directories`         |
| Verificar si existe la carpeta del proyecto / de una iteración | `filesystem:list_directory` (del padre) o `filesystem:get_file_info` |
| Detectar cuántas `iter-N/` existen ya                 | `filesystem:list_directory` sobre la carpeta del proyecto (filtrar prefijo `iter-`) |
| Crear carpetas (proyecto o `iter-N/`)                 | `filesystem:create_directory` (idempotente, crea intermedios) |
| Leer cualquier archivo del proyecto/iteración o `references/fase-N-detalle.md` | `filesystem:read_text_file` |
| Crear un archivo por primera vez                      | `filesystem:write_file` (solo si no existe)   |
| Actualizar campos puntuales de `estado_investigacion.json` o `proyecto.json` | `filesystem:edit_file` (oldText/newText exacto — nunca reescribir todo de memoria) |
| Anexar una entrada nueva a `audit.md` o `historial_global.md` | Patrón leer-y-reescribir (ver 2.2) |
| Obtener el timestamp real (ISO 8601)                  | Ver 2.3 — nunca shell                         |
| Listar contenido de un directorio                     | `filesystem:list_directory`                   |

Si no hay MCP `filesystem` (ni equivalente) conectado, avisar al usuario y
sugerir habilitarlo — nunca usar shell/bash como sustituto.

### 2.2 Append-only real sin shell

`filesystem:write_file` sobreescribe el archivo completo. Para anexar a
`audit.md` o a `historial_global.md`: (1) `read_text_file` completo, (2)
construir en memoria `contenido_leído + nueva_entrada` (nunca al revés), (3)
`write_file` con el contenido completo. Nunca omitir el paso 1.

### 2.3 Timestamp real (ISO 8601) sin shell

1. Escribir el archivo objetivo con un marcador temporal
   `"__PENDIENTE_TIMESTAMP__"` en el campo de fecha.
2. Llamar `filesystem:get_file_info` sobre ese mismo archivo — su fecha de
   modificación refleja el instante real de la escritura.
3. Reemplazar el marcador por ese valor con `filesystem:edit_file`.
4. Nunca invocar `date`, `Get-Date` ni ningún comando de shell; nunca inventar
   la hora de memoria.

### 2.4 Normalización del nombre de proyecto a snake_case

Para construir `{nombre_proyecto_snake_case}`: pasar a minúsculas, quitar
tildes/diacríticos, reemplazar cualquier secuencia de espacios o caracteres no
alfanuméricos por un solo guion bajo `_`, y quitar guiones bajos al inicio/
final. Ejemplo: "Impacto de la IA en Educación Rural" →
`impacto_de_la_ia_en_educacion_rural`. Confirmar con el usuario el nombre
resultante antes de crear la carpeta. Este nombre se fija una sola vez, en
`iter-0`, y no se vuelve a pedir en iteraciones globales posteriores de la
misma investigación.

### 2.5 Resolución de la ruta raíz

Antes de crear nada, llamar `filesystem:list_allowed_directories`:
- Si `/research-lc-workflow` (o su padre) está entre los permitidos, usar esa
  ruta absoluta tal cual.
- Si no, crear `research-lc-workflow/{nombre_proyecto_snake_case}` como
  subcarpeta dentro del primer directorio permitido, e informarle al usuario
  la ruta real resultante.
- Nunca asumir un path sin haber consultado `list_allowed_directories` al
  menos una vez por sesión.

---

## 3. Ciclo de trabajo por fase (con iteración de fase) — aplica a las 4 fases, dentro de cualquier iter-N

```
1. filesystem:list_allowed_directories (si no se hizo ya esta sesión)
2. filesystem:read_text_file sobre iter-N/estado_investigacion.json
   → confirmar fase_actual y gate_pendiente=false
3. Leer SOLO references/fase-<F>-detalle.md correspondiente a fase_actual
4. (Si es la primera vez en esta fase) anexar a iter-N/audit.md: INICIO_FASE,
   actualizar fases.<fase>.inicio + estado="en_progreso" (edit_file)
5. Producir/actualizar el borrador del entregable según fase-<F>-detalle.md
   (en iter-0, fase 1, sin insumo previo; en iter-N>0 fase 1, usando
   sintesis_final.md de iter-{N-1} — ver sección 4bis)
6. Presentar el borrador al usuario
7. Si pide cambios (iteración de fase):
     - Anexar ITERACION_SOLICITADA a iter-N/audit.md (2.2)
     - Incrementar fases.<fase>.iteraciones (edit_file)
     - Volver al paso 5. gate_pendiente sigue false, fase_actual no cambia.
8. Cuando el usuario está conforme, el agente ofrece solicitar el gate
   (nunca lo asume).
9. Al solicitar el gate: timestamp (2.3), gate_pendiente=true,
   gate_solicitado_en=<timestamp> (edit_file), anexar SOLICITUD_GATE (2.2).
10. DETENERSE. Esperar respuesta explícita.
11a. Aprueba → anexar APROBACION_USUARIO (2.2); actualizar (edit_file):
       fases.<fase>.fin, aprobado_por_usuario=true, estado="completada",
       gate_pendiente=false, fase_actual=<siguiente fase, o "finalizada" si
       era sintesis_datos>; actualizar iter-N/contexto_condensado.md
       (directiva 12) y sugerir conversación nueva.
       Si fase_actual pasó a "finalizada" → ver sección 4bis (cierre de
       iteración global).
11b. Pide más cambios → tratar como iteración de fase (volver a 7).
11c. Rechaza el enfoque general → anexar RECHAZO_USUARIO (2.2),
       gate_pendiente=false, no se avanza de fase, retomar desde el paso 5.
```

---

## 4. Inicialización de una Investigación Nueva (siempre crea `iter-0`)

1. Si el usuario no dio nombre de proyecto, preguntarlo (directiva 1).
2. Normalizar a snake_case (2.4) y confirmar con el usuario.
3. `filesystem:list_allowed_directories` y resolver la ruta raíz (2.5).
4. `filesystem:create_directory` sobre
   `/research-lc-workflow/{nombre_proyecto_snake_case}/iter-0/`.
5. Crear, con `filesystem:write_file`, a partir de las plantillas de
   `assets/`:
   - En la raíz del proyecto: `proyecto.json` (de
     `proyecto_template.json`, completando `nombre_proyecto`, con
     `iteracion_actual: 0` y una sola entrada en `iteraciones`) y
     `historial_global.md` (de `historial_global_template.md`).
   - Dentro de `iter-0/`: `estado_investigacion.json` (de
     `estado_investigacion_template.json`, con `iteracion_global: 0` y
     `entrada_iteracion_anterior: null`), `audit.md`, y
     `contexto_condensado.md`.
6. Resolver los timestamps pendientes (2.3).
7. Confirmar al usuario la ruta creada y arrancar las tareas de
   `mapeo_y_alcance` leyendo `references/fase-1-detalle.md`.

---

## 4bis. Iteraciones Globales — repetir el workflow completo

### Cuándo se ofrece una nueva iteración global

Únicamente cuando, dentro de la iteración global vigente, se aprueba el gate
final de `sintesis_datos` (`fase_actual` pasa a `"finalizada"`). En ese
momento el agente:
1. Anexa a `iter-N/audit.md` el evento `CIERRE_INVESTIGACION`.
2. Anexa a `historial_global.md` (2.2) un evento `ITERACION_GLOBAL_CERRADA`
   con la ruta de `iter-N/sintesis_final.md`.
3. Actualiza `proyecto.json`: la entrada de esta iteración pasa a
   `estado: "finalizada"`, con su `fin`.
4. Le pregunta al usuario, explícitamente, si quiere iniciar una nueva
   iteración global sobre esta misma investigación, o si la deja cerrada
   así. **No arranca `iter-{N+1}` sin que el usuario lo pida.**

El usuario también puede pedir una nueva iteración global en cualquier otro
momento simplemente diciendo algo como "quiero otra vuelta de esta
investigación" sobre un proyecto cuya última iteración ya está finalizada —
no es necesario que lo ofrezca el agente primero.

### Cómo se crea `iter-{n+1}`

1. Leer `proyecto.json`, confirmar que la iteración vigente está
   `"finalizada"`, y determinar `n+1` (siguiente número consecutivo, nunca se
   reutiliza un número usado antes aunque esa iteración se hubiera
   abandonado).
2. `filesystem:create_directory` sobre
   `/research-lc-workflow/{nombre_proyecto}/iter-{n+1}/`.
3. Crear dentro, con `filesystem:write_file`, a partir de las plantillas:
   `estado_investigacion.json` (con `iteracion_global: n+1` y
   `entrada_iteracion_anterior` apuntando a la ruta de
   `iter-{n}/sintesis_final.md`), `audit.md`, `contexto_condensado.md`.
4. Anexar a `iter-{n+1}/audit.md` un evento `ITERACION_GLOBAL_INICIADA` que
   cite la ruta de la síntesis anterior usada como punto de partida.
5. Anexar a `historial_global.md` (2.2) el mismo evento a nivel de proyecto.
6. Actualizar `proyecto.json`: `iteracion_actual: n+1`, agregar la nueva
   entrada en `iteraciones` con `estado: "en_progreso"`.
7. Arrancar `mapeo_y_alcance` de `iter-{n+1}` leyendo
   `references/fase-1-detalle.md`, que instruye explícitamente cómo usar
   `sintesis_final.md` de `iter-{n}` como insumo de las nuevas preguntas de
   alcance (no se vuelve a preguntar el nombre de proyecto).

### Qué NO cambia entre iteraciones globales

El nombre de proyecto y la ruta raíz del proyecto son fijos. Lo único que se
repite es el ciclo de 4 fases dentro de una carpeta `iter-N/` nueva.

---

## 5. Retomar una Investigación Existente

Si el usuario menciona un nombre de proyecto que ya existe bajo
`/research-lc-workflow/`, o pide "continuar" sin dar nombre y solo hay un
proyecto en esa carpeta:

1. Leer `proyecto.json` para saber `iteracion_actual` y su estado — NO asumir
   nada por lo que se charló antes en la conversación, sobre todo si viene de
   una conversación nueva. Si `proyecto.json` no existe pero sí hay carpetas
   `iter-N/`, reconstruir el índice listando el directorio del proyecto
   (`filesystem:list_directory`) como respaldo.
2. Si la iteración vigente NO está `"finalizada"`: leer
   `iter-{iteracion_actual}/estado_investigacion.json` y
   `contexto_condensado.md`, y continuar el ciclo de la sección 3 desde
   `fase_actual`. Si `gate_pendiente` está en `true`, lo primero es resolver
   ese gate, no seguir produciendo contenido nuevo.
3. Si la iteración vigente SÍ está `"finalizada"`: informarle al usuario que
   esa investigación ya tiene una síntesis final cerrada, mostrar su ruta, y
   preguntar si quiere iniciar una nueva iteración global (sección 4bis) o
   solo consultar lo ya producido.

---

## 6. Formato de entradas de auditoría

Mismo formato de bloque para `iter-N/audit.md` y para `historial_global.md`:

```markdown
## [<timestamp ISO 8601>] — <TIPO_DE_EVENTO>

- **Fase / Iteración global:** <fase_actual o "iter-N">
- **Evento:** <descripción corta>
- **Detalle:** <qué se hizo / qué se pidió iterar / qué respondió el usuario>
- **Aprobado por usuario:** <true|false|N/A> — <cita breve si aplica>

---
```

Tipos de evento en `iter-N/audit.md`: `INICIO_INVESTIGACION` (solo iter-0),
`ITERACION_GLOBAL_INICIADA` (iter-N>0), `INICIO_FASE`, `ITERACION_SOLICITADA`,
`SOLICITUD_GATE`, `APROBACION_USUARIO`, `RECHAZO_USUARIO`,
`CIERRE_INVESTIGACION`.

Tipos de evento en `historial_global.md`: `ITERACION_GLOBAL_INICIADA`,
`ITERACION_GLOBAL_CERRADA`.

---

## 7. Archivos de referencia y plantillas de este skill

Detalle por fase (leer solo el de la fase activa):
- `references/fase-1-detalle.md` — Mapeo y Alcance
- `references/fase-2-detalle.md` — Búsqueda de Fuentes
- `references/fase-3-detalle.md` — Análisis Profundo
- `references/fase-4-detalle.md` — Síntesis de Datos

Plantillas a nivel de proyecto (raíz, fuera de cualquier `iter-N/`):
- `assets/proyecto_template.json`
- `assets/historial_global_template.md`

Plantillas por iteración global (dentro de cada `iter-N/`):
- `assets/estado_investigacion_template.json`
- `assets/audit_template.md`
- `assets/contexto_condensado_template.md`
- `assets/fuentes_investigacion_template.md` (entregable Fase 2)
- `assets/wiki_investigacion_template.md` (entregable Fase 3)

---

## 8. Recordatorio de cumplimiento

Si el agente se encuentra tentado a: avanzar de fase o de iteración global sin
aprobación/pedido explícito, asumir un mensaje ambiguo como aprobación,
limitar cuántas iteraciones de fase o iteraciones globales "debería" necesitar
el usuario, cargar el detalle de fases que no están activas, usar shell "solo
por esta vez", reutilizar un número de `iter-N` ya usado, u olvidar encadenar
`sintesis_final.md` de la iteración anterior al arrancar una nueva — esa es la
señal de detenerse. Ninguna de esas cosas está permitida.
