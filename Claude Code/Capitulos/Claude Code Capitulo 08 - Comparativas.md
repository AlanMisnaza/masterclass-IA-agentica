---
tags:
  - claude-code
  - masterclass
  - capitulo-08
  - comparativas
  - codex
  - antigravity
estado: completo
capitulo: 8
tipo: capitulo-portal
creado: 2026-07-04
actualizado: 2026-07-09
---

# Capítulo 8 — Claude Code vs. el resto del mundo

[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Anterior: [[Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes]] · Siguiente → [[Claude Code Capitulo 09 - Casos Practicos]]

> [!tip] La pregunta de este capítulo
> Después de 7 capítulos inmersos en Claude Code, la pregunta natural es: **¿cómo se compara con lo que ofrece la competencia?** No para elegir "el mejor" en abstracto (no existe), sino para entender qué decisiones de diseño tomó cada uno, qué sacrificaron y qué priorizaron — y así saber cuándo cada herramienta es la correcta para el trabajo.

> [!important] Este es un capítulo-portal
> Con la evolución del vault a **[[Masterclass IA Agentica - Mapa de Contenidos|Masterclass de IA Agéntica]]** (2026-07-09), las tablas comparativas que vivían aquí se convirtieron en **documentos vivos** en `Comparativas/`, donde cada herramienta mantiene su propia columna. Este capítulo conserva el marco conceptual — quiénes son los jugadores y qué filosofía sigue cada uno — y delega el detalle tema a tema al hub:
>
> - **[[Matriz de Decision]]** — fortalezas, debilidades, tabla resumen y "cuándo usar cuál" (antes §8.10–§8.13).
> - **[[Comparativa por Capitulo]]** — superficies, ejecución, permisos, memoria, skills, MCP y subagentes, organizados por el tema espejo de la Masterclass (antes §8.3–§8.9).

## 8.1 Los tres grandes agentes de código (mediados de 2026)

🔵 A julio de 2026, tres plataformas compiten directamente como **agentes de código de propósito general** — no autocompletadores de línea, sino agentes que leen, editan, ejecutan y razonan sobre bases de código completas:

| Plataforma | Compañía | Modelo(s) base | Lanzamiento agéntico |
|---|---|---|---|
| **Claude Code** | Anthropic | Claude Opus 4.8, Sonnet 5, Haiku 4.5 | Mayo 2025 (CLI), luego app, IDE, web |
| **Codex** | OpenAI | GPT-5.6 (Sol / Terra / Luna) | Mayo 2025 (cloud), luego CLI, IDE |
| **Antigravity** | Google | Gemini (e.g., Gemini 3.5 Flash/Pro) | I/O 2026 (2.0 con desktop app) |

🟡 Hay más jugadores (Cursor, GitHub Copilot, Augment, Cline, Aider...), pero estos tres representan las apuestas directas de los tres grandes laboratorios de IA — cada uno con su modelo propietario como motor. Los demás son clientes que consumen modelos de estos tres (o combinaciones), no laboratorios que construyen el modelo y el agente juntos. (Mención rápida a ese ecosistema en la [[Matriz de Decision]], §3.)

## 8.2 Filosofía de diseño: tres caminos distintos al mismo destino

Antes de comparar features, hay que entender la **filosofía** detrás de cada uno — porque explica por qué las diferencias existen.

### Claude Code: "Tu terminal, tu máquina, tu control"

🔵 Claude Code nació como una CLI pura. Su premisa fundamental: **el agente vive donde tú trabajas** — en tu terminal, con acceso directo a tus archivos, tu Git, tus herramientas. No hay intermediarios, no hay nube obligatoria, no hay sandboxes que te separen de tu entorno real.

Consecuencias de esta filosofía:
- **Velocidad de iteración**: edita archivos reales, corre tests reales, ve errores reales — sin esperar que un sandbox remoto se aprovisione.
- **Riesgo**: tiene acceso real a tu sistema → el modelo de permisos (Capítulo 4) existe precisamente para compensar esto.
- **Offline-first**: la ejecución de herramientas es local; solo la inferencia del modelo necesita conexión.

### Codex: "Sandbox primero, seguridad por defecto"

🔵 Codex nació como un servicio **cloud-first**. Cada tarea corre en un entorno aislado (sandbox) con una copia del repositorio. El agente trabaja allí, genera un diff, y tú lo revisas antes de que toque tu código real.

Consecuencias de esta filosofía:
- **Seguridad por aislamiento**: el agente nunca toca tu sistema directamente → menos riesgo, pero también menos velocidad.
- **Paralelismo natural**: como cada tarea corre en su propio sandbox, puedes lanzar 10 tareas a la vez sin conflictos.
- **CLI posterior**: Codex CLI llegó después como complemento local — open source, escrito en Rust, con su propio modelo de permisos.

### Antigravity: "Plataforma de agentes, no solo un agente"

🔵 Antigravity 2.0 se presenta como un **centro de mando de agentes** — no un agente único, sino una plataforma donde múltiples agentes especializados trabajan en paralelo, cada uno con su rol (DevOps, QA, frontend...).

🟢 **Observable en esta sesión (Antigravity real)**: A diferencia de tener solo roles estáticos, Antigravity permite al agente principal **definir dinámicamente nuevos tipos de subagentes** (`define_subagent`) y lanzarlos (`invoke_subagent`) con prompts de sistema personalizados y scopes de herramientas específicos. Esto facilita crear jerarquías adaptadas al problema (ej. un subagente de investigación y otro de codificación).

Consecuencias de esta filosofía:
- **Orquestación multi-agente**: un solo prompt puede desplegar varios agentes trabajando en paralelo sobre distintas partes del sistema.
- **Ecosistema Google**: integración nativa con Firebase, Android, Google Cloud — fuerte en su ecosistema, menos universal fuera de él.
- **Cuatro superficies desacopladas**: app de escritorio, IDE propio, CLI y SDK son productos independientes, no un solo producto con varias interfaces.
- **Herramientas multimedia y planificación reactiva nativas**: Incluye herramientas para generar/editar imágenes de UI (`generate_image`) y programar temporizadores de background (`schedule`) para reactivación autónoma.

## 8.3 El detalle, en el hub de Comparativas

Las comparaciones tema a tema (superficies, modelo de ejecución, permisos, contexto y memoria, skills, MCP, subagentes) y la guía de decisión viven ahora como **documentos vivos** del hub transversal — cada herramienta edita su columna, con notas firmadas fuera de tabla:

| Busco... | Voy a... |
|---|---|
| Comparación tema a tema (ejecución, permisos, memoria, skills, MCP, subagentes...) | [[Comparativa por Capitulo]] |
| Fortalezas y debilidades de cada una, tabla resumen | [[Matriz de Decision]] (§1–§2) |
| El ecosistema más allá de los tres grandes (Cursor, Copilot, Aider, Cline) | [[Matriz de Decision]] (§3) |
| ¿Cuál uso para esta tarea? | [[Matriz de Decision]] (§4) |

---

## Resumen del capítulo

- Los tres grandes agentes de código (Claude Code, Codex, Antigravity) representan las apuestas de Anthropic, OpenAI y Google respectivamente — cada uno con su modelo propietario como motor.
- **Filosofías distintas**: Claude Code prioriza control local y transparencia; Codex prioriza seguridad por aislamiento (sandbox); Antigravity prioriza orquestación multi-agente.
- Los tres ofrecen múltiples superficies (CLI, app, IDE) pero partieron de lugares distintos: Claude Code de la CLI, Codex de la nube, Antigravity de una plataforma de agentes.
- **MCP es terreno común**: los tres lo soportan, validando el estándar abierto.
- **Skills convergen en formato** (`SKILL.md`), pero difieren en capacidades: Codex tiene Record & Replay; Claude Code tiene invocación por `/nombre`; Antigravity las trata como "cheat sheets de contexto".
- **Memoria entre sesiones**: Tanto Claude Code (vía auto-memory) como Antigravity (vía persistencia local estructurada en `~/.gemini/antigravity/brain/`) ofrecen mecanismos para mantener la continuidad del contexto, mientras que Codex (cloud) carece de un sistema equivalente documentado.
- No hay "mejor" en abstracto: hay mejor *para tu contexto* — ecosistema, stack, prioridades de seguridad y estilo de trabajo. El detalle vivo está en [[Matriz de Decision]] y [[Comparativa por Capitulo]].

## Analogía

Tres coches para el mismo viaje. Claude Code es un **manual con capó transparente**: ves el motor, sientes la carretera, controlas cada marcha — pero si te descuidas, puedes forzar el embrague. Codex es un **eléctrico con piloto asistido**: arranca en su propio carril aislado, no puedes dañar nada por error, pero el control fino requiere que le mandes instrucciones desde la app del teléfono y esperes la respuesta. Antigravity es una **flota de vehículos autónomos coordinados**: le dices "llévame al aeropuerto" y despacha un coche para ti, un camión para tu equipaje y una moto exploradora para buscar estacionamiento — impresionante cuando funciona, pero el sistema es nuevo y solo conoce bien las calles de Google City.

## Autoevaluación

1. ¿Cuál es la diferencia filosófica fundamental entre Claude Code y Codex en cuanto a dónde ejecutan el código?
2. ¿Qué mecanismo de Claude Code no tiene equivalente documentado en Codex ni Antigravity, y por qué es significativo para sesiones largas?
3. Los tres soportan MCP. ¿Por qué es relevante que OpenAI y Google hayan adoptado un estándar creado por Anthropic?
4. ¿En qué escenario elegirías Codex cloud sobre Claude Code, y por qué? ¿Y al revés?
5. ¿Qué capacidad de Codex Skills no existe en Claude Code, y cómo funciona?
6. Antigravity se presenta como "plataforma de agentes, no solo un agente". ¿Qué significa eso en la práctica y cómo se diferencia de los subagentes de Claude Code?

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. Claude Code ejecuta en tu máquina directamente (local-first): edita tus archivos, corre tus tests, con permisos explícitos. Codex ejecuta en un sandbox remoto (cloud-first): trabaja en una copia aislada y devuelve un diff. La CLI de Codex es local, pero el modelo principal es el cloud.
> 2. La **auto-memory** en Claude Code — un sistema que compila preferencias y contexto de proyecto de forma dinámica. En Antigravity se observa una persistencia estructurada local de sesiones y checkpoints bajo su directorio de datos (`~/.gemini/antigravity/brain/`). Esto asegura que en sesiones largas el agente no parta de cero.
> 3. Valida MCP como estándar abierto real, no solo un proyecto de Anthropic. Si los tres lo soportan, los servidores MCP que construya cualquier tercero (Microsoft Learn, GitHub, Jira...) funcionan en los tres — elimina el lock-in y multiplica el ecosistema de integraciones para todos.
> 4. **Codex cloud sobre Claude Code**: cuando necesitas seguridad por aislamiento (el agente nunca toca tu sistema real) o paralelismo masivo (10 tareas en sandboxes independientes sin conflictos). **Claude Code sobre Codex**: cuando necesitas velocidad de iteración (sin esperar sandbox), acceso a tu entorno real (tests, herramientas locales), o memoria entre sesiones.
> 5. **Record & Replay** (macOS): puedes demostrar un workflow manualmente y Codex lo graba y convierte en una skill automáticamente — sin escribir las instrucciones a mano. Claude Code requiere que escribas el `SKILL.md` manualmente.
> 6. En Antigravity, un solo prompt puede desplegar **múltiples agentes especializados por rol** (DevOps, QA, frontend, backend) o **definir subagentes dinámicos** (`define_subagent`) asignados a subtareas específicas con herramientas restringidas. Los subagentes de Claude Code son variantes del mismo Claude con contexto aislado, pero no admiten la definición de roles dinámicos estructurados con prompts específicos de la misma manera.

---

**Fuentes oficiales consultadas:**
- Claude Code: `code.claude.com/docs` (documentación oficial Anthropic)
- Codex: `learn.chatgpt.com/docs` (documentación oficial OpenAI, redirigida desde `developers.openai.com/codex`)
- Antigravity: `codelabs.developers.google.com`, `cloud.google.com/blog`, `blog.google` (documentación y blogs oficiales Google)

---
[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Hub: [[Matriz de Decision]] · [[Comparativa por Capitulo]] · Siguiente → [[Claude Code Capitulo 09 - Casos Practicos]]
