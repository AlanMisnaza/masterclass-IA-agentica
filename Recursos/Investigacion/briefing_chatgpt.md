# Briefing interno: ChatGPT y capacidades de código (investigación julio 2026)

Material de referencia para el Capítulo 7. No mostrar tal cual al usuario.
Nota metodológica del agente: help.openai.com y openai.com/index bloquean WebFetch directo (403); los datos vienen de snippets indexados de WebSearch citando esas mismas páginas oficiales textualmente — tratar con confianza alta pero no como fetch primario 100% verificado.

## Idea central para la guía
ChatGPT (el chat) es fundamentalmente conversacional/propósito general, NO un editor de código agéntico. La capacidad de "leer/editar/ejecutar código a través de un repo completo, de forma autónoma" pertenece a un producto DISTINTO de OpenAI: Codex (ver briefing_comparativas_windsurf_codex.md). Esta distinción es el punto pedagógico clave a transmitir.

## Despliegue y features de código dentro de ChatGPT
- Code Interpreter → renombrado "Advanced Data Analysis" (ADA): ejecuta Python en sandbox aislado sin internet (pandas/NumPy), efímero (se destruye al terminar la conversación o tras inactividad). Incluido para usuarios de pago sin activación separada.
- Canvas: lienzo separado para escritura/código con revisiones iterativas, botón "Execute" para correr Python en el navegador, "Fix bug", "Port to a language". EN RETIRO: ya no disponible en GPT-5.5 Instant/Thinking, sustituido por "code blocks"/"writing blocks" integrados en el chat normal.
- "Work with Apps" (SOLO macOS): la app de escritorio de ChatGPT puede leer y editar código directamente en editores externos reales (VS Code y forks, Xcode, JetBrains) vía Accessibility API de macOS. Genera un diff que el usuario revisa/aplica. Requiere extensión "ChatGPT – Work with Code on macOS" para VS Code. Disponible desde marzo 2025, Plus/Pro/Team, solo macOS. Limitado al editor abierto/visible, NO es navegación autónoma de todo un repo.
- ChatGPT agent (agent mode): navegador visual + navegador de texto + terminal + acceso directo a APIs, tareas multi-paso interrumpibles. Integra lo que antes era "Operator" (navegador autónomo). No es específico de código (también hace research web, formularios, compras).
- Deep Research: modo agéntico de investigación (no código), navega decenas/cientos de fuentes, corre sobre variante de o3.
- Projects: archivos persisten dentro del alcance de un proyecto (contexto, NO ejecución).
- Custom GPTs + Actions: llaman APIs externas vía esquema OpenAPI configurado manualmente — invocación de APIs en lenguaje natural, no edición de código en sí.
- NO existe extensión de IDE nativa del chat ChatGPT — la extensión "openai.chatgpt" en VS Code Marketplace es en realidad la integración de Codex, producto distinto.

## Precios (2026)
Free $0, Go $8/mes (EEUU, con ads), Plus $20/mes, Pro dos tiers $100/mes y $200/mes (el de $100 lanzado 9-abr-2026, posicionado explícitamente contra Claude Max al mismo precio), Business (antes "Team", renombrado ago-2025, bajó $5/mes desde 2-abr-2026), Enterprise a consultar, Edu plan económico para universidades, "for Teachers" gratis para docentes K-12 verificados EEUU hasta jun-2027.
Límites agent mode: Pro ~400 mensajes/mes, Plus/Business ~40/mes (solo cuentan solicitudes iniciadas por el usuario, no aclaraciones intermedias).
Workspace agents (Enterprise/Business): gratis hasta 6-jul-2026, luego precio por créditos.

## MCP
SÍ soporta MCP en la app de chat vía "Connectors", renombrados "Apps" desde 17-dic-2025. Las Apps son wrappers MCP mantenidos por OpenAI (Google Workspace, Dropbox, etc.) con UI interactiva. "Developer mode" (beta): permite a organizaciones construir/publicar conectores MCP propios con capacidades de LECTURA Y ESCRITURA (acciones reales en herramientas de terceros), no solo lectura.

## Modelos (2026)
GPT-5 → GPT-5.1 (Instant/Thinking) → GPT-5.2 → GPT-5.3 Instant → GPT-5.4 (Thinking/Pro/mini/nano) → GPT-5.5 (Instant, default actual; Pro, máximo cómputo). Retirados: GPT-4o/4.1/4.1-mini/o4-mini/GPT-5(Instant/Thinking) desde 13-feb-2026. o3 se retira 26-ago-2026 (sunset de 90 días). Deep Research corre sobre variante de o3.
Acceso por plan: Free limitado con degradación; Plus elige Instant/Medium/High; Pro además Extra High/Pro Standard/Pro Extended.

## Diferenciador y público objetivo
Asistente conversacional de propósito general donde código es una capacidad entre muchas (junto a escritura, research, análisis de datos, imágenes). Patrón dominante: usuario describe/pega código → ChatGPT genera/explica → usuario copia a su propio entorno y ejecuta manualmente. Público: ayuda puntual con snippets, aprendizaje, debugging asistido, análisis exploratorio, automatización ligera vía Actions/Apps — NO desarrolladores que necesitan un agente autónomo operando sobre un repo completo en producción (ese nicho lo cubre Codex, un producto distinto de OpenAI).

## Vacíos declarados (no confirmados por bloqueo 403 de fetch directo)
Límites numéricos exactos de Advanced Data Analysis por plan; detalle verbatim de "Models & Limits" de Business/Enterprise; precio final exacto de Business tras la baja de abril 2026; si "memoria de documentos persistente" es un nombre oficial de feature o descripción informal.
