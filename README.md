# AI_intro

El objetivo de este proyecto es implementar una Red Neuronal Convolutiva (CNN, por sus siglas en inglés), para la categorización de imágenes de piezas de LEGO.

Se utilizó un Dataset de 6,414 elementos (imágenes .PNG) divididos en 16 categorías, que corresponden a 16 tipos diferentes de piezas LEGO. La separación de datos para el entrenamiento y prueba del modelo fue de 80%/20%, considerando que 1280 imágenes serán suficientes para validar el correcto funcionamiento del modelo, y aprovechando el resto para un entrenamiento robusto y completo. 

## Escalamiento de Imágenes
En cuanto a las técnicas de escalamiento, se decidió utilizar las siguientes:

_**Zoom:**_ Como su nombre lo indica, es la función encargada de hacer zoom a las imágenes, de forma aleatoria dentro del rango establecido del 0 al 30%. Esto sirve para que el modelo no se quede con un solo tamaño de la pieza, y sea capaz de identificarlas sin importar que tan cercana o lejana ha sido tomada la imagen. 
_**width_shift_range:**_ Se refiere al cambio en el rango de ancho de la imagen. En otras palabras, "mueve" la imagen hacia la izquierda o derecha, en este caso dentro de un rango del 0 al 20% de su tamaño hacia cada lado, para que el modelo sea capaz de identificar piezas de LEGO no solo centradas en la imagen, sino ubicadas en distintas zonas de la misma. 
_**height_shift_range:**_ Es la función encargada de "mover" la imagen, al igual que width_shift_range, pero de forma horizontal, en un rango del 0 al 20% tanto hacia arriba como hacia abajo. Su objetivo es muy similar, el de ubicar las piezas de LEGO en distintas áreas dentro de la matriz de la imagen, para que el modelo no busque objetos únicamente en el centro de la imagen.
 Se decidió no utilizar otras funciones como la _**rotación**_, o el _**flip horizontal,**_ debido a que el dataset se encuentra ya muy completo en cuanto a los ángulos en que fueron tomadas las imágenes, por lo que dichas técnicas serían de poca utilidad al entrenar el modelo.
Es importante mencionar que el escalamiento de imágenes se lleva a cabo como parte del proceso de entrenamiento, con la misma RAM que se utiliza para correr la red. Las imágenes generadas en ningún momento se almacenan en el disco, sino que la función es utilizada únicamente una vez que se comienza a entrenar el modelo.


## Red Neuronal Convolutiva
Se utilizó como referencia la implementación propuesta por Alex Krizhevsky, Ilya Sutskever y Geoffrey E. Hinton en su paper “ImageNet Classification with Deep Convolutional Neural Networks”, y se adaptó a las capacidades de cómputo con que se disponía para la ejecución de este proyecto, así como las diferencias en dimensiones entre el dataset utilizado para su red (1.2 millones de imágenes de alta resolución, divididas en 1,000 clases), y el nuestro. A continuación se describe en detalle el diseño de la red.
Se decidió implementar un modelo secuencial, es decir, una red en la que la salida de una capa se convierte directamente en la entrada de la siguiente. El orden de las capas del modelo es el siguiente:
_**Input --> Conv2D --> Conv2D --> Conv2D --> Flatten --> Dense --> Dense --> Output**_
•	3 capas convolutivas de 2 dimensiones, en las que el tamaño de los filtros se va reduciendo, pero la cantidad de filtros a aplicar va aumentando proporcionalmente, y se aplica la función de activación ReLU (Rectified Linear Unit) en las 3. 
La implementación que se utilizó de guí sugiere un total de 5 capas convolutivas, disminuyendo de igual forma el tamaño de los filtros a aplicar, pero a una escala bastante mayor (aplicando 96 filtros de 11 x 11 x 3, y llegando hasta 256 filtros de 3 x 3 x 192). Como se mencionó anteriormente, en nuestro modelo se redujo el tamaño de dichos filtros, y la cantidad de los mismos, para una correcta adaptación al poder de cómputo con que se cuenta para el entrenamiento.
•	Una capa “Flatten”, para “aplanar”, o convertir la salida de la última capa convolutiva 2D, en un vector de una sola dimensión. Hacemos esto para que la siguiente capa (densa) funcione correctamente.
•	1 capa densa de 64 neuronas. Esta capa es también conocida como “completamente conectada” ya que todas las neuronas se conectan entre ellas.
•	Y por último, una capa densa con activación softmax, adecuada para la categorización de imágenes, y 16 neuronas, una para cada categoría del dataset con el que se está trabajando.



_link a Google Drive del Dataset: https://drive.google.com/drive/folders/1N3XUCOKy_GJDuUURXu-Upi_2kAdC32ds?usp=sharing_

_link al Notebook: https://colab.research.google.com/drive/1Ot38XblgfjPriJ4eujBNpSJjBmvzH5Om?usp=sharing


Referencias:

**Dataset -** [Joost Hazelzet]. ([2021]). [Images of LEGO Bricks], [Version 4]. Retrieved [May 15th] from [https://www.kaggle.com/datasets/joosthazelzet/lego-brick-images].

**Paper de referencia – **A. Krizhevsky, I. Sutskever, and G. E. Hinton, "ImageNet classification with deep convolutional neural networks," in Advances in Neural Information Processing Systems 25, 2012, pp. 1097-1105.
