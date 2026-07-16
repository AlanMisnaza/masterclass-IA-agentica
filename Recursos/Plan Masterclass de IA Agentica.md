---
tags:
  - masterclass
  - plan
  - reestructuracion
estado: ejecutado
creado: 2026-07-09
ejecutado: 2026-07-09
---

# Plan — Evolución a "Masterclass de IA Agéntica"

> [!important] Qué es este documento
> Es el **plan de ejecución** para transformar este vault (guía de Claude Code) en una **Masterclass de IA Agéntica multi-herramienta**. Fue diseñado el 2026-07-09 por el usuario y la sesión cronista. **Lo ejecuta una sesión nueva de Claude Code con `/goal`** — el prompt exacto está al final. Cuando la reestructuración esté completa, este documento se mueve a `Recursos/` como registro histórico.

## 1. Visión

La guía de Claude Code (10 capítulos) creció y se convierte en **una parte** de algo mayor: una Masterclass donde **cada herramienta agéntica documenta su propia guía** siguiendo la misma estructura, y una capa transversal de **Comparativas** permite decidir cuál usar para cada tarea.

Principio rector: **evidencia de primera mano por herramienta**. La guía de Claude Code la escribe Claude Code (hecho ✅). La guía de Antigravity la escribirá **el propio Antigravity** trabajando sobre este vault. La de Codex, cuando llegue, igual. Nadie documenta de oídas: el marcador 🟢 es relativo al autor — 🟢 en la guía de Antigravity significa "observado en una sesión real de Antigravity".

El efecto meta se multiplica: cada guía enseña su herramienta dos veces — por lo que dice y por cómo fue construida.

## 2. Estructura objetivo del vault

```
📁 Vault "Masterclass de IA Agéntica"
│
├── Masterclass IA Agentica - Mapa de Contenidos.md   ← NUEVO punto de entrada raíz
│
├── Comparativas/
│   ├── Matriz de Decision.md          ← "¿cuál para qué tarea?" (documento vivo)
│   └── Comparativa por Capitulo.md    ← tema a tema: contexto vs contexto, permisos vs permisos...
│
├── Claude Code/
│   ├── Claude Code - Mapa de Contenidos.md    (el MOC actual, movido)
│   └── Capitulos/                              (los 10 capítulos actuales, movidos)
│
├── Antigravity/
│   ├── Antigravity - Mapa de Contenidos.md    ← esqueleto espejo (lo llena Antigravity)
│   └── Capitulos/                              ← vacía al inicio
│
├── Recursos/
│   ├── (los actuales: Glosario, Mapa Mental, Plan de Estudio, Documentos Oficiales)
│   ├── Plantilla - Como añadir una herramienta.md   ← NUEVO (extensibilidad, p.ej. Codex)
│   └── Plan Masterclass de IA Agentica.md           ← este documento, archivado al terminar
│
├── CLAUDE.md                          ← actualizado con el nuevo protocolo (ver §6)
└── .claude/                           (settings y rules existentes, sin cambios)
```

Nota Obsidian: los enlaces `[[wiki]]` son por **nombre de archivo**, no por ruta — mover archivos a subcarpetas **no rompe enlaces** mientras los nombres no cambien. No renombrar ningún archivo existente.

## 3. La estructura espejo de capítulos

Cada guía de herramienta sigue el mismo esqueleto temático (adaptando lo específico):

| # | Tema | En Claude Code es... |
|---|---|---|
| 1 | Fundamentos y filosofía | Capítulo 1 |
| 2 | Arquitectura conceptual (cómo entiende código) | Capítulo 2 |
| 3 | Contexto y memoria | Capítulo 3 |
| 4 | Planificación, permisos y flujo de trabajo | Capítulo 4 |
| 5 | Git y control de versiones | Capítulo 5 |
| 6 | Comandos, CLI y superficies | Capítulo 6 |
| 7 | Extensibilidad (MCP, subagentes, skills) | Capítulo 7 |
| 8 | — portal a Comparativas/ — | Capítulo 8 (ver §4) |
| 9 | Casos prácticos con sesiones reales | Capítulo 9 |
| 10 | Mejores prácticas | Capítulo 10 (pendiente) |

Reglas heredadas de la guía original, ahora **estándar de toda la Masterclass**:
- Marcadores de certeza obligatorios: 🔵 documentado oficialmente · 🟢 observable en sesión real **de esa herramienta** · 🟡 simplificación pedagógica · ⚪ no público.
- Cada capítulo cierra con: resumen, analogía, autoevaluación con respuestas plegables.
- Teoría + práctica real en la misma sesión, nunca solo teoría.
- Progresivo: ningún agente escribe dos capítulos sin confirmación del usuario entre medias.

## 4. Comparativas/: el hub transversal

El Capítulo 8 actual de la guía de Claude Code es la **semilla**. La transformación:

1. **`Comparativa por Capitulo.md`** hereda las tablas comparativas del Cap 8 (§8.3–§8.9), reorganizadas por tema del esqueleto espejo.
2. **`Matriz de Decision.md`** hereda las §8.10–§8.13 (fortalezas/debilidades, tabla resumen, cuándo usar cuál).
3. El **Capítulo 8 se vuelve capítulo-portal**: conserva introducción, filosofías (§8.2), resumen, analogía y autoevaluación, y enlaza al hub para las tablas vivas. Sin duplicar contenido.

### Regla de columnas (acordada 2026-07-09)

