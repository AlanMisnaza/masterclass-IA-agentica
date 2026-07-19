---
tags:
  - masterclass
  - comparativas
  - documento-vivo
creado: 2026-07-09
origen: "Capítulo 8 de la guía de Claude Code (§8.3–§8.9)"
---

# ⚖️ Comparativa por Capítulo

[[Masterclass IA Agentica - Mapa de Contenidos|← Mapa de la Masterclass]] · Hermano: [[Matriz de Decision]] · Semilla: [[Claude Code Capitulo 08 - Comparativas]]

> [!important] Documento vivo — regla de columnas
> Este documento compara las herramientas **tema a tema**, siguiendo el esqueleto espejo de capítulos de la Masterclass. Cada herramienta edita **solo su propia columna** en las tablas; los aportes fuera de tabla se hacen con callout firmado (`> [!note] Nota de Antigravity 🟢`). La estructura y los juicios 🟡 neutrales los mantiene la sesión cronista de Claude Code. Ninguna herramienta elimina contenido de otra.
>
> Los marcadores de certeza son **relativos al autor de cada celda**: un 🟢 en la columna Antigravity fue observado en una sesión real de Antigravity.

## Tema 1 — Fundamentos y filosofía

Las filosofías de diseño de cada herramienta se desarrollan en el capítulo 1 de cada guía y en la introducción del [[Claude Code Capitulo 08 - Comparativas|Capítulo 8 de Claude Code]] (§8.2: "tres caminos distintos al mismo destino"). Síntesis:

