Case 1 - Telecomunicaciones
Objetivo:
Identificar operadores ineficaces del servicio de telefonía virtual CallMeMaybe

Desarrollo:

En el siguiente proyecto está dividido en seis pasos, iniciando con la carga, exploración y limpieza de datos, pasando luego a realizar las correspondientes integraciones y respectivo análisis enfocado en identificar los operadores ineficaces de la compañía.

Metodología:

En cada uno de los pasos mencionados en el párrafo anterior, se hizo una descripción objetiva y organizada de lo que fué su desarrollo desde lo técnico - analítico, resaltando a su vez los hallazgos emergentes, que fueron la fuente de principal en el proceso de identificación de insights y la generación de conclusiones y recomendaciones.

Orden:

Cabe mencionar que, desde el punto de vista de orden y coherencia entre la tabla de contenido y su respectivo desarrollo en notebook-VS, ambos siguen la ordinalidad especificada para cada uno de sus ítems.

Inicio:

Paso 1. Carga y exploración de datos
Revisar estructura, tipos de datos y valores faltantes:
Se llevó a cabo la importación de librerías y carga de ambos dataset sin inconveniente. A la vez, en esta exploración inicial, se observó lo siguiente :

1.1 Dataset clients
a. Variables clave identificadas: Se destacan user_id como identificador único y tariff_plan, que permite segmentar a los usuarios.

b. Valores Nulos: No hay

c. Tipo de datos: * Columna date_start se encuentra en formato string, por lo que se debe convertir a tipo datetime para facilitar el análisis temporal. * Columna tariff_plan se encuentra en formato string, pero es un conjunto limitado de valores(planes A y B) y se debe convertir a categórico para la posterior facilidad de análisis.

1.2 Dataset (telecom_new):
a. variables clave identificadas: calls_count, total_call_duration y is_missed_call permiten analizar el uso y la calidad del servicio. Así mismo, la variable user_id será clave para relacionar ambos datasets en etapas posteriores del análisis.

b. Valores Nulos:

La columna operator_id presenta un total de 8172 valores nulos
La columna internal presenta 117 nulos, las demás tienen sus datos completos.
c. Tipo de datos: Este DF presentó las siguientes inconsistencias. date aparece como STR, por lo cual debe ser cambiado a (pd.to_datetime) internal es tipo object, pero debe ser cambiado a bool (por ser binaria). Las demás columnas, presentan tipos de datos acordes a su contenido.

Paso 2. Limpieza de datos
2.1 Manejo de valores nulos
Se hicieron las correspondientes modificaciones, unicamente en el dataset telecom_new, ya que el dataset clients no presenta nulos.

a. Columna Internal: Es una columna booleana/categórica: True = interna, False = externa. La mayoría de los registros contaban con valor, y los nulos eran muy pocos (0.2%). Así que, rellenar los nulos con False tuvo sentido porque, estos representaban casos donde probablemente la llamada no era interna, lo que podría permitir errores en análisis categóricos o al graficar.

b. Columna operator_id: Es una columna numérica que identifica a un operador. Los nulos por su parte, representan llamadas sin operador, lo cual es información valiosa por sí misma. Si los rellenáramos con un número arbitrario, perderíamos esa distinción y el análisis de quién maneja llamadas sería incorrecto. Por eso se dejó como Int64 nullable: permitiendo nulos y análisis fácil sin inventar datos.

2.2 Corrección de tipos de datos
Modificaciones realizadas:

a. En dataset clients

La columna date_start : Pasó de STR a pd.to_datetime64 (por ser fecha requiere este tipo de formato)
La columna tariff_plan pasó de STR a categórico ya que maneja A,B,C (lo que optimiza memoria y permite análisis más limpio).
b. En el dataset telecom_new

La columna date se madificó de STR a pd.to_datetime64 (por ser fecha requiere este tipo de formato)
La columna internal pasó de tipo bool a categórico para facilidad de análisis ya que es binaria (maneja True y False)
C. Verificación de los cambios: Se hizo a traves de df.info() en ambos datasets quedando validados.

2.3 Verificación y Eliminación de duplicados
dataset clients: No presentó filas duplicadas (0 cero)

dataset telecom_new: Presentó filas duplicadas (4900)

Acción realizada: Una vez verificados los duplicados con el método df.duplicated(), se procedió a eliminarlos con el método df.drop_duplicates() comprobando finalmente dicha eliminación con el método df.duplicated().sum(), que arrojó (0 cero).

Paso 3. Integración de datos (MERGE)
a. Cómo se hizo?: Se realizó la integración de los datasets mediante un left join por user_id. Posteriormente, se validó la consistencia del merge verificando que:

b. Qué paso después de realizado el merge?: el número de registros se mantuvo constante, no existían duplicados en la clave de unión, y no se generaron valores nulos en las variables incorporadas.

c. Por qué left join?: Este se usó pensando en preservar todas las observaciones del dataset de llamadas, que constituye la unidad principal de análisis, evitando la pérdida de información en caso de que existieran usuarios sin correspondencia en la tabla de clientes.

d. Qué se logró con el merge?: El merge permitió integrar la información de clientes al nivel de eventos de llamadas, enriqueciendo cada registro con variables de negocio y replicando la información del cliente en todas sus interacciones.

Paso 4. Análisis exploratorio de datos (EDA)
4.1 Distribuciones de variables clave
Teniendo en cuenta que, el objetivo principal era identificar operadores ineficaces, se analizaron la siguientes variables:

a. 'calls_count', 'call_duration', 'total_call_duration', mediante un resumen descriptivo con el método .describe() (no sin antes convertir los segundos a minutos para facilidad de interpretación), producto del cual se generó inicialmente, el siguiente análisis:

Las variables presentaban una distribución altamente sesgada, con predominio de registros con pocas llamadas y de corta duración, junto con outliers extremos que inflan las medias. Además, un porcentaje notable de llamadas tiene duración cero, lo que indica posibles llamadas fallidas, colgadas por el operador o problemas operativos.

Análisis individual por variable:

1-Primera variable a analizar: call_duration_min:

