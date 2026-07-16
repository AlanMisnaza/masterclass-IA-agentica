# Briefing interno: capacidades oficiales de Claude Code (investigación julio 2026)

Fuente primaria: https://code.claude.com/docs/en/ (vía agente claude-code-guide)

Este archivo es material de referencia para escribir los Capítulos 2-6 de la guía.
No mostrar tal cual al usuario — extraer y explicar pedagógicamente.

## 1. CLAUDE.md y memoria
- Instrucciones persistentes cargadas al inicio de cada sesión.
- Jerarquía: managed policy (empresa) > user (~/.claude/CLAUDE.md) > project (./CLAUDE.md) > local (./CLAUDE.local.md, gitignored).
- Imports con @path (@README, @docs/x.md) se expanden a contenido real.
- AGENTS.md: si el repo lo usa, CLAUDE.md puede importar @AGENTS.md.
- Path-specific rules en .claude/rules/ con frontmatter `paths:` — cargan solo si Claude lee archivos que matchean.
- Auto memory: ~/.claude/projects/<project>/memory/MEMORY.md, primeras 200 líneas/25KB se cargan siempre, resto on-demand. Topic files. /memory para ver/editar. autoMemoryEnabled:false para desactivar.
- /init genera CLAUDE.md analizando el repo; si ya existe, sugiere mejoras en vez de sobrescribir.
Fuente: code.claude.com/docs/en/memory.md

## 2. Permisos y modos
Modos: default (manual, solo lectura), acceptEdits (auto-acepta ediciones y comandos seguros comunes), plan (solo lectura/planificación), auto (classifier ML decide en background), dontAsk (solo allow rules preaprobadas), bypassPermissions (todo, solo en contenedores aislados).
Cambio: Shift+Tab cicla modos; --permission-mode al iniciar; permissions.defaultMode en settings.
Auto mode: classifier bloquea por defecto curl|bash, git reset --hard, git commit --amend tras push, terraform destroy, exfiltración de datos, credenciales en transcript. Permite por defecto: file ops en working dir, instalar deps de lockfiles, leer .env, HTTP read-only, push a rama propia.
Protected paths nunca auto-aprobados: .git/, .claude/, .vscode/, .npmrc, .mcp.json, etc. (excepto bypassPermissions).
settings.json permissions: allow/ask/deny con patrones tipo Bash(npm run lint), Edit(src/**/*.ts). Precedencia deny > ask > allow.
Fuente: code.claude.com/docs/en/permission-modes.md

## 3. Hooks
Comandos deterministas en puntos del lifecycle. Eventos: SessionStart/End, UserPromptSubmit, PreToolUse/PostToolUse/PostToolUseFailure, PermissionRequest/Denied, Notification, SubagentStart/Stop, InstructionsLoaded, ConfigChange, PreCompact/PostCompact, TaskCreated/Completed, Stop, Elicitation, WorktreeCreate/Remove, etc.
Tipos: command (shell), http (POST a URL), prompt (evaluación LLM 1 turno, default Haiku), agent (multi-turno con tools, experimental), mcp_tool.
Config en settings.json con matcher (por tool name/regex) y campo `if` (permission rule syntax).
I/O: input JSON por stdin; output exit 0 = normal, exit 2 = bloqueado (stderr es feedback a Claude), o JSON estructurado con permissionDecision.
Ubicación: ~/.claude/settings.json (todos proyectos), .claude/settings.json (proyecto, versionable), .claude/settings.local.json (proyecto, local).
Fuente: code.claude.com/docs/en/hooks-guide.md

## 4. Subagentes
Asistentes con su propio context window, system prompt, tools y permisos independientes. Built-in: general-purpose, Explore, Plan. Custom: .claude/agents/<name>/AGENT.md con frontmatter (name, description, model, tools, permissionMode, isolation:"worktree", enablePersistentMemory).
Invocación automática (Claude detecta match con description) o explícita (Task tool / /agent-name).
Background por defecto: Claude sigue trabajando mientras el subagente corre. Contexto aislado: el subagente NO comparte context window con la sesión principal; recibe solo la descripción de la tarea y devuelve un resumen.
Nested subagents hasta 5 niveles.
Fuente: code.claude.com/docs/en/sub-agents.md

