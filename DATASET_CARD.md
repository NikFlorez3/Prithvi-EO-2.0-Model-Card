---
pretty_name: Harmonized Landsat and Sentinel-2 (HLS)
license: otras (datos de dominio público NASA/USGS; Sentinel-2 bajo Copernicus)
language:
  - es
tags:
  - earth-observation
  - geospatial
  - remote-sensing
  - multispectral
  - surface-reflectance
  - landsat
  - sentinel-2
  - HLS
  - NASA
size_categories:
  - >10M
task_categories:
  - image-segmentation
  - image-classification
  - time-series-forecasting
---

# Ficha del conjunto de datos — Harmonized Landsat and Sentinel-2 (HLS)

> Documentación académica elaborada adaptando la plantilla oficial de *Dataset Cards* de Hugging Face.
> Esta ficha describe un conjunto de datos público de NASA; **no** es un conjunto creado por la autora.

---

## Descripción general del conjunto de datos

**HLS (Harmonized Landsat and Sentinel-2)** es un conjunto de datos de **reflectancia superficial
armonizada y lista para análisis**, generado por NASA a partir de observaciones de los satélites **Landsat 8
y Landsat 9** (NASA/USGS; sensores OLI/OLI-2) y **Sentinel-2A, 2B y 2C** (ESA; sensor MSI). La
armonización permite emplear ambas constelaciones como si fueran una sola colección, ofreciendo observaciones
de la superficie terrestre a **30 m de resolución** con una **frecuencia de revisita global del orden de
2–3 días** (mediana cercana a 2 días con cinco satélites).

HLS constituye el insumo de preentrenamiento de **Prithvi-EO-2.0**; véase la
[ficha del modelo](MODEL_CARD.md) para su relación con el modelo.

---

## 1. Detalles del conjunto de datos

### 1.1 Descripción

- **Curaduría / responsable:** NASA (HLS Science Team; algoritmo desarrollado en NASA Goddard Space Flight
  Center y producido en NASA Marshall Space Flight Center, a través del equipo IMPACT).
- **Producción y archivo:** NASA Land Processes Distributed Active Archive Center (**LP DAAC**), en
  asociación con el USGS.
- **Colaboradores:** USGS (Landsat) y ESA (Sentinel-2 / programa Copernicus).
- **Licencia:** datos abiertos y de acceso libre de NASA/USGS; Sentinel-2 se distribuye bajo la política de
  datos abiertos de Copernicus (ESA). El acceso requiere registro en Earthdata Login.
- **Idioma de la documentación:** inglés (documentación oficial); esta ficha en español.

### 1.2 Fuentes

- **Repositorio / portal de datos:** https://www.earthdata.nasa.gov/data/projects/hls
- **Sitio del equipo científico HLS:** https://hls.gsfc.nasa.gov/
- **Acceso a datos (Earthdata Search):** https://search.earthdata.nasa.gov/search?q=HLS
- **Artículo principal:** Claverie et al. (2018), *Remote Sensing of Environment*, 219, 145–161.

---

## 2. Usos

### 2.1 Usos previstos (directos)

- Preentrenamiento y **ajuste fino (*fine-tuning*)** de modelos fundacionales de observación de la Tierra
  (por ejemplo, **Prithvi-EO-2.0**).
- Monitoreo **agrícola** y de salud de cultivos a escala de parcela.
- Clasificación de **coberturas del suelo** y cambios de uso.
- Seguimiento de **perturbaciones**: cicatrices de incendios, inundaciones, daños por tormentas.
- Análisis de **series temporales** de la superficie terrestre (fenología, productividad).
- Derivación de **índices de vegetación** (productos HLS-VI).

### 2.2 Usos fuera de alcance

- Aplicaciones que requieran **resolución sub-30 m** sin datos complementarios.
- **Observación nocturna o bajo nubes** (HLS es óptico; se requieren datos radar para esos casos).
- **Decisiones críticas** sin validación técnica ni control de calidad local.
- Estimación de variables que requieran bandas no incluidas o condiciones no capturadas por sensores
  ópticos de reflectancia.

---

## 3. Estructura del conjunto de datos

### 3.1 Productos

