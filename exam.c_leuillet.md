exam.c_leuillet .md

> ##### Chloé Leuillet
>  ###### matricola n. 
> 
> ![massiccio dell'Étoile](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Ftse2.mm.bing.net%2Fth%2Fid%2FOIP.mRrhzPLj8ytBhOG0Vns4EwHaE8%3Fr%3D0%26pid%3DApi&f=1&ipt=ea81da7d16cf44fc9b59881251b8494d4ecbf02b8f6147e3baa0151ebe8b253d&ipo=images)
> ![video dell'incendio](https://img.youtube.com/vi/iMtpQLQPxN0/0.jpg)]

#### Analisi dell'Impatto dell'Incendio a Marsiglia (8 Luglio 2025, Pennes-Mirabeau) 

## 1. 📌Ecological background

Gli ecosistemi mediterranei sono estremamente vulnerabili agli incendi, un fenomeno che la crisi climatica sta rendendo sempre più frequente e intenso. Marsiglia, situata in un'area caratterizzata da una complessa interfaccia tra ambiente naturale e urbanizzato, è stata colpita da un grave incendio boschivo nell'estate 2025.

Il telerilevamento satellitare è uno strumento fondamentale per monitorare la distruzione della copertura vegetale e la successiva capacità di resilienza del territorio. In questo progetto, utilizzeremo i dati Sentinel-2 L2A per quantificare il danno subito dalla vegetazione, analizzando la variazione spettrale tra il periodo pre-incendio e quello post-incendio.


## 2. Obiettivo del Progetto in R

L’obiettivo dell'elaborazione è **quantificare la perdita di biomassa vegetale** causata dall'incendio, ma anche l'estenzione dell'incendio e il modo in cui si è ripreso il terreno, confrontando l'immagine pre-evento (Giugno 2025), subito post-evento (9 luglio 2025) e marzo 2026, calcolando indici spettrali come NBR (Normalized Burn Ratio) e NDVI (normalized different vegetation index).


## 3. Pacchetti

install.packages("terra")
install.packages("viridis")
install.packages("ggplot2")
library(terra)
library(viridis)
library(ggplot2)


## 4. Impostazione della working directory
setwd("C:/copernicus 2")

## 5. Rilevazione sentinel-2 bands da locale

#PRE incendio 

b08_pre <- rast ("C:/copernicus 2/Pre_29_06_2025/b08RAW.tiff")

b12_pre <- rast ("C:/copernicus 2/Pre_29_06_2025/b12RAW.tiff")

b04_pre <- rast ("C:/copernicus 2/Pre_29_06_2025/b04RAW.tiff")


#POST

b08_post <- rast ("C:/copernicus 2/Post_09_07_2025/b08RAW.tiff")

b12_post <- rast ("C:/copernicus 2/Post_09_07_2025/b12RAW.tiff")

b04_post <- rast ("C:/copernicus 2/Post_09_07_2025/b04RAW.tiff")


## 6. Creazione grafici
dev.new()
par(mfrow = c(2, 3))

# PRE
plot(b08_pre, main="B08 (NIR) - Vegetazione pre incendio", col=terrain.colors(100))
plot(b12_pre, main="B12 (SWIR) - Umidità e suolo pre incendio", col=heat.colors(100))
plot(b04_pre, main="B04 (Red) - Clorofilla pre incendio", col=gray.colors(100))

# POST
plot(b08_post, main="B08 (NIR) - Vegetazione post incendio", col=terrain.colors(100))
plot(b12_post, main="B12 (SWIR) - Aree bruciate", col=heat.colors(100))
plot(b04_post, main="B04 (Red) - Clorofilla post incendio", col=gray.colors(100))
#
<img width="688" height="465" alt="Grafico 1" src="https://github.com/user-attachments/assets/869a4b57-186f-4cdc-a148-4241880319d2" />

## 7. Calcolo NBR (Normalized Burn Ratio)
#pre 

nbr_pre  <- (b08_pre - b12_pre) / (b08_pre + b12_pre)

#post

nbr_post <- (b08_post - b12_post) / (b08_post + b12_post)

## 8. Visualizzazione NBR (confronto visivo incendio)
dev.new()
par(mfrow = c(1, 2))

plot(nbr_pre, main="NBR Pre-incendio", col=rev(terrain.colors(100)))
plot(nbr_post, main="NBR Post-incendio", col=rev(terrain.colors(100)))

<img width="494" height="307" alt="Grafico 2" src="https://github.com/user-attachments/assets/01b4ca83-b576-45f6-a575-bf83cb1649aa" />


## 9. Calcolo NDVI (normalized different vegetation index)
# NDVI pre-incendio
ndvi_pre <- (b08_pre - b04_pre) / (b08_pre + b04_pre)

# NDVI post-incendio
ndvi_post <- (b08_post - b04_post) / (b08_post + b04_post)

## 10. Visualizzazione NDVI (normalized different vegetation index)
dev.new()
par(mfrow = c(1, 2))

plot(ndvi_pre, main="NDVI Pre-incendio", col=rev(terrain.colors(100)))
plot(ndvi_post, main="NDVI Post-incendio", col=rev(terrain.colors(100)))
#
<img width="522" height="314" alt="Grafico 3" src="https://github.com/user-attachments/assets/7f6a2a0f-a7bb-4055-8513-e58e3c7056ee" />

## 11. Classificazione danno

dnbr <- nbr_pre - nbr_post

severity <- classify(dnbr, rbind(
  c(-Inf, 0.1, 1),   # non bruciato
  c(0.1, 0.27, 2),   # basso
  c(0.27, 0.44, 3),  # moderato
  c(0.44, Inf, 4)    # alto
))

dev.new()

plot(severity,
     col=c("green","yellow","orange","red"),
     main="Classificazione severità incendio")

<img width="406" height="421" alt="Grafico 4" src="https://github.com/user-attachments/assets/6360b2ab-8221-4833-8f7a-4485f03eec47" />


## 12. Percentuale danno per pixel
f <- freq(severity)

tot <- sum(f$count)

f$percent <- (f$count / tot) * 100

f

<img width="373" height="115" alt="immagine" src="https://github.com/user-attachments/assets/bc9d7876-3d7f-4a61-8016-9be24517cc3b" />


## 13. Calcolo ettari
area <- expanse(severity, unit="ha")

area

 1 1706.785 ha

## 14. Calcolo danno per ettari
area_class <- expanse(severity, unit="ha", byValue=TRUE)

area_class

<img width="416" height="395" alt="Grafico 5" src="https://github.com/user-attachments/assets/5c0ea98a-9c97-40b7-baae-59b59caa7bcb" />



## 15. Istogramma per severità incendio

dev.new()
barplot(area_class$area,
        names.arg=c("Non bruciato", "Basso", "Moderato", "Alto"),
        col=c("green","yellow","orange","red"),
        main="Area per classe di severità incendio (ha)",
        ylab="Ettari")

## 16. Distribuzione del danno 
dev.new()
hist(values(dnbr),
     col="orange",
     main="Distribuzione dNBR",
     xlab="Valori dNBR")

<img width="430" height="412" alt="Grafico 6" src="https://github.com/user-attachments/assets/934848fd-13fc-4a4c-aa03-1745d9b2f706" />


## 17. Ripresa terreno post incendio confronto temporale

b08_rip <- rast ("C:/copernicus 2/Ripresa 04 03 2026/b08RAW.tiff")
b04_rip <- rast ("C:/copernicus 2/Ripresa 04 03 2026/b04RAW.tiff")

ndvi_rec <- (b08_rip - b04_rip) / (b08_rip + b04_rip)

dev.new()
par(mfrow = c(1, 3))

plot(ndvi_pre, main="NDVI Pre incendio")
plot(ndvi_post, main="NDVI Post incendio")
plot(ndvi_rec, main="NDVI Recupero")

<img width="607" height="317" alt="Grafico 7" src="https://github.com/user-attachments/assets/9abfaadf-4123-4f34-8557-0e7f566a536e" />


## Conclusioni

Il progetto ha permesso di mappare con precisione l'impatto dell'incendio delle Pennes-Mirabeau a Marsiglia al luglio 2025. L'uso di R ha facilitato un'analisi qualitativa e quantitativa, confermando l'utilità del telerilevamento nella gestione delle emergenze forestali.


## Il contributo del telerilevamento

Il telerilevamento si conferma uno strumento indispensabile per:

* Quantificare l'area effettivamente bruciata.
* Monitorare il tasso di rigenerazione negli anni successivi.
* Supportare le decisioni per il ripristino ecologico.


