---
tags:
  - masterclass
  - comparativas
  - matriz-de-decision
  - documento-vivo
creado: 2026-07-09
origen: "Capítulo 8 de la guía de Claude Code (§8.10–§8.13)"
---

# 🧭 Matriz de Decisión — ¿cuál herramienta para qué tarea?

[[Masterclass IA Agentica - Mapa de Contenidos|← Mapa de la Masterclass]] · Hermano: [[Comparativa por Capitulo]] · Semilla: [[Capitulo 08 - Comparativas]]

> [!important] Documento vivo — regla de columnas
> Esta matriz sintetiza fortalezas, debilidades y criterios de decisión. Cada herramienta edita **solo su propia columna/sección**; aportes fuera de tabla con callout firmado. Los juicios 🟡 comparativos los mantiene la sesión cronista de Claude Code — si una herramienta cree que un juicio es injusto, añade su nota firmada, no lo reescribe. Los marcadores 🟢 son relativos al autor de cada aporte.

## 1. Lo que cada uno hace mejor (y peor)

🟡 Esta sección es una síntesis con juicio — no una tabla de features, sino una valoración honesta basada en la documentación oficial revisada. Marcador 🟡 porque toda comparativa implica simplificación.

### Claude Code brilla en...

- **Control local y transparencia**: ves exactamente qué hace, en tu sistema, con permisos explícitos. Sin cajas negras.
- **Memoria entre sesiones**: la auto-memory es una ventaja real — el agente aprende tus preferencias y no las olvida.
- **Ecosistema de persistencia**: CLAUDE.md + rules + settings + memory = continuidad automática al cambiar de superficie ([[Capitulo 03 - Contexto y Memoria|Capítulo 3]]).
- **CLI como ciudadano de primera clase**: nació ahí, se nota. Composable con pipes, scripteable, integrable.

### Claude Code flaquea en...

- **Paralelismo limitado**: los subagentes son útiles pero no hay orquestación multi-agente de múltiples roles como en Antigravity.
- **Seguridad por confianza**: depende de que configures bien los permisos. Si le dices "allow all", tienes acceso total sin red de seguridad. Codex cloud elimina esa posibilidad por diseño.
- **Record & Replay**: no existe. Codex puede grabar tu workflow y convertirlo en skill; Claude Code requiere que escribas las instrucciones manualmente.

### Codex brilla en...

- **Seguridad por aislamiento**: el modelo sandbox-first es inherentemente más seguro — el agente nunca puede dañar tu sistema real.
- **Paralelismo de tareas**: lanza 10 tareas cloud en paralelo, cada una en su sandbox, sin conflictos.
- **Record & Replay**: convierte workflows demostrados en skills automáticamente.
- **Open source CLI**: Codex CLI es open source (Rust) — puedes inspeccionar, modificar y contribuir.

### Codex flaquea en...

- **Latencia cloud**: cada tarea necesita aprovisionar un sandbox, clonar el repo, instalar dependencias. Es más lento que editar archivos locales.
- **Memoria entre sesiones**: no documenta un sistema equivalente a la auto-memory.
- **Experiencia local secundaria**: la CLI llegó después; la experiencia principal sigue siendo cloud.

### Antigravity brilla en...

- **Orquestación multi-agente dinámica**: múltiples agentes especializados en paralelo es su propuesta central, potenciada por la capacidad de definir dinámicamente nuevos subagentes (`define_subagent`) adaptados al contexto de la tarea.
- **Ecosistema Google**: si tu stack es Firebase + Android + Google Cloud, la integración nativa es una ventaja enorme.
- **Cuatro superficies independientes**: puedes usar solo el CLI, solo el SDK, solo la app — no te fuerza a un paquete monolítico.
- **Skills como "cheat sheets de contexto"**: el enfoque de comprimir documentación en skills es pragmático y bien pensado.
- **Generación de UI/imágenes integrada (`generate_image`)**: capacidad nativa para prototipar y diseñar interfaces visuales o assets directamente desde prompts en el chat, facilitando el diseño interactivo con el usuario.
- **Automatización temporal en background (`schedule`)**: un programador integrado para temporizadores y cron jobs recurrentes que ejecutan código de manera desasociada en background y notifican al agente de forma autónoma.

### Antigravity flaquea en...

- **Madurez**: es el más nuevo (2.0 lanzado en I/O 2026). Menos tiempo en producción = menos refinamiento.
- **Documentación técnica**: la documentación oficial es más tutorial/marketing que especificación técnica. Detalles como el tamaño de contexto exacto o pricing cambian constantemente, aunque sus capacidades internas ahora son completamente observables y documentadas.
- **Ecosistema cerrado**: fuerte dentro de Google, pero la universalidad fuera de ese ecosistema no está demostrada.

## 2. La tabla resumen

