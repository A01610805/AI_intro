# AI_intro

_link a Google Drive del Dataset: https://drive.google.com/drive/folders/1N3XUCOKy_GJDuUURXu-Upi_2kAdC32ds?usp=sharing_


Se utilizará un Dataset de 6,414 elementos (imágenes .PNG) divididos en 16 categorías.
La separación de datos para el entranamiento y prueba del modelo fue de 80%/20%, considerando que 1280 imágenes serán suficientes para validar el correcto funcionamiento del modelo, y aprovechando el resto para un entrenamiento robusto y completo.

En cuanto a las técnicas de escalamiento, se utiliza en zoom, así como el cambio en el rango de ancho y largo de la imagen. Se decidió no utilizar otras funciones como la rotación, o el flip horizontal, debido a que el dataset se encuentra ya muy completo en cuanto a los ángulos en que fueron tomadas las imágenes, por lo que dichas técnicas nos serían de poca utilidad.