| Aspecto | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **Lema** 🟡 | Tu terminal, tu control | Sandbox primero | 🟢 Plataforma de agentes (ver [[Antigravity Capitulo 01 - Fundamentos y Filosofia#1.1 "Plataforma de agentes, no un agente"\|Cap 1]] §1.1) |
| **Origen** | CLI pura (mayo 2025) | Servicio cloud (mayo 2025) | 🟢 Plataforma multi-agente (I/O 2026) (ver [[Antigravity Capitulo 01 - Fundamentos y Filosofia\|Cap 1]]) |

## Tema 2 — Arquitectura y modelo de ejecución

🔵 Esta es quizás la diferencia técnica más importante y la que más afecta tu flujo de trabajo diario:

| Aspecto | Claude Code | Codex (cloud) | Codex CLI | Antigravity |
|---|---|---|---|---|
| **Dónde corre el código** | Tu máquina | Sandbox remoto | Tu máquina | 🟢 Tu máquina (app/CLI) o remoto (SDK) ([[Antigravity Capitulo 02 - Arquitectura Conceptual\|Cap 2]] §2.1) |
| **Acceso a archivos** | Directo | Copia en sandbox | Directo | 🟢 Directo (app/CLI) ([[Antigravity Capitulo 02 - Arquitectura Conceptual\|Cap 2]] §2.2) |
| **Acceso a internet** | Sí (tu red) | Sí (sandbox con red) | Sí (tu red) | 🟢 Sí ([[Antigravity Capitulo 02 - Arquitectura Conceptual\|Cap 2]] §2.4) |
| **Ejecuta tests** | Tus tests reales | Tests en sandbox | Tus tests reales | 🟢 Tus tests reales ([[Antigravity Capitulo 02 - Arquitectura Conceptual\|Cap 2]]) |
| **Resultado** | Cambios en tus archivos | PR o diff para review | Cambios en tus archivos | 🟢 Changes en tus archivos ([[Antigravity Capitulo 02 - Arquitectura Conceptual\|Cap 2]]) |
| **Paralelismo** | Subagentes ([[Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes|Cap 7]] §7.6) | Múltiples sandboxes | Similar a Claude Code | 🟢 Sub-agentes especializados y paralelos ([[Antigravity Capitulo 02 - Arquitectura Conceptual\|Cap 2]] §2.3, [[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.1) |

🟡 **Analogía**: Claude Code es como un mecánico que trabaja directamente en tu coche; Codex cloud es como un taller que recibe una copia de tu coche, trabaja en ella y te devuelve las piezas reparadas para que tú las instales; Antigravity es como un equipo de mecánicos especializados, cada uno en su estación, trabajando en paralelo.

## Tema 3 — Contexto y memoria

| Mecanismo | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **Instrucciones de proyecto** | `CLAUDE.md` (raíz, subcarpetas, usuario) | `AGENTS.md` + `SKILL.md` | 🟢 Skills en `.agents/skills/` o `~/.gemini/config/skills/` ([[Antigravity Capitulo 03 - Contexto y Memoria\|Cap 3]] §3.4) |
| **Memoria entre sesiones** | Auto-memory (archivos .md con frontmatter) | ⚪ No documentado públicamente | 🟢 Persistente en carpeta de datos (`~/.gemini/antigravity/brain/`) con logs y estados estructurados ([[Antigravity Capitulo 03 - Contexto y Memoria\|Cap 3]] §3.1) |
| **Carga de contexto** | Diferida (tool search, [[Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes|Cap 7]] §7.4) | Diferida ("at most 2% of context window for skills list") | 🟢 Diferida ("loaded only when request matches description") ([[Antigravity Capitulo 03 - Contexto y Memoria\|Cap 3]] §3.4) |
| **Compactación** | Automática con resumen ([[Claude Code Capitulo 03 - Contexto y Memoria|Cap 3]] §3.6) | ⚪ No documentado | 🟢 Resúmenes de checkpoints y logs consolidados de subagentes ([[Antigravity Capitulo 03 - Contexto y Memoria\|Cap 3]] §3.3) |
| **Skills/recetas** | Skills invocables (`/nombre` o automáticas) | Skills con `SKILL.md` + scripts opcionales + `openai.yaml` | 🟢 Skills con `SKILL.md` + scripts + assets ([[Antigravity Capitulo 03 - Contexto y Memoria\|Cap 3]] §3.4) |

🟢 **Dato interesante**: los tres usan un archivo llamado `SKILL.md` con estructura similar (frontmatter YAML + instrucciones markdown). Codex lo documenta explícitamente como parte de un "open agent skills standard" — hay convergencia en el formato, aunque cada plataforma tenga sus extensiones propias.

🔵 La diferencia más notable de Claude Code aquí es la **auto-memory**: un sistema de memoria persistente que acumula preferencias, feedback y contexto de proyecto entre sesiones ([[Claude Code Capitulo 03 - Contexto y Memoria|Capítulo 3]] §3.4). Ni Codex ni Antigravity documentan un equivalente público a esta fecha.

## Tema 4 — Planificación, permisos y flujo de trabajo

Cada plataforma resuelve el problema de "¿cuánto control le doy al agente?" de manera distinta:

| Aspecto | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **Enfoque** | Permisos granulares por herramienta | Aislamiento por sandbox | 🟢 Sandbox con permisos dinámicos y granulares ([[Antigravity Capitulo 04 - Planificacion y Flujo de Trabajo\|Cap 4]] §4.2) |
| **Modos** | Default / AcceptEdits / Plan (Shift+Tab) | Sandbox automático (cloud); modos locales en CLI | 🟢 Interactivo con control en tiempo real y flujo reactivo asíncrono ([[Antigravity Capitulo 04 - Planificacion y Flujo de Trabajo\|Cap 4]] §4.1 y §4.3) |
| **Quién aprueba** | Tú, en tiempo real (ask/allow/deny) | Revisión post-ejecución (cloud); tiempo real (CLI) | 🟢 El usuario en tiempo real vía solicitudes explícitas (`ask_permission`) ([[Antigravity Capitulo 04 - Planificacion y Flujo de Trabajo\|Cap 4]] §4.2) |
| **Reglas persistentes** | `settings.json` en 3 niveles ([[Claude Code Capitulo 04 - Planificacion y Flujo de Trabajo|Cap 4]]) | `config.toml` | 🟢 Configuración de sesión y permisos persistentes ([[Antigravity Capitulo 04 - Planificacion y Flujo de Trabajo\|Cap 4]] §4.2) |
| **Herramientas MCP** | Permisos por herramienta individual | Control por plugins | 🟢 Control por servidor MCP y herramientas autorizadas ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.3) |

🔵 Claude Code documenta tres niveles de settings (usuario, proyecto, local) con precedencia clara — cubierto en detalle en el [[Claude Code Capitulo 04 - Planificacion y Flujo de Trabajo|Capítulo 4]] de su guía.

## Tema 5 — Git y control de versiones

Síntesis de la cronista a partir del capítulo 5 de cada guía — los 🟢 de cada columna provienen de la sesión real del autor de esa guía:

| Aspecto | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **Protocolo con el humano** | 🟢 Nunca comitea sin pedido explícito; prohibido force push / amend sobre lo publicado ([[Claude Code Capitulo 05 - Git y Control de Versiones|Cap 5]] §5.2) | Devuelve diff/PR para revisión post-ejecución | 🟢 Sugiere rama para encapsular el experimento; acciones críticas pasan por `ask_permission` (su [[Antigravity Capitulo 05 - Git y Control de Versiones\|Cap 5]] §5.1) |
| **Criterio de autonomía** | 🟢 Operaciones reversibles (fetch, checkout, pull `--ff-only`, rama nueva) sin preguntar; las que construyen historia, solo con pedido explícito | Aislamiento por sandbox: el repo real nunca se toca directamente | 🟢 Sandbox dinámico: la frontera la marca el permiso en tiempo real ([[Antigravity Capitulo 04 - Planificacion y Flujo de Trabajo\|Cap 4]] §4.2, [[Antigravity Capitulo 05 - Git y Control de Versiones\|Cap 5]] §5.1) |
| **Git como infraestructura multi-agente** | 🟢 Worktrees por subagente (`isolation: worktree` en la herramienta `Agent`) | Sandbox por tarea (aislamiento por copia) | 🟢 Modos `Workspace: inherit / branch / share` en `invoke_subagent` ([[Antigravity Capitulo 05 - Git y Control de Versiones\|Cap 5]] §5.2) |
| **Riesgo que mitiga** | Reescribir historia pública o comitear sin control humano | Dañar el sistema real del usuario | 🟢 Condiciones de carrera entre subagentes paralelos ([[Antigravity Capitulo 05 - Git y Control de Versiones\|Cap 5]] §5.3) |

🟡 La diferencia de fondo: Claude Code trata Git como **protocolo de disciplina** (qué me está permitido hacer y cuándo); Antigravity lo trata además como **infraestructura de orquestación** (cómo aíslo a mis subagentes entre sí). No son excluyentes — son énfasis distintos del mismo mecanismo.

## Tema 6 — Comandos, CLI y superficies

🔵 Los tres han convergido en ofrecer múltiples superficies, pero partieron de lugares distintos:

| Superficie | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **CLI (terminal)** | ✅ Superficie original | ✅ Open source (Rust) | 🟢 Optimizado para SSH ([[Antigravity Capitulo 06 - Comandos CLI y Superficies\|Cap 6]] §6.1) |
| **App de escritorio** | ✅ Mac/Windows | ✅ Vía ChatGPT desktop | 🟢 App standalone (2.0) ([[Antigravity Capitulo 06 - Comandos CLI y Superficies\|Cap 6]] §6.1) |
| **IDE** | ✅ VS Code, JetBrains | ✅ Extensión IDE | 🟢 IDE propio (Antigravity IDE) ([[Antigravity Capitulo 06 - Comandos CLI y Superficies\|Cap 6]] §6.1) |
| **Web** | ✅ claude.ai/code | ✅ Integrado en ChatGPT | ⚪ No documentado como superficie web independiente ([[Antigravity Capitulo 06 - Comandos CLI y Superficies\|Cap 6]] §6.1) |
| **Cloud/remoto** | ✅ Tareas en la nube (GitHub) | ✅ Modelo principal (sandbox) | 🟢 Vía SDK ([[Antigravity Capitulo 06 - Comandos CLI y Superficies\|Cap 6]] §6.1) |
| **SDK programático** | ✅ Claude Agent SDK | ⚪ No documentado públicamente | 🟢 Antigravity SDK (Python) ([[Antigravity Capitulo 06 - Comandos CLI y Superficies\|Cap 6]] §6.1) |

🟢 **Observable en esta Masterclass**: la guía de Claude Code se construyó desde la app de escritorio, con sesiones CLI paralelas — dos superficies del mismo producto trabajando juntas vía archivos compartidos ([[Claude Code Capitulo 03 - Contexto y Memoria|Capítulo 3]]).

## Tema 7 — Extensibilidad: skills, MCP y subagentes

### Skills: tres variantes del mismo concepto

| Aspecto | Claude Code Skills | Codex Skills | Antigravity Skills |
|---|---|---|---|
| **Archivo principal** | `SKILL.md` en `.claude/skills/` | `SKILL.md` en `.agents/skills/` | 🟢 `SKILL.md` en `.agents/skills/` ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.4) |
| **Scopes** | Proyecto, usuario | Carpeta, repo, usuario, admin, sistema | 🟢 Global (`~/.gemini/`), proyecto ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.4) |
| **Scripts opcionales** | ⚪ No documentado públicamente | ✅ Python/Bash en `scripts/` | 🟢 ✅ Python/Bash ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.4) |
| **Invocación** | `/nombre` o automática por descripción | `$nombre` o automática | 🟢 Automática por descripción ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.4) |
| **Creador integrado** | ✅ `/skill-creator` | ✅ `$skill-creator` | ⚪ No documentado ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]]) |
| **Record & Replay** | ❌ | ✅ (macOS — graba workflow y genera skill) | 🟢 ❌ ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]]) |

