# Catastro — conceptos básicos

Fuente: Ley 13/2015 (reforma de la Ley Hipotecaria y del Texto Refundido de la
Ley del Catastro Inmobiliario, España); texto consolidado del Real Decreto
Legislativo 1/2004 (TRLCI). Los detalles autonómicos y los de otros países
deben añadirse como archivos propios.

## 1. Qué es el catastro

Es el registro administrativo que describe los **bienes inmuebles** de un
término municipal: su geometría, su superficie, su uso y su titularidad a
efectos fiscales y de gestión. No confundir con el Registro de la Propiedad,
que es un registro jurídico de derechos.

## 2. La parcela catastral

- **Parcela**: porción de suelo delimitada por una línea poligonal cerrada, con
  una referencia catastral única.
- **Polígono**: agrupación de parcelas dentro de una hoja del plano catastral.
  El identificador típico es `polígono / parcela`.
- **Manzana**: en suelo urbano, el conjunto de parcelas delimitado por calles.
- **Hoja catastral**: documento gráfico donde se dibujan parcelas y polígonos.

## 3. Referencia catastral

Identificador alfanumérico de 20 caracteres que individualiza un inmueble. Se
compone de:

| Bloque | Contenido |
| --- | --- |
| 7 caracteres | finca o parcela |
| 7 caracteres | hoja del plano |
| 4 caracteres | inmueble dentro de la parcela |
| 2 caracteres | control |

Ejemplo de forma: `1234567 VG1234N 0001 WX`. Es obligatoria en escrituras,
contratos y trámites con inmuebles.

## 4. Linderos y deslinde

- **Lindero**: línea que separa dos parcelas o una parcela de un camino, río o
  límite administrativo.
- **Deslinde**: procedimiento para fijar linderos cuando hay duda o conflicto.
- **Amojonamiento**: materialización física de los linderos con hitos o mojones.

## 5. Cartografía catastral

Dos productos habituales:

1. **Cartografía vectorial** (parcelario): polígonos con atributos, editable en
   SIG. Es la base para QGIS, GeoPackage o PostGIS.
2. **Ortofoto / PNOA**: imagen aérea georreferenciada que se usa como referencia
   visual para comprobar linderos.

La cartografía catastral se apoya en la red geodésica; por eso la calidad de un
levantamiento depende del sistema de referencia (ver dominio de geodesia).

## 6. Errores frecuentes del principiante

- Confundir referencia catastral con número de finca registral.
- Comparar superficies sin decir si son gráficas o construidas.
- Suponer que el catastro "da fe" de la propiedad: no la da, la describe.
