---
title: "Automatiza un Reporte de Criptomonedas con n8n, ChatGPT y Telegram"
author: "Cristian Perafán"
date: 2025-01-08
description: "Este artículo aprenderas a como realizar un análisis automatizado de precios de criptomonedas y enviar un reporte diario a través de Telegram utilizando n8n."
tags:
- n8n
- Telegram
- criptomonedas
- automatización
---

# **Crear un Bot de Telegram para generar un reporte de precios de criptomonedas con n8n**

En este artículo aprenderas a como realizar un análisis automatizado del precio de criptomonedas y enviarlo a un Bot de Telegram utilizando n8n.

## **🧰 Requisitos**

Antes de comenzar, asegúrate de tener lo siguiente:
1. ✅ Docker instalado en tu máquina.
2. ✅ Una cuenta de Telegram.
3. ✅ Una API Key de OpenAI (ChatGPT).

## **Manos a la obra**

### **1. Instalar n8n con Docker**

Por motivos de simplicidad, utilizare Docker para instalar n8n. Si quiere realizar otro tipo de instalación, puedes consultar la [documentación oficial de n8n](https://docs.n8n.io/).
En tu consola, ejecuta los siguientes comandos:


1. Crear un volumen para n8n:
   ```bash
   docker volume create n8n_data
   ```

2. Ejecutar n8n con Docker:
   ```bash
   docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
    ```

Si la instalación fue exitosa, podrás acceder a n8n en tu navegador en [http://localhost:5678](http://localhost:5678).
Y veras la siguiente pantalla de configuración:

![n8n Setup](https://i.ibb.co/xSZmg5HM/set-up-n8n.png)


### **2. Crear un Bot de Telegram**

### **3. Crear un Workflow en n8n**

1. Entra a n8n y crea un nuevo workflow.

![Crear workflow](https://i.ibb.co/LXW6PMSY/Crear-workflow.png)

