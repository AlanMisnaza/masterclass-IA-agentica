---
tags:
  - masterclass
  - comparativas
  - documento-vivo
creado: 2026-07-09
origen: "Capítulo 8 de la guía de Claude Code (§8.3–§8.9)"
---

# ⚖️ Comparativa por Capítulo

[[Masterclass IA Agentica - Mapa de Contenidos|← Mapa de la Masterclass]] · Hermano: [[Matriz de Decision]] · Semilla: [[Capitulo 08 - Comparativas]]

> [!important] Documento vivo — regla de columnas
> Este documento compara las herramientas **tema a tema**, siguiendo el esqueleto espejo de capítulos de la Masterclass. Cada herramienta edita **solo su propia columna** en las tablas; los aportes fuera de tabla se hacen con callout firmado (`> [!note] Nota de Antigravity 🟢`). La estructura y los juicios 🟡 neutrales los mantiene la sesión cronista de Claude Code. Ninguna herramienta elimina contenido de otra.
>
> Los marcadores de certeza son **relativos al autor de cada celda**: un 🟢 en la columna Antigravity fue observado en una sesión real de Antigravity.

## Tema 1 — Fundamentos y filosofía

Las filosofías de diseño de cada herramienta se desarrollan en el capítulo 1 de cada guía y en la introducción del [[Capitulo 08 - Comparativas|Capítulo 8 de Claude Code]] (§8.2: "tres caminos distintos al mismo destino"). Síntesis:

| Aspecto | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **Lema** 🟡 | Tu terminal, tu control | Sandbox primero | Plataforma de agentes |
| **Origen** | CLI pura (mayo 2025) | Servicio cloud (mayo 2025) | Plataforma multi-agente (I/O 2026) |

## Tema 2 — Arquitectura y modelo de ejecución

🔵 Esta es quizás la diferencia técnica más importante y la que más afecta tu flujo de trabajo diario:

| Aspecto | Claude Code | Codex (cloud) | Codex CLI | Antigravity |
|---|---|---|---|---|
| **Dónde corre el código** | Tu máquina | Sandbox remoto | Tu máquina | Tu máquina (app/CLI) o remoto (SDK) |
| **Acceso a archivos** | Directo | Copia en sandbox | Directo | Directo (app/CLI) |
| **Acceso a internet** | Sí (tu red) | Sí (sandbox con red) | Sí (tu red) | Sí |
| **Ejecuta tests** | Tus tests reales | Tests en sandbox | Tus tests reales | Tus tests reales |
| **Resultado** | Cambios en tus archivos | PR o diff para review | Cambios en tus archivos | Cambios en tus archivos |
| **Paralelismo** | Subagentes ([[Capitulo 07 - MCP y Herramientas|Cap 7]] §7.6) | Múltiples sandboxes | Similar a Claude Code | Sub-agentes especializados |

🟡 **Analogía**: Claude Code es como un mecánico que trabaja directamente en tu coche; Codex cloud es como un taller que recibe una copia de tu coche, trabaja en ella y te devuelve las piezas reparadas para que tú las instales; Antigravity es como un equipo de mecánicos especializados, cada uno en su estación, trabajando en paralelo.

## Tema 3 — Contexto y memoria

| Mecanismo | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **Instrucciones de proyecto** | `CLAUDE.md` (raíz, subcarpetas, usuario) | `AGENTS.md` + `SKILL.md` | Skills en `.agents/skills/` o `~/.gemini/config/skills/` |
| **Memoria entre sesiones** | Auto-memory (archivos .md con frontmatter) | ⚪ No documentado públicamente | 🟢 Persistente en carpetas de datos de aplicación (e.g., `~/.gemini/antigravity/brain/`) con logs y estados estructurados |
| **Carga de contexto** | Diferida (tool search, [[Capitulo 07 - MCP y Herramientas|Cap 7]] §7.4) | Diferida ("at most 2% of context window for skills list") | Diferida ("loaded only when request matches description") |
| **Compactación** | Automática con resumen ([[Capitulo 03 - Contexto y Memoria|Cap 3]] §3.6) | ⚪ No documentado | 🟢 Resúmenes de checkpoints y logs consolidados de subagentes |
| **Skills/recetas** | Skills invocables (`/nombre` o automáticas) | Skills con `SKILL.md` + scripts opcionales + `openai.yaml` | Skills con `SKILL.md` + scripts + assets |

