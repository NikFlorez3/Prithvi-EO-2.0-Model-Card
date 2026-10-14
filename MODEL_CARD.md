---
license: apache-2.0
language:
  - es
tags:
  - earth-observation
  - geospatial
  - remote-sensing
  - foundation-model
  - vision-transformer
  - masked-autoencoder
  - multi-temporal
  - HLS
  - IBM
  - NASA
datasets:
  - NASA HLS v2 (Harmonized Landsat and Sentinel-2)
base_model: Prithvi-EO-1.0
pipeline_tag: image-segmentation
---

# Ficha técnica del modelo — Prithvi-EO-2.0

> Documentación académica elaborada siguiendo la metodología de *Model Cards for Model Reporting*
> (Mitchell et al., 2019) y adaptando la plantilla oficial de *Model Cards* de Hugging Face.
> Esta ficha describe el modelo original desarrollado por IBM, NASA y el Centro de Supercomputación
> de Jülich; **no** es un desarrollo propio de la autora de la ficha.

---

## 1. Nombre e identificación del modelo

| Campo | Valor |
| --- | --- |
| **Nombre del modelo** | Prithvi-EO-2.0 |
| **Familia / linaje** | Prithvi (modelos fundacionales geoespaciales de IBM–NASA); antecesor: Prithvi-EO-1.0 |
| **Versión** | 2.0 (segunda generación) |
| **Tipo de modelo** | Modelo fundacional geoespacial (*geospatial foundation model*) para teledetección, basado en *Vision Transformer* (ViT) preentrenado con *Masked Autoencoder* (MAE) |
| **Modalidad** | Imágenes satelitales multiespectrales y multitemporales (datos ópticos de reflectancia superficial) |
| **Identificador en Hugging Face** | `ibm-nasa-geospatial/Prithvi-EO-2.0-300M` y variantes asociadas |
| **Licencia** | Apache-2.0 |
| **Repositorio de código** | https://github.com/NASA-IMPACT/Prithvi-EO-2.0 |
| **Biblioteca de referencia** | TerraTorch (IBM): https://github.com/IBM/terratorch |
| **Artículo científico** | Szwarcman et al. (2024), arXiv:2412.02732 — https://arxiv.org/abs/2412.02732 |
| **DOI del artículo** | https://doi.org/10.48550/arXiv.2412.02732 |
| **Fecha de publicación** | Diciembre de 2024 |
| **Demo interactiva** | https://huggingface.co/spaces/ibm-nasa-geospatial/Prithvi-EO-2.0-Demo |

---

## 2. Desarrolladores y organizaciones responsables

El modelo **Prithvi-EO-2.0** fue desarrollado conjuntamente por:

- **IBM Research**
- **NASA** (NASA Marshall Space Flight Center y el equipo NASA IMPACT)
- **Jülich Supercomputing Centre (JSC)**

El trabajo contó además con la participación de las siguientes instituciones académicas y de investigación:
The University of Alabama in Huntsville, University of Iceland, Virginia Tech, Arizona State University,
Oregon State University, Clark University, Boston University, University of California, Berkeley y
Earth from Space Institute.

**Autoría científica (selección).** El artículo que introduce el modelo está firmado, entre otros, por:
Daniela Szwarcman, Sujit Roy, Paolo Fraccaro, Þorsteinn Elí Gíslason, Benedikt Blumenstiel, Rinki Ghosal,
Pedro Henrique de Oliveira, João Lucas de Sousa Almeida, Rocco Sedona, Yanghui Kang, Srija Chakraborty,
Sizhe Wang, Ankur Kumar, Myscon Truong, Denys Godwin, Hyunho Lee, Chia-Yu Hsu, Ata Akbari Asanjan,
Besart Mujeci, Trevor Keenan, Paulo Arévalo, Wenwen Li, Hamed Alemohammad, Pontus Olofsson,
Christopher Hain, Robert Kennedy, Bianca Zadrozny, Gabriele Cavallaro, Campbell Watson, Manil Maskey,
Rahul Ramachandran y Juan Bernabé Moreno.

**Financiación / enfoque.** El proyecto se enmarca en el enfoque *Trusted Open Science* adoptado por las
organizaciones participantes, con publicación abierta de los pesos del modelo, el código y el artículo.

---

## 3. Descripción general de Prithvi-EO-2.0