Analizando inicialmente la totalidad de usuarios, se encontró que la mayoría de las llamadas eran cortas (mediana: 1 minuto), y una proporción significativa incluso no tenía duración (posibles llamadas fallidas o no completadas).

La media (14 minutos) estaba inflada por algunos valores extremadamente largos, lo que evidenció outliers importantes como el de la llamada de máxima duración que fue de 2407 minutos (40 horas), algo evidentemente anormal que podría deberse a problemas tales como lineas abiertas, error del sistema ó datos mal capturados, entre otros. En este sentido, datos como este pasaron a ser atipicos - no representativos.

Ahora bien, al continuar analizando la totalidad de usuarios, a través de un boxplot e histogramas se reconfirmó la existencia de asimetría de la distribución y valores extremos que dieron a los outliers como casos aislados.

Ahora los Típicos de call_duration_min

En este sentido, se procedió entonces a separar tipicos y outliers haciendo uso de la regla IQR, buscando tener un criterio estadístico objetivo. De acuerdo a esto, los típicos son los que caen dentro del rango definido por Q1 -1.5.IQR y Q3 + 1.5·IQR, mientras que los outliers son los valores que quedan por fuera de dicho intervalo.

Finalmente, el cálculo IQR produjo el siguiente resultado para la variable call_duration_min :

Límite inferior: -14.25 Límite superior: 23.75 Cantidad de típicos: 42136 Cantidad de outliers: 6866

Límite inferior: –14.25 Como la duración de una llamada no puede ser negativa, este valor se interpreta en la práctica como 0 minutos. Cabe aclarar que, el cálculo estadístico da un número negativo porque aplica la fórmula sin considerar la lógica del negocio, pero en telecomunicaciones no existen llamadas con duración negativa.

Límite superior: 23.75 Según la regla del IQR, cualquier llamada que dure más de ~24 minutos se considera un outlier estadístico. No significa que sea un error, sino que es poco frecuente comparado con el comportamiento general.

Cantidad de típicos: 42,136 Son las llamadas cuya duración está entre 0 y 23.75 minutos. Representan la gran mayoría de los registros y reflejan el comportamiento “normal”.

Cantidad de outliers: 6,866 Son las llamadas que duran más de 23.75 minutos. Estas son las excepciones, y aunque estadísticamente se marcan como outliers, pueden ser llamadas reales y válidas, solo que poco comunes.

Finalmente es importante mencionar que, se observó que tanto la media como la mediana, necesariamente presentaron reducción entre los datos completos y los típicos. Por ejemplo en esta variable (call_count), la mediana pasó de 4.0 a 3.0 y la media de 16.5 a 5.7, esta última presentando una reducción notable de 10.8 y así en las demás variables analizadas.

Distribución de llamdas por operador

Importante mencionar que cerca del 18% (7.402)de las llamadas registradas en esta variable en el grupo de datos típicos, no tuvieron un operador asignado.

2-Segunda variable a analizar: calls_count:

En el análisis de total de los datos de la variable, se evidenció que la mayoría de los registros tenía pocas llamadas, con una mediana de 4 llamadas, mientras que la media era de 16.46, debido a casos extremos con miles de llamadas (max 4817), que indicaban una distribución altamente sesgada a la derecha, de tal manera que, unos pocos registros con volúmenes extremadamente altos inflaban la media.

Se realizó visualización complementaria con histograma y boxplot que confirmó una desviación estándar de 63.6, evidenciando lo anteriormente mencionado.

Ahora vamos con los típicos de call_count

Para separar típicos de outliers, se procedió a hacer uso de la regla IQR, obteniendo los siguientes resultados en call_count:

tipicos = 42955 outliers = 6047 lim_inf = 0 # ajustado desde -15.50 lim_sup = 28.50

lo que significaba que:

Límite inferior: –15.50 Como no pudía haber un número negativo de llamadas, este valor se ajustó en la práctica a 0 llamadas. El cálculo estadístico lo da porque aplica la fórmula sin considerar la lógica del negocio.

Límite superior: 28.50 Según la regla del IQR, cualquier registro con más de ~29 llamadas se considera un outlier estadístico. No significa que sea un error, sino que es poco frecuente comparado con el comportamiento general.

Cantidad de típicos: 42,955 La gran mayoría de los registros caen dentro del rango normal (0–28.5 llamadas). Estos representan el patrón de uso más común.

Cantidad de outliers: 6,047 Son los casos con un número de llamadas superior al límite. Aunque estadísticamente se marcan como outliers, pudían ser clientes con un uso intensivo del servicio.

3- Tercera variable a analizar: total_call_duration:(incluye los tiempos de espera) comportamiento similar a call_duration.

En esta variable, se pudo evidenciar que la mayoría de registros tenían duración total baja, (menos de 1 minuto), con mediana de 3,5 minutos, pero que a la vez existían registros extremos que elevaban la media a 19,3 minutos, desviando la percepción del comportamiento típico, muy seguramente aumentada por el "wait_time".

visualización complementaria: Adicional a lo anterior, si hizo uso de una descripción estadística de la variable (.describe()), junto con su respectivo histograma y boxplot, que permitieron evidenciar características similares a lo mencionado anteriormente, pero con las siguientes particularidades:

Distribución muy sesgada: Todo lo anterior confirmó que, la mayoría de los registros contenía un total de llamadas relativamente cortas (menos de 15 min), mientras que unos pocos presentaban sesiones extremadamente largas.

Outliers importantes: Valores como 2769 min (~46 h) corresponden a casos extremos o registros acumulados, pero son pocos.

La media (19.3) se vió inflada por estos outliers, mientras que la mediana (3.5) representó mejor el comportamiento típico.

Registros con duración 0: Indicaban llamadas fallidas o no completadas.

ahora los típicos vs outliers de la variable total_call_duration_min

Los resultados al aplicar IQR fueron los siguientes:

Límite inferior: -20.61 Límite superior: 36.39 Cantidad de típicos: 43070 Cantidad de outliers: 5932

Límite inferior: –20.61 Cabe aclarar que, como no puede haber una duración negativa de llamadas, este valor se ajustó en la práctica a 0 minutos. (El cálculo estadístico lo da porque aplica la fórmula sin considerar la lógica del negocio).

