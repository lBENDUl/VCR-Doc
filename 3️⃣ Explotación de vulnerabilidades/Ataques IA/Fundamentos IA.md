# Fundamentos de IA y Machine Learning


## 1. IA, ML y Deep Learning — La jerarquía

La **Inteligencia Artificial (IA)** es el campo más amplio: engloba cualquier sistema que imite capacidades cognitivas humanas (razonamiento, percepción, lenguaje natural, visión por computador, robótica).

El **Machine Learning (ML)** es una subdisciplina de la IA centrada en que los sistemas aprendan de datos sin ser programados explícitamente. Se divide en tres paradigmas:

- **Aprendizaje supervisado** — aprende de datos etiquetados (clasificación, regresión).
- **Aprendizaje no supervisado** — encuentra patrones en datos sin etiquetas (clustering, reducción de dimensionalidad).
- **Aprendizaje por refuerzo** — aprende por prueba y error mediante recompensas/penalizaciones.

El **Deep Learning (DL)** es una subdisciplina del ML que usa redes neuronales con múltiples capas para extraer representaciones jerárquicas de datos complejos (imágenes, audio, texto). Sus características clave son el aprendizaje jerárquico de características, el entrenamiento end-to-end y la escalabilidad.

---

## 2. Repaso matemático esencial

No hace falta dominar todo esto, es referencia de consulta.

- **Álgebra lineal**: operaciones con matrices (multiplicación, transpuesta, inversa, determinante, traza), normas vectoriales (L1, L2, L∞).
- **Notación**: subíndice `x_t` para series temporales, superíndice `x^n` para potencias, `Σ` para sumatorios.
- **Logaritmos y exponenciales**: `log2` (teoría de la información), `ln` (cálculo/probabilidad), `e^x` (modelado de crecimiento).
- **Probabilidad y estadística**: probabilidad condicional `P(A|B)`, esperanza `E[X]`, varianza `Var(X)`, covarianza `Cov(X,Y)`, correlación `ρ`.
- **Teoría de conjuntos**: unión, intersección, complemento.

---

## 3. Algoritmos de Aprendizaje Supervisado

### Conceptos clave

| Concepto | Descripción |
|---|---|
| **Features** | Variables de entrada del modelo |
| **Labels** | Valores objetivo conocidos (respuestas correctas) |
| **Overfitting** | El modelo memoriza el training set, no generaliza |
| **Underfitting** | El modelo es demasiado simple para capturar patrones |
| **Cross-validation** | Evalúa la generalización dividiendo los datos en folds |
| **Regularización** | Penaliza la complejidad para evitar overfitting (L1/L2) |

### Regresión Lineal

Predice un valor continuo asumiendo una relación lineal: `y = mx + c`. El objetivo es minimizar el error cuadrático (OLS). La extensión múltiple usa varios predictores. Métricas: MSE, RMSE, R².

### Regresión Logística

Clasifica en categorías usando la **función sigmoide** para mapear cualquier valor a un rango [0,1]. La salida se interpreta como probabilidad. Usa **log-loss** como función de coste. Para múltiples clases se extiende con **softmax**.

### Árboles de Decisión

Estructura en árbol que divide los datos según el feature más informativo en cada nodo. Criterios de split: **entropía/Ganancia de información** o **Gini impurity**. Ventaja: muy interpretables. Desventaja: propensos a overfitting.

### Naive Bayes

Clasificador probabilístico basado en el **Teorema de Bayes**, asumiendo independencia entre features. `P(clase|features) ∝ P(features|clase) * P(clase)`. Rápido y eficiente, especialmente en clasificación de texto.

### Support Vector Machines (SVM)

Encuentra el **hiperplano de máximo margen** que separa las clases. Los puntos más cercanos al hiperplano son los **vectores soporte**. El **kernel trick** permite separar datos no lineales proyectándolos a mayor dimensionalidad (kernels: lineal, RBF, polinomial).

---

## 4. Algoritmos de Aprendizaje No Supervisado

### K-Means Clustering

Agrupa datos en `k` clusters minimizando la distancia intra-cluster. Proceso: inicializar centroides aleatoriamente → asignar cada punto al centroide más cercano → recalcular centroides → repetir hasta convergencia. El método del **codo** ayuda a elegir `k`.

### PCA (Análisis de Componentes Principales)

Reduce la dimensionalidad preservando la máxima varianza. Calcula los **eigenvectors** de la matriz de covarianza (componentes principales) y proyecta los datos sobre ellos. Útil para visualización y eliminación de ruido.

### Detección de Anomalías

Identifica puntos que se desvían significativamente del comportamiento normal. Tres tipos de anomalías:

