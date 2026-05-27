## Case 3 - SQL

### Objetivo:
Analizar base de datos relacional sobre libros, editoriales, autores y calificaciones de clientes y reseñas de libros, para generar una propuesta de valor enfocada en un nuevo producto.

Objetivos específicos:
1. Encontrar el número de libros publicados después del 1 de enero de 2000.
   
2. Encontrar el número de reseñas de usuarios y la calificación promedio para cada libro.

3. Identificar la editorial que ha publicado el mayor número de libros con más de 50 páginas (esto te ayudará a excluir folletos y publicaciones similares de tu análisis).
   
4. Identifica al autor que tiene la más alta calificación promedio del libro: (mirar solo los libros con al menos 50 calificaciones).

5. Encuentra el número promedio de reseñas de texto entre los usuarios que calificaron más de 50 libros.


Desarrollo: 

En el siguiente proyecto está dividido en cuatro pasos, iniciando con la exploración de datos, pasando luego a realizar las correspondientes consultas con las que se logra dar respuesta a cada uno de los 5 objetivos iniciales.

Metodología:

Consta inicialmente de una breve narrativa con enfoque técnico, analítico de lo que fué el paso a paso seguido en cada una de las consultas, que una vez ejecutadas generaron resultados conducentes a conclusiones y recomendaciones.


Inicio:

### Paso 1. Exploración de la base de datos

#### 1.1 Revisar tablas y relaciones
* Nombre de la B.D: **'data-analyst-final-project-db'**
* Fueron identificadas 9 tablas en la estructura de la base, en donde tabla contiene al rededor de 6 a 8 columnas.

* Identificar claves primarias y foráneas:

*Conceptos fundamentales:*
- PK (Primary Key): columna que identifica de forma única cada fila en su tabla.
- FK (Foreign Key): columna que apunta a la PK de otra tabla, creando la relación.
- Atributos: columnas que guardan información descriptiva, pero no son claves.

En **'data-analyst-final-project-db'**, las PK, FK y atributos, están conformados así:

AUTHORS (author_id PK) ──< BOOKS (book_id PK, author_id FK, publisher_id FK) >── PUBLISHERS (publisher_id PK)
BOOKS (book_id PK) ──< RATINGS (rating_id PK, book_id FK)
BOOKS (book_id PK) ──< REVIEWS (review_id PK, book_id FK)

Para visualizar las primeras filas:

Primero, se creó una lista llamada **tables** con los nombres de todas las tablas disponibles en la base de datos, como advertisment_costs, authors, books, entre otras, lo que permitió recorrerlas de forma automática sin tener que escribir una consulta para cada una manualmente.

Luego, se utilizó un bucle for que itera sobre cada nombre de tabla en la lista. En cada iteración, se construyó una consulta SQL usando f-string (f"SELECT * FROM {table} LIMIT 5"), la cual seleccionó todas las columnas de la tabla actual pero limitando el resultado a las primeras 5 filas, evitando sobrecarga de datos.

Después, la función pd.read_sql() de la biblioteca pandas ejecuta la consulta utilizando la conexión engine y guardando el resultado en un DataFrame llamado df.

Finalmente, se imprimió el nombre de la tabla y su contenido. El salto de línea (\n) antes del nombre ayuda a que la salida sea más legible, separando visualmente los resultados de cada tabla.


### Paso 2 - Consultas 

#### 2.1 Encontrar el número de libros publicados después del 1 de enero de 2000.

La consulta se hizo sobre la tabla books a través de un SELECT COUNT(*), FROM y WHERE, a la vez aplicando condición (>) sobre la columna publication_date, para finalmente extaer y guardar el resultado en la varibale num_books e imprimir en modo f-string.

Resultado:

819 libros, lo que permite analizar la producción editorial en el período reciente.


#### 2.2 Encontrar el número de reseñas de usuarios 

Esta consulta se realizó de manera muy similar a la anterior, a partir de la tabla reviews y la función COUNT(*) que devolvió como resultado el total de reseñas almacenadas sin importar qué libro o usuario fuera.


Resultado:

Fueron registradas un total de 819 reseñas por parte de los usuarios, lo cual en principio indicaría que, todos los libros fueron reseñados en plataforma, lo cual no seguro hasta el momento ya que  un mismo libro podría contar con varios registros.


#### 2.3 Encontrar la calificación promedio para cada libro.

Esta consulta se realizó de la siguiente manera: 

Seleccionando el título del libro con SELECT books.title y posteriormente, promediando las calificaciones (rating) asociadas a ese libro, mediante AVG(ratings.rating) AS avg_rating (alias para posibles consultas específicas, ej ORDER BY o HAVING)

AVG() por su parte, devuelve el promedio de los valores numéricos y FROM ratings INNER JOIN books ON ratings.book_id = books.book_id, une la tabla ratings con la tabla books usando la columna book_id como clave, lo que asegura que cada calificación esté vinculada al libro correcto.
  
