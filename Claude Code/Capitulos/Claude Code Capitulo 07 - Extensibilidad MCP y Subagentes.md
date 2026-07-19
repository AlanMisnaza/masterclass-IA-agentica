---
tags:
  - claude-code
  - masterclass
  - capitulo-07
  - mcp
  - herramientas
  - subagentes
  - skills
  - hooks
estado: completo
capitulo: 7
creado: 2026-07-04
actualizado: 2026-07-18
---

# Capítulo 7 — Extensibilidad: MCP, Subagentes y Skills

[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Anterior: [[Claude Code Capitulo 06 - Comandos CLI y Superficies]] · Siguiente → [[Claude Code Capitulo 08 - Comparativas]]

> [!tip] La pregunta de este capítulo
> Los Capítulos 2–5 mostraron las herramientas con las que Claude Code viene de fábrica (leer, editar, ejecutar, buscar). Este capítulo responde la pregunta natural siguiente: **¿cómo se le dan manos que Anthropic nunca le fabricó?** — tu Jira, tu base de datos, tu Teams, la documentación de Microsoft. Y su pregunta gemela: ¿cuándo conviene *extender* a Claude (MCP) y cuándo conviene que *delegue* (subagentes)?

## 7.1 Manos de fábrica y manos que faltan

🟢 Lo que ya conoces de capítulos anteriores son las **herramientas nativas**: `Read`, `Write`, `Edit`, `Glob`, `Grep`, la terminal (`Bash`/`PowerShell`), `WebSearch`/`WebFetch`. Cubren el mundo local (archivos, comandos) y la web pública. Pero tu mundo de trabajo real es más grande: el repo institucional en GitHub, un DWH en Oracle, tableros de Power BI, canales de Teams. Nada de eso es un archivo en tu disco.

Antes, había dos salidas, ambas malas:
1. **Copiar y pegar** datos del sistema externo a la conversación — gasta contexto (Capítulo 3) y se desactualiza al instante.
2. **Integraciones a medida** — cada fabricante de IA escribiendo un conector distinto para cada herramienta: N modelos × M sistemas = N×M integraciones.

🔵 **MCP (Model Context Protocol)** es la respuesta de Anthropic a ese problema: un **estándar abierto** (publicado en noviembre de 2024, especificación en `modelcontextprotocol.io`) que define cómo cualquier aplicación puede exponer herramientas y datos a cualquier modelo. El sistema externo implementa el estándar *una vez* (un "servidor MCP") y cualquier cliente compatible se conecta: N+M en vez de N×M.

🟢 La mejor prueba de que el estándar trascendió a Anthropic está en esta misma sesión: uno de los servidores conectados aquí es el **servidor MCP oficial de Microsoft Learn** — Microsoft publicando sus docs en el protocolo de un competidor, porque el protocolo es de todos.

🟡 La analogía clásica (y útil): MCP es el **USB-C de la IA**. Antes cada aparato traía su cargador propietario; ahora hay un puerto estándar y cualquier fabricante hace periféricos.

## 7.2 Qué expone un servidor: las tres primitivas

🔵 Un servidor MCP puede ofrecer tres tipos de cosas (fuente: `code.claude.com/docs/en/mcp`):

| Primitiva | Qué es | Quién la invoca | Cómo se ve en Claude Code |
|---|---|---|---|
| **Tools** | Funciones invocables (buscar, crear un ticket, consultar una BD) | El modelo, durante su turno | Herramientas con nombre `mcp__servidor__herramienta` |
| **Resources** | Datos de solo lectura (un documento, un esquema) | Tú, mencionándolos | `@servidor:protocolo://ruta` en tu mensaje |
| **Prompts** | Plantillas de instrucciones preparadas por el servidor | Tú, como slash command | `/mcp__servidor__prompt` |

Dos capacidades adicionales documentadas que completan el cuadro:
- 🔵 **Elicitation**: el servidor puede pedirte input estructurado (un formulario) a mitad de operación.
- 🔵 **Channels**: un servidor puede *empujar* eventos hacia la sesión (un mensaje de Telegram, un webhook) para que Claude reaccione sin que tú escribas nada.
- 🟢 **Instrucciones de servidor**: un servidor puede inyectar sus propias instrucciones de uso al contexto de la sesión. Observable aquí: el servidor de Microsoft Learn incluye un texto que me indica su flujo recomendado ("Search da amplitud, Fetch da profundidad").

## 7.3 Conectarse: los cuatro transportes

🔵 Verificado en vivo en esta sesión contra la página oficial de MCP (la descargué completa hoy, 2026-07-06):

| Transporte | Qué es | Cuándo usarlo |
|---|---|---|
| **HTTP** | Servidor remoto vía HTTP streamable (alias `streamable-http`) | **El recomendado** para todo lo remoto; soporta OAuth |
| **SSE** | Server-Sent Events | ⚠️ **Deprecado** — usa HTTP donde esté disponible |
| **stdio** | Un proceso local en tu máquina (ej. `npx servidor`) | Herramientas con acceso directo a tu sistema; scripts propios |
| **WebSocket** | Conexión bidireccional persistente | Servidores que empujan eventos; no soporta OAuth ni el flag `--transport` (se configura por JSON) |

Los comandos esenciales (todos documentados; los probarás en §7.11):

```bash
claude mcp add --transport http <nombre> <url>     # remoto HTTP
claude mcp add --transport stdio <nombre> -- npx <paquete>  # proceso local
claude mcp list / get <nombre> / remove <nombre>   # gestión
claude mcp login / logout <nombre>                 # OAuth sin panel
# y dentro de la sesión: /mcp  (panel de gestión)
```

🔵 Detalle que ahorra dolores con stdio: el `--` separa las opciones de Claude de las del servidor — todo lo que va después del `--` se pasa intacto al proceso. Y el servidor recibe la variable `CLAUDE_PROJECT_DIR` con la raíz del proyecto.

🔵 Un ejemplo oficial directamente relevante para tu mundo DWH: la documentación muestra cómo conectar bases de datos vía stdio con `claude mcp add --transport stdio db -- npx -y @bytebase/dbhub ...` — consultas a la BD como herramienta del modelo, sin copiar y pegar resultados.

## 7.4 Dónde vive la configuración: scopes y confianza

🔵 Igual que `settings.json` (Capítulo 6) y la memoria (Capítulo 3), los servidores MCP tienen niveles. La elección responde dos preguntas: *¿en qué proyectos carga?* y *¿lo comparto con mi equipo?*

| Scope | Carga en | ¿Compartido? | Archivo |
|---|---|---|---|
| **Local** (default) | Solo el proyecto actual | No — privado tuyo | `~/.claude.json` (bajo la ruta del proyecto) |
| **Project** | Solo el proyecto actual | **Sí, vía Git** | `.mcp.json` en la raíz del repo |
| **User** | Todos tus proyectos | No — privado tuyo | `~/.claude.json` |

Precedencia ante duplicados por nombre: **local > project > user** (se usa la definición completa del nivel ganador; no se mezclan campos).

> [!warning] Confianza y seguridad (🔵 documentado)
> - Los servidores project-scope de `.mcp.json` **piden tu aprobación** la primera vez que abres el proyecto — precisamente porque vienen de Git y pudo agregarlos otra persona (`claude mcp reset-project-choices` reinicia esas decisiones).
> - Un servidor que trae contenido externo (webs, tickets, correos) puede exponerte a **prompt injection**: instrucciones maliciosas escondidas en ese contenido. Verifica que confías en cada servidor antes de conectarlo.
> - Las reglas de permisos del Capítulo 4 aplican también aquí: puedes escribir `mcp__servidor__herramienta` en `allow`/`ask`/`deny`.

## 7.5 🟢 Disección en vivo: los MCP de esta sesión

Todo lo anterior es teoría documentada. Ahora, el método de la guía: verla funcionando **de verdad, aquí**. Esta sesión (app) tiene varios servidores MCP conectados, observables de primera mano:

| Servidor (nombre real en sesión) | Qué aporta | Herramienta de ejemplo |
|---|---|---|
| Microsoft Learn (registrado bajo un UUID) | Docs oficiales de Microsoft/Azure | `microsoft_docs_search` |
| `mcp-registry` | Buscar conectores en el registro de Anthropic | `search_mcp_registry` |
| `claude-in-chrome` | Automatizar el navegador Chrome | `navigate`, `read_page` |
| Herramientas de sesión de la app | Capítulos de sesión, tareas en background | `mark_chapter`, `spawn_task` |
| Vista previa / widgets | Previsualizar apps, gráficos inline | `show_widget` |

Cuatro observaciones de primera mano que confirman la teoría:

1. **La convención de nombres es real**: la herramienta con la que busqué en el registro se llama, literalmente, `mcp__mcp-registry__search_mcp_registry` — el patrón `mcp__servidor__herramienta` de §7.2. Y el nombre del servidor es solo una **etiqueta local**: el de Microsoft Learn está registrado aquí bajo un UUID, y funciona igual.

2. **Carga diferida (tool search)**: 🔵 por defecto, Claude Code no carga los esquemas completos de todas las herramientas MCP al arrancar — solo sus nombres — y los recupera bajo demanda con una herramienta interna llamada `ToolSearch`. ¿Por qué? Economía de contexto (Capítulo 3): docenas de servidores con docenas de herramientas cada uno inflarían la ventana antes de empezar. 🟢 Observable hoy: antes de poder llamar al buscador de Microsoft Learn tuve que ejecutar `ToolSearch` para cargar su esquema — pasó en esta misma conversación, dos veces.

3. **Llamada real #1 — resultado vacío también es dato**: busqué `"obsidian"` en el registro de conectores de Anthropic y la respuesta fue `{"results":[]}` — JSON estructurado, como toda respuesta MCP. Hallazgo real: a hoy, no hay conector oficial de Obsidian en el registro (este vault se edita con herramientas nativas de archivo, y por eso funciona igual de bien: las notas son Markdown plano).

4. **Llamada real #2 — un servidor describiéndose a sí mismo**: le pregunté al servidor MCP de Microsoft Learn por su propia documentación de conexión. Respondió con las páginas oficiales que confirman su endpoint (`https://learn.microsoft.com/api/mcp`), su transporte (HTTP streamable) y que no requiere autenticación. Ese dato alimenta directamente tu práctica de §7.11.

## 7.6 Subagentes: delegar en vez de enchufar

MCP le da a Claude más herramientas; los **subagentes** le dan *ayudantes*. Son la otra vía de extensión y resuelven un problema distinto.

🔵 Documentado (`code.claude.com/docs/en/sub-agents`):
- Un subagente es un asistente con **su propia ventana de contexto**, su propio system prompt y sus propias herramientas y permisos.
- Hay subagentes de serie (`general-purpose`, `Explore` para búsqueda de solo lectura, `Plan` para diseñar planes) y puedes definir los tuyos como un **archivo Markdown único** con frontmatter YAML: `.claude/agents/<nombre>.md` (alcance proyecto, versionable en Git) o `~/.claude/agents/<nombre>.md` (alcance usuario, disponible en todos tus proyectos).
- Claude puede invocarlos automáticamente (si la descripción del agente encaja con la tarea) o tú explícitamente.
- Corren **en segundo plano por defecto** y pueden anidarse hasta 5 niveles.
- Lo crucial: el subagente **no comparte contexto** con la sesión principal. Recibe la descripción de su tarea, trabaja aparte, y devuelve solo un resumen.

🟢 Observable en esta sesión: mis instrucciones listan los tipos de agente disponibles (`general-purpose`, `Explore`, `Plan`, `claude-code-guide`...). Y hay un ejemplo real en la historia de este proyecto: la deduplicación del lineamiento Git en el repo institucional la hizo un subagente en la sesión original — la sesión principal solo recibió el resultado.

🟡 La diferencia en una frase: **MCP extiende las manos; un subagente es otro par de manos con su propia mesa de trabajo.** Si la tarea va a ensuciar tu contexto con exploración masiva (leer 40 archivos para responder una pregunta), delegar al subagente protege la ventana de la sesión principal — el Capítulo 3 explicó por qué eso vale oro.

### 🟢 Disección en vivo: crear un subagente propio en esta sesión

El método de la guía otra vez: no basta con describir el mecanismo, hay que verlo nacer. En la sesión cronista del **2026-07-13** creamos un subagente propio para practicar — un **revisor de capítulos** de solo lectura — que quedó en el archivo `.claude/agents/revisor-capitulos.md`.

Su anatomía son **dos partes en un mismo archivo**:

```markdown
---
name: revisor-capitulos          # id único — OBLIGATORIO
description: Revisa un capítulo…  # cuándo delegarle — OBLIGATORIO (el texto que Claude lee para invocarlo solo)
tools: Read, Grep, Glob          # lista blanca; si se OMITE, hereda TODAS → aquí lo vuelve read-only
model: sonnet                    # opcional; por defecto 'inherit' (usa el modelo de la sesión)
---
Eres el revisor de calidad de la Masterclass…   # el cuerpo = system prompt del subagente
```

Tres lecciones que solo se ven al crearlo:
1. **Frontmatter (para la máquina) vs cuerpo (para el modelo).** Los `---` separan la configuración YAML del system prompt. Solo `name` y `description` son obligatorios.
2. **`tools` es una decisión de seguridad.** Con `Read, Grep, Glob` el agente no puede escribir ni ejecutar comandos; omitir el campo habría heredado *todas* las herramientas del padre. El "entorno read-only impenetrable" es aquí una sola línea declarativa.
3. **Aislamiento real.** El subagente arranca solo con su cuerpo + el directorio de trabajo; **no** hereda esta conversación ni el system prompt de Claude Code. Por eso su cuerpo empieza ordenándose leer `CLAUDE.md` y `.claude/rules/capitulos-marcadores.md`: no sabe nada del proyecto hasta que lo lee él mismo.

🟢 **Observación honesta (corrige una expectativa):** la documentación advierte que el *primer* agente creado en una carpeta `.claude/agents/` nueva suele requerir reiniciar la sesión para que el watcher lo detecte. Aquí, tras escribir el archivo, el harness **registró `revisor-capitulos` como agente invocable sin reiniciar**, en el mismo turno. Dato de primera mano: el watcher puede ser más ágil que el caso conservador de la doc — pero si tu agente no aparece, reiniciar sigue siendo la solución documentada.

### Confusión frecuente: llamadas paralelas a herramientas ≠ subagentes

🟢 *Observable de primera mano (2026-07-09):* al investigar la estructura de sesiones CLI para documentar la §6.7 del Capítulo 6, esta sesión ejecutó 5 comandos PowerShell en dos lotes paralelos — 3 primero, 2 después. Esto **no** es delegar a subagentes; es una **llamada paralela a herramientas**, y la diferencia es fundamental:

| | Llamadas paralelas a herramientas | Subagente |
|---|---|---|
| **Quién ejecuta** | El mismo Claude de la sesión actual | Un Claude nuevo, separado |
| **Contexto** | Todo ocurre en la ventana de contexto actual — los resultados entran aquí | Ventana de contexto propia, aislada de esta conversación |
| **Qué sabe** | Todo lo que hemos hablado | Solo lo que le dices en el prompt de la tarea |
| **Qué devuelve** | Los resultados completos, directamente | Un resumen de su trabajo |
| **Cuándo conviene** | Tareas rápidas e independientes con resultados pequeños | Exploración masiva que contaminaría el contexto, o trabajo en segundo plano |

**El ejemplo concreto:** para documentar cómo eliminar sesiones CLI, se necesitaba saber la estructura real de archivos en `~/.claude/projects/`. Eso requería 5 consultas al sistema de archivos:

1. Listar archivos de sesión de un proyecto → confirmar que son JSONL
2. Ver tamaños legibles → saber cuánto pesan
3. Listar todos los proyectos registrados → ver el panorama completo
4. Buscar archivos ocultos → verificar si hay metadatos adicionales
5. Inspeccionar la raíz de `.claude` → buscar un posible índice global

Las 3 primeras son independientes entre sí (ninguna necesita el resultado de otra), así que se lanzaron **en un solo lote, al mismo tiempo**. Las 2 restantes también eran independientes → otro lote paralelo. Resultado: 5 consultas en 2 turnos en lugar de 5 turnos secuenciales.

🟡 **La regla de decisión:** si los resultados son pequeños y los necesitas en tu contexto para razonar sobre ellos → herramientas en paralelo. Si los resultados son enormes o la tarea puede correr en segundo plano mientras haces otra cosa → subagente. En este caso, los 5 comandos devolvieron tablas de pocas líneas (~20 líneas total) — lanzar un subagente habría sido como contratar a alguien para abrir una puerta que puedes abrir tú mismo.

### 🟢 Disección en vivo #2: auditoría forense de seis invocaciones reales de un mismo subagente

Todo lo anterior describe el mecanismo desde fuera. Esta disección lo mira desde **dentro del log crudo**: una sesión distinta (proyecto `desc-curriculo-benchmarking-nacional-saber-pro`, 2026-07-15) auditó, línea por línea del JSONL, las **seis invocaciones reales** de un subagente `commit-es` (rol: generar mensajes de commit en español) ocurridas en otra sesión anterior. No es teoría releída — es el transcript mismo como evidencia.

**Sintaxis real de invocación** (confirmada en el log, no en la documentación):

```
Agent({
  description: "Generar mensaje de commit en español para el diff actual",
  subagent_type: "commit-es",
  prompt: "<texto que el orquestador construye explícitamente>"
})
```

El único canal de entrada es `prompt` — no hay ningún otro dato que "se filtre" desde la sesión que invoca.

Tres hallazgos que **no** se ven leyendo el `.md` del agente ni la documentación, solo agregando los seis `toolStats` reales:

1. **El modelo del subagente no hereda el del orquestador.** A mitad de la sesión auditada, el modelo del orquestador cambió de `claude-fable-5` a `claude-opus-4-8` (visible en el campo `model` de sus propios mensajes) — pero el subagente **resolvió siempre a `claude-sonnet-5`**, las seis veces. El `model: sonnet` del frontmatter es una asignación fija del agente, no algo que herede o negocie con quien lo invoca.
2. **El harness añade su propia metadata, separada de la respuesta del agente.** Lo que el orquestador recibe de vuelta no es solo el texto que produjo `commit-es` — el `tool_result` trae un bloque extra (`agentId`, `usage` con `subagent_tokens`/`tool_uses`/`duration_ms`) que **añade el framework**, no el subagente. Quien orquesta tiene que distinguir "esto lo dijo el agente" de "esto lo contabiliza el harness".
3. **El paso documentado no fue el paso ejecutado — y aun así el resultado fue correcto.** El `.md` de `commit-es` ordena como primer paso "lee `CLAUDE.md`". En el log real, `readCount:0` en 5 de las 6 invocaciones: el subagente **nunca llamó a `Read` sobre `CLAUDE.md`**. Cumplió las reglas del proyecto (sin coautoría IA, tipos de commit correctos) igual, porque el **orquestador ya las había pre-digerido dentro del `prompt`** que le escribió ("tipo esperado: docs", "sin footer de coautoría"). La conformidad vino del diseño del prompt de invocación, no de que el subagente releyera el archivo — el paso 1 del `.md` fue, en la práctica, letra muerta.

🟡 La lección que atraviesa los tres hallazgos: **lo que un subagente "hace" en la práctica puede diferir de lo que su propio `.md` promete**, y quien orquesta no puede asumir que el subagente compensará un prompt incompleto solo porque el archivo dice que debería. Esto es el mismo principio del Capítulo 4 (permisos y flujo) aplicado a subagentes: confía, pero verifica con el log, no con la documentación de intención.

Bonus observado en el mismo audit: en una de las seis invocaciones, el orquestador sugirió el tipo de commit `chore` (por convención de `.gitignore`), pero el subagente **razonó distinto y eligió `fix`** porque el cambio real cerraba un hueco de seguridad de datos — ejemplo concreto de "qué decide él mismo" de la tabla de §7.6.

## 7.7 Skills: recetas que Claude sabe seguir

MCP le da a Claude más **manos** (§7.1–7.5); un subagente le da **otro par de manos con su propia mesa** (§7.6). Una **skill** es la tercera vía y no es ni lo uno ni lo otro: es una **receta** — un procedimiento repetible, escrito una vez, que Claude (o tú) invoca cuando la tarea encaja. La tabla de decisión de §7.8 lo resume así: *le falta una **capacidad** → MCP; le sobra **trabajo** → subagente; le falta **procedimiento** → skill.*

🔵 Anatomía (fuente: `code.claude.com/docs/en/skills`). Una skill es una carpeta con un archivo **`SKILL.md`**: frontmatter YAML con dos campos **obligatorios** — `name` (≤64 caracteres, debe coincidir con el nombre de la carpeta) y `description` (el texto que Claude lee para decidir cuándo activarla) — y un **cuerpo** en Markdown con las instrucciones. Puede además traer archivos y scripts auxiliares en la misma carpeta.

🟡 La diferencia con un subagente, en una frase: **el subagente es *quién*; la skill es *el instructivo*.** El mismo Claude sigue la skill sin abrir otra ventana de contexto; un subagente sí es otro Claude aparte con su propia mesa.

### 7.7.1 De dónde salen: los cuatro orígenes

Esta subsección nació de una pregunta concreta del usuario — *"¿cómo accede Claude a las skills que subí a Claude online?"* — y la respuesta corta es: **no las lee de tu disco; llegan desde el servidor, atadas a tu cuenta.** Los cuatro orígenes posibles, con lo que encontré **de primera mano** en esta máquina (2026-07-18):

| Origen | Dónde vive | ¿Compartida? | 🟢 En esta máquina |
|---|---|---|---|
| **Personal (disco)** | `~/.claude/skills/` | Privada tuya | **No existe la carpeta** |
| **Proyecto (disco)** | `.claude/skills/` (+ anidadas en subcarpetas, para monorepos) | Sí, vía Git | **No existe la carpeta** |
| **Plugin / marketplace** | `~/.claude/plugins/marketplaces/<marketplace>/…` | Según el marketplace | **1 marketplace** (`claude-plugins-official`) |
| **Cuenta / organización** | **Servidor de Anthropic** (subidas en claude.ai o el panel de tu organización) | Según tu cuenta/org | **No en disco, pero sí en mi lista** |

🔵 Los tres primeros orígenes y sus rutas están documentados (`code.claude.com/docs/en/skills` y `.../discover-plugins`); las skills de cuenta/organización se guardan **del lado servidor** — al subirlas por *Ajustes → Personalizar* del app de escritorio o el panel de administración de la organización, **no se copian a tu disco** (fuente: Centro de ayuda de Claude, *Provision and manage skills* / *Use skills in Claude*).

**La prueba de primera mano** de que ese cuarto origen existe está en esta misma sesión: en mi lista de skills disponibles aparece `estandarizacion-construccion-sql` — que es claramente *tuya o de tu organización* (habla de Oracle 23c, tablas `DIM_`/`FCT_`/`STG_`, DWH, Power BI), no una skill genérica de Anthropic. 🟢 Verifiqué en disco las tres ubicaciones locales posibles y ninguna la contiene (ni existen las carpetas de skills):

```bash
ls ~/.claude/skills/                         # → (no existe la carpeta)
ls .claude/skills/                           # → (no existe la carpeta) en el proyecto
find ~/.claude -iname "*estandarizacion*"    # → (nada): tampoco está como plugin
```

🟢 **No está en ningún archivo local, y aun así puedo invocarla.** La única explicación es que llegó desde tu cuenta de Claude, por el servidor. (La carpeta `~/.claude/projects/…IA-skills/` que sí aparece son *transcripts* `.jsonl` de una sesión donde la construiste —el mismo tipo de archivo del workaround de §6.7—, no la skill en sí.)

> [!important] El matiz que lo cambia todo: app/Cloud sí, CLI no
> 🔵 Las skills de cuenta/organización **solo bajan a las sesiones del app de escritorio (Cowork) y a las sesiones en la nube**; una sesión **de CLI pura** (`claude` en tu terminal) ve *únicamente* los archivos locales (`~/.claude/skills/` y `.claude/skills/`) — **no** recibe las de tu cuenta (fuente: Centro de ayuda de Claude).
> 🟢 Confirmado de primera mano en **los dos lados** de la frontera, misma carpeta (el vault):
> - **App (esta sesión):** `estandarizacion-construccion-sql` aparece en mi lista pese a no existir en disco.
> - **CLI (`claude` en la terminal, v2.1.211):** el comando `/skills` reporta **"No skills found"** y solo señala `.claude/skills/` y `~/.claude/skills/` como orígenes — la skill de cuenta **no aparece** (verificado en la sesión CLI del usuario, 2026-07-18).
>
> Es la misma frontera app↔CLI del protocolo de dos sesiones (Cap. 6 §6.6): la sesión cronista (app) ve las skills de tu cuenta; una CLI cruda, no.

**Cómo "accede" Claude, con precisión.** Igual que con los esquemas de herramientas MCP (§7.5, *tool search*), hay **dos niveles** — y esto es *progressive disclosure*:
1. 🔵 **Descubrimiento** (siempre): el harness me entrega, al arrancar el turno, solo el **nombre + la descripción de una línea** de cada skill habilitada. Todavía **no tengo su contenido**; la descripción es mi único criterio para decidir cuándo activarla (por eso son tan detalladas).
2. 🔵 **Carga real** (solo al invocar): cuando la tarea encaja, llamo a la herramienta `Skill` (o tú escribes `/nombre-de-la-skill`), y **ahí** el cuerpo completo y sus archivos entran en mi contexto.

🟡 La biblioteca otra vez: la lista de skills es el **índice** — veo todos los lomos con su resumen de contraportada, pero el libro sigue en el estante hasta que pido uno concreto (invocarlo). No leo la biblioteca entera "por si acaso": sería insostenible para la ventana de contexto (Cap. 3).

### 7.7.2 Cómo mantenerlas siempre actualizadas

No hay un único botón de "actualizar": mantener una skill al día depende de **por cuál de los cuatro orígenes llega**. Origen por origen:

**a) Skills en disco (personal y proyecto).**
🔵 Editar el contenido de un `SKILL.md` existente se detecta **en caliente, sin reiniciar** la sesión (Claude Code vigila los cambios). La excepción: si creas la carpeta `skills/` de nivel superior *por primera vez* cuando antes no existía, suele hacer falta reiniciar la sesión para que el watcher la detecte. 🟢 Es el mismo comportamiento que vimos al crear el subagente `revisor-capitulos` en §7.6 — el watcher lo registró sin reiniciar. Para skills de **equipo**, versiónalas en `.claude/skills/` dentro del repo: "actualizar" se vuelve un `git pull` normal (misma lógica que `.mcp.json` en §7.4). Es el método más robusto: la fuente de verdad es Git y todo el equipo converge al hacer pull.

