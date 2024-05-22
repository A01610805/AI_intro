# AI_intro


Se utilizará un Dataset de 6,414 elementos (imágenes .PNG) divididos en 16 categorías.
La separación de datos para el entrenamiento y prueba del modelo fue de 80%/20%, considerando que 1280 imágenes serán suficientes para validar el correcto funcionamiento del modelo, y aprovechando el resto para un entrenamiento robusto y completo.

En cuanto a las técnicas de escalamiento, se decidió utilizar las siguientes:
Zoom -> Como su nombre lo indica, es la función encargada de hacer zoom a las imágenes, de forma aleatoria dentro del rango establecido del 0 al 30%. Esto sirve para que el modelo no se quede con un solo tamaño de la pieza, y sea capaz de identificarlas sin importar que tan cercana o lejana ha sido tomada la imagen.
_width_shift_range_ -> Se refiere al cambio en el rango de ancho de la imagen. En otras palabras, "mueve" la imagen hacia la izquierda o derecha, en este caso dentro de un rango del 0 al 20% de su tamaño hacia cada lado, para que el modelo sea capaz de identificar piezas de LEGO no solo centradas en la imagen, sino ubicadas en distintas zonas de la misma.
height_shift_range -> Es la función encargada de "mover" la imagen, al igual que _width_shift_range_, pero de forma horizontal, en un rango del 0 al 20% tanto hacia arriba como hacia abajo. Su objetivo es muy similar, el de ubicar las piezas de LEGO en distintas áreas dentro de la matriz de la imagen, para que el modelo no busque objetos únicamente en el centro de la imagen.
Se decidió no utilizar otras funciones como la _rotación_, o el _flip horizontal_, debido a que el dataset se encuentra ya muy completo en cuanto a los ángulos en que fueron tomadas las imágenes, por lo que dichas técnicas serían de poca utilidad al entrenar el modelo.


_link a Google Drive del Dataset: https://drive.google.com/drive/folders/1N3XUCOKy_GJDuUURXu-Upi_2kAdC32ds?usp=sharing_

_link al Notebook: https://colab.research.google.com/drive/1Ot38XblgfjPriJ4eujBNpSJjBmvzH5Om?usp=sharing
