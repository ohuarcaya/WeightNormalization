# Weight Normalization

La normalización de los pesos de una red neuronal a través de la reparametrización de vectores pesos ocasiona una optimización del problema, además acelera la convergencia del descenso de gradiente estocástico. La reparameterización usada en la presente investigación se basa en el *batch normalization*, pero no introduce ninguna dependencia entre los ejemplos en un *minibatch*. Es decir que el presente método también puede aplicarse con éxito a modelos recurrentes como LSTMs y a aplicaciones sensibles al ruido como el aprendizaje de refuerzo profundo o modelos generativos, para los cuales la normalización por lotes es menos adecuada. La sobrecarga computacional del método es pequeña, de esta manera permite más pasos de optimización para ser usados en la misma cantidad de tiempo. Finalmente se presentará ejemplos donde se visualizará la  gran utilidad de este método a través de algunas aplicaciones.

# Introducción

Los éxitos recientes en el aprendizaje profundo han demostrado que las redes neuronales entrenadas por la optimización basada en el gradiente de primer orden son capaces de lograr resultados asombrosos en diversos campos como la visión por computador, el reconocimiento del habla y el modelado del lenguaje. Sin embargo, este método depende en gran medida de la curvatura del objetivo que se optimiza. Si el número de condición de la matriz de Hessiana del objetivo en el óptimo es bajo, entonces el descenso de gradiente de primer orden tendrá dificultad para progresar. La cantidad de curvatura, y la optimización que se plantea, no es invariante a la reparameterización: puede haber múltiples formas equivalentes de parametrizar el mismo modelo. Entonces el principal objetivo sería encontrar la mejor forma de por parametrizar una red neuronal.

Se han desarrollado varios métodos para mejorar el acondicionamiento del gradiente de costos para arquitecturas de redes neuronales generales. Un enfoque consiste en multiplicar explícitamente el gradiente de costos por una inversa aproximada de la matriz de información de Fisher, obteniendo así un gradiente natural aproximadamente blanqueado. Alternativamente, podemos usar descenso de gradiente de primer orden estándar sin precondicionamiento, pero cambiar la parametrización de nuestro modelo para dar gradientes que son más parecidos a los gradientes naturales blanqueados de estos métodos.

Por ejemplo, podemos transformar las salidas de cada neurona para que tengan salida cero y pendiente cero en promedio. De esta manera la transformación diagonaliza aproximadamente la matriz de información de Fisher, blanqueando así el gradiente, conduciendo a un mejor desempeño de optimización. Otro enfoque en esta dirección es la normalización por lotes, un método en el que la salida de cada neurona (antes de la aplicación de la no linealidad) se normaliza por la media y desviación estándar de las salidas calculadas sobre los ejemplos en el minibatch. Esto reduce el desplazamiento covariable de las salidas de las neuronas y los autores sugieren que también aproxima la matriz de Fisher a la matriz de identidad.

Siguiendo este segundo enfoque para aproximar la optimización del gradiente natural, se presenta un método simple pero general, llamado normalización del peso, para mejorar la optimización de los pesos de los modelos de red neuronal. El método se inspira en la normalización por lotes, pero es un método determinista que no comparte la propiedad de la normalización por lotes de añadir ruido a los gradientes. Además, la sobrecarga impuesta por nuestro es menor: no se requiere memoria adicional y el cálculo adicional es insignificante.


# Normalización del Peso

Consideramos redes neuronales artificiales estándar donde el cálculo de cada neurona consiste en tomar una suma ponderada de características de entrada, seguida de una no linealidad elemental:

<p align="center">
  <img src="https://latex.codecogs.com/gif.latex?y%20%3D%20%5Cphi%28w.x%20&plus;%20b%29">
</p>

donde ![](https://latex.codecogs.com/gif.latex?w) es un vector de peso k-dimensional, ![](https://latex.codecogs.com/gif.latex?b) es un término de polarización escalar, ![](https://latex.codecogs.com/gif.latex?x) es un vector k-dimensional de características de entrada, ![](https://latex.codecogs.com/gif.latex?%5Cphi%28.%29) denota una no linealidad elemental tal como el rectificador ![](https://latex.codecogs.com/gif.latex?max%28.%2C%200%29), y ![](https://latex.codecogs.com/gif.latex?y) denota la salida escalar de la neurona.

Después de asociar una función de pérdida a una o más salidas neuronales, dicha red neuronal es comúnmente entrenada por descenso de gradiente estocástico en los parámetros ![](https://latex.codecogs.com/gif.latex?w), ![](https://latex.codecogs.com/gif.latex?b) de cada neurona.

Con la intención de acelerar la convergencia de este procedimiento de optimización, la reparameterización de cada vector de peso ![](https://latex.codecogs.com/gif.latex?w) en términos de un vector de parámetro ![](https://latex.codecogs.com/gif.latex?v) y un parámetro escalar ![](https://latex.codecogs.com/gif.latex?g) y realizar un descenso de gradiente estocástico con respecto a esos parámetros. La expresión del vector quedaría expresado de la siguiente forma:

<p align="center">
  <img src="https://latex.codecogs.com/gif.latex?w%20%3D%20%5Cfrac%7Bg%7D%7B%7C%7Cv%7C%7C%7Dv">
</p>
donde ![](https://latex.codecogs.com/gif.latex?v)  es un vector k-dimensional, ![](https://latex.codecogs.com/gif.latex?g) es un escalar, y ![](https://latex.codecogs.com/gif.latex?%7C%7Cv%7C%7C) denota la norma euclidiana de ![](https://latex.codecogs.com/gif.latex?v). Esta reparameterización tiene el efecto de fijar la norma euclidiana del vector de peso ![](https://latex.codecogs.com/gif.latex?w) : ahora tenemos ![](https://latex.codecogs.com/gif.latex?%7C%7Cw%7C%7C%20%3D%20g), independiente de los parámetros ![](https://latex.codecogs.com/gif.latex?v).

Investigaciones anteriores también desarrollaban la idea de normalizar el vector de peso, pero la optimización solo se realizaba mediante la parametrización de ![](https://latex.codecogs.com/gif.latex?w), aplicando solamente la normalización después de cada paso de descenso de gradiente estocástico. Con el presente método se reparameteriza explícitamente el modelo y realizar un descenso de gradiente estocástico en los nuevos parámetros ![](https://latex.codecogs.com/gif.latex?v), ![](https://latex.codecogs.com/gif.latex?g) directamente. De esta forma se mejora el acondicionamiento del gradiente y conduce a una convergencia mejorada del procedimiento de optimización: Al desacoplar la norma del vector de peso ![](https://latex.codecogs.com/gif.latex?g) de la dirección del vector de peso ![](https://latex.codecogs.com/gif.latex?%28v/%7C%7Cv%7C%7C%29), se acelera la convergencia de nuestra optimización de descenso de gradiente estocástico.
