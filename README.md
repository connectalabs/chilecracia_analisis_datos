# Análisis de Datos Chilecracia  

Felicitaciones por el increíble proyecto y muchas gracias por compartir la data para que todos podamos estudiarla. Con nuestro equipo de **ConnectaLabs** nos juntamos para analizar y aportar nuestro granito de arena poder sacar conclusiones útiles para la ciudadanía.

Corrimos varios modelos (SVDs para extraer “temas” transversales y Tsne para graficar diversos grupos de participantes).
  
## Datos Chile  
  
Los datos se descargaron desde la web de chilecracia https://chilecracia.org/datos.  
  
La información para el análisis se encuentra en un archivo **data.zip** para  
usar hay que descomprimirlo con unzip.  
  
    sudo apt install
    unzip unzip data.zip 

## Ambiente  
  
- Python 3.6.8  
- Pandas  
- Pandas Profiling  
- sklearn (KMeans, DBSCAN, TSNE)

## Descubrimientos
###	Votaciones excesivas

![Img1]([http://lab.connectalabs.ai/chilecracia_analisis/img/uuid_repeticion.png](http://lab.connectalabs.ai/chilecracia_analisis/img/uuid_repeticion.png))