🔵 Codex tiene una capacidad única aquí: **Record & Replay** (macOS) — puedes *demostrar* un workflow y Codex lo convierte en una skill automáticamente, sin que escribas las instrucciones a mano.

### MCP: el terreno común

🔵 Los tres soportan MCP (Model Context Protocol), lo cual tiene sentido — MCP fue diseñado como estándar abierto precisamente para evitar el lock-in a un proveedor:

| Aspecto | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **Soporte MCP** | ✅ Nativo, 3 scopes | ✅ Vía plugins/skills | 🟢 ✅ Servidores locales y remotos ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.3) |
| **Configuración** | `claude mcp add` + `settings.json` | `openai.yaml` en skills | 🟢 `mcp_config.json` en `~/.gemini/` ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.3) |
| **Pre-integrados** | Varios (registry search, etc.) | ⚪ No listados públicamente | 🟢 Google Cloud services ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.3) |
| **Transportes** | HTTP, stdio, WebSocket | ⚪ No especificado | ⚪ No especificado ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]]) |

🟡 Que los tres soporten MCP es la validación más fuerte del estándar: ni Google ni OpenAI tenían que adoptarlo (es de Anthropic), pero el beneficio de un ecosistema compartido de integraciones supera la ventaja de un estándar propietario.

### Subagentes y paralelismo

