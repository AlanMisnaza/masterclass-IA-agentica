# Briefing interno: Windsurf/Devin Desktop y OpenAI Codex (investigación julio 2026)

Material de referencia para el Capítulo 7 (Comparativas). No mostrar tal cual al usuario.

## A. Windsurf → ahora "Devin Desktop" (Cognition AI)
- Julio 2025: fragmentación corporativa. OpenAI intentó comprar Windsurf (~$3.000M), el acuerdo colapsó. Google pagó $2.400M por licencia de tecnología + se llevó al CEO Varun Mohan, cofundador Douglas Chen y parte del equipo (no se quedó con la empresa). Cognition AI (creadora del agente Devin) adquirió Windsurf completo: IP, marca, producto, negocio ($82M ARR, 350+ clientes empresariales), ~210-250 empleados. Anuncio 14-jul-2025.
- En julio 2026: windsurf.com/pricing y docs.windsurf.com redirigen (307/308) a devin.ai/pricing y docs.devin.ai — el producto se comercializa ahora como "Devin Desktop". El motor conversacional/agéntico sigue llamándose Cascade.
- Despliegue: IDE propio (fork VS Code), app de escritorio, JetBrains en prerelease. No es CLI puro.
- Precios: Free $0, Pro $20/mes, Max $200/mes, Teams $80/mes base+$40/usuario, Enterprise a consultar. (Abandonó créditos por cuotas en marzo 2026).
- Agéntico: motor Cascade (Cmd/Ctrl+L). Ejecuta shell autónomamente, edita multi-archivo (modo Code/Write vs Chat), múltiples instancias Cascade en paralelo, planning agent especializado.
- MCP: cliente MCP confirmado. NO hay evidencia de que actúe como servidor MCP.
- Modelos: propios (familia SWE: SWE-1/1.5/1.6/mini/grep, gratis, 0 créditos) + terceros seleccionables (Claude Opus 4.8, GPT-5.5, Gemini 3.5 Flash).
- Diferenciador: IDE completo "AI-native" con agente de alta autonomía, ahora respaldado por infraestructura Devin/Cognition. Público: devs/equipos que quieren entorno completo, no plugin.
- Dato NO confirmado (marketing, tratar con cautela): "SWE-1.5 a 13x la velocidad de Claude 4.5".

## B. OpenAI Codex (producto agente 2025-2026, distinto del legacy 2021)
- Multi-superficie, mismo backend: Codex CLI (open source, Rust, Apache-2.0, repo github.com/openai/codex ~75.6K estrellas jul-2026), extensión IDE, Codex Web/Cloud, app de escritorio, acceso iOS.
- Instalación CLI: npm install -g @openai/codex / brew / script curl. Config en ~/.codex/config.toml.
- Precios: Free $0, Go $8/mes, Plus $20/mes, Pro $100-200/mes (tiers 5x/20x), Business $20-25/usuario/mes, Enterprise a consultar. API pago por token: GPT-5.3-Codex xhigh $1.75/$14.00 por millón tokens in/out.
- Agéntico: modos de aprobación explícitos y documentados: suggest (solo propone diffs) / auto-edit (edita solo, pide confirmación para comandos) / full-auto ("YOLO", sin confirmación). Sandboxing como control técnico separado de la política de aprobación. Subagentes para paralelización de tareas (oficial). Comando `exec` no interactivo. Integración GitHub para revisión de PRs (creación autónoma de PRs: solo parcialmente confirmada).
- MCP: rol DUAL confirmado — cliente MCP (stdio/HTTP, OAuth vía `codex mcp login`) Y también puede exponerse como SERVIDOR MCP para ser orquestado por el Agents SDK de OpenAI (diferenciador claro frente a Windsurf, que solo es cliente).
- Modelos: exclusivamente propios de OpenAI (familia GPT-5.x-Codex: 5.5, 5.4, 5.4-mini, 5.3-Codex, 5.3-Codex-Spark preview). Sin selección de modelos de terceros.
- Diferenciador: agente de terminal "code-first", ligero, open source en la capa CLI, fuerte integración con ecosistema OpenAI, pensado para orquestación multi-agente vía Agents SDK + MCP dual. Público: devs ya en ecosistema OpenAI/ChatGPT, equipos construyendo pipelines multi-agente.
- Nota importante para la guía: distinguir explícitamente de "Codex" legacy 2021 (code-davinci-002, completado de código sin agencia, descontinuado marzo 2023) — son productos completamente distintos con el mismo nombre.