Límite superior: 36.39 Según la regla del IQR, cualquier registro con más de ~36 minutos de duración total se considera un outlier estadístico. No significa que sea un error, sino que es poco frecuente comparado con el comportamiento general.

Cantidad de típicos: 43,070 La gran mayoría de los registros se ubicaron dentro del rango normal (0–36.39 minutos). Estos representaban el patrón de uso más común.

Cantidad de outliers: 5,932 Son los casos con una duración total superior al límite. Aunque estadísticamente se marcan como outliers, pueden ser clientes con un uso intensivo del servicio.

Análisis de los tiempos de espera como parte importante de la variable duración total de llamada

Dicho tiempo de espera se obtuvo restando la duración de llamada al tiempo total, lo que resultados importantes ya que algunas llamadas con duración 0 min, tuvieron tiempos de espera superiores a los 700 minutos.

Otro factor importante a tener en cuenta es que, muchas de las llamdas no contaron con operador asignado por razones desconocidas, aún así dentro del grupo de los típicos de esta variable, el promedio de tiempo de espera para las llamadas sin operador fue de 2.84 min y con operador de 3.69 minutos, lo cual en ambos casos es supremamente alto para una adecuada atención telefónica. (Si incluyeramos outliers el promedio de espera del total de las llamadas con operador sería de 5.20 minutos)

Resumen final de los típicos y outliers de las 3 variables Partiendo de la base de que en principio cada variable contiene 49.000 registros, al realiza una unión de conjuntoslas tres en total suman 147.000 entre típicos y outliers.

Una vez aplicada la regla IQR, 128.161 (87,1%) de ellos pasaron a ser típicos y 18.845(12.8%) outliers, los primeros reflejando mayor concentración y compactación.

No obstantes, al realizar una intersección entre los conjuntos de las 3 variables anteriormente analizadas, las cifras necesariamente se reducen, es decir que los típicos pasan a ser 39.289(85.1%) y los outliers 6.866(14.9%).

En este sentido, los típicos representan el comportamiento general de la base de clientes, a la vez siendo útiles para entrenar potenciales modelos predictivos y entender patrones de uso habituales.

Por otra parte los outliers, aunque son minoría (14.9% del total), resultan ser estratégicamente importantes, ya que también podrían reflejar clientes intensivos, posibles fraudes o segmentos especiales con requerimientos de atención diferenciada.

Aquí en el análisis de estos 6.866 outliers surge algo muy claro y diciente y es lo siguiente: El 99.21% de estos, correspondió a llamadas con operador asignado, de las cuales el 72% fueron salientes.

Podemos decir entonces, que el problema de productividad en los outliers, está relacionado con llamadas salientes con operador asignado, comportamiento muy semejante en los típicos también.

Así entonces, antes de pasar a analizar los diferentes tipos de llamada y teniendo un panorama más claro de la situación de que se viene presentando con llamadas salientes realizadas por operadores, y la respectiva distribución porcentual de típicos y outliers en las 3 variables, se procedió a definir unos umbrales, que permitan clasificar a los operarios en eficientes e ineficientes.

Definición de umbrales y construcción del score de eficiencia/productividad

Se establecieron umbrales estadísticos a partir de los cuartiles Q1, Q2 y Q3 de las principales variables de desempeño. Estos umbrales, permitieron transformar medidas dispersas (número de llamadas, duración promedio, tiempo total y porcentaje de llamadas perdidas) en parámetros comparables y consistentes.

Cabe mencionar que, los valores de referencia utilizados para evaluar la eficiencia de los operadores (calls_muy_bajo, calls_bajo, duration_alta, etc.), se definieron a partir de los datos “típicos” del equipo, es decir, excluyendo outliers extremos para no distorsionar el análisis.

muy importante Los umbrales no son arbitrarios: se derivaron directamente del comportamiento del equipo en condiciones normales, usando percentiles para identificar valores bajos, altos o extremo, lo que aseguro que la evaluación de eficiencia, reflejara la realidad operativa y permitiera priorizar a los operadores que requieren atención.

Explicación de los umbrales de eficiencia

Cómo se calcularon dichos umbrales?:

Cálculo de percentiles (quantiles): Se usaron los percentiles 25, 50 y 75 de las métricas clave para definir los límites de alerta y riesgo:
Calls: 25% y 50% → identifica operadores con pocas llamadas.
Duración de llamada y total: 50% y 75% → identifica llamadas muy largas o acumulación excesiva de tiempo.
Missed calls: promedio general del equipo → identifica el nivel normal de llamadas no atendidas.
Creación de los umbrales: Cada métrica recibió valores de referencia basados en estos percentiles:

Ejemplo: calls_muy_bajo = 1 → indica que 25% de los operadores hicieron 1 o menos llamadas.
duration_muy_alta = 9.50 → indica que 25% de las llamadas más largas superan 9.5 minutos.
Uso en el scoring

Asignación de puntaje por indicador: Cada operador recibe un puntaje de ineficiencia (0, 1 o 2) por cada indicador clave (por ejemplo, cantidad de llamadas, duración promedio, duración total, llamadas perdidas).

0 → desempeño dentro del rango esperado (eficiente). 1 → desempeño ligeramente fuera del rango esperado (alerta leve). 2 → desempeño fuera del rango esperado (riesgo alto).

Cálculo del ineff_score

Se suman los puntajes de todos los indicadores para obtener un score total por operador. Este valor refleja la ineficiencia relativa del operador considerando todos los indicadores.

Es importante aclarar que el ineff_score, a la vez se conviertió en materia prima para lo que sería un operador summary que permitiría agrupar los registros e indicadores por cada operador(ej llamadas, perdidas, duración, total, etc).

En este sentido, dicho operador se contruyó a partir de un .groupby, agrupando las filas del DataFrame por cada valor único de operator_id. Ejemplo: si había tres operadores (A, B, C), se creaban 3 grupos de filas correspondientes a cada operador y el .agg calculaba varias estadísticas sobre cada grupo, creando a la vez las siguientes columnas:

