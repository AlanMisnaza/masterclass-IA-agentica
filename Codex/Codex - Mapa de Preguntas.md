---
tags:
  - codex
  - evidencia
  - preguntas
creado: 2026-07-19
estado: activo
---

# 🗺️ Codex — Mapa de Preguntas

[[Codex - Mapa de Contenidos|← Mapa de Codex]] · [[Codex - Bitacora de Exploracion|← Bitácora]]

Este mapa dirige la exploración. Una pregunta no equivale a una afirmación: se mantiene abierta hasta que la bitácora guarde evidencia directa 🟢, documentación oficial 🔵 o declare con honestidad el límite ⚪.

## Prioridad actual

### CX-P01 — Fronteras de lectura, escritura y ejecución local

- **Por qué primero:** determina qué prácticas son seguras y cuáles requieren autorización antes de modificar el vault.
- **Qué observar:** qué acciones se pueden hacer dentro del workspace, cuáles requieren aprobación y cómo se informa el alcance de cada permiso.
- **Evidencia mínima:** una práctica inocua de lectura, una de escritura deliberada y el registro del mecanismo de aprobación cuando aplique.
- **Conexión probable:** planificación, permisos y flujo; Git.
- **Estado:** parcialmente observada por E-001; sin generalizar todavía.

### CX-P02 — Instrucciones de proyecto y precedencia

- **Por qué primero:** este vault usa `AGENTS.md`; entender qué instrucciones operan evita atribuir reglas del proyecto al producto sin comprobarlo.
- **Qué observar:** cómo se descubre una instrucción local, si hay instrucciones anidadas y qué ocurre ante una contradicción.
- **Evidencia mínima:** un ejemplo real, su archivo fuente y el límite explícito de la observación.
- **Conexión probable:** fundamentos; contexto y memoria.
- **Estado:** abierta.

### CX-P03 — Contexto, continuidad y límites de memoria

- **Por qué primero:** condiciona cómo registrar evidencia entre sesiones y evita confundir un contexto compartido con memoria del producto.
- **Qué observar:** qué información permanece visible dentro de una sesión, qué se recupera al reabrir trabajo y qué queda solo en archivos del vault.
- **Evidencia mínima:** dos momentos o superficies comparables, sin inferir mecanismos internos no públicos.
- **Conexión probable:** contexto y memoria; superficies.
- **Estado:** abierta.

### CX-P04 — Superficies de Codex y sus diferencias observables

- **Por qué importa:** la experiencia de escritorio no debe presentarse como si describiera automáticamente CLI, web o IDE.
- **Qué observar:** tareas disponibles, contexto accesible, artefactos y límites por superficie.
- **Evidencia mínima:** una observación etiquetada por superficie más fuente oficial cuando se haga una afirmación transversal.
- **Conexión probable:** comandos, CLI y superficies; arquitectura conceptual.
- **Estado:** abierta.

### CX-P05 — Ciclo de una modificación segura

- **Por qué importa:** la Masterclass necesita enseñar un flujo real: inspeccionar, cambiar, verificar y entregar sin borrar trabajo ajeno.
- **Qué observar:** cómo Codex presenta el diff, qué verificaciones son proporcionales y cuándo se detiene para pedir decisión humana.
- **Evidencia mínima:** un cambio reversible en el vault con comprobación posterior, solo cuando el usuario lo solicite.
- **Conexión probable:** planificación, permisos y flujo; Git; casos prácticos.
- **Estado:** abierta.

### CX-P06 — Skills, agentes y herramientas disponibles

- **Por qué importa:** hay una configuración local de revisor y habilidades visibles en esta sesión, pero su descubrimiento, activación y alcance no se deben asumir.
- **Qué observar:** condiciones para usar una skill, límites de un agente auxiliar y artefactos que deja cada mecanismo.
- **Evidencia mínima:** una invocación segura con entrada, salida y archivos implicados registrados.
- **Conexión probable:** extensibilidad; contexto y memoria.
- **Estado:** abierta; el revisor local no se usa aún porque referencia una regla inexistente.

### CX-P07 — Git, ramas y entrega de cambios

- **Por qué importa:** el estado actual del repositorio y la presencia de contenido no rastreado exigen distinguir inspección, staging, commit y publicación.
- **Qué observar:** salvaguardas antes de acciones destructivas, creación de ramas y señales de una operación completada.
- **Evidencia mínima:** una sesión real solicitada por el usuario; no se harán operaciones Git de escritura solo para llenar esta pregunta.
- **Conexión probable:** Git y control de versiones; mejores prácticas.
- **Estado:** abierta.

### CX-P08 — De una sesión real a un caso práctico reutilizable

- **Por qué importa:** el futuro capítulo de casos debe extraer decisiones, evidencia y resultados, no transcribir conversaciones.
- **Qué observar:** qué metadatos y artefactos pueden guardarse sin exponer información sensible y permiten reconstruir el aprendizaje.
- **Evidencia mínima:** una bitácora completa y un caso con antes, acción, resultado, límite y lección.
- **Conexión probable:** casos prácticos; mejores prácticas.
- **Estado:** iniciada con la sesión CX-2026-07-19-01.

## Reglas para convertir preguntas en contenido consolidado

Una respuesta puede alimentar un capítulo solo si:

1. La pregunta y su alcance están explícitos.
2. La bitácora enlaza una observación 🟢 o una fuente 🔵; las inferencias se etiquetan 🟡 y las lagunas ⚪.
3. Existe una consecuencia práctica o ejercicio real seguro cuando el tema lo permite.
4. Se conocen las excepciones relevantes para no presentar el resultado como universal.
5. El usuario confirma el alcance del capítulo propuesto.

El umbral orientativo para proponer un capítulo es un conjunto de preguntas conectadas que explique un flujo completo, no completar una casilla por su número. La guía conservará los diez temas espejo, pero podrá investigar y consolidar los temas en otro orden.
