---
tags:
  - codex
  - evidencia
  - bitacora
creado: 2026-07-19
estado: activa
---

# 📓 Codex — Bitácora de Exploración

[[Codex - Mapa de Contenidos|← Mapa de Codex]] · [[Codex - Mapa de Preguntas|Mapa de preguntas →]]

Registro cronológico de lo que se observa, consulta o queda sin verificar durante sesiones reales de Codex. Su objetivo es que cualquier aprendizaje consolidado pueda volver a una sesión, un comando, un artefacto o una fuente concreta.

> [!warning] Cómo leer esta bitácora
> Una entrada 🟢 describe solo lo visto en la sesión y el alcance indicados; no se generaliza a otras superficies, planes o fechas sin evidencia adicional. La presencia de una afirmación en un archivo histórico del vault no la convierte en evidencia de Codex.

## Protocolo de registro

Cada observación nueva debe indicar:

- Identificador, fecha y objetivo de la sesión.
- Marcador de certeza y alcance exacto.
- Acción reproducible o fuente oficial, sin copiar datos sensibles.
- Resultado observado, separación explícita entre hecho y aprendizaje.
- Pregunta del [[Codex - Mapa de Preguntas|mapa de preguntas]] que responde o que abre.

## Sesión CX-2026-07-19-01 — Protocolo de arranque y ruta documental

**Objetivo.** Validar el estado del vault y establecer una ruta para documentar Codex sin adelantar capítulos.

**Contexto.** Aplicación de escritorio Codex, workspace local `C:\Users\ALANMILE\0.prueba\docuemntacion desde claude app`, rama `main`.

### E-001 — Inspección local del workspace

- **Certeza:** 🟢 observable en esta sesión de Codex de escritorio.
- **Acción:** ejecutar `git status --short`, `git status --branch --short`, `rg --files` y lectura de notas Markdown desde el workspace.
- **Resultado observado:** Codex pudo inspeccionar el estado Git, enumerar archivos y leer los documentos solicitados dentro del workspace.
- **Alcance:** demuestra esas operaciones en este entorno y sesión; no demuestra permisos para otros directorios ni para otras superficies de Codex.
- **Aprendizaje provisional:** la inspección local es una primera fuente adecuada para registrar el estado de un vault antes de editarlo.
- **Preguntas relacionadas:** [[Codex - Mapa de Preguntas#CX-P01 — Fronteras de lectura, escritura y ejecución local|CX-P01]].

### E-002 — Estado inicial preservado

- **Certeza:** 🟢 observable en esta sesión de Codex de escritorio.
- **Acción:** `git status --short` y `git status --branch --short`.
- **Resultado observado:** la rama era `main`, estaba 9 commits por delante de `origin/main`, y `AGENTS.md` junto con `.codex/` aparecían como no rastreados. No se detectaron modificaciones en archivos rastreados.
- **Alcance:** fotografía de este workspace el 2026-07-19; no es una propiedad de Codex ni una recomendación Git.
- **Decisión:** conservar esos archivos sin modificarlos durante esta sesión.

### E-003 — Territorio Codex aún inexistente

- **Certeza:** 🟢 observable en esta sesión de Codex de escritorio.
- **Acción:** enumerar los archivos del vault y contrastarlos con [[Masterclass IA Agentica - Mapa de Contenidos]].
- **Resultado observado:** no existía el directorio `Codex/`; el mapa raíz describía a Codex como futuro.
- **Decisión:** crear un territorio de exploración con bitácora y mapa de preguntas, sin archivos de capítulos ni cambios en Comparativas.

### C-001 — Contexto histórico que no se adopta como evidencia

Los documentos [[Comparativa por Capitulo]] y [[Matriz de Decision]] ya incluyen contenido sobre Codex y una lista general de fuentes oficiales. Esta sesión no ha verificado esas afirmaciones ni su atribución; por tanto se tratan como antecedente a contrastar, no como pruebas 🟢 de la guía de Codex.

### Preguntas abiertas al cerrar la sesión

- ¿Cómo se delimitan y comunican en la práctica los permisos de lectura, escritura y ejecución? → [[Codex - Mapa de Preguntas#CX-P01 — Fronteras de lectura, escritura y ejecución local|CX-P01]].
- ¿Qué instrucciones de proyecto se cargan y cómo se resuelven entre sí? → [[Codex - Mapa de Preguntas#CX-P02 — Instrucciones de proyecto y precedencia|CX-P02]].
- ¿Qué información de la sesión persiste y cuál queda fuera de ella? → [[Codex - Mapa de Preguntas#CX-P03 — Contexto, continuidad y límites de memoria|CX-P03]].

## Plantilla para la próxima entrada

### E-XXX — Título descriptivo

- **Certeza:** 🟢 / 🔵 / 🟡 / ⚪.
- **Contexto y alcance:**
- **Acción o fuente:**
- **Resultado observado o afirmación documentada:**
- **Límite conocido:**
- **Aprendizaje o decisión:**
- **Pregunta relacionada:**