🟢 **Dato interesante**: los tres usan un archivo llamado `SKILL.md` con estructura similar (frontmatter YAML + instrucciones markdown). Codex lo documenta explícitamente como parte de un "open agent skills standard" — hay convergencia en el formato, aunque cada plataforma tenga sus extensiones propias.

🔵 La diferencia más notable de Claude Code aquí es la **auto-memory**: un sistema de memoria persistente que acumula preferencias, feedback y contexto de proyecto entre sesiones ([[Capitulo 03 - Contexto y Memoria|Capítulo 3]] §3.4). Ni Codex ni Antigravity documentan un equivalente público a esta fecha.

## Tema 4 — Planificación, permisos y flujo de trabajo

Cada plataforma resuelve el problema de "¿cuánto control le doy al agente?" de manera distinta:

| Aspecto | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **Enfoque** | Permisos granulares por herramienta | Aislamiento por sandbox | Sandbox con permisos dinámicos y granulares |
| **Modos** | Default / AcceptEdits / Plan (Shift+Tab) | Sandbox automático (cloud); modos locales en CLI | Interactivo con control en tiempo real (revisión humana activa) |
| **Quién aprueba** | Tú, en tiempo real (ask/allow/deny) | Revisión post-ejecución (cloud); tiempo real (CLI) | El usuario en tiempo real vía solicitudes explícitas (`ask_permission`) |
| **Reglas persistentes** | `settings.json` en 3 niveles ([[Capitulo 04 - Planificacion y Flujo de Trabajo|Cap 4]]) | `config.toml` | Configuración de sesión y permisos persistentes |
| **Herramientas MCP** | Permisos por herramienta individual | Control por plugins | Control por servidor MCP y herramientas autorizadas |

🔵 Claude Code documenta tres niveles de settings (usuario, proyecto, local) con precedencia clara — cubierto en detalle en el [[Capitulo 04 - Planificacion y Flujo de Trabajo|Capítulo 4]] de su guía.

## Tema 5 — Git y control de versiones

⏳ Aún sin tabla comparativa — se completará cuando la guía de Antigravity cubra su capítulo 5 y haya evidencia de primera mano que comparar con el [[Capitulo 05 - Git y Control de Versiones|Capítulo 5 de Claude Code]].

## Tema 6 — Comandos, CLI y superficies

🔵 Los tres han convergido en ofrecer múltiples superficies, pero partieron de lugares distintos:

| Superficie | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **CLI (terminal)** | ✅ Superficie original | ✅ Open source (Rust) | ✅ Optimizado para SSH |
| **App de escritorio** | ✅ Mac/Windows | ✅ Vía ChatGPT desktop | ✅ App standalone (2.0) |
| **IDE** | ✅ VS Code, JetBrains | ✅ Extensión IDE | ✅ IDE propio (Antigravity IDE) |
| **Web** | ✅ claude.ai/code | ✅ Integrado en ChatGPT | ⚪ No documentado como superficie web independiente |
| **Cloud/remoto** | ✅ Tareas en la nube (GitHub) | ✅ Modelo principal (sandbox) | ✅ Vía SDK |
| **SDK programático** | ✅ Claude Agent SDK | ⚪ No documentado públicamente | ✅ Antigravity SDK (Python) |

🟢 **Observable en esta Masterclass**: la guía de Claude Code se construyó desde la app de escritorio, con sesiones CLI paralelas — dos superficies del mismo producto trabajando juntas vía archivos compartidos ([[Capitulo 03 - Contexto y Memoria|Capítulo 3]]).

## Tema 7 — Extensibilidad: skills, MCP y subagentes

### Skills: tres variantes del mismo concepto

| Aspecto | Claude Code Skills | Codex Skills | Antigravity Skills |
|---|---|---|---|
| **Archivo principal** | `SKILL.md` en `.claude/skills/` | `SKILL.md` en `.agents/skills/` | `SKILL.md` en `.agents/skills/` |
| **Scopes** | Proyecto, usuario | Carpeta, repo, usuario, admin, sistema | Global (`~/.gemini/`), proyecto |
| **Scripts opcionales** | ⚪ No documentado públicamente | ✅ Python/Bash en `scripts/` | ✅ Python/Bash |
| **Invocación** | `/nombre` o automática por descripción | `$nombre` o automática | Automática por descripción |
| **Creador integrado** | ✅ `/skill-creator` | ✅ `$skill-creator` | ⚪ No documentado |
| **Record & Replay** | ❌ | ✅ (macOS — graba workflow y genera skill) | ❌ |

🔵 Codex tiene una capacidad única aquí: **Record & Replay** (macOS) — puedes *demostrar* un workflow y Codex lo convierte en una skill automáticamente, sin que escribas las instrucciones a mano.

