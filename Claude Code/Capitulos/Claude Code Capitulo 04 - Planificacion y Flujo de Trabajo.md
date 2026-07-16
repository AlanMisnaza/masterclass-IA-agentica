---
tags:
  - claude-code
  - masterclass
  - capitulo-04
  - planificacion
estado: completo
capitulo: 4
creado: 2026-07-05
---

# Capítulo 4 — Planificación y Flujo de Trabajo de Cambios

[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Anterior: [[Claude Code Capitulo 03 - Contexto y Memoria]] · Siguiente → [[Claude Code Capitulo 05 - Git y Control de Versiones]]

> [!tip] La pregunta de este capítulo
> Las instrucciones humanas casi nunca traen toda la información necesaria para actuar bien. ¿Cómo decide un agente cuándo preguntar, cuándo proponer y seguir, y qué controles (Plan Mode, permisos, verificación obligatoria) impiden que actúe mal sobre tu proyecto?

## 4.1 El problema: instrucciones ambiguas y el costo de actuar mal

Cuando pediste "clonemos el repo", esa instrucción parecía completa — pero al explorar, apareció una carpeta que ya era un clon, en una rama con trabajo sin commitear. Actuar de inmediato (clonar de nuevo, o cambiar de rama sin más) habría arriesgado perder contexto o mezclar dos líneas de trabajo sin que tú lo decidieras. La instrucción original no contenía toda la información necesaria para actuar bien — y eso es la norma, no la excepción, con instrucciones humanas.

## 4.2 Cómo se interpretan instrucciones: resolver ambigüedad antes de actuar

🔵 *Documentado*: existe una herramienta formal para esto — preguntas estructuradas de opción múltiple que se presentan al usuario cuando hay una decisión real que solo él puede tomar.

🟢 *Observable en esta sesión* — así se ha usado, varias veces, con propósitos distintos:

| Momento | Qué se preguntó | Por qué no se decidió sola |
|---|---|---|
| Inicio del vault | ¿Ritmo progresivo o todo de una vez? ¿Un archivo o varios enlazados? | Afecta cómo se consume toda la guía de ahí en adelante |
| Rama en conflicto (Cap. 2) | ¿Nueva rama desde `main` o seguir en `feature/notificacion-teams`? | Mezclar dos líneas de trabajo en un repo real no es reversible sin fricción |
| Alcance de la memoria (permisos web) | ¿Global o solo este proyecto? | Cambia el comportamiento de *todos* tus proyectos, no solo este |

No toda decisión merece una pregunta formal de este tipo. Cuando la decisión es de bajo riesgo y reversible (por ejemplo, qué nombre darle a una rama nueva), basta con **proponer con una recomendación clara y seguir**, dejando la puerta abierta a que se corrija — es el registro que se usó, por ejemplo, al proponer el proyecto de práctica de datos en la conversación anterior a elegir el repo real. La pregunta formal se reserva para decisiones con consecuencias reales o ambigüedad genuina; la recomendación abierta, para todo lo demás.

## 4.3 Plan Mode: la función formal — y por qué no es lo que usamos hasta ahora

🔵 *Documentado*: Claude Code tiene un modo específico llamado **Plan Mode**, en el que solo se puede leer y proponer — ninguna edición se ejecuta hasta que el plan se aprueba explícitamente. Es una función nombrada, con un mecanismo de activación propio.

⚠️ *Precisión que hay que hacer, otra vez*: en ningún momento de esta sesión se activó formalmente el Plan Mode. Lo que ocurrió en el Capítulo 2 (proponer la disyuntiva de la rama y esperar) fue el **patrón general** de plantear antes de actuar — no la función específica. Es la misma distinción que ya hicimos en el Capítulo 2 §2.4: mismo espíritu, mecanismo distinto. Confundir ambos sería exactamente el tipo de imprecisión que el marco 🔵/🟢/🟡/⚪ está diseñado para evitar.

## 4.4 Gestión de tareas: una memoria de trabajo distinta de todo lo visto en el Capítulo 3

🟢 *Observable, y de forma muy concreta*: durante esta sesión se ha usado un sistema de tareas (crear, marcar en progreso, marcar completada) para cada capítulo y cada frente de trabajo del repo. En un punto de la sesión, al consultar la lista, la respuesta fue **"No tasks found"** — la lista completa de tareas anteriores había desaparecido, sin relación con el estado real del trabajo (los capítulos seguían escritos en sus archivos).

⚪ *Honestidad necesaria*: no se puede afirmar con certeza *por qué* se vació — si fue por una compactación de contexto (Capítulo 3), por alcance de sesión, o por otro motivo interno. Lo que sí es 🟢 observable es la consecuencia práctica: **la lista de tareas no es una fuente de verdad persistente**, a diferencia de la memoria de usuario o de un `CLAUDE.md`.

Esto permite una tabla de síntesis útil, cruzando lo visto en este capítulo con todo el Capítulo 3:

| Mecanismo | ¿Sobrevive a esta sesión? | ¿Quién lo escribe? | ¿Dónde vive? |
|---|---|---|---|
| Lista de tareas (Task) | No, es frágil incluso dentro de la sesión | Claude, como bitácora de trabajo en curso | Efímero, ligado a la sesión activa |
| Ventana de contexto | No | La conversación misma | Efímero, se compacta o se pierde |
| `CLAUDE.md` | Sí, entre sesiones | El humano | Dentro del repositorio, versionado |
| Memoria de usuario | Sí, entre sesiones y proyectos | Claude, autónomamente | Fuera del repositorio, en tu máquina |

La lección práctica: si algo importa que sobreviva, **no basta con que esté en la lista de tareas** — hay que escribirlo en `CLAUDE.md` o dejar que se guarde como memoria de usuario, tal como se hizo con el método de trabajo y las correcciones de esta sesión.

## 4.5 Verificación antes de avanzar: parte es hábito, parte es la propia herramienta

Ya vimos en el Capítulo 2 (§2.5) la disciplina de distinguir lo verificable de lo que hay que preguntar. Hay un refuerzo adicional que vale la pena nombrar explícitamente:

🔵 *Documentado y mecánico, no solo "buena costumbre"*: la herramienta de edición de archivos **exige haber leído el archivo dentro de la misma conversación antes de poder editarlo**. No es una sugerencia de estilo — si se intenta editar sin haber leído antes, la herramienta falla. Es una barrera de seguridad impuesta por el diseño de la herramienta misma, no algo que dependa únicamente del criterio del modelo en ese momento.

🟢 Esto se puede ver literalmente en esta sesión: antes de expandir el Capítulo 3 con el detalle de memoria, se leyeron primero tanto el propio capítulo como el archivo de memoria a corregir — no por costumbre, sino porque editar sin ese paso previo no habría sido posible.

## 4.6 El ciclo de planificación, de punta a punta

```
        instrucción recibida
                │
                ▼
    ¿es ambigua o tiene consecuencias
     reales/difíciles de revertir?
       │                      │
      sí                      no
       │                      │
       ▼                      ▼
  presentar opciones     proceder con verificación
  y esperar respuesta      mínima (leer antes de
       │                    escribir/editar)
       └──────────┬─────────┘
                   ▼
                actuar
                   ▼
         verificar el resultado
                   ▼
              informar
```

Este diagrama no reemplaza el ciclo del Capítulo 1 (recibe→explora→planifica→actúa→verifica→informa) — lo detalla justo en el punto de "planifica", que es el que este capítulo desarrolla en profundidad.

## Resumen del capítulo

- Las instrucciones humanas suelen venir incompletas; parte del trabajo es notar cuándo falta información antes de actuar, no después.
- Existe una herramienta formal para preguntas estructuradas, reservada para decisiones con consecuencias reales; para el resto, basta proponer con una recomendación clara.
- Plan Mode es una función específica y nombrada — no hay que confundirla con el patrón general de "proponer y esperar" que se usó de forma orgánica en esta sesión.
- La lista de tareas es memoria de trabajo frágil, distinta de `CLAUDE.md` y de la memoria de usuario — lo que importa que persista no puede depender solo de ella.
- Parte de "evitar errores" está impuesta por el diseño mismo de las herramientas (no se puede editar sin haber leído antes), no solo por el criterio del modelo.

## Analogía

Un cirujano no opera apenas escucha el síntoma: primero pide los exámenes que hagan falta, y si el diagnóstico es ambiguo, consulta con el paciente o con otro especialista antes de decidir el procedimiento. Además, el propio protocolo del quirófano *exige* ciertos pasos de verificación antes de cualquier corte — no es que el cirujano decida por las buenas hacerlos: el sistema no permite avanzar sin ellos. Planificar bien no es solo una virtud personal; también es una estructura que te obliga a no saltarte pasos.

## Autoevaluación

1. ¿Por qué no toda decisión ambigua merece una pregunta formal de opción múltiple? Da un criterio para distinguir cuándo sí y cuándo no.
2. Explica la diferencia entre "Plan Mode" como función específica y el patrón general de "proponer y esperar" usado en el Capítulo 2.
3. ¿Qué evidencia hay en esta sesión de que la lista de tareas no es una fuente de verdad persistente? ¿Qué consecuencia práctica tiene eso?
4. ¿Qué significa que la verificación "antes de avanzar" está en parte impuesta por el diseño de la herramienta, y no solo por criterio del modelo? Da el ejemplo concreto de este capítulo.
5. Ubica en el diagrama de síntesis (§4.6) en qué punto se resolvió la disyuntiva de la rama del Capítulo 2.

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. Se reserva para decisiones con consecuencias reales o difíciles de revertir (ej. mezclar ramas de un repo real); para decisiones de bajo riesgo y reversibles, basta proponer con una recomendación clara y dejar espacio a corrección.
> 2. Plan Mode es una función nombrada y activable explícitamente, donde no se ejecuta ninguna edición hasta aprobación formal. El patrón general es solo el comportamiento de plantear opciones y esperar respuesta, sin activar esa función concreta — mismo espíritu, mecanismo distinto.
> 3. La lista de tareas se vació en un punto de la sesión ("No tasks found") sin relación con el estado real del trabajo. La consecuencia: lo que deba sobrevivir tiene que guardarse en CLAUDE.md o memoria de usuario, no basta con que esté anotado como tarea.
> 4. Significa que la herramienta de edición literalmente no permite editar un archivo si no se ha leído antes dentro de la misma conversación — es una restricción del propio sistema, no una elección del modelo en ese momento. Ejemplo: antes de expandir el Capítulo 3, se leyó primero el archivo.
> 5. En la rama "¿es ambigua o tiene consecuencias reales/difíciles de revertir? → sí → presentar opciones y esperar respuesta" — ahí se ubica la pregunta sobre qué hacer con `feature/notificacion-teams`.

---
[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Siguiente → [[Claude Code Capitulo 05 - Git y Control de Versiones]]