Prithvi-EO-2.0 es un **modelo fundacional geoespacial de segunda generación** para aplicaciones de
observación de la Tierra. Se preentrenó de forma **auto-supervisada** sobre **4,2 millones de muestras
globales de series temporales** derivadas del producto **Harmonized Landsat and Sentinel-2 (HLS) v2** de
NASA, a **30 m de resolución espacial**.

Frente a su predecesor (Prithvi-EO-1.0), incorpora dos mejoras arquitectónicas principales: (i) *embeddings*
de parches y codificaciones posicionales en **3D**, que permiten procesar de forma nativa secuencias
temporales de imágenes; y (ii) *embeddings* de **tiempo y localización** (fecha de adquisición y
coordenadas geográficas) en las variantes **TL**. El modelo se concibió como una base genérica que,
mediante *fine-tuning*, se adapta a tareas como segmentación de inundaciones e incendios, clasificación de
coberturas y cultivos, detección de deslizamientos y estimación de biomasa, entre otras.

---

## 4. Arquitectura y funcionamiento del modelo

**Arquitectura base.** Prithvi-EO-2.0 se basa en la arquitectura **Vision Transformer (ViT)**, preentrenada
con un enfoque de **autoencoder enmascarado (MAE)**. La estrategia MAE reconstruye regiones enmascaradas de
la entrada a partir del contexto visible, lo que da lugar a representaciones generales reutilizables.

Modificaciones clave respecto de un ViT/MAE estándar:

1. ***Patch embeddings* y codificaciones posicionales 3D.** Los *embeddings* 2D se reemplazan por versiones
   3D para admitir entradas con características espacio-temporales, es decir, una secuencia de *T* imágenes
   de tamaño (H, W). El *embedding* 3D emplea una capa convolucional 3D que divide la entrada en cubos que
   no se solapan de tamaño (t, h, w) en las dimensiones tiempo, alto y ancho. Para las codificaciones
   posicionales se generan codificaciones 1D seno/coseno por dimensión y se combinan en una única
   codificación posicional 3D.
2. **Incorporación de geolocalización y fecha.** En las variantes **TL**, el codificador (*encoder*) y el
   decodificador (*decoder*) reciben información de **tiempo** (año y día del año, 1–365) y **localización**
   (latitud y longitud centrales). Cada metadato se codifica de forma independiente con codificación
   seno/coseno 2D y se suma a los *tokens* mediante una suma ponderada con pesos aprendidos (uno para el
   tiempo y otro para la localización, con pesos separados para encoder y decoder).
3. **Mecanismo de descarte (*drop*) de metadatos.** Dado que la geolocalización y la fecha no siempre están
   disponibles, durante el preentrenamiento se descartan aleatoriamente la geolocalización y/o los datos
   temporales, de modo que el modelo aprende a operar también en su ausencia.

**Formato de entrada.** El modelo procesa pilas espacio-temporales de imágenes multiespectrales con seis
bandas y puede aplicarse igualmente a imágenes estáticas configurando la dimensión temporal *T* = 1.
El orden de las bandas es: **Azul, Verde, Rojo, NIR estrecho, SWIR 1 y SWIR 2**.

**Uso.** El preentrenamiento es auto-supervisado (reconstrucción); su aprovechamiento en tareas concretas
se realiza mediante *fine-tuning* supervisado con la biblioteca **TerraTorch**.

---

## 5. Datos utilizados para su entrenamiento

| Aspecto | Detalle |
| --- | --- |
| **Conjunto de datos** | NASA HLS v2 — *Harmonized Landsat and Sentinel-2* (producto de reflectancia superficial) |
| **Resolución espacial** | 30 m |
| **Volumen** | 4,2 millones de muestras globales de series temporales |
| **Bandas** | Azul, Verde, Rojo, NIR estrecho, SWIR 1, SWIR 2 (seis bandas) |
| **Naturaleza** | Series temporales multiespectrales ópticas de observación de la Tierra |
| **Infraestructura de entrenamiento** | JUWELS HPC del Jülich Supercomputing Centre (JSC) |
| **Modalidad de entrenamiento** | Auto-supervisado (Masked Autoencoder) |

El producto HLS combina observaciones de los satélites **Landsat 8 y Landsat 9** (NASA/USGS) y
**Sentinel-2A/2B/2C** (ESA) en una colección armonizada y lista para análisis. La ficha específica de estos
datos se documenta en el archivo [`DATASET_CARD.md`](DATASET_CARD.md) de este mismo repositorio.

---

## 6. Características de las imágenes satelitales HLS

