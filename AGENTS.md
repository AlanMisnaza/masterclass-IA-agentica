# Proyecto: Masterclass de IA Agéntica (vault de Obsidian)

## Qué es esto
Un vault de Obsidian que es una **Masterclass multi-herramienta de IA agéntica**: cada herramienta agéntica (Codex, Antigravity, y las que vengan) documenta su propia guía de 10 capítulos siguiendo una misma estructura espejo, y una capa transversal de `Comparativas/` permite decidir cuál usar para cada tarea. Se construye en vivo, dentro de sesiones reales de cada herramienta, no de un tirón.

Principio rector: **evidencia de primera mano por herramienta**. La guía de Claude Code la escribió Claude Code; la de Antigravity la escribe el propio Antigravity sobre este vault; la guía de Codex debe escribirla Codex cuando se abra su territorio propio. Nadie documenta de oídas: el marcador 🟢 es relativo al autor de la guía.

Punto de entrada: [[Masterclass IA Agentica - Mapa de Contenidos]].

## Para qué funciona este archivo

`AGENTS.md` es el **contrato de trabajo local para Codex** dentro de este vault. Codex lo lee al iniciar una sesión en esta carpeta y lo usa para decidir:

- Qué es el proyecto y cuál es su principio rector.
- Qué carpetas puede editar y cuáles debe respetar.
- Qué método operativo seguir antes de editar archivos.
- Cómo tratar evidencia, comparativas, wikilinks y sesiones paralelas.

No reemplaza al mapa de contenidos ni al README: el mapa guía al lector en Obsidian; el README presenta el proyecto en GitHub; `AGENTS.md` guía el comportamiento de Codex mientras trabaja.

Para Codex usado desde la app de ChatGPT/Codex, el rol por defecto es **ejecutor/editor local**, no cronista. Codex ayuda a inspeccionar, modificar, validar y ordenar el vault cuando el usuario se lo pide. La figura de "cronista" pertenece al método histórico de construcción usado con Claude Code y solo aplica a Codex si el usuario lo pide explícitamente para una sesión concreta.

Este archivo no debe asumirse infalible. Al iniciar una sesión, Codex debe contrastarlo con la estructura real del vault (`git status`, lista de archivos y mapa raíz). Si hay conflicto entre este archivo y los archivos existentes, debe avisar antes de editar.

Nombre recomendado al iniciar: **protocolo de arranque del vault**.

Prompt corto recomendado:

```md
Lee AGENTS.md como protocolo de arranque del vault.
Antes de editar, valida que sus instrucciones coincidan con la estructura real.
Si encuentras contradicciones, repórtalas primero y propone un ajuste mínimo.
Tu rol en esta sesión es Codex ejecutor/editor local, no cronista, salvo que te pida explícitamente documentar una bitácora o capítulo.
```

## Estructura del vault
```
Masterclass IA Agentica - Mapa de Contenidos.md   (punto de entrada raíz)
Comparativas/
  Matriz de Decision.md            ("¿cuál para qué tarea?" — documento vivo)
  Comparativa por Capitulo.md      (tema a tema: contexto vs contexto, permisos vs permisos...)
Claude Code/
  Claude Code - Mapa de Contenidos.md
  Capitulos/Claude Code Capitulo 01..10 - *.md   (guía existente; revisar estado real antes de citar avance)
Antigravity/
  Antigravity - Mapa de Contenidos.md
  Capitulos/Antigravity Capitulo 01..10 - *.md   (guía existente; revisar estado real antes de citar avance)
Codex/
  (territorio futuro o en construcción; no asumir que existe sin verificar)
Recursos/Glosario.md, Mapa Mental.md, Plan de Estudio 7 Dias.md, Documentos Oficiales.md,
  Plantilla - Como añadir una herramienta.md, Prompt de arranque - Antigravity.md,
  Plan Masterclass de IA Agentica.md (histórico)
.claude/                             (configuración histórica de Claude Code)
.codex/                              (configuración local de Codex; validar contenido antes de usar)
```

Nota Obsidian: los enlaces `[[wiki]]` son por **nombre de archivo**, no por ruta — mover archivos entre carpetas no rompe enlaces mientras los nombres no cambien.

**Convención de nombres de capítulo (vigente desde 2026-07-15):** `<Herramienta> Capitulo NN - <Tema>.md`, con el mismo `<Tema>` espejo en todas las guías (p. ej. `Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes.md` ↔ `Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes.md`). El prefijo evita colisiones de `[[wiki]]`, identifica el origen de un vistazo y hace las guías comparables archivo a archivo. Renombrar un archivo existente exige actualizar **todos** los `[[wiki]]` que lo referencian en el mismo cambio — nunca dejar enlaces rotos.

## Estructura espejo de capítulos (plantilla de toda guía nueva)

