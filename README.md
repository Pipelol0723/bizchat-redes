# BizChat Redes — demo de bot multicanal (Vercel)

Bot de respuestas para redes sociales (WhatsApp · Instagram · Messenger) con IA, en
**modo híbrido** y modelo **BYOK**. Sitio estático (`index.html`) + una función serverless
(`api/demo-chat.ts`). Pensado para desplegar en **Vercel** en minutos.

Demo de portafolio de **Andrés Felipe Céspedes** · [pipelol.dev](https://pipelol.dev)

---

## Cómo funciona (3 capas)

1. **Preguntas comunes → respuesta al instante.** Horarios, menú, reservas, domicilios… se
   resuelven en el navegador (`index.html`), **sin gastar IA**. Es el ~80 % de los mensajes reales.
2. **Preguntas distintas → IA real.** El frontend llama a `POST /api/demo-chat`, que usa el
   Vercel AI SDK con la **API key del cliente** (Claude, OpenAI o Gemini). Guardrails, límite de
   entrada (280) y de salida (`maxTokens`), y rate-limit incluidos.
3. **Sin backend / sin key → respaldo simulado.** Si la función falla o no hay key, el frontend
   responde localmente para que el demo **nunca se rompa**.

> **BYOK — tu llave, tu cuenta.** El costo de los tokens llega directo a la factura del cliente
> en su proveedor (centavos por conversación). Yo cobro el montaje y el mantenimiento.

---

## Estructura

```
bizchat-redes/
├── index.html          # el demo (API_ENDPOINT = "/api/demo-chat")
├── api/
│   └── demo-chat.ts    # función Edge: IA real, multi-proveedor, guardrails, límites
├── package.json        # deps del AI SDK (sin build step — sitio estático + función)
├── .env.example        # variables de entorno (keys van en Vercel, no en el repo)
└── .gitignore
```

---

## Desplegar en Vercel

1. **Importar el repo.** [vercel.com/new](https://vercel.com/new) → *Import Git Repository* →
   elige `Pipelol0723/bizchat-redes`. Framework Preset: **Other** (Vercel detecta solo el sitio
   estático + la carpeta `api/`). No hace falta configurar build.
2. **Agregar tu API key.** Project → **Settings → Environment Variables**, agrega **una**:
   - `ANTHROPIC_API_KEY` (recomendado) · o `OPENAI_API_KEY` · o `GOOGLE_GENERATIVE_AI_API_KEY`
   - opcional: `DEFAULT_PROVIDER` = `anthropic` | `openai` | `google`
3. **Deploy.** Vercel instala dependencias, publica `index.html` en `/` y la función en
   `/api/demo-chat`. Tu demo queda en `https://bizchat-redes.vercel.app` (o tu dominio).

> ¿Sin key todavía? Igual despliega — el demo funciona con el respaldo simulado. Agrega la key
> cuando quieras activar la IA real; Vercel redepliega solo.

### Dominio propio (opcional)
En **Settings → Domains** puedes apuntar un subdominio tuyo, p. ej. `demo.pipelol.dev`.

---

## Desarrollo local (opcional)

```bash
npm install -g vercel     # una sola vez
vercel dev                # corre el sitio + la función en localhost
```

Crea un `.env.local` (copia de `.env.example`) con tu key para probar la IA real en local.

---

## Personalizar para un cliente

- **Negocio:** edita el bloque `SYSTEM` en `api/demo-chat.ts` (nombre, horarios, servicios, tono)
  y las listas `QUICK` / `SIM` dentro de `index.html` (respuestas al instante).
- **Marca/diseño:** el `index.html` usa tokens CSS (`--accent`, canales, etc.) fáciles de ajustar.
- **Límites:** `MAX_INPUT` / `MAX_OUTPUT_TOKENS` en la función; `MAX_INPUT` / `MAX_OUTPUT` en el HTML.

---

## Notas

- El rate-limit de la función es *best-effort* por instancia. Para producción con tráfico real,
  usa **Vercel KV** o **Upstash Redis**.
- Este demo usa el negocio ficticio **Café Noire**. El plan completo del producto (canales,
  precios, roadmap) está en `../yo/bizchat-social/PLAN.md`.
