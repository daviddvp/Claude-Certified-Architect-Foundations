# Guía de estudio — Claude Certified Architect: Foundations (CCA-F)

Basada en el Exam Guide oficial v1.0 (julio 2026), publicado en el Anthropic Partner Academy.

**Datos clave del examen**

| | |
|---|---|
| Código | CCAR-F |
| Preguntas | 60 (opción múltiple y respuesta múltiple) |
| Escenarios | 4 de un banco de 6, elegidos al azar |
| Duración | 120 minutos |
| Nota de corte | 720 sobre una escala de 100–1000 |
| Coste | 125 USD |
| Validez | 12 meses (renovación gratuita a tiempo) |
| Intentos | Máx. 4 en 12 meses; espera de 14 / 30 / 90 días tras cada suspenso |

---

## 1. Dominio 1 — Agentic Architecture & Orchestration (27%)

El bloque de mayor peso. Cubre el ciclo del agente, orquestación multiagente, subagentes, hooks y gestión de sesiones.

**Puntos del temario**
- Ciclo agéntico: envío de peticiones, lectura de `stop_reason` (`tool_use` vs `end_turn`), ejecución de tools, reintroducción de resultados en el historial
- Patrones coordinador-subagentes (hub-and-spoke): el coordinador centraliza comunicación, manejo de errores y enrutamiento
- Tool `Task` para invocar subagentes (requiere incluir `"Task"` en `allowedTools`), `AgentDefinition`, paso explícito de contexto — los subagentes no heredan memoria del padre
- Workflows con enforcement programático (hooks, gates previos) frente a guía solo por prompt, cuando el cumplimiento debe ser determinista
- Hooks del Agent SDK (`PostToolUse`) para normalizar datos heterogéneos e interceptar llamadas que violan reglas de negocio
- Estrategias de descomposición de tareas: cadena de prompts fija vs descomposición dinámica según hallazgos
- Gestión de sesiones: `--resume <nombre>`, `fork_session`, cuándo resumir vs empezar de cero con un resumen estructurado

**Dónde estudiarlo**
- Curso 1.1 *Introduction to Agent Skills*
- Curso 1.4 *Claude Code in Action*
- Documentación oficial — Claude Agent SDK: https://docs.claude.com/en/api/agent-sdk/overview
- Documentación oficial — Agents y tool use en la API: https://platform.claude.com/docs
- Prep Course del Dominio 1

---

## 2. Dominio 2 — Tool Design & MCP Integration (18%)

**Puntos del temario**
- Descripciones de tools como mecanismo principal de selección; descripciones mínimas generan selección poco fiable entre tools similares
- Errores estructurados en MCP: flag `isError`, categorías (transitorio / validación / negocio / permiso), campo `isRetryable`
- Distribución de tools por agente (menos tools por agente = mejor fiabilidad), configuración de `tool_choice` (`auto`, `any`, forzado a una tool concreta)
- Integración de servidores MCP: `.mcp.json` a nivel de proyecto vs `~/.claude.json` a nivel de usuario, expansión de variables de entorno, MCP resources para exponer catálogos de contenido
- Selección correcta de tools nativas: `Grep` para contenido, `Glob` para rutas, `Read`/`Write` para archivos completos, `Edit` para cambios puntuales

**Dónde estudiarlo**
- Curso 1.3 *Introduction to Model Context Protocol*
- Documentación oficial MCP: https://modelcontextprotocol.io
- Documentación oficial — Tool use en la API: https://platform.claude.com/docs
- Documentación oficial Claude Code — herramientas nativas y servidores MCP: https://docs.claude.com
- Prep Course del Dominio 2

---

## 3. Dominio 3 — Claude Code Configuration & Workflows (20%)

**Puntos del temario**
- Jerarquía de `CLAUDE.md` (usuario, proyecto, directorio) y sintaxis `@import` para mantenerlo modular
- `.claude/rules/` con frontmatter YAML y patrones glob para carga condicional según el archivo que se edita
- Comandos slash personalizados (`.claude/commands/`, compartidos vía control de versiones) y skills (`.claude/skills/` con `SKILL.md`; opciones `context: fork`, `allowed-tools`, `argument-hint`)
- Plan mode (cambios arquitectónicos, multiarchivo) vs ejecución directa (cambios simples y acotados)
- Refinamiento iterativo: ejemplos concretos de entrada/salida, TDD guiado por fallos de test, patrón de entrevista
- Integración en CI/CD: flag `-p`/`--print` para modo no interactivo, `--output-format json`, `--json-schema`

**Dónde estudiarlo**
- Curso 1.4 *Claude Code in Action* (curso central de este dominio)
- Curso 1.1 *Introduction to Agent Skills* (para la parte de skills y frontmatter)
- Documentación oficial Claude Code: https://docs.claude.com/en/docs/claude-code
- Prep Course del Dominio 3

---

## 4. Dominio 4 — Prompt Engineering & Structured Output (20%)

