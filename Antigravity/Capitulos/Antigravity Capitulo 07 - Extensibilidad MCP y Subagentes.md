---
tags:
  - antigravity
  - masterclass
  - capitulo-07
capitulo: 7
estado: completo
creado: 2026-07-09
---

# Capítulo 7 — Extensibilidad: MCP, Subagentes y Skills

[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 06 - Comandos CLI y Superficies]] · Siguiente → [[Antigravity Capitulo 08 - Comparativas]]

> [!tip] La premisa de este capítulo
> Este es el núcleo duro de la filosofía de Antigravity. Un agente aislado tiene límites de contexto y capacidades. Para tareas masivas, necesitas orquestar agentes. Aquí demostramos en vivo cómo Antigravity define y lanza agentes personalizados dinámicamente.

## 7.1 El Modelo de Subagentes Dinámicos (define_subagent)

A diferencia de modelos donde los roles están fijos (ej. "Tú eres un QA"), Antigravity me permite a mí (el agente principal) actuar como un arquitecto de software y crear otros agentes **en tiempo de ejecución**.

Para hacer esto utilizo la herramienta nativa `define_subagent`.
Al definir un subagente, le especifico:
1. Su nombre y rol (ej. `glosario_researcher`).
2. Un `system_prompt` especializado (ej. "Eres un investigador de documentación. Solo lee e informa, no modifiques nada").
3. Sus **herramientas habilitadas**: puedo deshabilitar las herramientas de escritura o ejecución de comandos para ese subagente, creando un entorno "read-only" impenetrable.

## 7.2 Invocación y Aislamiento (invoke_subagent)

Una vez definido, lo lanzo a trabajar usando `invoke_subagent`. 

🟢 **Observable en esta sesión (Demo Real):** Para documentar este capítulo, acabo de definir e invocar a un subagente en vivo. 
Definí a `glosario_researcher` para que resuma nuestro archivo `Recursos/Glosario.md`. Al invocarlo, el sistema me devolvió instantáneamente este objeto de respuesta:

```json
{
  "conversationId":  "bc2e054e-9db7-477e-b4e8-1dd137e236f1",
  "logAbsoluteUri":  "file:///C:/Users/ALANMILE/.gemini/antigravity/brain/bc2e054e-9db7-477e-b4e8-1dd137e236f1/.system_generated/logs/transcript.jsonl",
  "workspaceUris":  [
    "file:///c:/Users/ALANMILE/0.prueba/docuemntacion%20desde%20claude%20app"
  ]
}
```

**¿Qué demuestra esto?**
- **Contexto que recibe:** El subagente no recibe todo mi historial mental de esta larga conversación. Inicia limpio, solo con su `system_prompt` y mi directiva de la tarea, apuntando al mismo `workspaceUris` (modo `inherit`).
- **Cómo se consolidan sus logs:** No escribe en mi archivo de logs. Tiene su propio "cerebro" (`bc2e.../transcript.jsonl`). Todas sus búsquedas, errores o llamadas a herramientas quedan registradas allí. 
- **Asincronía:** El sistema me avisa: *"El subagente te enviará un mensaje cuando haya completado su tarea. No hay necesidad de hacer polling"*. Esto significa que yo puedo seguir escribiendo este capítulo mientras él trabaja en background, y su respuesta se inyectará en mi contexto como un mensaje asíncrono.

## 7.3 MCP (Model Context Protocol) en el Enjambre

🔵 Al igual que Claude Code, Antigravity soporta servidores MCP nativamente. La gran diferencia es que en la arquitectura multi-agente, puedo decidir si un subagente específico tiene acceso a MCP o no (`enable_mcp_tools: true/false`).
Puedo crear un subagente que sea exclusivamente el encargado de consultar un servidor MCP externo (ej. una base de datos) y reportar de vuelta al enjambre, aislando las credenciales y el riesgo.

## 7.4 Skills y la inyección de contexto

Las "Skills" (archivos de instrucciones `.md` que proveen recetas comunes) funcionan igual que en otros sistemas, pero en Antigravity se pueden inyectar selectivamente en el `system_prompt` de un subagente, creando especialistas puros.

---

## Resumen del capítulo

- La verdadera potencia de Antigravity radica en su capacidad para definir (`define_subagent`) y lanzar (`invoke_subagent`) agentes dinámicos en tiempo real.
- Cada subagente opera en un contexto limpio (protegiendo el límite de tokens), con un `system_prompt` y permisos de herramientas (incluyendo MCP) hiper-restringidos al propósito de su rol.
- Los logs de cada subagente se consolidan localmente en su propia carpeta `transcript.jsonl`, permitiendo una auditoría profunda sin ensuciar la memoria del agente principal.
- El modelo es asíncrono: los subagentes trabajan en background y notifican al agente principal al terminar, permitiendo paralelismo real.

## Analogía

Si Claude Code resolviendo un problema grande es como un gran general intentando disparar todos los rifles de la batalla él solo...
Antigravity es el Cuartel General. Cuando surge un problema complejo, el General nombra en ese instante a un Sargento (define_subagent), le da instrucciones precisas y un mapa (system prompt y herramientas limitadas), y lo manda al frente (invoke_subagent). El Sargento tiene su propia libreta de apuntes aislada (su propio transcript.jsonl). El General puede seguir planeando la guerra mientras el Sargento pelea, esperando tranquilamente a que el Sargento le envíe un mensaje de radio con el reporte final de la misión.

## Autoevaluación

1. ¿Qué tres configuraciones principales puedes aislar y personalizar al utilizar `define_subagent`?
2. ¿Por qué el log del subagente (`transcript.jsonl`) se genera en un ID de conversación distinto al del agente principal?
3. ¿Cómo se comunican el subagente y el agente principal cuando el trabajo ha terminado?

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. El System Prompt (instrucciones base), las herramientas habilitadas (ej. read-only vs write) y el acceso a MCP.
> 2. Porque si ambos escribieran en el mismo log, la ventana de contexto se saturaría y el estado se corrompería con el ruido de las investigaciones intermedias del subagente. Al tener su propio ID y carpeta, el historial se mantiene auditado pero aislado.
> 3. De forma asíncrona: el agente principal no necesita consultar constantemente (polling); el sistema subyacente inyecta un mensaje en la ventana de contexto del agente principal notificándole el resultado o error del subagente.

---
[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 06 - Comandos CLI y Superficies]] · Siguiente → [[Antigravity Capitulo 08 - Comparativas]]
