# Geodesia — sistemas de referencia y proyecciones

Fuente: RD 1071/2007 (sistema de referencia geodésico oficial en España,
ETRS89 y REGCAN95); normas ISO 19111 (sistemas de referencia por coordenadas);
documentación de EPSG.

## 1. Elipsoide, datum y sistema de referencia

- **Elipsoide**: modelo matemático de la Tierra (semieje mayor `a` y
  aplanamiento `f`). El más usado hoy: GRS80 y WGS84.
- **Datum**: elipsoide + posición y orientación respecto a la Tierra real.
- **Sistema de referencia**: datum + convenciones (unidades, época, ejes).

Cambiar de datum cambia las coordenadas aunque el punto físico sea el mismo.
Ese es el origen de la mayoría de los "desplazamientos" de 100–200 m entre
mapas antiguos y actuales.

## 2. Marcos habituales

| Marco | Uso | Notas |
| --- | --- | --- |
| ITRF | global, época variable | base científica, se actualiza por época |
| ETRS89 | oficial en Europa | fijado a la placa euroasiática, no deriva |
| WGS84 | GPS/GNSS | casi coincide con ETRS89 a nivel centimétrico en Europa |
| ED50 | cartografía antigua española | datum europeo de 1950, requiere transformación |
| REGCAN95 | Canarias | sistema propio, no ETRS89 |

En España, ETRS89 es el sistema legal para cartografía y geodesia desde 2007.
Un levantamiento GNSS en WGS84 y una cartografía en ETRS89 pueden tratarse como
equivalentes en Europa dentro de pocos centímetros, pero **hay que decirlo**,
no asumirlo.

## 3. Proyección UTM

Proyección conforme cilíndrica transversal. Parámetros:

- **Huso**: 60 husos de 6° de longitud; huso 30 cubre 6° O a 0°.
- **Factor de escala** en el meridiano central: `k0 = 0,9996`.
- **Falso este**: 500.000 m.
- **Falso norte**: 0 en el hemisferio norte; 10.000.000 m en el sur.

Coordenadas UTM típicas en España peninsular (husos 29, 30, 31):

```
X ≈ 200.000 – 800.000 m
Y ≈ 4.000.000 – 4.800.000 m
```

Ejemplo: Madrid, huso 30, ETRS89/UTM30N → EPSG:25830.

## 4. Códigos EPSG que conviene saber

| EPSG | Nombre | Uso |
| --- | --- | --- |
| 4326 | WGS84 geográficas | GPS, GeoJSON, web |
| 4258 | ETRS89 geográficas | España, lat/lon |
| 25830 | ETRS89 / UTM zona 30N | España peninsular centro |
| 25829 | ETRS89 / UTM zona 29N | oeste peninsular |
| 25831 | ETRS89 / UTM zona 31N | este peninsular |
| 23030 | ED50 / UTM zona 30N | cartografía antigua |
| 3857 | Web Mercator | teselas web (Google, OSM) |

Regla práctica: para medir distancias o áreas usa **UTM**; para almacenar o
intercambiar usa **geográficas** o el CRS del proyecto, y no mezcles CRS en la
misma capa.

## 5. Geoide y altitudes

- **Geoide**: superficie equipotencial que coincide con el nivel medio del mar.
- **Ondulación (N)**: diferencia entre el elipsoide y el geoide.
- **Altitud elipsoidal (h)** y **ortométrica (H)**: `h ≈ H + N`.

En España la ondulación ronda los 50 m (positiva, es decir, el geoide queda por
encima del elipsoide WGS84). Mezclar cotas ortométricas con elipsoidales produce
errores de decenas de metros, no de centímetros. Rangos como "−100 a −200 m"
para España son incorrectos.

Ejemplo correcto (úsalo tal cual, no lo recalcules a ojo):

- GNSS da `h = 1000 m` y el modelo da `N = +50 m`.
- Cota ortométrica: `H = h − N = 1000 − 50 = 950 m`.
- Comprobación: `h = H + N = 950 + 50 = 1000 m`.

Si `N` fuese negativa (por ejemplo `N = −20 m`), entonces
`H = 1000 − (−20) = 1020 m`, es decir, la cota ortométrica **sube**.
