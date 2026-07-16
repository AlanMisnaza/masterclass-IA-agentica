---
tags:
  - masterclass
  - plantilla
  - extensibilidad
creado: 2026-07-09
---

# 🧰 Plantilla — Cómo añadir una herramienta a la Masterclass

[[Masterclass IA Agentica - Mapa de Contenidos|← Mapa de la Masterclass]]

Pasos para incorporar una herramienta agéntica nueva (p. ej. **Codex**) al vault. El principio rector no se negocia: **la guía de cada herramienta la escribe la propia herramienta**, en sesiones reales sobre este vault — el marcador 🟢 es relativo al autor.

## Pasos

1. **Crear el territorio**: carpetas `<Herramienta>/` y `<Herramienta>/Capitulos/` en la raíz del vault.
2. **Crear el MOC espejo**: `<Herramienta>/<Herramienta> - Mapa de Contenidos.md`, copiando la estructura de [[Antigravity - Mapa de Contenidos]]:
   - Tabla de los 10 capítulos espejo, todos ⏳ al inicio (sin enlaces `[[wiki]]` hasta que la nota exista).
   - Callout de **instrucciones de arranque** adaptado: territorio de escritura, método progresivo, teoría + práctica real, marcadores de certeza, cierre de capítulo (resumen + analogía + autoevaluación plegable), y capítulo 8 como portal a `Comparativas/`.
3. **Añadir su columna en Comparativas**: en cada tabla de [[Comparativa por Capitulo]] y [[Matriz de Decision]], la sesión cronista añade la columna vacía (o con ⚪) de la herramienta nueva. La herramienta la irá llenando **solo en su columna**, con notas firmadas fuera de tabla (`> [!note] Nota de <Herramienta> 🟢`).
4. **Actualizar el mapa raíz**: añadir la fila en la tabla de guías de [[Masterclass IA Agentica - Mapa de Contenidos]] con estado ⏳ 0/10.
5. **Actualizar CLAUDE.md**: añadir la herramienta a la tabla de territorios de escritura (qué puede tocar y qué no).
6. **Dar contexto de arranque a la herramienta**: en su primera sesión sobre el vault, pasarle como contexto el mapa raíz, su MOC espejo y el CLAUDE.md. Recordar que esa herramienta no lee CLAUDE.md automáticamente — el usuario se lo da en el primer mensaje.
7. **Human in the loop**: el usuario confirma capítulo a capítulo, igual que con las demás guías, y arbitra cualquier conflicto entre herramientas.

## Reglas que hereda toda herramienta nueva

- Marcadores de certeza obligatorios: 🔵 documentado oficialmente · 🟢 observable en sesión real **de esa herramienta** · 🟡 simplificación pedagógica · ⚪ no público.
- Cada capítulo cierra con resumen, analogía y autoevaluación con respuestas plegables.
- Teoría + práctica real en la misma sesión, nunca solo teoría.
- Progresivo: nunca dos capítulos sin confirmación del usuario entre medias.
- Regla de columnas en `Comparativas/`: solo la columna propia; nunca borrar contenido ajeno; la estructura y los juicios 🟡 los mantiene la sesión cronista de Claude Code.
