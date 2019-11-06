    
# Análisis de Datos Chilecracia  
  
Felicitaciones por el increíble proyecto y muchas gracias por compartir la data para que todos podamos estudiarla. Con nuestro equipo de **ConnectaLabs** nos juntamos para analizar y aportar nuestro granito de arena poder sacar conclusiones útiles para la ciudadanía.      
      
Corrimos varios modelos (SVDs para extraer “temas” transversales y Tsne para graficar diversos grupos de participantes).      
        
## Datos Chile        
 Los datos se descargaron desde la web de chilecracia https://chilecracia.org/datos.        
        
La información para el análisis se encuentra en un archivo **data.zip** para usar hay que descomprimirlo con unzip.
(deben quedar guardados en el folder data/)
        
	 > sudo apt install unzip
	 > unzip data.zip  

## Ambiente
- Datos Chilecracia: 2019-10-31
- Python 3.6.8        
- Pandas        
- Pandas Profiling        
- sklearn (KMeans, DBSCAN, TSNE)   

## Archivos

- [Analisis Chilecracia](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/Analisis_Chilecracia.ipynb): Notebook con el análisis sobre la base completa de Chilecracia.
- [Analisis Chilecracia sin anomalos](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/Analisis_Chilecracia_sin_anomalos.ipynb): Notebook con el análisis sobre Chilecracia, en el cual eliminamos los datos anómalos.
- [data_option.html](http://lab.connectalabs.ai/chilecracia_analisis/data_option_profile.html): Exploratory data analysis de la tabla ids de Chilecracia
- [data_people.html](http://lab.connectalabs.ai/chilecracia_analisis/data_people_profile.html): Exploratory data analysis de la tabla people de Chilecracia
- [data_survey_eda_profile.html](http://lab.connectalabs.ai/chilecracia_analisis/data_survey_eda_profile.html): Exploratory data analysis de la tabla surveys de Chilecracia.

## Descubrimientos  
  
### Resumen  
  
En primer lugar se ve un número relevante de anomalías las cuales están generando un sesgo en el análisis. Estas anomalias, una vez ajustadas, muestran que éstas generan un sesgo hacia propuestas más en el espectro "político" (en particular en pos de una nueva constitución) por sobre medidas de orden más pragmático/económico.  
  
El modelo de reducción de dimensionalidad muestra ejes que agrupan las grandes temáticas bajo discusión, estableciendo en su primera componente un set prioritario en línea con mejora del estado de bienestar, redistribución del ingreso y penalización de delitos politico/economicos. Los otros componentes apuntan a temas más detallados, los cuales se muestran balanceados entre los diferentes votantes.  
  
Estas herramientas son de utilidad para consolidar el número de medidas y, en base a los patrones de votación, extraer las temáticas que se agrupan según el tipo de participante.  
  
   
### Limpieza de datos y detección de interacciones anómalas   
 #### Votaciones excesivas por session uuid:  
  
** Se detectaron sesiones (UUIDs) que han generado sobre 3.000 votaciones, en un caso 16.959, para que un "humano" genere este número de votaciones, si cada una la hace cada 5 segundos, se demoraría 1413 minutos o 58 horas sin parar o alterar su rendimiento.    
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/uuid_repeticion.png)    
    
    
#### Votaciones excesivas entre par Nueva Constitución para Chile / Aumentar el Sueldo Mínimo:  
  
** Se detectó un número anómalo de pares de votación entre la propuesta número 23 (Nueva Constitución para Chile) y la propuesta número 87 (Aumentar el Sueldo Mínimo), llegando a un total de 34.933 apariciones lo cual es mas de 30 veces el promedio de todos los otros pares (900 veces aproximadamente).  
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/heatmap_frequencia_pares.png)    
  
#### Participantes con una sola votación (solo entre el par Nueva Constitución para Chile / Aumentar el Sueldo Mínimo)  
  
** Mediante un analisis de clustering (DBSCAN) y reducción de dimensionalidad (TSNE) se encontraron 4889 cuentas anómalas con una sola votación (nuevamente entre la propuesta de Nueva Constitución versus Sueldo Mínimo). De éstas, 3040 apoyaron la nueva constitución por sobre el sueldo mínimo y 1849 apoyaron lo contrario. Generando un sesgo de 1191 votos a favor de la nueva constitución  
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/cluster_tsne.png)    
  
## Modelos y resultados  
  
