---
tags:
  - antigravity
  - masterclass
  - capitulo-03
capitulo: 3
estado: completo
creado: 2026-07-09
---

# Capítulo 3 — Contexto y Memoria

[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 02 - Arquitectura Conceptual]] · Siguiente → [[Antigravity Capitulo 04 - Planificacion y Flujo de Trabajo]]

> [!tip] La premisa de este capítulo
> En un sistema donde operan múltiples agentes en paralelo, la memoria no puede ser solo una "ventana de chat". Antigravity necesita un cerebro local estructurado para poder consolidar los aprendizajes de su enjambre sin perder el hilo.

## 3.1 La anatomía de la memoria: El "Brain"

A diferencia de modelos basados en la nube que olvidan tu proyecto en cuanto cierras la pestaña, o de sistemas que compactan todo en un solo archivo Markdown, Antigravity cuenta con una estructura local persistente por defecto. 

🔵 Cada conversación (incluyendo esta que estás leyendo) tiene un identificador único. Toda la información de esa conversación se guarda localmente en el directorio de la aplicación, típicamente en `~/.gemini/antigravity/brain/<conversation-id>/`. 

Esta carpeta no es solo un registro de texto plano, sino una base de datos estructurada con:
- **Scratch**: Archivos de datos temporales.
- **Artefactos**: Documentos generados durante la planificación (ej. `implementation_plan.md`, `task.md`).
- **Logs del sistema**: Transcripciones en formato JSON Lines (`.jsonl`).

## 3.2 Evidencia en vivo: El registro JSONL

🟢 **Observable en esta sesión:** Para ilustrar esto con datos reales, acabo de inspeccionar mi propia carpeta de "cerebro" (`brain/fffd9951-2f5e-46f4-a2aa-9437687b068b`). He encontrado el archivo `.system_generated/logs/transcript.jsonl`, que en este mismo instante pesa aproximadamente 222 KB.

¿Por qué se guarda en JSONL en vez de simple texto? 
Porque cada línea es un objeto JSON estructurado que representa un paso o acción específica (`step_index`, `source`, `type`, `status`, `content`, `tool_calls`). 
Esto permite que, si tú cierras la aplicación y la abres mañana, Antigravity lea exactamente qué herramientas usó y qué errores se encontraron, reconstruyendo el estado mental exacto.

## 3.3 Contexto compartido: Orquestación del enjambre

El verdadero poder de esta memoria estructurada se despliega cuando usamos subagentes. 

🟡 *Simplificación pedagógica*: Si yo lanzo un subagente investigador, ¿empieza desde cero sin saber nada del proyecto? 
No. Al invocar un subagente (`invoke_subagent`), Antigravity le permite heredar el contexto y los permisos. El subagente generará su propio ID de conversación y su propio `transcript.jsonl`. 
Cuando el subagente termina, se comunica conmigo (el agente principal) enviándome un mensaje asíncrono con los resultados consolidados de su trabajo. 

Este diseño "desacoplado pero conectado" asegura que:
1. El contexto de mi (el agente principal) ventana no se sature de ruido con los mil comandos de búsqueda que hizo el investigador.
2. Queda un rastro perfecto de auditoría de lo que hizo el investigador en su propio archivo `transcript.jsonl`.

## 3.4 Manejo de la Ventana de Contexto (Context Window)

Incluso con los potentes motores Gemini (que manejan 1 o 2 millones de tokens), meter basura al contexto degrada la calidad de la respuesta.
- Antigravity solo inyecta los resultados consolidados y los resúmenes enviados por los subagentes.
- Las herramientas de búsqueda (`grep_search`) truncan las salidas excesivamente largas (por defecto a 50 coincidencias) para proteger la ventana.
- La memoria a largo plazo (instrucciones, preferencias) puede almacenarse en archivos de instrucciones de proyecto (`skills/`), y el sistema los carga a demanda solo cuando su descripción hace match semántico con la intención del usuario.

---

## Resumen del capítulo

- La memoria de Antigravity reside en un "Cerebro" local (`~/.gemini/antigravity/brain/`) que persiste la información entre reinicios.
- Las conversaciones no son simple texto, sino eventos estructurados en archivos `transcript.jsonl`, permitiendo una reconstrucción fiel del estado.
- Esta memoria descentralizada es vital para la orquestación multi-agente: cada subagente tiene su propio historial, evitando saturar la ventana de contexto del agente director.
- El sistema protege la ventana de contexto filtrando resultados y delegando tareas en background para mantener el foco en la planificación de alto nivel.

## Analogía

Imagina un hospital (Antigravity). El agente principal es el médico de cabecera. La memoria no es lo que el médico recuerda en su cabeza en ese instante, sino el **historial clínico estructurado** (el Brain y los JSONL) del paciente. Si el médico envía al paciente a un especialista (subagente investigador), el especialista genera sus propios resultados de laboratorio, pero al médico de cabecera solo le envía el informe final consolidado. El expediente completo sigue existiendo en el archivo por si hay que auditarlo, pero el escritorio del médico se mantiene ordenado.

## Autoevaluación

1. ¿Dónde y en qué formato guarda Antigravity el historial exacto de las llamadas a herramientas y conversaciones?
2. ¿Por qué es beneficioso que un subagente genere su propio ID de conversación en lugar de ejecutar todo en el mismo hilo del agente principal?
3. En Antigravity, si cierras la aplicación en medio de un proyecto y la abres al día siguiente, ¿por qué el agente no pierde el hilo?

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. En el directorio local de datos de la app, específicamente en el "Brain" (por ejemplo, `~/.gemini/antigravity/brain/<id>/.system_generated/logs/transcript.jsonl`). Se guarda en formato JSON Lines.
> 2. Porque mantiene limpia y eficiente la ventana de contexto del agente principal. El subagente puede ejecutar 20 búsquedas fallidas antes de encontrar la solución, y esos intentos se guardan en su propio log; al principal solo le envía el hallazgo final.
> 3. Porque el sistema recupera su estado desde la memoria estructurada local (el archivo `transcript.jsonl` y los artefactos de la sesión), que contienen el rastro paso a paso y los resultados de cada herramienta usada previamente.

---
[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 02 - Arquitectura Conceptual]] · Siguiente → [[Antigravity Capitulo 04 - Planificacion y Flujo de Trabajo]]
