# Entregas Trabajo Sistemas Inteligentes Calificado por LFC

**Criterios de evaluación Trabajo Módulo Sistemas Inteligentes -- Luis Fernando Castillo.**

La evaluación se organiza en tres niveles acumulativos sobre un mismo caso de uso elegido por el grupo: automatización visual con N8n, desarrollo programático de agentes con LangChain y aplicación de una estrategia de racionalidad formal.

El único instrumento de entrega es la plantilla PPTX oficial del proyecto, ([Link](https://docs.google.com/presentation/d/1T1l41vweYwT6q7_jTzG24okkU7_nWekU/edit?usp=drive_link&ouid=104644354533170013901&rtpof=true&sd=true)) complementada con los recursos digitales indicados en cada sección (repositorio o Git, videos y JSON del flujo). No existen entregas parciales: el grupo envía todo  antes del 10 de junio.

# Escala de Evaluación

La nota final es la suma de los puntos obtenidos en cada nivel entregado, con techo en 5.0. El Nivel 1 es obligatorio para todos los grupos; los Niveles 2 y 3 son voluntarios y acumulativos sobre el nivel anterior.

| **#** | **Nivel** | **Contenido del Proyecto** | **Rango** | **Máx.** |
| --- | --- | --- | --- | --- |
| 1 | Base | Flujo N8n funcional con documentación, video y JSON | 0.0 – 3.5 | **3.5** |
| 2 | Intermedio | Mismo caso en LangChain: código, arquitectura y video | 3.5 – 4.5 | **4.5** |
| 3 | Avanzado | Técnica de Racionalidad integrada (LD / SE / RNA / ML) | + ≤ 0.5 | **5.0** |

# 3. Instrumento de Evaluación: Plantilla PPTX

La plantilla PowerPoint es el eje central del proyecto. Contiene las  diapositivas estructuradas que el grupo debe diligenciar completamente. Cada cuadro con borde dorado discontinuo es un campo editable; el contenido de instrucción en gris claro debe reemplazarse por el contenido real del proyecto.

| **#** | **Diapositiva** | **Contenido esperado** | **Nivel** |
| --- | --- | --- | --- |
| **1** | **Portada** | Título del proyecto, integrantes, docente y período académico. | **Todos** |
| **2** | **Justificación** | Problema identificado, propuesta de solución y relevancia académica. | **Todos** |
| **3** | **Introducción** | Contexto, objetivos y arquitectura general del sistema. | **Todos** |
| **4** | **N8n — Pipeline** | Descripción del flujo: trigger, procesamiento y salida. Métricas generales. | **Nivel 1 ≤ 3.5** |
| **5** | **N8n — Paso a Paso** | Creación del flujo en 6 pasos documentados con capturas de pantalla. | **Nivel 1 ≤ 3.5** |
| **6** | **N8n — Video + JSON** | Enlace al video de creación/ejecución y bloque JSON exportado del flujo. | **Nivel 1 ≤ 3.5** |
| **7** | **LangChain — Arquitectura** | Componentes: LLM, ChatPromptTemplate, Tools y Chain/Agent. | **Nivel 2 ≤ 4.5** |
| **8** | **LangChain — Código y CoT** | Fragmento de código Python, cadena de pensamiento y persistencia/RAG. | **Nivel 2 ≤ 4.5** |
| **9** | **LangChain — Video** | Enlace al video del sistema LangChain funcionando en tiempo real. | **Nivel 2 ≤ 4.5** |
| **10** | **Técnica Racional — Diseño** | Justificación y diseño de la estrategia elegida (LD/SE/RNA/ML). | **Nivel 3 = 5.0** |
| **11** | **Técnica Racional — Resultados** | Resultados análisis crítico e integración en el pipeline. | **Nivel 3 = 5.0** |
| **12** | **Conclusiones** | Hallazgos por nivel, aprendizajes del equipo y trabajo futuro. | **Todos** |

# Dimensiones Generales de Evaluación

Las siguientes dimensiones son transversales a todos los niveles y se valoran en la presentación oral y en la revisión de la plantilla entregada:

| **Dimensión** | **¿Qué se evalúa?** | **Cómo se evidencia** |
| --- | --- | --- |
| Funcionalidad | El sistema hace lo que declara. Se prueba con datos reales del caso de uso. | video |
| Documentación técnica | Claridad del código, README, comentarios | Repositorio Git o Notebook , PPTX |
| Comprensión conceptual | El grupo entiende cada componente. Se verifica en la Q&A de la presentación. | Video |
| Justificación de decisiones | Por qué se eligió cada herramienta, modelo o técnica. | README + PPTX sección 07 |
| Métricas y evidencia | Se reportan métricas cuantitativas apropiadas para cada nivel. | Notebook / PPTX sección 11 |
| Originalidad | El caso de uso es propio, concreto y aporta valor real. | Presentación + Video |

# Nivel 1 — N8n (diapositivas 04, 05 y 06)

## 5.1 Descripción

N8n es una plataforma de automatización de flujos de trabajo de código abierto que permite orquestar servicios, APIs y modelos de IA mediante nodos visuales. El grupo diseña e implementa un flujo que resuelva un problema real, lo documenta en la plantilla y graba un video de creación y ejecución.

## 5.2 Contenido requerido en la plantilla

### Diapositiva 04 — Descripción del Pipeline

- Nodo trigger: tipo (Webhook, Cron, Manual, etc.) y configuración usada.
- Nodo(s) de procesamiento: lógica aplicada, llamadas a LLM o APIs, condicionales.
- Nodo de salida: canal de entrega del resultado (email, Slack, base de datos, HTTP Response).
- Captura de pantalla del flujo activo con todos los nodos visibles.
- Métricas generales: número de nodos, tipo de trigger, tiempo promedio de ejecución, tasa de éxito.

### Diapositiva 05 — Paso a Paso

- Documentación de los 6 pasos de creación: acceso a N8n, creación del workflow, configuración del trigger, nodos de procesamiento, salida y activación.
- Cada paso debe incluir descripción textual y, opcionalmente, captura de pantalla de la configuración.

### Diapositiva 06 — Video y JSON

- Enlace al video (YouTube o Google Drive) con duración de 3 a 8 minutos, narrado, mostrando creación y ejecución real del flujo.
- Bloque de código con el JSON exportado del flujo (N8n → Menú ⋮ → Download → JSON). El archivo original debe adjuntarse en el correo o si desean en el repositorio Git

hub

- -

# 6. Nivel 2 — LangChain (diapositivas 07, 08 y 09)

## 6.1 Descripción

LangChain es un framework Python para construir aplicaciones con modelos de lenguaje grandes. El grupo reimplementa el mismo caso de uso del Nivel 1 usando LangChain (versión ≥ 0.2), manteniendo o mejorando la funcionalidad, y documenta cada componente en la plantilla.

## 6.2 Contenido requerido en la plantilla

### Diapositiva 07 — Arquitectura y Componentes

- LLM: modelo utilizado (GPT-4o, Claude, Llama, Gemini u otro), temperatura y parámetros relevantes.
- ChatPromptTemplate: estructura del prompt (system message, human template, variables de entrada) con ejemplo de uso en código.
- Tools: herramientas implementadas (búsqueda web, base de datos, API externa, tool personalizada) con justificación de cada una.
- Chain / Agent: tipo de cadena o agente (LLMChain, SequentialChain, AgentExecutor, LCEL) y descripción del flujo de razonamiento.

### Diapositiva 08 — Código, Cadena de Pensamiento y RAG

- Fragmento de código Python representativo (ChatPromptTemplate, construcción de la cadena, invocación).
- Cadena de pensamiento: indicar si se aplica Zero-shot CoT, Few-shot, ReAct, Tree of Thought u otro. Mostrar un ejemplo del log de razonamiento.
- Persistencia y RAG: si aplica, especificar vector store (FAISS, Chroma, Pinecone, etc.), documentos indexados y estrategia de retrieval. Si no aplica, justificar la decisión.

### Diapositiva 09 — Video

- Enlace al video (4 a 6 minutos) mostrando el sistema LangChain en ejecución real con narración de cada componente.
- El video debe mostrar el log de razonamiento o la traza del agente cuando sea visible.

## 6.3 Link Notebook o Repositorio Git requerido

# 7. Nivel 3 — Estrategia de Racionalidad (diapositivas 10 y 11)

## 7.1 Fundamento

Un agente racional actúa para maximizar su medida de desempeño esperada dada la información disponible (Russell & Norvig, 2022). El grupo integra al pipeline LangChain un componente de razonamiento formal que aporte capacidad inferencial, aprendizaje o manejo estructurado de incertidumbre, y lo evidencia con métricas cuantitativas.

## 7.2 Contenido requerido en la plantilla

### Diapositiva 10 — Diseño de la Técnica

- Selección justificada de la estrategia: ¿por qué es la más adecuada para el caso de uso?
- Lógica Difusa: variables lingüísticas, funciones de membresía, mínimo 5 reglas IF-THEN y método de desfusificación.
- Sistema Experto: base de conocimiento (≥ 7 reglas), motor de inferencia y ejemplo de trazabilidad de una conclusión.
- Redes Neuronales: arquitectura (capas, activaciones, función de pérdida), dataset utilizado y framework.
- Machine Learning: algoritmo elegido, features principales, validación cruzada y pipeline de entrenamiento.

### Diapositiva 11 — Resultados y Análisis

- Análisis crítico: ¿cómo se integra la técnica en el pipeline LangChain? ¿Qué mejora cuantificable aporta respecto al Nivel 2? ¿Qué limitaciones se identificaron?

Propuesta de mejora futura para la técnica implementada

# Integridad Académica y Uso de IA

El uso de herramientas de IA generativa (ChatGPT, Claude, Copilot, etc.) está permitido como apoyo al desarrollo. El grupo debe:

- Declarar en el README y en la plantilla PPTX qué partes fueron asistidas por IA generativa y de qué forma.
- Comprender y poder explicar en detalle cualquier fragmento de código entregado durante la sesión de preguntas.
- No presentar como propio código generado íntegramente sin comprensión ni adaptación al caso de uso real.

# Referencias

- N8n Documentation. [https://docs.n8n.io](https://docs.n8n.io/)
- LangChain Python Docs. [https://python.langchain.com/docs/](https://python.langchain.com/docs/)
- Russell, S. & Norvig, P. (2022). Artificial Intelligence: A Modern Approach, 4.ª ed. Pearson.
- Zadeh, L. A. (1965). Fuzzy sets. Information and Control, 8(3), 338–353.
- Jackson, P. (1999). Introduction To Expert Systems, 3.ª ed. Addison Wesley.
- Goodfellow, I., Bengio, Y. & Courville, A. (2016). Deep Learning. MIT Press.
- Géron, A. (2023). Hands-On Machine Learning with Scikit-Learn, Keras & TensorFlow, 3.ª ed. O'Reilly.