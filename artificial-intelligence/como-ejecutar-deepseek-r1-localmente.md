---
title: "Como Ejecutar DEEPSEEK-R1 Localmente: Ollama + Open Web UI"
author: "Cristian Perafán"
date: 2025-02-01
description: "En este artículo aprenderás a ejecutar DEEPSEEK-R1 localmente utilizando Ollama y Open Web UI."
tags:
- DeepSeek
- DeepSeek-R1
- Ollama
- Open Web UI
---

# **Como Ejecutar DEEPSEEK-R1 Localmente: Ollama + Open Web UI**

DeepSeek R1 es un modelo de inteligencia artificial de código abierto desarrollado en China que, según algunos benchmarks, supera a competidores como OpenAI-o1. Dado que la mayoría de las computadoras personales no cuentan con los recursos para ejecutar grandes modelos de lenguaje, utilizaremos una versión más liviana de DeepSeek R1, que cuenta con 7.000 millones de parámetros.

![DeepSeek R1 Benchmarks](https://github.com/deepseek-ai/DeepSeek-R1/raw/main/figures/benchmark.jpg)
*Benchmarks de DeepSeek R1 comparados con otros modelos*

En esta guía, aprenderás a ejecutar DeepSeek R1 localmente utilizando tres herramientas principales:

- [**Ollama**](https://ollama.com/): una herramienta de código abierto que permite ejecutar LLMs localmente.
- [**Open Web UI**](https://openwebui.com/): una interfaz web que facilita la ejecución de LLMs y la visualización de resultados.
- [**Docker**](https://www.docker.com/): para garantizar coherencia, aislar dependencias y simplificar el uso de Open Web UI.

## **Instalación de Ollama**

### **Paso 1: Instalación de Ollama**

1. Visita el sitio web oficial de [Ollama](https://ollama.ai/download).
   
   ![Ollama Download](https://i.postimg.cc/wvpGFxD1/image.png)
   *Descarga de Ollama*

2. Selecciona la opción correspondiente a tu sistema operativo.
3. Descarga el archivo y ejecuta el archivo de instalación.
4. Una vez completada la instalación, abre un CMD y ejecuta el siguiente comando para verificar que Ollama esté correctamente instalado:
   
   ```sh
   ollama --version
   ```

   ![Ollama Version](https://i.postimg.cc/pLGHrjbP/image.png)
    *Verificación de la versión de Ollama*

### **Paso 2: Descargar el Modelo DeepSeek R1**

1. En la terminal, ejecuta el siguiente comando para descargar el modelo de 7 mil millones de parámetros:
   
   ```sh
   ollama run deepseek-r1:7b
   ```

    ![Descarga de DeepSeek R1](https://i.postimg.cc/YqPGFvwz/image.png)
     *Descarga de DeepSeek R1*
2. Espera a que la descarga finalice.
3. Una vez completada la instalación, puedes interactuar con el modelo.

    ![Interacción con DeepSeek R1](https://i.postimg.cc/Nf0bYCtC/image.png)
     *Interacción con DeepSeek R1*


## **Instalación de Open Web UI**

### **Paso 1: Instalación de Open Web UI**

1. Con el fin de facilitar el uso de Open Web UI, Open Web UI proporciona una imagen de Docker que incluye todas las dependencias necesarias para ejecutar la interfaz web. Por lo tanto, necesitarás tener Docker instalado en tu sistema. Si aún no lo tienes, puedes descargarlo desde el sitio web oficial de [Docker](https://www.docker.com/).
   
   ```sh
   docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
   ```

3. Una vez finalizada la instalación, abre tu navegador y dirígete a `http://localhost:3000`.
4. Ingresa con cualquier credencial para la cuenta de ADMIN, ya que estas seran almacenadas localmente.

    ![Open Web UI](https://i.postimg.cc/7Z926wNg/image.png)
     *Interfaz de Open Web UI*


## **Interacción con DeepSeek R1 a través de Open Web UI**

1. En la interfaz de Open Web UI, escribe tu pregunta. *Recuerda en la parte superior izquierda seleccionar el modelo DeepSeek R1*.

![Open Web UI DeepSeek R1](https://i.postimg.cc/wx44tYwY/image.png)
*Interfaz de Open Web UI con DeepSeek R1 seleccionado*

2. Espera a que el modelo genere una respuesta.

![Interacción con DeepSeek R1](https://i.postimg.cc/wx44tYwY/image.png)
*Interacción con DeepSeek R1 a través de Open Web UI*


## **Consideraciones Finales**

- Los modelos con más parámetros ofrecen mejores respuestas, pero requieren más recursos computacionales.
- Open Web UI permite utilizar distintos modelos alojados en Ollama, brindando flexibilidad según las necesidades del usuario.


Felicidades 🎉, ahora tienes una configuración completa para utilizar DeepSeek R1 localmente con Open Web UI. Esta combinación te permite acceder a un potente modelo de inteligencia artificial directamente desde tu computadora sin depender de servicios en la nube. 