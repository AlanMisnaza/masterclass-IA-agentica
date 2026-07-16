---
tags:
  - antigravity
  - masterclass
  - capitulo-05
capitulo: 5
estado: completo
creado: 2026-07-09
---

# Capítulo 5 — Git y Control de Versiones

[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 04 - Planificacion y Flujo de Trabajo]] · Siguiente → [[Antigravity Capitulo 06 - Comandos CLI y Superficies]]

> [!tip] La premisa de este capítulo
> Un agente que edita código localmente debe respetar el control de versiones. Antigravity lleva la integración con Git al siguiente nivel: no solo usa ramas para aislar cambios del usuario, sino que utiliza clonación y worktrees de Git nativamente para orquestar subagentes sin que se pisen el código entre ellos.

## 5.1 Git como herramienta de aislamiento del humano

Al igual que Claude Code, Antigravity puede ejecutar comandos de Git mediante `run_command`. Sin embargo, dado su sandbox dinámico (visto en el Capítulo 4), las operaciones destructivas de Git o que alteran el entorno principal suelen requerir tu permiso explícito (a menos que se auto-aprueben en `settings.json`).

El flujo tradicional es:
1. El agente explora (usa `list_dir`, `view_file`).
2. Diseña un plan y crea un artefacto.
3. Sugiere crear una nueva rama de Git para encapsular el experimento antes de ejecutar `replace_file_content`.

## 5.2 Git como infraestructura de los Subagentes

El rasgo más avanzado de Antigravity no es cómo interactúa con Git para ti, sino cómo lo usa para **sí mismo**.

🟢 **Observable en esta sesión:** Al inspeccionar mi propia herramienta interna `invoke_subagent` (que utilizo para delegar trabajo a otros agentes), existe un parámetro crítico llamado `Workspace`. 

Este parámetro define cómo el subagente interactuará con los archivos del proyecto respecto al agente principal:
- **`inherit`** (heredar): El subagente trabaja en la misma carpeta que yo. Si él rompe algo, yo lo veo roto. Es rápido, pero peligroso si ambos intentamos editar el mismo módulo a la vez.
- **`branch`** (ramificar): Antigravity crea un workspace clonado y aislado a partir de mi estado actual. El subagente puede hacer commits, romper todo y probar libremente sin afectar mi carpeta de trabajo real.
- **`share`** (compartir): Utiliza la funcionalidad subyacente similar a un **Git worktree** (o Mercurial share), permitiendo que el subagente opere en una rama independiente del código, pero sin duplicar el almacenamiento físico del repositorio en el disco duro.

## 5.3 Resolución de conflictos en el Enjambre

Esta arquitectura (particularmente `branch` y `share`) resuelve el mayor cuello de botella del desarrollo multi-agente: las condiciones de carrera (race conditions). 

🟡 *Simplificación pedagógica*: Si ordenas a un Agente Frontend rediseñar el botón de login, y a un Agente Backend cambiar la API de autenticación, ambos necesitan editar `auth.js` al mismo tiempo. 
Con Claude Code (lineal), haces uno y luego el otro.
Con Antigravity, lanzo a ambos subagentes con `Workspace: branch`. Cada uno hace sus commits en su entorno aislado. Cuando ambos terminan, yo (el agente principal) actúo como un *Tech Lead*, reviso los diffs, y los consolido en la rama principal.

---

## Resumen del capítulo

- Antigravity respeta los flujos de trabajo convencionales de Git (ramas, commits), solicitando permisos mediante el sandbox dinámico para acciones críticas.
- El control de versiones es la columna vertebral de la orquestación multi-agente: la herramienta `invoke_subagent` usa Git (clonaciones o worktrees) para aislar entornos a través de los modos de Workspace (`inherit`, `branch`, `share`).
- Esto permite paralelismo masivo sin condiciones de carrera, posibilitando que múltiples agentes editen la misma base de código al mismo tiempo sin destruirse el trabajo mutuamente.

## Analogía

Trabajar en modo `inherit` es como tener a tres cocineros cortando cebollas en la misma tabla de picar: rápido, pero es probable que alguien se corte un dedo o mezclen ingredientes equivocados. 
Trabajar en modo `branch` (o `share`) usando Git es como darle a cada cocinero su propia tabla de picar y sus propios boles. Cada uno prepara su parte de la receta de forma aislada. Cuando todos terminan, te entregan los boles preparados y tú, como Chef, mezclas todo en la olla principal sin conflictos.

## Autoevaluación

1. ¿Cuáles son los tres modos de Workspace disponibles al lanzar un subagente en Antigravity y qué diferencia fundamental existe entre ellos?
2. ¿Por qué es necesario aislar el entorno de trabajo (usando Git worktrees o branches) cuando tienes múltiples agentes operando a la vez?

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. `inherit` (comparte exactamente la misma carpeta en vivo con el agente principal), `branch` (crea un clon aislado del repositorio para experimentar libremente) y `share` (crea un entorno aislado tipo Git worktree que permite usar otra rama independiente sin duplicar físicamente el peso del repo).
> 2. Para evitar condiciones de carrera (race conditions) y conflictos directos. Si varios agentes intentan editar el mismo archivo simultáneamente en la misma carpeta, los cambios se sobreescribirían o corromperían. El aislamiento vía Git les permite iterar, comitear y fallar sin afectar la rama principal ni a sus compañeros.

---
[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 04 - Planificacion y Flujo de Trabajo]] · Siguiente → [[Antigravity Capitulo 06 - Comandos CLI y Superficies]]
