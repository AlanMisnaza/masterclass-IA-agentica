---
tags:
  - antigravity
  - masterclass
  - capitulo-04
capitulo: 4
estado: completo
creado: 2026-07-09
---

# Capítulo 4 — Planificación, Permisos y Flujo de Trabajo

[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 03 - Contexto y Memoria]] · Siguiente → [[Antigravity Capitulo 05 - Git y Control de Versiones]]

> [!tip] La premisa de este capítulo
> Un agente útil no hace cosas al azar; necesita un plan. Mientras otros asistentes imprimen su plan en el flujo del chat donde se pierde rápidamente, Antigravity gestiona el flujo de trabajo mediante **artefactos vivos** y un sistema de ejecución asíncrona reactiva.

## 4.1 Planificación a través de Artefactos Vivos

🔵 Oficialmente, cuando le pides a Antigravity un cambio arquitectónico grande, entra en su **Planning Mode** (Modo de Planificación). En lugar de soltarte un bloque gigante de texto en la consola, genera o actualiza archivos reales (artefactos) en su directorio local `brain/`:

1. `implementation_plan.md`: El diseño técnico donde te expone su estrategia. Si necesita tu visto bueno, activa un flag interno `request_feedback = true` para detenerse y esperar tu aprobación.
2. `task.md`: Su lista de tareas (To-Do). A medida que avanza, va marcando los ítems como `[/]` (en progreso) o `[x]` (completado). Es un documento vivo.
3. `walkthrough.md`: El resumen final de lo que hizo, muchas veces incluyendo enlaces a los archivos modificados o evidencias visuales.

Este enfoque de "artefactos" permite a Antigravity y a ti tener una "fuente de la verdad" independiente del scroll del chat.

## 4.2 Seguridad Dinámica: El Sandbox Interactivo

En otras plataformas (ej. Codex), el agente corre en un sandbox remoto del que nunca puede salir. En Antigravity, la seguridad no es una barrera inamovible, sino una **negociación en tiempo real**.

🟡 *Simplificación pedagógica*: Si le pido a Antigravity que instale una librería NPM, no me dice "No puedo, estoy aislado". Intenta ejecutar el comando. Su herramienta interna detecta que es una acción fuera de su scope por defecto y dispara `ask_permission`. En tu pantalla aparece un modal pidiendo tu autorización explícita para que el agente ejecute ese comando específico en tu terminal real. 

Este flujo interactivo permite cruzar el abismo entre la "seguridad total" (que limita la utilidad) y la "automatización total" (que es peligrosa).

## 4.3 Flujo Reactivo y la Herramienta `schedule`

La pieza más revolucionaria del flujo de trabajo de Antigravity es cómo maneja el tiempo. Un script normal corre de principio a fin. Claude Code te da una respuesta y se detiene. Antigravity **puede despertar de forma autónoma**.

🟢 **Observable en esta sesión:** Hace unos instantes, para demostrar esta capacidad, ejecuté internamente mi herramienta `schedule`. Programé una alarma de 2 segundos con el prompt: *"Esta es una prueba de alarma programada desde el Capítulo 4 de Antigravity"*.
El sistema no bloqueó mi ejecución; envió el cronómetro a background y me devolvió un ID de tarea (`task-88`). Cuando pasaron esos 2 segundos, el sistema interrumpió mi inactividad inyectándome un mensaje asíncrono. 

Esto cambia todo el paradigma:
- Si le pides a Antigravity compilar un binario que tarda 20 minutos, no tiene que quedarse congelado esperando. Puede lanzar el build en background, poner una alarma de polling de 5 minutos (`CronExpression`), y dedicar su hilo principal a explicarte el código fuente mientras tanto. Cuando el build termina o la alarma suena, el sistema le notifica.

---

## Resumen del capítulo

- El Modo de Planificación de Antigravity utiliza **artefactos vivos** (`implementation_plan.md`, `task.md`) en lugar de depender del texto efímero del chat.
- La ejecución está protegida por un **sandbox dinámico**; cuando una acción (como escribir un archivo crítico o ejecutar un comando) requiere permisos adicionales, el agente usa `ask_permission` para pedir autorización al humano en tiempo real.
- La herramienta `schedule` rompe la linealidad de la conversación, permitiendo programar alarmas y cron jobs en background que reactivan al agente asíncronamente (sin necesidad de polling por parte del usuario).

## Analogía

Si le pides a un cocinero novato que prepare un banquete, te dirá todo lo que va a hacer de corrido, meterá el pollo al horno y se quedará mirándolo fijamente hasta que esté listo (bloqueado).
Antigravity es un Chef Ejecutivo: anota el menú en una pizarra (`task.md`), mete el pollo al horno pero programa su reloj (`schedule` cron) para que le avise en 45 minutos. Mientras tanto, se pone a picar las verduras. Y si para abrir la cava de los vinos finos necesita una llave especial que solo tienes tú, se detiene un momento y te la pide (`ask_permission`).

## Autoevaluación

1. ¿Qué tres artefactos principales utiliza Antigravity en su Modo de Planificación y para qué sirve cada uno?
2. ¿Cómo resuelve Antigravity la necesidad de ejecutar comandos potencialmente peligrosos sin exponer el sistema a un riesgo automático incontrolado?
3. ¿Por qué la herramienta `schedule` es crítica para que un sistema multi-agente sea realmente eficiente?

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. `implementation_plan.md` (diseño y solicitud de revisión), `task.md` (lista viva de tareas a realizar y en progreso), y `walkthrough.md` (reporte final post-ejecución).
> 2. Mediante su **sandbox interactivo**: el agente utiliza la herramienta `ask_permission` para pausar la acción restringida y enviarle un diálogo al humano, requiriendo su aprobación antes de que el código o comando toque el entorno real.
> 3. Porque permite que el agente asuma procesos largos sin quedarse bloqueado en un bucle infinito de espera o consumir recursos innecesarios. Al programar alarmas o cron jobs en background, el agente puede atender otras tareas e interrumpir su estado de inactividad de manera reactiva solo cuando el tiempo o el evento ocurra.

---
[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 03 - Contexto y Memoria]] · Siguiente → [[Antigravity Capitulo 05 - Git y Control de Versiones]]