**b) Skills de plugin / marketplace.**
🔵 Se actualizan actualizando su marketplace o su plugin, desde la sesión (fuente: `.../discover-plugins`):
```
/plugin marketplace list              # ver marketplaces configurados
/plugin marketplace update            # actualizar TODOS
/plugin marketplace update <nombre>   # actualizar uno (p. ej. claude-plugins-official)
/plugin update <plugin>               # reinstalar un plugin con su manifiesto más reciente
```
🟢 En esta máquina, el marketplace `claude-plugins-official` está declarado en `~/.claude/plugins/known_marketplaces.json` como un **repo de GitHub** (`anthropics/claude-plugins-official`) con un campo `lastUpdated` (`2026-07-14`). "Actualizar" es, literalmente, traer la última versión de ese repo — por eso versionar en Git y actualizar son la misma idea vista desde dos ángulos.

**c) Skills de cuenta / organización (las que subiste a Claude online).**
🔵 La fuente de verdad **no está en tu disco**, sino en tu **biblioteca de Claude en la web / app de escritorio** (*Ajustes → Personalizar*) o, para las de organización, en el panel de administración. Actualizar = **volver a subir** la nueva versión ahí (las de organización las re-sube un admin y se provisionan a todos).
⚪ **Hueco honesto:** la documentación oficial **no especifica** el mecanismo ni el *tiempo* exacto de propagación a las sesiones, ni ofrece historial de versiones o rollback documentado. En la práctica, una **sesión nueva** toma el conjunto de skills habilitado en ese momento — así que, tras re-subir, lo fiable es **abrir una sesión nueva** para confirmar que corres la última versión. No especulamos más allá de eso: es un punto a verificar con Anthropic.

