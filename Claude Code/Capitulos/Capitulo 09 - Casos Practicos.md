---
tags:
  - claude-code
  - capitulo-09
  - casos-practicos
  - sesiones-reales
estado: completo
capitulo: 9
creado: 2026-07-04
actualizado: 2026-07-09
---

# Capítulo 9 — Casos Prácticos: sesiones completas

[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Anterior: [[Capitulo 08 - Comparativas]] · Siguiente → [[Capitulo 10 - Mejores Practicas]]

> [!tip] La pregunta de este capítulo
> Los capítulos anteriores enseñaron los conceptos por separado: contexto, permisos, Git, MCP, subagentes. Pero en el trabajo real **todo ocurre junto**. Este capítulo muestra cómo se combinan, usando **casos reales que ocurrieron durante la construcción de esta misma guía** — no ejemplos hipotéticos, sino sesiones que puedes verificar en los transcripts.

> [!note] Por qué estos casos y no otros
> Podríamos inventar escenarios ("imagina que tienes un bug en producción..."), pero sería contradecir el método de esta guía: evidencia de primera mano, marcador 🟢, no simulaciones. Todo lo que sigue **ocurrió de verdad** entre el 4 y el 9 de julio de 2026, en las sesiones que construyeron este vault. Las referencias a capítulos son enlaces a donde se enseñó cada concepto.

## 9.1 Caso 1 — Construir una guía desde dentro del propio sistema

**Situación**: documentar Claude Code usando Claude Code, produciendo un vault de Obsidian completo con 10 capítulos, en vivo.

**Lo que lo hace interesante**: es un caso *meta* — el sistema se documenta a sí mismo. Parece simple hasta que aparecen los problemas reales.

### Conceptos en acción

| Capítulo | Concepto | Cómo apareció aquí |
|---|---|---|
| [[Capitulo 01 - Fundamentos y Filosofia\|Cap 1]] | Filosofía Unix | Cada capítulo es un archivo .md independiente, componible, no un monolito |
| [[Capitulo 02 - Arquitectura Conceptual\|Cap 2]] | Lectura de código | Claude lee el vault completo para entender la estructura antes de escribir |
| [[Capitulo 03 - Contexto y Memoria\|Cap 3]] | CLAUDE.md + auto-memory | El método de trabajo vive en CLAUDE.md; las preferencias del usuario se acumulan en auto-memory |
| [[Capitulo 03 - Contexto y Memoria\|Cap 3]] | Compactación | La sesión se compactó múltiples veces — el resumen preservó el contexto esencial |
| [[Capitulo 04 - Planificacion y Flujo de Trabajo\|Cap 4]] | Permisos | WebSearch/WebFetch auto-aprobados en `settings.json` del proyecto |
| [[Capitulo 04 - Planificacion y Flujo de Trabajo\|Cap 4]] | Plan Mode | Antes de cada capítulo: confirmar estado → alinearse → escribir |
| [[Capitulo 06 - Comandos CLI y Superficies\|Cap 6]] | Superficies | Toda la guía se escribió desde la app; las sesiones CLI fueron paralelas |
| [[Capitulo 07 - MCP y Herramientas\|Cap 7]] | MCP + tool search | Herramientas MCP cargadas bajo demanda (registry, Microsoft Learn, session management) |

### Patrón clave: el checkpoint humano

🟢 En el Capítulo 7, después de verificar tres confirmaciones (estado del vault, trabajo pendiente, roles del protocolo), el cronista arrancó a trabajar **sin esperar el visto bueno explícito del usuario**. El usuario interrumpió: *"tenías que confirmarme, A MÍ, antes de arrancar, ¿qué pasó?"*

**Lección aprendida**: "si cuadra, seguimos" lo juzga el usuario, no Claude. Verificar datos y confirmar datos son dos acciones distintas — la primera la puede hacer Claude solo; la segunda requiere que el humano diga "adelante". Este feedback se guardó en auto-memory ([[Capitulo 03 - Contexto y Memoria|Cap 3 §3.4]]) para que no se repita.

**Concepto subyacente**: el human in the loop del [[Capitulo 04 - Planificacion y Flujo de Trabajo|Capítulo 4]] no es decorativo — es un control real que el agente debe respetar incluso cuando técnicamente puede seguir solo.

### Patrón clave: la compactación como aliada

🟢 Esta sesión se compactó múltiples veces al acercarse al límite de contexto. Cada compactación generó un resumen que preservó:
- El estado de cada capítulo (qué está ✅ y qué ⏳)
- Las reglas del protocolo de dos sesiones
- Los feedbacks guardados en memoria
- Los hallazgos pendientes de documentar

La compactación no fue un problema — fue una **feature**. La sesión pudo continuar el trabajo horas después sin perder el hilo, porque el mecanismo del [[Capitulo 03 - Contexto y Memoria|Capítulo 3 §3.6]] funcionó como se diseñó.

---

## 9.2 Caso 2 — El protocolo de tres cerebros (cronista + ejecutor + humano)

**Situación**: el usuario quiere mejorar un prompt de asistente GPT. En vez de hacerlo en esta sesión, abre una **sesión CLI** en otra carpeta (`correcion del asistente`) y le pide a esta sesión (app) que **observe y documente** lo que hace la CLI.

**Lo que lo hace interesante**: dos instancias de Claude Code trabajando en paralelo sobre tareas distintas, coordinadas por el usuario.

### El flujo real

```
Usuario ←──────────────────────────── humano in the loop
   │                                        │
   ├──→ Sesión CLI (ejecutor)               │
   │    carpeta: correcion del asistente    │
   │    tarea: mejorar prompt GPT           │
   │    modelo: Opus 4.8                    │
   │                                        │
   └──→ Sesión App (cronista)               │
        carpeta: vault de la guía           │
        tarea: observar, explicar, documentar
        modelo: Opus 4.8 (luego 4.6)
```

### Conceptos en acción

| Capítulo | Concepto | Cómo apareció aquí |
|---|---|---|
| [[Capitulo 03 - Contexto y Memoria\|Cap 3]] | Aislamiento de contexto | Cada sesión tiene su propio contexto — el cronista no sabe qué hace el ejecutor a menos que lo lea |
| [[Capitulo 06 - Comandos CLI y Superficies\|Cap 6]] | Gap de visibilidad (§6.6) | `list_sessions` no encontró la sesión CLI → descubrimiento del prefijo `local_` |
| [[Capitulo 06 - Comandos CLI y Superficies\|Cap 6]] | Lectura de JSONL | Workaround: leer el transcript directamente en `~/.claude/projects/` |
| [[Capitulo 07 - MCP y Herramientas\|Cap 7]] | Herramientas MCP de sesión | `list_sessions` y `search_session_transcripts` son herramientas del servidor `ccd_session_mgmt` |
| [[Capitulo 04 - Planificacion y Flujo de Trabajo\|Cap 4]] | Límites del agente | El cronista tiene **prohibido** escribir fuera del vault — solo lee carpetas externas |

### El hallazgo inesperado: el gap de visibilidad

🟢 Al intentar observar la sesión CLI, el cronista descubrió que las herramientas de la app no la veían. La investigación reveló:

1. **Síntoma**: `list_sessions` devolvía lista vacía para el proyecto CLI.
2. **Diagnóstico**: los IDs de sesión CLI (`2c5acaed-...`) no tienen el prefijo `local_` que la app espera.
3. **Causa raíz**: `list_sessions` pertenece al servidor MCP `ccd_session_mgmt` (CCD = Claude Code **Desktop**) — solo indexa sesiones que ella creó.
4. **Solución**: leer el archivo JSONL directamente con la herramienta `Read`.

**Patrón de resolución**: síntoma → hipótesis → verificación en el sistema de archivos → solución → documentación del hallazgo (se convirtió en la §6.6 del Capítulo 6). Este es el flujo natural de debugging, pero aplicado a la propia infraestructura de Claude Code.

### El valor del modelo

El usuario lo resumió con una frase que se quedó:

> *"Es como hacer pair programming con tres cerebros atentos: tú cronista, especialista en lo que se requiera; el que ejecuta, especialista en lo que estamos ejecutando; y yo, que debo ser el human in the loop."*

No es solo un arreglo de conveniencia — es una **arquitectura de trabajo** que explota el aislamiento de contexto (cada sesión se especializa) y la persistencia compartida (CLAUDE.md, memoria) para crear una experiencia mayor que la suma de las partes.

---

## 9.3 Caso 3 — Mejorar un prompt de asistente GPT desde la CLI

**Situación**: el usuario tiene un asistente GPT que guía usuarios por un lineamiento de documentación de modelos de datos. Quiere que la CLI mejore el prompt (que tiene problemas con URLs sensibles).

**Lo que lo hace interesante**: Claude Code no solo edita código — trabaja sobre **prompts de otros sistemas de IA**. Y la elicitation en CLI apareció como mecánica real por primera vez.

### Conceptos en acción

| Capítulo | Concepto | Cómo apareció aquí |
|---|---|---|
| [[Capitulo 02 - Arquitectura Conceptual\|Cap 2]] | Lectura y comprensión | La CLI leyó `instrucciones.md` (prompt TAREA) y el lineamiento (742 líneas) para entender el sistema completo |
| [[Capitulo 06 - Comandos CLI y Superficies\|Cap 6]] | Elicitation (§6.4) | Opus 4.8 usó `AskUserQuestion` para hacer preguntas con opciones sobre cómo manejar URLs sensibles |
| [[Capitulo 07 - MCP y Herramientas\|Cap 7]] | Herramienta nativa vs. MCP | La CLI usó herramientas nativas (Read, Edit) — no necesitó MCP porque todo era local |
| [[Capitulo 04 - Planificacion y Flujo de Trabajo\|Cap 4]] | Propuesta antes de actuar | Opus 4.8 propuso 3 cambios concretos y pidió confirmación antes de editar |

### El flujo de la sesión CLI

1. **Lectura**: leyó ambos archivos (`instrucciones.md` y el lineamiento) para entender contexto completo.
2. **Análisis**: identificó 3 problemas en el prompt del asistente GPT.
3. **Propuesta**: presentó los 3 cambios con justificación, pidiendo aprobación antes de tocar nada.
4. **Elicitation**: usó `AskUserQuestion` con dos preguntas estructuradas:
   - *"¿Qué debe hacer el asistente con los campos de URL/ruta?"* (2 opciones)
   - *"¿Aplico también el marcador al campo 'Ubicación' de la Sección 1.2?"* (2 opciones)
5. **Ejecución**: tras recibir las respuestas, aplicó los cambios.

### Lo que el cronista observó y documentó

🟢 Este caso generó documentación real en la guía:
- La **elicitation en CLI** (§6.4) se documentó porque la vimos en acción por primera vez en esta sesión.
- El **panel `claude agents`** (§6.7) se documentó porque el usuario compartió la captura del panel donde se veía esta sesión corriendo.
- La **distinción entre llamadas paralelas y subagentes** (§7.6) surgió de explicar los comandos PowerShell que el cronista usó para investigar la estructura de sesiones.

**Lección meta**: una sesión de trabajo real genera más material educativo que un ejemplo inventado, porque los problemas inesperados (el gap de visibilidad, la confusión paralelo vs. subagente) no se pueden predecir — solo descubrir.

---

## 9.4 Caso 4 — El expediente `claude mcp serve`: investigación epistemológica

**Situación**: durante la investigación para el Capítulo 7, se encontraron múltiples fuentes de terceros que describían un comando `claude mcp serve`. ¿Existe o no?

**Lo que lo hace interesante**: no es un caso de "arreglar un bug", sino de **verificar una afirmación técnica** — el tipo de trabajo que un analista de datos (como el usuario) hace constantemente.

### El flujo de investigación

| Paso | Acción | Herramienta | Resultado |
|---|---|---|---|
| 1 | Buscar en la documentación oficial de la CLI | WebFetch → `code.claude.com/docs/en/cli-reference` | ❌ No aparece |
| 2 | Buscar en la documentación de MCP | WebFetch → `code.claude.com/docs/en/mcp` | ❌ No aparece |
| 3 | Buscar en fuentes de terceros | WebSearch | ✅ Varias fuentes lo describen consistentemente |
| 4 | Verificar si las fuentes se copian entre sí | Análisis manual | 🟡 Patrón de eco: descripciones similares, posible fuente común |
| 5 | Conclusión | — | ⚪ No se afirma que existe; se documenta la ausencia con marcador ⚪ |

### Conceptos en acción

| Capítulo | Concepto | Cómo apareció aquí |
|---|---|---|
| [[Capitulo 01 - Fundamentos y Filosofia\|Cap 1]] | Marcadores de certeza (§1.6) | La cadena 🔵→🟢→🟡→⚪ en acción: sin evidencia oficial, no se afirma |
| [[Capitulo 06 - Comandos CLI y Superficies\|Cap 6]] | Fuente matriz (§6.1) | La documentación oficial es la autoridad final; fuentes de terceros no la reemplazan |
| [[Capitulo 07 - MCP y Herramientas\|Cap 7]] | MCP (§7.4-7.5) | El contexto para entender qué haría `serve` si existiera |

### La lección epistemológica

🟡 **Consistencia ≠ verdad.** Cinco fuentes diciendo lo mismo no valen más que una si las cinco se copiaron del mismo dato original sin verificar. En un ecosistema que cambia cada semana (Claude Code tiene changelog semanal), la cadena correcta es:

```
Afirmación → ¿Está en code.claude.com/docs? 
  → Sí: marcador 🔵 
  → No: ¿La puedo probar yo mismo?
    → Sí: marcador 🟢
    → No: marcador ⚪ y a otra cosa
```

Esto aplica igual a documentación técnica que a datos en un dashboard de BI — el usuario, como analista de datos, lo conectó inmediatamente con su trabajo diario.

---

## 9.5 Caso 5 — Investigar la estructura interna de sesiones

**Situación**: el usuario pregunta sobre los comandos PowerShell que el cronista ejecutó para documentar la gestión de sesiones CLI (§6.7 del Capítulo 6). Quiere entender no solo qué hacen, sino por qué se ejecutaron así.

**Lo que lo hace interesante**: es un caso de **introspección operativa** — el usuario no pregunta "haz algo", sino "explícame lo que acabas de hacer y por qué".

### El flujo de investigación (5 comandos, 2 lotes)

**Lote 1 (3 comandos en paralelo)** — independientes entre sí:
1. Listar archivos de sesión de un proyecto → descubrir qué tipos de archivo existen
2. Mismos archivos con tamaño en KB → dimensionar el peso de las sesiones
3. Listar todas las carpetas de proyecto → ver el panorama completo

**Lote 2 (2 comandos en paralelo)** — también independientes:
4. Buscar archivos ocultos en la carpeta del proyecto → verificar si hay metadatos adicionales
5. Listar archivos en la raíz de `.claude` → buscar un índice global

### Conceptos en acción

| Capítulo | Concepto | Cómo apareció aquí |
|---|---|---|
| [[Capitulo 07 - MCP y Herramientas\|Cap 7]] | Llamadas paralelas vs. subagentes (§7.6) | Los 5 comandos fueron llamadas paralelas a herramientas, NO subagentes — resultados pequeños, necesarios en el contexto actual |
| [[Capitulo 03 - Contexto y Memoria\|Cap 3]] | Protección del contexto | Si los resultados hubieran sido enormes (200 archivos), un subagente habría sido la decisión correcta |
| [[Capitulo 02 - Arquitectura Conceptual\|Cap 2]] | Lectura del sistema | Claude "lee" el sistema de archivos igual que lee código — con herramientas de exploración |

### La distinción que generó documentación nueva

🟢 La pregunta del usuario (*"creo que esto es algo de subagentes"*) reveló una confusión frecuente que valió la pena documentar:

| | Herramientas en paralelo | Subagente |
|---|---|---|
| Quién ejecuta | Este mismo Claude | Otro Claude aparte |
| Contexto | Resultados entran aquí | Contexto propio, aislado |
| Cuándo conviene | Resultados pequeños, necesarios aquí | Exploración masiva, trabajo en background |

Esta distinción se añadió a la §7.6 del Capítulo 7 con el ejemplo real como evidencia — otro caso de la práctica enriqueciendo la teoría.

---

## 9.6 Patrones transversales: lo que los cinco casos enseñan juntos

Vistos en conjunto, los cinco casos repiten ciertos patrones que vale la pena nombrar:

### Patrón 1: "La práctica genera mejor documentación que la planificación"

Cuatro de las cinco secciones nuevas que se añadieron a capítulos ya cerrados (§6.4 elicitation, §6.6 gap de visibilidad, §6.7 gestión de sesiones, §7.6 paralelo vs. subagentes) **no estaban planificadas** — surgieron de preguntas o problemas que aparecieron durante el trabajo real. La documentación más útil no se predice; se descubre.

### Patrón 2: "El error del agente es material educativo"

El error del checkpoint (Caso 1), el gap de visibilidad (Caso 2), la confusión paralelo/subagente (Caso 5) — todos fueron errores o limitaciones que, una vez entendidos, se convirtieron en secciones de la guía. No esconder los errores sino documentarlos es lo que hace que una guía sea honesta.

### Patrón 3: "Verificar antes de afirmar"

El expediente `claude mcp serve` (Caso 4) y el gap de visibilidad (Caso 2) comparten la misma estructura: algo que *debería* funcionar, no funciona — y la investigación revela por qué. En ambos casos, la cadena fue: **observar el síntoma → formular hipótesis → verificar en la fuente primaria → documentar con el marcador correcto**. Es el método científico aplicado a herramientas de software.

### Patrón 4: "El contexto compartido multiplica el valor"

Los casos 2 y 3 (tres cerebros, mejora del prompt GPT) muestran que dos sesiones de Claude Code coordinadas por un humano producen más que la suma de las partes: el ejecutor se concentra en la tarea; el cronista observa, conecta y documenta. Pero esto solo funciona si la infraestructura de compartición (CLAUDE.md, memoria, JSONL legible) está en su sitio — sin ella, el cronista queda ciego (Caso 2).

### Patrón 5: "El humano no es un cuello de botella — es un control de calidad"

En el Caso 1, Claude podría haber seguido solo después de verificar los datos. No lo hizo (o no debió hacerlo) porque el checkpoint existe para que el humano valide la dirección, no solo los datos. El agente es rápido pero no infalible; el humano es más lento pero tiene contexto que el agente no puede derivar de los archivos: prioridades, urgencia, intención real detrás de una instrucción ambigua.

---

## Resumen del capítulo

- Los cinco casos provienen de la **construcción real de esta guía** — no son ejemplos inventados, sino sesiones verificables en los transcripts del proyecto.
- **Caso 1** (la guía misma): CLAUDE.md, auto-memory, compactación y el error de checkpoint — el agente debe esperar confirmación humana, no asumirla.
- **Caso 2** (tres cerebros): dos sesiones paralelas, el gap de visibilidad CLI↔App, y el workaround JSONL — una arquitectura de trabajo que explota el aislamiento de contexto.
- **Caso 3** (prompt GPT): Claude Code trabajando sobre prompts de otros sistemas de IA, elicitation en CLI, y el panel `claude agents`.
- **Caso 4** (`claude mcp serve`): investigación epistemológica — consistencia entre fuentes ≠ verdad; solo la documentación oficial y la evidencia de primera mano son autoridad.
- **Caso 5** (estructura de sesiones): la diferencia entre llamadas paralelas a herramientas y subagentes, descubierta por la pregunta del usuario.
- **Patrón transversal**: la práctica genera mejor documentación que la planificación; los errores son material educativo; verificar antes de afirmar.

## Analogía

Piensa en un cirujano aprendiendo cirugía. Puede leer todos los manuales (capítulos 1–8), pero el aprendizaje real ocurre en las **residencias**: casos reales donde todo se combina — la anatomía del Capítulo 2, la asepsia del Capítulo 4, las herramientas del Capítulo 7 — y además aparecen complicaciones que ningún manual predijo. Estos cinco casos son las residencias de esta guía: no reemplazan la teoría, la anclan en la realidad. Y el detalle que los hace valiosos es que *no salieron bien a la primera* — las complicaciones fueron la parte más instructiva.

## Autoevaluación

1. ¿Qué error cometió el cronista en el Caso 1, y qué regla del Capítulo 4 violó?
2. En el Caso 2, ¿por qué `list_sessions` no encuentra sesiones CLI? Resume el diagnóstico en una frase.
3. El Caso 4 concluye que `claude mcp serve` no se puede afirmar que exista. ¿Qué evidencia faltó y qué marcador le corresponde?
4. ¿Cuál es la diferencia entre "verificar datos" y "confirmar dirección" en el contexto del checkpoint humano?
5. Nombra el patrón transversal que conecta los Casos 2 y 4, y explica por qué es relevante para un analista de datos.

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. El cronista verificó las tres confirmaciones y **arrancó a trabajar sin esperar el "adelante" explícito del usuario**. Violó la regla del human in the loop del Capítulo 4: el agente puede preparar, investigar y verificar por su cuenta, pero la decisión de avanzar le corresponde al humano.
> 2. Porque `list_sessions` es una herramienta MCP del servidor `ccd_session_mgmt` (Claude Code **Desktop**) — solo indexa sesiones con prefijo `local_` en el ID; las sesiones CLI usan UUIDs sin prefijo.
> 3. Faltó que el comando apareciera en la documentación oficial (`code.claude.com/docs`) o que se pudiera probar en una sesión real. Las fuentes de terceros, por consistentes que sean, no sustituyen estas dos vías. Marcador: ⚪ (no público / desconocido).
> 4. **Verificar datos** = comprobar que los hechos son correctos (¿en qué capítulo vamos? ¿cuál es el estado del repo?) — esto Claude puede hacerlo solo. **Confirmar dirección** = validar que el siguiente paso es el que el humano quiere dar — esto solo lo puede hacer el humano con un "adelante" explícito.
> 5. **"Verificar antes de afirmar"**: en ambos casos, algo que *debería* funcionar (un comando, una herramienta) no funciona, y la investigación en la fuente primaria revela por qué. Es relevante para un analista de datos porque el mismo patrón aplica a datos en dashboards: un dato consistente en cinco reportes puede estar mal si todos beben de la misma fuente corrupta — la cadena de verificación es: dato → fuente primaria → evidencia directa → marcador de confianza.

---
[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Siguiente → [[Capitulo 10 - Mejores Practicas]]
