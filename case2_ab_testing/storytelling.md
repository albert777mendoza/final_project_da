## Case 2 - A/B Testing  

### Objetivo

Evaluar si el sistema de recomendaciones recientemente implementado en la tienda online, generó mejoras significativas en la tasa de conversión, comparando los resultados de un grupo de control y un grupo de prueba.


### Descripción técnica

- Nombre de la prueba: `recommender_system_test`
- Grupos: А (control), B (nuevo embudo de pago)
- Launch date: 2020-12-07
- Fecha en la que dejaron de aceptar nuevos usuarios: 2020-12-21
- Fecha de finalización: 2021-01-01
- Audiencia: 15% de los nuevos usuarios de la región de la UE
- Propósito de la prueba: probar cambios relacionados con la introducción de un sistema de recomendaciones mejorado

- Resultado esperado:

* Dentro de los 14 días posteriores a la inscripción, los usuarios mostrarán una mejor conversión en vistas de la página del producto (el evento `product_page`), instancias de agregar artículos al carrito de compras (`product_card`) y compras (`purchase`).

* En cada etapa del embudo `product_page → product_card → purchase`, habrá al menos un 10% de aumento.
  
* Número previsto de participantes de la prueba: 6 000


Desarrollo: 

En el siguiente proyecto está dividido en siete pasos, iniciando con la exploración y preparación de los datos, los cuales una vez analizados permitirán construir un embudo de conversión para luego ejecutar la prueba A/B, de cuyos resultados saldrán unas conclusiones y recomendaciones una vez hayan sido analizados e interpretados.

Metodología:

Consta inicialmente de una breve narrativa que, a diferencia de la descripción técnico-analítica del Case 1 (Telecom), se enfoca enfoca más en el análisis de los resultados obtenidos en cada uno de los pasos mencionados en el párrafo anterior, con el fin comprobar si el reciente sistema implementado por la tienda, aportó o no valor a sus tasas de conversión.


Inicio:


### Paso 1. Preparación de datos

#### 1.1 Identificar grupos y usuarios (control vs test)

Una vez cargados los cuatro datasets y después de realizadas las primeras exploraciones, fueron identificados en el df participants, los grupos A y B con 8.214 (57%) y 6.311 (43%) usuarios respectivamente.

#### 1.2 Verificar consistencia: tipos de datos, NAN, duplicados, contaminación entre grupos

- Respecto a tipo de datos, sólo aquellas que tenían que ver con fecha, fueron convertidas de str a datetime.

- En cuanto al tratamiento de valores faltantes (NaN) en la columna details:

En el conjunto de datos original, la columna details, que contiene información adicional sobre el evento (por ejemplo, el monto total de la compra en USD para los eventos purchase), dueron identificados aproximadamente un 86% de valores faltantes.

Dado que las métricas principales del experimento —como la tasa de conversión de usuarios (product_page → purchase)— dependieron únicamente de las columnas user_id y event_name, que no contienen valores faltantes, se decidió no eliminarlos ni imputar los NaN en details.

Esta decisión se fundamentó en los siguientes puntos:

1. Integridad del test: El merge de los DataFrames se realizó por user_id, por lo que todos los usuarios válidos para el AB test permanecen en el análisis.
   
2. Impacto mínimo en métricas principales: Las conversiones y el conteo de usuarios no dependen de details, por lo que los NaN no afectan los resultados del experimento.

3. Documentación y transparencia: Se documenta que los valores faltantes existen y se aclarará cómo se manejan en análisis secundarios que sí utilizan details, como métricas monetarias agregadas.

En resumen, mantener los NaN en details aseguró que no se perdiera  información crítica ni se introdujera sesgo en las métricas principales del experimento, mientras que se preservó la integridad del dataset para análisis posteriores.

- Respecto a valores duplicados:

Se identificó que aproximadamente el 6.5% de los usuarios aparecían en ambos grupos (A y B). Para asegurar la confiabilidad del AB test, los 441 usuarios presentes en ambos grupos —usuarios “contaminados”— fueron eliminados, ya que cada usuario debe estar expuesto a solo una versión del experimento.


### Paso 2. Análisis inicial

#### 2.1 Comparar tamaños de muestra

El experimento AB contó con los siguientes tamaños de muestra por grupo:

Grupo A: 7,433 usuarios
Grupo B: 5,764 usuarios

Estos tamaños permiten un análisis confiable de la tasa de conversión y métricas monetarias, asegurando suficiente potencia estadística para detectar diferencias entre los grupos.

#### 2.2 Distribución por usuarios

##### 2.2.1 Eventos por usuario

A pesar de que los grupos presentan consistencia en la distribución por dispositivo y disponibilidad de eventos, se identifican desbalances en el tamaño de muestra y en la distribución temporal, lo que podría afectar la validez de los resultados del experimento.