**Puntos del temario**
- Criterios explícitos en prompts para reducir falsos positivos (mejor que instrucciones vagas tipo "sé conservador")
- Few-shot prompting: 2-4 ejemplos dirigidos para casos ambiguos, generalización a patrones nuevos
- Salida estructurada con `tool_use` y JSON Schema: elimina errores de sintaxis pero no errores semánticos; campos opcionales/nulos para evitar que el modelo invente datos
- Bucles de validación y reintento con feedback del error concreto; los reintentos no sirven cuando la información simplemente no está en la fuente
- Message Batches API: ahorro del 50%, ventana de hasta 24h sin SLA de latencia garantizado, sin llamadas a tools multi-turno, correlación por `custom_id`
- Arquitecturas de revisión multi-instancia (revisor independiente sin el contexto de razonamiento del generador) y multi-pasada (por archivo + integración cruzada)

**Dónde estudiarlo**
- Curso 1.2 *Building with the Claude API* (curso central de este dominio)
- Documentación oficial — Prompt engineering: https://docs.claude.com/en/docs/build-with-claude/prompt-engineering
- Documentación oficial — Tool use y JSON Schema: https://platform.claude.com/docs
- Documentación oficial — Message Batches API: https://platform.claude.com/docs
- Prep Course del Dominio 4

---

## 5. Dominio 5 — Context Management & Reliability (15%)

**Puntos del temario**
- Efecto "lost in the middle" en contextos largos; extracción de hechos persistentes (fechas, importes, IDs) fuera del historial resumido
- Patrones de escalado: pedir escalado explícito del cliente, vacíos o excepciones de política, incapacidad de progresar; la confianza autoinformada y el sentimiento no son proxies fiables de complejidad
- Propagación de errores en sistemas multiagente: contexto estructurado de fallo (tipo, qué se intentó, resultados parciales) en vez de estados genéricos
- Gestión de contexto en exploración de código grande: scratchpads, subagentes de exploración aislada, `/compact`, recuperación ante caída vía manifiestos de estado
- Revisión humana y calibración de confianza: muestreo estratificado, precisión por tipo de documento y campo
- Procedencia de la información en síntesis multi-fuente: mapas claim-fuente, anotación de conflictos entre fuentes, fechas de publicación para evitar falsas contradicciones

**Dónde estudiarlo**
- Curso 1.4 *Claude Code in Action* (para `/compact`, sesiones largas, exploración de código)
- Curso 1.1 y 1.3 (paso de contexto entre skills/subagentes y MCP)
- Documentación oficial — gestión de contexto: https://docs.claude.com/en/docs/build-with-claude
- Documentación oficial Agent SDK — manejo de errores y estado
- Prep Course del Dominio 5

---

## 6. Resumen: qué curso cubre qué dominio

| Curso | Dominios que refuerza |
|---|---|
| 1.1 Introduction to Agent Skills | 1, 3 |
| 1.2 Building with the Claude API | 4 (principal), 2 |
| 1.3 Introduction to Model Context Protocol | 2 (principal), 1 |
| 1.4 Claude Code in Action | 3 (principal), 1, 5 |
| Prep Courses | Repaso final de los 5, organizado por dominio y peso |

---

## 7. Orden de estudio recomendado

1. Cursos 1.1 → 1.2 → 1.3 → 1.4 del Learning Path, en ese orden
2. Práctica hands-on con los 4 ejercicios guiados del propio Exam Guide: agente con Agent SDK y escalado, configuración completa de Claude Code para un proyecto de equipo, pipeline de extracción estructurada, pipeline de investigación multiagente con propagación de errores
3. Prep Courses, empezando por el Dominio 1 (mayor peso)
4. Repasar las preguntas de práctica del apartado 8 de esta guía
5. Revisar el apéndice del Exam Guide: tecnologías dentro y fuera de alcance (fuera quedan, por ejemplo: fine-tuning, autenticación/facturación de la API, despliegue de infraestructura MCP, computer use, visión, streaming, rate limiting)

---

## 8. Preguntas de práctica oficiales (parafraseadas, con explicación)

El Exam Guide incluye 12 preguntas de muestra repartidas en 5 de los 6 escenarios posibles. Aquí tienes el planteamiento y la lógica de cada respuesta correcta, reformulados — para el enunciado exacto y las opciones literales, consulta el PDF oficial del Exam Guide.

### Escenario: agente de soporte al cliente

**P1 — Verificación de identidad saltada.** Un agente a veces omite la comprobación de identidad del cliente y pasa directamente a buscar el pedido solo con el nombre, lo que puede llevar a reembolsos mal dirigidos.
*Respuesta correcta:* añadir una barrera programática que bloquee las acciones de pedido/reembolso hasta que la verificación de identidad se haya completado. Cuando una secuencia de pasos es crítica para el negocio, un hook o gate determinista da garantías que un simple recordatorio en el prompt no puede dar.

