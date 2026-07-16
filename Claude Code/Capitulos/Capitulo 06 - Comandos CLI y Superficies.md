---
tags:
  - claude-code
  - capitulo-06
  - cli
  - comandos
  - superficies
estado: completo
capitulo: 6
creado: 2026-07-06
---

# Capítulo 6 — Comandos, CLI y Superficies de Claude Code

[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Anterior: [[Capitulo 05 - Git y Control de Versiones]] · Siguiente → [[Capitulo 07 - MCP y Herramientas]]

> [!tip] Por qué existe este capítulo (y no estaba en el plan original)
> Surgió de una observación tuya, legítima y certera: llevábamos cinco capítulos hablando de Claude Code sin explicar cómo se usan sus comandos — y toda esta guía se ha construido desde una app, no desde la terminal. Este capítulo cierra ese hueco y te deja listo para **abrir la CLI en una sesión paralela y trabajar desde ahí**.

## 6.1 La fuente matriz: dónde vive la verdad oficial

Tu pregunta fue exactamente la correcta: *"¿cuál es la fuente a la que puedo ir siempre?"*. Respuesta directa:

🔵 **La documentación oficial de Claude Code es de Anthropic y vive en `https://code.claude.com/docs`**. Las URLs antiguas (`docs.claude.com/en/docs/claude-code/...`) redirigen ahí — si encuentras enlaces viejos en blogs, te llevarán al mismo sitio.

Páginas que conviene tener ancladas como referencia permanente:

| Página                   | URL                                     | Para qué                                                                 |
| ------------------------ | --------------------------------------- | ------------------------------------------------------------------------ |
| Overview / Quickstart    | `code.claude.com/docs/en/overview`      | Instalación y primer uso                                                 |
| **Referencia de la CLI** | `code.claude.com/docs/en/cli-reference` | Todos los comandos y flags — la página más importante para este capítulo |
| Slash commands / Skills  | `code.claude.com/docs/en/skills`        | Comandos de sesión y skills personalizadas                               |
| Settings                 | `code.claude.com/docs/en/settings`      | Todo lo configurable en `settings.json`                                  |
| Memoria                  | `code.claude.com/docs/en/memory`        | CLAUDE.md, reglas, auto-memory (lo del Capítulo 3)                       |
| MCP                      | `code.claude.com/docs/en/mcp`           | Lo que veremos en el Capítulo 7                                          |
| Novedades                | `code.claude.com/docs/en/whats-new`     | Changelog semanal — Claude Code cambia rápido                            |
|                          |                                         |                                                                          |

Regla de oro para filtrar fuentes: si el dominio no es `code.claude.com`, `platform.claude.com`, `claude.com`, `support.claude.com` o `anthropic.com`, **no es oficial** — puede ser útil, pero es de terceros y puede estar desactualizado o equivocado. (Esta guía misma es un ejemplo de fuente no-oficial: útil, pero su autoridad final siempre es la documentación de arriba.)

## 6.2 Tres cosas que se parecen y no son lo mismo

Antes del catálogo, una distinción que evita mucha confusión:

| Concepto | Quién lo invoca | Qué es | Ejemplo |
|---|---|---|---|
| **Herramienta (tool)** | El modelo, durante su turno | Una capacidad que Claude usa para actuar (leer, editar, ejecutar) | `Read`, `Edit`, `Bash` — todo lo visto en los Capítulos 2-5 |
| **Slash command** | Tú, escribiendo `/algo` | Un control de la sesión: cambia configuración, dispara acciones, abre paneles | `/model`, `/compact`, `/clear` |
| **Skill** | Tú (`/nombre`) o Claude automáticamente | Un paquete de instrucciones reutilizable, definido en un archivo (`SKILL.md`) | `/code-review`, o las skills de tu repo institucional (`estandarizacion-construccion-sql`) |

🟡 *Simplificación útil*: las herramientas son las **manos** de Claude; los slash commands son los **controles del tablero** que tú operas; las skills son **recetas guardadas** que cualquiera de los dos puede poner en marcha.

## 6.3 Catálogo esencial de slash commands

🔵 Catálogo documentado (el completo está en la referencia oficial; estos son los que más vas a usar, agrupados por función):

**Sesión y contexto**
- `/clear` — limpia la conversación (los archivos editados quedan intactos).
- `/compact` — compacta manualmente el historial (Capítulo 3 §3.6, sin esperar a que ocurra solo).
- `/context` — muestra qué está ocupando la ventana de contexto ahora mismo.
- `/rewind` — vuelve a un punto anterior (checkpoints de la sesión), incluso recupera de un `/clear` accidental.
- `/cd` — cambia el directorio de trabajo sin romper la sesión.

**Configuración**
- `/model` — cambia el modelo a mitad de sesión.
- `/config` — ajustes simples (tema, modelo por defecto, etc.).
- `/permissions` — panel de reglas de permisos (allow/ask/deny del Capítulo 4).
- `/memory` — ver y editar CLAUDE.md, reglas y auto-memory (todo el Capítulo 3, en un panel).
- `/mcp` — gestionar servidores MCP (Capítulo 7).
- `/hooks` — ver los hooks registrados.
- `/effort` — nivel de esfuerzo de razonamiento del modelo.

**Trabajo**
- `/init` — genera un CLAUDE.md analizando el repo (lo que hicimos a mano en el Capítulo 1, automatizado).
- `/plan` — entra en Plan Mode formal (Capítulo 4 §4.3 — la función, no el patrón).
- `/code-review`, `/security-review`, `/simplify` — revisiones del diff actual.
- `/goal` — fija una condición de cierre y mantiene a Claude trabajando hasta cumplirla.
- `/loop`, `/schedule`, `/batch` — recurrencia, programación y cambios masivos en paralelo.

🟢 *Observable en esta sesión — y es el dato clave de este capítulo*: no todos estos comandos existen en todas las superficies. En esta conversación, por ejemplo, los comandos que abren paneles interactivos de terminal (`/permissions`, `/config`, `/hooks`, `/doctor`, `/agents`) **no están disponibles** — mis propias instrucciones de sesión lo declaran explícitamente. Y `/goal`, que motivó en parte este capítulo, tampoco aparece en la lista de skills invocables de esta sesión. El catálogo completo vive en la CLI; las apps exponen subconjuntos.

## 6.4 La CLI propiamente: instalación y anatomía

🔵 Instalación en Windows (tu caso) — dos vías documentadas:

```powershell
# Opción A: instalador nativo (PowerShell)
irm https://claude.ai/install.ps1 | iex

# Opción B: vía npm (requiere Node.js)
npm install -g @anthropic-ai/claude-code
```

(macOS/Linux tienen equivalentes con `curl`, Homebrew y gestores de paquetes — detalle exacto en la página de instalación oficial.)

Una vez instalada, la anatomía básica:

```bash
claude                        # sesión interactiva en la carpeta actual
claude -p "explica este repo" # modo headless: responde y termina (scripteable, se puede encadenar con pipes)
claude -c                     # continúa la conversación más reciente de esta carpeta
claude --resume               # elige qué sesión anterior retomar
claude --permission-mode plan # arranca directamente en Plan Mode
claude mcp list               # gestiona servidores MCP desde fuera de la sesión
claude agents                 # panel de todas las sesiones en background
claude update                 # actualiza la CLI
```

Y dentro de la sesión interactiva, los controles de teclado más importantes:

- **Shift+Tab** — cicla los modos de permiso sin escribir comandos.
- **Ctrl+B** — manda la tarea actual a segundo plano y te devuelve el control.
- **Esc** — interrumpe a Claude a mitad de turno (como hiciste tú en esta sesión, pero con tecla).
- **`!` al inicio** — ejecuta un comando de shell tú mismo, sin pasar por Claude.
- **`@`** — menciona archivos con autocompletado para meterlos al contexto.

🟢 *Observable de primera mano (2026-07-10, captura de una sesión CLI en otra carpeta del usuario)*: la barra inferior de la terminal muestra el catálogo completo de atajos, más amplio que el listado arriba:

| Atajo | Función |
|---|---|
| `!` | Modo shell (ejecutar comandos directamente) |
| `/` | Modo comandos (slash commands) |
| `@` | Rutas de archivo con autocompletado |
| `/btw` | Pregunta lateral sin desviar la tarea principal |
| Doble Esc | Limpiar el input actual |
| Shift+Tab | Auto-aceptar ediciones (ciclo de modos de permiso) |
| Ctrl+T | Mostrar/ocultar la lista de tareas (TaskList) |
| Ctrl+O | Salida detallada (verbose) |
| `\` + Enter | Insertar salto de línea dentro del input |
| Ctrl+Shift+_ | Deshacer (undo) |
| Alt+V | Pegar imágenes desde el portapapeles |
| Alt+P | Cambiar de modelo rápidamente |
| Ctrl+S | Guardar el prompt actual en un stash para reusarlo después |
| Ctrl+G | Editar el input en tu editor externo (`$EDITOR`) |
| `/keybindings` | Personalizar todos estos atajos |

🟡 Ninguno de estos apareció documentado como tal en el catálogo oficial que revisamos en el Capítulo 6 original — es una ampliación 🟢 que confirma lo dicho en el §6.3: el catálogo completo vive en la CLI, y crece con cada versión (aquí, v2.1.206).

🟡 El modo `-p` (headless) es lo que convierte a Claude Code en pieza de la filosofía Unix del Capítulo 1: `tail -200 app.log | claude -p "resume los errores"` lo trata como un comando más, componible con cualquier otro.

### Corrección en vivo: los nombres reales de los modos de permiso

🟢 *Observable de primera mano (2026-07-11, tres capturas consecutivas de la misma sesión CLI, v2.1.206)*: al presionar Shift+Tab varias veces seguidas, la barra inferior mostró el ciclo completo con **nombres distintos a los que documenté originalmente en esta sección**:

```
Shift+Tab →  "plan mode on"    (shift+tab to cycle)
Shift+Tab →  "auto mode on"    (shift+tab to cycle)
Shift+Tab →  "manual mode on"  · ← for agents
```

| Nombre mostrado en pantalla (v2.1.206) | Nombre que usé antes en esta guía | Qué significa |
|---|---|---|
| `manual mode` | `default` | Tú apruebas cada acción antes de que ocurra |
| `auto mode` | `acceptEdits` | Las ediciones de archivo se auto-aceptan; el resto sigue pidiendo permiso |
| `plan mode` | `plan` | Claude solo planea, no ejecuta, hasta que apruebes el plan completo |

🟡 **Corrección, no contradicción**: el comportamiento de los tres modos (Capítulo 4) no cambió — lo que cambió es la **etiqueta visible** en la barra de estado. Es coherente con lo que ya dijo el §6.3: Claude Code tiene changelog semanal, y los nombres de superficie evolucionan más rápido que el concepto subyacente. Corrijo aquí la nomenclatura vieja de esta guía con la evidencia más reciente.

⚪ **No verificado**: si `manual`/`auto`/`plan` son los nombres internos nuevos (los que usarías en `claude --permission-mode manual`) o solo etiquetas de UI mientras el flag sigue aceptando `default`/`acceptEdits`/`plan` por compatibilidad. Para confirmarlo, ejecutar `claude --permission-mode manual` y observar si es aceptado.

### `/model`, `/effort` y `/config`: tres comandos que se pisan (caso real)

🟢 *Observable de primera mano (2026-07-10)*: una secuencia real de comandos en una sesión CLI (`~\desc-curriculo-benchmarking-nacional-saber-pro`, Claude Code v2.1.206) expone cómo se relacionan — y se confunden — estos tres comandos:

```
> /model claude-opus-4-6
  Set model to Opus 4.6 and saved as your default for new sessions

> /effort medium
  Set effort level to medium (saved as your default): Balanced approach
  with standard implementation and testing

> /model sonnet
  Set model to Sonnet 5 and saved as your default for new sessions

> /config model=opus effortLevel=medium
  Set Model to opus
  effortLevel isn't a /config setting. Run /config to see what's available.

> /config model=claude-opus-4-6 effortLevel=medium
  Model takes one of: default, sonnet, opus, haiku, fable, best,
  sonnet[1m], opus[1m], fable[1m], opusplan. For a specific model ID, use /model.
  effortLevel isn't a /config setting. Run /config to see what's available.

> /model claude-opus-4-6
  Set model to Opus 4.6 and saved as your default for new sessions
```

**Qué revela esta secuencia:**

1. **`/model` acepta dos formatos**: un alias corto (`sonnet`, `opus`, `haiku`, `fable`, `best`, `default`) o un ID específico de modelo (`claude-opus-4-6`). Ambos cambian el modelo **y lo guardan como default** para sesiones futuras — no es un cambio de una sola vez.
2. **`/effort` es un comando independiente** — no un parámetro de `/model` ni de `/config`. Cambia el nivel de esfuerzo de razonamiento (aquí, `medium`: "enfoque equilibrado con implementación y pruebas estándar") y también se guarda como default.
3. **`/config` tiene su propio espacio de parámetros, distinto al de `/model`**: acepta `model=<alias>` (usando los mismos alias cortos, no IDs arbitrarios), pero **rechaza `effortLevel`** — ese parámetro no existe en `/config`, solo como comando propio (`/effort`). El mensaje de error es explícito y hasta sugiere la alternativa correcta ("For a specific model ID, use /model").
4. **La sesión termina donde empezó**: tras probar `/config` sin éxito, el usuario vuelve a `/model claude-opus-4-6` — el camino que sí funciona.

🟡 **La lección práctica**: `/model` y `/effort` son comandos de una sola responsabilidad cada uno; `/config` es un panel más general que **no** es un sinónimo de "todos los ajustes en un solo comando". Si un parámetro falla en `/config`, la CLI te lo dice y sugiere el comando correcto — vale la pena leer el mensaje de error en vez de asumir que el parámetro no existe en ningún lado.

⚪ **No verificado desde esta sesión**: la lista completa de parámetros válidos de `/config` (el mensaje solo reveló los valores válidos para `model`). Para verla completa, ejecutar `/config` sin argumentos, como el propio error sugiere.

### Recaps: resúmenes automáticos entre turnos

🟢 *Observable de primera mano (2026-07-11, misma sesión CLI del asistente GPT)*: entre turnos largos o con muchas herramientas de por medio, Claude Code inserta automáticamente una línea en cursiva atenuada que resume el estado de la tarea — sin que tú la pidas:

```
※ recap: Estamos ajustando el prompt del asistente de documentación de modelos
  de datos para manejar URLs sensibles y recomendar chats nuevos por
  documentación; ya aplicamos todos los cambios en instrucciones.md. Próximo
  paso: revisar el archivo completo contigo para confirmar que todo quedó bien.
  (disable recaps in /config)
```

**Qué revela esta captura:**

1. **El recap resume dos cosas**: qué se ha hecho hasta ahora y cuál es el próximo paso — como si Claude hiciera una pausa para que tú (o él mismo tras una interrupción) no pierdas el hilo.
2. **Se puede desactivar**: la pista `(disable recaps in /config)` confirma que es una preferencia configurable, no un comportamiento fijo.
3. **Aparece justo después del indicador de tiempo de razonamiento**, que en esta captura usa verbos de cocina en vez de "Thinking": *"Cogitated for 23s"*, *"Brewed for 6s"*, *"Sautéed for 29s"*. 🟡 Es un detalle de personalidad de la interfaz — probablemente un conjunto de verbos elegidos al azar para no repetir siempre la misma palabra.
4. **El idioma del recap varió dentro de la misma sesión**: uno apareció en español (calzando con el idioma de la conversación) y otro en inglés, en turnos distintos de la misma sesión. ⚪ No verificado por qué cambia — podría depender del turno que resume, no de una regla fija.

🟡 **Para qué sirve en la práctica**: si retomas una sesión larga tras un rato (o si delegaste a un subagente y vuelves), el recap más reciente te da el resumen sin tener que releer el scroll completo — cumple una función parecida a la compactación del Capítulo 3, pero visible en cada punto de control, no solo cuando el contexto se llena.

### Elicitation: cuando Claude te hace preguntas estructuradas en la CLI

🟢 *Observable de primera mano (2026-07-08, sesión CLI del asistente GPT):* además de texto libre, Claude puede hacerte **preguntas con opciones** dentro de la terminal — un formulario interactivo que aparece directamente en la línea de comandos. Técnicamente es la herramienta `AskUserQuestion` (la misma "elicitation" que documentamos en el [[Capitulo 07 - MCP y Herramientas#7.2 Qué expone un servidor las tres primitivas|Capítulo 7 §7.2]] como primitiva MCP), pero aquí la usa el propio Claude Code, no un servidor externo.

**Lo que ves en la terminal:**

```
← □ Pregunta 1   □ Pregunta 2   ✔Submit  →

Texto de la pregunta activa

 1. Opción A (Recomendado)
    Descripción de qué implica esta opción.
 2. Opción B
    Descripción alternativa.
 3. Type something.
───────────────────────────────
❯ 4. Chat about this

Enter to select · Tab/Arrow keys to navigate · ctrl+g to edit in Notepad · Esc to cancel
```

**Controles específicos de la elicitation:**

| Tecla | Función |
|---|---|
| **Tab / Flechas** | Navegar entre las opciones de una pregunta, y entre pestañas si hay varias preguntas |
| **Enter** | Seleccionar la opción actual |
| **Ctrl+G** | Abrir el texto en un editor externo (Notepad en Windows) — útil si necesitas escribir algo largo |
| **Esc** | Cancelar la elicitation sin responder |

**Las cuatro acciones posibles ante una pregunta:**
1. **Elegir una opción predefinida** — seleccionas y Enter.
2. **"Type something"** — escribes tu propia respuesta libre, para cuando ninguna opción encaja.
3. **"Chat about this"** — sales del selector y vuelves al flujo conversacional para discutir antes de comprometerte con una respuesta. Es la válvula de escape: si la pregunta no te queda clara o necesitas contexto, no estás obligado a elegir.
4. **Esc** — cancelas del todo.

🟡 En la app de escritorio, las preguntas con opciones se renderizan como botones clickeables — la misma herramienta, distinta presentación visual. Otra manifestación de la regla del §6.5: un motor, varias superficies.

## 6.5 El mapa de superficies: dónde puede vivir Claude Code

🔵 Documentado — el mismo motor, varias superficies:

| Superficie | Qué es | Cuándo conviene |
|---|---|---|
| **CLI (terminal)** | La experiencia original y más completa | Trabajo serio de desarrollo; automatización; scripts |
| **Extensión VS Code** | Panel dentro del editor, diffs visuales | Si vives en VS Code y quieres ver los cambios en línea |
| **Plugin JetBrains** | Claude Code en la terminal del IDE | Ecosistema IntelliJ/PyCharm |
| **Desktop App** | Aplicación con GUI: sesiones múltiples, diffs visuales, artifacts, tareas programadas | Gestionar varias sesiones; usuarios que prefieren interfaz gráfica |
| **Web (claude.ai/code)** | Sesiones en la nube conectadas a GitHub | Tareas que corren sin tu máquina encendida |
| **GitHub Actions** | Claude en CI, responde a `@claude` en PRs | Automatización de equipo |
| **Slack / móvil / Chrome** | Integraciones específicas | Casos puntuales (avisos, control remoto, automatizar navegador) |

### ¿Y esta conversación, dónde ocurre?

Apliquemos el propio método de la guía:

- 🟢 *Observable*: los comandos de panel interactivo de terminal están declarados como no disponibles en esta sesión; y dispongo de herramientas que la CLI pura no lista públicamente (generar páginas Artifact, controlar un navegador de vista previa, widgets visuales).
- 🟡 *Inferencia razonable*: ese perfil de capacidades coincide con una **app con interfaz gráfica** (Desktop App o superficie equivalente), no con la terminal cruda.
- ⚪ *No verificable desde dentro*: el nombre exacto del producto/versión. No lo afirmo porque no puedo comprobarlo.

La consecuencia práctica es la que importa: **lo aprendido en esta guía aplica en todas las superficies** (el motor es el mismo: contexto, memoria, permisos, Git, herramientas), pero el *catálogo exacto de comandos disponibles* varía. La CLI es el conjunto completo; lo demás son proyecciones.

## 6.6 El gap de visibilidad entre superficies (descubierto en vivo)

> [!warning] Esta sección se añadió después de cerrar el capítulo, porque el hallazgo ocurrió durante el trabajo real del Capítulo 7 — no en teoría, sino al intentar observar una sesión CLI desde la app.

### El problema

🟢 **Observable de primera mano (2026-07-08):** al abrir una sesión CLI en una carpeta distinta y querer observarla desde la app de escritorio (cronista), las herramientas de gestión de sesiones de la app (`list_sessions`, `search_session_transcripts`) **no la encontraron**. La sesión existía, tenía transcript completo, pero era invisible para esas herramientas.

### Lo que compartimos vs. lo que no

El Capítulo 3 documentó qué se comparte entre superficies: CLAUDE.md, auto-memory, reglas, settings. Ese mecanismo funciona perfectamente — una sesión CLI carga las mismas instrucciones que la app si apunta a la misma carpeta. Pero hay una capa que **no** se comparte: la **gestión de sesiones activas**.

🟢 Evidencia del sistema de archivos (verificada hoy):

| Elemento | CLI | App (Desktop) | ¿Compartido? |
|---|---|---|---|
| Directorio de proyecto | `~/.claude/projects/<ruta>/` | Mismo directorio | ✅ Sí |
| Formato del transcript | JSONL (`user`, `assistant`, `attachment`...) | Mismo formato JSONL | ✅ Sí |
| ID de sesión | UUID sin prefijo (`2c5acaed-...`) | UUID con prefijo `local_` (`local_a1f92e0c-...`) | ❌ No |
| Visible en `list_sessions` | No | Sí | ❌ No |
| Buscable en `search_session_transcripts` | No | Sí | ❌ No |
| Legible como archivo desde cualquier sesión | Sí | Sí | ✅ Sí |

🟡 **Inferencia razonable:** la herramienta `list_sessions` pertenece al servidor MCP `ccd_session_mgmt` (CCD = Claude Code Desktop). Es una herramienta **de la app**, no del motor compartido. Por eso solo indexa sesiones que ella misma creó (las que llevan el prefijo `local_`). No es que la CLI oculte sus datos — es que la app no los busca.

### El workaround (🟢 comprobado)

El transcript de una sesión CLI es un archivo JSONL normal en `~/.claude/projects/<ruta>/`. Cualquier sesión de cualquier superficie puede **leerlo directamente** con la herramienta `Read`. No necesitas `list_sessions` para observar lo que pasó — necesitas saber dónde está el archivo. La ruta es predecible: el nombre de la carpeta del proyecto con `-` reemplazando separadores de ruta.

### Por qué esto importa (el modelo de tres cerebros)

En el protocolo de trabajo de esta guía ([[#Protocolo de dos sesiones]]), la sesión cronista (app) necesita observar lo que hace la sesión de ejecución (CLI). Sin este workaround, el cronista queda ciego y el modelo de **pair programming con tres cerebros** se rompe:

- **Tú** (human in the loop): decides qué se hace y en qué orden.
- **Sesión de ejecución** (CLI): especialista que ejecuta el trabajo real sobre los archivos.
- **Sesión cronista** (app): observa, explica y conecta lo ejecutado con la documentación.

Si el cronista no puede ver al ejecutor, pierde su razón de ser. La vía rápida es `list_sessions`; la vía universal es leer el JSONL directamente.

### ¿Es obligatorio que sea así?

⚪ **No público.** No sabemos si es una decisión de diseño deliberada (aislamiento entre superficies) o una limitación pendiente. Lo que sí sabemos es que no hay barrera técnica real: los datos están en el mismo directorio, en el mismo formato. Solo falta que la herramienta de búsqueda los incluya.

---

## 6.7 Gestión y limpieza de sesiones CLI

> [!warning] Sección añadida en vivo (2026-07-09) tras observar la pantalla de gestión de sesiones CLI con sesiones huérfanas (carpetas eliminadas) acumulándose.

### Cómo llegar al panel de sesiones

🟢 *Observable de primera mano:* la pantalla que vimos se llama **"claude agents"** y es el hub central de todas tus sesiones CLI. Para llegar:

**Desde fuera de cualquier sesión (terminal limpia):**

```powershell
claude agents
```

**Desde dentro de una sesión activa:**

```
/agents
```

**Lo que aparece en pantalla** — una interfaz TUI (Terminal User Interface) dividida en tres zonas:

```
┌─ Encabezado ─────────────────────────────────────────────────┐
│ Claude Code v2.1.205                                         │
│ Opus 4.8 · ~/carpeta/actual                                  │
│ 0 awaiting input · 1 working · 3 completed                   │
├─ Working ────────────────────────────────────────────────────┤
│ ✱ Nombre de la tarea activa...    Prompt inicial...     9m   │
├─ Completed ──────────────────────────────────────────────────┤
│ • Nombre sesión 1                 clear                 1h   │
│ • Nombre sesión 2                 working directory...  18d  │
│ • Nombre sesión 3                 working directory...  9m   │
├─ Controles ──────────────────────────────────────────────────┤
│ ❯ describe a task for a new session                          │
│ enter to open · space to reply · ctrl+x to delete · ?        │
└──────────────────────────────────────────────────────────────┘
```

**Navegación dentro del panel:**

| Tecla | Función |
|---|---|
| **↑ / ↓** | Navegar entre sesiones de la lista |
| **Enter** | Abrir la sesión seleccionada (retomarla) |
| **Space** | Enviar un mensaje a la sesión sin abrirla |
| **Ctrl+X** | **Eliminar** la sesión seleccionada |
| **?** | Ver todos los atajos disponibles |
| **Esc** | Salir del panel |

🟢 **Dato observado:** las sesiones cuya carpeta de trabajo ya no existe muestran `working directory no longer exists: C:\Users\...` — es el caso de las sesiones huérfanas que vimos en la captura. Estas sesiones son candidatas inmediatas a limpieza.

### El panel agrupa por proyecto y muestra más estados de los documentados

🟢 *Observable de primera mano (2026-07-11, captura del panel con varias sesiones activas)*: cuando tienes sesiones en más de una carpeta, el panel las **agrupa por ruta de proyecto**, con un encabezado de carpeta y sus tareas debajo:

```
~\0.prueba\correcion del asistente
✱ Improve GPT assistant prompt for sensitive URL hand...   Done   · [prompt]              1h
✱ Improve GPT assistant prompt for documentation           Idle   · "sí, commitea..." →   1d

~\0.prueba\docuemntacion desde claude app
✱ Create prompt with goals for Antigravity execution   Needs input · send a prompt to start   1d
```

**Estados observados** (más ricos que el resumen "Working/Completed" documentado antes):

| Estado | Qué significa |
|---|---|
| `Done` | La tarea terminó y no requiere nada más de ti |
| `Idle` | La sesión sigue abierta pero no está trabajando activamente — quedó a la espera |
| `Needs input` | Existe la sesión (fue creada) pero nunca recibió su primer prompt — literalmente "send a prompt to start" |
| `awaiting input` (contador del encabezado) | Cuenta cuántas sesiones están en estado `Needs input` en total |

🟡 **Un ejemplo real y meta**: en esta misma captura, la tarea *"Create prompt with goals for Antigravity execution"* — en la carpeta de este vault — está en `Needs input`. Es, casi con certeza, la sesión que abriste siguiendo el plan de reestructuración de la Masterclass (§8 del [[Plan Masterclass de IA Agentica]]): existe, pero todavía espera que le pegues el prompt `/goal` para arrancar.

⚪ **No verificado**: si estos estados (`Done`, `Idle`, `Needs input`) son una lista cerrada y completa, o si existen otros que simplemente no aparecieron en esta captura puntual.

### Vía 1: Eliminar sesiones desde el panel (Ctrl+X)

La forma más rápida y segura. Desde el panel de `claude agents`:

1. Navega con las flechas hasta la sesión que quieres eliminar.
2. Presiona **Ctrl+X**.
3. Confirma si te lo pide.

🟡 Esta vía es la recomendada porque Claude Code se encarga de la limpieza interna. No necesitas saber dónde están los archivos ni preocuparte por dejar residuos.

### Vía 2: Eliminar sesiones manualmente (sin CLI)

🟢 *Verificado en esta sesión:* cada sesión CLI es un archivo JSONL en `~/.claude/projects/`. Si la CLI no está disponible, si quieres hacer limpieza masiva, o simplemente quieres entender la mecánica, puedes eliminar sesiones a mano.

**Estructura de archivos de sesión:**

```
~/.claude/
├── projects/
│   ├── C--Users-ALANMILE-0-prueba-correcion-del-asistente/
│   │   ├── 2c5acaed-347c-4a71-8f5c-fbe43441f5e5.jsonl  ← sesión 1 (143 KB)
│   │   └── 837f4277-19f6-49f9-b486-6a4b108282ef.jsonl  ← sesión 2 (136 KB)
│   ├── C--Users-ALANMILE-0-prueba-docuemntacion-desde-claude-app/
│   │   └── ...sesiones de este vault...
│   └── ...una carpeta por cada proyecto donde hayas usado Claude Code...
└── history.jsonl  ← índice global de historial de sesiones
```

**Regla de la ruta:** el nombre de la carpeta del proyecto se forma reemplazando `\`, `/` y `:` por `-`. Así `C:\Users\ALANMILE\0.prueba\correcion del asistente` se convierte en `C--Users-ALANMILE-0-prueba-correcion-del-asistente`.

**Para eliminar manualmente:**

```powershell
# 1. Listar sesiones de un proyecto específico
Get-ChildItem "$env:USERPROFILE\.claude\projects\C--Users-ALANMILE-0-prueba-correcion-del-asistente" -Filter "*.jsonl"

# 2. Eliminar una sesión específica (por su UUID)
Remove-Item "$env:USERPROFILE\.claude\projects\C--Users-ALANMILE-0-prueba-correcion-del-asistente\2c5acaed-347c-4a71-8f5c-fbe43441f5e5.jsonl"

# 3. Eliminar TODAS las sesiones de un proyecto (cuidado)
Remove-Item "$env:USERPROFILE\.claude\projects\C--Users-ALANMILE-0-prueba-correcion-del-asistente\*.jsonl"
```

> [!caution] Precaución
> Eliminar un JSONL es irreversible — no hay papelera ni undo. El transcript de la sesión se pierde para siempre. Si necesitas conservar algo de una sesión antes de borrarla, ábrela primero con `claude --resume` o lee el JSONL directamente.

⚪ **No verificado:** si eliminar manualmente el JSONL también limpia la entrada del panel `claude agents` automáticamente, o si queda un fantasma hasta que la CLI reindexe. La vía segura es usar Ctrl+X; la manual es para cuando necesitas control total o la CLI no está disponible.

### Ejercicio práctico: limpieza de sesiones huérfanas

> [!example] Ejercicio — Haz esto tú mismo, sin intervención de Claude ni de la CLI
>
> **Objetivo:** encontrar y eliminar sesiones huérfanas (cuya carpeta de trabajo ya no existe) directamente desde el Explorador de Archivos y PowerShell.
>
> **Paso 1 — Navega al directorio de sesiones:**
> Abre el Explorador de Archivos y ve a:
> ```
> C:\Users\ALANMILE\.claude\projects\
> ```
> (Recuerda: `.claude` es una carpeta oculta — activa "Mostrar elementos ocultos" en la pestaña Vista del Explorador.)
>
> **Paso 2 — Identifica carpetas de proyectos que ya no existen:**
> Cada subcarpeta corresponde a un proyecto. Revisa cuáles apuntan a rutas que ya no tienes en tu disco. Por ejemplo, si ves `C--Users-ALANMILE-0-prueba-transversal-analitica-institucional-IA-...` pero esa carpeta ya no existe, es candidata a limpieza.
>
> **Paso 3 — Inspecciona antes de borrar:**
> Abre un JSONL con un editor de texto (Notepad, VS Code) y mira las primeras líneas para confirmar que no necesitas esa sesión. Cada línea es un objeto JSON con un campo `type` (`user`, `assistant`, etc.).
>
> **Paso 4 — Elimina con PowerShell:**
> ```powershell
> # Ver cuántas sesiones tiene cada proyecto
> Get-ChildItem "$env:USERPROFILE\.claude\projects" -Directory |
>   ForEach-Object {
>     $count = (Get-ChildItem $_.FullName -Filter "*.jsonl").Count
>     "$($_.Name): $count sesiones"
>   }
>
> # Eliminar una sesión específica que ya identificaste como innecesaria
> Remove-Item "$env:USERPROFILE\.claude\projects\NOMBRE-CARPETA\UUID-SESION.jsonl"
> ```
>
> **Paso 5 — Verifica:**
> Abre `claude agents` y comprueba que la sesión eliminada ya no aparece (o aparece como fantasma que desaparecerá en la próxima carga).
>
> **Pregunta de reflexión:** ¿por qué es importante limpiar sesiones huérfanas en lugar de dejarlas acumularse? (Pista: piensa en el peso del directorio `projects/` y en la claridad del panel `claude agents`.)

## 6.8 El puente práctico: abrir la CLI al lado y continuar desde allá

Esto era tu objetivo declarado desde el principio ("poder abrir la línea de comandos en una sesión al lado y trabajar a partir de lo que hemos charlado acá"). La buena noticia: **ya está todo preparado, por diseño**, gracias a los mecanismos del Capítulo 3. Comprueba el razonamiento:

1. El método de trabajo vive en el `CLAUDE.md` de este proyecto → una sesión CLI abierta en esta carpeta **lo carga sola** (Capítulo 3 §3.2).
2. Tus preferencias y el estado del proyecto viven en la auto-memory → también se cargan solas (§3.4).
3. La regla de los marcadores 🔵🟢🟡⚪ vive en `.claude/rules/` → se activa sola al tocar capítulos (§3.3).
4. Los permisos de WebSearch/WebFetch viven en `.claude/settings.json` del proyecto → aplican igual.

Es decir: la continuidad entre esta app y una sesión CLI **no depende de copiar y pegar nada** — depende de los archivos de persistencia que ya construimos, y una sesión nueva en la misma carpeta los recoge todos. Pasos concretos:

```powershell
# 1. Instala la CLI (si no la tienes):
irm https://claude.ai/install.ps1 | iex

# 2. Abre una terminal EN la carpeta del vault (o del repo institucional):
cd "C:\Users\ALANMILE\0.prueba\docuemntacion desde claude app"

# 3. Arranca:
claude

# 4. Verifica tú mismo la continuidad — pregunta algo que solo sabría
#    por la memoria/CLAUDE.md, por ejemplo:
#    "¿en qué capítulo vamos y cuál es el método de trabajo?"
```

Y ya dentro, prueba los comandos que esta app no te deja ver: `/context`, `/permissions`, `/hooks`, `/agents`.

## Resumen del capítulo

- La fuente matriz oficial es **`code.claude.com/docs`** (Anthropic); la página clave para comandos es la **CLI reference**. Todo dominio distinto de los oficiales es fuente de terceros.
- Herramientas (las usa el modelo), slash commands (los usas tú) y skills (recetas reutilizables) son tres cosas distintas que se confunden fácil.
- El catálogo de slash commands cubre sesión/contexto (`/clear`, `/compact`, `/rewind`), configuración (`/model`, `/permissions`, `/memory`, `/mcp`) y trabajo (`/init`, `/plan`, `/goal`, `/code-review`).
- La CLI se instala en minutos, tiene modo interactivo (`claude`) y headless (`claude -p`, componible con pipes), y controles de teclado propios (Shift+Tab, Ctrl+B, Esc, `!`, `@`).
- Claude Code es un motor con muchas superficies; la CLI expone el conjunto completo de capacidades, las apps exponen subconjuntos — esta guía se escribió desde una app, y por eso comandos como `/goal` no estaban disponibles aquí.
- La continuidad entre superficies no se copia a mano: viaja en los archivos de persistencia del Capítulo 3 (CLAUDE.md, memoria, reglas, settings). Abrir la CLI en la misma carpeta basta.
- Hay un **gap de visibilidad**: la gestión de sesiones de la app (`list_sessions`) no ve sesiones CLI. Los transcripts CLI son archivos JSONL legibles directamente en `~/.claude/projects/`. Este workaround es infraestructura crítica para el modelo de tres cerebros (usuario + ejecutor + cronista).
- Las sesiones CLI se gestionan desde el panel **`claude agents`** (accesible con `claude agents` desde la terminal o `/agents` dentro de una sesión). Desde ahí puedes abrir, responder o eliminar sesiones con Ctrl+X. También puedes eliminar manualmente borrando los archivos JSONL en `~/.claude/projects/<proyecto>/` — útil para limpieza masiva o cuando la CLI no está disponible.
- **`/model`, `/effort` y `/config` no son intercambiables**: `/model` y `/effort` son comandos de responsabilidad única que además guardan su valor como default; `/config` tiene su propio espacio de parámetros (acepta `model=<alias>` pero no `effortLevel`) y te lo dice explícitamente si te equivocas — leer el mensaje de error ahorra tiempo.
- Los modos de permiso del Shift+Tab se llaman en pantalla **`manual` / `auto` / `plan`** (v2.1.206) — corrección de la nomenclatura `default`/`acceptEdits`/`plan` usada antes en esta guía; el comportamiento no cambió, solo la etiqueta visible.
- Claude Code inserta **recaps automáticos** entre turnos (resumen + próximo paso), desactivables desde `/config`, acompañados de un indicador de tiempo de razonamiento con verbos aleatorios ("Cogitated", "Brewed", "Sautéed" en vez de "Thinking").
- El panel `claude agents` **agrupa sesiones por carpeta de proyecto** cuando hay varias, y expone estados más ricos que "Working/Completed": `Done`, `Idle`, `Needs input` (con contador `awaiting input` en el encabezado).

## Analogía

Piensa en un automóvil moderno: el mismo motor puede conducirse desde el volante físico (la CLI — control total, todos los mandos a la vista), desde una app del teléfono que arranca el motor y regula la temperatura (la Desktop App — cómoda, pero con menos mandos), o mediante programación de encendido automático (headless/`-p` — nadie al volante, tareas programadas). El manual del fabricante (la documentación oficial) describe todos los mandos; cada interfaz expone solo los que tienen sentido en ella. Y la configuración del asiento y los espejos (CLAUDE.md, memoria) se guarda en el coche, no en la llave: entres por donde entres, te espera igual.

## Autoevaluación

1. ¿Cuál es la fuente matriz oficial de Claude Code y cómo distingues una fuente oficial de una de terceros?
2. Explica la diferencia entre herramienta, slash command y skill, con un ejemplo de cada una.
3. ¿Qué evidencia apareció en esta misma sesión de que las superficies exponen subconjuntos distintos de comandos?
4. ¿Para qué sirve `claude -p` y con qué principio del Capítulo 1 conecta?
5. ¿Por qué abrir una sesión CLI en la carpeta de este vault continuaría el trabajo sin copiar nada a mano? Nombra los cuatro mecanismos que lo hacen posible.
6. *(Añadida §6.6)* Una sesión de la app quiere observar lo que hizo una sesión CLI en otra carpeta. `list_sessions` no la encuentra. ¿Por qué, y cuál es el workaround?
7. *(Añadida §6.7)* Tienes 15 sesiones CLI acumuladas de pruebas antiguas cuyas carpetas ya no existen. Describe dos vías para eliminarlas y cuál es la precaución principal antes de borrar.
8. *(Añadida §6.4)* Quieres cambiar el modelo a Opus y el nivel de esfuerzo a `medium` en un solo comando `/config model=opus effortLevel=medium`, y falla. ¿Por qué falla exactamente esa parte, y qué dos comandos deberías usar en su lugar?
9. *(Añadida §6.4)* Ves en la barra inferior "manual mode on" tras presionar Shift+Tab. ¿A cuál de los tres modos de permiso del Capítulo 4 corresponde, y por qué esto no contradice lo aprendido allá?
10. *(Añadida §6.7)* En el panel `claude agents` ves una tarea en estado `Needs input`. ¿Qué significa exactamente, y en qué se diferencia de `Idle`?

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. `code.claude.com/docs`, de Anthropic. Es oficial si el dominio es code.claude.com, platform.claude.com, claude.com, support.claude.com o anthropic.com; cualquier otro dominio es de terceros, útil pero sin autoridad final.
> 2. Herramienta: la invoca el modelo para actuar (ej. `Edit`). Slash command: lo escribes tú para controlar la sesión (ej. `/compact`). Skill: paquete de instrucciones reutilizable que puedes invocar tú o Claude automáticamente (ej. `/code-review`).
> 3. Los comandos de panel interactivo (`/permissions`, `/hooks`, `/agents`, etc.) están declarados como no disponibles en esta sesión, y `/goal` no aparece en la lista de skills invocables — pese a estar ambos documentados como parte de Claude Code.
> 4. Es el modo headless: responde una vez y termina, lo que permite encadenarlo con pipes y scripts (`tail log | claude -p "..."`). Conecta con la filosofía Unix del Capítulo 1: herramienta pequeña y componible.
> 5. Porque la continuidad viaja en archivos de persistencia, no en la conversación: (1) CLAUDE.md del proyecto con el método de trabajo, (2) auto-memory con preferencias y estado, (3) `.claude/rules/` con la regla de marcadores, (4) `.claude/settings.json` con los permisos. Una sesión nueva en la misma carpeta los carga todos automáticamente.
> 6. `list_sessions` es una herramienta MCP del servidor `ccd_session_mgmt` (CCD = Claude Code Desktop) — solo indexa sesiones de la app (IDs con prefijo `local_`). Las sesiones CLI usan IDs sin prefijo → invisibles. Workaround: los transcripts CLI son archivos JSONL en `~/.claude/projects/<ruta-del-proyecto>/`, legibles directamente con `Read` desde cualquier sesión.
> 7. **Vía 1 (recomendada):** abrir `claude agents`, navegar con flechas hasta la sesión y presionar Ctrl+X. **Vía 2 (manual):** ir a `~/.claude/projects/<carpeta-del-proyecto>/` y eliminar los archivos `.jsonl` con `Remove-Item` o desde el Explorador. **Precaución principal:** la eliminación es irreversible — no hay papelera ni undo. Siempre inspeccionar el JSONL antes de borrar para confirmar que no contiene trabajo que necesites conservar.
> 8. Falla porque `effortLevel` **no es un parámetro válido de `/config`** — ese comando solo acepta `model=<alias>` (y otros ajustes documentados, pero no el nivel de esfuerzo). El nivel de esfuerzo tiene su propio comando dedicado. La combinación correcta es **dos comandos separados**: `/model opus` (o `/model claude-opus-4-6` para el ID exacto) y `/effort medium`.
> 9. Corresponde a `default` — el modo donde apruebas cada acción antes de que ocurra. No contradice el Capítulo 4 porque el comportamiento es el mismo; solo cambió la etiqueta que se muestra en pantalla entre versiones de la CLI (aquí, v2.1.206). Es el mismo patrón ya visto en el §6.3: el catálogo y sus nombres evolucionan con cada versión.
> 10. `Needs input` significa que la sesión **fue creada pero nunca recibió su primer mensaje** — está literalmente esperando que le envíes un prompt para arrancar ("send a prompt to start"). `Idle`, en cambio, es una sesión que **ya tuvo actividad** y ahora está simplemente a la espera, sin trabajo pendiente inmediato. La diferencia es "nunca empezó" vs. "empezó y ahora está en pausa".

---
[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Siguiente → [[Capitulo 07 - MCP y Herramientas]]
