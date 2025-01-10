---
title: "Construyendo un RAG para chatear con PDF's en Python"
author: "Cristian Perafán"
date: 2025-01-08
description: "Aprende a crear un RAG en Python para evaluar el desempeño de tus proyectos."
tags:
- Python
- Programación
- Inteligencia Artificial Generativa
- Procesamiento de Lenguaje Natural
- PDF
---

# Construyendo un RAG para chatear con PDF's en Python

Los grandes modelos de lenguaje (Large Language Models, LLM) a menudo proporcionan respuestas genéricas que no son específicas para un contexto dado. Aquí es donde la técnica de Generación Aumentada por Recuperación (**RAG**, por sus siglas en inglés) se vuelve crucial. **RAG** nos permite integrar el conocimiento base de un LLM con conocimiento externo.

Imagina una empresa que tiene una colección de documentos internos, como políticas o manuales de capacitación. Quieren aprovechar el poder de los **LLMs** para proporcionar información a los usuarios o empleados, pero está claro que los **LLMs** no tienen acceso a estos documentos. Al utilizar **RAG**, la empresa puede construir un sistema que recupere información relevante de estos documentos y genere respuestas con **LLMs** adaptadas a las preguntas específicas de los empleados o clientes.

## ¿Cómo funciona?

![Diagrama de RAG](https://miro.medium.com/v2/resize:fit:828/format:webp/1*LLMjcL_dO3kha0_4aFJtQg.png)


1. **Consulta del usuario:** **RAG** recibe una pregunta o tarea.
2. **Búsqueda de información:** El sistema RAG consulta información de una fuente de datos, que podría ser una API, base de datos, documentos o información de la web.
3. **Proporcionar contenido al LLM:** Con la información recuperada, el **LLM** genera respuestas basadas en lo que ha aprendido de los datos.
4. **Generar salida:** Aquí es donde ocurre la magia, RAG genera una respuesta personalizada.


## Beneficios de RAG

- **Previene las alucinaciónes del modelo**: El conjunto de datos con el que se entrenó el modelo rara vez se actualiza o puede estar desactualizado, lo que lleva al modelo, en algunos casos, a generar respuestas parcialmente falsas.  
- **Ahorro de costos**: Reentrenar los parámetros del modelo puede requerir mucho tiempo y ser costoso. RAG puede reducir significativamente el costo de operar LLMs en un contexto empresarial.  
- **Mayor confianza del usuario**: RAG permite que las respuestas incluyan atribuciones a la fuente de información, lo que aumenta la confianza en una solución impulsada por IA generativa.  

## Manos a la obra

En este tutorial, utilizaremos las siguientes bibliotecas de Python:

- **LangChain**: Framework que ayuda a los desarrolladores a crear aplicaciones utilizando modelos de lenguaje de gran escala (LLMs).  
- **Chroma**: Base de datos vectorial de código abierto utilizada principalmente para almacenar y recuperar de manera eficiente incrustaciones vectoriales.  

### Prerequisitos  
  - En este tutorial Utilizo un documento PDF titulado [Home Gardeners Guide](https://www.extension.purdue.edu/extmedia/HO/HO-32-W.pdf) de la Universidad de Purdue.  
  - Configura tu clave de API de OpenAI.  

### Preprocesamiento de texto

En este ejemplo, utilicé un documento titulado [Home Gardeners Guide](https://www.extension.purdue.edu/extmedia/HO/HO-32-W.pdf). Puedes usar uno o más archivos PDF de tu elección.  
1. Define el nombre de la carpeta donde se encuentran los archivos PDF.  
2. Carga los documentos PDF.  
3. **Chunk text**: Se refiere al proceso de dividir un documento grande en piezas más pequeñas de información, llamadas "fragmentos". Dividir el documento en partes más pequeñas permite que el sistema de recuperación indexe, busque y devuelva fragmentos específicas de información en lugar de todo el documento.  

`src/text_processor.py`

```python
from langchain_community.document_loaders import PyPDFDirectoryLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_core.documents import Document

# Ruta al directorio que contiene los archivos PDF
DOCUMENTS_PATH = 'documents'
def chunk_pdfs() -> list[Document]:
    # Inicializa el cargador de documentos y carga los documentos
    document_loader = PyPDFDirectoryLoader(DOCUMENTS_PATH)
    documents = document_loader.load()

    # Inicializa el divisor de texto
    text_splitter = RecursiveCharacterTextSplitter(
        chunk_size=800, # Tamaño de cada chunk en caracteres
        chunk_overlap=100, # Superposición entre chunks en caracteres
        length_function=len, # Función para calcular la longitud del texto
        add_start_index=True, # Agregar índice de inicio a los chunks
    )

    # Divide los documentos en chunks
    chunks = text_splitter.split_documents(documents)

    return chunks
```
### Carga de Datos

Una vez que los documentos se dividieron en piezas más pequeñas, los guardamos en la base de datos vectorial [Chroma](https://www.trychroma.com/). Python proporciona una integración fluida con [Chroma](https://www.trychroma.com/) para este propósito.

![Diagrama de un modelo de Embedding](https://miro.medium.com/v2/resize:fit:828/format:webp/1*s2yvUt4dMzI-QGRotNMA3w.png)

Al trabajar con una base de datos vectorial, podemos usar un modelo de embedding. Un modelo de embedding traduce texto u otros tipos de datos en representaciones numéricas que la base de datos vectorial puede procesar y comprender. En este ejemplo, utilizaremos el modelo de embedding de OpenAI para este propósito.

`src/chroma_dp.py`

```python
import os
import shutil
from langchain_community.vectorstores import Chroma
from langchain_core.documents import Document

CHROMA_PATH = 'chroma'
def save_to_chroma_db(chunks: list[Document], embedding_model) -> Chroma:
    # Elimina la base de datos Chroma si ya existe
    if os.path.exists(CHROMA_PATH):
        try:
            shutil.rmtree(CHROMA_PATH)
        except Exception as e:
            print(f"Error removing Chroma database: {e}")

    # Crea una nueva base de datos Chroma
    db = Chroma.from_documents(
        chunks,
        persist_directory=CHROMA_PATH,
        embedding=embedding_model
    )
    # Guarda los chunks en la base de datos Chroma
    print(f"Saved chunks to {CHROMA_PATH}")
    return db
```

### Recuperación de la Información

Una vez que hayamos implementado los métodos para preprocesar nuestros documentos PDF, podemos comenzar a integrar todo el flujo de trabajo en un archivo `main.py`:

1. Preprocesar el texto y generar fragmentos de documento para almacenar en la base de datos vectorial.
2. Importar el modelo de incrustación de OpenAI.  
3. Guardar los documentos procesados en la base de datos vectorial.  
4. Recuperar los 5 documentos más similares a un contexto dado y fusionarlos en una sola cadena. El contexto que utilizaremos es: “¿Cuáles son los pasos recomendados para fertilizar un jardín de verduras? 

`main.py`

```python
import os
from src.file_processor import chunk_pdfs
from src.chroma_db import save_to_chroma_db
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import OpenAIEmbeddings
from langchain.chat_models import ChatOpenAI

if not os.getenv("OPENAI_API_KEY"):
    os.environ["OPENAI_API_KEY"] = "<your-openai-api-key>"

# Preprocesar los documentos PDF
processed_documents = chunk_pdfs()
# Inicializar el modelo de embedding de OpenAI
embedding_model = OpenAIEmbeddings(
    model="text-embedding-3-large"
)
# Guardar los documentos procesados en la base de datos vectorial
db = save_to_chroma_db(processed_documents, embedding_model)

query = "What are the recommended steps for fertilizing a vegetable garden?"

# Recuperar los documentos más similares al contexto dado
docs = db.similarity_search_with_score(query, k=3)

# Fusionar los documentos recuperados en una sola cadena
context = "\n\n---\n\n".join([doc.page_content for doc, _score in docs])
```

### Generación de Respuestas

**Generar respuestas con el LLM**  
Los Prompt Templates son una herramienta que nos permite estructurar de manera efectiva las interacciones con los LLM, creando prompts reutilizables y estructurados. Ayudando al modelo a comprender el contexto y generar respuestas relevantes.

- Definir el Prompt template.
- Usar el prompt template para generar una respuesta con el LLM de OpenAI. 


`main.py`

```python
# Definir el prompt template
PROMPT_TEMPLATE = """
You have to answer the following question based on the given context:
{context}
Answer the following question:{question}
Provide a detailed answer.
Don't include non-relevant information.
"""

# Crear el prompt 
prompt_template = ChatPromptTemplate.from_template(PROMPT_TEMPLATE)
prompt = prompt_template.format(context=context, question=query)

# Importar el modelo de chat de OpenAI y generar una respuesta
model = ChatOpenAI()
response = model.predict(prompt)

print(response)
```

Respuesta genera por el **RAG**:

```plaintext
The recommended steps for fertilizing a vegetable garden are as follows:

1. Establish the basic fertility level by applying the right kind and amount of fertilizer to your garden soil. This can be determined through a soil test, which will indicate the specific fertilizer needed.

2. Apply fertilizer to maintain the basic fertility level each year after it has been established. This ensures that your soil remains at the optimal fertility level for growing healthy plants.

3. If the soil test recommends "no basic application" due to adequate fertility levels, then skip this step and monitor the soil for any excess elements that may need to be addressed.

4. Plow or spade the soil after applying half of the recommended fertilizer to distribute it evenly throughout the top 7 inches of soil. This helps ensure that the plants receive the necessary nutrients for growth.

By following these steps, you can effectively fertilize your vegetable garden and promote healthy plant growth throughout the growing season.
```

¡Felicidades! 🎉 Has creado con éxito un sistema básico de RAG que recupera documentos pertinentes y los utiliza como contexto para generar respuestas informativas.

Puedes encontrar el código completo en: [GitHub](https://github.com/CristianPerafan/Retrieval-Augmented-Generation)