> [!tip] Principio que unifica los tres casos
> 🟡 Una skill "es solo archivos". Para lo que controlas en disco (personal, proyecto, plugin), **mantenerla al día = control de versiones**: edita el `SKILL.md`, versiónalo en Git, haz pull. Para lo que vive en el servidor (cuenta/org), la **biblioteca online es la autoridad**: re-subes ahí y una sesión nueva la recoge. La trampa a evitar es tener *dos copias divergentes* de la misma skill —una local y una en tu cuenta— sin saber cuál gana; por eso conviene decidir, por skill, cuál es su único origen de verdad.

🔎 **Cómo saber qué tienes cargado ahora mismo.** 🔵 En una sesión puedes listar las skills activas con `/skills` (y `/help` muestra además comandos y prompts de servidores MCP). 🟢 En esta sesión de app, ese inventario mezcla las de Anthropic de fábrica (`docx`, `pdf`, `xlsx`…) con tu `estandarizacion-construccion-sql` de cuenta — juntas, porque para mí todas son "skills disponibles" sin importar su origen.

> [!note] Práctica sugerida (en tu sesión de CLI)
> Según el protocolo de dos sesiones, pruébalo tú en tu terminal: abre `claude`, ejecuta `/skills` para ver el inventario y `/plugin marketplace update` para traer lo último del marketplace. Contrasta la lista de `/skills` de la CLI con la que ves en el app: si `estandarizacion-construccion-sql` aparece en el app pero **no** en la CLI, acabas de comprobar de primera mano la frontera app↔cuenta de §7.7.1.

