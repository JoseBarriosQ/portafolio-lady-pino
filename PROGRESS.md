# PROGRESS.md — Portafolio-Lady

Historial de cambios del proyecto. Barry actualiza este archivo después de cada cambio significativo.

---

## Fase 1 — Setup base + 5 páginas (✅ COMPLETADA)

#### 🟢 Cambio 40 — Hero Comida rediseñado completo según Figma (texto + highlights HTML + card flotante)
- **Archivos:** `src/layouts/CaseStudyLayout.astro`, `src/pages/proyectos/comida-para-todos.astro`
- **Qué se hizo:**
  - **CaseStudyLayout — 4 props nuevas opcionales** (retrocompat con MACH/Santander):
    - `titleHTML` — string HTML inyectado vía `<Fragment set:html={...} />` para títulos con múltiples highlights inline (alternativa al patrón `title` + `titleHighlight` que solo soporta 1 highlight al final)
    - `durationLabel` (default "DURACIÓN") — permite cambiar el label del segundo info-item (ej. "TIPO" en Comida)
    - `floatingCardLabel` + `floatingCardValue` — renderizan card flotante absoluta en `.case-hero__image` (bottom 24 right 0, bg blanco, border 1px rgba(0,0,0,0.06), box-shadow, padding 14×20). Color del valor toma `var(--bright-color, #F26B3D)`
    - **Bug solucionado:** los `<em class="case-hero__title-highlight">` inyectados por `set:html` no recibían los estilos scoped de Astro (las marcas `astro-XXX` solo se aplican a elementos del JSX, no a contenido inyectado). Solución: regla `.case-hero :global(.case-hero__title-highlight) { ... }` — el `:global()` desactiva el scoping para el selector hijo, manteniendo el padre `.case-hero` scoped
  - **comida-para-todos.astro — props actualizadas a Figma:**
    - `client="Comida para todos Fundación / ONG"` (antes "Comida para todas | ONGs")
    - `titleHTML='Diseño de Plataforma digital para <em class="case-hero__title-highlight">acceso</em> a <em class="case-hero__title-highlight">ayuda alimentaria</em>'` (2 highlights inline, antes title flat)
    - `description="Una experiencia digital enfocada en facilitar el acceso a ayuda alimentaria de forma clara, accesible y humana."`
    - `role="UX-UI Designer"` (antes "UX Designer")
    - `durationLabel="TIPO"` (antes default "DURACIÓN")
    - `focus="Accesibilidad / UX"` (antes tags array)
    - `floatingCardLabel="Usuarios atendidos"` + `floatingCardValue="+2.400 familias"`
    - `highlightColor="#F26B3D"` + `brightColor="#F26B3D"` (naranja Figma — antes default rojo)
    - `heroBackground="linear-gradient(180deg, #FFE0B4 87.93%, #FFFFFF 99.97%)"` (gradient invertido respecto a las specs Figma textuales — amarillo predomina ~88% del hero, franja blanca solo en el bottom donde "descansan" los teléfonos. Stops `87.93% / 99.97%` = inverso aritmético de los Figma `0.03% / 12.07%`)
- **Por qué:** José pasó comparativa Figma vs construido y pidió "déjalo como el Figma" — 7 diferencias detectadas y aplicadas en una pasada
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect hero: bg gradient correcto · ✅ inspect highlight em: `rgb(242,107,61)` italic 700 (naranja activo) · ✅ inspect floating-card: position absolute top 24 right 0 bg blanco shadow OK · ✅ inspect btn: `rgb(242,107,61)` naranja · ✅ texto Figma renderizado: client, description, ROL UX-UI Designer, TIPO Impacto Social, ENFOQUE Accesibilidad / UX · ✅ MACH y Santander no afectados (props nuevas opcionales)
- **Lección:** `set:html` en Astro NO recibe scoped CSS — usar `:global()` con padre scoped para mantener especificidad sin filtrar globalmente
- **Lección iterativa gradient:** las specs Figma textuales `0.03% / 12.07%` interpretadas literalmente daban una franja amarilla en el TOP y blanco predominante. José aclaró visualmente "solo la parte de abajo es blanca, el gradiente se usa al revés" → invertir stops a `87.93% / 99.97%` (amarillo dominante + blanco solo bottom). La confusión nació de leer las specs sin ver el render Figma completo — las stops Figma a veces describen el complemento (qué queda blanco) en lugar de qué queda con color
- **Iteración layout final (con Figma de referencia):**
  - Card flotante movida de `bottom: 24px; right: 0` → `top: 50%; right: -16px; transform: translateY(-50%)` (centro-derecha vertical, sobresale 16px del image container) — match Figma donde la card está a la altura del medio del phone derecho
  - Quitado el recorte agresivo `max-height: 520px` + `object-fit: cover` (los teléfonos completos según Figma)
  - Reemplazado por `align-items: start` en `.case-hero__inner` para que el texto se alinee arriba del hero en vez de centrado vertical (el "no desplazar el texto" se logra con alignment, no con cropping de imagen)
  - **Desborde flotante:** `.case-hero__image-main { transform: translateY(160px); position: relative; z-index: 2 }` → la imagen de teléfonos se desplaza visualmente 160px hacia abajo SIN cambiar el flow (transform no afecta layout). Resultado: invade exactamente 60px de la siguiente sección (`.case-section`) creando el efecto de phones flotantes sobre el corte hero/sección. Hero mantiene altura original 921px sin desplazar nada del texto del lado izquierdo. Z-index 2 asegura que los teléfonos queden encima del bg de la siguiente sección
- **Estado:** `[x] completado`

#### 🟢 Cambio 39 — Hero Comida con gradient Figma (#FFE0B4 top → blanco) + navbar amarillo continuo
- **Archivos:** `src/pages/proyectos/comida-para-todos.astro`
- **Qué se hizo:**
  - `heroBackground="linear-gradient(180deg, #FFE0B4 0.03%, #FFFFFF 12.07%)"` (specs Figma exactas — color amarillo `#FFE0B4` solo en el ~12% superior del hero, resto fade a blanco)
  - Navbar: `body:has(.case-comida-para-todos) .navbar { background-color: #FFE0B4 }` para matchear el top color del gradient (navbar amarillo + top del hero amarillo = continuidad visual sin corte)
  - `accentColor` queda como `#FAE0CA` (se sigue usando en stat-cards de secciones internas)
- **Iteraciones:**
  - 1: gradient Figma + navbar amarillo → José: "están al revés, amarillo es el principal" (interpretación inicial: amarillo debía dominar todo el hero)
  - 2: gradient invertido (blanco top → amarillo dominante 88%) + navbar blanco → José: "el topnav es el amarillo solo" (aclaración: el amarillo solo debe ir arriba en el navbar, no dominar el hero)
  - 3 (final): vuelta al gradient Figma original + navbar amarillo. Conclusión: el amarillo es "principal" en el sentido de que solo aparece arriba (navbar + franja top del hero), el resto del hero es blanco
