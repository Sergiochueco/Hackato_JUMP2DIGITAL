# Proyecto Hackato_JUMP2DIGITAL Barcelona

<p align="center">
   <img align="center" width="800" src="https://raw.githubusercontent.com/Sergiochueco-94/Hackato_JUMP2DIGITAL/main/Images/Banner.PNG" />
</p>


1. **Introducción**: Presentación del conjunto de datos y de les variables seleccionadas.

    He elegido las siguientes bases de datos para el reto:
    
        - 2017_lloguer_preu_trim (conjunt base)
        - 2017_poblacio_exposada_barris_mapa_estrategic_soroll_bcn_long


    Con estas dos bases de datos, he intentado realizar la unión de ambos conjuntos de datos de la mejor manera posible y extraer datos interesantes para una posible futura fase del proyecto, como podría ser un modelo de ML (Machine Learning) o DL (Deep Learning).


        Trabajaremos con las siguientes versiones

        Pandas 2.0.2
        Numpy 1.25.0
        Seaborn 0.12.2
        Sklearn 1.3.1
        Python 3.11.3

2. **Depuración de datos**: Descripción detallada de las técnicas de preprocesamiento aplicadas y los criterios de evaluación utilizados.

    Por un lado, el dataset de **lloguer**:
    
    Hemos visto que el DataFrame de lloguer esta compuesto por 584 filas, 292 con precio medio mensual del alquiler y los otros 292 son exactamente las mismas filas varian que el precio esta puesto por m2. Así que vamos a generar un "id" único para cada fila, ya que cada fila corresponde a un trimestre, distrito y barrio distintos. 

    Después colocaremos esas 292 filas con precio por m2 y lo convertiremos en una columna, de tal manera que nos quedaremos con un Dataframe de lloguer de 292 filas y con valores de precio medio mensual y precio por m2 en columnas diferenciadas 

    Para imputar los valores nulos presentes en este dataset, primero realizaremos un groupby por barrio y realizaremos un bfill, ffill, por si alguno de ellos tuviese algun valor en un determinado trimestre, como el ejemplo de "VALLBONA", donde nos interesa más que nos impute su mismo valor que el valor de otro barrio. Para el resto en el que el valor sea todo nulo, el KNNImputer buscará por cercania los valores que correspondan y los imputará, seguramente cogerá los distritos e imputará por distritos los precios medios.



    Una vez limpio y ordenado este dataset, pasamos al dataset de **poblacio**:

    En este dataset no tenemos valores nulos ni duplicados. Pero si tenemos columnas con gran cantidad de etiquetas.

    Por un lado, la columna "Concepte", agruparemos las diferentes etiquetas en:

    Clasificación general de Concepte, según la web de www.barcelona.cat,lo divide en estas 9 categorias diferentes:

    - total
    - patios
    - ocio
    - peatones
    - industria
    - ferrocarril
    - grandes infraestructuras
    - tránsito
    - parques

    

    Por otro lado, a "Rang_soroll", también dividimos etiquetas en:

        if valor in ["<40 dB", "40-45 dB", "45-50 dB"]:
          return "Bajo"
        elif valor in ["50-55 dB", "55-60 dB", "60-65 dB", "65-70 dB", "70-75 dB"]:
          return "Medio"
        else:
          return "Alto"

    De esta manera, también reducimos las dimensiones de nuestro dataset.

    Una vez tenemos las etiquetas agrupadas, realizamos un groupby por 'barrio','distrito','concepte' y "nivel_ruido, para aplicar la media de las etiquetas y así que los valores sean acordes a la reducción de etiquetas.


       df_poblacio['Media_Valor_Concepte'] = df_poblacio.groupby(["Codi_Districte","Codi_Barri","Nivell_Soroll","Concepte"])[['Valor_Porcentaje']].transform(np.mean)

    Eliminamos las columnas de "rango de ruido" y "Valor_Porcentaje", y así, podemos hacer un drop_duplicates() para reducir aún más las dimensiones de nuestro dataset.

    Convertimos las columnas tipo object a numéricas, como son el caso de Nivell_Soroll.


    Una vez los dos dataset limpios, los mergeamos y obtenemos un único dataset. Hemos creado un dataset en el que recoge por cada trimestre, distrito y barrio el precio medio del alquiler tanto mensual como por m2. Además tenemos información por niveles de ruido y que % de agentes contaminantes está formado ese nivel de ruido.

    De ahí que obtengamos un dataset final de 7884 filas (4 trimestres, 73 barrios , 3 niveles de ruido y 9 conceptos contaminantes diferentes) y 11 columnas

   Una vez hecho esto, realizamos un OHE a la columnas categoricas, en este caso, solo tenemos "Concepte". De esta manera, ya tenemos todas las variables numéricas para nuestro futuro modelo.

   Por último, escalamos nuestros datos con MinMaxScaler(), de esta manera reducimos la varianza de nuestros datos.





3. **Resultados**: Presentación de los resultados obtenidos. 
        
<p align="center">
  <img align="center" width="600" src="https://raw.githubusercontent.com/Sergiochueco-94/Hackato_JUMP2DIGITAL/main/Images/analisis_precio.PNG" />
</p>

<p align="center">
  <img align="center" width="600" src="https://raw.githubusercontent.com/Sergiochueco-94/Hackato_JUMP2DIGITAL/main/Images/concepto_ruido.PNG" />
</p>

<p align="center">
  <img align="center" width="600" src="https://raw.githubusercontent.com/Sergiochueco-94/Hackato_JUMP2DIGITAL/main/Images/seleccion_datos.PNG" />
</p>



 Se añaden más resultados, en el apartado de conclusiones


4. **Conclusiones**: Principales inferencias derivadas de los resultados obtenidos.  
        
   Podemos observar como la mayoría de precio oscilan entre 600€ y 1000€ aprox (€/mes).La distribución es parecida a una distribución normal,lo que ayudará a nuestro futuro modelo,aunque un poco a la izquierda, pero tiene sentido que haya alquileres a precios más cercanos a la media que a precios más altos.

   Podemos ver también, como la mayoría de precio oscila entre 10€ y 15€ aprox ( €/m2).En este caso,la distribución es parecida a una distribución normal, lo que ayudará a nuestro futuro modelo


   Observamos como en todos los trimestres Sarrià-Sant Gervasi y Les Corts son los dos distritos con los precios más altos, respectivamente.
   Por contra, Nou Barris y Sant Andreu los distritos más baratos, respectivamente.

   Podríamos decir que los agentes con nivel de ruido más altos son los de Tráfico, Grandes_infraestructuras y Patios, respectivamente. Ya que como se puede observar todos tienen un nivel 0 de ruído muy alyto, es decir que no superan los 50dB, pero estos que comentamos podemos ver como el nivel 1 de ruido que va de los 50-75dB, son un poco más notables


   En definitiva, como hemos ido observando a lo largo del proyecto, los precios son más altos en determinados Barrios y Distritos, el precio parece estar relacionado con los m2 de las viviendas. Por otro lado, a nivel de contaminación acústica, hemos podido observar que hay agentes contaminantes con mayor elevado nivel sonoro que otros, como el caso del tránsito o grandes infraestructuras, como era de esperar. Según la tabla de correlación final de los datos, el nivel de ruido no parece estar correlacionado con el precio de alquiler de la vivienda. Así que, probablemente este más afectado por la superficie como comentábamos anteriormente. 
