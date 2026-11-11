# Geodesia — sistemas de referencia y proyecciones (Colombia)

Fuente: Resolución IGAC 068 de 2005 (adopción de MAGNA-SIRGAS); Resolución IGAC
715 de 2018 (MAGNA-SIRGAS 2018); Resolución IGAC 370 de 2021 (proyección CTM12,
Origen Nacional); Resolución IGAC 0515 de 2026 (modelo geoidal QGeoidCOL2023);
norma ISO 19111 (sistemas de referencia por coordenadas); documentación de EPSG.

## 1. Elipsoide, datum y sistema de referencia

- **Elipsoide**: modelo matemático de la Tierra (semieje mayor `a` y
  aplanamiento `f`). Colombia usa el **GRS80**.
- **Datum**: elipsoide + posición y orientación respecto a la Tierra real.
- **Sistema de referencia**: datum + convenciones (unidades, época, ejes).

Cambiar de datum cambia las coordenadas aunque el punto físico sea el mismo. En
Colombia, además, la corteza se mueve: por eso hay que decir **en qué época** se
midió y a qué época se reducen las coordenadas.

## 2. Marcos habituales en Colombia

| Marco | Uso | Notas |
| --- | --- | --- |
| ITRF2014 | global, época variable | base científica; MAGNA-SIRGAS 2018 está referido a él |
| MAGNA-SIRGAS 2018 | oficial en Colombia | ITRF2014, época 2018.0; Resolución IGAC 715 de 2018 |
| MAGNA-SIRGAS (original) | cartografía anterior a 2018 | ITRF94, época 1995.4; Resolución IGAC 068 de 2005 |
| WGS84 | GPS/GNSS | casi coincide con MAGNA-SIRGAS a nivel centimétrico, pero no es idéntico |
| Datum Bogotá (1941) | cartografía antigua | origen en el Observatorio Astronómico Nacional |
| Bogotá 1975 | cartografía antigua | EPSG 21894 a 21899; requiere transformación |

**MAGNA** significa Marco Geocéntrico Nacional de Referencia y es una
densificación de **SIRGAS**, que a su vez es una realización regional del ITRF.
No son tres sistemas independientes: son el mismo marco con distinta densidad de
estaciones.

Un levantamiento GNSS en WGS84 y una cartografía en MAGNA-SIRGAS pueden tratarse
como equivalentes dentro de pocos centímetros, pero **hay que decirlo**, no
asumirlo, y hay que declarar la época.

## 3. Proyección CTM12 (Origen Nacional)

Proyección **Transverse Mercator** oficial para todo el país, adoptada por la
Resolución IGAC 370 de 2021. Parámetros:

| Parámetro | Valor |
| --- | --- |
| Elipsoide | GRS80 |
| Latitud de origen | 4° N |
| Longitud del meridiano central | 73° O |
| Factor de escala en el meridiano central | `k0 = 0,9992` |
| Falso este | 5.000.000 m |
| Falso norte | 2.000.000 m |
| Código EPSG | 9377 |

El falso este de 5.000.000 m es lo que permite que todo el país quede con
coordenadas Este positivas aunque esté al oeste de Greenwich.

## 4. Zonas Gauss-Krüger

La cartografía anterior a CTM12 usaba cinco zonas Gauss-Krüger de MAGNA-SIRGAS,
cada una con su meridiano central:

| EPSG | Zona |
| --- | --- |
| 3114 | MAGNA-SIRGAS / Colombia Far West zone |
| 3115 | MAGNA-SIRGAS / Colombia West zone |
| 3116 | MAGNA-SIRGAS / Colombia Bogotá zone |
| 3117 | MAGNA-SIRGAS / Colombia East Central zone |
| 3118 | MAGNA-SIRGAS / Colombia East zone |

La zona Bogotá (EPSG 3116) reemplazó a la antigua Bogotá 1975 / Colombia Bogota
zone (EPSG 21897). A su vez, MAGNA-SIRGAS 2018 tiene su propia versión de la zona
Bogotá: **EPSG 11116**.

## 5. Códigos EPSG que conviene saber

| EPSG | Nombre | Uso |
| --- | --- | --- |
| 4326 | WGS84 geográficas | GPS, GeoJSON, web |
| 4686 | MAGNA-SIRGAS geográficas | Colombia, lat/lon |
| 9377 | MAGNA-SIRGAS / CTM12 | proyección oficial del país |
| 3114–3118 | MAGNA-SIRGAS / Gauss-Krüger | cartografía por zonas |
| 11116 | MAGNA-SIRGAS 2018 / zona Bogotá | cartografía actual de Bogotá |
| 21894–21899 | Bogotá 1975 | cartografía antigua |
| 32617 | WGS84 / UTM 17N | oeste de Colombia |
| 32618 | WGS84 / UTM 18N | centro de Colombia |
| 32619 | WGS84 / UTM 19N | este de Colombia |
| 3857 | Web Mercator | teselas web (Google, OSM) |

Regla práctica: para medir distancias o áreas usa **CTM12** o la zona
Gauss-Krüger que corresponda; para almacenar o intercambiar usa **geográficas**
(4686) o el CRS del proyecto, y no mezcles CRS en la misma capa.

## 6. Geoide y altitudes

- **Geoide**: superficie equipotencial que coincide con el nivel medio del mar.
- **Ondulación (N)**: diferencia entre el elipsoide y el geoide.
- **Altitud elipsoidal (h)** y **ortométrica (H)**: `h ≈ H + N`.

Colombia usa el modelo geoidal **QGeoidCOL2023** (Resolución IGAC 0515 de 2026),
con grilla de 5′ × 5′, que reemplaza al anterior **GEOCOL2004** (grilla de
2′ × 2′). Su precisión reportada es una desviación estándar de 21,27 cm.

Mezclar cotas ortométricas con elipsoidales produce errores de decenas de metros,
no de centímetros. Siempre hay que indicar el modelo usado.

Ejemplo hipotético (no es un valor de Colombia, sirve solo para ver el signo):

- GNSS da `h = 1000 m` y el modelo da `N = +20 m`.
- Cota ortométrica: `H = h − N = 1000 − 20 = 980 m`.
- Comprobación: `h = H + N = 980 + 20 = 1000 m`.

Si `N` fuese negativa (por ejemplo `N = −20 m`), entonces
`H = 1000 − (−20) = 1020 m`, es decir, la cota ortométrica **sube**.
