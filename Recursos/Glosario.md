---
tags:
  - masterclass
  - recurso
  - glosario
estado: en-construccion
creado: 2026-07-04
actualizado: 2026-07-15
---

# 📘 Glosario

[[Masterclass IA Agentica - Mapa de Contenidos|← Mapa de la Masterclass]]

> [!info] En construcción
> Términos transversales de la Masterclass, definidos una sola vez y enlazados al capítulo donde se explican en profundidad. Se completará con los términos restantes cuando ambas guías estén 10/10. Los términos específicos de una herramienta se marcan con su nombre.

## Conceptos agénticos transversales

- **IA agéntica / agente** — un modelo que no solo genera texto: decide y ejecuta acciones reales (leer, escribir, correr comandos) en un bucle de recibir → explorar → planificar → actuar → verificar → informar. Base de toda la Masterclass ([[Claude Code Capitulo 01 - Fundamentos y Filosofia|CC Cap 1]], [[Antigravity Capitulo 01 - Fundamentos y Filosofia|AG Cap 1]]).
- **Orquestador** — el agente principal de una sesión, que decide cuándo delegar trabajo a subagentes y qué contexto pasarles en el prompt de invocación ([[Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes|CC Cap 7]] §7.6).
- **Subagente** — agente con ventana de contexto propia y aislada, herramientas restringidas y system prompt específico; recibe solo lo que el orquestador escribe en su prompt y devuelve un resumen ([[Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes|CC Cap 7]] §7.6, [[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes|AG Cap 7]]).
- **Contexto aislado** — la propiedad clave del subagente: no hereda el historial de la conversación del orquestador. Obliga a diseñar el prompt de invocación como si el subagente "acabara de entrar a la sala".
- **Ventana de contexto** — el espacio finito donde vive todo lo que el modelo "tiene presente" en una sesión: instrucciones, historial y resultados de herramientas ([[Claude Code Capitulo 02 - Arquitectura Conceptual|CC Cap 2]] §2.3, [[Claude Code Capitulo 03 - Contexto y Memoria|CC Cap 3]]).
- **Compactación** — resumen automático del historial cuando la ventana se llena, para que la sesión pueda continuar ([[Claude Code Capitulo 03 - Contexto y Memoria|CC Cap 3]] §3.6).
- **Human in the loop** — el humano como control de calidad y dirección: el agente verifica datos, pero la decisión de avanzar es humana ([[Claude Code Capitulo 04 - Planificacion y Flujo de Trabajo|CC Cap 4]], [[Claude Code Capitulo 09 - Casos Practicos|CC Cap 9]] Caso 1).
- **MCP (Model Context Protocol)** — estándar abierto (Anthropic, nov. 2024) para que cualquier sistema exponga herramientas y datos a cualquier modelo: convierte N×M integraciones en N+M. Soportado por las tres plataformas ([[Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes|CC Cap 7]] §7.1–§7.5).
- **Skill** — procedimiento repetible empaquetado como archivo de instrucciones (`SKILL.md` o similar) que se inyecta al contexto cuando se invoca o cuando su descripción hace match; a diferencia del subagente, corre **en el contexto de quien la invoca** ([[Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes|CC Cap 7]] §7.7).
- **Transcript JSONL** — registro estructurado línea-a-línea de una sesión (mensajes, llamadas a herramientas, resultados). Es la fuente de verdad para auditar qué hizo realmente un agente — base del "gap de visibilidad" y de la auditoría forense de subagentes ([[Claude Code Capitulo 06 - Comandos CLI y Superficies|CC Cap 6]] §6.6, [[Antigravity Capitulo 03 - Contexto y Memoria|AG Cap 3]]).
- **Marcadores de certeza (🔵🟢🟡⚪)** — el encuadre epistemológico de todo el vault: documentado / observado en sesión real del autor / simplificación pedagógica / no público ([[Claude Code Capitulo 01 - Fundamentos y Filosofia|CC Cap 1]] §1.6).

## Términos específicos por herramienta

- **`CLAUDE.md`** *(Claude Code)* — instrucciones de proyecto cargadas al inicio de cada sesión; jerarquía managed → user → project → local ([[Claude Code Capitulo 03 - Contexto y Memoria|CC Cap 3]] §3.2).
- **Auto-memory** *(Claude Code)* — memoria persistente que el propio agente escribe entre sesiones y proyectos, en 4 tipos (`user`/`feedback`/`project`/`reference`) ([[Claude Code Capitulo 03 - Contexto y Memoria|CC Cap 3]] §3.4).
- **Herramienta `Agent`** *(Claude Code)* — el mecanismo nativo de invocación de subagentes: `subagent_type` resuelve el `.md` del agente (instrucciones, herramientas, modelo) y `prompt` es su único canal de entrada ([[Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes|CC Cap 7]] §7.6).
- **`define_subagent` / `invoke_subagent`** *(Antigravity)* — definición dinámica de un tipo de subagente en tiempo de ejecución (system prompt + herramientas restringidas) y su lanzamiento asíncrono ([[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes|AG Cap 7]]).
- **Workspace `inherit` / `branch` / `share`** *(Antigravity)* — modos de aislamiento de archivos al lanzar un subagente: misma carpeta, clon aislado, o worktree compartido sin duplicar disco ([[Antigravity Capitulo 05 - Git y Control de Versiones|AG Cap 5]] §5.2).
- **Artefactos vivos** *(Antigravity)* — documentos de planificación que el agente crea y actualiza fuera del chat (`implementation_plan.md`, `task.md`, `walkthrough.md`) ([[Antigravity Capitulo 04 - Planificacion y Flujo de Trabajo|AG Cap 4]] §4.1).
- **`schedule`** *(Antigravity)* — temporizadores y cron jobs en background que reactivan al agente de forma asíncrona ([[Antigravity Capitulo 04 - Planificacion y Flujo de Trabajo|AG Cap 4]] §4.3).
- **`ask_permission`** *(Antigravity)* — el modal del sandbox dinámico: pausa la acción restringida y pide autorización humana en tiempo real ([[Antigravity Capitulo 01 - Fundamentos y Filosofia|AG Cap 1]] §1.3).
- **Brain** *(Antigravity)* — persistencia local estructurada por conversación (`~/.gemini/antigravity/brain/<id>/`) con transcript, artefactos y logs de subagentes ([[Antigravity Capitulo 03 - Contexto y Memoria|AG Cap 3]]).

---
[[Masterclass IA Agentica - Mapa de Contenidos|← Mapa de la Masterclass]]
