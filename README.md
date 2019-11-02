  
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
  
#### Votaciones excesivas entre par Nueva Constitución para Chile / Aumentar el Sueldo Minimo:

** Se detectó un numero anómalo de pares de votacion entre la propuesta numero 23 (Nueva Constitución para Chile) y la propuesta numero 87 (Aumentar el Sueldo Minimo), llegando a un total de 34.933 apariciones lo cual es mas de 30 veces el promedio de todos los otros pares (900 veces aproximadamente).

![img02](http://lab.connectalabs.ai/chilecracia_analisis/img/Screen Shot 2019-11-02 at 15.36.15.png)  

#### Participantes con una sola votacion (solo entre el par Nueva Constitución para Chile / Aumentar el Sueldo Minimo)


  