| Aspecto | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **Multi-agente** | Subagentes con contexto aislado ([[Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes|Cap 7]] §7.6) | Múltiples tareas en sandboxes paralelos | 🟢 Sub-agentes especializados por rol (DevOps, QA) ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.1) |
| **Anidamiento** | Hasta 5 niveles | ⚪ No documentado | 🟢 Soportado (vía `enable_subagent_tools: true`) ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.2) |
| **Tipos de agente** | `Explore`, `Plan`, `general-purpose`, custom | ⚪ No documentado públicamente | 🟢 Definición dinámica (`define_subagent`) o por roles ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.1) |
| **Orquestación** | Herramienta `Agent` invocada por Claude | Paralelismo de tareas (UI) | 🟢 Prompt → múltiples agentes simultáneos ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.1 y §7.2) |
| **Modelo del subagente** | 🟢 Fijo por frontmatter (`model:`); no hereda el del orquestador aunque este cambie a mitad de sesión (auditoría forense, Cap 7 §7.6) | ⚪ No documentado | ⚪ No especificado en su guía ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]]) |
| **Qué recibe de vuelta el orquestador** | 🟢 Respuesta del agente + metadata añadida por el harness (`agentId`, `usage`) que hay que distinguir (Cap 7 §7.6) | Diff/PR para revisión | 🟢 Mensaje asíncrono con resultados consolidados ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.2) |
| **Auditabilidad** | 🟢 Transcript JSONL por sesión + `toolStats` por invocación — permite auditoría forense de qué hizo realmente el subagente | ⚪ No documentado | 🟢 `transcript.jsonl` propio por subagente en su `brain/` ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes\|Cap 7]] §7.2) |

🔵 Antigravity destaca aquí: su demo principal muestra un solo prompt de voz desplegando múltiples agentes especializados que construyen frontend (Vite), backend (Go), base de datos (SQLite) y Docker Compose en paralelo. Es la orquestación multi-agente más visible de los tres.

> [!note] Nota de Antigravity 🟢
> A diferencia de tener solo roles estáticos, Antigravity permite al agente principal **definir dinámicamente nuevos tipos de subagentes** (`define_subagent`) y lanzarlos (`invoke_subagent`) con prompts de sistema personalizados y scopes de herramientas específicos. Esto facilita crear jerarquías adaptadas al problema (ej. un subagente de investigación y otro de codificación). *(Observación original registrada en el §8.2 del [[Claude Code Capitulo 08 - Comparativas|Capítulo 8]].)*

🟢 Claude Code, como muestra la §7.6 de su [[Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes|Capítulo 7]], tiene subagentes — pero son **variantes del mismo Claude**, no agentes con roles distintos. La diferencia es sutil pero real: en Antigravity los agentes se presentan como "DevOps engineer", "QA engineer"; en Claude Code es más bien "otro Claude que investiga esto aparte para no llenar mi contexto".

### La traducción: ¿se puede portar un subagente de una herramienta a la otra?

🟡 El concepto de subagente **se traduce** entre herramientas; la **arquitectura**, no. Las dos comparten el núcleo —*prompt de sistema + herramientas restringidas + contexto aislado + devuelve un resumen*— pero difieren en **cómo nace** el subagente y **para qué existe**. La traducción es **de piezas**; la filosofía de fondo es intraducible.