| Dimensión | Claude Code | Codex | Antigravity |
|---|---|---|---|
| **Filosofía** | Tu terminal, tu control | Sandbox primero | Plataforma de agentes |
| **Ejecución** | Local | Cloud (+ CLI local) | Local (app/CLI) + remoto (SDK) |
| **Modelo** | Opus 4.8 / Sonnet 5 / Haiku 4.5 | GPT-5.6 (Sol/Terra/Luna) | Gemini (e.g., Gemini 3.5 Flash) |
| **CLI** | Propietaria | Open source (Rust) | Propietaria |
| **Permisos** | Granulares, 3 niveles | Sandbox (cloud) + modos (CLI) | Sandbox con permisos dinámicos y granulares (`ask_permission`) |
| **Memoria** | Auto-memory persistente | ⚪ No documentado | 🟢 Persistente en carpeta de datos local (`~/.gemini/antigravity/`) |
| **MCP** | ✅ 3 scopes | ✅ Vía plugins | ✅ Local y remoto |
| **Multi-agente** | Subagentes (mismo modelo) | Tareas paralelas (sandboxes) | Sub-agentes dinámicos (`define_subagent`) |
| **Skills** | SKILL.md, `/nombre` | SKILL.md, `$nombre`, Record & Replay | SKILL.md, automáticas |
| **Fuerza** | Control + memoria + CLI | Seguridad + paralelismo cloud | Orquestación multi-agente + UI/Imagen y Background Tasks |
| **Debilidad** | Seguridad por confianza | Latencia cloud | Madurez + lock-in Google |

## 3. ¿Y los otros? (mención rápida)

🟡 Más allá de los tres grandes laboratorios, existe un ecosistema de herramientas que merecen mención:

- **Cursor**: editor con IA integrada (usa modelos de Anthropic y OpenAI). No es un agente autónomo sino un IDE con IA como copiloto. Muy popular, excelente para desarrollo interactivo, pero no compite en la categoría de "agente que ejecuta solo".
- **GitHub Copilot**: el autocompletador más popular del mundo, ahora con modo agente. Ventaja: integración nativa con GitHub. Limitación: depende del ecosistema GitHub.
- **Aider**: CLI open source que puede usar múltiples modelos (Claude, GPT, local). No es de un laboratorio — es un cliente, no un motor.
- **Cline**: extensión de VS Code, open source, multi-modelo. Similar a Aider en filosofía.

La diferencia clave: estos últimos son **clientes que consumen modelos ajenos**, mientras que Claude Code, Codex y Antigravity son **el laboratorio que construye el modelo Y el agente**. Eso les da ventajas de optimización profunda (codex-1 optimizado para diffs limpios, Claude con tool use nativo, Gemini con integración Google) que un cliente genérico no puede replicar.

## 4. ¿Cuándo usar cuál? (guía de decisión)

🟡 Simplificación práctica — la respuesta honesta depende de tu contexto:

**Usa Claude Code cuando...**
- Quieres control total sobre tu entorno local.
- Necesitas que el agente recuerde tus preferencias entre sesiones.
- Tu stack es diverso (no atado a un ecosistema específico).
- Valoras la transparencia de ver exactamente qué hace el agente.
- Trabajas con la CLI como herramienta principal.

**Usa Codex cuando...**
- La seguridad por aislamiento es prioridad (entornos corporativos, código sensible).
- Necesitas lanzar muchas tareas en paralelo sin conflictos.
- Tu equipo ya está en el ecosistema OpenAI/ChatGPT.
- Quieres grabar workflows y convertirlos en skills automáticamente.

**Usa Antigravity cuando...**
- Tu stack es Google (Firebase, Android, Google Cloud) o quieres aprovechar motores Gemini avanzados.
- Necesitas orquestación multi-agente real (definiendo subagentes dinámicos a demanda).
- Requieres generar o editar interfaces visuales y assets en caliente (`generate_image`).
- Quieres programar flujos asíncronos y cron jobs en segundo plano (`schedule`).
- Prefieres una plataforma de agentes sobre un agente individual con superficies desacopladas.

**La verdad incómoda**: en la práctica, muchos equipos usan **más de uno**. Un agente para tareas rápidas locales (Claude Code o Codex CLI), otro para tareas largas en paralelo (Codex cloud), y skills/integraciones del ecosistema que ya usan (Antigravity si son Google, Codex si son OpenAI). La pregunta no es "cuál es el mejor" sino "cuál encaja mejor en este momento, para esta tarea".

---

**Fuentes oficiales consultadas:**
- Claude Code: `code.claude.com/docs` (documentación oficial Anthropic)
- Codex: `learn.chatgpt.com/docs` (documentación oficial OpenAI, redirigida desde `developers.openai.com/codex`)
- Antigravity: `codelabs.developers.google.com`, `cloud.google.com/blog`, `blog.google` (documentación y blogs oficiales Google) + 🟢 sesiones reales de Antigravity sobre este vault

---
[[Masterclass IA Agentica - Mapa de Contenidos|← Mapa de la Masterclass]] · [[Comparativa por Capitulo|Comparativa por Capítulo →]]
