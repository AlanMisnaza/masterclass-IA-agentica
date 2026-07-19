---
tags:
  - masterclass
  - moc
creado: 2026-07-09
---

# 🎓 Masterclass de IA Agéntica — Mapa de Contenidos

Este vault es una **Masterclass multi-herramienta sobre IA agéntica**: cada herramienta de código agéntico (Claude Code, Antigravity, y las que vengan) documenta su propia guía siguiendo una misma estructura espejo de 10 capítulos, y una capa transversal de **Comparativas** permite decidir cuál usar para cada tarea.

> [!important] Principio rector: evidencia de primera mano
> Nadie documenta de oídas. La guía de Claude Code la escribe **Claude Code**; la de Antigravity la escribe **el propio Antigravity** trabajando sobre este vault; la de Codex, cuando llegue, igual. Cada guía enseña su herramienta dos veces: por lo que dice y por cómo fue construida.

## 📚 Las guías

| Guía            | Autor                   | Estado           | Punto de entrada                                    |
| --------------- | ----------------------- | ---------------- | --------------------------------------------------- |
| **Claude Code** | Claude Code (Anthropic) | ✅ 9/10 capítulos (falta el 10) | [[Claude Code - Mapa de Contenidos]]                |
| **Antigravity** | Antigravity (Google)    | ✅ 9/10 capítulos (falta el 9, se escribe con el usuario) | [[Antigravity - Mapa de Contenidos]]                |
| **Codex**       | Codex (OpenAI)          | 🧭 Exploración guiada por evidencia · 0/10 | [[Codex - Mapa de Contenidos]] |

## ⚖️ Comparativas — el hub transversal

> [!tip] ¿Cuál herramienta uso para esta tarea?
> Empieza por la **[[Matriz de Decision]]** — fortalezas, debilidades y guía de decisión "cuándo usar cuál". Es un documento vivo que crece con cada guía.

| Documento | Qué responde |
|---|---|
| [[Matriz de Decision]] | ¿Cuál herramienta para qué tarea? Fortalezas, debilidades, tabla resumen |
| [[Comparativa por Capitulo]] | Tema a tema: contexto vs. contexto, permisos vs. permisos, skills vs. skills... |

**Regla de columnas**: cada herramienta edita solo su propia columna en las tablas comparativas, y aporta fuera de tabla con callouts firmados (`> [!note] Nota de Antigravity 🟢`). La estructura y los juicios 🟡 neutrales los mantiene la sesión cronista de Claude Code. Ninguna herramienta borra contenido de otra.

## 🧩 La estructura espejo de capítulos

Toda guía de herramienta sigue este esqueleto temático (adaptando lo específico):

| # | Tema |
|---|---|
| 1 | Fundamentos y filosofía |
| 2 | Arquitectura conceptual (cómo entiende código) |
| 3 | Contexto y memoria |
| 4 | Planificación, permisos y flujo de trabajo |
| 5 | Git y control de versiones |
| 6 | Comandos, CLI y superficies |
| 7 | Extensibilidad (MCP, subagentes, skills) |
| 8 | — capítulo-portal hacia `Comparativas/` — |
| 9 | Casos prácticos con sesiones reales |
| 10 | Mejores prácticas |

**Convención de nombres de capítulo** (desde 2026-07-15): todo capítulo se nombra `<Herramienta> Capitulo NN - <Tema>.md` — p. ej. `Claude Code Capitulo 07 - Extensibilidad MCP y Subagentes.md` y `Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes.md`. El prefijo evita colisiones de `[[wiki]]` (Obsidian enlaza por nombre de archivo), identifica el origen de un vistazo y mantiene los nombres de tema espejo entre guías.

Reglas de toda la Masterclass (heredadas de la guía original de Claude Code):
- **Marcadores de certeza obligatorios** en contenido técnico (ver encuadre abajo).
- Cada capítulo cierra con: **resumen, analogía y autoevaluación** con respuestas plegables.
- **Teoría + práctica real** en la misma sesión, nunca solo teoría.
- **Progresivo**: ningún agente escribe dos capítulos sin confirmación del usuario entre medias.

## 📎 Recursos transversales

| Recurso | Descripción |
|---|---|
| [[Glosario]] | Términos técnicos, definidos una sola vez |
| [[Mapa Mental]] | Vista de conjunto en un solo diagrama |
| [[Plan de Estudio 7 Dias]] | Plan práctico día a día |
| [[Documentos Oficiales]] | Qué leer después en la documentación oficial |
| [[Plantilla - Como añadir una herramienta]] | Cómo incorporar una herramienta nueva (p. ej. Codex) |
| [[Plan Masterclass de IA Agentica]] | Registro histórico de la reestructuración de 2026-07-09 |

## Encuadre epistemológico — marcadores relativos al autor

Todo el contenido técnico del vault se etiqueta con estos marcadores (definidos en el [[Claude Code Capitulo 01 - Fundamentos y Filosofia#1.6 Información pública vs. simplificación pedagógica|§1.6 del Capítulo 1]] de la guía de Claude Code):

- 🔵 **Documentado oficialmente** por el fabricante (se cita la fuente cuando es posible).
- 🟢 **Observable directamente en una sesión real** — y aquí está la clave multi-herramienta: **el marcador es relativo al autor de la guía**. Un 🟢 en la guía de Antigravity significa "observado en una sesión real de Antigravity"; en la de Claude Code, "observado en una sesión real de Claude Code".
- 🟡 **Simplificación pedagógica** — modelo mental útil, no descripción literal del mecanismo interno.
- ⚪ **No público / desconocido** — se indica explícitamente en vez de especular.

Esta distinción es la columna vertebral de la Masterclass: entender una herramienta bien significa también saber qué partes de su funcionamiento son verificables, quién las verificó y cuáles son simplificaciones útiles.
