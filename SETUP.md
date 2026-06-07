# Workflow Facturas DIAN — Instrucciones de Configuración

## Arquitectura del Flujo

```
Gmail Trigger → Filtro (regex subject) → Descomprimir ZIP → Preparar Archivos
→ Subir a Google Drive → Combinar Datos → LLM Claude (extracción)
→ Parsear Respuesta → Google Sheets
```

## Requisitos Previos

### En el VPS con Docker

```bash
# Si n8n no tiene los nodos de LangChain instalados (community nodes)
# Asegurarse de que la imagen de n8n sea >= 1.30 (ya incluye @n8n/n8n-nodes-langchain)
docker pull n8nio/n8n:latest
```

---

## Paso 1: Importar el Workflow

1. Abrir n8n → Menu (⋮) → **Import from File**
2. Seleccionar `workflow-facturas-dian.json`
3. El workflow aparecerá inactivo (no se activará hasta configurar credenciales)

---

## Paso 2: Configurar Credenciales

### 2.1 Gmail (Trigger)

1. Click en el nodo "Gmail Trigger" → Credential → Create New → **Gmail OAuth2**
2. Necesitas configurar OAuth2 en [Google Cloud Console](https://console.cloud.google.com/):
   - APIs & Services → Enable: **Gmail API**
   - Credentials → OAuth 2.0 Client ID → tipo **Web Application**
   - Authorized redirect URI: `https://TU_N8N_URL/rest/oauth2-credential/callback`
   - Copiar Client ID y Client Secret en n8n
3. Click "Connect my account" en n8n → autorizar con la cuenta Gmail que recibe facturas
4. El trigger ya está configurado para polling cada minuto con descarga de adjuntos

### 2.2 Anthropic API (Claude)

1. Click en el nodo "Anthropic Claude" → Credential → Create New
2. Pegar tu API Key de [console.anthropic.com](https://console.anthropic.com/)
3. Modelo configurado: `claude-sonnet-4-20250514` (puedes cambiar a `claude-3-haiku-20240307` para pruebas más baratas)

### 2.3 Google Drive

1. Click en "Subir a Google Drive" → Credential → Create New → OAuth2
2. Necesitas crear un proyecto en [Google Cloud Console](https://console.cloud.google.com/):
   - APIs & Services → Enable: Google Drive API
   - Credentials → OAuth 2.0 Client ID → Desktop App
   - Descargar JSON, usar Client ID y Client Secret en n8n
3. **IMPORTANTE**: Reemplazar `GOOGLE_DRIVE_FOLDER_ID` en los parámetros del nodo con el ID real de la carpeta destino
   - El ID es la parte final de la URL de la carpeta: `https://drive.google.com/drive/folders/ESTE_ES_EL_ID`

### 2.4 Google Sheets

1. Click en "Registrar en Google Sheets" → Credential → Create New → OAuth2
2. Mismo proyecto de Google Cloud: habilitar Google Sheets API
3. **IMPORTANTE**: Reemplazar `GOOGLE_SHEET_ID` con el ID de tu hoja de cálculo
4. Crear la hoja con nombre **"Facturas"** y estos headers en la fila 1:

```
fecha_procesamiento | numero_factura | fecha_emision | razon_social_proveedor | nit_proveedor | dv_proveedor | razon_social_cliente | nit_cliente | subtotal | total_iva | total_factura | moneda | cufe | condicion_pago | forma_pago | link_pdf | link_xml | email_origen | fecha_email | referencia
```

---

## Paso 3: Ajustes del Filtro

El filtro actual usa regex `^\d{8,10};.+;.+;\d{2};.+$` que detecta el formato de subject de Siigo/DIAN:

```
901497302;CELIM S.A.S;CFEV96;01;CELIM S.A.S
```

Si tus facturas vienen de otros proveedores (no solo Siigo), ajusta las condiciones del nodo "Filtro Factura DIAN" agregando alternativas OR.

---

## Paso 4: Testing

1. **Test Manual**: Click "Test Workflow" con un correo real ya en la bandeja
2. **Verificar cada nodo** haciendo click en él después de la ejecución para ver input/output
3. **Puntos comunes de fallo**:
   - Compression node: si el binary property no se llama `attachment_0`, ajustar en el nodo
   - Google Drive: permisos OAuth insuficientes (scope: `drive.file`)
   - LLM: XML muy grande (>100K chars) — el Code "Combinar Datos" ya trunca

---

## Notas Críticas / Limitaciones Conocidas

| Issue                          | Detalle                                                                                           | Solución                                                   |
| ------------------------------ | ------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| Binary property name           | Gmail nombra adjuntos `attachment_0`, `attachment_1`, etc.                                        | Verificar en output del trigger y ajustar nodo Compression |
| ZIP con estructura de carpetas | Si el ZIP tiene subdirectorios, el Code "Preparar Archivos" busca por extensión sin importar path | Debería funcionar pero verificar                           |
| Multiple facturas en 1 email   | El flujo actual asume 1 ZIP = 1 factura                                                           | Si hay múltiples, necesitaremos Loop                       |
| Rate limit Anthropic           | Si llegan muchas facturas juntas, posible throttling                                              | Considerar agregar Wait node o batch processing            |
| XML encoding                   | Algunas facturas DIAN usan encoding diferente a UTF-8                                             | Si se ve texto corrupto, ajustar el Code node              |

---

## Próximos Pasos (Iteración)

1. **Probar** con el correo real del repo (`901497302;CELIM S.A.S;CFEV96;01;CELIM S.A.S.eml`)
2. Verificar que la decompresión del ZIP funciona correctamente
3. Validar que Claude extrae todos los campos del XML UBL 2.1
4. Ajustar mapeo de columnas si Google Sheets no los recibe bien
5. Una vez estable → Activar el trigger para polling automático

---

## System Prompt del LLM (referencia)

El prompt está embebido en el nodo "Extraer Datos Factura". Extrae estos campos:

```json
{
  "numero_factura": "ID del Invoice",
  "fecha_emision": "IssueDate (ISO 8601)",
  "razon_social_proveedor": "AccountingSupplierParty > PartyName > Name",
  "nit_proveedor": "CompanyID sin DV",
  "dv_proveedor": "dígito verificación",
  "razon_social_cliente": "AccountingCustomerParty > PartyName > Name",
  "nit_cliente": "CompanyID cliente",
  "subtotal": "number",
  "total_iva": "number",
  "total_factura": "number",
  "moneda": "DocumentCurrencyCode",
  "cufe": "cbc:UUID",
  "condicion_pago": "PaymentMeansCode",
  "forma_pago": "Contado o Crédito"
}
```

El system prompt completo se inyecta como prefijo al contenido XML en el nodo LLM Chain.
