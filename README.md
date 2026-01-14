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

# Avances 10/01/2025
Después de mucha investigacón y compilaciones de prueba y error, he investigado tópicos muy interesantes sobre la IA,
me doy cuenta de que esto es otro campo, muy cañon, que si requiere su tiempo para masterizarse y agarrar lo básico.

Pero bueno, algunos puntos starter:

-Claude no conviene pagarlo porque mencionan en reddit hay muchos temas de facturación, al cancelarlo especificamente.

-Hugging face es lo más barato pero no lo más ideal para producción

-No todos los modelos sirven como agentes, algunos son menos obedientes que otros (en Hugging face).

-Las limitaciones están en el entrenamiento, y el como tu limpies y proceses los datos, la complejidad recae
en el retunning y cuantas fuentes de datos diferentes usas (ETL's, batches, chunking, páginas web)

Vale, para esta práctica solo me basé en la documentación de práctica de la documentación de LangChain: https://docs.langchain.com/oss/python/langchain/rag

Para esto, solo hemos orquestado un vector database en memoria, iremos profundizando, haré unos datasets en pdf más de ejemplo, de acuerdo a algún tópico.
Iremos escalando este proyecto conforme vayamos viendo los tópicos.

**Extractor**

Extraer la información de n documentos, desde textos, bases de datos normales, de acuerdo a la docu
puedes extraer varias cosas... a nivel sintáctico ¿será posible? a lo mejor no

<img width="1166" height="1101" alt="image" src="https://github.com/user-attachments/assets/fbef360b-01a4-46ca-9bce-1815ad024718" />

La documentación recomienda unas librerias 
<img width="1901" height="1277" alt="image" src="https://github.com/user-attachments/assets/a1a36656-99e3-45ea-8192-16c505fd77ba" />

Para otros casos lo recomendable es usar algún lenguaje como GO para extraer capturas de pdfs que estén digitalizados. O Python si no se
requieren recursos.

Se hacen batches por letra para descartar asciis y saltos de linea, nos interesa que todo sea sobre un solo parrafo al final:

<img width="1371" height="991" alt="image" src="https://github.com/user-attachments/assets/4bad93e3-01bb-4c3d-a347-78af1ec634f4" />

*Embedder*

Aquí se decide cómo guardas tus vectores de texto, leí que hay dos implementaciones importantes: FAISS y Mongo (AWS también). Por
ahora usé memoria.

<img width="1699" height="623" alt="image" src="https://github.com/user-attachments/assets/49e1e151-eac8-4593-b910-66555ddd7c78" />

**Model agent**

Es el agente que ya está entrenado para cosas básicas pero, que lo puedes adaptar a tu contexto para
búsquedas más especificas de información, es decir lo adaptas a lo corporativo. Puedes crear consultas
customizadas y concretas expandiendo y... sometiendo a tu modelo en comportamiento para que no "alucine".

Esta fue la parte que más me consumió el día, porque tuve que ver esos conceptos de que muchos agentes, no sirven para rags,
o son más independientes, o su comportamiento cambia de acuerdo al rol. También es un dolor de cabeza entender los outputs del objeto de resultados.

Usé deepseek que si es bueno pero te trae su razonamiento, otros no me sirvieron, el de microsoft de la documentación creo ni sirve.
Pero gpt-oss-20b sirve a la perfección

<img width="1699" height="623" alt="image" src="https://github.com/user-attachments/assets/b8a1d56c-a430-4683-9814-fcb656a8f4eb" />

Estas son las reglas del agente, le especificamos explicitamente que use su tool para solo traer contexto de la información recolectada en el tool

<img width="1314" height="413" alt="image" src="https://github.com/user-attachments/assets/2568338e-23ab-4686-bd32-c4c857b1f96f" />

Este es el documento que usamos de prueba, sobre una herramienta ficticia llamada plataforma atlas:

<img width="1105" height="875" alt="image" src="https://github.com/user-attachments/assets/9a31a3e5-a425-4022-b69a-ba47ebf1e185" />

Las respuestas van de acuerdo al contexto del pdf:


<img width="1491" height="104" alt="image" src="https://github.com/user-attachments/assets/ca7b0afd-fbb9-4854-8311-c5356e0e33a1" />


Dandole un mejor tunning al system prompt, me suelta detalles parafraseados, resumidos pero precisos:


<img width="1607" height="1058" alt="image" src="https://github.com/user-attachments/assets/b9f203e2-6734-4b76-9605-9eb8ff3d56d3" />

Relacionados al contexto principal:


<img width="862" height="498" alt="image" src="https://github.com/user-attachments/assets/e3f462a6-93c2-485b-91ba-850875c80948" />


Tuneo final:

prompt = (
    "Eres un agente de langchain con acceso a una tool que te permite consultar información de un pdf sobre la plataforma Atlas, denominada content_and_artifact"
    "Usa esa tool para responder exclusivamente a preguntas relacionadas con la plataforma atlas explicitamente"
    "En caso de no encontrar la respuesta a la pregunta con la herramienta, solo indica que no tienes esa info, no busques en internet"
    "Retorna las respuestas en español"
    "Puedes parafrasear las respuestas"
    "Da respuestas detalladas cuando solo se requiera, de acuerdo al contexto"
    "No repitas palabras o contextos ya explicados durante la composición de tu respuesta"
    "Si hay definiciones que puedes dar de manera resumida, y breve y sin listados, narralos de esa manera"
    "si la pregunta requiere detalles o listados, puedes dejar de ser breve, pero solo si te piden más detalle explicitamente"
    "Si te piden ejemplos, procura dar ejemplos o casos de uso pero siempre globalizados de acuerdo al contexto de la información de tu tool"
)

Pregunta: ¿Qué es la plataforma atlas?

r=La Plataforma Atlas es un sistema interno creado para centralizar la gestión de la información operativa y analítica dentro de una organización tecnológica. Su función principal es permitir la consulta, actualización y trazabilidad de datos vinculados a proyectos, equipos y procesos internos. Atlas se construye con una arquitectura modular, lo que posibilita que cada componente evolucione de forma independiente sin afectar al resto, facilitando la escalabilidad conforme crece el volumen de datos y el número de usuarios concurrentes. Entre sus módulos más destacados se encuentran la Gestión de Proyectos, que registra proyectos activos e históricos con sus responsables y estados, y la Gestión de Equipos, que consolida la información de los grupos de trabajo.

¿Para qué me sirve la plataforma atlas?

r=EMPEZANDO A EJECUTAR EL AGENTE BASADO EN EL CONTEXTO DEL PDF
La plataforma Atlas está pensada para que tu organización tenga un único punto de referencia donde gestionar y analizar toda la información operativa.  

- **Centraliza datos**: reúne datos de proyectos, equipos y procesos en un solo repositorio, evitando que la información se disperse en hojas de cálculo o sistemas aislados.
- **Facilita la trazabilidad**: cada registro lleva su historial de cambios, lo que permite saber quién modificó qué y cuándo.
- **Apoya la toma de decisiones**: con datos consolidados y actualizados, los líderes pueden generar reportes y dashboards que reflejen el estado real de los proyectos y recursos.
- **Escala con la organización**: su arquitectura modular permite añadir o actualizar módulos sin interrumpir el funcionamiento general, adaptándose al crecimiento de usuarios y volumen de datos.     

Y ya me acabé mis créditos jejejejeje.

Con esto creas un buen MCP para prácticas. Lo próximo sería, adaptarlo a una api con FastAPI, y explorar lo siguiente:

-Similarity vs MMR

-Hybrid search:

-Embeddings + BM25

-Metadata filtering:

-fechas

-versiones

-idioma

-Re-ranking (MUY importante)

-Temperature ≠ calidad

-Top-p vs top-k

-Max tokens vs truncation

-Stop sequences

-Batch embeddings

-Caching (query + embeddings)

-Streaming responses

-Quantization (INT8 / 4-bit)


## Avances 12/01/2025

Hicimos solo una pequeña modificación para hacer el aplicativo menos dependiente de la memoria, exportamos el pdf a una instancia de MongoDB
para guardar los datos vectorizados. En mongo db se debe crear algo denominado search index, de tipo vector, para las transformaciones:

<img width="929" height="682" alt="image" src="https://github.com/user-attachments/assets/11e0859d-9737-4c76-9064-973862598471" />


El parametro de número de dimensiones es muy importante, relacionado al embedding que se usa para trasnformar el texto en los datos númericos vectoriales de esta linea:
<img width="887" height="21" alt="image" src="https://github.com/user-attachments/assets/2eb195c7-3fbf-4b5c-b99a-e4942000b648" />

Efectivamente se guardan, el objeto Document de la libreria LangChain wrappea correctamente nuestros registros, ya no son solo arreglos de texto,
pueden tener metadatos para diferenciar la fuente o cosas extras!

<img width="1914" height="1075" alt="image" src="https://github.com/user-attachments/assets/7f402ee5-05b6-4dd9-803f-2ce267278741" />


El output sale normal, sin depender de memoria, ahora guardas vectores. 

# Avances 14/01/2025

Ahora quise implementar lo que llaman como short term memory, aquí sugieren usar una instancia de postgress, que lo que hace es guardar los mensajes a corto plazo,
contruir una memoria para nuestro agente en pocas palabras.

Para prácticas puedes usar tu RAM pero lo más apropiado es usar un docker compose con tus credenciales configuradas:
ersion: '3.8'
services:
  postgres:
    container_name: postgress
    image: postgres:17-bookworm
    restart: always
    environment:
      POSTGRES_USER: root
      POSTGRES_PASSWORD: tu contra wey
      POSTGRES_DB: rag_memory
      POSTGRES_HOST_AUTH_METHOD: md5  # ← Añade esto
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - postgres-net
volumes:
  postgres_data:

networks:
  postgres-net:
    external: true

Short memory es para recordar conceptos sobre un hilo de conversación, me costó un guebo aunque es sencillo configurarlo porque,
se me olvidó que tenía una instancia de postgress activa desde el 2024 en mi computadora y me corrompia la conexión del Python.

Una vez hecho esto es fácil entender los siguientes términos:

Conversational memory es para que tu agente recuerde datos que le das en la misma conversación. Long term
es para recordar conceptos que le diste en otras conversaciones. Esas conversaciones puedes catalogarlas con algo
llamado thread_id, relacionado a un user_id de tu implementación preferida:

<img width="1086" height="1287" alt="image" src="https://github.com/user-attachments/assets/684d8277-e20b-4207-b4ad-1c691e3a153d" />

Con esto puedes probar esa memoria a corto plazo, solo diciendole tu nombre, una pregunta, volviendole a preguntar algo y preguntandole tu nombre otra vez, listo
tiene tu RAG una memoria:

<img width="1815" height="287" alt="image" src="https://github.com/user-attachments/assets/8d7ca093-ba6a-41c3-a142-0ce2faa1d8ec" />

De ahí son tópicos parecidos, ya no se extiende tanto el tema del agente, algunas extensiones que pudieran practicarse son solo los bloques
asincronicos y armarlo con una GUI web.

De ahí siguen los agentes de SQL, pero a grandes razgos no es un tema dificil para aprender en conceptos básicos.



<img width="1847" height="1059" alt="image" src="https://github.com/user-attachments/assets/4396a8e8-07c3-4903-8371-e253f9d04991" />

Orita que sea de noche le sigo para puntos más pequeños, y quiero refinar el prompt.
