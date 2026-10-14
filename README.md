# Prithvi-EO-2.0

## Descripción general

**Prithvi-EO-2.0** es un modelo fundacional geoespacial de segunda generación para aplicaciones de
observación de la Tierra, desarrollado conjuntamente por **IBM**, **NASA** y el **Jülich Supercomputing
Centre (JSC)**. Se preentrenó de forma auto-supervisada sobre **4,2 millones de muestras globales de series
temporales** del producto **Harmonized Landsat and Sentinel-2 (HLS)** de NASA a 30 m de resolución. Su
arquitectura se basa en un *Vision Transformer* (ViT) con un enfoque de *Masked Autoencoder* (MAE),
ampliado con *embeddings* 3D y, en las variantes **TL**, con información de tiempo y localización, lo que le
permite procesar secuencias multitemporales de imágenes multiespectrales. Mediante ajuste fino (*fine-tuning*)
se adapta a tareas como detección de inundaciones e incendios, detección de deslizamientos, clasificación de
coberturas y cultivos, estimación de biomasa y monitoreo de ecosistemas, entre otras aplicaciones de
teledetección.

## Ficha técnica del modelo

👉 **[Consultar la ficha técnica de Prithvi-EO-2.0](MODEL_CARD.md)**

La ficha sigue la metodología de *Model Cards for Model Reporting* (Mitchell et al., 2019) y adapta la
plantilla oficial de *Model Cards* de Hugging Face.

### Conjunto de datos

📄 **[Consultar la ficha del conjunto de datos HLS (Harmonized Landsat and Sentinel-2)](DATASET_CARD.md)**

## Autor de la ficha

- **Nombre:** Nikole Stefhanie Flórez Londoño
- **Universidad:** Universidad Distrital Francisco José de Caldas
- **Programa:** Ingeniería Catastral y Geodesia
- **Fecha:** 14 de octubre de 2026

> **Aclaración:** La autora elaboró esta **ficha académica** siguiendo metodologías de documentación de
> modelos. **No desarrolló el modelo original.** Todos los créditos de Prithvi-EO-2.0 corresponden a IBM,
> NASA y el Jülich Supercomputing Centre.

## Fuentes

- **IBM (TerraTorch):** https://github.com/IBM/terratorch
- **NASA (HLS – Earthdata):** https://www.earthdata.nasa.gov/data/projects/hls
- **NASA-IMPACT (código del modelo):** https://github.com/NASA-IMPACT/Prithvi-EO-2.0
- **Hugging Face (pesos del modelo):** https://huggingface.co/ibm-nasa-geospatial/Prithvi-EO-2.0-300M
- **Artículo científico (arXiv):** Szwarcman et al. (2024) — https://arxiv.org/abs/2412.02732
- **Metodología de fichas:** Mitchell et al. (2019) — https://arxiv.org/abs/1810.03993
