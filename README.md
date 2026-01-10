# arquitectura-rag
Repository for practicing RAG Architecture.

Después de un rechazo en otra posición, ando persiguiendo otra... que involucra inteligencia artificial.

Ahora veremos el mero mole y pozole. La arquitectura RAG consiste en:

# Embeddings
Parte fundamental del RAG, sirve para convertir texto en vectores que tengan similitud semántica del 
query del texto

# Vector store
Almacenamiento en memoria o bases de datos de vectores de los datos de la query vectorizada en 
similitud con los datos que vectorizaste de las fuentes de datos almacenadas en tu base de datos.

Estos se populan por medio de chunks, no almacenas todo el texto completo. Puede tener temas implicitos de procesados
como etls en escenarios aun más cañones.

#Retriever
Decide en base al algoritmo interno cuantos documentos (de la bdd de vectores categorizados) va a utilizar.
Porque claro, cada informacion o cada base tiene su contexto propio, lo cual representa cierto tiempo
de computación.

Con esto, empezamos a aventurarnos en las nuevas generaciones, para no sentirnos viejos, porque estudiar leetcode
quita tiempo.


