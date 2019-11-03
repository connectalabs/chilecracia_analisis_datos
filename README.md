  
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
 
### Limpieza de datos y deteccion de interacciones anomalas 
 
#### Votaciones excesivas por session uuid:

** Se detectaron sesiones (UUIDs) que han generado sobre 3.000 votaciones, en un caso 16.959, para que un "humano" genere este número de votaciones, si cada una la hace cada 5 segundos, se demoraría 1413 minutos o 58 horas sin parar o alterar su rendimiento.  
![img01](http://lab.connectalabs.ai/chilecracia_analisis/img/uuid_repeticion.png)  
  
  
#### Votaciones excesivas entre par Nueva Constitución para Chile / Aumentar el Sueldo Minimo:

** Se detectó un numero anómalo de pares de votacion entre la propuesta numero 23 (Nueva Constitución para Chile) y la propuesta numero 87 (Aumentar el Sueldo Minimo), llegando a un total de 34.933 apariciones lo cual es mas de 30 veces el promedio de todos los otros pares (900 veces aproximadamente).
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/heatmap_frequencia_pares.png)  

#### Participantes con una sola votacion (solo entre el par Nueva Constitución para Chile / Aumentar el Sueldo Minimo)

** Mediante un analisis de clustering (DBSCAN) y reduccion de dimensionalidad (TSNE) se encontraron 4889 cuentas anomalas con una sola votacion (nuevamente entre la propuesta de Nueva Constutucion versus Sueldo Minimo). De éstas, 3040 apoyaron la nueva constitucion por sobre el sueldo minimo y 1849 apoyaron lo contrario. Generando un sesgo de 1191 votos a favor de la nueva constitucion
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/cluster_tsne.png)  

## Modelos y resultados

Tras realizar acciones correctivas para mitigar los efectos de calidad de datos encontrados (limitar a participantes con menos de 500 votos, descartar las 4889 cuentas anomalas de 1 voto, y realizar downsampling aleatorio de las votaciones entre la medida 23 y 87). Se realizo un modelo de reduccion de dimensionalidad via SVD (Singular Value Decomposition https://en.wikipedia.org/wiki/Singular_value_decomposition). Donde la matriz de n participes y m propuestas se descompone en una de nxk y otra de kxm, sinendo k el numero de "temas" subyacentes que resumen la totalidad de propuestas.

Una forma de entender estos "temas" es que cada uno representa un votante tipo, el cual tiene preferencia de algunas propuestas sobre otras. Cada votante es una combinacion de estos "tipos de votante", el cual queda determinado por los pesos de cada participante a cada tema.

### Tema 1

El primer tema o tipo de votante se podria resumir como todos los elementos de prioridad "transversal" vs aquellos de relevancia "secundaria" o impopular. (ojo que en este caso el algoritmo los definio de manera inversa, la mayoria de los participantes poseen un valor negativo en esta dimension, lo que significa apoyo de las medidas con pesos negativos)

Se ve que la mayoria apoya medidas en linea con aumento al estado de bienestar (pensiones, salud), mejorar la redistribucion del ingreso (impuestos progresivos), penalizacion de delitos politico/economicos.

Por el contrario, dentro de las medidas "secundarias" se ven temas relativos a ordenamiento territorial, redes sociales, marihuana, periodo presindencial.

IMPORTANTE: AL COMPARAR ESTOS RESULTADOS CON LOS DEL MODELO SIN AJUSTAR POR ELEMENTOS ANOMALOS, LA RELEVANCIA DE LA NUEVA CONSTITUCION SALE DE LAS TOP 10, MIENTRAS QUE EL AUMENTO DEL SUELDO MINIMO SIGUE SIENDO DE ALTA RELEVANCIA (TOP 2)
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/tema_1.png)  





