# Portafolio Lady Pino

Sitio web personal de Lady Pino — Product Designer & UX Specialist. Construido con [Astro 4](https://astro.build) + CSS puro + [EmailJS](https://www.emailjs.com) para el formulario de contacto.

## 🚀 Estructura

```
src/
├── components/        # Navbar, Footer, CTASection, LogoLady
├── layouts/           # BaseLayout, CaseStudyLayout
├── pages/             # Rutas:
│   ├── index.astro    # Home con hero + 3 proyectos
│   ├── sobre-mi.astro # Bio + formulario de contacto
│   └── proyectos/     # Case studies: Santander, MACH, Comida para Todos
└── styles/global.css
public/assets/         # Imágenes, logos, mockups
```

## 🧞 Comandos

| Comando            | Acción                                             |
| :----------------- | :------------------------------------------------- |
| `npm install`      | Instala dependencias                               |
| `npm run dev`      | Dev server en `localhost:4321`                     |
| `npm run build`    | Build de producción en `./dist/`                   |
| `npm run preview`  | Preview del build local                            |

## 🔐 Variables de entorno (EmailJS)

El formulario de contacto en `/sobre-mi` usa [EmailJS](https://www.emailjs.com) (200 emails/mes gratis). Para que funcione:

1. Crear cuenta en EmailJS
2. Conectar un Email Service (Gmail / Outlook)
3. Crear un Email Template con variables `{{from_name}}`, `{{from_email}}`, `{{message}}`
4. Copiar las 3 claves al archivo `.env` (ver `.env.example` como plantilla):

```bash
PUBLIC_EMAILJS_PUBLIC_KEY=tu_public_key
PUBLIC_EMAILJS_SERVICE_ID=service_xxxxxxx
PUBLIC_EMAILJS_TEMPLATE_ID=template_xxxxxxx
```

> ⚠️ El archivo `.env` está en `.gitignore` — nunca subir credenciales al repo.

## 🌐 Deploy en Netlify

El repo incluye `netlify.toml` con la configuración del build. Pasos para deployar:

1. **Conectar repo en Netlify** → New site from Git → seleccionar este repo
2. Build settings se detectan automáticamente desde `netlify.toml`
3. **Configurar variables de entorno** en Site settings → Environment variables:
   - `PUBLIC_EMAILJS_PUBLIC_KEY`
   - `PUBLIC_EMAILJS_SERVICE_ID`
   - `PUBLIC_EMAILJS_TEMPLATE_ID`
4. Deploy → el sitio queda en `https://<site-name>.netlify.app`

## 🎨 Stack

- **Framework:** Astro 4 (static site generator, sin runtime JS por default)
- **Estilos:** CSS puro + Work Sans + Nunito (Google Fonts)
- **Animaciones:** IntersectionObserver para reveals + transitions CSS para hovers
- **Form:** EmailJS (sin backend)
- **Hosting:** Netlify (build gratuito + CDN global)

## 📄 Licencia

Proyecto privado. Todos los derechos reservados — Lady Pino © 2026.
