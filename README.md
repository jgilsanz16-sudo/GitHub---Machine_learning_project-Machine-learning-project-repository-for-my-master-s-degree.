# Trabajo_grupal_aprendizaje_automatico
1. Descripción del problema de negocio

Las compañías de seguros médicos necesitan saber cuánto dinero pueden gastar sus clientes en sanidad para poder poner precios justos a los seguros, controlar los riesgos y planificar medidas de prevención. En este proyecto se intenta predecir el coste médico anual de cada persona (variable charges) usando información básica como la edad, el sexo, si fuma o su índice de masa corporal.
El objetivo principal es ver qué factores personales influyen más en el gasto médico. Este enfoque es más útil que solo dividir a los clientes por edad, ya que predecir el coste permite tomar mejores decisiones de negocio.

Predecir el coste médico sirve para:

    •	Ajustar el precio del seguro según el riesgo de cada cliente (por ejemplo, cobrar más a personas con mayor gasto esperado).
    •	Detectar perfiles de alto riesgo, como fumadores con sobrepeso, y aplicar medidas para reducir costes.
    •	Reducir la incertidumbre al calcular primas, ya que se conoce mejor el riesgo real de cada asegurado.

En el dataset no existe una fórmula exacta para calcular el coste médico de cada persona. Por eso se usan técnicas de Machine Learning, que permiten encontrar patrones en los datos que serían difíciles de detectar a mano.

2. Origen del dataset

