---
tags:
  - antigravity
  - masterclass
  - capitulo-10
capitulo: 10
estado: completo
creado: 2026-07-09
---

# Capítulo 10 — Mejores Prácticas

[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 09 - Casos Practicos]] 

> [!tip] La premisa de este capítulo
> Una plataforma multi-agente mal orquestada genera más caos que un humano trabajando solo. Este capítulo recoge las mejores prácticas observadas empíricamente para aprovechar el enjambre sin perder el control del proyecto.

## 10.1 Orquesta, no abrumes (El principio de responsabilidad única)

El error más común al usar Antigravity es tratar de usar al agente principal como si fuera un modelo tradicional de un solo hilo, pasándole un prompt gigante: *"Analiza el bug de frontend, arregla el backend de Go, actualiza el esquema SQL y despliega"*.

**Mejor práctica:**
- Usa al agente principal como el **Director**. 
- Si la tarea es amplia, instrúyele explícitamente que defina subagentes separados para frontend, backend y QA (`define_subagent`).
- Lanza a los agentes especializados y consolida sus resultados. Esto protege tu ventana de contexto y previene las alucinaciones por "sobrecarga de memoria".

## 10.2 Aislamiento por defecto (Modo Branch)

Cuando tienes varios subagentes trabajando en paralelo, las condiciones de carrera (dos agentes intentando escribir en `config.json` a la vez) arruinarán tu código.

**Mejor práctica:**
- Evita el modo `Workspace: inherit` para tareas de código paralelo.
- Invoca a tus subagentes utilizando `Workspace: branch` o `share`. Esto obligará a cada agente a trabajar en una rama aislada, permitiéndote revisar un diff limpio antes de fusionarlo.

## 10.3 Aprovecha el flujo asíncrono y las alarmas

Antigravity no tiene por qué quedarse congelado esperando procesos largos (builds lentos, deploys, web scraping pesado). 

**Mejor práctica:**
- Utiliza intensivamente la herramienta `schedule`. 
- Si envías a un subagente a hacer una investigación masiva, no te quedes mirando la pantalla. Sigue planeando la arquitectura.
- Si lanzas un comando pesado con `run_command`, configura el temporizador de inactividad interno para que no bloquee al motor asíncrono.

## 10.4 Confía en el Sandbox Interactivo

Es tentador querer "apagar" las molestas alertas de seguridad para que el agente trabaje más rápido.

**Mejor práctica:**
- No modifiques `settings.json` para darle acceso ilimitado de root al enjambre. 
- Permite que Antigravity use `ask_permission` ante operaciones dudosas. Este modal interactivo es tu cinturón de seguridad: garantiza que el agente pueda avanzar de forma autónoma hasta el 99% de la tarea, pero que la acción crítica (como un borrado o despliegue) espere por tu clic humano.

---

## Resumen del capítulo

- Trata a Antigravity como un equipo, no como un solo robot. Delega tareas complejas definiendo subagentes especializados (`define_subagent`).
- Protege tu código de colisiones multi-agente lanzando a los agentes en ramas separadas de Git.
- Rompe el flujo lineal: usa temporizadores asíncronos (`schedule`) para tareas que requieran tiempo, manteniendo al agente principal libre.
- Mantén el sandbox interactivo encendido: las solicitudes de `ask_permission` son una red de seguridad vital para un enjambre autónomo.

## Analogía

Usar Antigravity intentando que haga todo en un solo bloque es como contratar a 5 músicos brillantes, atarles las manos, y obligar al director de orquesta a tocar todos los instrumentos a la vez. 
Aplicar estas mejores prácticas es darle la partitura al director, entregarle los instrumentos correctos a cada músico aislado en su sección, y dejar que el director marque el tempo.

## Autoevaluación

1. ¿Por qué es un anti-patrón enviar un prompt monolítico gigante al agente principal en lugar de delegarlo en subagentes?
2. Si tienes dos subagentes que deben modificar archivos del mismo proyecto, ¿cuál es la mejor configuración de `Workspace` y por qué?
3. ¿Cómo encaja el comando `schedule` en las mejores prácticas de eficiencia temporal?

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. Porque degradas la ventana de contexto del agente principal y mezclas intenciones (frontend, backend, base de datos). Delegar en subagentes especializados mantiene el contexto limpio y los logs (`transcript.jsonl`) debidamente auditados.
> 2. `Workspace: branch` o `share`. Aislar a los agentes en ramas o worktrees de Git previene las condiciones de carrera y los conflictos de edición concurrente.
> 3. Al no depender de que el usuario haga polling o se quede bloqueado frente a la pantalla, `schedule` permite programar revisiones y alarmas en background, liberando al hilo principal para continuar procesando lógica o interactuando con el usuario mientras las tareas largas terminan.

---
[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 09 - Casos Practicos]] 

*(Nota de Antigravity 🟢: La aplicación estricta de estas mejores prácticas —específicamente delegación, ejecución autónoma en bucle cerrado y actualización progresiva de artefactos— es exactamente lo que permitió que toda esta guía se redactara de forma fluida bajo la directiva asíncrona del comando `/goal` en esta misma sesión).*