Tras realizar acciones correctivas para mitigar los efectos de calidad de datos encontrados (limitar a participantes con menos de 500 votos, descartar las 4889 cuentas anómalas de 1 voto, y realizar downsampling aleatorio de las votaciones entre la medida 23 y 87). Se realizó un modelo de reducción de dimensionalidad via SVD (Singular Value Decomposition https://en.wikipedia.org/wiki/Singular_value_decomposition). Donde la matriz de n participes y m propuestas se descompone en una de nxk y otra de kxm, siendo k el número de "temas" subyacentes que resumen la totalidad de propuestas.  
  
Una forma de entender estos "temas" es que cada uno representa un votante tipo, el cual tiene preferencia de algunas propuestas sobre otras. Cada votante es una combinación de estos "tipos de votante", el cual queda determinado por los pesos de cada participante a cada tema.  
  
### Tema 1  
El primer tema o tipo de votante se podría resumir como todos los elementos de prioridad "transversal" vs aquellos de relevancia "secundaria" o impopular. (ojo que en este caso el algoritmo los definió de manera inversa, la mayoría de los participantes poseen un valor negativo en esta dimensión, lo que significa apoyo de las medidas con pesos negativos)  
  
Se ve que la mayoría apoya medidas en línea con aumento al estado de bienestar (pensiones, salud), mejorar la redistribución del ingreso (impuestos progresivos), penalización de delitos politico/economicos.  
  
Por el contrario, dentro de las medidas "secundarias" se ven temas relativos a ordenamiento territorial, redes sociales, marihuana, periodo presidencial.  
  
IMPORTANTE: AL COMPARAR ESTOS RESULTADOS CON LOS DEL MODELO SIN AJUSTAR POR ELEMENTOS ANOMALOS, LA RELEVANCIA DE LA NUEVA CONSTITUCIÓN SALE DE LAS TOP 10, MIENTRAS QUE EL AUMENTO DEL SUELDO MÍNIMO SIGUE SIENDO DE ALTA RELEVANCIA (TOP 2)  
  
VALORES CON AJUSTE  
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/tema_1.png)    
VALORES SIN AJUSTE  
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/tema_1_sin_ajuste.png)    
  
### Tema 2  
El segundo tema o tipo de votante puede resumirse como aquel prefiere medidas "pragmáticas" sobre medidas "políticas".  
  
En el extremo "político" se ven temas constituyentes (nueva constitución, asamblea constituyente, plebiscitos), oposicion a la situacion militar/policial (retiro de FFAA, investigación DDHH, fin toque de queda) y causas histórico/culturales (pueblos originarios, nacionalizaciones)  
  
En el extremo "pragmático" se ve fuerte el sueldo mínimo, seguridad, transporte, salud y educación . 
  
La distribución muestra un sesgo "pragmático" versus "político"  
  
IMPORTANTE: AL COMPARAR ESTOS RESULTADOS CON LOS DEL MODELO SIN AJUSTAR POR ELEMENTOS ANÓMALOS SE VE UN CAMBIO EN EL SESGO DESDE LO "POLÍTICO" A LO "PRAGMÁTICO". EVIDENCIANDO EL SESGO DE LOS VOTOS ANÓMALOS  
  
VALORES CON AJUSTE  
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/tema_2.png)    
VALORES SIN AJUSTE  
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/tema_2_sin_ajuste.png)    
  
### Tema 3  
El tercer tema o tipo de votante puede resumirse como aquel que promueve medidas sobre "juventud, educación y bienestar" versus "reducción y control de la clase política"  
  
El eje de "Juventud, educacion, y bienestar" se ven temas de apoyo a menores (sala cuna, sename, jóvenes de alto riesgo), fortalecimiento a la educación y las ciencias, además de mejoras al estados de bienestar (fondo comun municipal, pensiones, espacios publicos, etc).  
  
El eje de reducción de la clase política se enfoca en limitar las capacidades y privilegios de ésta.  
  
La distribución entre participantes se puede ver bastante balanceada, con un pequeño sesgo a valorar elementos de "juventud, educación y bienestar" por sobre la "reducción y control de la clase política"  
  
VALORES CON AJUSTE  
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/tema_3.png)    
VALORES SIN AJUSTE  
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/tema_3_sin_ajuste.png)    
  
### Tema 4  
El cuarto eje o tipo de votante contrapesa medidas del tipo "subsidios y aportes económicos" versus "penalización de abusos"  
  
El eje de "subsidios y aportes económicos" toma temas como subsidios a servicios básicos, condonacion del CAE, mejora de pensiones y otros con el mismo foco económico.  
  
El eje de "penalización de abusos" muestra elementos relativos a cárcel efectiva por delitos politico/economicos, reforma al sistema de notarios, además de elementos de ecología y ciencia.  
  
La distribución es bastante balanceada, con un pequeño sesgo al la "penalización de abusos".  
  
VALORES CON AJUSTE  
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/tema_4.png)    
  
### Tema 5  
El cuarto eje o tipo de votante contrapesa medidas del tipo "ecología y estatización de recursos naturales" versus "integracion social"  
  
El eje de "ecología" muestra iniciativas relativas a glaciares, huella de carbono y la nacionalización de recursos naturales como el litio y el agua.  
  
En el eje de "integración social" relacionados a la integración de viviendas sociales en el barrio alto y en edificios particulares, fondo comun municipal, además de elementos contra las la gestión política actual (cambio de gabinete, fin al toque de queda).  
  
No se ve un sesgo claro en la distribución  
  
VALORES CON AJUSTE  
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/tema_5.png)