El dataset empleado es el Medical Cost Personal Dataset, disponible públicamente en Kaggle (https://www.kaggle.com/datasets/mirichoi0218/insurance). Contiene datos reales de 1.338 asegurados, incluyendo sus características personales (edad, sexo), hábitos (fumador o no) y datos médicos básicos (índice de masa corporal, número de hijos dependientes), junto con el coste anual facturado por gastos médicos (charges). Este dataset se eligió por ser un conjunto de datos realista, bien estructurado y especialmente adecuado para analizar cómo distintos factores personales influyen en los costes médicos, objetivo principal de este proyecto. 

Ventajas del dataset:

    •	Contiene atributos personales relevantes (sexo, IMC, hijos, hábito de fumar, región), sin valores faltantes.
    •	Datos reales y heterogéneos: presenta una buena variedad de perfiles de asegurados.
    •	Tamaño manejable (1.338 registros) que facilita el EDA y el entrenamiento de modelos supervisados.


Desventajas del dataset:

    •	Conjunto de datos moderadamente pequeño para modelos muy complejos, lo que limita un poco el potencial predictivo.
    •	Algunas variables presentan distribuciones desbalanceadas (por ejemplo, solo el 20% son fumadores), lo que puede dificultar la generalización.
    •	No contiene información socioeconómica u otras variables clínicas detalladas que podrían mejorar las predicciones de coste (por ejemplo, ingresos, historial médico más completo, etc.).

3. Planteamiento del problema en términos científicos

Tipo de aprendizaje: El proyecto se formula como un problema de regresión supervisada, dado que la variable objetivo es numérica y continua (charges). 

Variable objetivo (target): charges, definida como el coste médico anual individual en dólares. Esta variable cuantitativa continua representa la cantidad facturada por el seguro de cada persona en el año. Se eligió charges como target porque su predicción tiene un impacto directo en las decisiones de negocio (estimación de riesgo económico por cliente). 

Vectores de atributos (X): 
Las variables empleadas como predictores para estimar charges son:

    •	age (edad del asegurado, agrupada en intervalos de edad)
    •	sex (sexo biológico: male/female)
    •	bmi (Índice de Masa Corporal, valor numérico)
    •	children (número de hijos dependientes)
    •	smoker (estatus de fumador: yes/no)
    •	region (región de residencia: northeast, northwest, southeast, southwest)

Cabe destacar que la variable age original se agrupó en tramos durante el preprocesamiento (ver Ingeniería de características) para suavizar su efecto y facilitar el aprendizaje del modelo. Todas las demás variables se utilizan en su formato original (categóricas o numéricas) según aparecían. 

Objetivo científico: Construir un modelo predictivo capaz de estimar el coste médico anual de una persona a partir de sus características personales, demográficas y de hábitos, empleando técnicas de aprendizaje automático supervisado. En resumen, el objetivo final es predecir charges de forma precisa para cada cliente nuevo, identificando a la vez las variables que más influyen en dicha predicción.

4. Enfoque técnico y metodología

Para resolver el problema se siguió una metodología clara y estructurada, cubriendo todas las fases habituales de un proyecto de machine learning:

    •   Análisis Exploratorio de Datos (EDA): se analizó la estructura y distribución de los datos, se identificaron duplicados y valores anómalos, y se estudiaron las relaciones entre variables, con especial atención a la variable objetivo charges.

    •   Ingeniería de características: a partir del EDA se limpiaron y transformaron los datos. Esto incluyó el tratamiento de duplicados, la codificación de variables categóricas, el escalado de variables numéricas y la creación de nuevas variables cuando fue necesario.

    •   Entrenamiento de modelos: se entrenaron distintos modelos de regresión, tanto lineales como no lineales, utilizando conjuntos de entrenamiento y validación. El preprocesamiento se integró mediante pipelines para garantizar la reproducibilidad.

    •   Evaluación y selección del modelo: los modelos se compararon usando métricas de error (MAE, RMSE) y el coeficiente de determinación R^2, apoyándose en validación cruzada para ajustar los hiperparámetros. Se seleccionó el modelo con mejor rendimiento.

    •   Evaluación final y conclusiones: el modelo elegido se evaluó sobre el conjunto de test, que se mantuvo separado durante todo el proceso, para estimar su capacidad de generalización. Finalmente, se interpretaron los resultados tanto a nivel técnico como desde una perspectiva práctica.

Todo el proceso quedó documentado en notebooks secuenciales (01_EDA, 02_Feature_Engineering, 03_Modelling y 04_Evaluacion_Final), de los que se extraen las principales conclusiones que se presentan a continuación.

5. Desarrollo del proyecto (EDA, modelado y evaluación)

Análisis exploratorio de datos (EDA):

Al analizar los datos, se vio que el dataset estaba bastante limpio: no había valores faltantes y solo se encontró un dato duplicado, que se eliminó. La variable charges (gastos médicos) está muy desbalanceada: la mayoría de personas tiene gastos bajos, pero existen algunos casos con costes muy altos. Estos valores extremos no se eliminaron porque representan situaciones reales.

El EDA mostró resultados esperados: los fumadores tienen gastos médicos mucho más altos que los no fumadores, y el BMI también está relacionado con mayores costes, sobre todo en fumadores. Además, a mayor edad, mayores suelen ser los gastos médicos. En cambio, el sexo y la región influyen poco en los costes, aunque el sureste presenta un promedio ligeramente más alto. En general, la edad y los hábitos de vida son las variables más importantes.

Preparación de los datos (ingeniería de características):
Antes de entrenar los modelos, se hicieron varios ajustes:

    •   Se eliminó el registro duplicado.
    •   No se eliminaron los outliers de charges.
    •   La edad se agrupó en rangos (18–25, 26–35, etc.) para reducir el ruido.
    •   Las variables categóricas (sexo, fumador y región) se transformaron en variables numéricas usando One-Hot Encoding.

Las variables numéricas se escalaron para que todas estuvieran en rangos similares.
Todo este proceso se hizo con pipelines para asegurar que el tratamiento de los datos fuera consistente.

Modelado:

El problema se abordó como uno de regresión. Los datos se dividieron en entrenamiento, validación y test.

Primero se usó una regresión lineal como modelo base, junto con Ridge, pero ambos dieron resultados parecidos y bastante limitados.

Después se probaron modelos más complejos: Random Forest y Gradient Boosting. Estos modelos funcionaron mejor porque captan relaciones no lineales. De ellos, el Gradient Boosting fue el que obtuvo mejores resultados en validación (R^2 ≈ 0,77).

Tras esto, se ajustaron sus hiperparámetros con GridSearchCV, obteniendo como mejor configuración un modelo con 200 árboles y profundidad máxima de 8. Este fue el modelo final elegido.

Evaluación final:

Al evaluar el modelo final en el conjunto de test, se obtuvieron los siguientes resultados: MAE ≈ 5.649, RMSE ≈ 7.526 y R^2 ≈ 0,67. Esto significa que el modelo explica alrededor del 67% de la variabilidad de los gastos médicos y que el error medio ronda los 5.600 dólares.
Aunque el rendimiento baja un poco respecto a la validación, el modelo generaliza de forma razonable. Se observa que tiende a subestimar los costes muy altos, algo normal por la presencia de outliers. Aun así, el modelo cumple bien el objetivo del proyecto y confirma que variables como fumar, la edad y el IMC son claves para predecir los costes médicos.

6. Conclusiones finales del proyecto

El proyecto concluye con un modelo de regresión basado en Gradient Boosting capaz de predecir el coste médico anual de un cliente con una precisión razonable (R^2≈0,67 en datos no vistos). Desde el punto de vista técnico, este resultado es positivo teniendo en cuenta el tamaño moderado del conjunto de datos y la alta variabilidad propia de los costes médicos.

El modelo captura correctamente los principales patrones del problema: predice mayores costes para fumadores, personas de mayor edad y con BMI elevado, lo que es coherente con la realidad. En cambio, variables como la región o el sexo muestran una influencia limitada, por lo que pueden simplificarse sin perder capacidad predictiva.

Aun así, el modelo presenta limitaciones. Aproximadamente un 33 % de la variabilidad de charges no queda explicada, lo que sugiere la existencia de factores relevantes no incluidos en los datos, como variables socioeconómicas o información clínica más detallada. Además, el tamaño del dataset (1.338 observaciones) limita la capacidad del modelo para generalizar, especialmente en casos poco frecuentes; disponer de más datos permitiría entrenar modelos más robustos.

Desde una perspectiva de negocio, el modelo aporta valor práctico. Puede utilizarse para estimar el coste esperado de un nuevo cliente y ajustar las primas de forma más objetiva y basada en datos. Por ejemplo, confirma que el tabaquismo es el factor con mayor impacto en el gasto sanitario, lo que justifica recargos específicos o programas de prevención. De forma similar, un BMI elevado se asocia a mayores costes, lo que puede motivar planes de salud personalizados. En cambio, la baja relevancia de la región indica que no es necesario diferenciar precios de forma significativa por zona geográfica.

En conclusión, se ha desarrollado un modelo predictivo que cumple los objetivos del proyecto: segmentar a los asegurados según su riesgo económico y apoyar la toma de decisiones en tarificación y gestión del riesgo. Como trabajo futuro, la incorporación de más datos y variables relevantes permitiría mejorar la precisión del modelo, pero incluso en su estado actual supone un primer paso sólido hacia una gestión más data-driven de los seguros de salud.

7. Uso de Git

Para cumplir con las buenas prácticas de control de versiones, el equipo utilizó Git y GitHub durante el desarrollo del proyecto. Todos los integrantes realizaron commits frecuentes con sus aportaciones, manteniendo un historial de cambios claro y trazable. El repositorio del proyecto se estructuró de forma colaborativa, integrando finalmente todo el código en la rama principal (main) para la entrega. Cada miembro contribuyó mediante pull requests y resolución conjunta de conflictos, asegurando la coherencia del código. El repositorio GitHub del proyecto es público y recoge tanto el código fuente como los documentos generados durante el desarrollo. Repositorio en GitHub: https://github.com/pablodiaz35/Trabajo_grupal_aprendizaje_automatico

8. Estructura del repositorio

La organización de los archivos del proyecto es la siguiente:
project/
├── data/             # Datos originales (ej: insurance.csv)
├── html/             # Notebooks exportados en HTML (EDA, feature engineering, modelado, evaluación)
├── notebooks/        # Notebooks Jupyter originales (.ipynb) con el código y análisis
└── README.md         # Documento README con detalles del proyecto
Esta estructura facilita la localización de cada componente: los datos brutos, los análisis intermedios (en notebooks) y los resultados finales (reportes HTML y README).

9. Autores

Equipo de trabajo e información de contacto:

Pablo Díaz Pérez — pablo.diazperez@cunef.edu
Javier Gilsanz Muñoz — javier.gilsanz@cunef.edu
Álvaro Pasqual del Pobil Hausmann — alvaro.pasqual@cunef.edu

10. Entrega

El proyecto se entrega cumpliendo con los requerimientos de la asignatura:
En formato ZIP, respetando la estructura de directorios solicitada.
Subido en paralelo al repositorio GitHub para control de versiones y transparencia.
Incluyendo los notebooks exportados a HTML en la carpeta /html para facilitar su consulta sin necesidad de entorno de ejecución.
Con esto, se proporciona toda la documentación y código necesarios para evaluar el trabajo de forma autónoma y reproducir los resultados obtenidos.