| Producto | Sensor de origen | Descripción |
| --- | --- | --- |
| **HLSL30** | Landsat 8/9 (OLI/OLI-2) | Reflectancia superficial y temperatura de brillo; remuestreada a 30 m |
| **HLSS30** | Sentinel-2A/2B/2C (MSI) | Reflectancia superficial; remuestreada a 30 m y ajustada a la respuesta espectral de Landsat |
| **HLS-VI** | Derivado de L30 y S30 | Suites de índices de vegetación (desde febrero de 2025) |
| **HLS-LL** | Derivado | Productos de baja latencia (previstos para ~2027) |

### 3.2 Bandas espectrales

Correspondencia entre las nomenclaturas L30 y S30 y el rango espectral:

| Nombre de la banda | Código L30 | N.º OLI | Código S30 | N.º MSI | Longitud de onda (µm) |
| --- | --- | --- | --- | --- | --- |
| Coastal Aerosol | band01 | 1 | B01 | 1 | 0,43 – 0,45 |
| Blue (Azul) | band02 | 2 | B02 | 2 | 0,45 – 0,51 |
| Green (Verde) | band03 | 3 | B03 | 3 | 0,53 – 0,59 |
| Red (Rojo) | band04 | 4 | B04 | 4 | 0,64 – 0,67 |
| Red-Edge 1 | — | — | B05 | 5 | 0,69 – 0,71 |
| Red-Edge 2 | — | — | B06 | 6 | 0,73 – 0,75 |
| Red-Edge 3 | — | — | B07 | 7 | 0,77 – 0,79 |
| NIR Broad (NIR ancho) | — | — | B08 | 8 | 0,78 – 0,88 |
| NIR Narrow (NIR estrecho) | band05 | 5 | B8A | 8A | 0,85 – 0,88 |
| SWIR 1 | band06 | 6 | B11 | 11 | 1,57 – 1,65 |
| SWIR 2 | band07 | 7 | B12 | 12 | 2,11 – 2,29 |
| Water Vapor (vapor de agua) | — | — | B09 | 9 | 0,93 – 0,95 |
| Cirrus | band09 | 9 | B10 | 10 | 1,36 – 1,38 |
| Thermal Infrared 1 | band10 | 10 | — | — | 10,60 – 11,19 |
| Thermal Infrared 2 | band11 | 11 | — | — | 11,50 – 12,51 |

> Las **seis bandas** empleadas por Prithvi-EO-2.0 son: Azul, Verde, Rojo, NIR estrecho (band05 / B8A),
> SWIR 1 (band06 / B11) y SWIR 2 (band07 / B12).

### 3.3 Formato y organización

- **Formato:** GeoTIFF optimizado para la nube (**COG**).
- **Teselado:** cuadrícula **MGRS** (idéntica a la de Sentinel-2), con teselas de ~**109,8 km** y solape de
  **4.900 m** por lado, en proyección **UTM**.
- **Convención de nombres (ejemplo):** `HLS.S30.T60HTE.2022103T222539.v2.0.B01.tif`
  - `HLS.S30` — nombre del producto;
  - `T60HTE` — identificador de tesela MGRS;
  - `2022103` — fecha juliana de adquisición (AAAADDD);
  - `T222539` — hora de adquisición (HHMMSS);
  - `v2.0` — versión de la colección;
  - `B01` — variable (banda espectral, banda de ángulos o índice de vegetación);
  - `tif` — formato.
- **Metadatos:** metadatos embebidos en el COG (georreferenciación, bandas espectrales, bandas de calidad
  —QA—) y un archivo externo `.met` en formato XML.

---

## 4. Creación del conjunto de datos

### 4.1 Justificación

HLS surge como resultado de la evaluación del **Satellite Needs Working Group (SNWG)** de 2016, en la que
agencias federales y usuarios finales identificaron la necesidad de observaciones tipo Landsat de mayor
frecuencia para monitorear cambios de corto plazo en la vegetación y otras componentes de la superficie
terrestre. Las similitudes espectrales entre OLI/OLI-2 y MSI hacen posible armonizar ambos sensores.

### 4.2 Datos de origen y procesamiento

- **Entradas:** productos Landsat 8/9 (Collection 2, nivel 1T: reflectancia de techo de atmósfera) y
  Sentinel-2 (L1C: reflectancia de techo de atmósfera).
- **Cadena de procesamiento:** correcciones radiométricas y geométricas; conversión a **reflectancia
  superficial**; ajuste **BRDF**; ajuste de **ancho de banda espectral**; remuestreo a una base común UTM de
  **30 m** y teselado en la cuadrícula MGRS de Sentinel-2.
- **Resultado:** dos productos principales (**L30** y **S30**) radiométricamente armonizados, con
  observaciones comparables entre sensores.