Nueva columna Qué hace ineff_score_mean Promedio (mean) de ineff_score por operador ineff_score_median Mediana (median) de ineff_score por operador missed_call_rate Promedio de is_missed_call (si es 1=perdida, 0=contestada, esto da la tasa de llamadas perdidas)

total_calls Cuenta cuántas filas hay por operador (count de operator_id) → total de llamadas total_duration Suma de total_call_duration_min → total de minutos de llamadas por operador

Finalmente, reset_index() después de groupby, convertía nuevamente en una columna normal, dejando un DataFrame limpio.

Resumen por operador El ineff_score se resume por operador mediante la media (ineff_score_mean) para tener un indicador consolidado de desempeño, facilitando el análisis comparativo y detección de areas críticas de mejora.

En ese sentido, se definió el siguiente semáforo:

Clasificación con semáforo El semáforo se define según los percentiles del ineff_score_mean de todos los operadores: 🔴 Crítico → operadores en el top 25% de ineficiencia (> Q75). 🟠 Riesgo medio → operadores entre la mediana y Q75. 🟡 Alerta leve → operadores ligeramente por debajo de la mediana (opcional según definición). 🟢 Excelente → operadores más eficientes (≤ Q50).

Objetivo Esta metodología asegura que la clasificación refleje la distribución real de desempeño de los operadores, destacando a los más ineficientes (acorde al objetivo del proyecto) de forma objetiva y alineada con los datos.

Así entonces, a partir de estos umbrales, fue construido el score de calificación de eficiencia y productividad integrado en un sólo valor final interpretable.

Resumen: Haciendo un recuento del ejercicio hasta este punto, podemos decir que, han sido limpiados los datos, organizados, integrados y clasificados, a la vez que identificados tipicos - outliers y definidos unos umbrales junto con un score de calificación de medición de la gestión operativa por agente.

Pero antes de hacer uso del score y proceder a aplicarlo en el análisis por operador, serán analizadas las llamdas por su tipo.

4.2 Análisis por tipo de llamada
El objetivo en esta etapa es medir tasa de llamadas perdidas y duración promedio por tipo de llamada. Así mismo, detectar si hay patrones de ineficiencia según dirección o llamadas internas/externas.

a. Tasa de llamadas perdidas por dirección de llamada(in / out): Se calcúló, con el método .groupby(), después de haber filtrado los datos típicos de las variables (call_coun, call_duration, total_duration).

Por su parte, los resultados mostraron que, Las llamadas salientes tienen una tasa de pérdidas más alta (52.56%) que las entrantes (41.46% ).

Esto confirma lo mencionado anteriormente respecto a que, los operadores son menos eficientes gestionando llamadas salientes, o que hay problemas con el sistema en ese tipo de llamadas.

Es un buen indicador temprano para priorizar el análisis de operadores que tengan más llamadas salientes perdidas.

Casi un 42% de las llamadas que deberían ser atendidas no lo fueron
Casi el 53% se pierden, más que las entrantes. Esto indica que los operadores tienen más dificultades en gestionar llamadas salientes, lo cual podría deberse a llamadas complejas, seguimiento de clientes o coordinación interna.
Finalmente, es importante destacar que, sólo estamos usando el grupo de datos previamente parametrizado como típicos, por tanto los valores no están distorsionados por outliers extremos, reflejando el comportamiento “normal” del equipo.

b. Duración mediana por dirección de llamada: Al igual que la anterior tasa, se calculó con .groupby(), pero con la diferencia de que en vez de la media, se usó la mediana ya que es mucho más cercana a las mayores frecuencias, obteniendo los siguientes resultados: Dirección Duración promedio (min) in 0.57 out 0.00

Qué podemos interpretar con este resultado?

Que la mayoría de las llamadas salientes registradas tienen duración nula (probablemente se cortan de inmediato, no se completan o se marcan como perdidas).

Esto puede reflejar un patrón de ineficiencia en las llamadas salientes, ya que no generan tiempo de conversación real.

Es un hallazgo clave para el análisis por tipo de llamada: no solo importa el volumen, sino también la calidad y efectividad de cada dirección.

Este resultado evidencia que las llamadas salientes, en su mayoría, no generan tiempo efectivo de conversación, lo cual constituye un indicador de ineficiencia en la gestión de este tipo de interacción.

c. *Tasa de llamadas perdidas por llamadas internas / externas *

Llamadas perdidas – Consolidado y por tipo

Consolidado general
Sin operador
Total llamadas: 6,875
Perdidas: 6,816
% de pérdidas: 99.14%
Con operador
Total llamadas: 32,414
Perdidas: 12,008
% de pérdidas: 37.05% Interpretación:
Sin operador, prácticamente todas las llamadas se pierden (≈99%).
Con operador, la tasa baja a 37%, pero sigue siendo elevada: más de 1 de cada 3 llamadas se pierde.
Esto confirma que el operador humano es indispensable, aunque persisten problemas de eficiencia.
Por tipo de llamada
Sin operador
Externas: 99.30% perdidas
Internas: 95.94% perdidas
Con operador
Externas: 35.40% perdidas
Internas: 45.49% perdidas
Interpretación:

Sin operador, tanto externas como internas muestran tasas de pérdida extremadamente altas, lo que evidencia que el sistema automático no es funcional.
Con operador, las externas tienen mejor desempeño (35.40%) que las internas (45.49%).
Las llamadas internas son más problemáticas, reflejando posibles fallas en procesos internos o saturación de recursos.
Conclusiones integradas:

El operador humano reduce significativamente las pérdidas, pero aún se mantiene un nivel alto de ineficiencia.
Las llamadas internas requieren atención prioritaria, ya que presentan la mayor tasa de pérdida incluso con operador.
El sistema sin operador no es viable, dado que prácticamente todas las llamadas se pierden.
Recomendaciones:
Optimizar la gestión interna para reducir la tasa de pérdida en llamadas internas.
Revisar la capacidad de atención y distribución de carga entre operadores.
Evaluar y rediseñar los procesos automáticos, ya que su desempeño actual es insuficiente.
d. Duración mediana por llamadas internas / externas

Siguiendo con la misma metodología de los anteriores cálculos, se realizó un .groupby(), entre los tipicos entre las variables internal y call-duration_min, con la particularidad de que se trabajó con la mediana en vez de la media, obteniendo los siguientes resultados:

