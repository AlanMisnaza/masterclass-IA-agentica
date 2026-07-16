---
tags:
  - antigravity
  - masterclass
  - capitulo-06
capitulo: 6
estado: completo
creado: 2026-07-09
---

# Capítulo 6 — Comandos, CLI y Superficies

[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 05 - Git y Control de Versiones]] · Siguiente → [[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes]]

> [!tip] La premisa de este capítulo
> Una de las grandes diferencias entre una herramienta (como una CLI estricta) y una "Plataforma de Agentes" es cómo interactúas con ella. Antigravity ofrece múltiples superficies acopladas y el uso de **Slash Commands** para desencadenar flujos de trabajo asíncronos complejos directamente desde la interfaz.

## 6.1 Superficies Desacopladas

🔵 Mientras que Claude Code nació estrictamente en la terminal (CLI), Antigravity (en su versión 2.0) se concibió como un **Centro de Mando de Agentes** que expone varias interfaces interconectadas pero independientes:
- **App de escritorio (Desktop App)**: La interfaz principal que permite visualización de artefactos (como Markdown vivos y diagramas Mermaid), previsualización de imágenes generadas (`generate_image`) y un chat persistente por sesión.
- **CLI**: Para ejecución rápida en servidores o por SSH.
- **IDE nativo (o extensiones)**: Para interacción directamente sobre el árbol de archivos.
- **SDK Programático**: Para que el usuario humano instancie agentes con código Python.

Esta variedad de superficies permite a Antigravity desplegar widgets interactivos, modales de permisos (`ask_permission`), y cuadros de diálogo (como preguntas de opción múltiple vía la herramienta `ask_question`) que serían toscos en una consola de texto puro.

## 6.2 Los Slash Commands (`/`)

En lugar de requerir que escribas largos prompts para activar modos específicos de trabajo, la interfaz principal de Antigravity expone los **Slash Commands**. Estos son atajos que desencadenan comportamientos ultra especializados del agente. 

🟢 **Observable en esta sesión:** De hecho, mientras escribo esta guía, estoy verificando en mi contexto interno de sistema la lista exacta de *Slash Commands* que puedo recomendarte ahora mismo. Son los siguientes:

1. `/goal`: Activa un bucle de ejecución autónoma de larga duración. Si me mandas este comando (como de hecho lo hiciste para desencadenar la redacción autónoma de estos capítulos), yo entro en un estado donde el sistema *fuerza* mi ejecución continua y me prohíbe detenerme a esperar tu entrada hasta que el objetivo se haya completado al 100%. Ideal para tareas que dejas corriendo toda la noche.
2. `/schedule`: Interfaz directa para programar alarmas recurrentes o temporizadores ("one-shot timers") sin tener que explicármelo en lenguaje natural.
3. `/browser`: Especializa mi enfoque inmediatamente hacia tareas que implican navegación web, automatización de páginas y búsquedas en internet.
4. `/grill-me`: Desencadena un flujo de entrevista interactiva. Si no tienes claro cómo diseñar un sistema, este comando me ordena empezar a hacerte preguntas desafiantes para alinear decisiones de diseño antes de escribir una sola línea de código.

## 6.3 La diferencia con las "Skills" manuales

A diferencia de las invocaciones manuales de skills locales (`/nombre-de-skill`) que simplemente inyectan un archivo de texto como contexto, los Slash Commands nativos de Antigravity reconfiguran el **estado de ejecución** del motor. 
Por ejemplo, `/goal` altera mis hooks de terminación del sistema (el *stop hook* del sandbox), impidiéndome físicamente pausar mi trabajo por inactividad.

---

## Resumen del capítulo

- Antigravity opera como una plataforma multi-superficie (Desktop App, CLI, SDK, IDE), permitiendo interacciones gráficas ricas (artefactos, modales, generación de imágenes).
- Utiliza **Slash Commands** para desencadenar modos de ejecución especializados.
- Comandos como `/goal` modifican profundamente el comportamiento del agente, permitiendo bucles de trabajo autónomos sin necesidad de confirmación paso a paso.
- Herramientas como `/grill-me` demuestran el rol de "co-piloto consultor", priorizando la alineación humana interactiva antes de la ejecución a ciegas.

## Analogía

Si manejar un coche por CLI es usar una palanca de cambios manual estricta (tú decides cada marcha en cada momento), la interfaz de Antigravity es un coche con botones de "Modos de Conducción" (Slash Commands). Puedes presionar `/goal` (piloto automático de larga distancia en autopista), `/browser` (modo todoterreno para buscar información), o `/grill-me` (el coche te pregunta a ti cuál es la mejor ruta basándose en el clima antes de arrancar).

## Autoevaluación

1. ¿Qué diferencia a los Slash Commands nativos de Antigravity (como `/goal`) de una simple macro de texto?
2. ¿Por qué una herramienta como `/grill-me` es especialmente útil en una plataforma agéntica?
3. ¿Cómo aprovecha Antigravity su superficie de "App de Escritorio" en contraste con una terminal tradicional (CLI)?

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. Los Slash Commands nativos alteran el **estado del motor y las reglas de ejecución** del agente (por ejemplo, impidiendo que el agente se detenga o inyectándole herramientas específicas de navegador), no son simplemente alias de texto para copiar y pegar instrucciones.
> 2. Porque evita que el agente tome decisiones arquitectónicas equivocadas de manera autónoma. Transforma la interacción de un modelo de "ejecución a ciegas" a un modelo de "entrevista de alineación", forzando al usuario a resolver la ambigüedad antes de empezar.
> 3. Al no estar limitado a texto plano, la App de Escritorio permite renderizar **artefactos vivos** (documentos Markdown reales, tablas, diagramas Mermaid), desplegar modales interactivos para solicitar permisos críticos (`ask_permission`) y previsualizar componentes de UI o imágenes generadas en caliente (`generate_image`).

---
[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 05 - Git y Control de Versiones]] · Siguiente → [[Antigravity Capitulo 07 - Extensibilidad MCP y Subagentes]]