### 4.3 Productores de los datos de origen

Los datos de origen son producidos por **NASA/USGS** (Landsat) y la **Agencia Europea (ESA)** mediante el
programa **Copernicus** (Sentinel-2). La armonización y distribución la realiza **NASA** a través del
**LP DAAC**.

---

## 5. Sesgos, riesgos y limitaciones

- **Cobertura espacial:** cobertura global de tierras emergidas **excepto la Antártida** (versión 2.0);
  versiones anteriores tuvieron cobertura limitada.
- **Sesgo óptico:** nubes, sombras, aerosoles y nieve pueden reducir la disponibilidad de observaciones
  válidas; las regiones persistentemente nubladas están infrarrepresentadas.
- **Diferencias residuales entre sensores:** la armonización reduce, pero no elimina, diferencias
  radiométricas residuales entre Landsat y Sentinel-2.
- **Resolución limitada:** 30 m; no adecuada por sí sola para objetos sub-métricos.
- **Sesgos estacionales y geográficos:** la distribución temporal y espacial de las observaciones no es
  uniforme.
- **Latencia:** la disponibilidad puede tener retraso (del orden de días); los productos de baja latencia
  (HLS-LL) aún no están finalizados.

### Recomendaciones

- Aplicar **máscaras de calidad (QA)** y control de nubosidad antes del análisis.
- Documentar la **versión de la colección** y las fechas de adquisición utilizadas.
- Validar los resultados con **datos de referencia locales**.
- Considerar las **diferencias de sensor** al comparar series derivadas de L30 y S30.
- Tener en cuenta la **cobertura del registro histórico** (Landsat desde 2013; Sentinel-2 desde 2015).

---

## 6. Cita

**APA 7**

Claverie, M., Ju, J., Masek, J. G., Dungan, J. L., Vermote, E. F., Roger, J.-C., Skakun, S. V., &
Justice, C. (2018). The Harmonized Landsat and Sentinel-2 surface reflectance data set. *Remote Sensing of
Environment, 219*, 145–161. https://doi.org/10.1016/j.rse.2018.09.002

**BibTeX**

```bibtex
@article{claverie2018hls,
  author  = {Claverie, Martin and Ju, Junchang and Masek, Jeffrey G. and Dungan, Jennifer L. and
             Vermote, Eric F. and Roger, Jean-Claude and Skakun, Sergii V. and Justice, Christopher},
  title   = {The Harmonized Landsat and Sentinel-2 surface reflectance data set},
  journal = {Remote Sensing of Environment},
  volume  = {219},
  pages   = {145--161},
  year    = {2018},
  doi     = {10.1016/j.rse.2018.09.002}
}
```

---

## 7. Glosario

- **Reflectancia superficial:** proporción de radiación reflejada por la superficie, corregida por efectos
  atmosféricos.
- **COG (*Cloud Optimized GeoTIFF*):** GeoTIFF con estructura interna optimizada para acceso en la nube.
- **MGRS (*Military Grid Reference System*):** sistema de referencia de cuadrícula militar empleado por
  Sentinel-2 para el teselado.
- **BRDF:** función de distribución de reflectancia bidireccional, usada para normalizar efectos de ángulo
  de observación/iluminación.
- **L30 / S30:** productos HLS derivados de Landsat y Sentinel-2, respectivamente.
- **Revisita:** intervalo temporal medio entre observaciones de un mismo lugar.

---

## 8. Más información

- HLS en NASA Earthdata: https://www.earthdata.nasa.gov/data/projects/hls
- Bandas espectrales HLS: https://www.earthdata.nasa.gov/data/projects/hls/spectral-bands
- Equipo científico HLS: https://hls.gsfc.nasa.gov/
- Recursos y tutoriales (NASA): https://github.com/nasa/HLS-Data-Resources

---

## 9. Autoría de la ficha

- **Autora:** Nikole Stefhanie Flórez Londoño
- **Universidad:** Universidad Distrital Francisco José de Caldas
- **Programa:** Ingeniería Catastral y Geodesia
- **Fecha:** 14 de octubre de 2026

**Declaración.** La autora elaboró esta ficha con fines exclusivamente académicos; **no** es productora ni
desarrolladora del conjunto de datos HLS, cuyo mérito corresponde a NASA, USGS y ESA. La información se basa
en la documentación pública verificable de NASA y en el artículo de Claverie et al. (2018).