Posteriormente está el GROUP BY books.title agrupa los resultados por título de libro, logrando que el promedio se calcule por cada libro en lugar de sobre toda la tabla.

Finalmente, ORDER BY avg_rating DESC, ordenó los resultados de mayor a menor promedio de calificación, mostrando primero los libros mejor valorados.

Resultado:

Algunos libros alcanzaron calificación de 5.0, entre ellos Evening class y otros como Junky y Harvesting the Heart, obtivieron promedios de 2 y 1.5 respectivamente.


#### 2.4 Identificar la editorial que ha publicado el mayor número de libros con más de 50 páginas (esto te ayudará a excluir folletos y publicaciones similares de tu análisis) 

Esta consulta se inició seleccionando el nombre de la editorial con 
SELECT publisher y a la vez contando cuántos libros (book_id) están asociados a ella con COUNT(book_id) AS num_books, el alias num_books fue el nombre que se le dió a esa columna de conteo.

Posteriormente, fueron uniidas las tablas publishers y books usando la clave publisher_id, con FROM publishers INNER JOIN books ON publishers.publisher_id = books.publisher_id, de tal modo que cada libro quedó vinculado con su editorial.

Seguidamente, fueron filtrados los libros, dejando solo aquellos de más de 50 páginas con WHERE num_pages y el condicional > 50, haciendo el conteo únicamente sobre ese grupo de libros.

Posterioremente, fueron agrupados los resultados por editorial con 
GROUP BY publisher, en donde cada fila correspondió a una editorial distinta, para finalmente ordenar de forma descendente, las editoriales según la cantidad de libros publicados (con más de 50 páginas) con  ORDER BY num_books DESC.

resultado:

El resultado mostró las 334 editoriales  más productivas de la  base de datos como fueron Penguin Books, Vintage, etc, así como las de menor presencia, que apenas tienen 1 libro registrado con más de 50 páginas como Turtleback.
En otras palabras, es un conteo de libros por editorial, filtrado por la condición de páginas, y ordenado para ver quién publica má


#### 2.5 Identifica al autor que tiene la más alta calificación promedio del libro: (mirar solo los libros con al menos 50 calificaciones).

En este caso, se seleccionó el nombre del autor y calculó el promedio de las calificaciones (rating) de sus libros con SELECT a.author, AVG(r.rating) AS avg_rating. 

Luego con INNER JOIN authors a, books b, ratings r, fueron unidas las tres tablas:
- authors con books (cada libro tiene un autor).
- books con ratings (cada libro tiene calificaciones)

Luego se filtaron los libros con al menos 50 calificaciones con WHERE b.book_id IN (...)

Dicho filtro de menos de 50 calificaciones, se logró con la siguiente subconsulta:
SELECT book_id
FROM ratings
GROUP BY book_id
HAVING COUNT(*) >= 50

de tal manera que, fueron agrupadas las calificaciones por libro y seleccionadas solo las mayores o iguales a 50.


Así entonces y con el fin de calcular el promedio de calificaciones de todos sus libros (que cumplen la condición), se usó GROUP BY a.author, con el que naturalmente se pudo agrupar los resultados por autor, para luego ordenar los autores de mayor a menor promedio con ORDER BY avg_rating DESC

Finalmente, LIMIT 1 devuelvió solo el autor con el promedio más alto.

Resultado:

El autor con el promedio más alto fue: J.K. Rowling/Mary GrandPré	con un Promedio de 4.28

#### 2.6 Encuentra el número promedio de reseñas de texto entre los usuarios que calificaron más de 50 libros.

Para esta tarea, se calculó el número promedio de reseñas realizadas por los usuarios más activos de la plataforma. En primer lugar, se identificaron aquellos usuarios que han calificado más de 50 libros, utilizando la tabla de calificaciones y aplicando la función de agregación COUNT(DISTINCT book_id) junto con la cláusula HAVING, lo que permite filtrar únicamente a los usuarios con un alto nivel de participación.

Posteriormente, para este grupo de usuarios, se contabilizó el número total de reseñas realizadas por cada uno a partir de la tabla de reseñas, empleando nuevamente la función COUNT(*) y agrupando por usuario (GROUP BY username).

Finalmente, se calculó el promedio de reseñas por usuario mediante la función de agregación AVG(review_count), lo que proporciona una medida general del nivel de actividad en términos de escritura de reseñas entre los usuarios más comprometidos.

Este enfoque permite entender mejor el comportamiento de los usuarios más activos, no solo en términos de calificaciones, sino también en su contribución mediante reseñas, lo cual resulta clave para evaluar el nivel de interacción y generación de contenido dentro de la plataforma.

Resultado:

en promedio, los usuarios que han calificado más de 50 libros han escrito aproximadamente 24.33 reseñas.


### Paso 3 Análisis de resultados

En conjunto, los datos muestran una plataforma donde:

la interacción básica (calificaciones) es alta, pero la creación de contenido (reseñas) es limitada.