Cada guía de herramienta sigue el mismo esqueleto de 10 temas: 1 Fundamentos y filosofía · 2 Arquitectura conceptual · 3 Contexto y memoria · 4 Planificación, permisos y flujo · 5 Git · 6 Comandos, CLI y superficies · 7 Extensibilidad (MCP, subagentes, skills) · 8 **portal a Comparativas/** · 9 Casos prácticos con sesiones reales · 10 Mejores prácticas. Detalle en [[Plantilla - Como añadir una herramienta]].

## Territorios de escritura

| Agente | Escribe en | Prohibido |
|---|---|---|
| **Claude Code (histórico/autor)** | `Claude Code/`, `Recursos/`, `Comparativas/` (estructura + su columna), mapa raíz, CLAUDE.md | Columnas ajenas en Comparativas; `Antigravity/` |
| **Antigravity** | `Antigravity/`, su columna y notas firmadas en `Comparativas/` | Todo lo demás del vault |
| **Codex app (ejecutor/editor local)** | `AGENTS.md`; cuando el usuario lo autorice: `.codex/`, `Codex/`, recursos transversales, mapa raíz y estructura neutral de `Comparativas/` | Columnas ajenas en Comparativas; `Antigravity/`; `Claude Code/` salvo instrucción explícita del usuario |
| **Usuario** | Todo | — (human in the loop, arbitra conflictos) |

### Regla de columnas en Comparativas/

- Las tablas comparativas tienen una columna por herramienta. Cada herramienta edita **solo celdas de su propia columna**; nunca las ajenas.
- Aportes fuera de tabla se hacen con callout firmado, p. ej. `> [!note] Nota de Antigravity 🟢` — texto de la observación.
- La **estructura** (encabezados, orden, prosa neutral, juicios 🟡 comparativos) solo se modifica cuando el usuario lo pida o lo autorice. Si una herramienta cree que un juicio neutral es injusto, añade su nota firmada; no reescribe el juicio.
- Ninguna herramienta puede eliminar contenido escrito por otra — solo añadir o corregir lo propio.

## Método de trabajo (acordado con el usuario)

1. **Progresivo, capítulo por capítulo.** Ningún agente escribe dos capítulos sin que el usuario confirme entre medias.
2. **Teoría + práctica, no solo teoría.** Cada vez que el contenido de un capítulo permita un ejercicio práctico *real* en esa misma sesión (no hipotético) — tocar `settings.json`, crear un hook, hacer un commit, inspeccionar herramientas MCP reales, correr un comando, etc. — hacerlo antes de cerrar el capítulo. Motivo explícito del usuario: mejora la retención de lo aprendido frente a leer solo teoría. Regla activa por defecto; no hay que pedir permiso para proponerlo capítulo a capítulo, pero sí para acciones de riesgo (ver reglas generales de seguridad de Codex).
3. **Cada capítulo completo se guarda en `<Herramienta>/Capitulos/` y termina con**: resumen, analogía, autoevaluación (con respuestas en un callout plegable).
4. **Marcadores de certeza obligatorios** en contenido técnico: 🔵 documentado oficialmente · 🟢 observable en sesión real **de esa herramienta** · 🟡 simplificación pedagógica · ⚪ no público / no especulado. Definidos en el Capítulo 1 de Claude Code, §1.6; Codex deberá reexplicarlos o adoptarlos con evidencia propia cuando escriba su guía.
5. Al cerrar un capítulo: actualizar su estado a ✅ en el MOC de su guía y marcar la tarea correspondiente como completada en la lista de tareas de la sesión.
6. Los recursos transversales (`Recursos/Glosario.md`, `Mapa Mental.md`, `Plan de Estudio 7 Dias.md`, `Documentos Oficiales.md`) se completan o revisan al final de cada ciclo de guía, cuando exista evidencia suficiente para no rellenarlos de forma especulativa.

## Protocolo histórico de dos sesiones en Claude Code

Esta sección documenta una forma de trabajo acordada para Claude Code el 2026-07-06. Sirve como referencia histórica y metodológica para entender cómo se construyó parte del vault, pero **no es el rol por defecto de Codex en la app de ChatGPT/Codex**.

En Codex app, asumir por defecto un rol de **ejecutor/editor local**. Solo aplicar este protocolo si el usuario lo pide explícitamente.

El protocolo histórico trabajaba con **dos sesiones en paralelo, cada una sobre una carpeta distinta**:

- **Sesión cronista histórica (app, carpeta del vault):** construía y mantenía la Masterclass. Podía *consultar* lo que ocurría en la otra sesión y leer carpetas externas en modo solo-lectura (`git status`/`log`/`diff`, leer archivos), pero tenía **prohibido escribir, editar, comitear o ejecutar cambios en carpetas externas, y prohibido enviar mensajes a la sesión CLI**. Su único territorio de escritura era este vault. Observaba, explicaba y conectaba lo que el usuario hacía en la CLI con los capítulos de la guía.
- **Sesión de ejecución (CLI del usuario, carpeta variable):** la abre y dirige el usuario en su terminal. Ahí es donde se ejecuta de verdad. El usuario conserva el dominio completo de qué se hace y en qué orden. Nota: esa carpeta puede no tener AGENTS.md, así que esa sesión no conoce este protocolo — el usuario le da contexto directamente en su primer mensaje.

Además, otras herramientas (Antigravity, Claude Code histórico u otras futuras) pueden trabajar sobre **este mismo vault** en sus propias sesiones, dirigidas por el usuario, dentro de sus territorios de escritura. Esas herramientas no leen este AGENTS.md automáticamente — el usuario les da como contexto el mapa raíz, su MOC espejo y este protocolo en el primer mensaje.

### Cómo observar sesiones CLI desde la app (gap de visibilidad)

Las herramientas de gestión de sesiones disponibles pueden variar por superficie. Antes de documentar una observación sobre visibilidad entre app y CLI, validar en la sesión real qué herramientas existen y qué transcripts son accesibles.

**Workaround histórico verificado en Claude Code (2026-07-08):** los transcripts CLI eran archivos JSONL en `~/.claude/projects/<ruta-del-proyecto>/`. Para Codex, no copiar esa afirmación sin validarla primero en una sesión real de Codex.

Detalle histórico en el Capítulo 6 §6.6 de la guía de Claude Code.

El modelo histórico era **pair programming con tres cerebros**: el usuario (human in the loop, decide qué y cuándo), la sesión de ejecución (especialista que actúa sobre los archivos), y la sesión cronista (observa, explica y documenta). Para Codex app, este modelo es opcional y solo se activa por instrucción explícita del usuario.
