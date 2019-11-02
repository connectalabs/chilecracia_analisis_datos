  
# Análisis de Datos Chilecracia

Felicitaciones por el increíble proyecto y muchas gracias por compartir la data para que todos podamos estudiarla. Con nuestro equipo de **ConnectaLabs** nos juntamos para analizar y aportar nuestro granito de arena poder sacar conclusiones útiles para la ciudadanía.    
    
Corrimos varios modelos (SVDs para extraer “temas” transversales y Tsne para graficar diversos grupos de participantes).    
      
## Datos Chile      
 Los datos se descargaron desde la web de chilecracia https://chilecracia.org/datos.      
      
La información para el análisis se encuentra en un archivo **data.zip** para      
usar hay que descomprimirlo con unzip.      
      
	 > sudo apt install unzip
	 > unzip data.zip

## Ambiente      
- Python 3.6.8      
- Pandas      
- Pandas Profiling      
- sklearn (KMeans, DBSCAN, TSNE)    

## Descubrimientos
 
### Limpieda de datos y deteccion de interacciones anomalas 
 
#### Votaciones excesivas por session uuid:

** Se detectaron sesiones (UUIDs) que han generado sobre 3.000 votaciones, en un caso 16.959, para que un "humano" genere este número de votaciones, si cada una la hace cada 5 segundos, se demoraría 1413 minutos o 58 horas sin parar o alterar su rendimiento.  
   
![img01](http://lab.connectalabs.ai/chilecracia_analisis/img/uuid_repeticion.png)  
  
#### Votaciones excesivas entre par Nueva Constitucion / Sueldo Minimo:



- La opción **Nueva Constitución** tiene una frecuencia de aparición mayor al resto, aproximadamente un 80% superior al resto de preguntas que están en las 40.000 apariciones, lo que altera la representatividad de la muestra, considerando que hay sesiones que han generado situaciones cuestionables.  
  
![img02](http://lab.connectalabs.ai/chilecracia_analisis/img/opcion_a.png)
