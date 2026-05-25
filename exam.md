> #### Chloé Leuillet
>  ##### matricola n. 
> 
> ![video dell'incendio](https://img.youtube.com/vi/iMtpQLQPxN0/0.jpg)](https://www.youtube.com/watch?v=iMtpQLQPxN0)
> ![massiccio dell'Étoile](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Ftse2.mm.bing.net%2Fth%2Fid%2FOIP.mRrhzPLj8ytBhOG0Vns4EwHaE8%3Fr%3D0%26pid%3DApi&f=1&ipt=ea81da7d16cf44fc9b59881251b8494d4ecbf02b8f6147e3baa0151ebe8b253d&ipo=images)
> ![La Grande Étoile - Marsiglia](https://photos-provence.fr/marseille/chaine_etoile/la-grande-etoile-3.jpg)

# Analisi dell'Impatto dell'Incendio a Marsiglia (Giugno - Agosto 2025) 
## 1. 📌Ecological background

Gli ecosistemi mediterranei sono estremamente vulnerabili agli incendi, un fenomeno che la crisi climatica sta rendendo sempre più frequente e intenso. Marsiglia, situata in un'area caratterizzata da una complessa interfaccia tra ambiente naturale e urbanizzato, è stata colpita da un grave incendio boschivo nell'estate 2025.

Il telerilevamento satellitare è uno strumento fondamentale per monitorare la distruzione della copertura vegetale e la successiva capacità di resilienza del territorio. In questo progetto, utilizzeremo i dati Sentinel-2 per quantificare il danno subito dalla vegetazione, analizzando la variazione spettrale tra il periodo pre-incendio e quello post-incendio.


---

## 2. 🔥 Evento in questione

Nel corso dell'estate 2025, le aree forestali attorno a Marsiglia sono state colpite da significativi incendi. Questo progetto analizza la variazione dello stato di salute della vegetazione prima e dopo l'evento, utilizzando dati satellitari **Sentinel-2**.

L'area di studio è una zona soggetta a forte pressione antropica e climatica, dove il monitoraggio del recupero vegetativo e dell'entità del danno è cruciale per la gestione ambientale.

---

## 3. 🛰️ Obiettivo del Progetto in R

L’obiettivo dell'elaborazione è **quantificare la perdita di biomassa vegetale** causata dall'incendio, confrontando l'immagine pre-evento (Giugno 2025) e post-evento (Agosto 2025), calcolando indici spettrali come:

* **NDVI** (Normalized Difference Vegetation Index)
* **DVI** (Difference Vegetation Index)

---

## 4. 🧪 Metodologia

### Raccolta delle immagini

Le immagini sono state estratte dal **Copernicus Data Space Ecosystem**, selezionando scene Sentinel-2 L2A con pochi nuvole. 

### Impostazione della working directory

```md
setwd("C:/Users/tuo_nome/Documents/R_project/marsiglia_fire/")

```

### Caricamento pacchetti

```md
library(terra)  
library(imageRy)  
library(viridis)
library(ggplot2)  
library(patchwork)  

```

### Importazione raster Sentinel-2

```md
mars_giugno <- rast("marsiglia_giugno2025.tif")  
mars_agosto <- rast("marsiglia_agosto2025.tif")

```

### Analisi visiva (RGB)

```md
im.multiframe(1,2)
plotRGB(mars_giugno, r=3, g=2, b=1, stretch="lin", main="Giugno 2025 - Pre-incendio")
plotRGB(mars_agosto, r=3, g=2, b=1, stretch="lin", main="Agosto 2025 - Post-incendio")

```

---

### Analisi NDVI

$` NDVI = \frac{(NIR - Red)}{(NIR + Red)} `$

L'NDVI ci permette di identificare chiaramente dove la vegetazione è stata distrutta (valori prossimi allo 0 o negativi).

```md
ndvi_giugno <- im.ndvi(mars_giugno, 4, 3) 
ndvi_agosto <- im.ndvi(mars_agosto, 4, 3)

im.multiframe(1,2)
plot(ndvi_giugno, col=viridis(100), main="NDVI Giugno")
plot(ndvi_agosto, col=viridis(100), main="NDVI Agosto")

```

### Classificazione del danno

```md
class_matrix <- matrix(c(-Inf, 0.2, 1, 0.2, 0.5, 2, 0.5, Inf, 3), ncol = 3, byrow = TRUE)
ndvi_giugno_cl <- classify(ndvi_giugno, class_matrix)
ndvi_agosto_cl <- classify(ndvi_agosto, class_matrix)

```

---

## 📉 Analisi Multitemporale

La mappa di differenza mostra la variazione netta dell'indice di salute vegetativa tra i due mesi:

```md
ndvi_diff <- ndvi_agosto - ndvi_giugno
plot(ndvi_diff, col=magma(100), main="Differenza NDVI (Agosto - Giugno)")

```

> **Commento:** I pixel che presentano valori fortemente negativi (colore scuro) corrispondono all'area colpita dalle fiamme, dove la riflettanza nel NIR è crollata drasticamente a causa della rimozione della copertura arborea.

---

## Conclusioni

Il progetto ha permesso di mappare con precisione l'impatto dell'incendio di Marsiglia 2025. L'uso di R ha facilitato un'analisi quantitativa che va oltre la semplice osservazione visiva, confermando l'utilità del telerilevamento nella gestione delle emergenze forestali.


## 🎯 Il contributo del telerilevamento

Il telerilevamento si conferma uno strumento indispensabile per:

* Quantificare l'area effettivamente bruciata.
* Monitorare il tasso di rigenerazione negli anni successivi.
* Supportare le decisioni per il ripristino ecologico.



