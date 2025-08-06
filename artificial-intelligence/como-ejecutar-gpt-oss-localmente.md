---
title: "Como ejecutar los nuevos modelos de OpenAI en LOCAL: Ollama + GPT-OSS"
author: "Cristian Perafán"
date: 2025-02-01
description: "En este artículo aprenderás a ejecutar los nuevos modelos de OpenAI localmente utilizando Ollama."
tags:
- OpenAI
- gpt-oss
- Ollama
---

# **Como ejecutar los nuevos modelos de OpenAI en LOCAL: Ollama + GPT-OSS**

Recientemente OpenAI lanzó dos nuevos modelos de lenguaje de pesos abiertos: `gpt-oss-120b` y `gpt-oss-20b`, los cuáles superan a sus competidores en tareas similares de razonamiento y tienen buenas capacidades en lo que respecta a la eficiencia de uso de hardware. Estos nuevos modelos abren la puerta a un sin fin de posibilidades para desarrolladores y pequeñas empresas.

OpenAI menciona que el modelo `gpt-oss-20b`  puede ser ejecutado en dispositivos con 16GB de RAM, lo que incluso abre la posibilidad de poder ejecutarlo en computadoras personales y smartphones de gama alta.

En el caso que quieras profundicar más sobre estos modelos, puedes visitar el [Presentación de gpt-oss](https://openai.com/es-419/index/introducing-gpt-oss/).


<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*vjkIPeFDiV5fmsNqNmiVYQ.png" alt="Comparación de modelos de OpenAI (Fuente OpenAI)">
</p>

## **Qué es un Modelo de pesos abiertos?**

Un modelo de pesos abiertos es un modelo de inteligencia artificial cuyos pesos resultantes del entrenamiento son accesibles públicamente. Esto no significa que el código y datos de entrenamiento del modelo esten disponibles, sino que los pesos del modelo en sí pueden ser descargados y utilizados por cualquier persona.

Luego de esta breve introducción, veamos cómo ejecutar el modelo `gpt-oss-20b` localmente, ya que es el modelo más liviano de los dos.


## **Ejecutando el Modelo gpt-oss-20b**

**Paso 1: Instalación de Ollama**

1. Visita el sitio web oficial de [Ollama](https://ollama.ai/download).
   

   <p align="center">
   <img src="https://i.postimg.cc/wvpGFxD1/image.png" alt="Ollama Download">
   </p>
   
1. Selecciona la opción correspondiente a tu sistema operativo.
2. Descarga el archivo y ejecuta el archivo de instalación.
3. Una vez completada la instalación, se abrirá una ventana con la UI de Ollama.

   <p align="center">
   <img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*IzxUXSxickXB1JFaZZl_qA.png" alt="Ollama UI">
   </p>

**Paso 2: Descargar el Modelo gpt-oss-20b**

1. Selecciona el modelo `gpt-oss-20b` en la interfaz de usuario.

   <p align="center">
   <img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*LrbX3KoXcatLh7uzmK8cww.png" alt="Seleccionar Modelo">
   </p>

2. Escribe un mensaje en el cuadro de texto, en este caso voy a probar con el siguiente mensaje:
   
> Escribe una función en Python que determine si un número es primo. Luego úsala para imprimir todos los números primos entre 1 y 100.

   <p align="center">
   <img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*IuVGn7FGSHpKdssi7n5pzA.png" alt="Escribir Mensaje">
   </p>

3. Una vez que hayas escrito tu mensaje, se iniciará la descarga del modelo.

   <p align="center">
   <img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*bA_ldjYcqrcCKrveC5WHeg.png" alt="Descargando Modelo">
   </p>

4. Finalizada la descarga, el modelo comenzará a procesar tu mensaje y lo podrás utilizar localmente.

   <p align="center">
   <img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*uzx7UB5YmhIQpgsVDGF2oA.png" alt="Modelo Procesando Mensaje">
   </p>

**Paso 3: Descargar el Modelo gpt-oss-20b utilizando la Terminal**

1. Abre una terminal o CMD.
2. Escribe el siguiente comando para descargar el modelo `gpt-oss-20b`:

   ```sh
   ollama run gpt-oss:20b
   ```

3. Espera a que la descarga finalice.
4. Una vez completada la instalación, puedes interactuar con el modelo escribiendo mensajes directamente en la terminal.

   <p align="center">
   <img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*XmuJHt_XX4y8lQ8NWItXgQ.png" alt="Ejemplo de Uso en Terminal">
   </p>

## **Cierre**

Espero esta guía te haya sido útil para aprender a ejecutar los nuevos modelos de OpenAI localmente utilizando Ollama. Estos modelos es una gran disrupción en el mundo OpenSource y abren la puerta a un sin fin de posibilidades para desarrolladores y pequeñas empresas.

Hasta la próxima!😁



