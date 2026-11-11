# Catastro — conceptos básicos (Colombia)

Fuente: Ley 44 de 1990 (impuesto predial unificado y catastro); Ley 1995 de
2019 (normas catastrales); Ley 1955 de 2019, artículos 79 a 82 (catastro
multipropósito); Decreto 148 de 2020 (NUPRE); Resolución IGAC 1149 de 2021
(estructura del NPN); Resolución IGAC 388 de 2020, modificada por la
Resolución IGAC 679 de 2022 (especificaciones del levantamiento catastral);
Código Civil, artículos 900 y siguientes; Código General del Proceso,
artículos 400 a 405.

## 1. Qué es el catastro

Es el inventario o censo de los **predios** de un municipio: su geometría, su
superficie, su uso, su avalúo y su titularidad, con fines fiscales y de
ordenamiento. El **IGAC** es la máxima autoridad catastral del país; los
**gestores catastrales** y **operadores catastrales** ejecutan la gestión en
cada municipio.

Desde la Ley 1955 de 2019 se habla de **catastro multipropósito**: el mismo
inventario sirve para lo físico, lo jurídico, lo económico, lo fiscal y el
ordenamiento territorial. No confundir con el **registro de la propiedad**, que
lleva la **SNR** a través de las **ORIP** y que sí da fe de los derechos
inscritos en el **folio de matrícula inmobiliaria**.

## 2. Predio y unidades de terreno

- **Predio**: inmueble delimitado por un lindero cerrado, con o sin
  construcciones, identificado con un **NPN**.
- **Mejora**: construcción o cultivo que no constituye por sí mismo un predio.
- **Unidad predial**: cada una de las unidades independientes que resultan de
  una **propiedad horizontal**.
- **Manzana**: en suelo urbano, el conjunto de predios delimitado por vías.
- **Sector**, **comuna** y **barrio**: divisiones urbanas que forman parte de la
  clave del NPN. En suelo rural se usan **sector** y **vereda**.

## 3. Número Predial Nacional (NPN)

Identificador de **30 dígitos** que individualiza cada predio en todo el país
(Resolución IGAC 1149 de 2021). Antes de esa resolución el número predial tenía
20 dígitos. Sus componentes son:

| Dígitos | Componente |
| --- | --- |
| 1–2 | departamento |
| 3–5 | municipio |
| 6–7 | zona |
| 8–9 | sector |
| 10–11 | comuna |
| 12–13 | barrio |
| 14–17 | manzana o vereda |
| 18–21 | número de terreno |
| 22 | condición del predio |
| 23–30 | número de construcción |

La posición exacta de cada componente debe confirmarse en la Resolución IGAC
1149 de 2021 y en el Anexo D del ICDE: algunos glosarios del IGAC publican
variantes. Lo que sí es estable: son 30 dígitos, los dos primeros identifican el
departamento y los ocho últimos, la construcción.

## 4. NUPRE

El **NUPRE** (Número Único Predial y Registral) es el identificador que enlaza
catastro y registro. Su estructura es `ABC-1234-DEFH` (Decreto 148 de 2020):
cuatro grupos separados por guiones, con letras y números.

## 5. Linderos, deslinde y amojonamiento

- **Lindero**: línea que separa dos predios, o un predio de una vía, un río o un
  límite administrativo.
- **Deslinde y amojonamiento**: proceso para fijar y materializar linderos
  dudosos. En Colombia es un proceso **judicial** regulado por los artículos 400
  a 405 del Código General del Proceso; el artículo 401 enumera sus requisitos y
  exige, entre otros, los certificados de tradición y libertad de los
  colindantes. El Código Civil lo trata en los artículos 900 y siguientes.
- En la vía administrativa, el catastro no "deslinda" propiedad: hace
  **rectificación de linderos** o **actualización** con levantamiento topográfico
  y, si hay conflicto entre titulares, remite al proceso judicial.

## 6. Cartografía catastral

- **Modelo LADM_COL**: modelo de datos del catastro colombiano, derivado del
  estándar LADM (ISO 19152). Las entidades clave del levantamiento son
  `LC_PuntoLindero` y `LC_PuntoLevantamiento`.
- **Cartografía vectorial**: polígonos de predios con atributos, editable en SIG
  (QGIS, GeoPackage, PostGIS).
- **Ortofoto**: imagen aérea georreferenciada del IGAC, usada como referencia
  visual para comprobar linderos.

La exactitud posicional exigida depende de la escala y del tipo de suelo
(Resolución IGAC 388 de 2020, modificada por la 679 de 2022). Se mide con el
error circular al 95 %:

| Escala | Suelo | Error máximo al 95 % |
| --- | --- | --- |
| 1:1.000 | urbano | 0,52 m |
| 1:2.000 | urbano | 1,04 m |
| 1:5.000 | rural con comportamiento urbano | 2,60 m |
| 1:10.000 | rural | 5,2 m |
| 1:25.000 | bosque | 13,01 m |

## 7. Errores frecuentes del principiante

- Confundir el **NPN** con el **folio de matrícula inmobiliaria**: el primero es
  catastral y fiscal; el segundo es registral y jurídico.
- Confundir **NPN** con **NUPRE**: el NPN identifica el predio en catastro; el
  NUPRE enlaza catastro y registro.
- Comparar superficies sin decir si son catastrales, construidas o registrales.
- Suponer que el catastro "da fe" de la propiedad: no la da, la describe.
- Usar un NPN de 20 dígitos en trámites actuales: desde la Resolución IGAC 1149
  de 2021 son 30 dígitos.
