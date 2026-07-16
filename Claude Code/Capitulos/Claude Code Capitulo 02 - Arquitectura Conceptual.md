---
tags:
  - claude-code
  - masterclass
  - capitulo-02
  - arquitectura
estado: completo
capitulo: 2
creado: 2026-07-05
---

# Capítulo 2 — Arquitectura Conceptual: cómo "entiende" una base de código

[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Anterior: [[Claude Code Capitulo 01 - Fundamentos y Filosofia]] · Siguiente → [[Claude Code Capitulo 03 - Contexto y Memoria]]

> [!tip] Ejemplo central de este capítulo
> Todo este capítulo usa un caso 🟢 **real de esta misma sesión**: explorar y empezar a contribuir al repositorio `transversal-analitica-institucional` de la Célula de Analítica Institucional. No es un ejemplo inventado — es el rastro real de decisiones que se tomaron para entender ese repo antes de tocar nada.

## 2.1 El problema de fondo: no existe una "carga completa" del proyecto

Una idea intuitiva pero equivocada es pensar que Claude Code, al abrir un proyecto, "carga" todo el repositorio en su cabeza y luego razona sobre esa foto completa. No es así.

🔵 *Documentado*: los modelos detrás de Claude Code tienen una ventana de contexto finita (por ejemplo, Sonnet 5 maneja hasta 1M de tokens en esta generación). Es grande, pero sigue siendo un recurso limitado y compartido con el resto de la conversación.

🟡 *Simplificación pedagógica*: incluso si la ventana fuera enorme, volcar un repositorio entero en cada turno sería ineficiente e innecesario — la mayoría del código de cualquier proyecto real es irrelevante para una pregunta concreta. Por eso el comportamiento observable de Claude Code no es "leer todo", sino **explorar de forma incremental y dirigida**, igual que haría una persona nueva en un equipo.

## 2.2 Cómo decide qué leer y en qué orden: exploración dirigida por hipótesis

Este es el rastro real de esta sesión, en orden:

```
1. ls carpeta padre               → hipótesis: "¿ya existe algo con ese nombre?"
2. encontré carpeta con el nombre del repo
3. git status / git remote -v     → hipótesis: "¿es ya un clon de este repo?" → SÍ
4. git branch -vv / git log       → hipótesis: "¿en qué estado está?" → rama con trabajo sin commitear
5. find -maxdepth 2 (repo)        → hipótesis: "¿dónde vive la documentación?"
6. vi "documentacion/lineamientos"→ nombre de carpeta = señal fuerte
7. find dentro de esa carpeta     → 7 archivos, no cientos
8. Read de 4 archivos específicos → los que el propio nombre delataba como relevantes
                                     (readme-template, lineamiento-git-github, github/README, 
                                      creacion-asistentes-template)
```

En ningún punto de esa secuencia se leyó el repositorio completo. Cada paso usó el resultado del paso anterior para decidir el siguiente: el nombre de una carpeta (`documentacion/lineamientos`) fue suficiente señal para no seguir explorando `IA/`, `SQL/` o `gestion-actividades/` en ese momento — no porque esas carpetas no importaran nunca, sino porque no eran relevantes *para la pregunta que se estaba resolviendo entonces* (dónde está el estándar de README).

🟢 *Observable en esta sesión*: puedes revisar el historial de esta conversación y ver exactamente esa secuencia de comandos — no es una reconstrucción idealizada.

🟡 *Simplificación pedagógica*: no puedo describir el mecanismo interno exacto por el cual se "decide" seguir una pista en lugar de otra (eso es interno al modelo, ⚪ no público). Lo que sí es un patrón observable y repetible es la **estrategia**: usar nombres de archivos/carpetas, resultados de comandos previos y la tarea concreta como filtro, en vez de fuerza bruta.

### Diagrama: embudo de exploración

```
                    Repositorio completo
                  (decenas de carpetas/archivos)
                            │
                            ▼
        filtro: nombre de carpeta relevante a la tarea
                            │
                 "documentacion/lineamientos"
                            │
                            ▼
              filtro: nombre de archivo relevante
                            │
        readme-template.md · lineamiento-git-github.md
                            │
                            ▼
                  4 archivos leídos de verdad
              (no las ~30 rutas que existen en el repo)
```

## 2.3 Cómo usa el contexto: por qué se pudo cruzar información de 4 archivos distintos

La duplicación en la documentación del repo (la tabla "Ficha Técnica" y la estructura del README, repetidas en `readme-template.md` **y** en `lineamiento-git-github.md`) no se detectó leyendo un archivo "con más atención" — se detectó porque **los 4 archivos se leyeron en un solo bloque** y sus contenidos quedaron disponibles simultáneamente dentro de la misma ventana de contexto. Comparar A contra C solo es posible si A y C están ambos "presentes" al mismo tiempo.

```
┌─────────────────────── Ventana de contexto activa ───────────────────────┐
│                                                                           │
│  system prompt · definiciones de herramientas · CLAUDE.md del proyecto  │
│  ────────────────────────────────────────────────────────────────────   │
│  historial de la conversación (todo lo dicho hasta ahora)               │
│  ────────────────────────────────────────────────────────────────────   │
│  resultados de herramientas ya ejecutadas:                              │
│    · contenido de github/README.md                                      │
│    · contenido de readme-template.md          ←── comparables entre sí  │
│    · contenido de lineamiento-git-github.md    ←── porque coexisten     │
│    · contenido de creacion-asistentes-template.md                       │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

🔵 *Documentado*: esta ventana no es infinita, y cuando una conversación se alarga mucho, su contenido se **compacta** (se resume) para seguir cabiendo — ese mecanismo se explica a fondo en el [[Claude Code Capitulo 03 - Contexto y Memoria|Capítulo 3]]. Por ahora basta con entender que "usar el contexto" significa literalmente esto: mantener información ya recolectada disponible para cruzarla más adelante en la misma sesión, dentro de un espacio finito.

## 2.4 Cómo planifica cambios antes de escribir código

Antes de crear la rama nueva o escribir el README, se presentó explícitamente la disyuntiva de qué hacer con la rama `feature/notificacion-teams` (que tenía archivos sin commitear) y se esperó tu decisión antes de ejecutar cualquier comando de Git.

🔵 *Documentado*: Claude Code tiene un modo formal para esto llamado **Plan Mode**, en el que solo lee y propone, sin ejecutar ediciones, hasta que el plan se aprueba explícitamente. Lo veremos en detalle en el [[Claude Code Capitulo 04 - Planificacion y Flujo de Trabajo|Capítulo 4]].

⚠️ *Precisión importante*: lo que ocurrió en esta sesión **no fue formalmente Plan Mode** (no se activó ese modo específico) — fue el comportamiento general de plantear una decisión con consecuencias reales y esperar confirmación antes de actuar. Es la misma filosofía, pero conviene no confundir el patrón general con la función concreta que lleva ese nombre. Esta distinción es exactamente el tipo de precisión que exige el marco 🔵/🟢/🟡/⚪ del Capítulo 1: no todo lo que "parece" una función documentada lo es.

## 2.5 Cómo evita introducir errores: lo verificable frente a lo que hay que preguntar

En el borrador del README del repo, hay una línea divisoria clara:

| Campo | Cómo se completó | Por qué |
|---|---|---|
| Stack Tecnológico, Estructura del Proyecto, Guía de Instalación | Redactado con confianza | Se podía **verificar directamente** explorando el propio repositorio |
| Data Steward (nombre de una persona) | `[Por definir]` | No hay ningún archivo que lo diga — es conocimiento institucional que no está en el repo |
| Nivel de Confidencialidad / Criticidad | Propuesto, marcado como "a confirmar" | Es un juicio de negocio, no un hecho verificable en el código |

Esta distinción — entre lo que se puede fundamentar en evidencia real dentro del proyecto y lo que requiere criterio humano o institucional — es la misma lógica del Capítulo 1 §1.6, aplicada ahora a una tarea concreta en lugar de a la documentación sobre Claude Code. Evitar errores no significa "no cometer errores de sintaxis": significa **no rellenar huecos de conocimiento con suposiciones plausibles**.

## 2.6 Cómo ejecuta tareas complejas: el ciclo anidado

La tarea de alto nivel ("ayúdame a entender y contribuir a este repo") no se resolvió con una sola pasada del ciclo del Capítulo 1 — se resolvió con varios ciclos anidados, cada uno completo en sí mismo:

```
TAREA: entender y contribuir al repo
│
├─ Sub-ciclo 1: ¿ya existe un clon?
│    explora (ls, git status) → informa (sí, con trabajo sin commitear)
│
├─ Sub-ciclo 2: ¿cómo se organiza la documentación?
│    explora (find, Read x4) → informa (encontré duplicación)
│
├─ Sub-ciclo 3: ¿cómo resolvemos el conflicto de ramas?
│    planifica (dos opciones) → [espera aprobación] → actúa (checkout -b)
│
└─ Sub-ciclo 4: redactar el README
     actúa (Write) → verifica (marca placeholders en vez de inventar) → informa
```

Cada sub-ciclo es pequeño y verificable por separado. La complejidad de la tarea completa no se maneja de un solo salto: se descompone en pasos donde cada uno puede revisarse, corregirse o pausarse — de hecho, esta sesión se pausó exactamente después del sub-ciclo 4, cuando notaste que el foco se había desplazado de "entender Claude Code" a "terminar el README".

## Resumen del capítulo

- Claude Code no carga un repositorio completo: explora de forma incremental, usando nombres de archivos/carpetas y resultados previos como señales para decidir qué leer a continuación.
- El contexto de la sesión es lo que permite cruzar información de varios archivos leídos en momentos distintos — es un espacio finito, no infinito.
- Antes de ejecutar cambios con consecuencias reales, se plantean las opciones y se espera confirmación — el mismo espíritu que formaliza Plan Mode, aunque no sea siempre esa función específica.
- Evitar errores incluye distinguir explícitamente lo que es verificable desde el propio proyecto de lo que requiere conocimiento humano/institucional — y dejarlo marcado en vez de inventarlo.
- Las tareas complejas se resuelven como una cadena de ciclos pequeños y anidados (explora→planifica→actúa→verifica→informa), no como un único salto.

## Analogía

Piensa en un perito que llega a inspeccionar un edificio que nunca ha visto. No empieza midiendo cada ladrillo de cada habitación: mira los planos generales, identifica qué zonas son relevantes para lo que le pidieron revisar, entra primero a esas, y solo si encuentra algo que lo justifique, amplía la inspección a zonas contiguas. Al final cruza sus notas de varias habitaciones para detectar patrones (una grieta que se repite en dos pisos distintos) — y cuando algo requiere una decisión del propietario del edificio, no la toma por su cuenta: la pregunta y espera respuesta antes de tocar nada estructural.

## Autoevaluación

1. ¿Por qué Claude Code no "carga" el repositorio completo en cada tarea, más allá de que técnicamente podría intentarlo?
2. Describe el patrón de "exploración dirigida por hipótesis" usando el propio ejemplo del repo de este capítulo.
3. ¿Qué hizo posible detectar que la Ficha Técnica estaba duplicada en dos archivos distintos?
4. En el borrador del README, ¿qué diferencia hay entre los campos rellenados con confianza y los marcados `[Por definir]`? ¿Por qué importa esa diferencia?
5. Identifica al menos dos de los sub-ciclos anidados de la sección 2.6 y explica qué pregunta respondía cada uno.

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. Porque la ventana de contexto es finita y, aunque fuera enorme, la mayoría del código de un proyecto real es irrelevante para una tarea concreta — explorar de forma incremental es más eficiente que la fuerza bruta.
> 2. Se usó el nombre de una carpeta (`documentacion/lineamientos`) como señal para decidir dónde mirar, en vez de recorrer todas las carpetas del repo por igual; cada paso (ls → git status → find → Read) se apoyó en el resultado del paso anterior.
> 3. Que los 4 archivos se leyeron dentro de la misma ventana de contexto de la conversación, lo que permitió compararlos entre sí en lugar de olvidar el contenido de uno al leer el siguiente.
> 4. Los campos con confianza (Stack Tecnológico, Estructura) se podían verificar explorando el propio repositorio; los marcados `[Por definir]` (Data Steward, Confidencialidad) requieren conocimiento institucional o de negocio que no está en ningún archivo — importa porque inventarlos sería presentar una suposición como un hecho.
> 5. Ejemplos: sub-ciclo 1 respondía "¿ya existe un clon de este repo?"; sub-ciclo 2 respondía "¿dónde vive la documentación y hay algo raro en ella?"; sub-ciclo 3 respondía "¿qué hacemos con la rama que ya tenía trabajo sin commitear?".

---
[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Siguiente → [[Claude Code Capitulo 03 - Contexto y Memoria]]
