# Proyecto: Masterclass de IA Agéntica (vault de Obsidian)

## Qué es esto
Un vault de Obsidian que es una **Masterclass multi-herramienta de IA agéntica**: cada herramienta agéntica (Claude Code, Antigravity, y las que vengan) documenta su propia guía de 10 capítulos siguiendo una misma estructura espejo, y una capa transversal de `Comparativas/` permite decidir cuál usar para cada tarea. Se construye en vivo, dentro de sesiones reales de cada herramienta, no de un tirón.

Principio rector: **evidencia de primera mano por herramienta**. La guía de Claude Code la escribe Claude Code; la de Antigravity la escribe el propio Antigravity sobre este vault. Nadie documenta de oídas: el marcador 🟢 es relativo al autor de la guía.

Punto de entrada: [[Masterclass IA Agentica - Mapa de Contenidos]].

## Estructura del vault
```
Masterclass IA Agentica - Mapa de Contenidos.md   (punto de entrada raíz)
Comparativas/
  Matriz de Decision.md            ("¿cuál para qué tarea?" — documento vivo)
  Comparativa por Capitulo.md      (tema a tema: contexto vs contexto, permisos vs permisos...)
Claude Code/
  Claude Code - Mapa de Contenidos.md
  Capitulos/Claude Code Capitulo 01..10 - *.md   (guía completa 9/10; falta el 10)
Antigravity/
  Antigravity - Mapa de Contenidos.md
  Capitulos/Antigravity Capitulo 01..10 - *.md   (guía completa 9/10; el 9 se escribe con el usuario)
Recursos/Glosario.md, Mapa Mental.md, Plan de Estudio 7 Dias.md, Documentos Oficiales.md,
  Plantilla - Como añadir una herramienta.md, Prompt de arranque - Antigravity.md,
  Plan Masterclass de IA Agentica.md (histórico)
.claude/settings.json                 (WebSearch/WebFetch auto-aprobados en este proyecto)
```

Nota Obsidian: los enlaces `[[wiki]]` son por **nombre de archivo**, no por ruta — mover archivos entre carpetas no rompe enlaces mientras los nombres no cambien.

**Convención de nombres de capítulo (vigente desde 2026-07-15):** `<Herramienta> Capitulo NN - <Tema>.md`, con el mismo `<Tema>` espejo en todas las guías (p. ej. `Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes.md` ↔ `Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes.md`). El prefijo evita colisiones de `[[wiki]]`, identifica el origen de un vistazo y hace las guías comparables archivo a archivo. Renombrar un archivo existente exige actualizar **todos** los `[[wiki]]` que lo referencian en el mismo cambio — nunca dejar enlaces rotos.

## Estructura espejo de capítulos (plantilla de toda guía nueva)