## 5. MCP
Estándar abierto (modelcontextprotocol.io). Servidores exponen tools (funciones invocables), resources (datos de solo lectura), prompts (plantillas).
Transportes: HTTP (recomendado remoto, OAuth 2.0), SSE (deprecated), stdio (procesos locales), WebSocket (bidireccional persistente).
Scopes de config: local (~/.claude.json, privado a ti+proyecto), project (.mcp.json, compartido via git, requiere aprobación), user (~/.claude.json, todos tus proyectos), managed (enterprise). Precedencia: local > project > user > plugin > claude.ai connectors.
Auth: OAuth vía /mcp panel o `claude mcp login <name>`; dynamic headers; credenciales preconfiguradas.
Tool search: por defecto se difieren los esquemas de las tools MCP (solo nombres al inicio), se cargan on-demand con la herramienta ToolSearch — reduce overhead de contexto cuando hay muchos servidores MCP conectados. (Esto es literalmente lo que se ve en esta sesión: existe una lista de "deferred tools" y una herramienta ToolSearch).
Elicitation: los servidores pueden pedir input estructurado (formularios).
@server:protocolo://path para referenciar resources directamente.
/mcp__servidor__prompt para ejecutar prompts del servidor como slash command.
Comandos: claude mcp add/list/remove/get/login; /mcp en sesión.
Fuente: code.claude.com/docs/en/mcp.md

## 6. Contexto en conversaciones largas
Auto-compaction: cuando se llena la ventana de contexto, se resume automáticamente (proceso determinista, no decisión del LLM en el sentido de "elegir resumir", sino un mecanismo del sistema que dispara la compactación). /compact manual también disponible.
Sobrevive compactación: CLAUDE.md raíz (se relee de disco), auto memory (primeras 200 líneas/25KB).
No sobrevive automáticamente: CLAUDE.md anidados en subdirectorios (se recargan solo cuando se vuelve a leer un archivo de esa carpeta), instrucciones dadas solo en la conversación y no guardadas en CLAUDE.md/memoria.
/context muestra qué ocupa tokens (system prompt, memoria, entorno, MCP tools diferidas, historial).
Fuente: code.claude.com/docs/en/context-window.md

## 7. Integraciones/superficies
VS Code extension, JetBrains plugin, Desktop app (mac/win/linux beta, con GUI, sesiones cloud+local+SSH, computer use), Claude Code on the web (claude.ai/code, sesiones cloud vía GitHub, "Routines" programadas, "Remote Control" para teletransportarse a terminal local), GitHub Actions (anthropics/claude-code-action, responde a @claude en comentarios de PR), GitLab CI/CD, Slack, Chrome extension (browser automation), CLI pura (interactivo TUI o headless `claude -p`).
Fuente: code.claude.com/docs/en/platforms.md

## 8. Claude Agent SDK
Framework (Python/TS) para construir agentes custom con el mismo motor/tools/permisos que Claude Code, pero empaquetado para integración programática (backends, apps) en vez de CLI de terminal. Documentación de la relación exacta es más limitada/fragmentada que la de Claude Code propiamente. Categoría (b): documentado pero con detalles ambiguos.

## 9. Slash commands vs Skills
Slash commands (legado): .claude/commands/<name>.md, invocación 100% manual, contenido simple. Fusionados conceptualmente en Skills pero siguen funcionando.
Skills (estándar actual): .claude/skills/<name>/SKILL.md, frontmatter rico (invocation: user|auto, model, tools, allowedMcpTools, hidden, runInSubagent). invocation:auto permite que Claude decida invocar la skill solo con la descripción, sin que el usuario escriba "/".
Fuente: code.claude.com/docs/en/skills.md

## 10. settings.json — jerarquía
Precedencia (mayor a menor): Managed (enterprise/MDM, no se puede aflojar) > flags de línea de comandos > Local (.claude/settings.local.json, gitignored) > Project (.claude/settings.json, compartido en git) > User (~/.claude/settings.json).
Configurable: model, permissions (defaultMode/allow/deny/ask), env vars, hooks, theme, autoMemoryEnabled, MCP habilitados/deshabilitados, fallbackModel, sandbox, etc.
Fuente: code.claude.com/docs/en/settings.md

## 11. Features recientes relevantes (changelog, contexto: hoy es 2026-07-04)
- Checkpoints/rewind: /rewind permite recuperar estado previo a un /clear.
- Subagentes en background por defecto; sesiones sobreviven reinicios; anidamiento hasta 5 niveles; vista `claude agents` para monitorear varias sesiones en paralelo.
- Auto mode disponible también en Bedrock/Vertex/Foundry (requiere Opus 4.7+ en esos entornos).
- Modelos vigentes (corroborado independientemente por el propio system prompt de esta sesión): Claude Fable 5, Sonnet 5 (claude-sonnet-5, este es el modelo de esta sesión), Opus 4.8, Haiku 4.5.
Fuente: code.claude.com/docs/en/changelog.md

