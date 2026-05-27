# Caso 3 – SQL: Análisis de base de datos relacional sobre libros

## 📌 Ficha Ejecutiva

- **Problema de negocio:** Analizar una base de datos relacional de libros para generar insights sobre editoriales, autores, calificaciones y reseñas, con el fin de proponer mejoras en la plataforma.  
- **Dataset utilizado:** Base de datos relacional con información de libros, autores, editoriales, usuarios, calificaciones y reseñas.  
- **Herramientas:** SQL, SQLAlchemy, Python, Pandas, Seaborn, SciPy, Matplotlib.  
- **Resultado principal:** Se identificó una alta concentración editorial (Penguin Books y Vintage), baja participación en reseñas y fuerte dispersión en calificaciones según autor y contenido.  
- **Recomendación final:** Implementar estrategias de gamificación para incentivar reseñas, y sistemas de recomendación/segmentación para reducir sesgos y mejorar la diversidad editorial.

---

## 🔗 Recursos

<div class="project-links">
  <a href="https://github.com/albert777mendoza/final_project_da/tree/main/case3_sql" class="btn" target="_blank">📖 README</a>
  <a href="https://github.com/albert777mendoza/final_project_da/blob/main/case3_sql/notebooks/sql_analysis.ipynb" class="btn" target="_blank">📓 Notebook</a>
</div>

---

## 🎯 Objetivo

Analizar base de datos relacional sobre libros, editoriales, autores, calificaciones y reseñas de clientes, para generar una propuesta de valor enfocada en un nuevo producto.

---

## 🛠 Herramientas y Tecnologías

SQL | SQLAlchemy | Python | Pandas | Seaborn | SciPy | Matplotlib

---

## ⚙ Procesos desarrollados

- Exploración de la base de datos  
- Revisión de tablas y relaciones  
- Consultas SQL varias  
- Análisis de resultados  

---

## ❓ Preguntas Clave

- ¿Cómo puede la plataforma equilibrar la concentración editorial, promoviendo la diversidad y visibilidad de editoriales con menor presencia?  
- ¿Qué estrategias de motivación podrían incentivar a los usuarios más activos a generar más reseñas de calidad, además de calificar libros?  
- ¿De qué manera influye el reconocimiento de autores consolidados en la percepción de calidad y cómo se puede aprovechar esa influencia para dar visibilidad a autores menos conocidos?

---

## 📊 Metodología

1. **Exploración de la base de datos:** identificación de tablas, claves primarias y foráneas, revisión de relaciones entre autores, libros, editoriales, calificaciones y reseñas. Visualización de primeras filas con Python y Pandas.  
2. **Formulación de consultas SQL:** construcción de queries con funciones de agregación (`COUNT`, `AVG`), filtros (`WHERE`, `HAVING`) y uniones (`INNER JOIN`) para responder a los objetivos: número de libros publicados, reseñas, calificaciones promedio, editoriales más productivas y autores mejor valorados.  
3. **Análisis de resultados:** interpretación de hallazgos sobre participación de usuarios, dispersión en calificaciones, concentración editorial y la influencia de autores reconocidos. Identificación de patrones clave en la interacción y producción editorial.  
4. **Conclusiones y recomendaciones:** planteamiento de estrategias para incentivar reseñas mediante gamificación, equilibrar la concentración editorial con recomendaciones personalizadas y aprovechar la influencia de autores reconocidos para visibilizar nuevos talentos.

---

## 📸 Visualizaciones destacadas

**Distribución de reseñas por usuario:** la mayoría aporta entre 15 y 20 reseñas, mientras que pocos escriben muy pocas o muchas, evidenciando la necesidad de incentivos.  
![Distribución de reseñas por usuario](https://albert777mendoza.github.io/assets/img/resenas-usuario.png)

**Relación entre calificación promedio y número de reseñas:** muestra que títulos con buenas calificaciones (3.8–4.6) suelen tener pocas reseñas, sugiriendo que percepción de calidad y participación no siempre coinciden.  
![Relación calificación vs reseñas](https://albert777mendoza.github.io/assets/img/calificacion-resenas.png)

**Autores: Producción vs Calidad vs Popularidad:** combina número de libros publicados, calificación promedio y volumen de reseñas, evidenciando que autores reconocidos dominan tanto en productividad como en participación.  
![Autores: Producción vs Calidad vs Popularidad](https://albert777mendoza.github.io/assets/img/autores.png)

---

## 📝 Conclusiones y Recomendaciones

- Pocas editoriales como Penguin Books y Vintage dominan la producción de libros, evidenciando concentración de mercado.  
- Los usuarios más comprometidos (más de 50 libros calificados) escriben en promedio 24 reseñas, confirmando que la creación de contenido requiere motivación.  
- La experiencia de lectura depende del autor, contenido y expectativas del lector, las calificaciones presentan gran dispersión (algunos libros llegan a 5.0, otros apenas 1.5).  
- **Recomendaciones:**  
  - Incentivar la creación de reseñas mediante gamificación, reconocimientos o distintivos.  
  - Gestionar la variabilidad de percepción de calidad con filtros y segmentaciones (género, estilo, popularidad).  
  - Promover diversidad editorial y visibilizar autores menos conocidos, aprovechando la influencia de autores consolidados.

---

## 🚀 Cómo Ejecutar el Proyecto

```bash
git clone https://github.com/albert777mendoza/final_project_da.git
pip install -r requirements.txt
jupyter notebook

## 👤 Autor

Albert Mendoza
Data Analyst Jr. | Python | SQL | Tableau