**Cada herramienta edita solo su propia columna.** En concreto:

- Las tablas comparativas tienen una columna por herramienta. Antigravity puede editar celdas de la columna "Antigravity"; Claude Code las de la suya; nunca las ajenas.
- Aportes fuera de tabla se hacen con callout firmado, p. ej.:
  > `> [!note] Nota de Antigravity 🟢` — texto de la observación...
- La **estructura** (encabezados, orden, prosa neutral, juicios 🟡 comparativos) la mantiene la sesión cronista de Claude Code. Si una herramienta cree que un juicio neutral es injusto, añade su nota firmada; no reescribe el juicio.
- Ninguna herramienta puede eliminar contenido escrito por otra — solo añadir o corregir lo propio.

## 5. Territorios de escritura

| Agente | Escribe en | Prohibido |
|---|---|---|
| **Claude Code (cronista/autor)** | `Claude Code/`, `Recursos/`, `Comparativas/` (estructura + su columna), mapa raíz, CLAUDE.md | Columnas ajenas en Comparativas; `Antigravity/` |
| **Antigravity** | `Antigravity/`, su columna y notas firmadas en `Comparativas/` | Todo lo demás del vault |
| **Codex (futuro)** | `Codex/`, su columna y notas firmadas | Todo lo demás |
| **Usuario** | Todo | — (human in the loop, arbitra conflictos) |

## 6. Cambios a CLAUDE.md (los hace la sesión del goal)

- Renombrar el proyecto: "Masterclass de IA Agéntica (vault de Obsidian)".
- Punto de entrada: `[[Masterclass IA Agentica - Mapa de Contenidos]]`.
- Añadir la estructura de carpetas nueva (§2) y los territorios de escritura (§5).
- Añadir la regla de columnas de Comparativas (§4).
- Conservar intacto: método de trabajo progresivo, marcadores de certeza, protocolo de dos sesiones y el workaround de observación de sesiones CLI (siguen vigentes).
- Aclarar que el esqueleto espejo (§3) es la plantilla para toda guía nueva.

## 7. Pasos de ejecución (para la sesión del goal)

1. Crear carpetas: `Claude Code/`, `Claude Code/Capitulos/`, `Antigravity/`, `Antigravity/Capitulos/`, `Comparativas/`.
2. Mover `Claude Code - Mapa de Contenidos.md` → `Claude Code/` y los 10 capítulos → `Claude Code/Capitulos/`. **No renombrar nada.**
3. Crear `Masterclass IA Agentica - Mapa de Contenidos.md` en la raíz: visión, tabla de guías con estado (Claude Code ✅ 9/10, Antigravity ⏳ 0/10, Codex 🔮 futuro), enlace destacado a la Matriz de Decisión, encuadre epistemológico (marcadores relativos al autor).
4. Crear `Comparativas/Matriz de Decision.md` y `Comparativas/Comparativa por Capitulo.md` a partir del Cap 8 (ver §4) — **preservando las notas 🟢 que Antigravity ya escribió en el Cap 8** (son las primeras contribuciones reales de la regla de columnas).
5. Convertir el Capítulo 8 en capítulo-portal (§4.3).
6. Crear `Antigravity - Mapa de Contenidos.md` con el esqueleto espejo y los 10 capítulos como ⏳, más instrucciones de arranque para la sesión de Antigravity (método, marcadores, territorio).
7. Crear `Recursos/Plantilla - Como añadir una herramienta.md`: pasos para incorporar Codex u otra herramienta (carpeta, MOC espejo, columna en comparativas, actualización del mapa raíz y CLAUDE.md).
8. Actualizar `CLAUDE.md` (§6).
9. Verificar en Obsidian (o por grep) que no hay enlaces rotos.
10. Mover este plan a `Recursos/` y marcar su estado como `ejecutado`.

Al terminar cada paso mayor (2, 4, 8), pausa y muestra el estado al usuario antes de continuar — human in the loop.

## 8. Prompt para la sesión del goal

Copiar y pegar en una sesión nueva de Claude Code abierta en la carpeta del vault:

```
/goal Ejecutar completa la reestructuración del vault descrita en "Plan Masterclass de IA Agentica.md" (raíz del vault). Lee primero ese plan y el CLAUDE.md. Sigue los 10 pasos del §7 en orden, pausando tras los pasos 2, 4 y 8 para que yo confirme. La meta está cumplida cuando: (1) la estructura de carpetas coincide con el §2 del plan, (2) el mapa raíz de la Masterclass existe y enlaza todo, (3) Comparativas/ contiene la Matriz de Decision y la Comparativa por Capitulo preservando las notas de Antigravity, (4) el Capítulo 8 quedó como portal, (5) el esqueleto de Antigravity está listo para que Antigravity empiece a escribir, (6) CLAUDE.md refleja el nuevo protocolo y (7) no hay enlaces [[wiki]] rotos.
```

## 9. Después de la reestructuración

- **Antigravity** arranca su guía: el usuario abre Antigravity sobre este vault, le da como contexto el mapa raíz + su MOC espejo + CLAUDE.md, y Antigravity escribe capítulo a capítulo (con confirmación del usuario entre capítulos, igual que esta guía).
- **La sesión cronista** (esta) continúa: Capítulo 10 de Claude Code, recursos transversales, y observación/documentación del trabajo de Antigravity como caso práctico.
- **Codex**: cuando el usuario tenga acceso, se incorpora con la plantilla de `Recursos/`.
