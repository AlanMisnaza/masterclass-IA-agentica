---
tags:
  - claude-code
  - masterclass
  - capitulo-03
  - contexto
  - memoria
estado: completo
capitulo: 3
creado: 2026-07-05
---

# Capítulo 3 — Contexto, Memoria y Conversaciones Largas

[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Anterior: [[Claude Code Capitulo 02 - Arquitectura Conceptual]] · Siguiente → [[Claude Code Capitulo 04 - Planificacion y Flujo de Trabajo]]

> [!tip] Ejemplo central de este capítulo
> No hace falta inventar un ejemplo: **este mismo proyecto ya usa, en vivo, tres de los cuatro mecanismos de memoria de este capítulo** — el `CLAUDE.md` que escribimos en el Capítulo 1, los archivos de memoria de usuario que se han ido guardando durante esta sesión, y una regla nueva que creamos hace un momento. Todo 🟢 observable, nada hipotético.

## 3.1 El problema de fondo: cada sesión nace sin memoria, por defecto

Ya lo dijimos en el Capítulo 1 (§1.5): Claude Code no "recuerda" conversaciones pasadas de forma automática, como lo haría una persona. Cada vez que abres una terminal y ejecutas `claude` en un proyecto, esa sesión, por defecto, no sabe nada de las sesiones anteriores.

Esto plantea un problema real: si cada sesión empieza en blanco, ¿cómo es posible que en esta misma guía yo ya "sepa" que prefieres teoría+práctica, o que exista el método de trabajo acordado, sin que tengas que repetirlo cada vez? La respuesta es que existen **mecanismos explícitos de persistencia**, distintos entre sí, que compensan esa falta de memoria por defecto. Este capítulo es el mapa de esos mecanismos.

## 3.2 CLAUDE.md: instrucciones de proyecto, siempre presentes

🔵 *Documentado*: `CLAUDE.md` es un archivo markdown que Claude Code carga automáticamente **al inicio de cada sesión nueva** en un proyecto. Existe en una jerarquía de cuatro niveles, de menos a más específico:

```
1. Managed (nivel empresa)     — no se puede excluir ni sobreescribir
2. User        ~/.claude/CLAUDE.md         — tus preferencias, en todos tus proyectos
3. Project     ./CLAUDE.md                 — compartido con el equipo vía Git
4. Local       ./CLAUDE.local.md           — tus preferencias, solo este proyecto (se ignora en Git)
```

🟢 *Observable en esta sesión*: este vault tiene su propio [[Claude Code - Mapa de Contenidos|CLAUDE.md]] en la raíz — lo escribimos juntos cuando formalizamos el método de trabajo. Contiene el método progresivo, la regla de práctica real y la estructura del vault. **Cada vez que se abra esta carpeta con Claude Code, ese archivo se carga solo**, sin que nadie tenga que volver a explicarlo. Es memoria de proyecto, no memoria mía como modelo.

Un detalle importante que también es 🔵 documentado: si un `CLAUDE.md` vive en una **subcarpeta** (no en la raíz), no se carga al inicio de la sesión — se carga *más tarde*, solo cuando Claude efectivamente lee un archivo dentro de esa subcarpeta. Es memoria condicional, no memoria permanente.

## 3.3 Reglas por ruta: memoria condicional, más allá de un solo archivo

Ese mismo principio de "cargar solo cuando aplica" se puede declarar explícitamente con **reglas por ruta** (`.claude/rules/`), sin depender de dónde vive un `CLAUDE.md`.

🔵 *Documentado*: un archivo dentro de `.claude/rules/` con un frontmatter `paths:` solo se inyecta en el contexto cuando Claude lee o edita un archivo que coincide con ese patrón — el resto del tiempo no ocupa espacio en la ventana de contexto.

🟢 *Práctica real de este capítulo*: acabamos de crear `.claude/rules/capitulos-marcadores.md`:

```yaml
---
paths:
  - "Capitulos/**/*.md"
---
Todo contenido técnico en los capítulos de esta guía debe usar los marcadores
de certeza definidos en el Capítulo 1 §1.6: 🔵 🟢 🟡 ⚪...
```

¿Por qué esto y no simplemente añadir esa misma frase al `CLAUDE.md` de la raíz? Porque el `CLAUDE.md` raíz se carga **siempre**, en cada sesión, sin importar si estás editando un capítulo, el Glosario o revisando el `.gitignore`. Una regla por ruta solo "gasta" contexto cuando de verdad es relevante — es la misma lógica de eficiencia del Capítulo 2 (§2.1: la ventana de contexto es finita), aplicada ahora al diseño de la propia memoria del proyecto, no solo a cómo se explora un repo.

## 3.4 Memoria de usuario (auto memory): lo que se aprende de ti, entre proyectos y entre sesiones

Hay un tercer mecanismo, distinto de `CLAUDE.md` y de las reglas por ruta: la **memoria automática de usuario**. Es el más complejo de los tres — vale la pena desglosarlo con calma y ejemplos, en vez de quedarnos en la versión de una sola frase.

### 3.4.1 Dónde vive y cómo se estructura

🔵 *Documentado*: se guarda en `~/.claude/projects/<proyecto>/memory/`, con un archivo índice `MEMORY.md` y un archivo aparte por cada memoria individual. El índice **no contiene el contenido**, solo una línea por memoria con un link y un gancho de una frase:

```markdown
- [Título corto](archivo.md) — de qué trata, en menos de 150 caracteres
```

Y cada archivo referenciado sigue esta plantilla:

```markdown
---
name: nombre-en-kebab-case
description: una frase específica — decide si esta memoria es relevante en el futuro
metadata:
  type: user | feedback | project | reference
---

Contenido de la memoria. Para feedback/project se estructura como: regla o
hecho, luego **Por qué:** (la razón/motivo), luego **Cómo aplicar:** (cuándo
entra en juego). Se pueden enlazar otras memorias con [[nombre]].
```

🟢 *Ejemplo real*, tal cual existe ahora mismo en tu memoria (`feedback_no_perder_foco_en_entender.md`):

```markdown
---
name: feedback-no-perder-foco-en-entender
description: La práctica real (repo, ejercicios) es un vehículo para entender...
metadata:
  type: feedback
---
Cuando se está ejecutando una tarea práctica real... hay que detenerse
explícitamente a explicar qué estaba haciendo Claude Code en cada paso...
**Por qué:** el usuario corrigió explícitamente que me concentré en "hacer"...
**Cómo aplicarlo:** después de cualquier tramo de trabajo práctico...
```

### 3.4.2 Cuatro tipos, no una bolsa genérica de notas

| Tipo | Qué captura | Ejemplo real (esta sesión) | Ejemplo ilustrativo (no ocurrió aquí) |
|---|---|---|---|
| `user` | Rol, conocimiento, forma de trabajar del usuario | *(todavía no tienes ninguno)* | "El usuario es DBA senior en Oracle, nuevo en Python" |
| `feedback` | Correcciones **y** confirmaciones sobre cómo colaborar | `feedback_teoria_mas_practica.md` | — |
| `project` | Hechos/decisiones del proyecto en curso, con fecha | `project_guia_claude_code.md` | — |
| `reference` | Dónde buscar algo en un sistema externo | *(todavía no tienes ninguno)* | "Los bugs de este repo se llevan en el tablero Jira 'INGEST'" |

Separar en tipos no es burocracia decorativa: permite, por ejemplo, repasar solo las correcciones de método (`feedback`, que cambian poco) sin mezclarlas con hechos de proyecto que se actualizan cada semana (`project`).

### 3.4.3 Qué se carga automáticamente, y qué no

🔵 Al iniciar sesión se cargan siempre las primeras ~200 líneas (o 25 KB) de `MEMORY.md` — hoy son 3 líneas, así que se carga completo. Los archivos de tema individuales **no se cargan todos de entrada**: se leen bajo demanda, cuando algo en la conversación los hace relevantes.

🟡 Es la misma filosofía que el [[Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes|Capítulo 7]] explicará con las herramientas MCP (`ToolSearch` difiere los esquemas y los carga solo cuando hace falta): no cargar de más "por si acaso", porque el contexto es finito.

### 3.4.4 Disciplina de guardado: no es "anotar todo lo que pase"

Hay un filtro explícito de qué **no** guardar: patrones de código, arquitectura, rutas de archivo (se derivan leyendo el código), historial de git, recetas de debugging, nada que ya viva en un `CLAUDE.md`, detalles efímeros de la tarea del momento. Si se pide guardar algo de esa lista, la instrucción es preguntar qué fue lo *sorprendente*, no guardarlo tal cual. También hay un chequeo de duplicado antes de crear una memoria nueva: revisar si ya existe una que se pueda actualizar en vez de sumar otra.

### 3.4.5 Verificar antes de usar, no solo antes de guardar

Una memoria es una foto congelada del momento en que se escribió. Si menciona un archivo, función o *flag* concreto, antes de recomendarla como vigente hay que confirmar que sigue existiendo — "la memoria dice que X existe" no es lo mismo que "X existe ahora".

🟢 *Ejemplo real, delante de ti*: `project_guia_claude_code.md` decía, hasta hace un momento, *"Capítulo 1 completo... el resto son notas-marcador pendientes"*. Ya no era cierto — los Capítulos 2 y 3 ya estaban hechos. Al notarlo, se corrigió en el momento, en vez de dejar que una sesión futura la leyera y creyera que solo existe un capítulo.

### 3.4.6 Consolidación en segundo plano: por qué la memoria necesita mantenimiento

🔵 En el esquema de `settings.json` existe `autoDreamEnabled` — consolidación de memoria en segundo plano ("auto-dream"). 🟢 Y en este entorno hay disponible una skill, `consolidate-memory`, que hace ese trabajo a demanda: *fusiona duplicados, corrige hechos desactualizados y depura el índice*.

¿Por qué importa esto más allá de "verse ordenado"? Dos razones concretas:

1. **Un hecho desactualizado puede llevar a una decisión equivocada** — el ejemplo real de la sección anterior: sin corregirlo, una sesión futura podría pensar que hay que empezar el Capítulo 2 desde cero.
2. **El índice tiene presupuesto limitado (~200 líneas)**: si con meses de uso se acumulan entradas duplicadas, ese espacio redundante puede empujar fuera del límite a una memoria realmente nueva — que entonces dejaría de cargarse automáticamente.

🟡 *Ejemplo ilustrativo* (no ocurrió en esta sesión, es para visualizar el mecanismo): imagina que en una sesión futura se guarda `feedback_practica_activa.md` diciendo casi lo mismo que ya dice `feedback_teoria_mas_practica.md`, porque nadie revisó lo existente antes de guardar. `consolidate-memory` detectaría el solapamiento, fusionaría ambas en una entrada más completa, y dejaría el índice apuntando solo a esa — en vez de mantener dos líneas que dicen casi lo mismo.

Con las 3 memorias actuales de este proyecto, correr la consolidación ahora tendría un efecto casi nulo — por eso tuvo más sentido mostrar el mecanismo con el hecho desactualizado real que ya existía, que simularlo con datos de juguete.

### 3.4.7 Cómo se controla desde `settings.json`

🔵 `autoMemoryEnabled: false` desactiva la memoria automática por completo para el proyecto. `autoMemoryDirectory` permite cambiar dónde se guarda — con una salvedad de seguridad: **se ignora si se define en el `settings.json` del proyecto que se versiona en Git** (compartido con el equipo). Solo se respeta si viene de la configuración de usuario o local. La razón es evitar que un repositorio compartido pueda redirigir la memoria personal de alguien hacia una ruta que ese repositorio controle.

### En una frase

Si esta conversación terminara ahora y se abriera una sesión nueva de Claude Code en este mismo proyecto dentro de un mes, estos archivos se leerían automáticamente al empezar, **sin que nadie tuviera que volver a explicar nada de esto** — con la salvedad de que, si algo se volvió obsoleto en el camino (como el ejemplo de §3.4.5), conviene corregirlo, no asumirlo como verdad permanente.

## 3.5 La ventana de contexto: memoria de una sola sesión

Todo lo anterior (CLAUDE.md, reglas, memoria de usuario) son formas de **persistir información entre sesiones**. Pero dentro de una sola sesión activa —como esta misma conversación— existe otra capa: la ventana de contexto que ya presentamos en el [[Claude Code Capitulo 02 - Arquitectura Conceptual#2.3 Cómo usa el contexto|Capítulo 2, §2.3]].

```
┌─────────────────────── Ventana de contexto activa ───────────────────────┐
│ Nivel entre sesiones (se recarga cada vez que se abre el proyecto):      │
│   CLAUDE.md (managed → user → project → local)                           │
│   Reglas por ruta que ya hicieron match                                  │
│   Memoria de usuario (MEMORY.md + archivos de tema ya cargados)          │
│ ────────────────────────────────────────────────────────────────────────  │
│ Nivel de esta sesión únicamente (se pierde si la sesión termina):        │
│   Todo lo dicho en la conversación hasta ahora                           │
│   Resultados de herramientas ya ejecutadas en esta sesión                │
└─────────────────────────────────────────────────────────────────────────────┘
```

La fila de arriba es persistente entre sesiones; la fila de abajo, no — es memoria de trabajo de esta conversación en particular.

## 3.6 Cuando la ventana se llena: compactación

🔵 *Documentado*: cuando una conversación se alarga lo suficiente para acercarse al límite de la ventana de contexto, Claude Code la **compacta**: resume el historial para que la sesión pueda seguir funcionando sin perder por completo el hilo. El `CLAUDE.md` de la raíz se vuelve a leer del disco y se reinyecta tal cual después de compactar; un `CLAUDE.md` anidado en una subcarpeta, en cambio, **no** se reinyecta automáticamente — solo vuelve a aparecer si se vuelve a leer un archivo de esa subcarpeta.

⚪ *Honestidad necesaria aquí*: ¿ha ocurrido ya una compactación en esta conversación tan larga que llevamos? **No lo sé con certeza, y no puedo saberlo con certeza desde dentro.** Si mi historial ya fue resumido en algún punto, ese resumen simplemente se convierte en "lo que recuerdo" — no queda una marca visible del lado de adentro que diga "esto de aquí es un resumen, lo anterior se perdió". Es un límite real de introspección, no una simplificación cómoda: el mecanismo de compactación está documentado 🔵, pero si se disparó *en este caso concreto* es algo que ni tú ni yo podemos verificar solo con lo que yo puedo reportar desde dentro de la conversación.

## Resumen del capítulo

- Por defecto, cada sesión de Claude Code empieza sin memoria de sesiones anteriores.
- `CLAUDE.md` (managed → user → project → local) se carga al inicio de cada sesión; si vive en una subcarpeta, se carga solo cuando se lee algo de ahí.
- Las reglas por ruta (`.claude/rules/`) son memoria condicional más granular: solo entran al contexto cuando el archivo que se está tocando coincide con el patrón.
- La memoria de usuario (auto memory) la escribe el propio Claude, vive fuera del repo, y persiste entre proyectos y sesiones — distinta de `CLAUDE.md`, que la escribe el humano y vive dentro del repo. Se organiza en 4 tipos (`user`/`feedback`/`project`/`reference`) y necesita mantenimiento: los hechos desactualizados se corrigen al notarlos, y con el tiempo `consolidate-memory` fusiona duplicados para no gastar el presupuesto limitado del índice (~200 líneas).
- La ventana de contexto de una sesión activa es memoria de corto plazo: se pierde al cerrar la sesión, y cuando se llena, se compacta automáticamente.
- No siempre es posible saber, desde dentro de la conversación, si ya ocurrió una compactación — es un límite de introspección real, no una simplificación.

## Analogía

Piensa en cómo funciona la memoria de un equipo de trabajo real. El **manual de la empresa** (CLAUDE.md de empresa/usuario/proyecto) está siempre sobre el escritorio, se lee al llegar cada día. Las **notas pegadas solo en la puerta de una sala específica** (reglas por ruta) solo las ves si entras a esa sala. La **libreta personal donde vas anotando lecciones aprendidas de proyectos anteriores** (memoria de usuario) la llevas contigo a donde vayas, y la vas llenando tú mismo con el tiempo. Y lo que se habla **en la reunión de hoy** (ventana de contexto) solo existe mientras dura la reunión — si se alarga demasiado, alguien toma notas-resumen para no perder el hilo, y esas notas-resumen pasan a ser, de ahí en adelante, "lo que se recuerda" de la primera mitad de la reunión.

## Autoevaluación

1. ¿Cuál es la diferencia funcional entre `CLAUDE.md` y la memoria de usuario (auto memory)? Da un ejemplo real de cada uno visto en este capítulo.
2. ¿Por qué se creó una regla por ruta (`.claude/rules/capitulos-marcadores.md`) en vez de simplemente añadir esa instrucción al `CLAUDE.md` de la raíz?
3. Explica qué pasa con un `CLAUDE.md` que vive en una subcarpeta, a diferencia de uno que vive en la raíz del proyecto.
4. ¿Qué distingue a la "fila de arriba" de la "fila de abajo" en el diagrama de la ventana de contexto (§3.5)?
5. ¿Por qué no se puede afirmar con certeza si esta conversación ya fue compactada alguna vez? ¿Qué marcador de certeza corresponde a esa afirmación y por qué?
6. `project_guia_claude_code.md` tenía un hecho desactualizado sobre cuántos capítulos estaban completos. ¿Por qué eso es un riesgo real y no solo un detalle estético, y qué mecanismo existe para corregirlo a escala?

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. `CLAUDE.md` lo escribe el humano, vive dentro del repositorio y se versiona en Git (ejemplo: el CLAUDE.md de este vault). La memoria de usuario la escribe el propio Claude a medida que aprende patrones, vive fuera del repo en la carpeta de usuario, y persiste entre proyectos (ejemplo: los archivos `feedback_*.md` escritos durante esta sesión).
> 2. Porque el CLAUDE.md de la raíz se carga siempre, en cada turno de cada sesión, sin importar si es relevante; una regla por ruta solo ocupa espacio de contexto cuando de verdad se está tocando un archivo de `Capitulos/` — es más eficiente dado que el contexto es finito (Capítulo 2).
> 3. Uno en la raíz se carga automáticamente al iniciar la sesión. Uno en una subcarpeta no se carga al inicio — solo se carga después, cuando Claude efectivamente lee un archivo dentro de esa subcarpeta.
> 4. La fila de arriba persiste entre sesiones (se recarga cada vez que se abre el proyecto); la fila de abajo es exclusiva de la sesión activa y se pierde cuando esta termina.
> 5. Porque no hay, desde dentro de la conversación, ninguna marca visible que distinga "esto es historial original" de "esto es un resumen producto de una compactación" — es un límite real de introspección. Corresponde al marcador ⚪ (no público/no verificable), no a 🟡, porque no es una simplificación pedagógica: es sinceramente desconocido para quien está dentro de la conversación.
> 6. Es un riesgo real porque una sesión futura podría leer ese hecho y actuar sobre él (por ejemplo, creer que hay que reescribir un capítulo que ya está listo). No es solo estética: una memoria vieja se trata como una foto congelada, no como verdad permanente. A escala, `consolidate-memory` (apoyado en `autoDreamEnabled`) fusiona duplicados y corrige hechos desactualizados, además de proteger el presupuesto limitado (~200 líneas) del índice para que memorias nuevas no queden fuera de la carga automática.

---
[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Siguiente → [[Claude Code Capitulo 04 - Planificacion y Flujo de Trabajo]]