- **QA:** ✅ inspect hero: `background-image: linear-gradient(rgb(255,224,180) 0.03%, rgb(255,255,255) 12.07%)` · ✅ inspect navbar: `rgb(255,224,180)` · ✅ screenshot: navbar amarillo + franja amarilla top del hero fluyen sin corte, fade a blanco visible debajo
- **Lección:** las specs CSS literales del Figma estaban correctas desde el inicio — la frase "amarillo es el principal" era ambigua sin verlo aplicado. Aplicar primero las specs literales y validar visualmente antes de invertir
- **Estado:** `[x] completado`

#### 🟢 Cambio 38 — Navbar matcheando hero en case studies MACH y Comida
- **Archivos:** `src/pages/proyectos/comida-para-todos.astro`, `src/pages/proyectos/mach.astro`
- **Qué se hizo:** Replicado el patrón de navbar Santander (cambio 23) en los otros 2 case studies. Agregado `pageSlug="comida-para-todos"` y `pageSlug="mach"` en cada página + bloque `<style is:global>` con `body:has(.case-comida-para-todos) .navbar { background-color: #FAE0CA }` y `body:has(.case-mach) .navbar { background-color: #EDE8FF }` — colores idénticos al `accentColor` del hero
- **Por qué:** José notó visualmente que en Comida (y aplicaba igual a MACH) el navbar default crema cortaba el flujo del hero coloreado. Ahora el navbar continúa el color del hero sin línea de corte
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect Comida: navbar `rgb(250,224,202)` = hero · ✅ inspect MACH: navbar `rgb(237,232,255)` = hero · ✅ inspect Santander: navbar `rgb(255,180,181)` intacto · ✅ screenshots: navbar y hero continuos sin discontinuidad visual
- **Estado:** `[x] completado`

---


#### 🟢 Cambio 1 — Setup inicial del proyecto Astro
- **Archivos:** `package.json`, `astro.config.mjs`, `tsconfig.json`
- **Qué se hizo:** Creado proyecto Astro 4 con Node 20, ajustado package.json a `^4.16.0` y `engines: ">=18.17.1"`
- **Por qué:** Node 20 instalado en el equipo; Astro 5/6 requiere Node 22
- **Estado:** `[x] completado`

#### 🟢 Cambio 2 — Sistema de diseño (global.css)
- **Archivos:** `src/styles/global.css`
- **Qué se hizo:** Import Work Sans (Google Fonts, pesos 400/500/600/700), variables CSS (colores, tipografía, espaciado), reset, typography (h1-h4, p), botones (btn-primary, btn-outline), container, section-label
- **Por qué:** Design system unificado basado en el Figma de Lady Pino
- **Estado:** `[x] completado`

#### 🟢 Cambio 3 — Componente Navbar
- **Archivos:** `src/components/Navbar.astro`
- **Qué se hizo:** Navbar sticky con FirmaLadyPino.png como logo, 3 links (PROYECTOS, ACERCA DE MI, CONTACTO), prop activePage, animación underline hover
- **Por qué:** Navegación consistente en todas las páginas
- **Estado:** `[x] completado`

#### 🟢 Cambio 4 — Componente Footer
- **Archivos:** `src/components/Footer.astro`
- **Qué se hizo:** Footer con LinkedIn (SVG inline), wspIcon.png (WhatsApp), removalIcon.png (CV), texto "Diseñado por Lady Pino 2026 ❤️"
- **Por qué:** Footer compartido en todas las páginas con iconos sociales de Lady
- **Estado:** `[x] completado`

#### 🟢 Cambio 5 — Componente CTASection
- **Archivos:** `src/components/CTASection.astro`
- **Qué se hizo:** CTA configurable con props: title, subtitle, btnText, btnHref — con iconos sociales
- **Por qué:** Reutilizable en diferentes páginas con variantes de texto y destino
- **Estado:** `[x] completado`

#### 🟢 Cambio 6 — Layout BaseLayout
- **Archivos:** `src/layouts/BaseLayout.astro`
- **Qué se hizo:** SEO meta tags, OG tags, favicon (FirmaLadyPino.png), global.css import, Navbar + slot + Footer
- **Por qué:** Layout base compartido por todas las páginas
- **Estado:** `[x] completado`

#### 🟢 Cambio 7 — Layout CaseStudyLayout
- **Archivos:** `src/layouts/CaseStudyLayout.astro`
- **Qué se hizo:** Layout para case studies con props (title, description, client, role, duration, tags[], heroImage, heroImageAlt, accentColor, headingColor), header con tags, hero image, content slot, clases de sección reutilizables
- **Por qué:** Los 3 proyectos comparten la misma estructura de presentación
- **Estado:** `[x] completado`

#### 🟢 Cambio 8 — Página de inicio (index.astro)
- **Archivos:** `src/pages/index.astro`
- **Qué se hizo:** Hero 2-col (texto izquierda + ladyGirasoles.png con FirmaLadyPino.png overlay), 3 cards de proyecto (Santander rosa, MACH lavanda, Comida durazno), CTASection
- **Por qué:** Home principal del portafolio
- **Estado:** `[x] completado`

#### 🟢 Cambio 9 — Página Sobre Mí (sobre-mi.astro)
- **Archivos:** `src/pages/sobre-mi.astro`
- **Qué se hizo:** Back link, 2-col bio (LadySobremi.png + texto + "Ver mi CV" btn), bio-extra, CTASection variante, formulario de contacto (Nombre, E-mail, Mensaje, Enviar)
- **Por qué:** Página de presentación personal de Lady Pino
- **Estado:** `[x] completado`