Las imágenes utilizadas por Prithvi-EO-2.0 provienen del proyecto **HLS (Harmonized Landsat and Sentinel-2)**
de NASA, cuyas características principales son:

- **Origen:** datos de los sensores **OLI/OLI-2** (Landsat 8/9) y **MSI** (Sentinel-2A/2B/2C).
- **Naturaleza del producto:** reflectancia superficial armonizada y lista para análisis, generada mediante
  correcciones radiométricas y geométricas que reducen las diferencias instrumentales entre sensores.
- **Resolución espacial:** 30 m.
- **Cobertura temporal:** desde 2013 (Landsat 8) y 2015 (Sentinel-2) hasta la actualidad.
- **Cobertura espacial:** superficial terrestre global, excluyendo la Antártida (versión 2.0).
- **Frecuencia de revisita:** del orden de 2–3 días a escala global (mediana cercana a 2 días con cinco
  satélites y hasta ~1,6 días con cuatro satélites en 2022 y ~1,4 días con cinco en 2025).
- **Productos:** **L30** (derivado de Landsat) y **S30** (derivado de Sentinel-2), complementados desde 2025
  por productos de índices de vegetación (HLS-VI).
- **Sistema de teselado:** cuadrícula **MGRS** (idéntica a Sentinel-2), con teselas de ~109,8 km y solape de
  4.900 m por lado, en proyección UTM.
- **Formato:** GeoTIFF optimizado para la nube (COG), con metadatos embebidos.

**Bandas HLS utilizadas por el modelo** (nomenclatura L30 / S30 y rango espectral):

| Banda usada | Nombre | Código L30 | Código S30 | Longitud de onda (µm) |
| --- | --- | --- | --- | --- |
| 1 | Azul (*Blue*) | band02 | B02 | 0,45 – 0,51 |
| 2 | Verde (*Green*) | band03 | B03 | 0,53 – 0,59 |
| 3 | Rojo (*Red*) | band04 | B04 | 0,64 – 0,67 |
| 4 | NIR estrecho (*Narrow NIR*) | band05 | B8A | 0,85 – 0,88 |
| 5 | SWIR 1 | band06 | B11 | 1,57 – 1,65 |
| 6 | SWIR 2 | band07 | B12 | 2,11 – 2,29 |

> El detalle ampliado de los datos HLS se encuentra en [`DATASET_CARD.md`](DATASET_CARD.md).

---

## 7. Entradas, salidas y procesamiento

**Entradas**

- Una **secuencia temporal** de imágenes multiespectrales de la misma localización, en orden cronológico.
- **Seis bandas** en el orden: Azul, Verde, Rojo, NIR estrecho, SWIR 1, SWIR 2.
- Valores en **unidades de reflectancia**.
- Formato típico: **GeoTIFF** (un archivo por fecha en el guion de inferencia).
- **Metadatos opcionales** para las variantes TL: fecha de adquisición (año, día del año) y localización
  (latitud/longitud central).

**Salidas**

- En **preentrenamiento**: reconstrucción de las regiones enmascaradas de la entrada (objetivo MAE).
- En **tareas posteriores (*fine-tuning*)**: la cabeza de tarea define la salida, por ejemplo mapas de
  segmentación/clasificación por píxel, etiquetas a nivel de imagen o valores continuos (regresión).

**Procesamiento / despliegue**

- El guion `inference.py` del repositorio oficial permite ejecutar la **reconstrucción de imágenes** sobre
  un conjunto de imágenes HLS de una misma localización en distintas fechas:

  ```bash
  python inference.py --data_files t1.tif t2.tif t3.tif t4.tif \
      --input_indices <índices 0-based de las seis bandas Prithvi en la entrada>
  ```

- Construcción del *backbone* dentro de un flujo PyTorch mediante TerraTorch:

  ```python
  from terratorch.registry import BACKBONE_REGISTRY

  model = BACKBONE_REGISTRY.build("prithvi_eo_v2_tiny_tl", pretrained=True)
  ```

- Existe una **demo** pública que emplea esta misma lógica:
  https://huggingface.co/spaces/ibm-nasa-geospatial/Prithvi-EO-2.0-Demo

---

## 8. Variantes disponibles

Disponibles en Hugging Face bajo la organización `ibm-nasa-geospatial`. El sufijo **TL** indica variantes con
*embeddings* de **tiempo y localización** (temporal + location).

