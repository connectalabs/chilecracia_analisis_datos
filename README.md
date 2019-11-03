  
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

### Resumen

En primer lugar se ve un numero relevante de anomalias las cuales estan generando un sesgo en el analisis. Estas anomalias, una vez ajustadas, muestran que éstas generan un sesgo hacia propuestas mas en el espectro "politico" (en particular en pos de una nueva constitucion) por sobre medidas de orden mas pragmatico/economico.

El modelo de reduccion de dimensionalidad muestra ejes que agrupan las grandes tematicas bajo discusion, estableciendo en su primera componente un set prioritario en linea con mejora del estado de bienestar, redistribucion del ingreso y penalizacion de delitos politico/economicos. Los otros componentes apuntan a temas mas detallados, los cuales se muestran balanceados entre los diferentes votantes.

Estas herramientas son de utilidad para consolidar el numero de medidas y, en base a los patrones de votacion, extraer las tematicas que se agrupan segun el tipo de participante.

 
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

### Tema 2
El segundo tema o tipo de votante puede resumirse como aquel preferiere medidas "pragmaticas" sobre medidas "politicas".

En el extremo "politico" se ven temas consituyentes (nueva constitucion, asamblea constituyente, plebiscitos), oposicion a la situacion militar/policial (retiro de FFAA, investigacion DDHH, fin toque de queda) y causas historico/culturales (pueblos originarios, nacionalizaciones)

En el extremo "pragmatico" se ve fuerte el sueldo minimo, seguridad, transporte, salud y educacion

La distribucion muestra un sesgo "pragmatico" versus "politico"

IMPORTANTE: AL COMPARAR ESTOS RESULTADOS CON LOS DEL MODELO SIN AJUSTAR POR ELEMENTOS ANOMALOS SE VE UN CAMBIO EN EL SESGO DESDE LO "POLITICO" A LO "PRAGMATICO". EVIDENCIANDO EL SESGO DE LOS VOTOS ANOMALOS
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/tema_2.png)  

### Tema 3
El tercer tema o tipo de votante puede resumirse como aquel que promueve medidas sobre "juventud, educacion y bienestar" versus "reduccion y control de la clase piolitica"

El eje de "Juventud, educacion, y bienestar" se ven temas de apoyo a menores (sala cuna, sename, jovenes de alto riesgo), fortalecimiento a la educacion y las ciencias, ademas de mejoras al estados de bienestar (fondo comun municipal, pensiones, espacios publicos, etc).

El eje de reduccion de la clase politica se enfoca en limitar las capacidades y privilegios de ésta.

La distribucion entre participantes se puede ver bastante balanceada, con un pequeño sesgo a valorar elementos de "juventud, educacion y bienestar" por sobre la "reduccion y control de la clase politica"
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/tema_3.png)  

### Tema 4
El cuarto eje o tipo de votante contrapesa medidas del tipo "subsidios y aportes economicos" versus "penalizacion de abusos"

El eje de "subsidios y aportes economicos" toma temas como subsudios a servicios basicos, condonacion del CAE, mejora de pensiones y otros con el mismo foco econonico.

El eje de "penalizacion de abusos" muestra elementos relativos a carcel efectiva por delitos politico/economicos, reforma al sistema de notarios, ademas de elementos de ecologia y ciencia.

La distribucion es bastante balanceada, con un pequeño sesgo al la "penalizacion de abusos".
![img01](https://github.com/connectalabs/chilecracia_analisis_datos/blob/master/img/tema_4.png)  

### Tema 5
El cuarto eje o tipo de votante contrapesa medidas del tipo "ecologia y estatizacion de recursos naturales" versus "integracion social"

El eje de "ecologia" muestra iniciativas relativas a glaciares, huella de carbono y la nacionalizacion de recursos naturales como el litio y el agua.

En el eje de "integracion social" relacionados a la integracion de viviendas sociales en el barrio alto y en edificios particulares, fondo comun municipal, ademas de elementos contra las la gestion politica actual (cambio de gabinete, fin al toque de queda).

No se ve un sesgo claro en la distribucion






