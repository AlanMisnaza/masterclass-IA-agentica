---
tags:
  - claude-code
  - capitulo-05
  - git
estado: completo
capitulo: 5
creado: 2026-07-05
---

# Capítulo 5 — Git y Control de Versiones

[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Anterior: [[Capitulo 04 - Planificacion y Flujo de Trabajo]] · Siguiente → [[Capitulo 06 - Comandos CLI y Superficies]]

> [!warning] Este capítulo incluye un error real, mío, sin pulir
> No es un ejemplo de otro — es algo que hice yo en esta misma sesión y que, al revisarlo para escribir este capítulo, encontré impreciso. Lo dejo tal cual, en la §5.5, porque es mejor evidencia de "cómo evitar errores con Git" que cualquier ejemplo inventado sin errores.

## 5.1 El problema: Git tiene operaciones sin "deshacer" fácil

`git push --force`, `git reset --hard`, `git commit --amend` sobre algo ya publicado — todas son operaciones legítimas, y todas pueden destruir trabajo de otra persona o reescribir historia que ya es pública. El riesgo no es usar Git; es usarlo sin distinguir qué operaciones son reversibles y cuáles no.

## 5.2 Cómo se decide qué hacer sola/o y qué preguntar primero

🔵 *Documentado*: existe un protocolo explícito — nunca hacer force push, nunca omitir hooks o firmas, preferir crear un commit nuevo en vez de enmendar uno existente (salvo que se pida explícitamente lo contrario), no comitear archivos sensibles, y **nunca comitear cambios salvo que el usuario lo pida explícitamente**.

🟢 *Observable en esta sesión, por partida doble*: en el repo de práctica hay ahora mismo **dos cambios reales sin comitear** — el `README.md` (con 3 placeholders pendientes) y la deduplicación de `lineamiento-git-github.md` (hecha por el subagente). Ninguno de los dos se comiteó, y no fue un olvido: es la regla "nunca comitear sin pedirlo explícitamente" aplicada de forma literal, dos veces.

En cambio, sí se ejecutaron sin preguntar: `git fetch`, `git checkout main`, `git pull --ff-only`, `git checkout -b <rama nueva>`. ¿Por qué esas sí y el commit no? Porque son **operaciones de lectura o de bajo riesgo y reversibles** (crear una rama no destruye nada; puede borrarse sin consecuencia), mientras que un commit empieza a construir historia que otras personas podrían ver si se llega a publicar.

## 5.3 El ciclo real de Git que se ejecutó, paso a paso

```
git fetch origin          → traer las referencias remotas más recientes, sin tocar el working directory
git checkout main         → moverse a la rama base, antes de ramificar desde ahí
git pull --ff-only        → asegurar que "main" local == remoto, sin crear un merge commit
git checkout -b docs/...  → crear la rama de trabajo desde una base ya actualizada
```

El orden importa: si se hubiera creado la rama nueva *antes* de actualizar `main`, la rama habría partido de una base potencialmente desactualizada. `--ff-only` en el pull es, además, una elección deliberada — si `main` local tuviera commits propios que el remoto no tiene, el comando falla en vez de crear un merge automático silencioso. Es preferir un error visible a una resolución automática que podría ocultar un conflicto real.

## 5.4 Por qué "todavía no hay ningún commit" es la parte correcta, no un pendiente

Vale la pena decirlo sin rodeos: en este capítulo no hay una demostración de "cómo hacer un commit con Claude Code", porque **deliberadamente no se ha hecho ninguno todavía** en el repo de práctica. Los 3 placeholders del README (Data Steward, Confidencialidad, Criticidad) siguen sin tu confirmación, y comitear antes de eso sería exactamente el error que este capítulo advierte: avanzar sobre una base incompleta porque "ya se puede".

## 5.5 Autocrítica real: un prefijo de rama que no sigue del todo la convención documentada

Al preparar este capítulo, volví a leer con cuidado `lineamiento-git-github.md` (el que ya habíamos explorado en el Capítulo 2) para citar bien su convención de ramas. Encontré algo que no había notado antes:

- **§4.1** del lineamiento (GitHub Flow, el aplicable a este tipo de proyecto) documenta explícitamente solo dos prefijos de rama: **`feat/`** y **`fix/`**.
- **`docs`** aparece en el lineamiento, pero en la **§5.2**, como un *tipo de commit* (`docs: cambios exclusivamente documentales`), no como un prefijo de rama.

Cuando propuse la rama `docs/readme-raiz-y-deduplicacion-lineamientos` en el Capítulo 2, dije que seguía *"la convención feat/fix/docs que ya usa el repo"* — y eso mezcló dos listas que el propio documento mantiene separadas: prefijos de rama (§4.1) y tipos de commit (§5.2). No es un error grave ni que rompa nada técnicamente, pero es una imprecisión real, cometida por mí, al citar una convención que en ese momento no había verificado con el mismo cuidado con el que se verificaron otros datos del mismo capítulo.

🟢 *Por qué importa dejarlo así, sin pulirlo*: es la prueba de que "seguir la documentación de un proyecto" no es infalible solo porque quien lo hace sea Claude Code — y es exactamente el motivo por el cual el propio lineamiento exige **al menos un Code Review por pares antes de cualquier merge** (§8.3 del documento). Ese control humano no es un trámite: es lo que atraparía este tipo de imprecisión antes de que llegara a `main`.

## 5.6 Buenas prácticas de commits: las del repo, no una convención genérica impuesta

Cuando llegue el momento de comitear los cambios pendientes, la convención a seguir no es una genérica inventada — es la que el propio repositorio ya documenta en su lineamiento (Conventional Commits, tipos `feat/fix/refactor/docs/data/ci/chore`, cuerpo explicando el *por qué* no el *qué*). Esto conecta directamente con la filosofía "sin opiniones impuestas" del Capítulo 1 (§1.3): Claude Code no debería traer su propia convención de commits por defecto cuando el proyecto ya tiene una explícita y documentada — debe adoptar la del proyecto.

## Resumen del capítulo

- Git tiene operaciones reversibles (crear una rama, hacer fetch) y otras que no lo son (force push, reset --hard, amend sobre algo publicado) — la regla es actuar libremente en las primeras y pedir confirmación explícita para las segundas.
- Nunca comitear sin que el usuario lo pida explícitamente — en esta sesión hay dos cambios reales sin comitear, deliberadamente, mientras falten datos.
- El orden de los comandos de Git no es arbitrario: actualizar la base antes de ramificar, y preferir que un `pull` falle visiblemente antes que resuelva un conflicto en silencio.
- Seguir la convención de commits/ramas de un proyecto es preferible a imponer una propia — pero seguirla bien requiere verificarla con cuidado, no citarla de memoria.
- Ni Claude Code está exento de cometer imprecisiones al aplicar una convención — por eso el control humano (code review) antes de un merge no es un trámite, es la red de seguridad real.

## Analogía

Un editor de textos que trabaja para una revista no impone su propio manual de estilo sobre el de la revista — usa el de la casa, aunque tenga sus propias preferencias. Pero incluso el editor más cuidadoso se equivoca de vez en cuando citando una norma de memoria en lugar de volver a mirar el manual; por eso ninguna edición sale a imprenta sin que otra persona la revise antes. La revisión no existe porque el editor sea malo en su trabajo — existe porque cualquiera, por bueno que sea, se beneficia de una segunda mirada antes de que algo se vuelva permanente.

## Autoevaluación

1. ¿Qué criterio distingue las operaciones de Git que se ejecutaron sin preguntar de la que sigue pendiente (el commit)?
2. ¿Por qué se usó `git pull --ff-only` en vez de un `pull` normal? ¿Qué se evita con eso?
3. Describe la imprecisión real de la §5.5. ¿En qué dos secciones distintas del lineamiento aparecen `docs` y `feat`/`fix`, y qué se confundió?
4. ¿Por qué el hallazgo de la §5.5 no se corrigió en silencio, sino que se dejó documentado en el capítulo?
5. ¿Cómo conecta "seguir la convención del propio repo, no una impuesta" con la filosofía del Capítulo 1?

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. La reversibilidad y el riesgo: fetch/checkout/pull --ff-only/checkout -b son de bajo riesgo y reversibles; comitear empieza a construir historia que podría publicarse, así que requiere pedido explícito del usuario.
> 2. Para que, si la rama local y la remota hubieran divergido, el comando fallara visiblemente en vez de crear un merge commit automático que podría ocultar un conflicto real sin que nadie lo note.
> 3. `feat/` y `fix/` aparecen en §4.1 como prefijos de rama; `docs` aparece en §5.2 como tipo de commit. Se confundieron ambas listas al describir la nueva rama como si "docs/" siguiera una convención de rama ya establecida, cuando en realidad solo estaba documentada como tipo de commit.
> 4. Porque es evidencia real de que ni Claude Code aplica las convenciones de un proyecto de forma infalible, y eso refuerza por qué el code review humano antes de un merge no es un trámite — es la red de seguridad que atraparía justo este tipo de error.
> 5. El Capítulo 1 (§1.3) describe a Claude Code como una herramienta sin opiniones impuestas, que se integra al flujo de trabajo existente en vez de forzar el suyo; adoptar la convención de commits ya documentada por el repo en vez de traer una genérica es la misma filosofía aplicada a Git específicamente.

---
[[Claude Code - Mapa de Contenidos|← Mapa de Contenidos]] · Siguiente → [[Capitulo 06 - Comandos CLI y Superficies]]