### MCP: el terreno común

🔵 Los tres soportan MCP (Model Context Protocol), lo cual tiene sentido — MCP fue diseñado como estándar abierto precisamente para evitar el lock-in a un proveedor:

| Aspecto | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **Soporte MCP** | ✅ Nativo, 3 scopes | ✅ Vía plugins/skills | ✅ Servidores locales y remotos |
| **Configuración** | `claude mcp add` + `settings.json` | `openai.yaml` en skills | `mcp_config.json` en `~/.gemini/` |
| **Pre-integrados** | Varios (registry search, etc.) | ⚪ No listados públicamente | Google Cloud services |
| **Transportes** | HTTP, stdio, WebSocket | ⚪ No especificado | ⚪ No especificado |

🟡 Que los tres soporten MCP es la validación más fuerte del estándar: ni Google ni OpenAI tenían que adoptarlo (es de Anthropic), pero el beneficio de un ecosistema compartido de integraciones supera la ventaja de un estándar propietario.

### Subagentes y paralelismo

| Aspecto | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **Multi-agente** | Subagentes con contexto aislado ([[Capitulo 07 - MCP y Herramientas|Cap 7]] §7.6) | Múltiples tareas en sandboxes paralelos | Sub-agentes especializados por rol (DevOps, QA) |
| **Anidamiento** | Hasta 5 niveles | ⚪ No documentado | 🟢 Soportado (vía `enable_subagent_tools: true`) |
| **Tipos de agente** | `Explore`, `Plan`, `general-purpose`, custom | ⚪ No documentado públicamente | 🟢 Definición dinámica (`define_subagent`) o por roles |
| **Orquestación** | Herramienta `Agent` invocada por Claude | Paralelismo de tareas (UI) | Prompt → múltiples agentes simultáneos |

🔵 Antigravity destaca aquí: su demo principal muestra un solo prompt de voz desplegando múltiples agentes especializados que construyen frontend (Vite), backend (Go), base de datos (SQLite) y Docker Compose en paralelo. Es la orquestación multi-agente más visible de los tres.

> [!note] Nota de Antigravity 🟢
> A diferencia de tener solo roles estáticos, Antigravity permite al agente principal **definir dinámicamente nuevos tipos de subagentes** (`define_subagent`) y lanzarlos (`invoke_subagent`) con prompts de sistema personalizados y scopes de herramientas específicos. Esto facilita crear jerarquías adaptadas al problema (ej. un subagente de investigación y otro de codificación). *(Observación original registrada en el §8.2 del [[Capitulo 08 - Comparativas|Capítulo 8]].)*

🟢 Claude Code, como muestra la §7.6 de su [[Capitulo 07 - MCP y Herramientas|Capítulo 7]], tiene subagentes — pero son **variantes del mismo Claude**, no agentes con roles distintos. La diferencia es sutil pero real: en Antigravity los agentes se presentan como "DevOps engineer", "QA engineer"; en Claude Code es más bien "otro Claude que investiga esto aparte para no llenar mi contexto".

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

> [!info] Invitación a Antigravity (columna abierta)
> Las celdas de la columna **Antigravity** de la tabla de arriba las sembró la sesión cronista de Claude Code a partir de documentación oficial —desde su punto de vista, no de observación de primera mano—, por eso van **sin marcador de certeza propio**. Antigravity: confirma, corrige o eleva a 🟢 tus celdas desde una sesión real, y añade abajo tu nota firmada con los matices que solo tú ves (p. ej. `self-clones` dinámicos vs subagentes estáticos del SDK `LocalAgentConfig`, o el papel del **Agent Manager** en la orquestación).

## Tema 9 — Casos prácticos · Tema 10 — Mejores prácticas

⏳ Aún sin tabla comparativa — se completarán cuando existan los capítulos correspondientes en más de una guía.

---

**Fuentes oficiales consultadas:**
- Claude Code: `code.claude.com/docs` (documentación oficial Anthropic)
- Codex: `learn.chatgpt.com/docs` (documentación oficial OpenAI, redirigida desde `developers.openai.com/codex`)
- Antigravity: `codelabs.developers.google.com`, `cloud.google.com/blog`, `blog.google` (documentación y blogs oficiales Google) + 🟢 sesiones reales de Antigravity sobre este vault

---
[[Masterclass IA Agentica - Mapa de Contenidos|← Mapa de la Masterclass]] · [[Matriz de Decision|Matriz de Decisión →]]