## 7.8 ¿Herramienta nativa, MCP, subagente o skill?

Con el mapa completo, la decisión práctica (🟡 guía simplificada, completando la tabla del Capítulo 6 §6.2):

| Necesitas... | Usa | Por qué |
|---|---|---|
| Actuar sobre archivos y terminal locales | Herramientas nativas | Ya están, sin configurar nada |
| Hablar con un sistema externo (GitHub, BD, docs, Teams...) | Servidor MCP | Acceso directo y actualizado, sin copiar/pegar |
| Una tarea grande que contaminaría tu contexto, o trabajo en paralelo | Subagente | Contexto aislado, devuelve solo el resumen |
| Un procedimiento repetible con instrucciones fijas | Skill | Receta versionable, invocable por ti o por Claude |

La pregunta operativa que lo resume: *¿le falta una **capacidad** (→ MCP), le sobra **trabajo** (→ subagente), o le falta **procedimiento** (→ skill)?*

## 7.9 Cómo se invoca —y cómo garantizar— una skill o un agente

Pregunta que surgió en vivo en esta sesión: *"¿por qué a veces no se llama a un agente o una skill, y cómo hago que se use siempre?"* La respuesta corta es que **no hay magia**: 🔵 una skill o un subagente se activa solo si algo lo **dispara**, y hay tres disparadores — de más **duro** (determinista, lo ejecuta el harness) a más **blando** (a criterio del modelo).

