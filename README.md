# Cielo actual

Mapa astronómico en vivo para San Francisco del Oro, Chihuahua (26°52′ N, 105°51′ O).

## Qué muestra

- Estrellas sobre el horizonte local, proyectadas con el cenit al centro; incluye una banda tenue de la Vía Láctea y galaxias sin etiquetas encima del mapa.
- Orientación como al mirar el cielo recostado: la parte de abajo del mapa es lo que tienes enfrente. Por defecto se asume que miras al norte (**norte abajo, sur arriba, oeste a la izquierda y este a la derecha**). El selector **Mirando hacia** gira el mapa para que coincida con la dirección en la que estás de frente (norte, este, sur u oeste) y recuerda tu elección. La malla de azimut/altitud es tenue, solo como referencia visual.
- Posiciones de la Luna y los planetas calculadas para la hora actual.
- Satélites del grupo visual de CelesTrak sobre el horizonte, con estelas de hasta 7 minutos y coordenadas de cada satélite. Al pasar el cursor por su nombre en la tarjeta, se resalta en el mapa.
- Aeronaves cercanas reportadas por ADS-B (radio de 100 millas), con estelas de hasta 7 minutos. Cada una muestra su azimut, su altura sobre el horizonte y su altitud real de vuelo (pies y metros). Al pasar el cursor por su nombre, se resalta en el mapa en color ámbar con una etiqueta de indicativo y altitud.
- Próxima pasada de la Estación Espacial Internacional calculada con los elementos orbitales actuales de CelesTrak.
- Eventos celestes de la semana (lunes a domingo): fases lunares, Luna del cazador, eclipses, lluvias de meteoros, agrupaciones Starlink y próxima pasada de la Estación Espacial Internacional. Los eventos activos aparecen con alerta; los ya ocurridos se retiran.
- Lluvias de meteoros activas según su calendario estacional. Los meteoros individuales no se pueden anticipar.

El mapa se actualiza cada minuto. La disponibilidad de satélites y aeronaves depende de los servicios y de la cobertura de datos. No se necesitan claves privadas.

## Publicar con GitHub Pages

En el repositorio, abre **Settings → Pages**, selecciona la rama `main` y la carpeta `/ (root)`. GitHub Pages publicará `index.html`.

## Proxy con Cloudflare Workers (necesario)

Los servicios de aeronaves y CelesTrak no permiten, o limitan, las peticiones directas desde el navegador (CORS, bloqueos por IP y límites de uso). Por eso la página las pide a través de un Worker gratuito de Cloudflare que:

- agrega un `User-Agent` identificable, que adsb.lol exige;
- permite CORS para que el navegador pueda leer la respuesta;
- guarda en caché las respuestas: 30 segundos para aeronaves y 2 horas para órbitas. Así los servicios externos reciben muy pocas peticiones.

### Crearlo

1. En [dash.cloudflare.com](https://dash.cloudflare.com), ve a **Workers & Pages → Create → Create Worker**.
2. Ponle el nombre `aviones` y pulsa **Deploy**. Debe ser un Worker aparte, sin archivos del sitio asociados: si tiene archivos estáticos, Cloudflare los sirve antes de ejecutar el código del proxy.
3. Pulsa **Edit code**, reemplaza todo por este código y pulsa **Deploy**:

```js
export default {
  async fetch(req) {
    const u = new URL(req.url).searchParams.get('url');
    const ok = /^https:\/\/(api\.adsb\.lol|opendata\.adsb\.fi|api\.airplanes\.live|celestrak\.org)\//;
    if (!u || !ok.test(u)) return new Response('bad request', { status: 400 });

    const isTLE = u.startsWith('https://celestrak.org');
    const ttl = isTLE ? 7200 : 30;

    const r = await fetch(u, {
      headers: { 'User-Agent': 'cielo-actual/1.0 (https://github.com/lcorito-oss)' },
      cf: { cacheEverything: true, cacheTtlByStatus: { '200-299': ttl, '400-599': 0 } }
    });

    return new Response(r.body, {
      status: r.status,
      headers: {
        'content-type': r.headers.get('content-type') || 'text/plain',
        'access-control-allow-origin': '*',
        'cache-control': 'public, max-age=' + (isTLE ? 3600 : 20)
      }
    });
  }
}
```

4. En `index.html`, la constante `PROXY` debe apuntar a la dirección de tu Worker, terminada en `/?url=`:

```js
const PROXY='https://aviones.TU-USUARIO.workers.dev/?url=';
```

El Worker solo acepta consultas a los cuatro dominios permitidos, así que no funciona como proxy abierto. El plan gratuito de Cloudflare incluye 100,000 peticiones al día, y esta página hace unas 1,440.

### Comprobar que funciona

Abre en una pestaña (cambia el dominio por el tuyo):

```
https://aviones.TU-USUARIO.workers.dev/?url=https%3A%2F%2Fapi.adsb.lol%2Fv2%2Fpoint%2F26.8667%2F-105.85%2F100
```

Debe mostrar un JSON con `"msg": "No error"`. La lista `"ac"` puede venir vacía si no hay aeronaves en el radio. Si responde `bad request`, revisa el código del Worker; si responde `User-Agent too generic`, falta el encabezado `User-Agent`.

## Notas de uso

- **Órbitas de satélites:** CelesTrak solo actualiza sus datos cada unas 2 horas y bloquea temporalmente a quien los pide con más frecuencia. Por eso la página guarda las órbitas en el navegador por 2 horas y calcula las posiciones cada minuto con esos datos; no las vuelve a descargar.
- **Cobertura ADS-B:** las aeronaves provienen de receptores voluntarios. En esta zona la cobertura es escasa, por lo que es normal ver pocas aeronaves o ninguna.
- **Errores:** si una fuente falla, su tarjeta muestra un mensaje corto y se intenta de nuevo en la siguiente actualización. El detalle técnico queda en la consola del navegador (F12).

## Fuentes y atribuciones

- Catálogo de estrellas: subconjunto de estrellas brillantes del catálogo Hipparcos, reutilizado del proyecto [reloj](https://github.com/lcorito-oss/reloj).
- Posiciones astronómicas: [Astronomy Engine](https://github.com/cosinekitty/astronomy) (licencia MIT).
- Propagación de órbitas: [satellite.js](https://github.com/shashwatak/satellite-js) (licencia MIT).
- Órbitas de satélites y estaciones: [CelesTrak GP](https://celestrak.org/NORAD/elements/).
- Fechas globales de eclipses: [NASA Eclipse Web Site](https://eclipse.gsfc.nasa.gov/).
- Tráfico aéreo: [adsb.lol](https://github.com/adsblol/api), datos abiertos bajo licencia [ODbL](https://opendatacommons.org/licenses/odbl/). Como respaldo se consultan también [adsb.fi](https://adsb.fi/) y [airplanes.live](https://airplanes.live/), aunque estos pueden rechazar peticiones desde Cloudflare Workers.
- Calendario de lluvias: [American Meteor Society](https://www.amsmeteors.org/meteor-showers/meteor-shower-calendar/).

Los servicios externos pueden cambiar sus límites, disponibilidad o formatos.

## Licencia

MIT. Consulta el archivo `LICENSE`.