Interna/Externa Duración promedio (min) False (externa) 0.25 True (interna) 0.10

Qué dicen estas cifras?

Externas: la mediana de duración es 0.27 minutos → la mitad de las llamadas externas duran menos de 16 segundos.
Internas: la mediana es 0.10 minutos → la mitad de las llamadas internas duran menos de 6 segundos, lo cual no resulta normal tratándose de atención a usuario.
4.3 Primer insight de ineficiencia
A partir del análisis de los registros típicos, y los resultado obtenidos en el análisis por tipo de llamada, fueron identificados patrones de ineficiencia operacional en la gestión de llamadas.

Ineficiencia estructural: Los patrones observados muestran que la operación presenta fallas tanto en volumen (altas tasas de pérdida) como en calidad (duraciones nulas en llamadas salientes).

Dependencia del operador humano: Sin operador, la atención es prácticamente inexistente; con operador, mejora pero sigue siendo insuficiente.

Mayor problema en llamadas internas y salientes: Internas reflejan fallas de coordinación interna; salientes evidencian baja efectividad en procesos de contacto externo.

Impacto en negocio:

Pérdida de oportunidades de contacto con clientes.

Baja efectividad en seguimiento y gestión comercial.

Disminución de la productividad del equipo

las llamadas salientes representan un punto crítico de ineficiencia dentro de la operación, constituyéndose como una prioridad para análisis posteriores orientados a identificar causas raíz y definir acciones de mejora.

Adicionalmente, el análisis de duración mostró un hallazgo aún más crítico: la mediana de duración de las llamadas salientes es igual a 0 minutos, lo que implica que al menos el 50% de estas llamadas no logra establecer un tiempo efectivo de conversación. Este comportamiento sugiere que una proporción considerable de intentos de contacto:

No es atendida por el destinatario Se interrumpe de manera inmediata O no se completa correctamente desde el origen

Este resultado, en conjunto con la alta tasa de pérdida, confirma que las llamadas salientes no solo fallan en volumen, sino también en calidad, al no generar interacción real con el usuario final.

Desde una perspectiva operativa, este patrón configura una ineficiencia estructural, ya que implica la asignación de recursos (tiempo de operadores, uso de sistema, intentos de contacto) a actividades que no producen valor efectivo. En términos de negocio, esto puede traducirse en:

Pérdida de oportunidades de contacto con clientes Baja efectividad en procesos de seguimiento o gestión comercial Disminución de la productividad del equipo

Cabe resaltar que este comportamiento no parecía ser un fenómeno aislado, sino un patrón consistente dentro del comportamiento “normal” del sistema, dado que el análisis se realizó sobre datos previamente filtrados como típicos.

En consecuencia, este insight sugiere la existencia de posibles fallas en el proceso de llamadas salientes, las cuales podrían estar asociadas a factores como:

Calidad u organización de las bases de datos de contacto Estrategias inadecuadas de timing en la ejecución de llamadas

Dificultades en la gestión operativa por parte de los operadores Limitaciones o fallos en el sistema de marcación

Este análisis permitirá identificar operadores con comportamientos ineficientes, diferenciando entre aquellos que fallan más en llamadas externas (impacto sobre clientes) y los que presentan problemas internos (impacto sobre el flujo del equipo).

En consecuencia, podrán ser planificadas acciones correctivas específicas: entrenamiento en gestión de llamadas externas, optimización de procesos internos y seguimiento de operadores con scores altos de ineficiencia.

4.4 Análisis por operador
Objetivo: medir desempeño de los 1081 operadores que conforman la compañía CallmeMaybe, usando métricas representativas (sin outliers) y detectar patrones de ineficiencia.

a. Análisis de productividad y eficiencia general por operador

Basado en los parámetros definidos anteriormente a partir de IQR y umbrales, se procederá a medir los operadores en sus diferentes indicadores.

La estructura de medición del indicador a utilizar está conformada por las siguientes variables:

calls_count → Cantidad de llamadas realizadas por operador. call_duration_min → Duración promedio por llamada. total_call_duration_min → Duración total acumulada de llamadas. is_missed_call → Indica si la llamada fue perdida. internal → Diferencia entre llamadas internas y externas. direction → Diferencia entre llamadas entrantes y salientes

Cálculo del semáforo Cada operador recibió un score de ineficiencia que combina:

Baja productividad (pocas llamadas) Llamadas largas (posible ineficiencia) Llamadas perdidas (impacto directo en eficiencia)

La suma de condiciones activas da un número entre 0–3, que luego se traduce en un semaforo visual: 🟢 Excelente 🟡 Alerta leve 🟠 Riesgo medio 🔴 Crítico

Así entonces, una vez implementado el score de calificación, los resultados obtenidos a nivel grupal fueron los siguientes:

resultado de la medición general:

Nivel semáforo Cantidad de operadores Porcentaje 🔴 Crítico 270 25.0% 🟠 Riesgo medio 258 23.9% 🟡 Alerta leve 282 26.1% 🟢 Excelente 271 25.1%

Interpretación:

Operadores críticos (🔴, 25%) Un cuarto de los operadores presentaron los niveles más altos de ineficiencia. Son los principales candidatos a revisión, capacitación o seguimiento inmediato.

Riesgo medio (🟠, 23.9%) Cerca de 1 de cada 5 operadores tuvo desempeño por debajo de lo esperado.

Alerta leve (🟡, 26.1%) susceptibles de mejora con procesos de feedback.

Excelente (🟢, 25.1%) Estos operadores pueden servir de referencia o modelo para el resto del equipo.

En el ranking de calificación, pudo evidenciarse que los operarios que encabezan el top de los ineficientes, obtuvieron puntajes entre 4.24 y 6 lo que los ubica en nivel crítico del score. Por el contrario al final de la lista encontramos a los ubicados en nivel excelente tal como se mencionó al inicio.

Así entonces, en este punto del ejercicio, se logra su objetivo: Identificar a los operadores ineficientes de la compañía de telecomunicaciones. Más sinembargo, se realizan a continuación análisis complementarios.