### Los tres disparadores, de duro a blando

**1. Hook — determinista, lo ejecuta el harness (lo más duro).**
🔵 Un *hook* es un comando que Claude Code ejecuta **automáticamente** en un punto del ciclo de vida de la sesión, configurado en `settings.json` (`~/.claude/settings.json` global · `.claude/settings.json` de proyecto · `.claude/settings.local.json` privado). Su valor es el **determinismo**: lo corre el harness **siempre** que se cumpla la condición, sin depender de mi juicio (fuente: `code.claude.com/docs/en/hooks`). Se engancha a eventos como `PreToolUse` (antes de una herramienta), `PostToolUse`, `UserPromptSubmit` o `SessionStart`, y filtra por un `matcher` (p. ej. el nombre de la herramienta).

Aquí el matiz **crucial** y honesto: 🔵 **un hook NO puede invocar una skill ni un subagente.** La documentación lo dice explícito — los hooks se comunican solo por stdout/stderr y códigos de salida, y *"no pueden disparar comandos `/` ni llamadas a herramientas"*. Lo que un hook **sí** puede hacer son dos cosas:
- **Bloquear** una acción de forma determinista: un `PreToolUse` que termina con código de salida `2`, o que devuelve JSON con `permissionDecision: "deny"` y un `permissionDecisionReason`, cancela la llamada a la herramienta.
- **Inyectar texto** que yo leo como contexto (`additionalContext`), por ejemplo en `UserPromptSubmit` o `SessionStart`.

Entonces, ¿cómo se usa un hook para "forzar" que los commits pasen por la skill? 🟡 Aplicando las piezas documentadas de arriba a este caso, el patrón sería un `PreToolUse` que **matchea** `Bash` con un `git commit` y lo **deniega** con un motivo tipo *"usa la skill git-commit-es"*. El hook **garantiza que el atajo crudo se bloquea**; que yo luego elija la skill sigue siendo decisión mía. 🟡 Es decir: el hook da determinismo **en el bloqueo y en el empujón, no en la invocación de la skill**. Ni el escalón más duro garantiza al 100% el *uso* de la herramienta correcta — garantiza que no puedas saltártela por la puerta de atrás.