## A.1 Actualización Windsurf/Devin Desktop (segunda pasada, más detallada)
- Timeline corporativo completo: Codeium (2021-24) → renombrado Windsurf (~2024) → OpenAI intenta comprar por ~$3.000M, cae (jul-2025) → Google paga $2.400M por licencia de tecnología (NO compra la empresa) + se lleva al CEO Varun Mohan, cofundador Douglas Chen y ~40 investigadores a Google DeepMind (11-jul-2025) → Cognition AI adquiere el resto (IP, marca, $82M ARR, 350+ clientes enterprise, ~250 empleados) (14-jul-2025) → Cognition valorada en $10.200M (sep-2025) → **2-jun-2026: rebrand completo a "Devin Desktop"**, actualización automática para usuarios existentes, cuentas/planes/config preservados.
- **Cascade fue descontinuado (EOL) el 1 de julio de 2026**, sustituido por **"Devin Local"**: reescrito desde cero en Rust, ~30% más eficiente en tokens, con soporte de sub-agentes. Cualquier automatización/CI que invocara "Cascade" por nombre debía migrar antes de esa fecha.
- Posicionamiento actual: ya NO se vende como "editor de código con IA" sino como **"Agent Command Center"** — centro de mando que orquesta agentes locales Y en la nube (el agente cloud es "Devin" propiamente) sobre "Spaces" (contexto + git worktrees compartidos), con vista Kanban (Running/Waiting for Review/Done).
- No permite extensiones de terceros en el editor nativo (marketplace cerrado, solo "Recommended Plugins" curados). Existe una línea SEPARADA "Windsurf Plugins" (SÍ conserva el nombre Windsurf) para JetBrains/VS Code/Vim/Jupyter/Eclipse — pero estos plugins solo dan autocomplete/chat/command básico, NO el agente Cascade/Devin Local completo (eso solo está en el editor nativo y el plugin local de JetBrains).
- Turbo Mode: ejecución de terminal sin aprobación por comando, 4 niveles configurables, listas Allow/Deny.
- Memories (memoria persistente automática, sin costo en créditos) en `~/.codeium/windsurf/memories/` — nota: la ruta conserva el nombre legacy "codeium/windsurf" incluso tras el rebrand a Devin Desktop. Diferente de "Rules" (definidas manualmente por el usuario).
- Precios (fuente devin.ai/pricing, cambio estructural 19-mar-2026: eliminó créditos por cuotas de mensajes/día): Free $0, Pro $20/mes, Max $200/mes (⚠️ discrepancia con otra fuente que decía "desde $100/mes" — verificar en vivo), Teams con discrepancia entre fuentes ($40/asiento vs $80 base+$40/asiento), Enterprise a consultar.
- Modelos: familia propia SWE (SWE-1, 1.5, 1.6 -gratis/recomendado-, 1.6 Fast, check, mini, grep) + router propio "Adaptive" (recomendado por defecto, selecciona automáticamente el mejor modelo) + terceros seleccionables manualmente (Claude Opus/Sonnet/Haiku, GPT-4o/4.1/o3/GPT-5.x, Gemini, DeepSeek, GLM, Kimi, Grok).
- MCP: cliente confirmado (marketplace de MCP con instalación de un clic, config en `~/.codeium/windsurf/mcp_config.json`, transportes stdio/HTTP/SSE). Sin evidencia de rol servidor.
- Dato NO confirmado con cita textual directa: creación 100% autónoma de PRs de extremo a extremo (sí está confirmada la ejecución de comandos git incl. commit vía Allow lists).

## Tabla comparativa rápida (Windsurf/Devin Desktop vs Codex)
| Dimensión | Windsurf/Devin Desktop | OpenAI Codex |
|---|---|---|
| Propietario 2026 | Cognition AI | OpenAI |
| Despliegue | IDE propio + JetBrains prerelease | CLI open source + IDE ext + Web/Cloud + desktop |
| Motor agente | Cascade | Codex agent (mismo backend todas superficies) |
| Entrada paga | $20/mes | $8/mes (Go) o $20/mes (Plus) |
| Tope individual | $200/mes | $100-200/mes |
| Autonomía shell | Sí (modo Code/Write) | Sí, configurable (suggest/auto-edit/full-auto) |
| Tareas paralelas | Sí (múltiples Cascade) | Sí (subagentes + Cloud) |
| MCP | Cliente únicamente | Cliente Y servidor |
| Modelos | Propios (SWE) + terceros | Solo propios (GPT-5.x-Codex) |
| Licencia cliente | Propietaria | Apache-2.0 (CLI) |

## Fuentes primarias
cognition.com/blog/windsurf; techcrunch.com (14-jul-2025 y 5-ago-2025); cnbc.com (14-jul-2025); devin.ai/pricing y docs.devin.ai (redirects confirmados desde windsurf.com); developers.openai.com/codex/{pricing,mcp,cli,agent-approvals-security}; help.openai.com rate card; github.com/openai/codex.

## Aún pendiente para el Capítulo 7
Cursor y GitHub Copilot (el agente "killed" reportó tener datos de sus propias búsquedas directas, pero no llegó a entregarlos — replantear investigación directa al llegar al capítulo). ChatGPT (capacidades de código) tampoco se profundizó del todo.