b. Tasa de llamadas perdidas por operador

La columna is_missed_call está en escala de 0 a 1, donde 1 significa que todas las llamadas de ese operador fueron perdidas y 0 que ninguna fue perdida.

Los operadores listados tienen un 100 % de llamadas perdidas (valor 1.0). Esto indica que estos operadores no atendieron ninguna llamada efectiva, un factor crítico que seguramente los ubica en la categoría 🔴 Crítico en el semáforo de ineficiencia.

Conclusión:

El análisis reveló que alrededor del 50% de los operadores presentan niveles de ineficiencia significativos (🔴 Crítico y 🟠 Riesgo medio), mientras que el 50% mantuvo un desempeño aceptable o excelente (🟡 Alerta leve y 🟢 Excelente). Esto indica que, aunque una parte del equipo funciona bien, es necesario enfocar esfuerzos en capacitar y supervisar a los operadores con mayor ineff_score para elevar la eficiencia global de la compañía.

Importante: Fueron identificados 508 de los 1081 operadores (47%) tanto en llamadas típicas como en outliers, lo que evidencia un patrón de comportamiento mixto.

4.5 Segmentación
Segmentación por semáforo de ineficiencia:

Los operadores ya están clasificados en cuatro niveles: 🔴 Crítico (25%) → Necesitan seguimiento y entrenamiento inmediato. 🟠 Riesgo medio (23.9%) → Supervisión y posibles mejoras preventivas. 🟡 Alerta leve (26.1%) → Desempeño aceptable con margen de optimización. 🟢 Excelente (25.1%) → Referencia para buenas prácticas.

Esta segmentación permite priorizar acciones según riesgo y enfocar recursos en los grupos más problemáticos.

Segmentación por características de llamadas:

Duración promedio: operadores con llamadas muy largas pueden generar ineficiencia; los que tienen llamadas muy cortas pueden estar priorizando volumen sobre calidad.

Tasa de llamadas perdidas: operadores con alta proporción de llamadas perdidas requieren atención específica, independientemente de la cantidad total de llamadas.

Tipo de llamada (interna vs externa) o dirección (in/out): permite entender si ciertos operadores tienen problemas más frecuentes en un tipo específico de llamada.

Beneficio de la segmentación

Permite acciones más personalizadas: capacitación, reestructuración de carga de trabajo o mentoría.

Facilita identificar patrones de ineficiencia: por ejemplo, si los críticos se concentran en llamadas externas o de cierta duración.

Optimiza la asignación de recursos y seguimiento, evitando intervenciones generales y costosas.

4.6 Identificación de patrones
A partir del análisis de los 1092 operadores de CallmeMaybe, se identificaron los siguientes patrones clave de desempeño:

Operadores críticos y riesgo medio
Aproximadamente 25% del equipo está en categoría 🔴 Crítico y 25% en 🟠 Riesgo medio.

Estos operadores presentan altos valores de ineff_score, ya sea por baja cantidad de llamadas, llamadas excesivamente largas o alto porcentaje de llamadas perdidas.

Son candidatos prioritarios para capacitación, seguimiento o intervención inmediata.

Eficiencia asociada a la duración de llamadas
Los operadores con mayor ineff_score tienden a acumular duraciones totales de llamadas más altas sin reflejar productividad proporcional (pocas llamadas).

El mapa de calor evidencia que la ineficiencia se concentra en ciertos rangos de duración de llamadas, mostrando que no solo la cantidad, sino la gestión del tiempo por llamada, impacta el desempeño.

Impacto de llamadas perdidas
Aunque muchos operadores críticos no muestran llamadas perdidas altas, existe un subgrupo donde tasa de llamadas perdidas y baja productividad coinciden, reforzando su calificación crítica.

Esto indica que la combinación de eficiencia operativa y seguimiento de llamadas es determinante para la evaluación.

Patrones de agrupamiento
La segmentación por semáforo y métricas permite identificar grupos homogéneos:

Operadores excelentes (🟢) con desempeño consistente, pocas llamadas perdidas y tiempos de llamada equilibrados.

Operadores con alerta leve (🟡) que mantienen productividad aceptable pero con margen de mejora.

Operadores críticos (🔴) con concentración de ineficiencia en varias métricas simultáneamente.

Operadores presentes tanto en tipicos como en outliers:
El problema operativo no está concentrado en un grupo reducido de operadores, sino que parece ser sistémico o dependiente del contexto de la llamada.

Conclusión del patrón:

El análisis revela que la ineficiencia no es aleatoria, sino que se concentra en operadores con baja productividad, llamadas largas y mala gestión de llamadas perdidas. Esto permite diseñar intervenciones específicas según el patrón de cada operador o grupo, maximizando la efectividad de la mejora operativa.

4.7 Cruces operador-cliente
El análisis de los cruces operador-cliente permitió identificar cómo se distribuyeron las interacciones de cada operador y si ciertos clientes estaban asociados a niveles de eficiencia distintos. Los hallazgos principales fueron:

Concentración de clientes por operador:

Algunos operadores manejan muy pocos clientes, pero con llamadas largas, lo que puede aumentar su ineff_score. Otros operadores atienden mayor cantidad de clientes con llamadas más cortas, mostrando eficiencia y mejor gestión del tiempo.

Clientes con alta incidencia de llamadas perdidas

Se identificaron clientes con frecuencia elevada de llamadas perdidas, especialmente en operadores críticos. Esto evidenció oportunidades de mejora en seguimiento y priorización de clientes específicos.

Patrones de interacción asociados al semáforo: Operadores con calificación 🔴 Crítico tienden a concentrar llamadas en pocos clientes con llamadas largas o pérdidas frecuentes, mientras que los operadores 🟢 Excelente distribuyen sus interacciones de manera más equilibrada.

Esta segmentación permitió detectar cuellos de botella o clientes que requieren atención especial.

Uso en la mejora operativa: Con base en estos cruces, la compañía podría diseñar planes de asignación de clientes, reentrenamiento de operadores y estrategias de priorización de llamadas, teniendo en cuenta que dichos cruces, facilitan la identificación de clientes estratégicos que podrían estar impactando la eficiencia general del equipo.

