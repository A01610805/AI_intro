# Implementación de Clasificador de piezas de LEGO utilizando XCeption

## Introducción

El objetivo de este proyecto es implementar una Red Neuronal Convolutiva (CNN, por sus siglas en inglés), utilizando el principio de “Transfer Learning” con la herramienta “XCeption” para la categorización de imágenes de piezas de LEGO.

Se utilizó un Dataset _[1]_ de 6,414 elementos (imágenes .PNG) divididos en 16 categorías, que corresponden a 16 tipos diferentes de piezas LEGO. La separación de datos para el entrenamiento y prueba del modelo fue de 80%/20%, considerando que 1280 imágenes serán suficientes para validar el correcto funcionamiento del modelo, y aprovechando el resto para un entrenamiento robusto y completo.

A continuación se describe el proceso por el que se pasó para llegar a la versión final del modelo, incluyendo una versión muy poco funcional del mismo.  



## Escalamiento de Imágenes
Antes del entrenamiento del modelo como tal, las imágenes fueron preprocesadas mediante diferentes técnicas de escalamiento. Luego de un análisis del dataset, observando el tipo de imágenes con que se contaba, se decidió utilizar las siguientes técnicas:

_**Zoom:**_ Como su nombre lo indica, es la función encargada de hacer zoom a las imágenes, de forma aleatoria dentro del rango establecido del 0 al 30%. Esto sirve para que el modelo no se quede con un solo tamaño de la pieza, y sea capaz de identificarlas sin importar que tan cercana o lejana ha sido tomada la imagen. 

_**width_shift_range:**_ Se refiere al cambio en el rango de ancho de la imagen. En otras palabras, "mueve" la imagen hacia la izquierda o derecha, en este caso dentro de un rango del 0 al 20% de su tamaño hacia cada lado, para que el modelo sea capaz de identificar piezas de LEGO no solo centradas en la imagen, sino ubicadas en distintas zonas de la misma. 

_**height_shift_range:**_ Es la función encargada de "mover" la imagen, al igual que width_shift_range, pero de forma horizontal, en un rango del 0 al 20% tanto hacia arriba como hacia abajo. Su objetivo es muy similar, el de ubicar las piezas de LEGO en distintas áreas dentro de la matriz de la imagen, para que el modelo no busque objetos únicamente en el centro de la imagen.

 Se decidió no utilizar otras funciones como la _**rotación**_, o el _**flip horizontal,**_ debido a que el dataset se encuentra ya muy completo en cuanto a los ángulos en que fueron tomadas las imágenes, por lo que dichas técnicas serían de poca utilidad al entrenar el modelo.
 Es importante mencionar que el escalamiento de imágenes se lleva a cabo como parte del proceso de entrenamiento, con la misma RAM que se utiliza para correr la red. Las imágenes generadas en ningún momento se almacenan en el disco, sino que la función es utilizada únicamente una vez que se comienza a entrenar el modelo.

Por último, una vez que declaramos el batch de imágenes, tanto para entrenamiento como para validación, éstos pasan por un proceso de “**_Shuffle_**”, para no cesgar el entrenamiento del modelo, ni desbalancear la cantidad de imágenes que recibe de cada categoría.

## Red Neuronal Convolutiva

**_Versión 1_**

Se utilizó inicialmente como referencia la implementación propuesta por Alex Krizhevsky, Ilya Sutskever y Geoffrey E. Hinton en su paper “ImageNet Classification with Deep Convolutional Neural Networks” _[2]_, y se adaptó a las capacidades de cómputo con que se disponía para la ejecución de este proyecto, así como las diferencias en dimensiones entre el dataset utilizado para su red (1.2 millones de imágenes de alta resolución, divididas en 1,000 clases), y el nuestro. A continuación se describe en detalle el diseño de la red.

Se decidió implementar un **_modelo secuencial_**, es decir, una red en la que la salida de una capa se convierte directamente en la entrada de la siguiente. El orden de las capas del modelo es el siguiente:
_**Input --> Conv2D --> Conv2D --> Conv2D --> Conv2D --> Conv2D --> Flatten --> Dense --> Dense  --> Dense --> Output**_

•	5 capas convolutivas de 2 dimensiones, en las que el tamaño de los filtros se va reduciendo, pero la cantidad de filtros a aplicar va aumentando proporcionalmente, y se aplica la función de activación ReLU (Rectified Linear Unit) en las 5. 
La implementación que se utilizó de guía disminuye de igual forma el tamaño de los filtros a aplicar, pero a una escala bastante mayor (aplicando 96 filtros de 11 x 11 x 3, y llegando hasta 256 filtros de 3 x 3 x 192). Como se mencionó anteriormente, en nuestro modelo se redujo el tamaño de dichos filtros, y la cantidad de los mismos, para una correcta adaptación al poder de cómputo con que se cuenta para el entrenamiento.

•	Una capa “Flatten”, para “aplanar”, o convertir la salida de la última capa convolutiva 2D, en un vector de una sola dimensión. Hacemos esto para que la siguiente capa (densa) funcione correctamente.