#### 🟢 Cambio 10 — Case Study Santander
- **Archivos:** `src/pages/proyectos/santander.astro`
- **Qué se hizo:** Tema rosa (#FDE8E4, #8B3A2A), hero laptopSantander.png, 8 secciones: Desafío (68% abandono), Objetivos, Enfoque, Insights, Análisis funnel, Propuesta, Impacto (+25%/-18%/+12%/-20%), Conclusión
- **Por qué:** Case study del proyecto Santander
- **Estado:** `[x] completado`

#### 🟢 Cambio 11 — Case Study MACH
- **Archivos:** `src/pages/proyectos/mach.astro`
- **Qué se hizo:** Tema morado (#EDE8FF, #5B4DB8), hero phonemock1.png, 7 secciones: Contexto (4.2M users), Fricciones (6 cards), Objetivos, Proceso, Rediseño, Impacto (+28%/+18%/-15%/+22%), Conclusión
- **Por qué:** Case study del proyecto MACH
- **Estado:** `[x] completado`

#### 🟢 Cambio 12 — Case Study Comida para Todos
- **Archivos:** `src/pages/proyectos/comida-para-todos.astro`
- **Qué se hizo:** Tema durazno (#FAE0CA, #7A4A2A), hero TelefonosComidaParaTodos.png, 8 secciones: Desafío (stats x3), Barreras (4 cards), Persona María 46 años, Flujo actual, Propuesta, Diseño inclusivo (3 imgs), Impacto (+45%/-60%/+80%/3x), Conclusión
- **Por qué:** Case study del proyecto social Comida para Todos
- **Estado:** `[x] completado`

#### 🟢 Cambio 13 — Assets del portafolio
- **Archivos:** `assetsLady/` (24 archivos), `public/assets/`
- **Qué se hizo:** Verificación y organización de 24 assets: fotos de Lady, logos, mockups de los 3 proyectos, imágenes de proceso e íconos
- **Por qué:** Assets necesarios para replicar el Figma pixel-perfect
- **Estado:** `[x] completado`

#### 🟢 Cambio 14 — Documentos del proyecto (Barry Skills)
- **Archivos:** `Plan-Implementacion-Portafolio-Lady.docx`, `Plan-Implementacion-Portafolio-Lady.pdf`
- **Qué se hizo:** Plan de implementación completo v2.0 generado con barry skills (docx + pdf). Incluye fases, tech stack, costos $0, pasos de deploy a Netlify
- **Por qué:** Documentación ejecutiva del proyecto para José y Lady Pino
- **Estado:** `[x] completado`

#### 🟢 Cambio 15 — Configuración de entorno
- **Archivos:** `Portafolio-Lady/.claude/settings.json`, `ProyectosClaude/.claude/launch.json`
- **Qué se hizo:** 21 permisos en settings.json (MCP preview + Bash npm/astro), config de launch.json para puerto 4321
- **Por qué:** Permitir preview y builds sin prompts de permiso
- **Estado:** `[x] completado`

#### 🟢 Cambio 16 — Barry Skills + CLAUDE.md + PROGRESS.md
- **Archivos:** `CLAUDE.md`, `PROGRESS.md`, `barry-skills/` (carpeta completa)
- **Qué se hizo:** Replicado sistema barry-skills de CoreLink adaptado para Astro/Portafolio-Lady. Creados CLAUDE.md y PROGRESS.md del proyecto
- **Por qué:** José solicitó llevar toda la configuración de CoreLink a este proyecto
- **Estado:** `[x] completado`

---

#### 🟢 Cambio 27 — Sección "El Desafío" Santander rediseñada según Figma
- **Archivos:** `src/pages/proyectos/santander.astro`
- **Qué se hizo:**
  - **Header:** label "El Contexto" → "01 - CONTEXTO" (numerado, color #A4665C, letter-spacing 0.08em). Título con `desafio-title-group` (línea horizontal roja 96×2px + h2). H2: "El " charcoal 40px weight 500 + `<em>Desafío</em>` rojo italic weight 700
  - **Layout:** `desafio-grid` con 2 columnas (1.1fr / 1fr)
  - **Columna izquierda — `.challenge-list`:** 4 cards en stack vertical, fondo #FEF2F0, borde-left 3px sólido #EF4D4D, padding 20px 24px, gap 16px. Iconos SVG inline (clock/document/users/shield) con `stroke="currentColor"` color #A4665C
  - **Columna derecha — `.stats-panel`:** 2 cards `.desafio-stat` apiladas con gap 16px, fondo #FEF2F0, padding 28px 32px:
    - Card 1: label "TASA DE ABANDONO INICIAL" + número 68% en italic 64px #A4665C + desc "de usuarios no completaban el flujo"
    - Card 2: label "PRINCIPAL PUNTO DE FUGA" + título "Etapa 1 — Inicio del flujo" 20px weight 600 + desc "Formulario inicial con exceso de campos..."
  - **Responsive:** @media max-width 900 → grid 1 col
- **Por qué:** José pasó comparativa Figma vs construido — 5 diferencias: label numerado, título con highlight cursivo + línea, layout 2 cols, iconos SVG en vez de emoji, panel apilado de 2 stats
- **Lección iterativa:** primer intento usó `.stat-card` para las cards del panel pero colisionó con `.stat-card` global del CaseStudyLayout (sección Impacto). Renombré todas a `.desafio-stat` y restauré las originales de Impacto
  - **Ajuste:** removido `max-width: 980px` del `.desafio-desc` → ocupa todo el ancho del contenedor (1098px de 1178px-padding)
  - **Ajuste:** `.desafio-label::before { display: none }` — quita la línea horizontal heredada del `.section-label::before` global (28x2px) ya que el numerado "01 - CONTEXTO" va sin línea según Figma
  - **Specs Figma label:** `.desafio-label` actualizado a Work Sans 20px weight 500 line-height 1.8 (180%) letter-spacing 0 color #A7A2A6 text-transform none — override del `.section-label` global que era 0.8rem 600 uppercase con color-heading
  - **Fix línea centrada:** la línea horizontal antes del título estaba 8px más abajo del centro visual del texto. Causa: el global `:global(.case-section h2)` aplicaba `line-height: 1.7` (68px) y `margin-bottom: 16px` que mi `.desafio-title` (especificidad menor) no anulaba. Solución: aumentar especificidad con `.desafio-title-group .desafio-title { line-height: 1.2; margin: 0 }` → midDelta 0px, línea perfectamente centrada al texto
  - **Bordes en lugar de sombras:** José descartó el sombreado. Ahora `.challenge-item` y `.desafio-stat` con `border: 1px solid #E8D5CE` (rosa-beige sutil). `.challenge-item` mantiene el `border-left: 3px solid #EF4D4D` rojo. `box-shadow: none`

#### 🟢 Cambio 37 — Sección Propuesta: bg gradient consistente con Objetivos
- **Archivos:** `src/pages/proyectos/santander.astro`
- **Qué se hizo:** cambiado `style="background-color: #FEF2F0"` flat → `style="background: linear-gradient(180deg, rgba(255, 184, 185, 0.3) 0%, #FFFFFF 100%)"` (mismo gradient de la sección Objetivos)
- **Por qué:** José pidió que esta sección use el mismo gradiente que las otras secciones del mismo color (Objetivos)
- **QA:** ✅ inspect: backgroundImage `linear-gradient(rgba(255,184,185,0.3) 0%, rgb(255,255,255) 100%)`
- **Estado:** `[x] completado`

#### 🟢 Cambio 36 — Cards Propuesta: imagen flotante + caja blanca solo en el texto
- **Archivos:** `src/pages/proyectos/santander.astro`
- **Qué se hizo:**
  - `.propuesta-screen` (wrapper): removidos `background: #fff`, `border: 1px solid #E8D5CE`, `border-radius: 12px`, `padding: 32px 32px 24px` — ahora es contenedor transparente que solo provee layout flex column align-center con gap 24px
  - `.propuesta-screen__body` (texto): agregados `background: #fff`, `border: 1px solid #E8D5CE`, `border-radius: 12px`, `padding: 24px 28px` — la caja blanca ahora envuelve solo el label "PANTALLA 0X" + descripción
  - Resultado: las imágenes mockup flotan directamente sobre el fondo de la sección (sin marco), y solo el texto aparece dentro de la card blanca
- **Por qué:** José pidió que solo el texto tenga la caja blanca y las imágenes muestren el fondo de la página
- **QA:** ✅ inspect: wrap bg transparente (rgba 0,0,0,0) sin border ni padding · body bg blanco border 1px #E8D5CE radius 12px padding 24×28
- **Estado:** `[x] completado`

#### 🟢 Cambio 35 — Navbar: sin border + no sticky (estática en flujo del documento)
- **Archivos:** `src/components/Navbar.astro`
- **Qué se hizo:**
  - Removido `border-bottom: 1px solid rgba(139,58,42,0.08)` del `.navbar`
  - Cambiado de `position: sticky; top: 0` → `position: relative` → ahora la navbar es parte del flujo del documento, no sigue al scroll
  - Iteraciones previas:
    1. Primera versión: auto-hide con `translateY(-100%)` y transition al scrollear → José descartó la transición
    2. Segunda versión: estática pero sticky-top → José aclaró que tampoco debe seguir al scroll, solo visible arriba en todos los módulos
    3. Versión final: position relative, sin script de scroll, sin transición → la navbar se queda arriba en el documento y desaparece naturalmente al scrollear
- **Por qué:** José quiere que el navbar sea parte del flujo natural del documento, visible solo cuando estás arriba en el top de la página, sin animaciones ni sticky behavior
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect: `position: relative`, `border-bottom: 0px none` · scrollY=0 navbar rectTop 0 visible · scrollY=600 navbar rectTop -600 fuera del viewport (no sigue al scroll)
- **Estado:** `[x] completado`

#### 🟢 Cambio 34 — Sección "Conclusión" rediseñada según Figma (centrada + CTA)
- **Archivos:** `src/pages/proyectos/santander.astro`
- **Qué se hizo:**
  - **Header:** label "Cierre" → "08 - CIERRE" con clase `.conclusion-label` (patrón unificado). Título envuelto en `.conclusion-title-group` con línea roja 96×2 + h2 "Conclusión" (sin `<em>` — el Figma no tiene highlight en este título)
  - **CSS reglas unificadas:** agregados `.conclusion-label`, `.conclusion-title-group`, `.conclusion-title-line`, `.conclusion-title` a los selectores compartidos (label/before-display-none/title-group/title-line/title — sin selector `em` ya que no aplica)
  - **Layout centrado:** `.conclusion-inner { text-align: center; display: flex; flex-direction: column; align-items: center }` para centrar todos los elementos. `.conclusion-title-group { justify-content: center }` para centrar línea+título
  - **Subtítulo:** copy actualizado del Figma ("El rediseño permitió transformar un flujo con alta fricción en una experiencia más clara, eficiente y alineada con los objetivos del negocio. Al priorizar la entrada del funnel, cada mejora tuvo un efecto multiplicador sobre todas las etapas posteriores.") · 16px / 400 / line 1.7 / max-width 720px / text-align center
  - **CTA:** nuevo botón `.conclusion-btn` "Ver mas proyectos" → `/#proyectos` · bg #EF4D4D color blanco · padding 12×28 · border-radius 50px · 14px / 600 · transición opacity + translateY hover
  - **Removido:** `.conclusion p { font-size: 22px; color: #766F6D }` antiguo (reemplazado por la regla scoped a `.conclusion-section .conclusion-desc`)
- **Por qué:** José pasó la sección Figma "Conclusión" centrada con CTA al final ("Ver mas proyectos") y copy actualizado
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect: inner flex column text-align center · label "08 - CIERRE" · title "Conclusión" charcoal · title-group justify-content center · desc 16px 4A4A4A max-width 720 text-center · btn bg rgb(239,77,77) color blanco border-radius 50px href "/#proyectos"
- **Estado:** `[x] completado`

#### 🟢 Cambio 33 — Sección "Impacto en el negocio" rediseñada según Figma (header centrado)
- **Archivos:** `src/pages/proyectos/santander.astro`
- **Qué se hizo:**
  - **Header:** label "Resultados" → "07 - DIAGNOSTICO" con clase `.impacto-label` (patrón unificado). Título con `.impacto-title-group` línea roja + h2 "Impacto en el" charcoal + `<em>negocio</em>` italic 700 rojo
  - **Header centrado:** `.impacto-inner { text-align: center }` + `.impacto-title-group { justify-content: center }` + `.impacto-desc` con `max-width: 640px` + márgenes auto + `text-align: center` (override scoped del global de p)
  - **CSS reglas unificadas:** agregados `.impacto-label`, `.impacto-title-group`, `.impacto-title-line`, `.impacto-title` y `.impacto-desc` a los selectores compartidos
  - **Subtítulo:** copy actualizado del Figma ("Los resultados fueron medidos en las 4 semanas posteriores al lanzamiento del rediseño en producción")
  - **Reemplazo del grid de stats:** quitado el `.stats-grid + .stat-card` global del CaseStudyLayout (bg #FDE8E4) por nuevo `.impacto-stats` grid 4 cols con `.impacto-stat`: bg #fff, border 1px #E8D5CE, padding 36×24, layout flex column centrado
  - **Numbers refinados:** `.impacto-stat__number` Work Sans 48px / 700 italic / `#A4665C` con `<sup>%</sup>` 24px / 600 italic vertical-align super
  - **Labels actualizados del Figma:** Conversión en inicio de flujo · Abandono en primera etapa · Calidad de leads generados · Tiempo de decisión del usuario (max-width 180px para wrap consistente)
  - **Negativo Unicode:** `−` (U+2212) en lugar de hyphen-minus para visual consistency
  - **Responsive:** @media 900 → 2 cols · @media 540 → 1 col
- **Por qué:** José pasó la sección Figma "Impacto en el negocio" con header centrado, stats sin bg (solo borde), números italic con sup, y copy actualizado
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect: inner text-align center · title-group justify-content center · label "07 - DIAGNOSTICO" · title con em italic "negocio" · desc max-width 640px text-align center · grid 4 cols 259px · 4 stats con +25%/−18%/+12%/−20% en italic 48px 700 #A4665C · labels correctos
- **Estado:** `[x] completado`

#### 🟢 Cambio 32 — Sección "Propuesta de solución" rediseñada según Figma
- **Archivos:** `src/pages/proyectos/santander.astro`
- **Qué se hizo:**
  - **Disclaimer banner:** agregado al inicio de la sección — card bg blanco border 1px #E8D5CE radius 8px con párrafo de confidencialidad ("Este proyecto fue desarrollado en un entorno de trabajo real bajo acuerdos de confidencialidad...")
  - **Header:** label "Entregables" → "06 - PROPUESTA" con clase `.propuesta-label` (patrón unificado). Título con `.propuesta-title-group` línea roja + h2 "Propuesta de" charcoal + `<em>solución</em>` italic 700 rojo
  - **CSS reglas unificadas:** agregados `.propuesta-label`, `.propuesta-title-group`, `.propuesta-title-line`, `.propuesta-title` y `.propuesta-desc` a los selectores compartidos
  - **Subtítulo:** copy actualizado del Figma ("Cada decisión de diseño respondía a un dato del análisis previo")
  - **Reemplazo del grid de imágenes:** quitadas las 2 imágenes apiladas (`.solution-images`) por nuevo grid 2 cols `.propuesta-screens`. Cada `.propuesta-screen` con bg blanco border 1px #E8D5CE radius 12px contiene: imagen mockup (220px max width centrado) + body con label "PANTALLA 01/02" uppercase 12px #A7A2A6 + descripción charcoal 16/500
  - **Pilares nuevos:** agregado grid 3 cols `.propuesta-pillars` con border-top + padding-top 32px que separa de las screens. Cada pillar: label uppercase + descripción charcoal 15/400 (CAMBIOS ESTRUCTURALES / JERARQUÍA VISUAL / SEÑALES DE CONFIANZA con copy del Figma)
  - **Responsive:** @media max-width 900 → screens 1 col + pillars 1 col gap 24
- **Por qué:** José pasó la sección Figma "Propuesta de solución" con disclaimer, 2 cards de pantallas con label y descripción, y 3 pilares al pie
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect: disclaimer presente · label "06 - PROPUESTA" · title con em italic "solución" · desc 18px #4A4A4A · 2 screens en grid 2 cols (536px cada uno) con labels PANTALLA 01/02 · 3 pillars en grid 3 cols (344px) con labels CAMBIOS ESTRUCTURALES/JERARQUÍA VISUAL/SEÑALES DE CONFIANZA
- **Estado:** `[x] completado`

#### 🟢 Cambio 31 — Sección "Análisis del funnel" rediseñada según Figma
- **Archivos:** `src/pages/proyectos/santander.astro`
- **Qué se hizo:**
  - **Header:** label "Evidencia" → "05 - DIAGNOSTICO" con clase `.analisis-label` (mismo patrón unificado). Título con `.analisis-title-group` línea roja + h2 "Análisis del" charcoal + `<em>funnel</em>` italic 700 rojo
  - **CSS reglas unificadas:** agregados `.analisis-label`, `.analisis-title-group`, `.analisis-title-line`, `.analisis-title` a los selectores compartidos
  - **Reemplazo del contenido:** quitadas las 2 imágenes apiladas (FunnelSantander.png + AnalisisDelFunnelSantander.png) que servían como placeholder. Ahora layout 2 columnas (1.1fr / 1fr) con el contenido del Figma:
    - **Columna izquierda — `.analisis-funnel-card`:** card bg #FEF2F0 border 1px #E8D5CE radius 12px padding 32px con label "TASA DE PASO POR ETAPA — ESTADO INICIAL" + 5 barras `.funnel-stage` (Inicio 100%, Datos personales 54%, Selección cobertura 38%, Pago 32%, Contratación 28%) cada una con nombre + value + barra horizontal degradante (de #8B4F44 oscuro a #E8C0B5 claro). Footer con border-top: "Mayor punto de fuga detectado" + "Etapa 1-2 (-46%)" rojo
    - **Columna derecha — `.analisis-content`:** intro `.analisis-content__intro` + card `.analisis-hipotesis` con bg #FEF2F0 border + border-left 3px rojo (label "HIPÓTESIS PRINCIPAL" + texto) + grid 2x1 `.analisis-mini-cards` con `.analisis-mini-card` (label + número 36px 700 #A4665C + desc): "11 campos requeridos" / "48s en etapa inicial"
  - **Removido:** clase `.funnel-content` ya no se usa
  - **Responsive:** @media max-width 900 → grid 1 col + mini-cards 1 col
- **Por qué:** José pasó la sección Figma "Análisis del funnel" con un nuevo layout de datos (no imágenes) y pidió aplicar el mismo patrón
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect: label "05 - DIAGNOSTICO" 20px #A7A2A6 · title charcoal con em italic rojo · grid 558/507px · 5 stages con names/values correctos · hipothesis con border-left 3px rojo · 2 mini-cards con número 36px 700 #A4665C
- **Estado:** `[x] completado`

#### 🟢 Cambio 30 — Sección "Insights clave" rediseñada según Figma (light theme)
- **Archivos:** `src/pages/proyectos/santander.astro`
- **Qué se hizo:**
  - **Cambio de tema:** removido `case-section--dark` (bg #1A1A1A + texto blanco/verde) → light theme con bg `#FEF2F0` (rosa claro coherente con el resto del case study)
  - **Header:** label "Aprendizajes" → "04 - DIRECCIÓN" con clase `.insights-label` (mismo patrón unificado: Work Sans 20/500/180%/#A7A2A6 sin línea). Título envuelto en `.insights-title-group` con línea roja 96×2 + h2 `.insights-title` ("Insights" charcoal + `<em>clave</em>` italic 700 rojo)
  - **CSS reglas unificadas:** agregados `.insights-label`, `.insights-title-group`, `.insights-title-line`, `.insights-title` y `.insights-desc` a los selectores compartidos
  - **Subtítulo nuevo:** "La investigación reveló patrones consistentes que explicaban el comportamiento de abandono de los usuarios."
  - **Cards rediseñadas:** `.insight-card` ahora con bg blanco, border 1px `#E8D5CE`, padding 32px, layout flex column con gap 16px. Cada card tiene:
    - `.insight-card__icon` — cuadrado 40×40 con bg `#FEF2F0`, border-radius 8px, color `#A4665C`, contiene SVG inline (rayo, ojo, layout, corazón)
    - `.insight-card__title` — Work Sans 18/600 charcoal `#3D3D3D`
    - `.insight-card__desc` — Work Sans 14/400 line 1.6 `#766F6D` gris
  - **Contenido actualizado del Figma:** Exceso de pasos en el flujo · Falta de claridad en el inicio · Información poco estructurada · Baja percepción de valor (con descripciones extendidas)
- **Por qué:** José pasó comparativa Figma vs construido — pidió aplicar el mismo rediseño que las secciones anteriores
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect: section bg `#FEF2F0` · label "04 - DIRECCIÓN" 20px #A7A2A6 · title charcoal con em italic rojo · 4 cards bg blanco border 1px #E8D5CE · icon 40×40 bg #FEF2F0 color #A4665C · titles del Figma correctos
- **Estado:** `[x] completado`

#### 🟢 Cambio 29 — Sección "Enfoque de trabajo" rediseñada según Figma
- **Archivos:** `src/pages/proyectos/santander.astro`
- **Qué se hizo:**
  - **Header:** label "Metodología" → "03 - PROCESO" con clase `.enfoque-label` (mismo patrón unificado de Desafío y Objetivos: Work Sans 20/500/180%/#A7A2A6 sin línea). Título envuelto en `.enfoque-title-group` con línea roja 96×2 + h2 `.enfoque-title` ("Enfoque de" charcoal + `<em>trabajo</em>` italic 700 rojo)
  - **CSS reglas unificadas:** agregados `.enfoque-label`, `.enfoque-title-group`, `.enfoque-title-line`, `.enfoque-title` a los selectores compartidos con `.desafio-*` y `.objetivos-*` — DRY mantenido
  - **Layout proceso:** reemplazado layout anterior (cards con emojis 🔍 📊 ✏️ 🧪 + flechas → entre ellas) por nuevo `.process-flow`:
    - Grid 4 columnas iguales con `position: relative`
    - Línea horizontal `::before` 1px `#D9C8C2` desde 12.5% a 87.5% (centros de los círculos extremos), top 32px
    - 4 `.process-step` con z-index 1 sobre la línea
    - Cada step: `.process-step__circle` 64×64 con border 1.5px `#A4665C` background `#FEF2F0` con número 01-04 charcoal-marrón Work Sans 14px / 500
    - `.process-step__title` Work Sans 18px / 600 charcoal + `.process-step__desc` Work Sans 14px / 400 / `#A7A2A6` gris
  - **Contenido actualizado según Figma:** Investigación / Análisis / Diseño / Iteración (antes era Validación) con descripciones actualizadas
- **Por qué:** José pasó comparativa Figma vs construido — diferencias: label numerado, título con línea + highlight, layout círculos numerados conectados (no cards con emojis), contenido distinto en step 4
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect: label "03 - PROCESO" 20px #A7A2A6 · title charcoal con em italic 700 rojo · 4 círculos 64×64 border #A4665C bg #FEF2F0 · grid 274×4px equilibrado · titles Investigación/Análisis/Diseño/Iteración
- **Estado:** `[x] completado`

#### 🟢 Cambio 28 — Sección Objetivos: gradient bg + border en cards + header con patrones de Desafío
- **Archivos:** `src/pages/proyectos/santander.astro`
- **Qué se hizo:**
  - Section background: `background-color: #FEF2F0` → `background: linear-gradient(180deg, rgba(255, 184, 185, 0.3) 0%, #FFFFFF 100%)` (specs Figma)
  - `.objective-card`: removido `box-shadow: 0 2px 12px rgba(0,0,0,0.04)`, agregado `border: 1px solid #E8D5CE` (mismo tono que challenge-item / desafio-stat)
  - **Header rediseñado siguiendo patrón de Desafío:**
    - Label "Dirección" → "02 - DIRECCIÓN" con clase `.objetivos-label` (Work Sans 20px / 500 / line-height 1.8 / `#A7A2A6` / sin línea heredada del global section-label)
    - Título envuelto en `.objetivos-title-group` con `.objetivos-title-line` (96×2px `#EF4D4D`) + h2 `.objetivos-title` con `<em>Objetivos</em>` (italic 700 rojo) + " del proyecto" (charcoal 40px/500)
  - **CSS refactorizado DRY:** unificadas las reglas de `.desafio-*` y `.objetivos-*` en selectores compuestos (mismas reglas para label, title-group, title-line, title) — evita duplicación si se agregan más secciones con este patrón
  - **Subtítulo Objetivos:** agregado `<p class="objetivos-desc">Definimos cuatro objetivos concretos, medibles y alineados con el negocio para orientar cada decisión de diseño.</p>` faltante
  - **Colores cards Figma:** `.objective-num` cambiado de Work Sans 28px 700 charcoal → Work Sans 40px 700 italic `#F5B5B0` (rosa claro). `.objective-card p` cambiado de 18px 400 #766F6D → 16px 600 `#3D3D3D` (charcoal)
  - **Fix specificity desc:** el global `:global(.case-section > .case-section__inner > p)` del CaseStudyLayout (specificity 0,4,1) sobreescribía mis `.desafio-desc` y `.objetivos-desc` (0,4,0). Solución: selectores de 3 clases `.desafio-section .case-section__inner .desafio-desc` y `.objetivos-section .case-section__inner .objetivos-desc` (0,5,0) → ganan. Fix también arregló retroactivamente el desafio-desc que estaba en 24px aunque no se había detectado
- **Por qué:** José pidió aplicar los mismos conceptos visuales de la sección Desafío al header completo de Objetivos
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect Objetivos: label 20/500/36px/#A7A2A6 sin ::before · title 40/500/48px charcoal · em italic 700 #EF4D4D · línea 96x2px #EF4D4D · midDelta 0 (línea centrada al texto) · objective-card border 1px #E8D5CE box-shadow none
- **Estado:** `[x] completado`
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect: label "01 - CONTEXTO" 14px #A4665C · título 40px charcoal con em rojo italic 700 · grid 558/507px · 4 challenge-items con borde-left 3px solid rojo + bg #FEF2F0 · 2 desafio-stat (panel) + 4 stat-card (Impacto) sin colisión de estilos
- **Estado:** `[x] completado`

#### 🟢 Cambio 26 — Hero Santander: laptop más grande + funnel overlay (Figma)
- **Archivos:** `src/layouts/CaseStudyLayout.astro`, `src/pages/proyectos/santander.astro`
- **Qué se hizo:**
  - **CaseStudyLayout** — props nuevas opcionales: `heroImageSecondary`, `heroImageSecondaryAlt`. `<img class="case-hero__image-main">` siempre + `<img class="case-hero__image-secondary">` solo si la prop existe (`{heroImageSecondary && (<img.../>)}`). `.case-hero__image` con `position: relative` para anclaje absoluto.
  - **Scoped Santander:** `.case-santander .case-hero__inner` grid `1fr 1.35fr` (más espacio a la imagen) · `.case-santander .case-hero__image-main` width 115% + translateX(-2%), `position: relative`, `z-index: 2` (laptop adelante, ligeramente desbordada) · `.case-santander .case-hero__image-secondary` position absolute top -32 right -24, width 38%, border-radius 12px, box-shadow 0 12px 32px rgba(0,0,0,0.18), `z-index: 1` (funnel detrás)
  - **santander.astro:** `heroImageSecondary="/assets/FunnelSantander.png"` con alt "Análisis del funnel de conversión Santander"
  - **Corrección iterativa:** asset inicial fue `AnalisisDelFunnelSantander.png` y z-index estaba invertido (overlay encima) → José corrigió a `FunnelSantander.png` y laptop adelante
  - **Ajuste:** `.case-santander .case-hero__image { align-self: end; margin-bottom: -100px }` → la laptop se desborda los 100px del padding-bottom del hero para tocar el borde sin afectar la posición del texto (texto mantiene padding-top 80px y padding-bottom 100px originales)
  - **Lección iterativa:** primer intento usó `padding-bottom: 0` en `.case-hero`, pero eso achicó el contenedor y subió el texto centrado verticalmente. Solución correcta: mantener el padding y desbordar solo la imagen con margin negativo
  - **Refinado:** `.case-santander .case-hero { overflow: hidden }` + `transform: translate(-2%, 80px)` en `.case-hero__image-main` → la laptop baja 80px adicionales y se recorta limpio al borde inferior (visible 377px de 457px totales). Solo afecta la laptop — el funnel overlay sigue en su posición original
  - **Ajuste funnel:** `.case-hero__image-secondary` width reducido de 38% a 28% (227 → 167px) para que el funnel asome más arriba de la laptop sin quedar cubierto. Overlap visual con laptop: 167×59px (antes era casi total)
- **Por qué:** José pasó comparativa Figma — la laptop debe ser más grande y debe haber un mockup de análisis del funnel posicionado arriba-derecha como overlay
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect Santander: grid `441/596px`, main 685×457px (115%), secondary 227×172px en top -32/right -24, position absolute z-index 2, shadow OK · ✅ MACH: `secondaryImgExists: false` (Astro no renderiza el `<img>` cuando la prop no se pasa) — retrocompatible
- **Estado:** `[x] completado`

#### 🟢 Cambio 25 — Tipografía y color caso-group Santander (specs Figma)
- **Archivos:** `src/layouts/CaseStudyLayout.astro`
- **Qué se hizo:**
  - Override scoped `.case-santander .case-hero__caso-text` con specs Figma: `font-size: 24px`, `font-weight: 500`, `line-height: 1.8` (180%), `letter-spacing: 0`, `color: #A4665C`
  - Override scoped `.case-santander .case-hero__caso-line` con `background-color: #A4665C` (mismo color que el texto)
  - Mismo patrón scoped del back-link → MACH/Comida sin cambios
- **Por qué:** José pasó specs Figma del "Caso de estudio - Banco Santander" (línea + texto)
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect: text fontFamily Work Sans, 24px, weight 500, line-height 43.2px (= 180%), color rgb(164,102,92) · líneaBg rgb(164,102,92)
- **Estado:** `[x] completado`

#### 🟢 Cambio 24 — Tipografía y color back-link Santander (specs Figma)
- **Archivos:** `src/layouts/CaseStudyLayout.astro`
- **Qué se hizo:**
  - Override scoped `.case-santander .back-link` con specs Figma: `font-size: 20px`, `font-weight: 400`, `line-height: 1.8` (180%), `letter-spacing: 0`, `color: #A4665C`
  - SVG flecha hereda vía `stroke="currentColor"` → flecha también `#A4665C` sin tocar el markup
  - Patrón scoped (igual que navbar-rosa del cambio #23) para no romper MACH/Comida que mantienen su back-link original
- **Por qué:** José pasó specs Figma exactas del back-link de Santander
- **QA:** ✅ build 5 páginas 0 errores · ✅ inspect `/proyectos/santander`: fontFamily Work Sans, 20px, weight 400, line-height 36px (= 180%), color rgb(164,102,92), svgStroke rgb(164,102,92) · ✅ MACH no tiene clase `case-santander` → selector no aplica, conserva estilo original
- **Estado:** `[x] completado`

#### 🟢 Cambio 23 — Hero case study rediseñado según Figma + navbar rosa Santander
- **Archivos:** `src/layouts/CaseStudyLayout.astro` (rediseño completo del hero), `src/pages/proyectos/santander.astro`
- **Qué se hizo:**
  - **CaseStudyLayout** — nuevo HTML del hero con todos los elementos del Figma:
    - `back-link` (← Volver atrás con SVG flecha)
    - `caso-group` (línea + "Caso de estudio - {client}")
    - `case-hero__title` con `<em class="case-hero__title-highlight">` para el highlight cursivo rojo
    - `case-hero__desc` con tipografía suave
    - `case-hero__info` (3 columnas: ROL · DURACIÓN · ENFOQUE) con divisor vertical + label uppercase rojo + value gris oscuro
    - `case-hero__btn` botón rojo brillante reemplaza `btn-primary`
  - **Nuevos props opcionales:** `titleHighlight`, `highlightColor`, `focus`, `brightColor`, `pageSlug`. Fallback inteligente: si no hay `focus`, usa `tags.join(' / ')` (compat MACH/Comida)
  - **Gradient corregido:** `linear-gradient(180deg, #FFB4B5 0%, #FFFFFF 100%)` (antes era invertido: rosa solo 12%)
  - **Navbar rosa Santander:** `<style is:global>` con `body:has(.case-santander) .navbar { background-color: #FFB4B5 }` aplica solo en esta page vía `pageSlug="santander"`
  - **headingColor Santander:** cambiado de `#8B3A2A` (marrón) a `#3D3D3D` (charcoal) según Figma — el rojo es solo para highlight/labels/botón
- **Por qué:** José pidió Figma fidelity completa del hero. Aprovecha el layout compartido para que MACH/Comida hereden mejoras sin romperse
- **QA:** ✅ build 0 errores · ✅ inspect Santander: highlight 48px italic weight 700 color `#EF4D4D` · botón bg `#EF4D4D` · navbar bg `#FFB4B5` · labels uppercase 12px `#EF4D4D` · caso-text 20px `#3D3D3D` · ✅ MACH inspect: info-value funcionando con fallback tags + navbar default `#FFF5EE` (no se rompe) · ✅ Comida igual
- **Estado:** `[x] completado`

#### 🟢 Cambio 22 — Hero Santander con gradient personalizable
- **Archivos:** `src/layouts/CaseStudyLayout.astro`, `src/pages/proyectos/santander.astro`
- **Qué se hizo:**
  - **CaseStudyLayout**: nuevo prop opcional `heroBackground` (string CSS). Si se pasa, sobreescribe el `accentColor` flat. Si no, el comportamiento default sigue igual (no rompe MACH/Comida).
  - **santander.astro**: pasa `heroBackground="linear-gradient(180deg, #FFB4B5 0.03%, #FFFFFF 11.98%)"`
- **Por qué:** José solicitó este gradient específico para el hero de Santander según el Figma. Las secciones siguientes ya están intercaladas con blanco/#FEF2F0
- **QA:** ✅ build 0 errores · ✅ inspect Santander: `background-image: linear-gradient(rgb(255,180,181) 0.03%, rgb(255,255,255) 11.98%)` · ✅ MACH sigue con flat `rgb(237,232,255)` (#EDE8FF) · ✅ Comida sigue con flat `rgb(250,224,202)` (#FAE0CA) — no se rompieron
- **Estado:** `[x] completado`

#### 🟢 Cambio 21 — Tipografía consistente en case study Santander + fix B2
- **Archivos:** `src/layouts/CaseStudyLayout.astro`, `src/pages/proyectos/santander.astro`
- **Qué se hizo:**
  - **CaseStudyLayout** (compartido por los 3 case studies):
    - `.case-hero__title`: Work Sans 48px weight 500 line-height 1.3 (era clamp 1.8-2.8rem line 1.15)
    - `.case-hero__label`: Work Sans 14px weight 600
    - `.case-tag`: bg cambiado de `rgba(139,58,42,0.1)` hardcoded a `var(--accent)` → cada case study usa su propio accentColor (**fix B2**)
    - `.case-section h2`: Work Sans 40px weight 500 line 1.7 color var(--case-heading) (vía :global)
    - `.case-section h2 + p`: Work Sans 24px weight 400 line 1.7 color #766F6D
    - `.stat-number`: Work Sans 40px weight 700 · `.stat-label`: Work Sans 16px color #766F6D
  - **santander.astro**: todos los `font-size` en rem reemplazados por px Work Sans con jerarquía consistente. Cards (challenge, objective, process, insight) con tipografía unificada. Highlight stat 80px. Conclusion 22px.
- **Por qué:** José pidió aplicar el mismo lenguaje visual de index/sobre-mi a la página Santander
- **QA:** ✅ build 0 errores · ✅ inspect: hero title 48px Work Sans / case-tag bg #FDE8E4 (accentColor Santander, NO marrón global) / h2 sección 40px / objective p 18px / stat-number 40px / case-section__inner 1600px (heredado del global) · Bonus: B2 resuelto para los 3 case studies (cada uno con su accentColor)
- **Estado:** `[x] completado`

#### 🟢 Cambio 20 — Ancho responsive 1600px global (DRY)
- **Archivos:** `src/styles/global.css`, `src/components/CTASection.astro`, `src/pages/index.astro`, `src/pages/sobre-mi.astro`
- **Qué se hizo:**
  - `global.css`: `--max-width` cambiado de `1200px` a `min(1600px, 92vw)` → afecta `.container` global y `CaseStudyLayout` que ya usaba la variable
  - `CTASection.astro`: `.cta__inner` actualizado de `1200px` hardcoded a `min(1600px, 92vw)`
  - `index.astro`: `.hero__inner` actualizado de `1440px` hardcoded a `min(1600px, 92vw)`
  - `sobre-mi.astro`: eliminado override scoped redundante (ahora hereda del global). Conservado `.cta-contact__inner` que ya tenía la fórmula
- **Por qué:** José pidió aplicar el ancho responsive al index y resto de páginas — solución DRY vía variable global afecta también case studies sin tocar más archivos
- **QA:** ✅ build 0 errores · ✅ inspect a 1920px en 5 selectores: `.hero__inner` 1600px, `.projects > .container` 1600px, `.cta__inner` 1600px, `.bio.container` 1600px, `.case-section__inner` 1600px (santander) · ✅ inspect a 1366px: hero 1257px (92vw) — responsive OK
- **Estado:** `[x] completado`

#### 🟢 Cambio 19 — Foto bio cap a 550px + gap fluido
- **Archivos:** `src/pages/sobre-mi.astro`
- **Qué se hizo:** `.bio` grid cambiado de `1fr 1fr` a `minmax(400px, 550px) 1fr` (foto cap a su tamaño original) + `gap: clamp(80px, 10vw, 180px)` para que el ancho extra del container 1600px se traduzca en más respiro entre foto y texto, no en foto gigante. `.bio__photo img` con `max-width: 550px`.
- **Por qué:** Al ampliar el container (cambio 18), el grid 1fr 1fr hizo que la foto creciera a ~750px — José pidió mantener tamaño original y aumentar separación
- **QA:** ✅ inspect 1920px: foto 550px / gap 180px / texto 790px · ✅ inspect 1366px: foto 550px / gap 136px / texto 490px · responsive sin overflow
- **Estado:** `[x] completado`

#### 🟢 Cambio 18 — Sobre-mi con ancho responsive más amplio
- **Archivos:** `src/pages/sobre-mi.astro`
- **Qué se hizo:** Override de `.container` solo en sobre-mi: `max-width: min(1600px, 92vw)` para `.sobre-mi > .container`, `.bio.container`, `.bio-extra.container` y `.cta-contact__inner`. No afecta otras páginas (cambio scoped).
- **Por qué:** En pantallas grandes el contenido se veía muy acotado con max-width 1200px del global container
- **QA:** ✅ build 0 errores · ✅ inspect a 1366px = 1257px (antes 1200) · ✅ inspect a 1920px = 1600px (antes 1200) · 92vw asegura escalado fluido sin overflow en mobile
- **Estado:** `[x] completado`

#### 🟢 Cambio 17 — Tipografía y colores formulario sobre-mi (specs Figma)
- **Archivos:** `src/styles/global.css`, `src/pages/sobre-mi.astro`
- **Qué se hizo:** Importada Nunito (400/500/600/700) en global.css. Aplicada a labels, inputs, textarea y botón Enviar del formulario: `font-family: Nunito; font-weight: 600; font-size: 16px; line-height: 100%; color: #A4665C`. Inputs/textarea con `border: 1px solid #A4665C`. Botón Enviar background `#A4665C`. Placeholders en Nunito 600 con color #A4665C 50% opacity. Textarea conserva line-height 1.5 para legibilidad multilinea.
- **Por qué:** José pasó specs Figma exactas del formulario
- **QA:** ✅ build 0 errores · ✅ inspect confirma label/input/botón con Nunito 600 16px lh-16px color rgb(164,102,92) y border-color rgb(164,102,92) 1px solid
- **Estado:** `[x] completado`

---

## Bugs detectados — Revisión visual (2026-04-25)

| ID | Error | Impacto | Archivo | Estado |
|----|-------|---------|---------|--------|
| B1 | MACH hero roto — phonemock1.png sin max-height, empuja título fuera del viewport | 🔴 Crítico | `CaseStudyLayout.astro` | pendiente |
| B2 | Tags color hardcodeado `rgba(139,58,42,0.1)` — marrón Santander en los 3 proyectos | 🔴 Incorrecto | `CaseStudyLayout.astro` | ✅ resuelto (cambio 21) |
| B3 | Logo navbar demasiado pequeño — `height: 36px` apenas visible | 🟡 Branding | `Navbar.astro` | pendiente |
| B4 | Firma overlay con coords negativas — `bottom:-16px; right:-8px` puede causar overflow | 🟡 Layout | `index.astro` | pendiente |
| B5 | Gap excesivo entre hero y sección proyectos — 160px combinados | 🟡 Espaciado | `index.astro` | pendiente |
| B6 | `align-items: center` en hero — descentra texto cuando imagen tiene altura variable | 🟡 Alineación | `CaseStudyLayout.astro` | pendiente |
| B7 | "Ver mi CV" usa `btn-outline` en vez de `btn-primary` | 🟡 Diseño | `sobre-mi.astro` | pendiente |
| B8 | Anchor `#solucion` no existe en `mach.astro` — botón "Ver solución" roto | 🟠 Link roto | `mach.astro` | pendiente |

---

## Fase 2 — Revisión visual + Responsive (pendiente)

#### 🔲 Pendiente — Revisión pixel-perfect contra Figma
- **Archivos:** Todas las páginas
- **Qué hacer:** Comparar screenshot vs Figma para cada página, ajustar espaciados, tipografías y colores
- **Estado:** `[ ] pendiente`

#### 🔲 Pendiente — Responsive mobile
- **Archivos:** `src/styles/global.css`, componentes, páginas
- **Qué hacer:** Media queries mobile-first, testing en 375px y 768px
- **Estado:** `[ ] pendiente`

---

## Fase 3 — Integración y Deploy (pendiente)

#### 🔲 Pendiente — EmailJS integration
- **Archivos:** `src/pages/sobre-mi.astro`
- **Qué hacer:** Conectar formulario de contacto con EmailJS (200 emails/mes gratis), configurar template de email para Lady Pino (j.barrios.r28@gmail.com → email real de Lady)
- **Estado:** `[ ] pendiente`

#### 🔲 Pendiente — Deploy Netlify
- **Archivos:** `netlify.toml` (a crear)
- **Qué hacer:** Crear cuenta Netlify, conectar repositorio GitHub, configurar build command `npm run build` y publish dir `dist/`
- **Estado:** `[ ] pendiente`

#### 🔲 Pendiente — URLs y CV reales de Lady
- **Archivos:** `src/components/Footer.astro`, `src/pages/sobre-mi.astro`
- **Qué hacer:** Actualizar URL LinkedIn, WhatsApp y enlace CV de Lady Pino con sus datos reales
- **Estado:** `[ ] pendiente`
