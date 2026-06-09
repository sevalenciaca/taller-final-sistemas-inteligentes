# Automatización Inteligente de Facturación Electrónica

## Descripción

Sistema inteligente que automatiza la lectura y procesamiento de facturas electrónicas colombianas (UBL 2.1) recibidas por correo electrónico. Implementado en tres niveles: automatización visual con N8n, agente programático con LangChain/GPT-4o, y priorización de pagos con lógica difusa.

## Arquitectura

```
┌────────────────────────────────────────────────────────────────┐
│                 FUENTE: Correo IMAP (993/SSL)                  │
└──────────────────────────┬─────────────────────────────────────┘
                           │
         ┌─────────────────┴─────────────────┐
         │                                   │
┌────────▼────────┐              ┌───────────▼───────────┐
│  NIVEL 1: N8n   │              │ NIVEL 2: LangChain    │
│  (33 nodos)     │              │ (Agente ReAct)        │
│                 │              │                       │
│ IMAP → ZIP →   │              │ GPT-4o + 3 Tools      │
│ XML Parser →   │              │ create_react_agent     │
│ GPT-4o →       │              │ LCEL Chain             │
│ Drive + Sheets │              │                       │
└────────┬────────┘              └───────────┬───────────┘
         │                                   │
         └─────────────────┬─────────────────┘
                           │
              ┌────────────▼────────────┐
              │  NIVEL 3: Lógica Difusa │
              │  Priorización de pagos  │
              └─────────────────────────┘
```

## Estructura del Repositorio

| Archivo                            | Descripción                                                   |
| ---------------------------------- | ------------------------------------------------------------- |
| `n8n_drive_sheets.json`            | Flujo N8n completo (33 nodos): IMAP → OpenAI → Drive → Sheets |
| `flujo_facturas_n8n.json`          | Flujo N8n base (solo parseo XML)                              |
| `agente_facturas.ipynb`            | Notebook LangChain: agente ReAct con 3 tools                  |
| `logica_difusa_priorizacion.ipynb` | Notebook: sistema difuso Mamdani para priorización            |
| `contenido_presentacion.txt`       | Contenido para la plantilla PPTX                              |
| `enunciado.md`                     | Criterios de evaluación del taller                            |

## Nivel 1 — N8n (33 nodos)

### Pipeline completo

1. **Lectura IMAP** → correos no leídos con adjuntos
2. **Extracción** → separación de adjuntos, descompresión ZIP
3. **Filtrado** → solo archivos PDF y XML
4. **Parseo XML UBL 2.1** → extracción de campos de factura colombiana
5. **Validación GPT-4o** → limpieza y normalización de datos vía OpenAI API
6. **Google Drive** → organización en carpetas (Facturas Suprokom/Archivos Facturas/Factura_XXXX)
7. **Google Sheets** → registro de 15 campos por factura con links a Drive

### Integraciones

- IMAP (mail.suprokom.com:993)
- OpenAI API (GPT-4o, temperature=0)
- Google Drive API v3 (OAuth2)
- Google Sheets API (OAuth2)

## Nivel 2 — LangChain (Agente ReAct)

### Componentes

| Componente | Implementación                              | Justificación                        |
| ---------- | ------------------------------------------- | ------------------------------------ | ----------------------- |
| **LLM**    | `ChatOpenAI(model="gpt-4o", temperature=0)` | Precisión en extracción estructurada |
| **Prompt** | `ChatPromptTemplate` (system + human)       | Guía al agente paso a paso           |
| **Tools**  | 3 herramientas `@tool` personalizadas       | Acceso a IMAP y filesystem           |
| **Agent**  | `create_react_agent` (LangGraph)            | Razonamiento autónomo ReAct          |
| **Chain**  | `ChatPromptTemplate                         | llm` (LCEL)                          | Post-procesamiento JSON |

### Tools

- `leer_correos` → Conecta IMAP, busca UNSEEN, retorna lista
- `extraer_adjuntos` → Descarga y descomprime ZIP
- `parsear_factura` → Parseo XML UBL 2.1 colombiano

## Nivel 3 — Lógica Difusa

- **Variables:** Monto ($0–$500k) + Urgencia (0–30 días) → Prioridad (0–100)
- **Reglas:** 7 reglas IF-THEN (Mamdani)
- **Desfusificación:** Centroide
- **Integración:** Post-procesamiento del CSV generado por el agente

## Requisitos

- Python 3.10+
- API Key de OpenAI (GPT-4o)
- Acceso IMAP al buzón de correo
- N8n (Docker o cloud) con credenciales Google OAuth2

## Ejecución

### N8n

1. Importar `n8n_drive_sheets.json` en N8n
2. Configurar credenciales (IMAP, OpenAI, Google Drive, Google Sheets)
3. Ejecutar manualmente o activar Schedule Trigger

### LangChain

1. Abrir `agente_facturas.ipynb` en Google Colab
2. Ejecutar las celdas en orden
3. Ingresar API Key de OpenAI y contraseña del correo cuando se soliciten

### Lógica Difusa

1. Abrir `logica_difusa_priorizacion.ipynb` en Google Colab
2. Ejecutar todas las celdas para ver priorización de facturas de ejemplo

## Uso de IA Generativa

Este proyecto fue desarrollado con asistencia de GitHub Copilot (Claude) para:

- Estructura general del código y configuración del agente
- Parseo del XML UBL 2.1 colombiano (AttachedDocument)
- Diseño de funciones de membresía y reglas difusas
- Flujo N8n con 33 nodos incluyendo lógica de carpetas Drive

Todo el código fue revisado, comprendido y adaptado al caso de uso específico por los integrantes del grupo.