**P2 — Confusión entre dos tools parecidas.** El agente confunde sistemáticamente dos tools con descripciones mínimas y casi idénticas.
*Respuesta correcta:* ampliar las descripciones de cada tool con formatos de entrada, ejemplos y límites de uso frente a la tool similar. Es la corrección de menor esfuerzo y mayor impacto, porque la descripción es el principal mecanismo que usa el modelo para elegir tool.

**P3 — Resolución en primer contacto muy por debajo del objetivo.** El agente escala casos sencillos y en cambio intenta resolver solo casos complejos que requieren excepciones de política.
*Respuesta correcta:* añadir criterios explícitos de escalado al system prompt, con ejemplos few-shot que muestren cuándo escalar y cuándo resolver. La confianza autoinformada por el modelo no es un proxy fiable, así que no sirve como mecanismo de enrutamiento.

### Escenario: generación de código con Claude Code

**P4 — Dónde guardar un comando slash compartido por todo el equipo.** Se quiere un comando `/review` disponible para cualquiera que clone el repositorio.
*Respuesta correcta:* en `.claude/commands/` dentro del propio repositorio (control de versiones), no en la carpeta personal del usuario.

**P5 — Reestructuración de un monolito a microservicios.** Cambios en decenas de archivos con decisiones de arquitectura pendientes.
*Respuesta correcta:* usar plan mode para explorar el código y diseñar el enfoque antes de tocar nada. Es exactamente el tipo de tarea (cambios grandes, decisiones arquitectónicas, múltiples enfoques válidos) para la que existe el plan mode.

**P6 — Convenciones distintas por tipo de archivo, con tests repartidos por todo el proyecto.** Se necesita que Claude aplique automáticamente la convención correcta según el archivo, sin depender de en qué carpeta esté.
*Respuesta correcta:* archivos de reglas en `.claude/rules/` con patrones glob en el frontmatter YAML. Los `CLAUDE.md` por subdirectorio no sirven bien cuando los archivos de un mismo tipo están dispersos por todo el árbol.

### Escenario: sistema de investigación multiagente

**P7 — El informe final cubre solo una parte del tema pedido.** Los subagentes funcionan bien individualmente, pero el coordinador solo generó subtareas para una porción del alcance real.
*Respuesta correcta:* el problema está en la descomposición del coordinador, que fue demasiado estrecha — no en los subagentes, que ejecutaron correctamente lo que se les asignó.

**P8 — Cómo debe fluir un fallo (timeout) de un subagente hacia el coordinador.**
*Respuesta correcta:* devolver contexto de error estructurado (tipo de fallo, qué se intentó, resultados parciales, alternativas posibles), en vez de un estado genérico, resultado vacío marcado como éxito, o abortar todo el flujo.

**P9 — Verificaciones puntuales que generan demasiadas idas y vueltas entre agentes.** El 85% son comprobaciones simples de hechos.
*Respuesta correcta:* dar al agente de síntesis una tool acotada solo para verificaciones simples, manteniendo el patrón de coordinación existente para los casos complejos — principio de mínimo privilegio en lugar de dar acceso total o forzar todo por lotes.

### Escenario: Claude Code en CI/CD

**P10 — El pipeline se queda colgado esperando entrada interactiva.**
*Respuesta correcta:* usar el flag `-p` (`--print`) para ejecutar Claude Code en modo no interactivo — es la forma documentada; las otras opciones (variable de entorno o flag `--batch`) no existen.

**P11 — Migrar a la Message Batches API para ahorrar coste en dos flujos: una comprobación bloqueante antes de mergear, y un informe nocturno.**
*Respuesta correcta:* mover solo el informe nocturno a batch; mantener el chequeo de pre-merge en llamadas en tiempo real, porque batch no garantiza SLA de latencia y no es apto para flujos bloqueantes.

**P12 — Una revisión de 14 archivos a la vez produce resultados inconsistentes y contradictorios entre archivos similares.**
*Respuesta correcta:* dividir en pasadas enfocadas — análisis individual por archivo para problemas locales, más una pasada de integración aparte para el flujo de datos entre archivos. Esto ataca la causa real (dilución de atención al procesar muchos archivos a la vez).

---

## 9. Registro y logística del examen

1. Revisar la página de certificación en el Anthropic Partner Academy
2. Descargar el Exam Guide y leer los Términos de Certificación y la Política de Examen
3. Registrarse y pagar (el precio en checkout refleja el descuento de tu nivel de partner, si aplica)
4. Crear cuenta en Pearson VUE y agendar el examen
5. Elegir proctorización online o centro de examen Pearson
6. Se puede cancelar o reprogramar hasta 24h antes sin perder la cuota

**El día del examen** hace falta identificación oficial vigente con foto que coincida exactamente con el nombre del registro. El examen se entrega mediante Pearson VUE, con normas estrictas: cámara y proctor visibles durante toda la sesión (si es online), escritorio despejado, prohibido comunicarse con otras personas o capturar contenido del examen.

---

## 10. Otras certificaciones en el mismo portal

- Claude Certified Associate – Foundations
- Claude Certified Developer – Foundations
- Claude Certified Architect – Professional (nivel superior al Foundations)
