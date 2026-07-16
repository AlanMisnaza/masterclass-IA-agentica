---
tags:
  - masterclass
  - antigravity
  - prompt
creado: 2026-07-09
---

# 🚀 Prompt de arranque — sesión de Antigravity

[[Masterclass IA Agentica - Mapa de Contenidos|← Mapa de la Masterclass]] · Relacionado: [[Antigravity - Mapa de Contenidos]] · [[Plantilla - Como añadir una herramienta]]

Prompt diseñado el 2026-07-09 para la primera sesión de Antigravity sobre este vault. Objetivo: dejar su guía **a la par** con la de Claude Code (capítulos 1–8 y 10; el 9 se escribe junto con el usuario), con foco especial en **agentes y subagentes** (`define_subagent` / `invoke_subagent`), que es el diferencial de Antigravity.

Copiar y pegar como primer mensaje en Antigravity, abierto sobre la carpeta del vault:

```
OBJETIVO (modo goal): Escribir la guía de Antigravity de la "Masterclass de IA Agéntica" hasta dejarla A LA PAR con la guía de Claude Code — es decir, capítulos 1 a 8 y 10 completos. El capítulo 9 (Casos Prácticos) NO se escribe ahora: lo haremos juntos el usuario y tú más adelante, porque necesita sesiones reales acumuladas.

CONTEXTO — lee estos 3 archivos ANTES de escribir nada, en este orden:
1. "Masterclass IA Agentica - Mapa de Contenidos.md" (raíz) — visión del vault y encuadre epistemológico.
2. "Antigravity/Antigravity - Mapa de Contenidos.md" — TU mapa de contenidos, con tus instrucciones de arranque en el callout.
3. "CLAUDE.md" (raíz) — el protocolo del vault. Aunque el archivo se llame así, sus territorios de escritura y la regla de columnas te aplican a ti.

TU TERRITORIO (innegociable):
- Escribes SOLO en "Antigravity/" (tu MOC y "Antigravity/Capitulos/").
- En "Comparativas/" solo puedes: editar celdas de TU columna en las tablas, y añadir callouts firmados "> [!note] Nota de Antigravity 🟢". No creas tablas ni encabezados nuevos ahí (la estructura la mantiene la sesión cronista de Claude Code) y JAMÁS editas o borras contenido de otra herramienta.
- No tocas nada más del vault. No renombras ningún archivo existente.

MÉTODO (un capítulo por turno, human in the loop):
- Escribe UN capítulo, actualiza tu MOC (⏳ → ✅ y añade el enlace [[wiki]]), y DETENTE. Espera mi confirmación explícita antes del siguiente. Nunca dos capítulos seguidos sin mi visto bueno.
- Nombres de archivo: "Antigravity/Capitulos/Antigravity Capitulo NN - <Tema>.md". IMPORTANTE: en Obsidian los enlaces [[wiki]] son por nombre de archivo, por eso tus capítulos llevan el prefijo "Antigravity" — no pueden llamarse igual que los de Claude Code.
- Formato de referencia: cualquier nota de "Claude Code/Capitulos/" (frontmatter YAML, navegación anterior/siguiente en cabecera y pie, callouts).
- Cada capítulo cierra con: resumen + analogía + autoevaluación con respuestas en callout plegable ("> [!success]- Ver posibles respuestas").
- Marcadores de certeza OBLIGATORIOS en todo contenido técnico: 🔵 documentado oficialmente por Google (cita la fuente) · 🟢 observable en TU sesión real de Antigravity, ahora mismo · 🟡 simplificación pedagógica · ⚪ no público (se declara, no se especula). El 🟢 es tu ventaja: eres la evidencia de primera mano.
- Teoría + práctica REAL: cada capítulo incluye al menos un ejercicio ejecutado de verdad en esta sesión (inspeccionar tu configuración, correr un comando, crear una skill, lanzar un subagente...), nunca hipotético. Narra qué demuestra el ejercicio, no solo lo ejecutes.

FOCO ESPECIAL — AGENTES Y SUBAGENTES (prioridad transversal de toda la guía):
Tu diferencial frente a Claude Code y Codex es la orquestación multi-agente. Quiero profundidad máxima en:
- "define_subagent" / "invoke_subagent": cómo defines dinámicamente un tipo de subagente (prompt de sistema propio, scope de herramientas restringido) y lo lanzas. En el capítulo 7, DEMUÉSTRALO en vivo: define un subagente real (p. ej. uno de investigación y uno de redacción), lánzalo sobre una subtarea del propio capítulo, y documenta con 🟢 lo observado — qué contexto recibe, qué devuelve, cómo se consolidan sus logs.
- Jerarquías y roles: subagentes estáticos por rol vs. definidos dinámicamente; cuándo conviene cada uno; límites observables (anidamiento, paralelismo real).
- Cómo tu memoria persistente ("~/.gemini/antigravity/brain/", checkpoints, logs consolidados de subagentes) se relaciona con la orquestación — capítulo 3.
- "schedule" (temporizadores/cron en background que te reactivan) como pieza de agencia autónoma — capítulos 4 y 7.
- Este foco atraviesa la guía: el capítulo 1 lo presenta como tu filosofía ("plataforma de agentes, no un agente"), el 2 explica la arquitectura, el 7 es el capítulo estrella con la demo completa.

CAPÍTULO 8 (especial): no es un capítulo completo, es un PORTAL. Crea una nota breve que enlace a [[Matriz de Decision]] y [[Comparativa por Capitulo]], y en ese mismo turno rellena en "Comparativas/" las celdas de tu columna que estén ⚪ o incompletas y añade las notas firmadas que consideres — solo tu columna, con 🟢 solo si lo verificaste en tu sesión.

CAPÍTULO 9: déjalo ⏳ en tu MOC con la nota "se escribirá junto con el usuario, con casos de sesiones reales acumuladas".

LA META ESTÁ CUMPLIDA CUANDO: (1) existen los capítulos 1-8 y 10 en "Antigravity/Capitulos/" con el prefijo correcto, cada uno confirmado por mí antes del siguiente; (2) el 8 es portal y tu columna en Comparativas quedó al día; (3) el 9 quedó ⏳ documentado; (4) tu MOC refleja los estados reales y no hay enlaces [[wiki]] rotos; (5) la profundidad en agentes/subagentes incluye al menos una demo real 🟢 de define_subagent/invoke_subagent; (6) no escribiste nada fuera de tu territorio.

Empieza ahora: lee los 3 archivos de contexto y escribe el Capítulo 1. Luego detente y espera mi confirmación.
```

## Notas de diseño

- **"A la par" = 1–8 + 10**: Claude Code tiene 1–9 (falta su 10); Antigravity tendrá 1–8 + 10 (falta su 9). Cuando la cronista escriba el Cap 10 de Claude Code y el usuario + Antigravity escriban el Cap 9 de Antigravity, ambas guías quedarán 10/10.
- **Prefijo de nombres**: obligatorio para no colisionar con los `[[wiki]]` de los capítulos de Claude Code (Obsidian enlaza por nombre de archivo).
- **Checkpoints dentro de la meta**: Antigravity no tiene el mecanismo `/goal` con hook de parada de Claude Code, así que la confirmación capítulo a capítulo se declara como criterio de cumplimiento explícito.
