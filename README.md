# Cielo actual

Mapa astronómico en vivo para San Francisco del Oro, Chihuahua (26°52′ N, 105°51′ O).

## Qué muestra

- Estrellas sobre el horizonte local, proyectadas con el cenit al centro; incluye una banda tenue de la Vía Láctea y galaxias sin etiquetas encima del mapa.
- Posiciones de la Luna y los planetas calculadas para la hora actual.
- Satélites del grupo visual de CelesTrak sobre el horizonte, con estelas de hasta 7 minutos y coordenadas de azimut/altitud resaltables desde la lista.
- Próxima pasada de la Estación Espacial Internacional calculada con los elementos orbitales actuales de CelesTrak.
- Aeronaves cercanas reportadas por ADS-B cuando hay receptores con cobertura, también con estelas de hasta 7 minutos.
- Eventos celestes: cuartos lunares, Luna del cazador, eclipses próximos y detección de agrupaciones Starlink cuando aparecen en los datos visibles.
- Lluvias de meteoros activas según su calendario estacional. Los meteoros individuales no se pueden anticipar.

El mapa se actualiza cada minuto. La disponibilidad de satélites y aeronaves depende de los servicios y de la cobertura de datos. La aplicación no necesita claves privadas.

## Publicar con GitHub Pages

En el repositorio, abre **Settings → Pages**, selecciona la rama `main` y la carpeta `/ (root)`. GitHub Pages publicará `index.html`.

## Fuentes y atribuciones

- Catálogo de estrellas: subconjunto de estrellas brillantes del catálogo Hipparcos, reutilizado del proyecto [reloj](https://github.com/lcorito-oss/reloj).
- Posiciones astronómicas: [Astronomy Engine](https://github.com/cosinekitty/astronomy).
- Órbitas de satélites y estaciones: [CelesTrak GP](https://celestrak.org/NORAD/elements/).
- Fechas globales de eclipses: [NASA Eclipse Web Site](https://eclipse.gsfc.nasa.gov/).
- Tráfico aéreo: [adsb.lol](https://github.com/adsblol/api).
- Calendario de lluvias: [American Meteor Society](https://www.amsmeteors.org/meteor-showers/meteor-shower-calendar/).

Los servicios externos pueden cambiar sus límites, disponibilidad o formatos. Si una fuente falla, su sección mostrará un mensaje y se intentará de nuevo en la siguiente actualización.
