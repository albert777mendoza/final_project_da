# Caso 2 – A/B Testing

## Español
### Objetivo
Evaluar si el sistema de recomendaciones recientemente implementado en la tienda online generó mejoras significativas en la tasa de conversión, comparando un grupo de control (A) y un grupo de prueba (B).

### Hallazgos Clave
- El grupo B no logró superar al grupo A en la conversión total (A: 51,8% vs B: 50,6%).  
- La diferencia observada (-1,1%) no fue estadísticamente significativa (p = 0.3024).  
- B mostró ligera ventaja en la etapa **product_page → cart**, pero perdió en la etapa crítica **cart → purchase**.  
- El número de participantes fue menor al esperado y hubo desbalance entre grupos, limitando la confiabilidad del experimento.  

### Conclusión y Recomendación
El cambio probado en el grupo B **no aportó valor significativo**. Se recomienda **no implementar** el nuevo sistema de recomendaciones hasta realizar un nuevo experimento con mejor diseño que corrija los desbalances y sesgos detectados.  

👉 [Explorar notebook completo](notebooks/ab_testing.ipynb)  
👉 [Ver storytelling completo](storytelling.md)

---

## English
### Objective
Evaluate whether the newly implemented recommendation system improved conversion rates by comparing a control group (A) and a test group (B).

### Key Findings
- Group B did not outperform Group A in total conversion (A: 51.8% vs B: 50.6%).  
- The observed difference (-1.1%) was not statistically significant (p = 0.3024).  
- B showed a slight advantage in **product_page → cart**, but lost in the critical **cart → purchase** stage.  
- The number of participants was lower than expected and groups were unbalanced, limiting reliability.  

### Conclusion and Recommendation
The change tested in Group B **did not provide significant value**. It is recommended **not to implement** the new recommendation system until a new experiment is conducted with improved design to correct group imbalances and biases.  

👉 [Explore full notebook](notebooks/ab_testing.ipynb)  
👉 [View full storytelling](storytelling.md)
