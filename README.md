# Rincon-post1-u2Algoritmos
Paso 6: Análisis de la razón de crecimiento ( T(2n) / T(n) )
Cálculo de razones experimentales
A partir de los datos medidos (tiempos promedio en µs), se calculó la razón ( T(2n) / T(n) ) para valores consecutivos de ( n ).
•	Insertion Sort
n	     T(n)(µs)	   T(2n)(µs)	  Razón
500	   13.311	     47.218       3.55
1000	 47.218	     176.054	    3.73
2000	 176.054	   713.878	    4.05
4000	 713.878	   2671.891	    3.74
8000	 2671.891	   11297.634	  4.23
Análisis:
Las razones obtenidas oscilan alrededor de 4, con un promedio aproximado de 3.9–4.0.
Esto confirma experimentalmente que InsertionSort presenta crecimiento cuadrático, consistente con su complejidad teórica:
                                       InsertionSort=O(n^2)
InsertionSort=O(n2)
Las pequeñas variaciones observadas se deben a factores experimentales como efectos de caché y optimizaciones del compilador JIT.

•	Merge Sort
n	     T(n)(µs)	   T(2n)(µs)	 Razón
500	   10.660	     24.530	     2.30
1000	 24.530	     65.794	     2.68
2000	 65.794	     213.001	   3.24
4000	 213.001	   467.047	   2.19
8000	 467.047	   1048.898	   2.24

Análisis:
La razón se estabiliza en torno a 2.2 para valores grandes de ( n ).
Esto concuerda con la predicción teórica para algoritmos ( O(n \log n) ).
El valor atípico observado para ( n = 2000 \rightarrow 4000 ) (3.24) puede atribuirse a:
	-Variaciones experimentales
	-Efectos de caché
	-Costos constantes asociados a asignaciones de memoria
En general, el comportamiento converge hacia 2 conforme aumenta ( n ), validando:
                                       MergeSort=O(nlog⁡n)
MergeSort=O(nlogn)

•	Heap Sort
n	    T(n)(µs)	  T(2n)(µs)	 Razón
500	  6.624	      14.749	   2.23
1000	14.749	    67.372	   4.57
2000	67.372	    172.036	   2.55
4000	172.036	    400.683	   2.33
8000	400.683	    874.984	   2.18

Análisis:
Exceptuando el valor atípico en el intervalo 1000 → 2000, las razones se sitúan entre 2.1 y 2.5, mostrando convergencia hacia 2.
La desviación observada puede explicarse por:
	Fase de calentamiento del JIT
	Reorganización de memoria
	Costos constantes del heapify
En términos generales, el comportamiento experimental confirma:
                                      HeapSort=O(nlog⁡n)
HeapSort=O(nlogn)

Comparación con las predicciones teóricas
Algoritmo	          Complejidad Teórica	     Razón Esperada	        Razón Observada
InsertionSort             	O(n²)	                ≈ 4	                   ≈ 4
MergeSort	                O(n log n)	            → 2	                  ≈ 2.2
HeapSort	                O(n log n)	            → 2	                  ≈ 2.2

Los resultados experimentales coinciden con el análisis asintótico esperado. Las desviaciones observadas respecto a la predicción teórica pueden explicarse por factores propios del entorno de ejecución. En particular, durante las primeras mediciones puede influir el proceso de warm-up del compilador JIT (Just-In-Time), el cual optimiza dinámicamente el código en tiempo de ejecución. Asimismo, el comportamiento de la jerarquía de memoria (caché L1, L2 y L3) afecta significativamente el rendimiento cuando el tamaño del arreglo comienza a exceder la capacidad de caché, generando mayor latencia por accesos a memoria principal. En el caso de MergeSort, el uso de memoria auxiliar puede provocar mayor presión sobre el recolector de basura (GC), especialmente para tamaños grandes, lo que introduce pequeñas variaciones adicionales en los tiempos medidos.

Paso 7: Comparación entre MergeSort y HeapSort (misma clase asintótica)
Análisis de constantes ocultas
MergeSort y HeapSort pertenecen a la misma clase de complejidad asintótica, O(n log n). Desde el punto de vista teórico, ambos algoritmos deberían exhibir un crecimiento equivalente cuando el tamaño de entrada tiende a infinito. Sin embargo, la notación Big-O describe únicamente el comportamiento dominante del algoritmo e ignora factores constantes, efectos de arquitectura y detalles de implementación que influyen en el rendimiento real. Por esta razón, resulta necesario analizar los tiempos absolutos medidos experimentalmente para identificar diferencias que la notación asintótica no captura.
Para n = 16000, los resultados obtenidos mediante JMH muestran que MergeSort presenta un tiempo promedio de 1048.898 microsegundos, mientras que HeapSort registra 874.984 microsegundos. Al calcular el cociente entre ambos tiempos se obtiene un factor aproximado de 1.20, lo que indica que, en este entorno experimental específico, MergeSort es aproximadamente 1.20 veces más lento que HeapSort para este tamaño de entrada. Aunque ambos algoritmos crecen según la misma función n log n, sus constantes multiplicativas difieren.
Esta diferencia puede explicarse considerando el comportamiento del acceso a memoria y la jerarquía de caché del hardware moderno. MergeSort realiza accesos secuenciales durante la fase de mezcla, lo cual generalmente favorece la localidad espacial y el aprovechamiento de las líneas de caché. En contraste, HeapSort opera sobre una estructura implícita de heap en arreglo, accediendo a posiciones calculadas como 2i+1 y 2i+2, lo que produce saltos en memoria que pueden reducir la eficiencia de la caché. No obstante, en este experimento particular, el costo asociado a la memoria auxiliar requerida por MergeSort y la posible presión adicional sobre la jerarquía de memoria parecen compensar su ventaja en localidad secuencial.
Además, MergeSort requiere un arreglo auxiliar de tamaño O(n), lo que incrementa el tráfico de memoria y puede influir en el comportamiento del recolector de basura si se realizan múltiples ejecuciones o asignaciones frecuentes. HeapSort, al ser un algoritmo in-place, utiliza memoria adicional constante, lo que reduce dicho impacto. Estos factores, junto con la optimización dinámica del compilador Just-In-Time (JIT) durante la fase de warm-up, pueden introducir variaciones en los tiempos medidos que no están contempladas en el análisis asintótico tradicional.
La notación O(n log n) modela únicamente el término dominante del tiempo de ejecución y omite las constantes multiplicativas que acompañan a la expresión T(n) = c · n log n. Diferencias en el número de comparaciones, intercambios, accesos a memoria y efectos de caché pueden modificar significativamente el valor de la constante c. Por ello, aunque dos algoritmos compartan la misma clase asintótica, su rendimiento práctico puede diferir por un factor constante apreciable, como se observa en los resultados experimentales obtenidos.
En conclusión, el análisis empírico confirma que tanto MergeSort como HeapSort presentan crecimiento O(n log n), pero evidencia también que la complejidad asintótica no es suficiente para predecir el rendimiento real en hardware moderno. Las constantes ocultas, la jerarquía de memoria, el comportamiento del JIT y la gestión de memoria influyen de manera determinante en el desempeño observado, justificando la diferencia medida entre ambos algoritmos.

