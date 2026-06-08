# Agente de Facturación Electrónica - LangChain

## Descripción

Sistema inteligente que automatiza la lectura y procesamiento de facturas electrónicas colombianas recibidas por correo electrónico, implementado con LangChain y GPT-4o.

## Arquitectura

```
┌────────────────────────────────────────────────────┐
│            LangChain Agent (ReAct)                 │
│               LLM: GPT-4o                          │
├────────────────────────────────────────────────────┤
│  Tools:                                            │
│  1. leer_correos    → IMAP, correos no leídos     │
│  2. extraer_adjuntos → ZIP → PDF + XML            │
│  3. parsear_factura  → XML → datos estructurados  │
├────────────────────────────────────────────────────┤
│  Output: DataFrame (tabla) + archivos en disco     │
└────────────────────────────────────────────────────┘
```

## Componentes LangChain

| Componente | Implementación                              | Justificación                                  |
| ---------- | ------------------------------------------- | ---------------------------------------------- |
| **LLM**    | `ChatOpenAI(model="gpt-4o", temperature=0)` | Precisión en extracción de datos estructurados |
| **Prompt** | `PromptTemplate` (formato ReAct)            | Guía al agente paso a paso                     |
| **Tools**  | 3 herramientas `@tool` personalizadas       | Acceso a IMAP y filesystem                     |
| **Agent**  | `create_react_agent` + `AgentExecutor`      | Razonamiento autónomo (CoT)                    |
| **Chain**  | `ChatPromptTemplate \| llm` (LCEL)          | Post-procesamiento JSON                        |

## Requisitos

- Python 3.10+
- API Key de OpenAI (GPT-4o)
- Acceso IMAP al buzón de correo

## Ejecución

1. Abrir `agente_facturas.ipynb` en Google Colab
2. Ejecutar las celdas en orden
3. Ingresar la API Key de OpenAI y la contraseña del correo cuando se soliciten
4. El agente procesará automáticamente los correos no leídos

## Disparadores

- **Manual:** Ejecutar la celda "Disparador Manual"
- **Automático:** Descomentar el job periódico (cada 5 minutos)

## Uso de IA Generativa

Este proyecto fue desarrollado con asistencia de GitHub Copilot (Claude) para:

- Estructura general del código
- Definición de herramientas del agente
- Configuración del prompt ReAct

Todo el código fue revisado, comprendido y adaptado al caso de uso específico de facturación electrónica colombiana.