Conclusión del patrón: El análisis cruzado operador-cliente mostró que, la eficiencia de los operadores está ligada a la cantidad de clientes, duración de llamadas y llamadas perdidas por cliente. Esto permite implementar medidas focalizadas que optimicen tanto la productividad del operador como la satisfacción del cliente.

Visualización complementaria

Adicional al anterior análisis de cruce de llamadas por cliente, se construyó un gráfico de dispersión que relaciona el número de clientes por operador con la duración total de llamadas. El tamaño de los puntos representa la tasa de llamadas perdidas y el color corresponde al semáforo dinámico de eficiencia.

Esta visualización permitió identificar de manera inmediata a los operadores críticos, caracterizados por pocas llamadas, duraciones excesivas y altos niveles de pérdida, frente a aquellos eficientes, que atienden más clientes con llamadas más cortas y menor proporción de pérdidas

Paso 5. Análisis de negocio
5.1 Detectar factores que influyen en el comportamiento del cliente
Para llevar a cabo esta parte del análisis, fue creada una matriz de correlación entre factores y comportamiento del cliente, la cual permitió evidenciar que, la tasa de llamadas perdidas es el principal factor asociado a la ineficiencia operativa (r=0.52).

El detalle de la lectura fue el siguiente:

total_calls vs total_duration = 0.80 → Los operadores que tienen más llamadas suelen acumular mayor duración total. Es lógico: más llamadas = más minutos.

missed_call_rate vs ineff_score_mean = 0.52 → La tasa de llamadas perdidas está bastante correlacionada con el nivel de ineficiencia. Es decir, perder llamadas es un factor fuerte que explica la ineficiencia.

total_duration vs ineff_score_mean = 0.20 → Una correlación positiva pero débil: operadores con más duración total tienden a ser un poco más ineficientes, pero no es tan determinante.

total_calls vs ineff_score_mean = 0.09 → Casi nula: el número de llamadas atendidas no explica directamente la ineficiencia.

total_duration vs missed_call_rate = 0.13 → Relación leve: operadores con más duración total tienden a perder algo más de llamadas, pero no es fuerte.

5.2 Generar insights accionables
La duración total de llamadas mostró una relación positiva pero débil (r=0.20), mientras que el número de llamadas atendidas prácticamente no influyó en el nivel de ineficiencia (r=0.09).

Estos hallazgos sugieren que: la gestión de llamadas perdidas es el aspecto crítico a mejorar para impactar directamente en el comportamiento del cliente y la eficiencia del servicio.

5.3 Prueba las hipótesis estadísticas
Planteamiento de hipótesis

Si bien, el análisis descriptivo evidencia que las llamadas salientes (out) presentan una mayor tasa de llamadas perdidas (52.56%) en comparación con las llamadas entrantes (41.46%), lo que representa una diferencia de aproximadamente 11 puntos porcentuales, este resultado sugirió que las llamadas salientes constituyen un posible foco de ineficiencia dentro de la operación, ya que concentran una mayor proporción de intentos fallidos.

Para validar este hallazgo, se planteó una prueba de hipótesis sobre la diferencia de proporciones entre ambos tipos de llamadas. En este contexto:

La hipótesis nula establece que no existen diferencias en la tasa de llamadas perdidas entre llamadas entrantes y salientes. La hipótesis alternativa propone que las llamadas salientes presentan una tasa significativamente mayor.

H₀ (nula): La tasa de llamadas perdidas es igual en entrantes y salientes

H₁ (alternativa): Las llamadas salientes tienen mayor tasa de pérdida

¿Por qué esta prueba es ideal aquí?

Porque la variable (is_missed_call) es binaria (0/1)
Se está comparando proporciones entre dos grupos
Se cuenta con datos a nivel individual (cada llamada)
Es más apropiado que usar el promedio de ineff_score para este caso específico.

Dado el tamaño de la diferencia observada, y sujeto a confirmación mediante prueba estadística, se consideró que las llamadas salientes representan un punto crítico de análisis, el cual debería priorizarse en etapas posteriores orientadas a identificar causas raíz, tales como problemas en la gestión operativa, tiempos de respuesta, segmentación de clientes o estrategias de contacto.

Resusltado: Interpretación del resultado Z-stat = 21.433 Es un valor extremadamente alto p-value = 0.00000 En realidad es algo como < 0.00001 (muy cercano a cero)

¿Qué significa esto?

La probabilidad de que la diferencia observada (52.56% vs 41.46%) sea por azar es prácticamente cero.

✅ Por lo tanto:

Es rechazada la hipótesis nula (H₀)

Se confirma que:

Las llamadas salientes tienen una tasa de pérdida significativamente mayor que las entrantes

Z > 2 → ya es significativo Z ≈ 39 → evidencia abrumadora

Validación estadística

Se realizó una prueba de hipótesis para comparar la tasa de llamadas perdidas entre llamadas entrantes y salientes.

El resultado arrojó un estadístico Z = 21.433 con un p-value < 0.000, lo que indica que la diferencia observada es altamente significativa desde el punto de vista estadístico.

Este hallazgo valida con rigor estadístico que las llamadas salientes son un punto crítico de ineficiencia.
No es solo una observación descriptiva: el test confirma que la diferencia es real y consistente en la población analizada.
Se refuerza la conclusión de que los intentos de contacto iniciados por los operadores tienen menor efectividad y mayor probabilidad de fallar.
Conclusión: El análisis estadístico demuestra que las llamadas salientes presentan una tasa de pérdida significativamente mayor que las entrantes. Este resultado, junto con la mediana de duración en cero, confirma que las llamadas salientes no solo fallan en volumen, sino también en calidad, constituyendo una ineficiencia estructural que debe ser abordada con prioridad en la gestión operativa y comercial.

Las llamadas salientes son un punto crítico real y deben ser prioridad de mejora operativa

Paso 6. Conclusiones y recomendaciones -Fuentes
Conclusiones:

El análisis evidencia que la ineficiencia operativa en CallMeMaybe no es aleatoria, sino estructural, y está principalmente asociada a dos factores críticos: la alta tasa de llamadas perdidas y la baja efectividad de las llamadas salientes.

