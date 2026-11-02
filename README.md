Actividad 4: comparación de modelos de análisis de sentimiento
Este trabajo compara tres modelos que clasifican reseñas de películas en inglés como positivas o negativas. El experimento usa el conjunto IMDb de Kaggle y mide tanto la calidad de las predicciones como el tiempo que tarda cada modelo.

El notebook está preparado para ejecutarse en Google Colab. La ejecución guardada en el archivo evaluó 2,000 reseñas en una GPU NVIDIA Tesla T4. En esa prueba, SiEBERT obtuvo el mejor F1 y DistilBERT respondió más rápido.
Archivos
- Actividad_4 (1).ipynb: notebook con la descarga y preparación de los datos, la evaluación de los modelos y la exportación de resultados.
- Actividad 4.docx: documento de la actividad con el análisis comparativo y las conclusiones.
Este README acompaña esos archivos. Si se descargan por separado, guárdalos en la misma carpeta para mantener el proyecto organizado.

Qué se compara
Modelo	Función	Tamaño del archivo de pesos observado	Licencia indicada en la ficha consultada
DistilBERT SST-2	Clasifica el sentimiento en inglés	268 MB	Apache-2.0
BERT SST-2 (TextAttack)	Clasifica el sentimiento en inglés	438 MB	No indicada claramente; confirmar antes de usar
RoBERTa large SiEBERT	Clasifica el sentimiento en inglés	1.42 GB	No indicada claramente; confirmar antes de usar


Los tamaños pueden variar si el repositorio del modelo se actualiza. La licencia se debe confirmar en la ficha y los términos vigentes del modelo antes de cualquier uso externo o comercial.
Requisitos
- Cuenta de Google para abrir y ejecutar el notebook en Colab.
- Acceso a internet para descargar el conjunto de datos, las librerías y los modelos.
- Una GPU de Colab es recomendable para reducir el tiempo de ejecución. El notebook también puede usar CPU, aunque la evaluación tardará más.
- Acceso al conjunto IMDB Dataset of 50K Movie Reviews en Kaggle. Según la configuración de Kaggle y Colab, puede ser necesario autorizar la descarga.
- Un token de Hugging Face no suele ser necesario porque los modelos usados son públicos. El notebook incluye un inicio de sesión opcional.
Cómo ejecutar el notebook
1. Abre Google Colab y carga Actividad_4 (1).ipynb.
2. Para usar GPU, selecciona Entorno de ejecución → Cambiar tipo de entorno de ejecución → GPU. Si no eliges GPU, el notebook usará CPU.
3. Confirma que Colab puede descargar el conjunto de Kaggle. Si solicita autenticación, completa el flujo de Kaggle en tu entorno; no escribas ni compartas credenciales en el notebook.
4. Ejecuta las celdas en orden con Entorno de ejecución → Ejecutar todo. La primera celda instala las librerías y la descarga inicial de modelos puede tardar.
5. Revisa las tablas de métricas y latencia que aparecen al final. Las celdas también guardan dos archivos CSV en /content.
No es necesario ingresar un token de Hugging Face para descargar los modelos públicos incluidos. Si Colab muestra un aviso de token ausente, el notebook lo trata como opcional.
Datos y configuración
El notebook descarga el CSV mediante KaggleHub. Luego:
- Conserva las columnas review (texto) y sentiment (etiqueta).
- Elimina filas vacías y duplicadas.
- Convierte las etiquetas negative y positive a 0 y 1.
- Divide los datos de forma estratificada: 80% para entrenamiento y 20% para prueba. La estratificación mantiene una proporción parecida de reseñas positivas y negativas en ambas partes.
- Evalúa una muestra estratificada de 2,000 reseñas del conjunto de prueba.
Los parámetros principales están al inicio del notebook:
SEED = 42
EVAL_LIMIT = 2000
BATCH_SIZE = 16
MAX_LENGTH = 512
- SEED: fija la semilla de la división aleatoria para hacerla repetible.
- EVAL_LIMIT: cantidad máxima de reseñas de prueba. Usa None para evaluar todo el conjunto de prueba, lo que requiere más tiempo y recursos.
- BATCH_SIZE: cantidad de reseñas procesadas juntas.
- MAX_LENGTH: longitud máxima del texto que procesa el modelo. Los textos más largos se recortan.
El notebook no vuelve a entrenar ni ajusta los modelos. Los evalúa tal como se descargan.
Resultados guardados en el notebook
Las cifras siguientes corresponden a la ejecución incluida en el notebook: 2,000 reseñas y GPU Tesla T4. Pueden cambiar al volver a ejecutar el experimento por diferencias de hardware, versiones de librerías o modelos, y configuración del entorno.
Modelo	Accuracy	F1	Tiempo por reseña en lote	Mediana por reseña individual	p95 individual
DistilBERT SST-2	0.8870	0.8836	16.21 ms	10.92 ms	12.41 ms
BERT SST-2 (TextAttack)	0.9055	0.9050	32.94 ms	13.94 ms	18.26 ms
RoBERTa large SiEBERT	0.9570	0.9575	94.96 ms	45.51 ms	46.70 ms


Lectura rápida: SiEBERT tuvo la mejor puntuación general, pero también tardó más y ocupa más espacio. DistilBERT fue el más rápido y liviano, con menor F1. BERT quedó entre ambos en calidad y velocidad.

En el notebook, los tres modelos cumplen los umbrales sugeridos para la actividad: F1 de al menos 0.85 y latencia mediana individual de hasta 250 ms. Estos límites son orientativos y deben revisarse para el uso previsto.
Qué significan las métricas
- Accuracy: proporción de todas las reseñas clasificadas correctamente.
- Precisión: de las reseñas que el modelo marcó como positivas, qué proporción realmente era positiva.
- Recall: de todas las reseñas positivas reales, qué proporción encontró el modelo.
- F1: medida que combina precisión y recall. Ayuda a comparar el equilibrio entre ambas.
- Tiempo por reseña en lote: tiempo total para procesar un grupo, dividido por la cantidad de reseñas.
- Mediana individual: tiempo típico que tarda una reseña en recibir una predicción.
- p95 individual: el 95% de las mediciones individuales tardó ese tiempo o menos.
Archivos de salida
Al terminar las celdas de exportación, los archivos quedan en la sesión de Colab:
- /content/metricas_modelos.csv: accuracy, precisión, recall, F1, velocidad en lote y tiempo de carga de cada modelo.
- /content/latencias_individuales.csv: mediana y p95 del tiempo para clasificar una reseña individual.
Para conservarlos, descárgalos desde el panel Archivos de Colab antes de cerrar o reiniciar la sesión.
Alcance y limitaciones
- Los resultados corresponden a una muestra de IMDb y no garantizan el mismo rendimiento con reseñas de otras plataformas, textos breves, sarcasmo o idiomas distintos del inglés.
- La muestra está equilibrada entre las dos clases; otras proporciones de opiniones pueden cambiar la interpretación de las métricas.
- La ficha de SiEBERT menciona evaluaciones con datos de Kaggle. No se puede asegurar que no haya coincidencias con los datos de este ejercicio. Para una comparación independiente, conviene probar con reseñas nuevas que no se hayan usado para entrenar o evaluar los modelos.
- El tiempo medido depende del equipo y de la configuración. No representa por sí solo el costo monetario de un servicio.
- La descarga apunta a repositorios públicos que pueden cambiar. Para repetir exactamente el experimento, se recomienda registrar las versiones de los modelos y de las librerías utilizadas.


