# Barbería Domínguez — web estática

Solo HTML + CSS (con algo de JS para el menú y el selector de barbero).
Sin dependencias, sin build, sin servidor.

## Archivos

- `index.html` — todo el contenido
- `styles.css` — todos los estilos

## Cómo verla

Abre `index.html` con doble clic. Ya está.

## Qué cambiar

### 1. Imágenes (11 en total)

Todas son enlaces a Unsplash de prueba. Busca `IMAGEN` o `src="https://images.unsplash`
en `index.html` y sustituye la URL por la tuya (o por una ruta local, ej. `img/corte-1.jpg`).

| Sitio | Cuántas | Tamaño recomendado |
|---|---|---|
| Hero (portada) | 1 | 1600 × 1000 px |
| Sobre nosotros | 1 | 900 × 1100 px |
| Galería | 6 | 800 × 800 px (cuadradas) |
| Equipo | 3 | 600 × 600 px (cuadradas) |

### 2. Datos del negocio

Busca y reemplaza en `index.html`:

- `Barbería Domínguez` → tu nombre
- `910 000 000` y `+34910000000` → tu teléfono
- `hola@barberiadominguez.es` → tu correo
- `Calle de la Tijera, 12 · 28012 Madrid` → tu dirección
- Precios y servicios en la sección `<!-- SERVICIOS -->`
- El iframe del mapa: en Google Maps → Compartir → Insertar un mapa → copia el `src`

### 3. Colores

Todo sale de `:root` al principio de `styles.css`. El color de marca es `--gold`.

## Reservas con Google Calendar

La web usa el widget nativo y gratuito de Google Calendar (Appointment Schedules).
Hay tres botones de barbero y cada uno carga **su** calendario.

**Los tres vienen sin calendario conectado.** Mientras estén vacíos, la web muestra
un aviso en lugar de un iframe roto.

### Paso 1 — Crear la página de citas

Para cada barbero, desde **su propia cuenta de Google**. Una página de citas apunta a
un solo calendario, así que no se pueden separar tres barberos desde una sola.

1. Google Calendar → **Crear → Página de citas**
2. Ponle nombre (ej. *"Corte de pelo con Roberto"*), duración y horario disponible
3. En **Ubicación y conferencia**, elige **En persona** y pon la dirección.
   Si dejas Google Meet, el cliente recibe un enlace de videollamada para una cita
   presencial, y además aparece esa frase en el widget.
4. En **Formulario de reserva**, si quieres el teléfono: activa el campo, o añade una
   pregunta tipo *"WhatsApp de contacto"* y márcala obligatoria.
   Nombre y correo son obligatorios de Google y no se pueden quitar.
5. **Compartir → Insertar** y copia los dos enlaces que te da Google

### Paso 2 — Pegarlos en la web

En `index.html`, cada barbero tiene su botón con dos atributos vacíos:

```html
<button class="barber is-active" role="tab" aria-selected="true"
        data-name="Roberto"
        data-embed=""
        data-link="">
```

- `data-embed` → URL larga del iframe
  (`calendar.google.com/calendar/appointments/schedules/...?gv=true`)
- `data-link` → enlace corto para compartir (`calendar.app.google/...`),
  lo usa el botón "Abrir calendario completo"

Para que la web cargue ya con un calendario al abrirse (sin pulsar un barbero),
pega también esa URL larga en el `src` del `<iframe id="bookingFrame">`.

Para cambiar nombres o especialidades, edita el `<strong>` y el `<small>` de cada botón.

### Si rehaces la página de citas en Google

El ID del iframe cambia. Vuelve a **Compartir → Insertar**, copia el `src` y
actualiza `data-embed` (y el enlace corto en `data-link`).

## Qué pasó con el móvil (ya arreglado)

Al principio no se podía reservar desde el teléfono. Eran cuatro cosas del código,
no de Google — el calendario de Google funcionaba bien probado por separado:

1. **El iframe salía a 305px** en pantalla de 375. El doble padding (contenedor + caja)
   le comía 70px, y por debajo de ~360px Google no muestra bien su vista táctil.
   Ahora el bloque va **a sangre** en móvil: 375px completos.
2. **Alto fijo de 700px con `overflow:hidden`.** El contenido de Google era más alto,
   así que creaba **su propio scroll interno**: al arrastrar el dedo movías el iframe
   en vez de la página y los días quedaban fuera de alcance. Ahora son 920px, suficiente
   para que quepa entero.
3. **El botón fijo "Pedir cita" tapaba la parte baja del calendario**, justo donde caen
   los días seleccionables. Ahora se esconde al entrar en la sección de reservas.
4. **Ese ocultado no funcionaba**: usaba `IntersectionObserver` y no disparaba.
   Reescrito con un cálculo directo de posición en scroll.

En escritorio no se notaba porque allí sobraba ancho y alto.

## Detalles pensados para el público mixto (mayores y jóvenes)

- Cuerpo de texto a 17–18 px, no 14 px
- Todos los botones ≥ 52 px de alto (fáciles de pulsar con el dedo)
- Teléfono y correo son enlaces pulsables (`tel:` / `mailto:`)
- Botón fijo "Pedir cita" abajo en móvil, siempre visible salvo en el calendario
- Botón "Reservar por teléfono" junto al calendario: reservar en Google exige un correo
  válido, y para parte de la clientela llamar es más rápido
- Contraste alto, foco visible al navegar con teclado
- Respeta `prefers-reduced-motion`