A partir del análisis por tipo de llamada, operador y validación estadística, se concluye que:

Las llamadas perdidas son el principal driver de ineficiencia Presentan la mayor relación con el desempeño operativo (r = 0.52). Más de 1 de cada 3 llamadas se pierde incluso con operador. Sin operador, la tasa de pérdida alcanza niveles críticos (~99%).
Esto confirma que reducir llamadas perdidas es la palanca más importante para mejorar la eficiencia global.

Las llamadas salientes representan el mayor problema operativo Tienen una tasa de pérdida significativamente mayor (52.56% vs 41.46% en entrantes). La mediana de duración es 0 minutos, lo que indica que al menos el 50% no genera interacción real. La diferencia fue validada estadísticamente (Z = 21.433, p < 0.001).
Esto demuestra que las llamadas salientes fallan tanto en volumen como en calidad, convirtiéndose en el principal cuello de botella del sistema.

Existe una fuerte dependencia del operador humano Sin operador, el sistema prácticamente no funciona. Con operador, mejora significativamente, pero aún con alta ineficiencia (37% pérdidas).
El operador es clave, pero la gestión actual no es suficiente para lograr eficiencia operativa.

Ineficiencia concentrada en el 50% del equipo 🔴 Crítico (25%) + 🟠 Riesgo medio (23.9%) = ~50% de operadores con problemas relevantes. La ineficiencia se concentra en operadores con: Alta tasa de llamadas perdidas Llamadas excesivamente largas o nulas Baja productividad
Esto permite focalizar acciones en un grupo específico con alto impacto potencial.

Problemas adicionales en llamadas internas Mayor tasa de pérdida que externas (con operador). Duraciones extremadamente bajas (mediana ~6 segundos).
Indican fallas en procesos internos y coordinación operativa.

Conclusión general

La operación presenta una ineficiencia estructural, especialmente en llamadas salientes y gestión de pérdidas.

Esto impacta directamente en:

Pérdida de oportunidades comerciales Baja efectividad de contacto Disminución de la productividad del equipo

Recomendaciones:

Optimización de llamadas salientes (PRIORIDAD #1)
Acción:

Validar calidad de bases de datos (números incorrectos o inactivos) Ajustar horarios de contacto (timing) Implementar validación previa de llamadas Evaluar sistema de marcación automática

Impacto esperado:

Reducción de llamadas fallidas Mayor tasa de contacto efectivo

Reducción de llamadas perdidas (palanca principal)
Acción:

Alertas en tiempo real para llamadas no atendidas SLA de respuesta por operador Automatización de reintentos inteligentes

Impacto esperado:

Mejora directa en eficiencia global Aumento en satisfacción del cliente

Intervención en operadores críticos
Acción:

Capacitación focalizada Coaching basado en operadores 🟢 excelentes Seguimiento individual con KPIs

Impacto esperado:

Reducción rápida de la ineficiencia Mejora en desempeño del equipo

Optimización de procesos internos (llamadas internas)
Acción:

Revisar flujos de comunicación interna Identificar cuellos de botella operativos Mejorar asignación de tareas

Impacto esperado:

Reducción de pérdidas internas Mayor fluidez operativa

Gestión eficiente del tiempo de llamadas
Acción:

Definir rangos óptimos de duración Detectar llamadas anormalmente cortas o largas Capacitación en manejo y cierre de llamadas

Impacto esperado:

Mejor uso del tiempo operativo Incremento de productividad

Sistema de monitoreo continuo (data-driven)
Acción: Implementar dashboard con:

Tasa de llamadas perdidas Duración de llamadas Volumen de llamadas (in/out) Desempeño por operador (semáforo)

Impacto esperado:

Detección temprana de problemas Mejora continua basada en datos

Fuentes:

1.Interquartile Range

https://www.investopedia.com/terms/i/interquartile-range.asp Explicación clara del IQR.2.

La fuente permitió aclarar dudas acerca del Método utilizado para la detección de valores atípicos (outliers) a partir de la regla de rango intercuartílico(IQR), la cual implica restar el primer cuartil del tercero usando la fórmula: IQR = Q3 − Q1

Esta medida estadística de dispersión, describe la amplitud del rango medio del 50 % central de un conjunto de datos. Se usa ampliamente para evaluar la variabilidad y detectar valores atípicos de manera robusta frente a valores extremos. Límite inferior: Q1 − 1,5 × IQR Límite superior: Q3 + 1,5 × IQR

Definición formal de Scoring Model Scoring Model https://www.saber.app/glossary/scoring-model
Define el scoring como un framework sistemático Explica asignación de puntos + variables + umbrales Habla de clasificación y toma de decisiones Incluye modelos rule-based (como el del proyecto Telecom)

Scoring Model Un modelo de puntuación (Scoring Model) es un sistema de decisión basado en reglas que asigna valores numéricos a variables o características para estimar la probabilidad o conveniencia de un resultado. Se utiliza ampliamente en análisis predictivo, evaluación de riesgos y clasificación automatizada de decisiones.

En este caso puntual, permitió entener mejor cómo asignar ponderaciones en pro de evaluar la gestión de los operadores de CallmeMaybe

Dataframes en Librerías Pandas Fuente: Material de estudio Sprint 3 del curso Data Analyst - TripleTen

En este material pude reforzar conocimiento y despejar dudas frente al uso de los diferentes métodos a emplear en el proyecto en pro de lograr el objetivo inicial propuesto.

Documentación Python https://docs.python.org/3/library/exceptions.html

Permitió interpretar y comprender mejor los errores presentados durante el proceso.

Entornos virtuales Fuente: Material de estudio Sprint 7 del curso Data Analyst - TripleTen
Una vez realizado el respectivo análisis de datos y logrado el objetivo del proyecto, pude despejar dudas acerca de cómo estructurar el repositorio local y publicar su contenido principal en la plataforma git hub.

Story Telling with Data https://luutruvn.com/wp-content/uploads/2025/03/Storytelling-with-Data_-A-Data-Visualization-Guide-for-Business-Professionals-PDFDrive-.pdf

Resolver dudas acerca de elaboración de dashboarad y adecuada organización de imágenes para presentación.