La distribución del número de eventos por usuario es similar entre los grupos A y B, lo que indica que ambos presentan niveles de interacción comparables, reduciendo el riesgo de sesgo en el análisis del experimento.

##### 2.2.2 Eventos por día

La distribución temporal de los eventos muestra patrones similares entre ambos grupos, con un incremento notable en la actividad alrededor de fechas cercanas a eventos comerciales, seguido de una disminución hacia el final del periodo. 

No se observan anomalías significativas que afecten de forma diferencial a los grupos.

#### 2.3 Definición del universo elegible

La proporción de usuarios en el experimento (~33%) supera significativamente el 15% esperado, lo que indica una posible desviación en la asignación de tráfico.

#### 2.4 Identificar peculiaridades o anomalías antes de la prueba

* Durante la fase de análisis exploratorio se identificaron varias anomalías relevantes que podrían afectar la validez del experimento. En primer lugar, se detectó la presencia de usuarios asignados a ambos grupos, lo que indica contaminación en la segmentación y fue corregido mediante la eliminación de dichos casos.

Adicionalmente, se observó un desbalance en el tamaño de los grupos A y B, lo que, aunque no invalida el análisis, debe tenerse en cuenta en la interpretación de los resultados.

La anomalía más significativa corresponde a la proporción de usuarios asignados al experimento, la cual alcanza aproximadamente un 33%, superando considerablemente el 15% especificado en el diseño inicial. Esto sugiere una posible desviación en la asignación de tráfico o en la implementación del experimento.

Finalmente, la distribución temporal de los eventos no presentó irregularidades críticas y se alinea con las fechas definidas, por lo que no representó riesgo para el análisis.

Se observó un desbalance significativo en la distribución temporal de usuarios entre los grupos, con una mayor concentración en el grupo A, lo que indicaba que podría introducirce sesgos en los resultados, ya que el comportamiento de los usuarios podría variar según la fecha de registro.

En conjunto, estas observaciones indicaron que, si bien el experimento puede ser analizado, sus resultados debían interpretarse con cautela debido a inconsistencias en su ejecución.


### Paso 3. Construcción del embudo de conversión

-En esta etapa se observó que, el grupo A tenía más usuarios en todas las etapas que B, lo que reflejó un desbalance inicial entre los grupos, algo a tener en cuenta al interpretar resultados.

-El embudo muestra caídas en cada transición: login → product_page → product_cart → purchase. La pérdida más fuerte en ambos grupos ocurre al pasar de product_page a product_cart.

Las conversiones, mostraron los siguientes resultados:

* Conv_page_to_cart: B (0.514) > A (0.477) → B convierte ligeramente mejor aquí.

* conv_cart_to_purchase: A (1.084) > B (0.984) → A supera a B en la última etapa.

* conv_page_to_purchase: A (0.518) > B (0.506) → B no alcanza el +10% esperado; impacto negativo.

El análisis del embudo muestra que el grupo B no presenta mejoras consistentes respecto a A. Aunque B convierte ligeramente mejor de product_page a product_cart, pierde ventaja en la última etapa, resultando en una conversión total inferior (-1,1%). Los resultados deben interpretarse con cautela debido al desbalance inicial entre grupos.

### Paso 4. Ejecución de la prueba A/B
   
Hipótesis
H0: No hay diferencia en la conversión entre A y B.
H1: Sí hay diferencia en la conversión entre A y B.
Resultados del z-test
p-value (product_page → purchase): 0.3024
p ≥ 0.05 → no hay evidencia estadísticamente significativa de diferencia.

Impacto real
Conversión total A → purchase: 51,8%
Conversión total B → purchase: 50,6%
Diferencia (B – A): -1,1% → B no mejora el embudo.

Interpretación final:

Aunque B muestra ligera mejora en la etapa product_page → product_cart, pierde ventaja en la etapa final.
La diferencia general no es significativa estadísticamente.
Advertencia: Los resultados deben interpretarse con cautela debido a desbalance entre grupos, sesgo temporal y tamaño de muestra distinto.

Conclusión:

El grupo B no logra mejorar la conversión total frente a A. Las diferencias observadas no son significativas, y las anomalías del diseño experimental limitan la confiabilidad de los resultados. El cambio probado no cumple con el objetivo de +10% en el funnel.

     
### Paso 5. Evaluación de resultados

Comparación con nivel de significancia:
Nivel α = 0.05
p-value obtenido: 0.3024

Determinación de significancia estadística:
Como p-value ≥ 0.05, no hay evidencia suficiente para rechazar la hipótesis nula (H0).
Conclusión: la diferencia entre A y B no es estadísticamente significativa.

Interpretación:

El cambio implementado en el grupo B no genera una mejora significativa en el funnel frente a A, y cualquier diferencia observada (-1,1%) puede deberse al azar o a las anomalías del experimento.