•	Unac capa densa de 128 neuronas. Esta capa es también conocida como “completamente conectada” ya que todas las neuronas se conectan entre ellas.

•	Otra capa densa, pero esta de 64 neuronas.

•	Y por último, una capa densa con activación softmax, adecuada para la categorización de imágenes, y 16 neuronas, una para cada categoría del dataset con el que se está trabajando.

Sin embargo, la implementación de este modelo fue poco exitosa, ya que el porcentaje de precisión dentro del entrenamiento en ningún momento pasó el 35%, y al probarlo, no llegó siquiera al 10% de presición (se muestran los resultados más a detalle en "Testing y Métricas").

_**Versión 2 (XCeption)**_

Una vez que se dio por deprecado el modelo inicialmente propuesto, se tomó la decisión de trabajar utilizando el principio de **_Transfer Learning_** _[3]_, el cual utiliza un modelo previamente entrenado como base previo al entrenamiento. 

En este caso, se utilizó como base el modelo de “imagenet”, uno de los datasets públicos más grandes y más utilizados para la categorización de imágenes. A este modelo se le agregó:

•	Una capa densa, de 1024 neuronas
•	Una capa de Dropout para evitar el overfitting (el sobre-entrenamiento del modelo, donde memoriza en lugar de aprender)
•	Y la capa densa de salida con 16 neuronas (una por cada categoría). 

Además, se agregó un paso de validación (cross-validation) dentro del entrenamiento, para conocer los avances reales del entrenamiento incluso antes del proceso de pruebas.

El paper [3] sugiere que además se declaren como entrenables las capas del modelo base, sin embargo, por cuestiones de tiempo y falta de poder computacional, se decidió entrenar únicamente las capas agregadas que se mencionaron anteriormente.

El modelo anteriormente mencionado logró un 89.1% de accuracy en training y un 72.6% en validación


## Testing y Métricas

_**Versión 1**_

Luego de haber entrenado el modelo hasta su estancamiento en el 34% de accuracy dentro del set de entrenamiento, se procedió a probar con el set de Testing, obteniendo un 9% de accuracy, apenas 3% por encima de lo que se obtendría si se categorizaran de forma aleatoria. 

Haciendo un análisis más profundo, buscando el porqué de estos resultados, se obtuvieron datos interesantes, muy útiles para dar los siguientes pasos en el mejoramiento del modelo. Al construir una matriz de confusión con los datos obtenidos en las pruebas,  se observó que la gran mayoría de las predicciones que hace el modelo, son de las categorías 14, 6 y 4. Es decir,  está “confundiendo” varias categorías de piezas y simplificando su clasificación casi en su totalidad, a estas 3 categorías. 
Se podría decir que es un problema de overfitting, interpretando que el modelo “memorizó” ciertos patrones. Sin embargo, al tener un porcentaje tan bajo de accuracy en el entrenamiento, es muy poco probable que este sea el caso.

_**Versión 2**_

Luego de haber entrenado el modelo de Xception hasta el 88% de accuracy en training (valor muy similar al que se presenta en el paper [3]), se corrieron pruebas que resultaron en un 87.5% de accuracy que, si bien es un modelo aún con posibles mejorasno es un modelo perfecto, logró una mejora de casi 1,000% (o 10x) con respecto a la primer versión presentada.
(para ver la matriz de confusión, léase "documentación final.pdf")

## Conclusión

Luego de haber entrenado y probado ambos modelos, podemos llegar a la conclusión de que la implementación de una CNN pre-entrenada fue la solución más adecuada para el problema planteado. 


(La documentación con imágenes y gráficas se encuentra en los PDFs dentro de este repositorio.  _documentacion.pdf_ es la versión del 29 de mayo, y _Documentación Final.pdf_ es, como su nombre lo indica, la versión final)


## Referencias:

**_[1]_ - Dataset -** [Joost Hazelzet]. ([2021]). [Images of LEGO Bricks], [Version 4]. Retrieved [May 15th] from [https://www.kaggle.com/datasets/joosthazelzet/lego-brick-images].

**_[2]_ - Paper de referencia –** A. Krizhevsky, I. Sutskever, and G. E. Hinton, "ImageNet classification with deep convolutional neural networks," in Advances in Neural Information Processing Systems 25, 2012, pp. 1097-1105.

**_[3]_ - Paper Xception -** X. Wu, R. Liu, H. Yang and Z. Chen, "An Xception Based Convolutional Neural Network for Scene Image Classification with Transfer Learning," 2020 2nd International Conference on Information Technology and Computer Application (ITCA), Guangzhou, China, 2020, pp. 262-267.

_link a Google Drive del Dataset: https://drive.google.com/drive/folders/1N3XUCOKy_GJDuUURXu-Upi_2kAdC32ds?usp=sharing_

Link a Notebook del primer modelo: https://colab.research.google.com/drive/1Ot38XblgfjPriJ4eujBNpSJjBmvzH5Om?usp=sharing

Link a Notebook del modelo con Xception:
https://colab.research.google.com/drive/1ThbQHjBURHzVIor1GoqaGVfs58MS9pbm?usp=sharing

