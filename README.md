# ¿Qué determina el resultado de un partido de fútbol?  
**Piedra angular:** entender si la **táctica del equipo**, la **calidad media de los jugadores** o la **ventaja de localía** explican mejor por qué un equipo gana, empata o pierde.

---

## Resumen ejecutivo  
En este análisis unimos datos de partidos (goles, equipos, titulares) con métricas FIFA de tácticas y ratings de jugadores para construir un modelo interpretable de resultado (home/draw/away). Encontramos que:

- La **diferencia de rating medio** entre los 11 titulares locales y visitantes (`Δ rating`) es el predictor dominante.  
- Las **tácticas** (presión defensiva, creación de disparos, etc.) tienen un efecto residual, mucho menor en un modelo lineal.  
- El modelo base (regresión logística balanceada) alcanza ~ 49 % de accuracy vs. 33 % de un azar 1/3, quedando la clase “draw” como la más difícil de predecir.

---

## Metodología  

1. **Carga y limpieza**  
   - Conexión a la base SQLite y lectura de las tablas `Match`, `Team_Attributes` y `Player_Attributes`.  
   - Conversión de la columna `date` a formato fecha y descarte de entradas sin fecha.  
   - Selección de columnas clave: goles, IDs de equipos y de 22 titulares.

2. **Ingeniería de variables**  
   - **Táctica:** extraer 8 métricas tácticas de cada equipo (velocidad de juego, presión, agresión…) justo antes de cada partido.  
   - **Talento:** obtener el `overall_rating` más reciente de cada jugador y promediar los 11 titulares por lado.  
   - Construir `Δ rating = rating_local − rating_visitante`.

3. **Exploración visual (EDA)**  
   - Distribución de victorias locales, empates y victorias visitantes.  
   - Box-plot de `Δ rating` por resultado.  
   - Curva de probabilidad de victoria local vs. `Δ rating`.  
   - Heatmap de correlación entre las diferencias tácticas (home − away).

4. **Modelado**  
   - Dataset final con 16 métricas tácticas (home & away) + `Δ rating`.  
   - Split 80/20 estratificado y pipeline: imputación (mediana), escalado estándar y regresión logística multinomial con `class_weight="balanced"`.  
   - Evaluación con accuracy, F1 y matriz de confusión.

---

## Hallazgos clave  

- **Δ rating**: cada +1 σ de ventaja de rating medio multiplica las odds de victoria local por ~ e⁰·⁶ ≈ 1.8.  
- **Presión defensiva local** y **creación de disparos visitante** emergen como los siguientes drivers más fuertes (coeficientes ≈ ±0.02).  
- El **desequilibrio de clases** (menos empates) y la falta de variables in-game (posesión, tarjetas, rachas recientes) limitan la predicción de empates.

---

## Recomendaciones  

- **Scouting**: focalizar fichajes que suban el rating medio del once en ≥ 2–3 puntos para convertir empates en victorias (~+8 pp).  
- **Táctica**: enfatizar presión defensiva en casa y neutralizar la creación de disparos del rival.  
- **Próximos pasos**: incorporar métricas de forma reciente (rolling goals), posesión media, tarjetas y modelos no lineales (Gradient Boosting) para mejorar la predicción de empates.

---

*© 2025 Rodrigo – Prueba técnica Senior Data Analyst*  