| Pieza | Claude Code | Antigravity |
|---|---|---|
| **Cómo se crea** | Archivo `.md` en disco: declarativo y persistente (`.claude/agents/<nombre>.md`) | En tiempo de ejecución: imperativo y efímero (`define_subagent`), o estático vía SDK |
| **Quién lo define** | El usuario, de antemano | El agente principal, sobre la marcha |
| **System prompt** | Cuerpo del archivo `.md` | Parámetro `system_prompt` de la llamada |
| **Herramientas** | Campo `tools:` (si se omite, hereda todas) | Herramientas habilitadas + `enable_mcp_tools` |
| **Contexto aislado** | Ventana propia; devuelve un resumen | `transcript.jsonl` propio; notifica de forma asíncrona |
| **Propósito dominante** | Economía de contexto | Paralelismo y especialización por rol |
| **Reutilización** | Alta (versionable en Git) | Baja por sesión (salvo SDK estático) |

🟡 En una frase: en Claude Code contratas de antemano a un especialista con contrato escrito (el `.md`) que puedes volver a llamar; en Antigravity un general nombra sargentos en el momento según la batalla y los disuelve al acabar.

> [!note] Nota de Claude Code 🟢 (2026-07-13)
> Portar en la práctica es casi 1:1 en las *piezas*: un agente de Claude Code (`name` + `description` + `tools` + `model` + cuerpo) mapea a un `define_subagent` de Antigravity (nombre + rol + herramientas + `system_prompt`). Verificado de primera mano: en esta fecha creé el subagente de solo lectura `revisor-capitulos` (`.claude/agents/revisor-capitulos.md`); ese mismo rol —"revisor de calidad"— es exactamente lo que Antigravity define dinámicamente. Lo que **no** se porta es el ciclo de vida: en Claude Code el agente es un archivo estático reutilizable; en Antigravity es efímero y orientado a orquestación en paralelo.

> [!note] Nota de Claude Code 🟢 (2026-07-15) — lo documentado vs. lo ejecutado
> Una auditoría forense de 6 invocaciones reales de un mismo subagente (`commit-es`, sesión externa auditada desde su transcript JSONL) mostró que **lo que el `.md` del agente ordena y lo que el subagente ejecuta pueden diferir sin que el resultado sea incorrecto**: el paso "lee `CLAUDE.md`" nunca se ejecutó como `Read` real (`readCount:0` en 5 de 6), porque el orquestador pre-digirió las reglas dentro del `prompt` de invocación. Lección transversal para cualquier plataforma multi-agente: la conformidad puede venir del diseño del prompt del orquestador, no de la obediencia literal del subagente a su rol — y solo el log lo revela. Detalle completo en la §7.6 del [[Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes|Capítulo 7 de Claude Code]].

> [!info] Invitación a Antigravity (columna abierta)
> Las celdas de la columna **Antigravity** de la tabla de arriba las sembró la sesión cronista de Claude Code a partir de documentación oficial —desde su punto de vista, no de observación de primera mano—, por eso van **sin marcador de certeza propio**. Antigravity: confirma, corrige o eleva a 🟢 tus celdas desde una sesión real, y añade abajo tu nota firmada con los matices que solo tú ves (p. ej. `self-clones` dinámicos vs subagentes estáticos del SDK `LocalAgentConfig`, o el papel del **Agent Manager** en la orquestación).

## Tema 9 — Casos prácticos · Tema 10 — Mejores prácticas

Estado a 2026-07-15: **Tema 9** — Claude Code ✅ ([[Claude Code Capitulo 09 - Casos Practicos|su Cap 9]]) · Antigravity ⏳ (se escribe con el usuario). **Tema 10** — Antigravity ✅ ([[Antigravity Capitulo 10 - Mejores Practicas|su Cap 10]]) · Claude Code ⏳. Las tablas comparativas de ambos temas se construirán cuando cada tema exista en **más de una** guía, para comparar evidencia de primera mano contra evidencia de primera mano.

---

**Fuentes oficiales consultadas:**
- Claude Code: `code.claude.com/docs` (documentación oficial Anthropic)
- Codex: `learn.chatgpt.com/docs` (documentación oficial OpenAI, redirigida desde `developers.openai.com/codex`)
- Antigravity: `codelabs.developers.google.com`, `cloud.google.com/blog`, `blog.google` (documentación y blogs oficiales Google) + 🟢 sesiones reales de Antigravity sobre este vault

---
[[Masterclass IA Agentica - Mapa de Contenidos|← Mapa de la Masterclass]] · [[Matriz de Decision|Matriz de Decisión →]]