Cada guía de herramienta sigue el mismo esqueleto de 10 temas: 1 Fundamentos y filosofía · 2 Arquitectura conceptual · 3 Contexto y memoria · 4 Planificación, permisos y flujo · 5 Git · 6 Comandos, CLI y superficies · 7 Extensibilidad (MCP, subagentes, skills) · 8 **portal a Comparativas/** · 9 Casos prácticos con sesiones reales · 10 Mejores prácticas. Detalle en [[Plantilla - Como añadir una herramienta]].

## Territorios de escritura

| Agente | Escribe en | Prohibido |
|---|---|---|
| **Claude Code (cronista/autor)** | `Claude Code/`, `Recursos/`, `Comparativas/` (estructura + su columna), mapa raíz, CLAUDE.md | Columnas ajenas en Comparativas; `Antigravity/` |
| **Antigravity** | `Antigravity/`, su columna y notas firmadas en `Comparativas/` | Todo lo demás del vault |
| **Codex (futuro)** | `Codex/`, su columna y notas firmadas | Todo lo demás |
| **Usuario** | Todo | — (human in the loop, arbitra conflictos) |

### Regla de columnas en Comparativas/

- Las tablas comparativas tienen una columna por herramienta. Cada herramienta edita **solo celdas de su propia columna**; nunca las ajenas.
- Aportes fuera de tabla se hacen con callout firmado, p. ej. `> [!note] Nota de Antigravity 🟢` — texto de la observación.
- La **estructura** (encabezados, orden, prosa neutral, juicios 🟡 comparativos) la mantiene la sesión cronista de Claude Code. Si una herramienta cree que un juicio neutral es injusto, añade su nota firmada; no reescribe el juicio.
- Ninguna herramienta puede eliminar contenido escrito por otra — solo añadir o corregir lo propio.

## Método de trabajo (acordado con el usuario)

1. **Progresivo, capítulo por capítulo.** Ningún agente escribe dos capítulos sin que el usuario confirme entre medias.
2. **Teoría + práctica, no solo teoría.** Cada vez que el contenido de un capítulo permita un ejercicio práctico *real* en esa misma sesión (no hipotético) — tocar `settings.json`, crear un hook, hacer un commit, inspeccionar herramientas MCP reales, correr un comando, etc. — hacerlo antes de cerrar el capítulo. Motivo explícito del usuario: mejora la retención de lo aprendido frente a leer solo teoría. Regla activa por defecto; no hay que pedir permiso para proponerlo capítulo a capítulo, pero sí para acciones de riesgo (ver reglas generales de seguridad de Claude Code).
3. **Cada capítulo completo se guarda en `<Herramienta>/Capitulos/` y termina con**: resumen, analogía, autoevaluación (con respuestas en un callout plegable).
4. **Marcadores de certeza obligatorios** en contenido técnico: 🔵 documentado oficialmente · 🟢 observable en sesión real **de esa herramienta** · 🟡 simplificación pedagógica · ⚪ no público / no especulado. Definidos en el Capítulo 1 de Claude Code, §1.6.
5. Al cerrar un capítulo: actualizar su estado a ✅ en el MOC de su guía y marcar la tarea correspondiente como completada en la lista de tareas de la sesión.
6. Los recursos transversales (`Recursos/Glosario.md`, `Mapa Mental.md`, `Plan de Estudio 7 Dias.md`, `Documentos Oficiales.md`) se completan al final, cuando ya existan los 10 capítulos de la guía de Claude Code.

## Convención de commits

Los mensajes de commit se redactan con la **skill `git-commit-es`** (Conventional Commits en español) — no improvises el formato. Al commitear:
- **Invoca la skill `git-commit-es`** para redactar el mensaje.
- **Sin footer `Co-Authored-By`** (salvo que el usuario lo pida).
- Commits **atómicos** y respetando los territorios de escritura: cada sesión commitea solo los archivos de su territorio; nunca arrastres cambios ajenos (de otra herramienta o sesión) al mismo commit.
- Tipos habituales: `docs` (capítulos y recursos), `refactor`/`chore` (estructura y config), `feat`/`fix` cuando aplique.

> Nota: no existe un agente/subagente dedicado a commits en este vault, y es deliberado. La skill basta y produce mejores mensajes que un subagente aislado, porque el POR QUÉ de un commit vive en la conversación de la sesión (no en el diff), y un subagente arranca sin ese contexto. Un subagente solo se justifica cuando el trabajo es grande, contamina el contexto, o necesita una compuerta de validación propia (no es el caso aquí).

## Protocolo de dos sesiones (acordado 2026-07-06)

El usuario trabaja con **dos sesiones en paralelo, cada una sobre una carpeta distinta**:

- **Sesión cronista (app, ESTA carpeta — el vault):** construye y mantiene la Masterclass. Puede *consultar* lo que ocurre en la otra sesión y leer carpetas externas en modo solo-lectura (`git status`/`log`/`diff`, leer archivos), pero tiene **prohibido escribir, editar, comitear o ejecutar cambios en carpetas externas, y prohibido enviar mensajes a la sesión CLI**. Su único territorio de escritura es este vault (dentro de los territorios de la tabla de arriba). Observa, explica y conecta lo que el usuario hace en la CLI con los capítulos de la guía.
- **Sesión de ejecución (CLI del usuario, carpeta variable):** la abre y dirige el usuario en su terminal. Ahí es donde se ejecuta de verdad. El usuario conserva el dominio completo de qué se hace y en qué orden. Nota: esa carpeta puede no tener CLAUDE.md, así que ese Claude no conoce este protocolo — el usuario le da contexto directamente en su primer mensaje.

Además, otras herramientas (Antigravity, futuro Codex) trabajan sobre **este mismo vault** en sus propias sesiones, dirigidas por el usuario, dentro de sus territorios de escritura. Esas herramientas no leen este CLAUDE.md automáticamente — el usuario les da como contexto el mapa raíz, su MOC espejo y este protocolo en el primer mensaje.

### Cómo observar sesiones CLI desde la app (gap de visibilidad)

Las herramientas `list_sessions` y `search_session_transcripts` son del servidor MCP `ccd_session_mgmt` (CCD = Claude Code Desktop) y **solo ven sesiones de la app** (IDs con prefijo `local_`). Las sesiones CLI usan IDs sin prefijo → invisibles para esas herramientas.

**Workaround verificado (2026-07-08):** los transcripts CLI son archivos JSONL en `~/.claude/projects/<ruta-del-proyecto>/`. Para observar una sesión CLI:
1. Listar `~/.claude/projects/` y buscar la carpeta con el nombre del proyecto CLI.
2. Leer el archivo `.jsonl` más reciente dentro de esa carpeta.
3. Cada línea es un objeto JSON con campo `type` (`user`, `assistant`, `attachment`, etc.).

Detalle completo en el Capítulo 6 §6.6 de la guía de Claude Code.

El modelo de trabajo es **pair programming con tres cerebros**: el usuario (human in the loop, decide qué y cuándo), la sesión de ejecución (especialista que actúa sobre los archivos), y la sesión cronista (observa, explica y documenta). Si el cronista no puede ver al ejecutor, el modelo se rompe — por eso el workaround de lectura directa de JSONL es infraestructura crítica del protocolo.
