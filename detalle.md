# Detalle del Proyecto — Automatización de Facturación Electrónica

## Caso de uso

Automatización de la lectura de facturas electrónicas recibidas por correo en la empresa Suprokom SAS. El flujo accede al buzón IMAP, lee correos no leídos, extrae adjuntos (ZIP/PDF/XML), parsea el XML UBL 2.1 colombiano, valida datos con GPT-4o, organiza archivos en Google Drive y registra la información en Google Sheets.

## Problema resuelto

La lectura manual correo por correo: descomprimir ZIP, abrir PDF, transcribir datos (NIT, razón social, monto, fecha) a una hoja de cálculo. ~30 minutos diarios con ~10 facturas/día.

## Implementación — Nivel 1: N8n (33 nodos)

### Flujo completo probado: `n8n_drive_sheets.json`

**Etapas:**

1. **Lectura IMAP** — Correos UNSEEN con adjuntos descargados
2. **Extracción** — Separar adjuntos, descomprimir ZIP, filtrar solo PDF/XML
3. **Parseo XML** — Parser JavaScript especializado para AttachedDocument y Invoice UBL 2.1
4. **Validación IA** — GPT-4o (temperature=0) limpia y normaliza campos
5. **Google Drive** — Estructura de carpetas idempotente: `Facturas Suprokom/Archivos Facturas/Factura_XXXX_fecha/`
6. **Google Sheets** — 15 campos por factura: NIT, razón social, número, fecha, valor, descripción, tipo doc, remitente, fecha correo, nombre PDF/XML, links Drive, carpeta, fecha procesamiento

**Integraciones configuradas:**

- IMAP: `mail.suprokom.com:993` (SSL)
- OpenAI API: GPT-4o via HTTP Request
- Google Drive OAuth2: creación de carpetas + upload de archivos
- Google Sheets OAuth2: append de filas

**Spreadsheet ID:** `1g5-fr0yTMe0RM5hLMskzs_mrcZSAajKkwXQCE3lyphM`

## Implementación — Nivel 2: LangChain

Notebook: `agente_facturas.ipynb`

- Agente ReAct con `create_react_agent` (LangGraph)
- 3 tools: leer_correos, extraer_adjuntos, parsear_factura
- Post-procesamiento con LCEL Chain

## Implementación — Nivel 3: Lógica Difusa

Notebook: `logica_difusa_priorizacion.ipynb`

- Variables: Monto ($0–$500k), Urgencia (0–30 días) → Prioridad (0–100)
- 7 reglas IF-THEN, desfusificación por centroide
- Integración como post-procesamiento del CSV de facturas

## Credenciales necesarias (no incluidas en el repo)

- IMAP: facturacion@suprokom.com
- OpenAI: API Key con acceso a gpt-4o
- Google: OAuth2 con scopes Drive + Sheets
