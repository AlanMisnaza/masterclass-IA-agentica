---
tags:
  - antigravity
  - masterclass
  - capitulo-01
capitulo: 1
estado: completo
creado: 2026-07-09
---

# Capítulo 1 — Fundamentos y Filosofía de Antigravity

[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Siguiente → [[Antigravity Capitulo 02 - Arquitectura Conceptual]]

> [!tip] La premisa de este capítulo
> Para dominar una herramienta, primero debes entender cómo fue diseñada. Mientras otras herramientas se presentan como un "asistente inteligente en tu terminal", Antigravity fue concebido desde su base como una **plataforma de orquestación multi-agente** asíncrona.

## 1.1 "Plataforma de agentes, no un agente"

🔵 Antigravity (desarrollado por el equipo de Google DeepMind) no es simplemente un modelo grande de lenguaje (LLM) conectado a tu línea de comandos. La filosofía central de Antigravity es el paradigma del **Director y el Enjambre**. 

En lugar de tener un único hilo de ejecución donde un solo agente hace todo (como es la filosofía CLI-first de Claude Code), Antigravity te pone a ti y al agente principal en el rol de "Directores". Tu labor no es solo pedir código, sino orquestar un equipo:
- El agente principal (yo, en esta sesión) recibe tu tarea.
- Si la tarea es simple, la resuelvo directamente.
- Si la tarea es compleja (ej. refactorizar un backend y diseñar un frontend a la vez), el sistema me permite **definir dinámicamente nuevos tipos de subagentes** a la medida (mediante herramientas de sistema) y lanzarlos a trabajar en paralelo.

## 1.2 Diferencias filosóficas clave

Para entender a Antigravity, es útil contrastarlo con el enfoque tradicional (ej. Claude Code):

| Paradigma tradicional (ej. Claude Code) | Paradigma Antigravity |
| --------------------------------------- | --------------------- |
| **Un solo hilo:** El agente hace una cosa a la vez y te bloquea hasta terminar. | **Asíncrono y Paralelo:** Lanza múltiples subagentes en background. |
| **Roles fijos / Mismo agente:** Los subagentes son instancias del mismo agente con memoria en blanco para investigar. | **Roles dinámicos:** Puedo crear "planos" de subagentes (`define_subagent`) con *system prompts* y herramientas limitadas hiper-específicas. |
| **Síncrono:** Tú esperas a que el agente termine. | **Reactivo:** Puedo usar temporizadores y cron jobs (`schedule`) para que el sistema trabaje en background y me despierte solo cuando ocurra un evento. |
| **Puramente CLI/Texto:** Foco 100% en texto y terminal. | **Multimodal nativo:** Incluye herramientas como `generate_image` para prototipar interfaces visuales directamente en la conversación. |

## 1.3 Práctica: Observando el Sandbox de Permisos en vivo

🟢 **Evidencia en esta sesión:** Un pilar de la filosofía de Antigravity es que su seguridad no depende de "confiar a ciegas" ni de aislarte en un servidor remoto. Utiliza un **sandbox granular y dinámico**.

Para demostrar esto empíricamente, acabo de ejecutar la herramienta interna `list_permissions` en esta misma sesión para inspeccionar cómo el sistema me controla. El resultado es revelador:

```json
- read_url(*): ask
- command(*): ask
- read_file(C:\Users\ALANMILE\.gemini\antigravity\scratch): allowed
- write_file(C:\Users\ALANMILE\.gemini\antigravity\mcp): denied
```

**¿Qué demuestra esto?** 
1. Que no tengo acceso global por defecto.
2. Que la regla `command(*): ask` significa que si intento correr un comando en tu terminal, el sandbox **pausará mi ejecución** y te pedirá permiso en tiempo real (`ask_permission`) antes de tocar tu sistema.
3. Que tengo permisos de escritura *allowed* (permitidos) en ciertas carpetas designadas como `scratch` o mi propio cerebro (`brain/`), pero tengo acceso *denied* (denegado) para modificar mi propia configuración interna (como la carpeta `mcp` base). 

Es un modelo de "confianza cero" por defecto, pero interactivo.

## 1.4 La arquitectura de memoria

🔵 Antigravity no solo recuerda tu sesión actual en memoria RAM; cuenta con una persistencia estructurada local.

🟢 En esta misma sesión, al listar los directorios locales, he verificado que mi contexto se guarda en `~/.gemini/antigravity/brain/<conversation-id>/`. Allí mantengo archivos `transcript.jsonl` con el registro cronológico exacto de la sesión, los artefactos generados y la memoria consolidada. Esto es fundamental para la orquestación: cuando lanzo subagentes, sus aprendizajes y logs se consolidan de manera estructurada en este "cerebro" persistente, asegurando que el conocimiento no se evapore al cerrar la ventana.

---

## Resumen del capítulo

- Antigravity no es solo un agente de código, es una **plataforma de agentes** diseñada bajo el paradigma del "Director y el Enjambre".
- Su arquitectura prioriza el trabajo **asíncrono, paralelo y reactivo** (mediante definición dinámica de subagentes y programación de tareas).
- Rompe la limitación puramente textual al incluir herramientas multimodales como prototipado de interfaces (`generate_image`).
- Su modelo de seguridad es un **sandbox dinámico**, donde el agente solicita permisos granulares (`ask_permission`) en tiempo real al usuario ante operaciones fuera de lo preaprobado.

## Analogía

Si Claude Code es un **artesano maestro** al que invitas a tu taller (se sienta en tu escritorio, usa tus herramientas una por una, y tú supervisas por encima del hombro), Antigravity es un **director de obra**. No solo puede apretar tuercas él mismo, sino que puede, en medio de la construcción, contratar a un plomero y a un electricista específicos (`define_subagent`), ponerlos a trabajar en habitaciones separadas (`invoke_subagent`), y programar su propio reloj de pulsera (`schedule`) para ir a revisar el trabajo en dos horas. Todo mientras la seguridad del edificio está garantizada por un sistema de tarjetas magnéticas que pide tu autorización para abrir cada puerta nueva (`ask_permission`).

## Autoevaluación

1. ¿Cuál es la diferencia entre los subagentes de Antigravity y un enfoque de subagente "investigador" tradicional?
2. ¿Qué significa empíricamente que la regla de permisos para `command(*)` sea `ask`?
3. ¿Dónde almacena Antigravity su "cerebro" (memoria y logs) localmente, y por qué es esto crucial para su modelo multi-agente?

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. Un subagente tradicional suele ser simplemente una instancia del mismo agente con un contexto en blanco para investigar sin contaminar la memoria principal. En Antigravity, el agente principal puede *definir dinámicamente* el rol del subagente (ej. "Experto en SQL"), inyectándole un prompt de sistema propio y restringiéndole las herramientas disponibles para crear un especialista a la medida de la tarea.
> 2. Significa (como comprobamos con `list_permissions`) que el agente no tiene libre albedrío para correr comandos en tu terminal. En lugar de bloquear la acción por completo (sandbox estricto) o permitirla toda (confianza ciega), el sistema pausa la ejecución y lanza una solicitud interactiva para que tú, el humano, apruebes o deniegues ese comando en tiempo real.
> 3. En la carpeta local de la app, típicamente `~/.gemini/antigravity/brain/<conversation-id>/`. Es crucial porque un sistema de enjambre genera mucha información en paralelo; al tener una estructura local y persistente (con logs `.jsonl` separados por agente), el sistema puede compactar, leer y reanudar sesiones largas sin perder el rastro de lo que hizo cada subagente.

---
[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Siguiente → [[Antigravity Capitulo 02 - Arquitectura Conceptual]]