## 12. Slash commands de referencia rápida
/init, /memory, /mcp, /hooks, /code-review, /simplify, /security-review, /debug, /verify, /run, /batch, /loop, /schedule, /plan (o Shift+Tab a plan mode), /compact, /recap, /clear, /config, /plugin, /cd, /rewind, /context, /goal, /feedback.

## 13. Verificación adicional (segunda pasada de investigación, julio 2026)

### ⚠️ CONTRADICCIÓN ENTRE AGENTES: Claude Code como servidor MCP
Una segunda pasada de investigación NO encontró `claude mcp serve` en la documentación oficial actual (code.claude.com/docs/en/cli-reference ni /mcp), solo en fuentes de terceros (builder.io, GitHub de steipete, etc.). Lo que SÍ confirmó oficialmente en dirección "servidor/exposición" es distinto: `claude remote-control` (servidor de Remote Control para controlar la sesión desde claude.ai/móvil) y `claude gateway --config gateway.yaml` (gateway auto-hospedado para SSO/políticas empresariales, v2.1.195+).
**Tratamiento para la guía: NO afirmar `claude mcp serve` como hecho confirmado.** Presentarlo, si se menciona, como "reportado de forma consistente por fuentes de terceros pero no verificado en la documentación oficial en el momento de escribir esta guía" — exactamente el caso de uso del marcador ⚪/🟡 que definimos en el Capítulo 1.

### Claude Code como SERVIDOR MCP (no solo cliente) — dato original, ver contradicción arriba antes de usar
- `claude mcp serve` expone las propias herramientas de Claude Code (edición de archivos, ejecución de comandos) como servidor MCP stdio para que OTROS clientes MCP se conecten a él.
- Arquitectura: JSON-RPC 2.0 sobre stdio. Cada conexión spawna un proceso nuevo, sin estado compartido entre conexiones.
- Requiere haber aceptado permisos interactivamente al menos una vez antes; `--dangerously-skip-permissions` para modo servidor headless (sin supervisión).
- Clientes que pueden conectarse: Claude Desktop, Cursor, Windsurf/Devin Desktop, otros clientes MCP.
- Limitación: el servidor solo expone herramientas, no su propio razonamiento; el cliente que lo invoca es responsable de la confirmación de usuario en cada tool call.
- Esto es un dato clave para el Capítulo 6: Claude Code no es solo un consumidor de MCP, también puede ser el proveedor.
Fuente: code.claude.com/docs/en/mcp (líneas 869-926), code.claude.com/docs/en/agent-sdk/mcp

### Integraciones de editor ampliadas
Además de VS Code y JetBrains: soporte confirmado para Cursor (vía extensión VS Code, `cursor:extension/anthropic.claude-code`), Devin Desktop (compatible vía extensión VS Code), Zed (mencionado explícitamente en `/terminal-setup`). NO confirmado: Neovim, Emacs, Sublime Text.

### Precios y planes (julio 2026) — CONFIRMADO
Claude Code está incluido en la misma suscripción de Claude (no hay compra adicional):
| Plan | Precio | Incluye Claude Code |
|---|---|---|
| Pro | $20/mes ($17/mes facturación anual) | Sí |
| Max | desde $100/mes (tiers 5x/20x) | Sí |
| Team | por asiento, precio no público | Sí |
| Enterprise | a consultar | Sí |
Los límites de uso se comparten entre Claude web/desktop/mobile y Claude Code. Alternativa: pago por token vía API/Console (workspace "Claude Code" autogenerado en platform.claude.com). Costo real reportado: ~$13/desarrollador/día de media, rango típico $150-250/desarrollador/mes en despliegues empresariales.
Fuente: claude.com/pricing, code.claude.com/docs/en/costs, support.claude.com (art. 11145838)

### Modelos — tabla de precios exacta confirmada
| Modelo | ID | Contexto | Salida máx. | Cutoff | Precio in/out por MTok |
|---|---|---|---|---|---|
| Claude Fable 5 | claude-fable-5 | 1M | 128k | ene-2026 | $10/$50 |
| Claude Opus 4.8 | claude-opus-4-8 | 1M | 128k | ene-2026 | $5/$25 |
| Claude Sonnet 5 (default) | claude-sonnet-5 | 1M | 128k | ene-2026 | $3/$15 (promo $2/$10 hasta 31-ago-2026) |
| Claude Haiku 4.5 | claude-haiku-4-5-20251001 | 200k | 64k | feb-2025 | $1/$5 |
Nota: todos los IDs son "pinned snapshots", no punteros que cambian solos. Opus 4.1, Sonnet 3.7 y Haiku 3.5 ya están retirados o en retiro (Opus 4.1 se retira 5-ago-2026).
Fuente: platform.claude.com/docs/en/docs/about-claude/models/overview