**2. Instrucción en `CLAUDE.md` — la leo, normalmente la sigo (intermedio).**
🔵 `CLAUDE.md` se carga en cada sesión de Claude Code; una instrucción como *"para commitear, usa la skill git-commit-es"* la **leo y por defecto la cumplo** — pero es una guía, no un candado: depende de mi juicio (fuente: `code.claude.com/docs/en/memory`). 🟢 Es exactamente lo que hicimos hace un momento en esta sesión: añadimos la convención de commits a `CLAUDE.md` para que toda sesión futura la conozca al arrancar.

**3. Coincidencia de `description` — a criterio del modelo (lo más blando).**
🔵 Sin hook ni instrucción, una skill o un subagente se ofrece por su campo **`description`**: si la tarea encaja con ella, la activo; si no, no. 🟡 Por eso *"a veces no se llaman"*: si la `description` no coincide con lo que se pide, o no lo juzgo pertinente, simplemente no se invoca — no es un fallo, es el mecanismo. (Es el mismo *progressive disclosure* de §7.7.1: primero veo la ficha, luego decido cargar el contenido.)

### El caso real de esta sesión 🟢

Ejemplo vivo de por qué el escalón blando a veces basta: cuando me pediste crear un commit, **usé la skill `git-commit-es` en mi contexto, no el subagente `commit-es`** — y fue lo correcto. La `description` de la skill coincide con "crear commit", y como el *porqué* del commit vivía en nuestra conversación (no en el diff), tenerlo en mi contexto produjo un mejor mensaje que aislarlo en un subagente. Ningún disparador más duro hacía falta. (Ese subagente `commit-es`, además, resultó ser una copia sin adaptar de otro repo y lo eliminamos en esta misma sesión.)

### La escalera de garantía: cómo acercarse a "siempre"

🟡 Ningún mecanismo **por sí solo** garantiza que se use una skill concreta. Para acercarte, se **combinan** de duro a blando:

| Escalón | Mecanismo | Qué garantiza |
|---|---|---|
| Más duro | **Hook** `PreToolUse` que bloquea el atajo crudo | Que no puedas saltarte la vía correcta (determinista) |
| ↓ | **Permisos** (Cap. 4) que solo permiten las herramientas de la skill | Cierra vías alternas |
| ↓ | **`CLAUDE.md`** que instruye usar la skill | Que yo lo lea y por defecto lo cumpla |
| Más blando | **`description`** afinada | Que el disparo natural ocurra sin fricción |

El principio de fondo: 🟡 **el determinismo vive en el harness (hooks, permisos); el juicio vive en el modelo (`description`, `CLAUDE.md`).** Cuanto más arriba en la escalera, menos depende de mi criterio — pero incluso arriba del todo, "bloquear el atajo" y "usar la herramienta correcta" son cosas distintas, y por eso la garantía total exige combinar escalones, no elegir uno.

## 7.10 El expediente `claude mcp serve`: el método de la guía en acción

Este apartado existe porque el proceso de investigación de esta guía produjo una **contradicción real**, y resolverla es la mejor demostración de para qué sirven los marcadores del Capítulo 1 §1.6.

- Múltiples fuentes de terceros (blogs técnicos, repos de GitHub) afirman, de forma consistente entre sí, que existe `claude mcp serve`: un comando que expondría las propias herramientas de Claude Code como servidor MCP para que *otros* clientes se conecten a él.
- 🟢 Verificación de primera mano (hoy, 2026-07-06): descargué la **referencia CLI oficial** y la **página oficial de MCP** completas. `claude mcp serve` **no aparece en ninguna de las dos**. Los subcomandos documentados son `add`, `add-json`, `list`, `get`, `remove`, `login`, `logout` y `reset-project-choices`.
- 🔵 Lo que sí existe documentado en la dirección "Claude Code como servidor" es otra cosa: `claude remote-control` (controlar tu sesión desde claude.ai/móvil) y `claude gateway` (gateway auto-hospedado empresarial).

Veredicto: ⚪ **no confirmado en la documentación oficial al momento de escribir esta guía.** Quizá existió y se retiró, quizá nunca existió, quizá está sin documentar — no se sabe, y la guía no especula. Si lo ves afirmado en un blog, ya sabes exactamente cuánta confianza asignarle y cómo verificarlo tú mismo (§6.1: la fuente matriz).

> [!note] La lección meta
> No es un apartado sobre un comando; es un apartado sobre **cómo saber qué es verdad** en un ecosistema que cambia cada semana. Fuentes de terceros consistentes entre sí pueden estar todas copiándose el mismo dato viejo. La cadena correcta es: afirmación → ¿está en `code.claude.com/docs`? → si no, marcador ⚪ y a otra cosa.

## 7.11 Práctica para ti: tu primer servidor MCP desde la CLI

Según nuestro protocolo de dos sesiones, esto lo ejecutas **tú, en tu terminal** (sirve cualquier carpeta; sugiero la del vault). Es reversible y sin riesgo: el servidor es oficial de Microsoft, de solo lectura y sin autenticación — y el endpoint lo confirmamos en vivo en §7.5.

```bash
# 1. ¿Qué servidores tienes configurados ahora? (probablemente ninguno)
claude mcp list

# 2. Conecta el servidor MCP de Microsoft Learn (scope local por defecto)
claude mcp add --transport http microsoft-learn https://learn.microsoft.com/api/mcp

# 3. Inspecciónalo
claude mcp get microsoft-learn

# 4. Úsalo de verdad: abre una sesión y pregunta algo que lo obligue a usarlo
claude
#   > dentro: /mcp   (mira el panel de gestión)
#   > "busca en la documentación de Microsoft cómo funciona DAX en Power BI"

# 5. Si no quieres conservarlo:
claude mcp remove microsoft-learn
```

Qué observar mientras lo haces: el prompt de permiso la primera vez que Claude invoca la herramienta (Capítulo 4 en acción), el nombre `mcp__microsoft-learn__...` en la llamada, y que la respuesta llega como datos estructurados que Claude interpreta.

## Resumen del capítulo