### Paso 6. Interpretación de impacto

Comparación de resultados obtenidos frente a los esperados:

Se evaluaron varias métricas clave para comparar los resultados observados con los resultados esperados en el experimento A/B.

1. Usuarios participantes

Resultado esperado: 6,000
Resultado observado: A: 4,937, B: 3,715
Comentario: El número total de usuarios fue menor al esperado y existió un desbalance entre los grupos, con B recibiendo significativamente menos participantes que A.

2. Conversión de página a carrito (Conv_page → cart)

Resultado esperado: incremento ≥ 10%
Resultado observado: B: 51,4% vs A: 47,7% → diferencia +3,7%
Comentario: La mejora observada fue menor al objetivo del 10%, aunque B mostró una ligera ventaja sobre A.

3. Conversión de carrito a compra (Conv_cart → purchase)

Resultado esperado: incremento ≥ 10%
Resultado observado: B: 98,4% vs A: 108,4% → diferencia -10%
Comentario: B no mejoró en esta etapa crítica, mostrando incluso un desempeño peor que A.

4. Conversión total de página a compra (Conv_page → purchase)

Resultado esperado: incremento ≥ 10%
Resultado observado: B: 50,6% vs A: 51,8% → diferencia -1,1%
Comentario: La conversión total de B no alcanzó la meta y fue inferior a la de A.

5. Significancia estadística

Resultado esperado: p < 0.05
Resultado observado: p = 0.3024
Comentario: La diferencia entre grupos no fue estadísticamente significativa; por lo tanto, no se puede afirmar que B sea mejor que A.

* Relevancia de negocio: 
La diferencia entre A y B es -1,1% en conversión total, lo cual no representa un beneficio tangible.
El p-value indica que la diferencia no es estadísticamente significativa, por lo que no hay evidencia de que B mejore realmente el funnel.

* Justificación para implementación:
Dado que B no cumple con el objetivo de +10% y la mejora observada es mínima y no confiable, no se justifica implementar el cambio.
Además, las anomalías del experimento (desbalance entre grupos, sesgo temporal) refuerzan la necesidad de ser cautelosos.

El cambio implementado aportó valor?...

El cambio probado en B NO aportó valor significativo a la tienda online.

### Paso 7. Conclusiones y recomendaciones
   
Hallazgos:

1.En general, el grupo B no logra superar al grupo A en el rendimiento del funnel, mostrando solo una ligera mejora en la primera etapa y un desempeño inferior en las etapas críticas finales.

El embudo muestra que el grupo B no mejora consistentemente frente a A:
Conv_page → cart: ligera ventaja de B (51,4% vs 47,7%)
Conv_cart → purchase: ventaja de A (108,4% vs 98,4%)
Conv_page → purchase total: A supera a B (51,8% vs 50,6%)

2. La prueba estadística confirma que la diferencia no es significativa (p-value = 0.3024).

3. Las anomalías en el diseño experimental (desbalance de grupos, sesgo temporal) limitan la confiabilidad de los resultados.

Comparación con objetivos iniciales:

Objetivo: aumento ≥10% en cada etapa del funnel.
Resultado: B no alcanza +10% en ninguna etapa, incluso empeora en algunas.
Número de participantes menor al esperado (A: 4.937, B: 3.715 vs 6.000 previstos), con desbalance de grupo.
Conclusión: el experimento no cumple los objetivos iniciales.


Conclusiones:

El cambio implementado en B no genera mejora significativa en el funnel.
La diferencia observada es mínima (-1,1%) y no aporta valor de negocio.

Recomendación:

A la tienda On Line, se recomienda NO implementar el cambio hasta no realizar un nuevo experimento con mejor diseño que corrija los desbalances y sesgos detectados.



Fuentes: 

1. Preparación para un Test A/B
   Material de estudio Sprint 11 bootcamp D.A TripleTen
   Diseño y estructura general del embudo y la prueba A/B
   
2. “Trustworthy Online Controlled Experiments: A Practical Guide to A/B Testing” — Ron Kohavi et al. (Cambridge University Press, 2020)
    Consultas sobre: métricas, análisis estadístico y problemas típicos como desbalance de grupos o contaminación

3. “A/B Testing: The Most Powerful Way to Turn Clicks Into Customers” — Dan Siroker & Pete Koomen (Wiley, 2013)
   explica claramente el proceso de pruebas A/B con ejemplos reales y principios de toma de decisiones basados en datos.

5. “Practical Guide to Controlled Experiments on the Web: Listen to Your Customers not to the Hippo” — Ron Kohavi et al., Proceedings of the 13th ACM    SIGKDD International Conference on Knowledge Discovery and Data Mining, 2007
Fundamentos de experimentación controlada en productos web y aplicaciones digitales.
   

7.  Calculadora para la muestra
   https://www.evanmiller.org/ab-testing/sample-size.html
