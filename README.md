# Sampling-based Algorithms for Motion Planning

## Resumen

Este proyecto de verano (1 de junio al 31 de julio de 2023) se enfocó en **algoritmos de muestreo para la planificación de movimiento en robótica**. Nuestro equipo, compuesto por 4 integrantes y supervisado por el Dr. Israel Becerra Durán, investigó e implementó los siguientes algoritmos:

- **PRM***  
- **RRT**  
- **RRG**  
- **RRT***  

El trabajo se basó en el documento _Sampling-based algorithms for optimal motion planning_ (2011) de Sertac Karaman y Emilio Frazzoli, así como en el libro _Planning Algorithms_ (2006) de Steven M. LaValle. En este documento, describimos la implementación y simulación de estos algoritmos, así como el uso de redes neuronales para predecir trayectorias basadas en las simulaciones.

---

## Planteamiento de Escenario

El objetivo fue evaluar el desempeño de los algoritmos en un entorno controlado e igual para cada uno. Las simulaciones, realizadas en **Python**, usaron un **laberinto 2D** de 16 casillas (4x4). El robot fue representado como un objeto circular de radio 0.25.

---

## Implementación de los Algoritmos

Cada integrante del equipo implementó uno de los algoritmos descritos en _Sampling-based algorithms for optimal motion planning_. Inicialmente, consideramos puntos iniciales fijos y una cantidad fija de iteraciones para generar las trayectorias.

### Observaciones

- **RRT y RRT*** muestran claramente una estructura de árbol donde las aristas no forman ciclos.  
- **PRM* y RRG**, al estar basados en grafos, presentan una mayor densidad de aristas y presencia de ciclos.  
- **RRT*** genera trayectorias más óptimas que **RRT**, con trayectorias más rectas debido a su proceso de _re-cableado_. Sin embargo, las trayectorias tienden a acercarse demasiado a los bordes del laberinto.  
- Los algoritmos basados en grafos permiten llegar a más ubicaciones del mapa de diferentes maneras, aunque no siempre producen trayectorias tan directas como los basados en árboles.

---

## Entrenamiento de Redes Neuronales

Utilizamos una red neuronal para cada algoritmo con el objetivo de predecir trayectorias a partir de los datos generados. Generamos un total de **2400 trayectorias** para entrenar las redes, considerando puntos de inicio y destino aleatorios en cada una de las 16 casillas del laberinto.

### Estructura de la Red Neuronal

Dado un punto inicial _p_ y un punto objetivo _q_, la red neuronal recibe como entrada la tupla \([p_t, q]\) y devuelve un punto siguiente _p_{t+1}_, acercándose idealmente a _q_.

Para construir y entrenar las redes neuronales, usamos **TensorFlow** y **Sklearn**:

- **TensorFlow:** Usamos _dropout_ del 25% para evitar el sobreajuste y establecimos un máximo de 4000 iteraciones.  
- **Sklearn:** Red neuronal básica sin _dropout_, también con un límite de 4000 iteraciones.

---

## Resultados

### Predicción de Trayectorias con Sklearn

| Algoritmo | % Trayectorias exitosas | Promedio de tiempo | Promedio de costo (longitud) | Desv. estándar de tiempo | Desv. estándar de costo |
|-----------|--------------------------|--------------------|-----------------------------|--------------------------|-------------------------|
| PRM*      | 30%                      | 0.00137           | 3.72                        | 0.00133                  | 3.05                   |
| RRT       | 82%                      | 0.00153           | 201.93                      | 0.00099                  | 152.54                 |
| RRG       | 32%                      | 0.00196           | 3.44                        | 0.00127                  | 2.28                   |
| RRT*      | 42%                      | 0.00391           | 4.12                        | 0.00244                  | 2.54                   |

---

### Predicción de Trayectorias con TensorFlow

| Algoritmo | % Trayectorias exitosas | Promedio de tiempo | Promedio de costo (longitud) | Desv. estándar de tiempo | Desv. estándar de costo |
|-----------|--------------------------|--------------------|-----------------------------|--------------------------|-------------------------|
| PRM*      | 36%                      | 0.4661            | 4.35                        | 0.614                   | 3.12                   |
| RRT       | 99%                      | 0.9934            | 202.98                      | 0.522                   | 178.91                 |
| RRG       | 23%                      | 0.4440            | 2.64                        | 0.340                   | 2.06                   |
| RRT*      | 59%                      | 0.2875            | 4.80                        | 0.167                   | 2.79                   |

---

### Predicción de Trayectorias con TensorFlow (Dropout)

| Algoritmo | % Trayectorias exitosas | Promedio de tiempo | Promedio de costo (longitud) | Desv. estándar de tiempo | Desv. estándar de costo |
|-----------|--------------------------|--------------------|-----------------------------|--------------------------|-------------------------|
| PRM*      | 30%                      | 0.6948            | 3.40                        | 0.875                   | 2.45                   |
| RRT       | 86%                      | 2.059             | 348.37                      | 2.25                    | 427.91                 |
| RRG       | 24%                      | 0.4374            | 2.78                        | 0.353                   | 2.24                   |
| RRT*      | 37%                      | 0.2323            | 3.47                        | 0.138                   | 2.15                   |

---

## Conclusiones

- **RRT** obtuvo el mayor porcentaje de trayectorias exitosas en todas las redes neuronales. Esto se debe a que sus trayectorias no se apegan a las paredes del laberinto, reduciendo el riesgo de colisiones.  
- **RRT*** genera trayectorias óptimas en menor tiempo y con menor costo, pero tiende a acercarse demasiado a las paredes, lo que provoca más colisiones.  
- **RRG** mostró un comportamiento similar a RRT*, con trayectorias óptimas pero menos exitosas por la misma razón.  
- **PRM*** tiene un porcentaje bajo de trayectorias exitosas, ya que las trayectorias generadas consisten en pocos puntos intermedios, lo que dificulta el entrenamiento de la red neuronal.  

---

## Referencias

- Karaman, Sertac, and Emilio Frazzoli. _Sampling-based algorithms for optimal motion planning_, 2011.  
- LaValle, Steven M. _Planning Algorithms_, 2006.  