- **MCP** es un estándar abierto (Anthropic, nov. 2024) que convierte el problema N×M de las integraciones en N+M: el sistema externo publica un servidor una vez, cualquier cliente compatible se conecta.
- Un servidor expone **tools** (`mcp__servidor__herramienta`), **resources** (`@servidor:...`) y **prompts** (`/mcp__servidor__prompt`); puede además pedir input estructurado, empujar eventos e inyectar instrucciones de uso.
- **Transportes**: HTTP (recomendado remoto), stdio (procesos locales), WebSocket (eventos push); SSE está deprecado.
- **Scopes**: local (privado, proyecto actual), project (`.mcp.json`, compartido vía Git, requiere aprobación), user (todos tus proyectos). Precedencia: local > project > user.
- Por defecto los esquemas de herramientas MCP se cargan **bajo demanda** (tool search) para proteger la ventana de contexto — lo vimos ocurrir en vivo dos veces en esta sesión.
- Los **subagentes** son la otra vía de extensión: contexto propio, corren en background, devuelven solo un resumen. MCP extiende las manos; el subagente es otro par de manos.
- **Definir el tuyo** = un archivo Markdown único `.claude/agents/<nombre>.md` (proyecto) o `~/.claude/agents/<nombre>.md` (usuario): frontmatter YAML con `name` y `description` obligatorios (+ `tools`/`model` opcionales) y un cuerpo que es su system prompt. En esta sesión creamos `revisor-capitulos` como ejemplo real (§7.6).
- **No confundir subagentes con llamadas paralelas a herramientas**: cuando Claude lanza varios comandos al mismo tiempo sin esperar entre ellos, lo hace él mismo en su contexto (como abrir 3 terminales a la vez). Un subagente es otro Claude aparte. Regla: resultados pequeños que necesitas aquí → paralelo; exploración masiva o trabajo en segundo plano → subagente.
- **Auditoría forense de seis invocaciones reales** (§7.6): el modelo del subagente no hereda el del orquestador (queda fijo por frontmatter), el harness añade metadata propia (`agentId`/`usage`) separada de la respuesta del agente, y el paso "lee `CLAUDE.md`" del `.md` nunca se ejecutó como `Read` real — el cumplimiento vino del prompt que armó el orquestador, no de una relectura del archivo.
- Las **skills** (§7.7) son la tercera vía de extensión: una **receta** repetible (`SKILL.md` = frontmatter `name`+`description` obligatorios + cuerpo Markdown). El subagente es *quién*; la skill es *el instructivo* que el mismo Claude sigue sin abrir otra ventana de contexto.
- **Skills, cuatro orígenes**: personal (`~/.claude/skills/`), proyecto (`.claude/skills/`, vía Git), plugin/marketplace, y **cuenta/organización** (servidor de Anthropic, subidas en claude.ai). Las de cuenta **no viven en tu disco**: llegan al app/nube desde el servidor — probado en vivo, `estandarizacion-construccion-sql` aparece en la lista pero no existe en `~/.claude`. **Una CLI pura no ve las de cuenta**; solo los archivos locales.
- **Acceso a skills = descubrimiento (nombre+descripción, siempre) vs carga (contenido, solo al invocar con `Skill` o `/nombre`)** — el mismo *progressive disclosure* del tool search MCP.
- **Mantenerlas actualizadas** depende del origen: en disco → editar el `SKILL.md` (se recoge en caliente) y versionar en Git; plugin → `/plugin marketplace update` y `/plugin update`; cuenta/org → re-subir en la biblioteca web y abrir sesión nueva (⚪ el mecanismo exacto de propagación no está documentado). `/skills` lista lo activo en la sesión.
- **Cómo se invoca una skill/agente** (§7.9): tres disparadores de duro a blando — **hook** (determinista, lo ejecuta el harness), **instrucción en `CLAUDE.md`** (la leo y por defecto la cumplo), **coincidencia de `description`** (a mi criterio). Por eso "a veces no se llaman": si nada los dispara, no se invocan.
- **El matiz del hook**: 🔵 un hook **no puede invocar una skill ni un subagente**; solo puede **bloquear** una acción (código de salida `2` o `permissionDecision: "deny"`) e **inyectar texto** que leo. Para "forzar" la skill de commits se bloquea el `git commit` crudo con un `PreToolUse` y se me empuja a la skill — determinismo en el bloqueo, no en la invocación. La garantía total exige **combinar** escalones (hook + permisos + CLAUDE.md + description).
- `claude mcp serve` es el caso de estudio epistemológico de la guía: reportado por terceros, ausente de la documentación oficial → ⚪.

## Analogía

Piensa en un taller mecánico. Las herramientas nativas de Claude son las de **su propio cinturón**: siempre las lleva puestas. MCP es la **regleta de enchufes normalizados** de la pared del taller: como la norma del enchufe es pública, cualquier fabricante — incluso la competencia — puede construir una estación especializada (el elevador de Microsoft, el compresor de GitHub) y enchufarla; el mecánico la usa al instante, sin adaptadores. El catálogo de estaciones disponibles es el registro de conectores. Y los scopes son dónde guardas cada aparato: tu **cajón personal en este taller** (local), el **tablero compartido del taller que todo el equipo ve en el inventario** (project, `.mcp.json` en Git), o tu **maletín que llevas a todos los talleres** (user). Un subagente, en cambio, no es un aparato: es **otro mecánico con su propia mesa**, al que le pasas la orden de trabajo y te devuelve el coche listo — sin que tengas que ver cada tuerca que tocó. Y una **skill** es la **ficha de procedimiento plastificada** colgada junto a la estación: "cómo hacer la revisión de 20 puntos", "cómo escribir un commit según nuestra norma". El mecánico la descuelga y la sigue paso a paso cuando el trabajo la pide. Algunas fichas son **tuyas, escritas a mano y guardadas en el cajón** (skills en disco); otras vienen en el **manual del fabricante** (plugin/marketplace); y otras están en el **tablón central de tu empresa** que la administración actualiza para todos los talleres a la vez (skills de cuenta/organización) — ese tablón es online, no está en tu cajón, y por eso tu maletín personal en un taller sin conexión a la red de la empresa (la CLI) no lo ve.

## Autoevaluación

