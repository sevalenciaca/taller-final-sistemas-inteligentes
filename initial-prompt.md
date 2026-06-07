Actúa como un Desarrollador Senior de n8n con experiencia comprobada en automatización de procesos financieros y Facturación Electrónica de la DIAN (Colombia). Conoces en profundidad el estándar UBL 2.1 que usa la DIAN, el campo CUFE, y la estructura de los nodos de n8n en su versión más reciente.

## OBJETIVO

Genera el código JSON completo, válido e importable de un workflow de n8n que automatice el procesamiento de facturas electrónicas recibidas por correo electrónico vía IMAP. El JSON debe poder importarse directamente en n8n sin errores de sintaxis.

---

## ESPECIFICACIONES TÉCNICAS DEL FLUJO

### Nodo 1 — Trigger: Email Read (IMAP)

- Protocolo: IMAP con TLS habilitado
- Escucha correos nuevos en la bandeja de entrada (o una carpeta específica como "Facturas")
- Descarga: `subject`, `text` (body en texto plano), `html` (body HTML), y el array `attachments` con sus buffers binarios
- Credenciales como placeholders:
  - `IMAP_HOST`: servidor IMAP (ej: imap.gmail.com)
  - `IMAP_PORT`: puerto (993 para TLS)
  - `IMAP_USER`: dirección de correo
  - `IMAP_PASSWORD`: contraseña o App Password
- Polling cada 1 minuto

### Nodo 2 — Filter: Validar que el correo es una factura DIAN

- Usa un nodo **Filter** (no "If") con las siguientes condiciones en modo AND:
  - El `subject` contiene al menos uno de estos términos (case-insensitive): `"factura"`, `"FE-"`, `"DIAN"`, `"documento soporte"`
  - El array `attachments` tiene longitud mayor a 0
- Si no cumple, el flujo termina silenciosamente (sin error)

### Nodo 3 — Code (JavaScript): Procesar adjuntos

- Itera sobre `$json.attachments`
- Lógica:
  - Si el adjunto tiene extensión `.zip`: usa el nodo **Extract From Archive** de n8n (no código manual de unzip) para descomprimir. Del contenido extraído, identifica el archivo con extensión `.xml` y el archivo con extensión `.pdf`. Pasa ambos al siguiente nodo.
  - Si el adjunto es directamente `.xml` o `.pdf`: lo pasa tal cual.
- Output del nodo: un objeto con claves `xmlContent` (string UTF-8 del XML) y `pdfAvailable` (boolean)
- Nota: para convertir el buffer del XML a string usa `Buffer.from(data, 'base64').toString('utf-8')`

### Nodo 4 — Extract From Archive (n8n nativo)

- Conectado después del nodo Code cuando el adjunto es `.zip`
- Extrae todos los archivos del archive
- Los archivos extraídos se identifican por su nombre/extensión en el nodo siguiente

### Nodo 5 — Basic LLM Chain (AI Extraction)

- Modelo: `claude-claude-sonnet-4-20250514` via **Anthropic Chat Model**
- Credencial placeholder: `ANTHROPIC_API_KEY`
- El input al LLM es el contenido XML como texto plano (no base64), inyectado en el prompt
- **System Prompt exacto a usar:**

```
Eres un extractor de datos de facturas electrónicas colombianas en formato UBL 2.1 (estándar DIAN).

Recibirás el contenido XML de una factura electrónica. Extrae ÚNICAMENTE los siguientes campos y devuelve un objeto JSON válido, sin markdown, sin explicaciones, solo el JSON:

{
  "numero_factura": "string — atributo ID del elemento Invoice",
  "fecha_emision": "string ISO 8601 — IssueDate",
  "razon_social_proveedor": "string — AccountingSupplierParty > Party > PartyName > Name",
  "nit_proveedor": "string — AccountingSupplierParty > Party > PartyTaxScheme > CompanyID (sin dígito de verificación)",
  "dv_proveedor": "string — dígito de verificación del NIT",
  "razon_social_cliente": "string — AccountingCustomerParty > Party > PartyName > Name",
  "nit_cliente": "string — AccountingCustomerParty > Party > PartyTaxScheme > CompanyID",
  "subtotal": number,
  "total_iva": number,
  "total_factura": number,
  "moneda": "string — DocumentCurrencyCode",
  "cufe": "string — UUID del elemento cbc:UUID",
  "condicion_pago": "string — PaymentMeans > PaymentMeansCode",
  "forma_pago": "string — 'Contado' o 'Crédito'"
}

Si un campo no existe en el XML, usa null. Nunca inventes datos.
```

### Nodo 6 — Set / JSON Parse

- Parsea la respuesta del LLM (que viene como string) a objeto JSON usando `JSON.parse
