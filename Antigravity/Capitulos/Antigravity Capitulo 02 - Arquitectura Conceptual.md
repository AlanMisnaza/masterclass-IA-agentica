---
tags:
  - antigravity
  - masterclass
  - capitulo-02
capitulo: 2
estado: completo
creado: 2026-07-09
---

# Capítulo 2 — Arquitectura Conceptual: cómo entiende una base de código

[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 01 - Fundamentos y Filosofia]] · Siguiente → [[Antigravity Capitulo 03 - Contexto y Memoria]]

> [!tip] La premisa de este capítulo
> Entender un proyecto grande es imposible si intentas memorizarlo todo a la vez. En este capítulo veremos cómo Antigravity explora un repositorio de forma focalizada e inteligente utilizando herramientas específicas, y cómo su naturaleza asíncrona le permite mapear bases de código complejas.

## 2.1 Exploración interactiva vs. Carga completa

Al igual que Claude Code (como se vio en su respectiva guía), Antigravity **no "descarga" el repositorio completo** en su memoria de una sola pasada. Esa aproximación sería ineficiente y superaría cualquier ventana de contexto útil.

🔵 Antigravity utiliza herramientas nativas especializadas (`list_dir`, `view_file`, `grep_search`) para explorar de forma interactiva y dirigida por hipótesis. Cuando le pides resolver un bug, el sistema recorre el árbol de directorios paso a paso.

## 2.2 La ventaja de las herramientas nativas

🟢 **Observable en esta sesión:** En la filosofía de Antigravity, el principio es usar siempre la herramienta más específica y segura para la tarea, en lugar de recurrir a trucos de bash. 

Por ejemplo, si le pido buscar dónde se define una función `calculateTotal`:
- Un agente CLI clásico podría ejecutar: `run_command` con `grep -rn "calculateTotal" .`
- Antigravity, sin embargo, tiene una regla estricta documentada en sus instrucciones críticas (que estoy siguiendo en este momento): **usar `grep_search` en lugar de correr comandos bash**. Esto le permite procesar los resultados directamente como objetos JSON estructurados en lugar de depender de parsear texto de consola.
- Si quiero ver un archivo, uso `view_file` en lugar de ejecutar `cat`. Esto me permite pedir líneas específicas de forma estructurada.

Esta diferencia conceptual es clave: Antigravity no "simula ser un humano tecleando comandos", sino que interactúa con el sistema de archivos a través de una API semántica estructurada.

## 2.3 Paralelismo en la exploración (El Enjambre)

La verdadera diferencia en la arquitectura conceptual de Antigravity radica en cómo escala esta exploración en proyectos gigantes.

🟡 *Simplificación pedagógica*: Imagina que tienes un proyecto monolítico con frontend (React), backend (Go) y base de datos (SQL). Un agente lineal explorará el frontend, luego el backend y luego la DB. 
Antigravity, gracias a su arquitectura (que detallaremos en el Capítulo 7), puede instanciar tres subagentes investigadores en paralelo. Uno navega la carpeta `frontend/`, otro `backend/` y otro `db/`. Cada uno reporta su resumen al "Director", quien consolida el entendimiento global en un tercio del tiempo.

## 2.4 Comprendiendo la intención detrás del código

Antigravity aborda el "entendimiento" no solo leyendo el código fuente, sino inspeccionando el estado del entorno:
- Puede revisar ramas de Git locales y worktrees.
- Puede examinar logs de sesiones pasadas en su carpeta `.gemini/antigravity/brain/` para saber qué se intentó antes y falló.
- Utiliza la herramienta `search_web` si encuentra dependencias o APIs de terceros no documentadas localmente.

---

## Resumen del capítulo

- Antigravity no carga el repositorio completo, sino que usa un enfoque interactivo dirigido por hipótesis.
- Prioriza herramientas API estructuradas (`list_dir`, `view_file`, `grep_search`) sobre comandos bash en crudo (`ls`, `cat`, `grep`), garantizando mayor fiabilidad semántica.
- Escala la exploración de grandes bases de código mediante subagentes en paralelo, dividiendo el trabajo de entendimiento.
- Complementa la lectura de código con la exploración de su propio historial y búsquedas web para contextos faltantes.

## Analogía

Si entender una base de código es como cartografiar una ciudad desconocida:
Un agente lineal entra por la puerta principal y recorre calle por calle dibujando el mapa.
Antigravity, en cambio, despliega drones especializados (herramientas estructuradas y subagentes): envía uno al norte, otro al sur, y pide que le envíen de vuelta fotos precisas con coordenadas GPS (respuestas JSON) en lugar de descripciones borrosas por radio.

## Autoevaluación

1. ¿Por qué Antigravity prefiere usar `view_file` o `grep_search` en lugar de ejecutar `cat` o `grep` en la terminal?
2. ¿Cómo soluciona Antigravity el límite de la ventana de contexto al explorar repositorios masivos?
3. ¿Cuál es el beneficio de que las respuestas de las herramientas locales sean objetos estructurados (JSON)?

> [!success]- Ver posibles respuestas (clic para expandir)
> 1. Por seguridad y precisión semántica. Las herramientas nativas están diseñadas para devolver la información formateada y libre de errores de parseo de consola, respetando las instrucciones críticas de su arquitectura.
> 2. Mediante exploración focalizada (descargando solo lo relevante) y la delegación en paralelo: lanzando subagentes que investigan distintas áreas y devuelven solo resúmenes consolidados al agente principal.
> 3. Evita tener que lidiar con problemas de paginación de terminal, caracteres de escape o formatos extraños; el agente principal recibe datos limpios y listos para integrarse a su entendimiento del entorno.

---
[[Antigravity - Mapa de Contenidos|← Mapa de Antigravity]] · Anterior: [[Antigravity Capitulo 01 - Fundamentos y Filosofia]] · Siguiente → [[Antigravity Capitulo 03 - Contexto y Memoria]]