la oferta editorial está concentrada en pocos actores dominantes,
y la percepción de calidad es heterogénea, con fuerte influencia de autores reconocidos.

El conjunto de datos analizado incluye 819 libros publicados desde el año 2000, lo que refleja una base suficientemente representativa de la producción editorial reciente y permite identificar patrones relevantes dentro de la plataforma.

1. Participación de usuarios: alta en calificaciones, moderada en reseñas

Se registraron 819 reseñas, una cifra equivalente al número de libros. Aunque esto podría sugerir una cobertura completa, la posible existencia de múltiples reseñas por libro indica que la participación no está distribuida de manera uniforme.

Además, los usuarios más activos (con más de 50 libros calificados) escriben en promedio 24.33 reseñas, lo que evidencia una brecha entre la interacción pasiva (calificar) y activa (reseñar). Esto sugiere que generar contenido escrito requiere un mayor nivel de compromiso.

2. Alta variabilidad en la percepción de calidad

Las calificaciones presentan una dispersión considerable, con libros que alcanzan puntuaciones máximas de 5.0, como Evening Class, y otros con valoraciones significativamente bajas, como Junky y Harvesting the Heart.

Esta variabilidad indica que la experiencia de los usuarios no es homogénea y que la calidad percibida depende en gran medida del contenido, autor o expectativas del lector.

3. Concentración en la producción editorial

Aunque se identificaron 334 editoriales, la producción se encuentra concentrada en un grupo reducido de actores dominantes, como Penguin Books y Vintage, mientras que muchas editoriales tienen una participación mínima, como Turtleback.

Esto refleja una estructura de mercado concentrada, donde pocas editoriales lideran la oferta de contenido.

4. Influencia de autores reconocidos en la valoración

El autor con mayor promedio de calificación es J. K. Rowling junto con Mary GrandPré (4.28), lo que sugiere que autores consolidados tienden a recibir mejores valoraciones.

Esto puede estar relacionado con factores como reconocimiento de marca, expectativas previas o fidelidad de los lectores.
   


### Paso 4. Conclusiones y Recomendaciones:

**Conclusiones:**

1. Pocas editoriales como Penguin Books y Vintage, dominan la producción de libros, ya que pese a haber identificado 334 de ellas, la mayoría denota presencia mínima, lo que evidencia una estructura de mercado concentrada. 

2. Los usuarios más comprometidos, con más de 50 libros calificados, escriben en promedio 24 reseñas, lo que confirma que la generación de contenido escrito requiere mayor esfuerzo y motivación. Este dato resalta la importancia de implementar estrategias que incentiven la participación activa, ya que la creación de contenido no solo aporta valor a la comunidad, sino que también fortalece el sentido de pertenencia y fidelidad hacia la plataforma.

3. La experiencia de lectura depende fuertemente del autor, el contenido y las expectativas individuales de los lectores, las calificaciones presentan una gran dispersión ya que algunos libros alcanzan puntuaciones máximas de 5.0, mientras otros reciben valoraciones significativamente bajas. 

4. El nivel de reconocimiento y popularidad del autor, influyen directamente en la valoración de los libros. Influencia de autores reconocidosAutores de renombre, como J. K. Rowling, obtienen mejores calificaciones promedio.


**Recomendaciones:**

1. Fomentar la creación de reseñas, mediante la implementación de incentivos como gamificación, reconocimientos o distintivos para motivar a los usuarios a escribir reseñas.

2. Destacar reseñas de calidad en la plataforma para dar visibilidad y reforzar la participación activa.

3. Equilibrar la concentración editorial, promoviendo la diversidad editorial mediante recomendaciones personalizadas que incluyan editoriales menos conocidas.

4. Crear secciones de “descubrimiento” que visibilicen nuevos actores y títulos.

5. Gestionar la variabilidad en la percepción de calidad, a tra vés de herramientas de filtrado y segmentación (por género, estilo, popularidad) que permitan a los usuarios, encontrar libros más afines a sus intereses y gustos.

6. Incorporar métricas adicionales (ej. popularidad, tendencia) que complementen la calificación promedio.

7. Capitalizar la influencia de autores reconocidos, apoyándose en autores de prestigio que conlleven a atraer lectores, recomendando simultaneamente obras similares de autores menos conocidos.


**Fuentes:**


1. Material de estudio Sprint 8 : Relaciones entre tablas - Resumen del capítulo
2. Material de estudio Sprint 8: Funciones avanzadas de SQL para analistas
3. Material de estudio Sprint 8: SQL como herramienta para trabajar con datos
4. SQLAlchemy Documentation
Explica cómo conectar Python con bases de datos usando create_engine() y ejecutar consultas desde código.
5. Python for Data Analysis – Wes McKinney
Referencia clave sobre el uso de pandas para manipulación, limpieza y análisis de datos estructurados.

Las anteriores fuentes contribuyeron a aclarar conceptos específicos, especialmente para la elaboración de las consultas.
