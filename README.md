# Roco4wd — Sitio web + Roco Assistant (IA)

Sitio web de Roco4wd con un asistente de IA ("Roco Assistant") que asesora a los visitantes y los deriva a un asesor humano por WhatsApp con un resumen automático de su consulta.

## Cómo está armado

```
/
├── index.html        ← el sitio completo (todo el front-end)
├── api/
│   └── chat.js        ← función serverless: habla con Groq, la API key vive solo aquí
├── vercel.json        ← configuración de rutas para Vercel
├── package.json
├── .env.example        ← plantilla de variables de entorno (no subir el .env real)
└── .gitignore
```

**Por qué hace falta el backend:** antes la API key de Groq estaba escrita directamente en el HTML, visible para cualquiera que abriera el inspector del navegador. Ahora el navegador le habla a `/api/chat` (tu propio servidor en Vercel), y es ese servidor el que tiene la key guardada como variable de entorno y habla con Groq. La key nunca llega al navegador del visitante.

## Pasos para subir a GitHub

1. Creá un repositorio nuevo en GitHub (puede ser privado).
2. En tu computadora, dentro de esta carpeta:
   ```bash
   git init
   git add .
   git commit -m "Sitio Roco4wd con Roco Assistant"
   git branch -M main
   git remote add origin https://github.com/TU_USUARIO/TU_REPO.git
   git push -u origin main
   ```
   (Reemplazá la URL por la de tu repo.)

## Pasos para deployar en Vercel

1. Entrá a [vercel.com](https://vercel.com) e iniciá sesión (podés usar tu cuenta de GitHub).
2. Click en **"Add New" → "Project"**.
3. Elegí el repositorio que acabás de subir.
4. Vercel va a detectar automáticamente que es un proyecto estático con funciones serverless — no necesitás cambiar nada en "Build settings".
5. **Antes de hacer Deploy**, abrí la sección **"Environment Variables"** y agregá:
   - **Name:** `GROQ_API_KEY`
   - **Value:** tu API key de Groq (la consigues en [console.groq.com](https://console.groq.com))
6. Click en **Deploy**.
7. En 1-2 minutos vas a tener una URL tipo `roco4wd-web.vercel.app` con el sitio funcionando y el chat conectado de forma segura.

## Probar en local (opcional)

Si querés probarlo en tu computadora antes de subir:

```bash
npm install -g vercel
cp .env.example .env.local
# editá .env.local y poné tu GROQ_API_KEY real
vercel dev
```

Esto levanta el sitio en `http://localhost:3000` con la función `/api/chat` funcionando igual que en producción.

## Dominio propio

Una vez deployado, en Vercel → tu proyecto → **Settings → Domains** podés conectar tu dominio (ej. `roco4wd.com`) siguiendo las instrucciones que te da Vercel (apuntar el DNS).

## Cómo funciona el asistente (Roco Assistant)

- El comportamiento, tono y reglas del asistente están definidos en `api/chat.js`, en la constante `SYSTEM_PROMPT`. Si querés ajustar cómo habla, qué pregunta, o cuándo deriva, modificá ese texto.
- Cuando el asistente reúne **vehículo + servicio/problema** (y nombre si lo dio), genera internamente un bloque oculto que el frontend detecta y convierte en una tarjeta con botón verde de WhatsApp, con un mensaje pre-armado tipo:
  > "Ya analicé tu consulta sobre suspensión en tu Toyota Fortuner 2024. Te conecto con un asesor."
- El número de WhatsApp de destino está en `index.html`, buscá la constante `WA_NUM` (y los demás botones de WhatsApp del sitio, todos usan `584140248764`).

## Modelo usado

Por defecto usa `llama-3.3-70b-versatile` de Groq (rápido y económico). Podés cambiarlo en `api/chat.js` si más adelante querés probar otro modelo disponible en Groq.
