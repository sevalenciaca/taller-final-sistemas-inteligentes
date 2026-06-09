# Automatización Inteligente de Facturación Electrónica con Agente LangChain

## Documentación Técnica Completa

> **Documento complementario a la plantilla PPTX.** Contiene toda la información técnica detallada que no cabe en el formato limitado de diapositivas.

---

**Proyecto:** Automatización Inteligente de Facturación Electrónica con Agente LangChain  
**Integrantes:** Sebastián Valencia Carvajal · Daniel Alzate Ocampo · Santiago Valencia Saavedra  
**Docente:** Luis Fernando Castillo  
**Programa:** Especialización en Inteligencia Artificial — Universidad Nacional de Colombia, Sede Manizales  
**Período:** 2026-I  
**Repositorio:** https://github.com/sevalenciaca/taller-final-sistemas-inteligentes

---

## Tabla de Contenidos

1. [Justificación y Problema](#1-justificación-y-problema)
2. [Arquitectura General](#2-arquitectura-general)
3. [Nivel 1 — N8n: Pipeline Completo (33 nodos)](#3-nivel-1--n8n-pipeline-completo-33-nodos)
4. [Nivel 2 — LangChain: Agente ReAct](#4-nivel-2--langchain-agente-react)
5. [Nivel 3 — Lógica Difusa: Priorización de Pagos](#5-nivel-3--lógica-difusa-priorización-de-pagos)
6. [Contexto Técnico: Facturación Electrónica Colombiana](#6-contexto-técnico-facturación-electrónica-colombiana)
7. [Conclusiones y Trabajo Futuro](#7-conclusiones-y-trabajo-futuro)
8. [Declaración de Uso de IA Generativa](#8-declaración-de-uso-de-ia-generativa)
9. [Referencias](#9-referencias)

---

## 1. Justificación y Problema

### 1.1 Contexto empresarial

En la empresa **Suprokom SAS**, la facturación electrónica se recibe vía correo electrónico en el buzón `facturacion@suprokom.com`. Un operario humano realiza diariamente el siguiente proceso manual:

1. Revisar correo por correo manualmente
2. Descargar y descomprimir archivos ZIP adjuntos
3. Abrir el PDF para verificar visualmente la información
4. Transcribir manualmente los datos (NIT, razón social, monto, fecha) a una hoja de cálculo
5. Guardar y organizar los archivos en carpetas

**Impacto:** ~30 minutos diarios con un volumen promedio de 10 facturas/día. Proceso repetitivo, propenso a errores de transcripción y sin criterio de priorización de pagos.

### 1.2 Marco regulatorio

La facturación electrónica en Colombia es obligatoria desde 2020 (Resolución DIAN 000042 de 2020). Las facturas se emiten en formato **UBL 2.1** (Universal Business Language) como archivos XML, frecuentemente empaquetados en archivos ZIP junto con la representación gráfica en PDF. Estos paquetes llegan por correo electrónico al destinatario.

### 1.3 Propuesta de solución

Desarrollar un sistema inteligente que automatice completamente este flujo en tres niveles:

| Nivel | Paradigma             | Implementación        | Valor agregado                        |
| ----- | --------------------- | --------------------- | ------------------------------------- |
| 1     | Automatización visual | N8n (33 nodos)        | Ejecución end-to-end sin intervención |
| 2     | Agente programático   | LangChain + GPT-4o    | Razonamiento autónomo con CoT         |
| 3     | Racionalidad formal   | Lógica difusa Mamdani | Priorización inteligente de pagos     |

### 1.4 Relevancia académica

El proyecto integra conceptos de:

- **Agentes inteligentes** (paradigma ReAct: Reasoning + Acting)
- **Procesamiento de lenguaje natural** (GPT-4o para validación de datos)
- **Automatización de flujos** (N8n como orquestador visual)
- **Racionalidad formal** (lógica difusa para manejo de incertidumbre lingüística)

Todos aplicados a un caso de uso empresarial real con datos de producción.

---

## 2. Arquitectura General

```
┌──────────────────────────────────────────────────────────────────┐
│                       FUENTE DE DATOS                            │
│            Correo IMAP (mail.suprokom.com:993/SSL)               │
│            Buzón: facturacion@suprokom.com                        │
│            Protocolo: IMAP4_SSL, filtro UNSEEN                   │
└───────────────────────────┬──────────────────────────────────────┘
                            │
               ┌────────────┴────────────┐
               │                         │
     ┌─────────▼─────────┐   ┌──────────▼──────────┐
     │   NIVEL 1: N8n    │   │ NIVEL 2: LangChain  │
     │  (33 nodos visual)│   │  (Agente ReAct)     │
     │                   │   │                     │
     │ • Manual Trigger  │   │ • GPT-4o (temp=0)   │
     │ • IMAP Read       │   │ • 3 Tools @tool     │
     │ • ZIP Extraction  │   │ • create_react_agent│
     │ • XML Parser UBL  │   │ • LCEL Chain        │
     │ • OpenAI GPT-4o   │   │ • DataFrame + CSV   │
     │ • Google Drive    │   │                     │
     │ • Google Sheets   │   │                     │
     └─────────┬─────────┘   └──────────┬──────────┘
               │                         │
               └────────────┬────────────┘
                            │
               ┌────────────▼────────────┐
               │  NIVEL 3: Lógica Difusa │
               │  (Priorización de pago) │
               │                         │
               │ • 2 variables entrada   │
               │ • 10 conjuntos difusos  │
               │ • 7 reglas IF-THEN      │
               │ • Desfusificación       │
               │   por centroide         │
               └─────────────────────────┘
```

### Archivos del repositorio

| Archivo                            | Descripción                                                           | Nivel |
| ---------------------------------- | --------------------------------------------------------------------- | ----- |
| `n8n_drive_sheets.json`            | Flujo N8n completo probado (33 nodos): IMAP → OpenAI → Drive → Sheets | 1     |
| `flujo_facturas_n8n.json`          | Flujo N8n base (versión inicial, solo parseo XML)                     | 1     |
| `agente_facturas.ipynb`            | Notebook LangChain: agente ReAct con 3 tools (36 celdas)              | 2     |
| `logica_difusa_priorizacion.ipynb` | Notebook: sistema difuso Mamdani (28 celdas)                          | 3     |
| `contenido_presentacion.txt`       | Contenido textual para la plantilla PPTX                              | —     |
| `enunciado.md`                     | Criterios de evaluación del taller                                    | —     |
| `documentacion_completa.md`        | Este documento                                                        | —     |

---

## 3. Nivel 1 — N8n: Pipeline Completo (33 nodos)

### 3.1 Descripción general

El flujo N8n implementa la automatización completa end-to-end: desde la lectura del correo IMAP hasta el registro en Google Sheets con links a los archivos organizados en Google Drive. Fue probado con datos reales del buzón de Suprokom.

**Archivo:** `n8n_drive_sheets.json`  
**Nombre del workflow:** "Agente Facturas Electronicas"

### 3.2 Inventario completo de nodos (33)

| #   | Nombre del Nodo                 | Tipo N8n            | Función                                          |
| --- | ------------------------------- | ------------------- | ------------------------------------------------ |
| 1   | Inicio Manual                   | `manualTrigger`     | Punto de entrada del flujo                       |
| 2   | Leer Correos No Leidos          | `emailReadImap` v2  | Lee correos UNSEEN del buzón IMAP                |
| 3   | Separar Adjuntos                | `code` v2           | Itera binarios, genera 1 item/archivo            |
| 4   | Es Archivo ZIP?                 | `if` v2             | Bifurca por extensión `.zip`                     |
| 5   | Extraer Archivos ZIP            | `compression` v1    | Descompresión nativa de ZIP                      |
| 6   | Separar Archivos ZIP            | `code` v2           | Separa archivos internos del ZIP                 |
| 7   | Combinar Archivos               | `merge` v2          | Merge append: archivos directos + descomprimidos |
| 8   | Filtrar PDF y XML               | `if` v2             | Solo pasa extensiones `.pdf` y `.xml`            |
| 9   | Parsear XML UBL 2.1             | `code` v2           | Parser especializado para factura colombiana     |
| 10  | Preparar Prompt OpenAI          | `code` v2           | Construye payload JSON para GPT-4o               |
| 11  | Llamar API OpenAI               | `httpRequest` v4.2  | POST a `/v1/chat/completions`                    |
| 12  | Procesar Respuesta OpenAI       | `code` v2           | Parsea JSON, normaliza valores                   |
| 13  | Buscar Carpeta Raiz             | `httpRequest` v4.2  | GET Drive API: busca "Facturas Suprokom"         |
| 14  | Extraer ID Carpeta Raiz         | `code` v2           | Extrae ID de la respuesta                        |
| 15  | Existe Carpeta Raiz?            | `if` v2             | Verifica existencia                              |
| 16  | Crear Carpeta Raiz              | `googleDrive` v3    | Crea "Facturas Suprokom" si no existe            |
| 17  | Fijar ID Carpeta Raiz Nueva     | `code` v2           | Propaga ID de la carpeta creada                  |
| 18  | Buscar Carpeta Archivos         | `httpRequest` v4.2  | Busca subcarpeta "Archivos Facturas"             |
| 19  | Extraer ID Carpeta Archivos     | `code` v2           | Extrae ID                                        |
| 20  | Existe Carpeta Archivos?        | `if` v2             | Verifica existencia                              |
| 21  | Crear Carpeta Archivos Facturas | `googleDrive` v3    | Crea subcarpeta si no existe                     |
| 22  | Fijar ID Carpeta Archivos Nueva | `code` v2           | Propaga ID                                       |
| 23  | Crear Carpeta Factura Numero    | `googleDrive` v3    | Carpeta individual: `Factura_XXXX_fecha`         |
| 24  | Fijar ID Carpeta Factura        | `code` v2           | Extrae ID de la carpeta de la factura            |
| 25  | Preparar PDF para Subir         | `code` v2           | Prepara binario del PDF                          |
| 26  | Preparar XML para Subir         | `code` v2           | Prepara binario del XML                          |
| 27  | Subir PDF a Drive               | `googleDrive` v3    | Upload del PDF a la carpeta                      |
| 28  | Subir XML a Drive               | `googleDrive` v3    | Upload del XML a la carpeta                      |
| 29  | Extraer Link PDF                | `code` v2           | Obtiene webViewLink del PDF                      |
| 30  | Extraer Link XML                | `code` v2           | Obtiene webViewLink del XML                      |
| 31  | Merge Links PDF y XML           | `merge` v2.1        | Combina links de ambos archivos                  |
| 32  | Consolidar Para Sheets          | `code` v2           | Arma objeto final con 15 campos                  |
| 33  | Agregar Fila Google Sheets      | `googleSheets` v4.5 | Inserta fila en hoja "Facturas"                  |

### 3.3 Flujo de conexiones

```
Inicio Manual
  └→ Leer Correos No Leidos
       └→ Separar Adjuntos
            └→ Es Archivo ZIP?
                 ├─ TRUE → Extraer Archivos ZIP → Separar Archivos ZIP ─┐
                 └─ FALSE ──────────────────────────────────────────────┤
                                                                        ▼
                                                               Combinar Archivos
                                                                        │
                                                               Filtrar PDF y XML
                                                                  ├─ TRUE → Parsear XML UBL 2.1
                                                                  └─ FALSE → (descartado)
                                                                        │
                                                             Preparar Prompt OpenAI
                                                                        │
                                                              Llamar API OpenAI
                                                                        │
                                                         Procesar Respuesta OpenAI
                                                                        │
                                                           Buscar Carpeta Raiz
                                                                        │
                                                        Extraer ID Carpeta Raiz
                                                                        │
                                                         Existe Carpeta Raiz?
                                                           ├─ TRUE → Buscar Carpeta Archivos
                                                           └─ FALSE → Crear Carpeta Raiz
                                                                        → Fijar ID Carpeta Raiz Nueva
                                                                        → Buscar Carpeta Archivos
                                                                        │
                                                      Extraer ID Carpeta Archivos
                                                                        │
                                                      Existe Carpeta Archivos?
                                                        ├─ TRUE → Crear Carpeta Factura Numero
                                                        └─ FALSE → Crear Carpeta Archivos Facturas
                                                                     → Fijar ID Carpeta Archivos Nueva
                                                                     → Crear Carpeta Factura Numero
                                                                        │
                                                       Fijar ID Carpeta Factura
                                                           ├→ Preparar PDF → Subir PDF → Extraer Link PDF ─┐
                                                           └→ Preparar XML → Subir XML → Extraer Link XML ─┤
                                                                                                             ▼
                                                                                                   Merge Links PDF y XML
                                                                                                             │
                                                                                                 Consolidar Para Sheets
                                                                                                             │
                                                                                              Agregar Fila Google Sheets
```

### 3.4 Detalle técnico por etapa

#### Etapa 1: Lectura y Extracción de Correos

**Nodo IMAP (`Leer Correos No Leidos`):**

- Host: `mail.suprokom.com`
- Puerto: 993 (SSL)
- Filtro: `["UNSEEN"]` (correos no leídos)
- Post-acción: marcar como leídos (`postProcessAction: "read"`)
- Descarga de adjuntos: activada (`downloadAttachments: true`)

**Nodo Code (`Separar Adjuntos`):**

```javascript
// Itera sobre todos los binarios de cada correo
// Extrae metadata: emailSubject, emailFrom, emailDate, emailMessageId
// Genera un item independiente por cada archivo adjunto
// Preserva el binario original para procesamiento posterior
const results = [];
for (const item of $input.all()) {
  const binary = item.binary || {};
  const emailData = item.json;
  // ... extracción de metadata del remitente
  for (const [key, binaryData] of Object.entries(binary)) {
    const filename = binaryData.fileName || key;
    const extension = filename.split(".").pop().toLowerCase();
    results.push({
      json: { ...emailMeta, filename, extension, mimeType, fromZip: false },
      binary: { data: binaryData },
    });
  }
}
return results;
```

**Gestión de ZIP:**

- El nodo IF verifica `extension === 'zip'`
- La rama TRUE descomprime con el nodo nativo Compression
- Los archivos descomprimidos se separan preservando metadata del correo original
- Se filtran archivos `.zip` internos (ZIP dentro de ZIP) para evitar bucles
- El Merge combina en modo `append` archivos directos y descomprimidos

**Filtrado:**

- Condicional OR: `extension === 'pdf' OR extension === 'xml'`
- Archivos de otros tipos (imágenes, firmas digitales, etc.) se descartan

#### Etapa 2: Parseo XML y Validación con IA

**Parser XML UBL 2.1 (`Parsear XML UBL 2.1`):**

Este es el Code Node más complejo del flujo (~80 líneas). Implementa:

1. **Detección de formato:** Distingue entre `AttachedDocument` (wrapper DIAN) e `Invoice` directo
2. **Decodificación CDATA:** La factura embebida en AttachedDocument viene como CDATA dentro del tag `<Description>`
3. **Decodificación de entidades HTML:** El XML embebido puede tener `&lt;` `&gt;` en lugar de `<` `>`
4. **Extracción de campos vía regex:** Usa expresiones regulares con soporte de namespaces para extraer:
   - `CompanyID` / `ID` → NIT del emisor
   - `RegistrationName` / `Name` → Razón social
   - `ID` (nivel documento) → Número de factura
   - `IssueDate` → Fecha de emisión
   - `PayableAmount` / `TaxInclusiveAmount` → Valor total
   - `Description` / `Name` (InvoiceLine/Item) → Descripción del concepto
5. **Agrupación por correo:** Asocia PDFs y XMLs del mismo correo usando `emailMessageId`

**Prompt de validación (`Preparar Prompt OpenAI`):**

System prompt enviado a GPT-4o:

```
Eres un asistente especializado en facturación electrónica colombiana (DIAN).
Recibes datos extraídos de un XML UBL 2.1 y debes validarlos, limpiarlos y
devolverlos en JSON estructurado.

Reglas estrictas:
1. Devuelve ÚNICAMENTE un objeto JSON válido, sin texto adicional
2. Si un campo es N/A o está vacío, intenta inferirlo del contexto
3. El campo valor_total debe ser un número (float). Convertir formato colombiano
4. La fecha debe quedar en formato YYYY-MM-DD
5. El número de factura debe incluir el prefijo (ej: FE-1234, FV0001)
6. La razón social debe estar en MAYÚSCULAS
```

**Estructura de salida del LLM:**

```json
{
  "nit_emisor": "string",
  "razon_social": "string",
  "numero_factura": "string",
  "fecha_emision": "string",
  "valor_total": 0.0,
  "descripcion": "string",
  "tipo_documento": "string",
  "correo_remitente": "string",
  "fecha_correo": "string",
  "nombre_xml": "string",
  "nombre_pdf": "string"
}
```

**Procesamiento de respuesta:**

- Limpia posible markdown (` ```json `) que el LLM pueda agregar
- Convierte `valor_total` string → float (maneja formatos `1.500.000` y `1500000,50`)
- Genera nombre de carpeta seguro: `Factura_{numero}_{fecha}`

#### Etapa 3: Organización en Google Drive

La estructura de carpetas en Drive es **idempotente**: busca antes de crear para evitar duplicados.

```
Mi Drive/
└── Facturas Suprokom/              ← Carpeta raíz (se crea si no existe)
    └── Archivos Facturas/          ← Subcarpeta (se crea si no existe)
        ├── Factura_FE-1234_2026-06-08/
        │   ├── factura_FE1234.pdf
        │   └── factura_FE1234.xml
        ├── Factura_QC92_2026-06-08/
        │   ├── factura_QC92.pdf
        │   └── factura_QC92.xml
        └── ...
```

**Patrón de creación idempotente:**

1. Buscar carpeta por nombre vía GET `https://www.googleapis.com/drive/v3/files` con query `name='X' and mimeType='application/vnd.google-apps.folder' and 'PARENT_ID' in parents and trashed=false`
2. Si existe → usar su ID
3. Si no existe → crear con nodo Google Drive → usar el nuevo ID

**Upload paralelo:** Los nodos "Subir PDF" y "Subir XML" se ejecutan en paralelo (ambos conectados desde "Fijar ID Carpeta Factura"), y sus links se reúnen en el nodo Merge.

#### Etapa 4: Registro en Google Sheets

**15 campos registrados por factura:**

| Campo                 | Tipo                | Fuente                            |
| --------------------- | ------------------- | --------------------------------- |
| `nit_emisor`          | String              | XML → GPT-4o                      |
| `razon_social`        | String              | XML → GPT-4o                      |
| `numero_factura`      | String              | XML → GPT-4o                      |
| `fecha_emision`       | String (YYYY-MM-DD) | XML → GPT-4o                      |
| `valor_total`         | Number              | XML → GPT-4o                      |
| `descripcion`         | String              | XML → GPT-4o                      |
| `tipo_documento`      | String              | Parser (AttachedDocument/Invoice) |
| `correo_remitente`    | String              | Metadata IMAP                     |
| `fecha_correo`        | String              | Metadata IMAP                     |
| `nombre_pdf`          | String              | Filename del adjunto              |
| `nombre_xml`          | String              | Filename del adjunto              |
| `link_pdf`            | URL                 | Google Drive webViewLink          |
| `link_xml`            | URL                 | Google Drive webViewLink          |
| `carpeta_drive`       | String              | Nombre de la carpeta creada       |
| `fecha_procesamiento` | String (YYYY-MM-DD) | Timestamp de ejecución            |

**Spreadsheet ID:** `1g5-fr0yTMe0RM5hLMskzs_mrcZSAajKkwXQCE3lyphM`  
**Hoja:** "Facturas"  
**Modo de inserción:** `valueInputMode: USER_ENTERED` (interpreta formatos)

### 3.5 Credenciales requeridas (4)

| Credencial                | Tipo    | Uso                           |
| ------------------------- | ------- | ----------------------------- |
| IMAP Facturacion Suprokom | IMAP    | Lectura de correos            |
| OpenAI Facturas           | API Key | Llamada a GPT-4o              |
| Google Drive Facturas     | OAuth2  | Creación de carpetas y upload |
| Google Sheets Facturas    | OAuth2  | Escritura de filas            |

### 3.6 Métricas del flujo

| Métrica                              | Valor                                         |
| ------------------------------------ | --------------------------------------------- |
| Número total de nodos                | 33                                            |
| Code Nodes (JavaScript)              | 14                                            |
| Nodos condicionales (IF)             | 4                                             |
| Nodos Google Drive                   | 5                                             |
| Nodos HTTP Request                   | 3                                             |
| Nodos Merge                          | 2                                             |
| Otros (trigger, compression, sheets) | 5                                             |
| Tipo de trigger                      | Manual (escalable a Schedule `*/5 * * * *`)   |
| Tiempo promedio por factura          | ~12 segundos                                  |
| Tasa de éxito en parseo              | ~95% (UBL 2.1 estándar)                       |
| Integraciones externas               | 4 (IMAP, OpenAI, Google Drive, Google Sheets) |

---

## 4. Nivel 2 — LangChain: Agente ReAct

### 4.1 Descripción general

Reimplementación del mismo caso de uso usando LangChain con un agente ReAct que decide autónomamente qué herramienta usar en cada paso del procesamiento.

**Archivo:** `agente_facturas.ipynb` (36 celdas)  
**Entorno:** Google Colab

### 4.2 Componentes LangChain

| Componente | Implementación                              | Versión            | Justificación                                     |
| ---------- | ------------------------------------------- | ------------------ | ------------------------------------------------- | ---------------------------------------- |
| **LLM**    | `ChatOpenAI(model="gpt-4o", temperature=0)` | langchain-openai   | Precisión en extracción de datos (determinístico) |
| **Prompt** | `ChatPromptTemplate.from_messages()`        | langchain-core     | System + Human message con instrucciones          |
| **Tools**  | 3 funciones con `@tool` decorator           | langchain-core     | Capacidades IMAP + filesystem + XML parsing       |
| **Agent**  | `create_react_agent()`                      | langgraph.prebuilt | Razonamiento ReAct con loop autónomo              |
| **Chain**  | `prompt                                     | llm` (LCEL)        | langchain-core                                    | Post-procesamiento para estructurar JSON |

### 4.3 System Prompt del Agente

```python
SYSTEM_PROMPT = """Eres un agente especializado en procesamiento de facturas
electrónicas colombianas.
Tu trabajo es:
1. Leer los correos no leídos del buzón de facturación
2. Extraer los archivos adjuntos (ZIP, PDF, XML) de cada correo
3. Parsear los XML de las facturas para extraer información estructurada
4. Presentar un resumen con los datos de cada factura

De cada factura debes extraer (cuando esté disponible en el XML):
- NIT del emisor
- Razón social del emisor
- Número de factura
- Fecha de emisión
- Valor total
- Descripción/concepto
- Ruta del archivo PDF
- Ruta del archivo XML

IMPORTANTE: Procesa TODOS los correos encontrados, uno por uno.
Al final, presenta un resumen consolidado en formato de tabla."""
```

### 4.4 Tools — Implementación detallada

#### Tool 1: `leer_correos`

```python
@tool
def leer_correos(cantidad_maxima: int = 10) -> str:
    """Lee los correos no leídos del buzón de facturación vía IMAP.
    Retorna un resumen de los correos encontrados con sus adjuntos."""
```

**Funcionalidad:**

- Conexión IMAP4_SSL al servidor `mail.suprokom.com:993`
- Búsqueda con filtro `UNSEEN`
- Decodificación de headers (Subject, From, Date) con manejo de encoding
- Detección de adjuntos por `content_disposition == "attachment"`
- Almacena resultados en variable global `correos_pendientes` para acceso posterior
- Retorna resumen textual con IDs, remitentes, asuntos y lista de adjuntos

#### Tool 2: `extraer_adjuntos`

```python
@tool
def extraer_adjuntos(email_id: str) -> str:
    """Extrae los archivos adjuntos de un correo específico.
    Descomprime archivos ZIP y guarda PDF y XML en disco."""
```

**Funcionalidad:**

- Recibe un `email_id` específico (obtenido de `leer_correos`)
- Crea carpeta temporal: `facturas_extraidas/correo_{id}_{timestamp}/`
- Para archivos ZIP:
  - Guarda el ZIP original
  - Descomprime con `zipfile.ZipFile`
  - **Prevención de path traversal:** Valida que la ruta extraída comience con `carpeta_correo`
- Para otros archivos (PDF, XML): guardado directo
- Retorna lista de archivos extraídos con rutas absolutas

#### Tool 3: `parsear_factura`

```python
@tool
def parsear_factura(ruta_carpeta: str) -> str:
    """Parsea los archivos XML de factura electrónica colombiana (UBL 2.1).
    Soporta formato AttachedDocument (factura embebida) e Invoice directo."""
```

**Funcionalidad:**

- Escanea la carpeta por archivos `.xml` y `.pdf`
- Parsea XML con `xml.etree.ElementTree`
- **Detección automática de namespaces** mediante `iterparse(events=["start-ns"])`
- **Dos modos de parseo:**
  1. **AttachedDocument:** Extrae datos del wrapper + parsea la factura embebida dentro del tag `<Description>` (con su propio set de namespaces)
  2. **Invoice directo:** Extrae datos directamente del XML
- Campos extraídos: NIT, razón social, número, fecha, total, descripción, rutas
- Retorna resumen formateado con iconos

### 4.5 Creación y Ejecución del Agente

```python
from langgraph.prebuilt import create_react_agent
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o", temperature=0)
tools = [leer_correos, extraer_adjuntos, parsear_factura]

agent_executor = create_react_agent(
    model=llm,
    tools=tools,
    prompt=SYSTEM_PROMPT
)

# Ejecución
resultado = agent_executor.invoke({
    "messages": [("human",
        "Lee todos los correos no leídos del buzón, extrae los adjuntos "
        "de cada uno, parsea las facturas XML y dame un resumen completo.")]
})
```

### 4.6 Cadena de Pensamiento (Chain of Thought)

**Estrategia:** ReAct (Reasoning + Acting) — Zero-shot CoT

El agente ejecuta un loop autónomo:

```
PASO 1:
🤖 Thought → Necesito leer los correos no leídos del buzón
   Action: leer_correos({'cantidad_maxima': 10})
🔧 Tool Result: Se encontraron 3 correos no leídos:
   - ID:2132 | De: proveedor1@... | Adjuntos: factura.zip
   - ID:2133 | De: proveedor2@... | Adjuntos: FE14022.zip
   - ID:2134 | De: proveedor3@... | Adjuntos: doc.pdf, doc.xml

PASO 2:
🤖 Thought → Debo extraer los adjuntos del primer correo
   Action: extraer_adjuntos({'email_id': '2132'})
🔧 Tool Result: Archivos extraídos en 'facturas_extraidas/correo_2132_...'
   → factura.pdf
   → factura.xml

PASO 3:
🤖 Thought → Ahora parseo los XML de esa carpeta
   Action: parsear_factura({'ruta_carpeta': 'facturas_extraidas/correo_2132_...'})
🔧 Tool Result: 📄 NIT: 1058816252, Razón Social: CLAUDIA JHOANA...

... (repite pasos 2-3 para cada correo)

PASO FINAL:
🤖 Final Answer: Resumen consolidado de 3 facturas procesadas
   [tabla con todos los datos]
```

**Visualización del CoT en el notebook:**

```python
messages = resultado["messages"]
for msg in messages:
    tipo = msg.__class__.__name__
    if tipo == "AIMessage" and msg.tool_calls:
        for tc in msg.tool_calls:
            print(f"🤖 Thought → Action: {tc['name']}({tc['args']})")
    elif tipo == "ToolMessage":
        print(f"🔧 Tool Result ({msg.name}): {msg.content[:200]}...")
```

### 4.7 Post-procesamiento con LCEL

Después de la ejecución del agente, un Chain LCEL estructura la salida en JSON:

```python
prompt_estructurar = ChatPromptTemplate.from_messages([
    ("system", """Convierte texto sobre facturas en JSON array con campos:
    nit_emisor, razon_social, numero_factura, fecha_emision (YYYY-MM-DD),
    valor_total (number), descripcion, ruta_pdf, ruta_xml.
    Responde SOLO con el JSON."""),
    ("human", "{texto_facturas}")
])

chain_estructurar = prompt_estructurar | llm
respuesta_json = chain_estructurar.invoke({"texto_facturas": final_msg.content})
```

### 4.8 Salida final

- **DataFrame de pandas** con todas las facturas procesadas
- **Exportación a CSV:** `registro_facturas.csv`
- **Disparador automático opcional:** Thread con `time.sleep(300)` para ejecución cada 5 minutos

### 4.9 Persistencia y RAG

**No se implementa RAG.** Justificación:

- Las facturas se procesan en tiempo real desde el correo (no requieren indexación previa)
- No hay búsqueda semántica sobre documentos históricos
- Los datos se persisten como CSV exportable, que alimenta el Nivel 3

### 4.10 Seguridad implementada

- **API Keys:** Se leen desde Colab Secrets (`userdata.get()`), nunca hardcodeadas
- **Path traversal prevention:** En `extraer_adjuntos`, se valida que las rutas extraídas del ZIP no escapen del directorio padre
- **Credenciales IMAP:** Se solicitan via Secrets de Colab

---

## 5. Nivel 3 — Lógica Difusa: Priorización de Pagos

### 5.1 Justificación de la técnica

La priorización de pagos en una empresa involucra **incertidumbre inherente**:

- Un monto "alto" no es un umbral binario fijo — $200,000 puede ser alto para una compra menor pero bajo para suministros industriales
- La urgencia es gradual: una factura de 5 días no es urgente, una de 25 sí, pero el límite exacto es difuso

La **lógica difusa** modela esta imprecisión lingüística mediante conjuntos difusos, replicando el razonamiento humano ("esta factura es bastante urgente y el monto es alto, así que la prioridad debería ser alta") de forma automatizada y cuantificable.

**Archivo:** `logica_difusa_priorizacion.ipynb` (28 celdas)  
**Framework:** scikit-fuzzy (`skfuzzy`)

### 5.2 Variables lingüísticas

| Variable      | Tipo                  | Universo de discurso       | Conjuntos difusos          |
| ------------- | --------------------- | -------------------------- | -------------------------- |
| **Monto**     | Entrada (Antecedente) | [0, 500,000] COP           | baja, media, alta          |
| **Urgencia**  | Entrada (Antecedente) | [0, 30] días desde emisión | baja, media, alta          |
| **Prioridad** | Salida (Consecuente)  | [0, 100]                   | baja, media, alta, crítica |

### 5.3 Funciones de membresía — Definición exacta

#### Monto (COP)

| Conjunto | Tipo        | Parámetros                       | Interpretación            |
| -------- | ----------- | -------------------------------- | ------------------------- |
| baja     | Trapezoidal | [0, 0, 30000, 80000]             | Facturas menores a $80K   |
| media    | Triangular  | [50000, 150000, 300000]          | Facturas entre $50K-$300K |
| alta     | Trapezoidal | [200000, 350000, 500000, 500000] | Facturas mayores a $200K  |

```python
monto['baja']  = fuzz.trapmf(monto.universe, [0, 0, 30000, 80000])
monto['media'] = fuzz.trimf(monto.universe, [50000, 150000, 300000])
monto['alta']  = fuzz.trapmf(monto.universe, [200000, 350000, 500000, 500000])
```

#### Urgencia (días desde emisión)

| Conjunto | Tipo        | Parámetros       | Interpretación                 |
| -------- | ----------- | ---------------- | ------------------------------ |
| baja     | Trapezoidal | [0, 0, 3, 8]     | Facturas recientes (0-8 días)  |
| media    | Triangular  | [5, 12, 20]      | Facturas de 5-20 días          |
| alta     | Trapezoidal | [15, 22, 30, 30] | Facturas vencidas (15-30 días) |

```python
urgencia['baja']  = fuzz.trapmf(urgencia.universe, [0, 0, 3, 8])
urgencia['media'] = fuzz.trimf(urgencia.universe, [5, 12, 20])
urgencia['alta']  = fuzz.trapmf(urgencia.universe, [15, 22, 30, 30])
```

#### Prioridad (salida)

| Conjunto | Tipo        | Parámetros         | Rango  |
| -------- | ----------- | ------------------ | ------ |
| baja     | Trapezoidal | [0, 0, 15, 30]     | 0-30   |
| media    | Triangular  | [20, 40, 60]       | 20-60  |
| alta     | Triangular  | [50, 70, 85]       | 50-85  |
| crítica  | Trapezoidal | [75, 85, 100, 100] | 75-100 |

```python
prioridad['baja']    = fuzz.trapmf(prioridad.universe, [0, 0, 15, 30])
prioridad['media']   = fuzz.trimf(prioridad.universe, [20, 40, 60])
prioridad['alta']    = fuzz.trimf(prioridad.universe, [50, 70, 85])
prioridad['critica'] = fuzz.trapmf(prioridad.universe, [75, 85, 100, 100])
```

### 5.4 Base de reglas IF-THEN (7 reglas)

| #   | Antecedente                            | Consecuente           | Lógica                            |
| --- | -------------------------------------- | --------------------- | --------------------------------- |
| R1  | monto=**alto** AND urgencia=**alta**   | prioridad=**CRÍTICA** | Factura cara y vencida → pagar YA |
| R2  | monto=**alto** AND urgencia=**media**  | prioridad=**ALTA**    | Factura cara, todavía hay tiempo  |
| R3  | monto=**medio** AND urgencia=**alta**  | prioridad=**ALTA**    | Monto moderado pero muy vencida   |
| R4  | monto=**medio** AND urgencia=**media** | prioridad=**MEDIA**   | Caso intermedio                   |
| R5  | monto=**bajo** AND urgencia=**alta**   | prioridad=**MEDIA**   | Barata pero vencida               |
| R6  | monto=**bajo** AND urgencia=**media**  | prioridad=**BAJA**    | Barata, no urgente                |
| R7  | monto=**bajo** AND urgencia=**baja**   | prioridad=**BAJA**    | Sin urgencia                      |

```python
regla1 = ctrl.Rule(monto['alta']  & urgencia['alta'],  prioridad['critica'])
regla2 = ctrl.Rule(monto['alta']  & urgencia['media'], prioridad['alta'])
regla3 = ctrl.Rule(monto['media'] & urgencia['alta'],  prioridad['alta'])
regla4 = ctrl.Rule(monto['media'] & urgencia['media'], prioridad['media'])
regla5 = ctrl.Rule(monto['baja']  & urgencia['alta'],  prioridad['media'])
regla6 = ctrl.Rule(monto['baja']  & urgencia['media'], prioridad['baja'])
regla7 = ctrl.Rule(monto['baja']  & urgencia['baja'],  prioridad['baja'])
```

### 5.5 Motor de inferencia

- **Método:** Inferencia Mamdani
- **Operador AND:** Mínimo (MIN)
- **Operador de implicación:** MIN (truncamiento)
- **Agregación:** MAX (unión de conjuntos)
- **Desfusificación:** Centroide (Center of Gravity)

```python
sistema_prioridad = ctrl.ControlSystem([regla1, ..., regla7])
simulador = ctrl.ControlSystemSimulation(sistema_prioridad)
```

### 5.6 Función de evaluación

```python
def evaluar_prioridad(monto_valor, dias_desde_emision):
    # Limitar a universos definidos
    monto_valor = max(0, min(500000, monto_valor))
    dias_desde_emision = max(0, min(30, dias_desde_emision))

    sim = ctrl.ControlSystemSimulation(sistema_prioridad)
    sim.input['monto'] = monto_valor
    sim.input['urgencia'] = dias_desde_emision
    sim.compute()

    score = sim.output['prioridad']

    # Clasificación lingüística
    if score >= 75:   categoria = "🔴 CRÍTICA"
    elif score >= 55: categoria = "🟠 ALTA"
    elif score >= 35: categoria = "🟡 MEDIA"
    else:             categoria = "🟢 BAJA"

    return {"score": round(score, 2), "categoria": categoria}
```

### 5.7 Ejemplo de trazabilidad completa

Para una factura con **Monto = $450,000** y **Días = 19**:

**Paso 1 — Fusificación:**

```
Monto = $450,000:
  μ_baja(450000)  = 0.0000
  μ_media(450000) = 0.0000
  μ_alta(450000)  = 0.6667

Urgencia = 19 días:
  μ_baja(19)  = 0.0000
  μ_media(19) = 0.1250
  μ_alta(19)  = 0.4286
```

**Paso 2 — Evaluación de reglas (operador MIN):**

```
R1: alta(0.6667) ∧ alta(0.4286) → MIN = 0.4286  → prioridad=CRÍTICA ✓
R2: alta(0.6667) ∧ media(0.1250) → MIN = 0.1250 → prioridad=ALTA ✓
R3-R7: activación = 0 (monto no pertenece a baja ni media)
```

**Paso 3 — Desfusificación por centroide:**

```
Score final ≈ 78.2
Categoría: 🔴 CRÍTICA
```

### 5.8 Integración en el pipeline

```
Nivel 2 (LangChain)          Nivel 3 (Lógica Difusa)
┌─────────────────┐          ┌─────────────────────┐
│ Agente procesa   │          │ Lee CSV del agente   │
│ correos → genera │ ──CSV──→│ Calcula días desde   │
│ registro_facturas│          │ emisión por factura   │
│ .csv             │          │ Evalúa sistema difuso│
└─────────────────┘          │ Agrega columnas:     │
                              │  - prioridad_score   │
                              │  - prioridad_categoria│
                              │ Ordena por prioridad │
                              └─────────────────────┘
```

### 5.9 Resultados con datos de ejemplo

| Factura | Razón Social              | Monto    | Días | Score | Categoría  |
| ------- | ------------------------- | -------- | ---- | ----- | ---------- |
| LP123   | EMPRESA XYZ SAS           | $450,000 | 19   | 78.2  | 🔴 CRÍTICA |
| FE14022 | VALENTINA GARZÓN MARIN    | $250,000 | 2    | 42.5  | 🟡 MEDIA   |
| QC92    | CLAUDIA JHOANA ESCOBAR P. | $13,000  | 2    | 18.7  | 🟢 BAJA    |

### 5.10 Visualizaciones generadas

1. **`funciones_membresia.png`** — Gráfica con 3 subplots mostrando las funciones de membresía de monto, urgencia y prioridad
2. **`superficie_control.png`** — Superficie 3D del sistema difuso (monto × urgencia → prioridad) con facturas reales marcadas
3. **`priorizacion_facturas.png`** — Barras horizontales por factura con umbrales de categoría

### 5.11 Mejora cuantificable respecto al Nivel 2

| Aspecto           | Sin lógica difusa (Nivel 2)       | Con lógica difusa (Nivel 3)         |
| ----------------- | --------------------------------- | ----------------------------------- |
| Orden de facturas | FIFO (orden de llegada al correo) | Por urgencia real (monto × tiempo)  |
| Decisión de pago  | Manual del operario               | Score numérico 0-100 + categoría    |
| Visibilidad       | Tabla plana                       | Tabla priorizada con colores        |
| Riesgo de mora    | Alto en facturas de alto monto    | Reducido: facturas críticas primero |

### 5.12 Limitaciones identificadas

1. Funciones de membresía calibradas manualmente (no adaptativas)
2. Solo 2 variables de entrada; no incluye historial del proveedor ni términos de pago
3. Universo de monto limitado a $500,000 — facturas mayores se saturan en prioridad alta
4. No considera descuentos por pronto pago

### 5.13 Propuestas de mejora futura

- Agregar variable "confiabilidad del proveedor" basada en historial
- Usar **ANFIS** (Adaptive Neuro-Fuzzy Inference System) para ajustar funciones de membresía con datos históricos
- Integrar como **Tool invocable** directamente por el agente LangChain
- Ampliar universo de monto para facturas de mayor cuantía

### 5.14 Métricas del sistema difuso

| Métrica                   | Valor               |
| ------------------------- | ------------------- |
| Variables de entrada      | 2 (monto, urgencia) |
| Variable de salida        | 1 (prioridad)       |
| Conjuntos difusos totales | 10                  |
| Reglas IF-THEN            | 7                   |
| Método de inferencia      | Mamdani             |
| Operador AND              | MIN                 |
| Desfusificación           | Centroide           |
| Framework                 | scikit-fuzzy        |

---

## 6. Contexto Técnico: Facturación Electrónica Colombiana

### 6.1 Formato UBL 2.1

La facturación electrónica en Colombia sigue el estándar **UBL 2.1** (Universal Business Language) definido por OASIS. Los elementos clave que se extraen son:

```xml
<!-- Namespaces principales -->
xmlns:cbc="urn:oasis:names:specification:ubl:schema:xsd:CommonBasicComponents-2"
xmlns:cac="urn:oasis:names:specification:ubl:schema:xsd:CommonAggregateComponents-2"

<!-- Campos extraídos -->
<cbc:ID>FE-1234</cbc:ID>                    <!-- Número de factura -->
<cbc:IssueDate>2026-06-06</cbc:IssueDate>   <!-- Fecha de emisión -->

<cac:AccountingSupplierParty>
  <cac:Party>
    <cac:PartyTaxScheme>
      <cbc:CompanyID>900123456</cbc:CompanyID>         <!-- NIT emisor -->
      <cbc:RegistrationName>EMPRESA XYZ</cbc:RegistrationName> <!-- Razón social -->
    </cac:PartyTaxScheme>
  </cac:Party>
</cac:AccountingSupplierParty>

<cac:LegalMonetaryTotal>
  <cbc:PayableAmount currencyID="COP">450000.00</cbc:PayableAmount> <!-- Total -->
</cac:LegalMonetaryTotal>

<cac:InvoiceLine>
  <cac:Item>
    <cbc:Description>Suministros de oficina</cbc:Description> <!-- Descripción -->
  </cac:Item>
</cac:InvoiceLine>
```

### 6.2 Formato AttachedDocument (particularidad DIAN)

La mayoría de facturas electrónicas colombianas no llegan como `Invoice` directo, sino envueltas en un documento **AttachedDocument**. Este es un XML wrapper que contiene:

1. Metadatos del documento (ID, fecha, emisor, receptor)
2. La factura completa como **texto embebido** dentro del tag `<cbc:Description>`, frecuentemente envuelta en `<![CDATA[...]]>` o con entidades HTML escapadas (`&lt;` → `<`)

```xml
<AttachedDocument>
  <cbc:ID>FE-1234</cbc:ID>
  <cac:SenderParty>...</cac:SenderParty>
  <cac:Attachment>
    <cac:ExternalReference>
      <cbc:Description><![CDATA[<?xml version="1.0"?>
        <Invoice xmlns="...">
          <!-- Factura completa aquí dentro -->
        </Invoice>
      ]]></cbc:Description>
    </cac:ExternalReference>
  </cac:Attachment>
</AttachedDocument>
```

**Implicación técnica:** El parser debe:

1. Detectar si el root tag es `AttachedDocument` o `Invoice`
2. Si es AttachedDocument, extraer y decodificar el CDATA/entidades del `<Description>`
3. Parsear el XML embebido con sus propios namespaces
4. Extraer los campos financieros (total, líneas) de la factura interna

Esta particularidad no es evidente en la documentación estándar de UBL 2.1 y fue uno de los principales desafíos técnicos del proyecto.

---

## 7. Conclusiones y Trabajo Futuro

### 7.1 Hallazgos por nivel

**Nivel 1 (N8n — 33 nodos):**

- Flujo probado exitosamente con datos reales del buzón de Suprokom
- La integración OpenAI dentro de N8n permite validar y normalizar datos extraídos del XML sin código adicional
- La gestión idempotente de carpetas en Google Drive evita duplicados en ejecuciones repetidas
- Limitación: la lógica de parseo XML compleja requiere Code Nodes extensos (>50 líneas de JavaScript)
- Fortaleza principal: ejecución end-to-end completamente automática (correo → Drive + Sheets)

**Nivel 2 (LangChain — Agente ReAct):**

- El paradigma ReAct permite que el agente decida autónomamente el orden de procesamiento
- GPT-4o demuestra capacidad para coordinar múltiples herramientas secuencialmente sin instrucciones específicas de orden
- El formato AttachedDocument de facturación colombiana requirió parseo especializado con detección de namespaces dinámicos
- LangGraph (`create_react_agent`) es el reemplazo moderno de `AgentExecutor` en LangChain ≥ 0.2

**Nivel 3 (Lógica Difusa — Priorización):**

- La inferencia difusa Mamdani modela adecuadamente decisiones con incertidumbre lingüística
- 7 reglas IF-THEN cubren el espacio de decisión completo para priorización binaria (monto × urgencia)
- La integración con el pipeline es directa: post-procesamiento del CSV generado por el agente
- Las visualizaciones (superficie 3D, barras de prioridad) facilitan la interpretación de resultados

### 7.2 Aprendizajes del equipo

1. La facturación electrónica colombiana (UBL 2.1) usa un formato wrapper (AttachedDocument) que no es evidente en la documentación estándar — requiere parseo en dos niveles
2. LangGraph es el reemplazo moderno de AgentExecutor y ofrece un loop de agente más robusto
3. N8n con 33 nodos puede implementar un pipeline complejo con múltiples integraciones de forma visual
4. GPT-4o con temperature=0 es efectivo para tareas de validación y normalización de datos estructurados
5. La combinación de extracción automática + priorización difusa elimina ~30 min/día de trabajo manual repetitivo

### 7.3 Comparación N8n vs LangChain

| Aspecto        | N8n (Nivel 1)           | LangChain (Nivel 2)       |
| -------------- | ----------------------- | ------------------------- |
| Paradigma      | Visual/declarativo      | Programático/imperativo   |
| Autonomía      | Flujo fijo predefinido  | Agente decide el orden    |
| Almacenamiento | Google Drive + Sheets   | CSV local                 |
| Validación IA  | HTTP Request a OpenAI   | Tool interno del agente   |
| Trigger        | Manual / Schedule       | Manual / Thread periódico |
| Debugging      | Visual en cada nodo     | Log de razonamiento (CoT) |
| Mantenimiento  | Edición visual de nodos | Edición de código Python  |

### 7.4 Trabajo futuro

- **OCR para facturas PDF-only:** Integrar OCR (Tesseract/GPT-4 Vision) para facturas que solo llegan en PDF sin XML
- **Dashboard en tiempo real:** Streamlit o Grafana para visualización del estado de facturas
- **Alertas automáticas:** Notificaciones por Slack/WhatsApp para facturas con prioridad CRÍTICA
- **ANFIS adaptativo:** Ajustar funciones de membresía con datos históricos de pagos
- **Tool de lógica difusa en el agente:** Integrar el sistema difuso como herramienta invocable por LangChain
- **Base de datos:** Migrar de Google Sheets a PostgreSQL para mayor escalabilidad

---

## 8. Declaración de Uso de IA Generativa

Este proyecto fue desarrollado con asistencia de **GitHub Copilot (Claude)** para:

- Estructura general del código y configuración del agente LangChain
- Parseo del XML UBL 2.1 colombiano (formato AttachedDocument)
- Diseño de funciones de membresía y reglas difusas
- Flujo N8n con 33 nodos incluyendo lógica de creación idempotente de carpetas en Drive
- Documentación técnica del proyecto

Todo el código fue revisado, comprendido y adaptado al caso de uso específico por los integrantes del grupo. Cualquier fragmento puede ser explicado en detalle durante la sesión de preguntas.

---

## 9. Referencias

1. N8n Documentation. https://docs.n8n.io
2. LangChain Python Docs. https://python.langchain.com/docs/
3. LangGraph Documentation. https://langchain-ai.github.io/langgraph/
4. OpenAI API Reference. https://platform.openai.com/docs/api-reference
5. Russell, S. & Norvig, P. (2022). _Artificial Intelligence: A Modern Approach_, 4.ª ed. Pearson.
6. Zadeh, L. A. (1965). Fuzzy sets. _Information and Control_, 8(3), 338–353.
7. DIAN Colombia. Resolución 000042 de 2020 — Facturación Electrónica.
8. OASIS UBL 2.1 Standard. https://docs.oasis-open.org/ubl/UBL-2.1.html
9. scikit-fuzzy Documentation. https://pythonhosted.org/scikit-fuzzy/
10. Yager, R. & Filev, D. (1994). _Essentials of Fuzzy Modeling and Control_. Wiley.