1. ¿Qué problema resuelve MCP (en términos de N y M) y cuáles son las tres primitivas que un servidor puede exponer?
2. ¿Qué transporte usarías para: (a) un servicio en la nube, (b) un script propio en tu máquina, (c) un servidor que te empuja eventos? ¿Y cuál está deprecado?
3. Quieres que todo tu equipo tenga el mismo servidor MCP al clonar el repo: ¿qué scope usas, en qué archivo queda, y qué le pasará a cada compañero la primera vez que abra el proyecto?
4. ¿Qué evidencia de esta misma sesión demuestra (a) la convención de nombres de herramientas MCP y (b) la carga diferida de esquemas?
5. ¿Por qué esta guía se niega a afirmar que `claude mcp serve` existe, pese a que varias fuentes lo describen igual? ¿Qué marcador le corresponde y cuál es la cadena de verificación correcta?
6. Te piden analizar 40 archivos SQL del DWH para un resumen ejecutivo, y aparte consultar el estado de unos tickets en Jira. ¿Qué mecanismo corresponde a cada parte y por qué?
7. *(Añadida §7.6)* Claude necesita verificar 5 cosas rápidas sobre la estructura de un directorio para documentarla. ¿Debería lanzar un subagente o usar llamadas paralelas a herramientas? ¿Y si en vez de 5 cosas rápidas fueran 200 archivos que necesita leer completos?
8. *(Añadida)* ¿Cuáles son los dos únicos campos **obligatorios** del frontmatter de un subagente, y qué ocurre si **omites** el campo `tools`?
9. *(Añadida §7.6, auditoría forense)* Un subagente tiene `model: sonnet` en su frontmatter. Si el orquestador que lo invoca está corriendo con `claude-opus-4-8`, ¿con qué modelo corre el subagente? Y si el `.md` del subagente ordena "primero lee `CLAUDE.md`" pero el log muestra `readCount:0`, ¿significa eso que el subagente incumplió las reglas del proyecto? ¿Por qué sí o por qué no?
10. *(Añadida §7.7)* Subiste una skill a tu cuenta de Claude (claude.ai). ¿Está guardada en tu disco? ¿Por qué la ve esta sesión del app pero no la vería una sesión de `claude` en tu terminal? Y explica la diferencia entre que Claude *descubra* una skill y que la *cargue*.
11. *(Añadida §7.7)* Tienes tres skills que actualizar: una en `.claude/skills/` del repo del equipo, una que vino de un marketplace, y una que subiste a tu cuenta online. ¿Cuál es la vía de actualización de cada una, y cuál de las tres tiene un mecanismo de propagación **no documentado oficialmente**?
12. *(Añadida §7.9)* Quieres garantizar que **todos** los commits de tu repo pasen por la skill `git-commit-es`. ¿Puede un hook *invocar* la skill directamente? Si no, ¿qué es exactamente lo que un hook `PreToolUse` sí puede hacer, y por qué "bloquear el `git commit` crudo" no es lo mismo que "garantizar que se use la skill"? Ordena de más duro a más blando los mecanismos que combinarías.

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. Sin estándar, N modelos × M sistemas exigen N×M integraciones a medida; con MCP cada sistema publica un servidor una vez (N+M). Primitivas: tools (funciones que invoca el modelo), resources (datos de solo lectura que mencionas con `@`), prompts (plantillas que ejecutas como `/mcp__servidor__prompt`).
> 2. (a) HTTP — el recomendado, con OAuth; (b) stdio — proceso local; (c) WebSocket — bidireccional persistente. El deprecado es SSE.
> 3. Scope **project**: queda en `.mcp.json` en la raíz del repo, versionado en Git. Cada compañero verá un prompt de aprobación la primera vez, porque la configuración viene de terceros vía Git (medida anti-abuso documentada).
> 4. (a) La búsqueda en el registro se hizo con la herramienta llamada literalmente `mcp__mcp-registry__search_mcp_registry`; (b) antes de llamar al buscador de Microsoft Learn hubo que cargar su esquema con `ToolSearch` — los esquemas no estaban precargados.
> 5. Porque no aparece en la documentación oficial (referencia CLI y página MCP, ambas verificadas de primera mano el 2026-07-06), solo en fuentes de terceros que pueden estar repitiéndose entre sí. Marcador: ⚪. Cadena: afirmación → buscarla en `code.claude.com/docs` → si no está, no afirmarla.
> 6. Los 40 archivos SQL → **subagente** (ej. `Explore`): exploración masiva que contaminaría el contexto principal; solo necesitas su conclusión. Los tickets → **servidor MCP de Jira**: es un sistema externo vivo, no archivos locales.
> 7. Las 5 cosas rápidas → **llamadas paralelas a herramientas**: los resultados son pequeños (~20 líneas), se necesitan en el contexto actual para razonar sobre ellos, y no justifican crear otro Claude. Se agrupan las independientes en lotes (ej. 3+2) para ejecutar en 2 turnos en vez de 5. Los 200 archivos → **subagente**: la lectura completa de 200 archivos desbordaría la ventana de contexto de la sesión principal; el subagente los procesa en su propio espacio y devuelve solo el resumen.
> 8. Solo `name` y `description`. Si omites `tools`, el subagente **hereda todas** las herramientas del agente padre; restringirlas (p. ej. a `Read, Grep, Glob`) es una decisión de seguridad activa que crea un subagente de solo lectura.
> 9. Corre con `claude-sonnet-5` — el `model` del frontmatter es una asignación fija del agente, no algo que herede del orquestador (confirmado en el log: el orquestador cambió de modelo a mitad de sesión y el subagente no varió). Y no, no necesariamente incumplió: `readCount:0` solo prueba que no llamó a `Read` sobre ese archivo — si el resultado final respetó las reglas del proyecto es porque el **orquestador las pre-digirió dentro del `prompt`** que armó para invocarlo. La lección es justamente que "documentado" y "ejecutado" pueden diferir sin que el resultado sea incorrecto — pero quien orquesta no puede darlo por hecho sin mirar el log.
> 10. **No**, no está en tu disco: las skills de cuenta viven en el **servidor de Anthropic** (se comprobó buscándola en `~/.claude` sin encontrarla, pese a ser invocable). El **app de escritorio (Cowork) y las sesiones en la nube** descargan las skills de tu cuenta; una **CLI pura** solo lee archivos locales (`~/.claude/skills/` y `.claude/skills/`), así que no la vería salvo que también existiera como archivo. *Descubrir* = tener su nombre + descripción en la lista (siempre, para decidir cuándo usarla); *cargar* = traer su contenido completo al contexto, y eso solo ocurre al invocarla (`Skill` o `/nombre`). Es *progressive disclosure*, igual que el tool search de MCP.
> 11. (a) La del repo → editar su `SKILL.md` (se recoge en caliente) y **versionarla en Git**: actualizar es `git pull`. (b) La de marketplace → `/plugin marketplace update <nombre>` (o `/plugin update <plugin>`), que trae la última versión del repo del marketplace. (c) La de tu cuenta → **re-subirla** en la biblioteca web/app y abrir una **sesión nueva** para recogerla. La tercera es la del **mecanismo no documentado**: la doc oficial no especifica cómo ni cuándo se propaga la nueva versión a las sesiones (⚪), ni ofrece rollback documentado.
> 12. **No**, un hook no puede invocar una skill (ni un subagente): la documentación es explícita en que los hooks se comunican solo por stdout/stderr y códigos de salida y no pueden disparar comandos `/` ni llamadas a herramientas. Un `PreToolUse` sí puede **bloquear** la acción (código de salida `2` o JSON con `permissionDecision: "deny"` + motivo) e **inyectar contexto** que el modelo lee. Por eso "bloquear el `git commit` crudo" ≠ "garantizar que se use la skill": el bloqueo es determinista, pero que el modelo *elija* la skill tras el bloqueo sigue siendo su juicio. De más duro a más blando: **hook** (bloquea el atajo) → **permisos** (cierran vías alternas) → **`CLAUDE.md`** (instruye) → **`description`** (dispara de forma natural). La garantía práctica surge de combinarlos, no de uno solo.

---
[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Siguiente → [[Claude Code Capitulo 08 - Comparativas]]