### Changelog reciente relevante (mayo-junio 2026, semanas 22-26)
- Opus 4.8 pasó a ser default en Max/Team Premium/Enterprise pay-as-you-go, con "high effort" por defecto; nuevo modo `/effort xhigh`.
- Dynamic workflows: orquestar docenas/cientos de subagentes desde un script.
- Auto mode expandido a Amazon Bedrock, Google Cloud Agent Platform, Microsoft Foundry.
- Subagentes anidados hasta 5 niveles; permisos de subagentes en background ahora se comunican a la sesión principal (antes se auto-denegaban silenciosamente).
- `/cd` para cambiar de directorio sin romper la cache de prompt; `--safe-mode` para arrancar sin customizaciones; `fallbackModel` con hasta 3 modelos de respaldo en orden.
- Artifacts (beta): convierte el output de una sesión en una página compartible que se actualiza in-place.
- `claude mcp login` / `claude mcp logout` sin menú interactivo.
- Reglas deny/ask ahora pueden filtrar por parámetros de la herramienta, no solo por nombre: sintaxis `Tool(param:value)`, ej. `Agent(model:opus)`.
- Otras features agénticas mencionadas: Ultrareview (flota de agentes cazadores de bugs en la nube), Ultraplan (planificar en la nube, ejecutar remoto), Computer use (research preview: Claude abre apps nativas y hace clic en la UI), Routines (tareas recurrentes programadas), Remote Control (continuar sesión desde el móvil), Channels (push de eventos vía webhook).
Fuente: code.claude.com/docs/en/whats-new

### Claude Agent SDK — instalación confirmada
- TypeScript/Node: `npm install @anthropic-ai/claude-agent-sdk`
- Python (requiere 3.10+): `pip install claude-agent-sdk`
- Mismas herramientas, mismo bucle de agente y gestión de contexto que Claude Code; pensado para que otros programas embeban el mismo motor.
Fuente: code.claude.com/docs/en/agent-sdk/overview

## 14. Verificación cruzada final (tercera pasada)
- Confirmado oficialmente (claude.com/pricing): Pro $17-20/mes, Max desde $100/mes (tiers 5x/20x), Team $20-25/asiento/mes, Enterprise asiento+consumo API separado. Claude Code incluido en el asiento, sin costo adicional, límites de uso compartidos con Claude web/desktop/móvil.
- Nueva gama de modelos por encima de Opus 4.8: **Claude Fable 5** (`claude-fable-5`, GA 9-jun-2026, mismo precio $10/$50 que antes) y **Claude Mythos 5** (`claude-mythos-5`, mismas capacidades que Fable 5 pero SIN clasificadores de seguridad que puedan rechazar solicitudes; acceso solo por invitación vía "Project Glasswing", pensado para flujos de ciberseguridad defensiva). Hubo un incidente de disponibilidad de ambos modelos con redeploy posterior ("acceso restaurado") — no se especula sobre la causa, solo se confirma el hecho del incidente.
- Integraciones ampliadas confirmadas: Desktop app (mac/win x64/ARM64), Web, Slack (@Claude en un hilo devuelve un PR), y **Apple Xcode** ahora soporta el Claude Agent SDK oficialmente.
- El antiguo "Claude Code SDK" fue renombrado a **Claude Agent SDK**, reflejando que ya se usa para agentes más allá de programación pura.
- Confirmado con fuente oficial: MCP como cliente (Jira, Sentry, Statsig, PostgreSQL, Figma, Slack, Gmail como ejemplos oficiales) + `claude mcp login/logout` (nuevo, sin menú interactivo).

## Nota de fiabilidad
Los apartados 1,2,3,4,5,7,9,10 están marcados por el agente como "documentación oficial completa/alta confianza". El apartado 6 (contexto) es oficial pero con detalle parcial del algoritmo exacto de compactación (no se especula sobre eso). El apartado 8 (Agent SDK) tiene confianza baja/detalles ambiguos — tratar con cautela en el capítulo. El punto sobre "arquitectura interna (pesos, atención, entrenamiento)" se marca explícitamente como NO público — no especular nunca sobre eso.
Cifras de versión (v2.1.19x) y numeritos exactos de pricing/context window deben tratarse como orientativos, no citarse como verdad absoluta cerrada en el texto final sin matizar "según el estado documentado a la fecha de esta guía".