| Variante | Parámetros | Metadatos temporales/geográficos | Pesos |
| --- | --- | --- | --- |
| Prithvi-EO-2.0-tiny-TL | 5 M | Sí (TL) | [enlace](https://huggingface.co/ibm-nasa-geospatial/Prithvi-EO-2.0-tiny-TL) |
| Prithvi-EO-2.0-100M-TL | 100 M | Sí (TL) | [enlace](https://huggingface.co/ibm-nasa-geospatial/Prithvi-EO-2.0-100M-TL) |
| Prithvi-EO-2.0-300M | 300 M | No | [enlace](https://huggingface.co/ibm-nasa-geospatial/Prithvi-EO-2.0-300M) |
| Prithvi-EO-2.0-300M-TL | 300 M | Sí (TL) | [enlace](https://huggingface.co/ibm-nasa-geospatial/Prithvi-EO-2.0-300M-TL) |
| Prithvi-EO-2.0-600M | 600 M | No | [enlace](https://huggingface.co/ibm-nasa-geospatial/Prithvi-EO-2.0-600M) |
| Prithvi-EO-2.0-600M-TL | 600 M | Sí (TL) | [enlace](https://huggingface.co/ibm-nasa-geospatial/Prithvi-EO-2.0-600M-TL) |

Las variantes de **300 M** y **600 M** son las de mayor difusión y las que el repositorio oficial destaca de
forma principal; las variantes **tiny-TL** y **100 M-TL** aparecen documentadas como parte de la familia de
modelos preentrenados.

---

## 9. Usos previstos y aplicaciones en teledetección

Prithvi-EO-2.0 se concibe como un **modelo base** para ser **adaptado (*fine-tuning*)** a tareas específicas
de observación de la Tierra. La documentación oficial reporta adaptaciones en:

| Tarea | Conjunto de datos | Dominio |
| --- | --- | --- |
| Detección de inundaciones | Sen1Floods11 | Emergencias / agua |
| Detección de cicatrices de incendios | `hls_burn_scars` | Perturbaciones / fuego |
| Intensidad de cicatrices de incendio | `burn_intensity` | Perturbaciones / fuego |
| Detección de deslizamientos | Landslide4Sense | Riesgos geológicos |
| Segmentación multitemporal de cultivos (EE. UU.) | `multi-temporal-crop-classification` | Agricultura |
| Cobertura y cultivos multitemporales (Europa) | Sen4Map | Agricultura / cobertura |
| Estimación de biomasa aérea | BioMassters | Carbono / bosques |
| Productividad primaria / flujo de carbono | `hls_merra2_gppFlux` (GPP) | Ciclo del carbono |

**Usuarios previstos:** equipos de investigación en teledetección y aprendizaje automático geoespacial,
agencias espaciales y ambientales, organismos de respuesta a desastres, grupos de agricultura de precisión y
de monitoreo de ecosistemas, y desarrolladores que integran el modelo en flujos de trabajo con TerraTorch.
El modelo puede emplearse también como **extractor de características** (*embeddings*) para tareas
posteriores.

---

## 10. Usos no recomendados

Los siguientes usos se consideran **fuera de alcance** o desaconsejados:

- **Uso directo como clasificador operativo (*zero-shot*)**: el modelo preentrenado no es una solución
  final; requiere *fine-tuning* y validación con datos locales.
- **Decisiones críticas de seguridad o legales** sin validación humana, auditoría y análisis de
  incertidumbre (por ejemplo, atribución de responsabilidades, delimitación legal de parcelas o alertas de
  emergencia no supervisadas).
- **Aplicaciones fuera del dominio espectral previsto**: por ejemplo, datos **SAR/radar**, imágenes
  hiperespectrales o de muy alta resolución comercial, sin una adaptación explícita.
- **Interpretar la reconstrucción como una verdad física**: las salidas del preentrenamiento son
  reconstrucciones aprendidas, no mediciones.
- **Vigilancia de personas o uso que vulnere la privacidad** y usos **militares/duales** no éticos.
- **Regiones con cobertura HLS muy limitada o ausente** (por ejemplo, la Antártida) sin un estudio de
  transferibilidad previo.
- **Presentar resultados del modelo como mediciones oficiales** de agencias o como base para decisiones
  regulatorias sin respaldo metodológico.

---

## 11. Evaluación, métricas y resultados publicados

La evaluación oficial se realizó con **GEO-Bench**, un marco de referencia para modelos fundacionales de
monitoreo de la Tierra compuesto por tareas de clasificación y segmentación.

**Resultados reportados por los autores (documentados en las fuentes oficiales):**

- **Prithvi-EO-2.0-600M-TL** supera al modelo anterior **Prithvi-EO-1.0 en un 8 %** en un conjunto amplio de
  tareas.
- Supera a **otros seis modelos fundacionales geoespaciales** en tareas de teledetección de distintos
  dominios y resoluciones, en un rango de **0,1 m a 15 m**.
- Los resultados evidencian la **versatilidad** del modelo tanto en observación de la Tierra clásica como en
  aplicaciones de alta resolución.

**Métricas típicas de las tareas evaluadas:** intersección sobre unión media (mIoU), F1, *accuracy* para
tareas de segmentación/clasificación, y RMSE/MAE para tareas de regresión.

> Nota metodológica: las cifras anteriores son las **reportadas por los desarrolladores** en el artículo y el
> repositorio oficial (arXiv:2412.02732; NASA-IMPACT/Prithvi-EO-2.0). Esta ficha académica no reproduce ni
> recalcula dichos experimentos; no se inventan resultados adicionales.

---

## 12. Limitaciones técnicas y geográficas

- **Dependencia del dominio óptico HLS (30 m):** el preentrenamiento se apoya en datos ópticos de
  reflectancia superficial, por lo que su rendimiento óptimo se espera en datos de naturaleza similar;
  sensores de resolución, rango espectral o física distintos (p. ej. SAR) probablemente requieran
  adaptación.
- **Sensibilidad a la nubosidad y a la calidad atmosférica:** al emplear imágenes ópticas, las regiones
  persistentemente nubladas pueden estar infrarrepresentadas.
- **Cobertura espacial del entrenamiento:** aunque HLS v2 cubre tierras emergidas globales (salvo la
  Antártida), la distribución de las 4,2 millones de muestras no es necesariamente uniforme; la
  transferibilidad a regiones poco representadas puede ser menor.
- **Dependencia de metadatos:** las ventajas de las variantes TL dependen de la disponibilidad de
  geolocalización y fecha; el mecanismo de descarte atenúa, pero no elimina, esta dependencia.
- **Ausencia de radar:** el modelo no incorpora datos SAR, relevantes para observación nocturna o bajo
  nubes.
- **Requisitos computacionales:** las variantes de 300 M y 600 M requieren recursos de cómputo (GPU)
  considerables para entrenamiento e inferencia.
- **Resolución y contexto:** el modelo trabaja a 30 m; no está diseñado para proporcionar detalle sub-métrico
  sin *fine-tuning* específico y datos adecuados.
- **Datos reportados incompletos:** las fuentes oficiales no detallan todos los hiperparámetros de
  preentrenamiento ni las emisiones de carbono asociadas.

---

## 13. Sesgos y consideraciones éticas

- **Sesgo geográfico y estacional:** al derivarse de HLS, la representación de regiones, estaciones y
  condiciones atmosféricas es desigual, lo que puede traducirse en diferencias de rendimiento por zona
  geográfica.
- **Sesgo de sensor:** la armonización reduce, pero no elimina, las diferencias residuales entre Landsat y
  Sentinel-2.
- **Riesgo de uso dual:** los modelos de observación de la Tierra admiten usos beneficiosos (agricultura,
  desastres, clima) y usos sensibles (vigilancia, militar). Se recomienda un uso conforme a principios
  éticos y legales.
- **Transparencia:** el modelo se libera como **código y pesos abiertos bajo licencia Apache-2.0**, lo que
  favorece la auditabilidad y la ciencia abierta (*Trusted Open Science*).
- **Datos personales:** los datos de entrada son imágenes satelitales de la superficie terrestre; el modelo
  no está diseñado para tratar datos personales ni información sensible.

---

## 14. Recomendaciones para su utilización

1. **Adaptar antes de decidir:** realizar *fine-tuning* con datos representativos del área y la tarea de
   interés, y validar con un conjunto independiente.
2. **Preferir variantes TL** cuando se disponga de fecha y localización; usar las variantes sin TL cuando
   esos metadatos no existan.
3. **Reportar incertidumbre y métricas por subgrupos** (región, estación, tipo de cobertura) para detectar
   sesgos.
4. **Documentar el origen y preprocesamiento** de los datos HLS utilizados (producto L30/S30, versión, fecha).
5. **Seleccionar la variante según el recurso disponible**: *tiny* o *100 M* para experimentación y
   prototipos; **300 M** o **600 M** para maximizar rendimiento cuando el cómputo lo permita.
6. **Citar adecuadamente** el artículo y los repositorios originales.
7. **No presentar el modelo como producto operacional** sin las validaciones y controles de calidad propios
   del dominio de aplicación.

---

## 15. Referencias bibliográficas (APA 7)

Claverie, M., Ju, J., Masek, J. G., Dungan, J. L., Vermote, E. F., Roger, J.-C., Skakun, S. V., &
Justice, C. (2018). The Harmonized Landsat and Sentinel-2 surface reflectance data set. *Remote Sensing of
Environment, 219*, 145–161. https://doi.org/10.1016/j.rse.2018.09.002

Hugging Face. (s. f.). *Model Cards* [Documentación]. Recuperado el 14 de octubre de 2026, de
https://huggingface.co/docs/hub/model-cards

IBM & NASA. (s. f.). *Prithvi-EO-2.0-300M* [Modelo]. Hugging Face. Recuperado el 14 de octubre de 2026, de
https://huggingface.co/ibm-nasa-geospatial/Prithvi-EO-2.0-300M

IBM. (s. f.). *TerraTorch: A fine-tuning and benchmarking toolkit for geospatial foundation models*
[Software]. GitHub. Recuperado el 14 de octubre de 2026, de https://github.com/IBM/terratorch

Lacoste, A., Lehmann, N., Rodriguez, P., Sherwin, E. D., Kerner, H., Lütjens, B., Irvin, J. A., Dao, D.,
Alemohammad, H., Drouin, A., Gunturkun, M., Huang, G., Vazquez, D., Newman, D., Bengio, Y., Ermon, S., &
Zhu, X. X. (2023). GEO-Bench: Toward foundation models for Earth monitoring. En *Advances in Neural
Information Processing Systems (NeurIPS 2023), Datasets and Benchmarks Track*.
https://doi.org/10.48550/arXiv.2306.03831

Mitchell, M., Wu, S., Zaldivar, A., Barnes, P., Vasserman, L., Hutchinson, B., Spitzer, E., Raji, I. D., &
Gebru, T. (2019). Model cards for model reporting. En *Proceedings of the Conference on Fairness,
Accountability, and Transparency (FAT\* '19)* (pp. 220–229). Association for Computing Machinery.
https://doi.org/10.1145/3287560.3287596

NASA. (s. f.). *Harmonized Landsat and Sentinel-2 (HLS)*. NASA Earthdata. Recuperado el 14 de octubre de
2026, de https://www.earthdata.nasa.gov/data/projects/hls

NASA-IMPACT. (s. f.). *Prithvi-EO-2.0* [Software]. GitHub. Recuperado el 14 de octubre de 2026, de
https://github.com/NASA-IMPACT/Prithvi-EO-2.0

Szwarcman, D., Roy, S., Fraccaro, P., Gíslason, Þ. E., Blumenstiel, B., Ghosal, R., de Oliveira, P. H.,
de Sousa Almeida, J. L., Sedona, R., Kang, Y., Chakraborty, S., Wang, S., Gomes, C., Kumar, A., Truong, M.,
Godwin, D., Lee, H., Hsu, C.-Y., Lal, R., … Bernabé Moreno, J. (2024). *Prithvi-EO-2.0: A versatile
multi-temporal foundation model for earth observation applications* (arXiv:2412.02732).
arXiv. https://doi.org/10.48550/arXiv.2412.02732

---

## 16. Autoría de la ficha académica

- **Autora de la ficha:** Nikole Stefhanie Flórez Londoño
- **Universidad:** Universidad Distrital Francisco José de Caldas
- **Programa:** Ingeniería Catastral y Geodesia
- **Fecha:** 14 de octubre de 2026

**Declaración de autoría y alcance.** La autora elaboró esta **ficha técnica con fines exclusivamente
académicos**, siguiendo la metodología de Mitchell et al. (2019) y adaptando la plantilla oficial de *Model
Cards* de Hugging Face. **La autora no desarrolló ni entrenó el modelo Prithvi-EO-2.0**; todos los créditos
del modelo corresponden a IBM, NASA y el Jülich Supercomputing Centre. La información aquí consignada
proviene de fuentes oficiales verificables (repositorio de NASA-IMPACT, modelo en Hugging Face, artículo
científico arXiv:2412.02732 y documentación de NASA sobre HLS).