- **Puntuales** — un único punto anómalo (ej. pico de tráfico de red).
- **Contextuales** — anómalo en un contexto específico (ej. 30°C en enero).
- **Colectivas** — un grupo de puntos que juntos son anómalos (ej. múltiples intentos de login coordinados).

Técnicas principales:

- **One-Class SVM** — aprende una frontera alrededor de los datos normales; lo que cae fuera es anomalía.
- **Isolation Forest** — aísla anomalías mediante particionado aleatorio. Las anomalías son más fáciles de aislar → caminos más cortos en los árboles. Score: `2^(-E(h(x))/c(n))`.
- **Local Outlier Factor (LOF)** — compara la densidad local de un punto con la de sus vecinos. Puntos en zonas de baja densidad relativa = outliers.

---

## 5. Aprendizaje por Refuerzo

### Q-Learning

Algoritmo off-policy que aprende una **Q-table** con el valor de ejecutar una acción `a` en el estado `s`. La actualización sigue la ecuación de Bellman:

```
Q(s,a) ← Q(s,a) + α[r + γ·max Q(s',a') - Q(s,a)]
```

- `α` — tasa de aprendizaje
- `γ` — factor de descuento (importancia del futuro)
- `ε-greedy` — equilibrio exploración/explotación

### SARSA

Similar a Q-Learning pero **on-policy**: actualiza con la acción que *realmente* tomará el agente (no el máximo teórico). Más conservador en entornos con penalizaciones.

---

## 6. Deep Learning

### Perceptrón y Redes Neuronales

El **perceptrón** es la unidad básica: calcula una suma ponderada de entradas y aplica una función de activación. Una red neuronal encadena capas de perceptrones. El entrenamiento usa **backpropagation** + **gradient descent**.

Funciones de activación comunes: Sigmoide, ReLU, Tanh, Softmax.

### CNN (Redes Neuronales Convolucionales)

Especializadas en datos espaciales (imágenes). Capas clave:

- **Convolucional** — detecta patrones locales con filtros.
- **Pooling** — reduce dimensionalidad preservando características relevantes.
- **Fully connected** — clasificación final.

Aplicaciones: clasificación de imágenes, detección de objetos, segmentación.

### RNN (Redes Neuronales Recurrentes)

Diseñadas para datos secuenciales. Mantienen un **estado oculto** que captura contexto temporal. Problema: **vanishing gradient** en secuencias largas. Solución: **LSTM** y **GRU**, que incorporan mecanismos de puertas para controlar el flujo de información.

---

## 7. IA Generativa

### Large Language Models (LLMs)

Modelos basados en la arquitectura **Transformer** entrenados en grandes corpus de texto. Componentes clave:

- **Tokenización** — convierte texto en tokens numéricos.
- **Embeddings** — representaciones vectoriales de los tokens.
- **Mecanismo de atención** — permite al modelo relacionar cualquier par de tokens independientemente de su distancia.
- **Atención multi-cabeza** — varias cabezas de atención en paralelo capturan diferentes tipos de relaciones.

El entrenamiento tiene dos fases: **pre-entrenamiento** (predecir el siguiente token sobre corpus masivo) + **fine-tuning** (ajuste a tarea específica, incluyendo RLHF para alineación).

### Modelos de Difusión

Generan imágenes aprendiendo a **revertir un proceso de ruido**:

1. **Proceso forward** — se añade ruido gaussiano gradualmente hasta destruir la imagen original.
2. **Proceso reverse** — una red neuronal (típicamente U-Net) aprende a predecir y eliminar el ruido paso a paso.
3. **Generación** — se parte de ruido puro y se aplica iterativamente el proceso inverso.

Para generación texto→imagen, se condiciona el proceso de denoising en un **embedding del texto** (obtenido con un encoder de lenguaje). El **noise schedule** controla cuánto ruido se añade en cada paso.

---

## Mapa conceptual rápido

```
IA
└── ML
    ├── Supervisado
    │   ├── Regresión Lineal / Logística
    │   ├── Árboles de Decisión
    │   ├── Naive Bayes
    │   └── SVM
    ├── No supervisado
    │   ├── K-Means
    │   ├── PCA
    │   └── Detección de Anomalías (One-Class SVM, Isolation Forest, LOF)
    ├── Refuerzo
    │   ├── Q-Learning
    │   └── SARSA
    └── Deep Learning
        ├── Redes Neuronales (MLP)
        ├── CNN
        ├── RNN / LSTM / GRU
        └── IA Generativa
            ├── LLMs (Transformers)
            └── Modelos de Difusión
```
