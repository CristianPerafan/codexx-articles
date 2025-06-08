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

# **Automatiza un Reporte de Criptomonedas con n8n, ChatGPT y Telegram**

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

   ![n8n Setup](https://cdn-images-1.medium.com/max/1000/1*XrSyEGt0IJsY433wSxBJww.png)


### **2. Crear un Bot de Telegram**


1. Abre Telegram y busca el BotFather. O si quieres, puedes acceder directamente al siguiente enlace: [BotFather](https://telegram.me/BotFather). Deberás iniciar una conversación con él con el comando `/newbot`.

   ![BotFather](https://cdn-images-1.medium.com/max/800/1*qB8z4vZHLECRJF5iDULyPA.png)

2. Debes seleccionar un nombre para tu Bot y un nombre de usuario que termine en `bot`. Una vez que hayas creado el Bot, BotFather te proporcionará un token de API. Este token es esencial para interactuar con tu Bot de Telegram.

   ![Bot Token](https://cdn-images-1.medium.com/max/800/1*NjmiwndQrEgvQPSdxUxg9A.png)

3. Debemos guardar el HTTP API Token que nos proporcionó BotFather, ya que lo utilizaremos más adelante en n8n para enviar mensajes a nuestro Bot de Telegram. Inicia una conversación con tu Bot siguiendo el enlace: `https://t.me/tu_nombre_de_usuario_bot`, donde `tu_nombre_de_usuario_bot` es el nombre  que elegiste para tu Bot.

   ![Bot Link](https://cdn-images-1.medium.com/max/800/1*M0jJk-8ih2Tp1odA8Hz0FQ.png)


4. Una vez que hayas iniciado la conversación con tu Bot, envíale un mensaje para iniciar la interacción. Luego debes acceder al siguiente enlace para obtener tu ID de chat: `https://api.telegram.org/bot<YourBOTToken>/getUpdates`, donde `<YourBOTToken>` es el token de API que obtuviste de BotFather. Esto te proporcionará un JSON con información sobre tu Bot, incluyendo tu ID de chat.

   Ejemplo de url: https://api.telegram.org/bot123456789:jbd78sadvbdy63d37gda37bd8/getUpdates
   
   ![Chat ID](https://cdn-images-1.medium.com/max/800/1*9ulha9oMKDxPqQQ1WBfB5Q.png)

   Guarda este ID, ya que lo necesitaremos más adelante para enviar mensajes a tu Bot de Telegram.

### **3. Crear un Workflow en n8n**

1. Entra a n8n y crea un nuevo workflow.

   <a href="https://ibb.co/x8mPCXms"><img src="https://cdn-images-1.medium.com/max/1000/1*PO6GqzRUzcEJG4kllJ3efg.png" alt="Crear-workflow" border="0"></a>

2. Agrega un nuevo paso a la aplicación de tipo `On a schedule` para que se ejecute diariamente a las 7:00 AM. Configura el nodo como se muestra a continuación:

   <a href="https://ibb.co/BHwrnYQj"><img src="https://cdn-images-1.medium.com/max/1000/1*urPBB2joybvHgDKHmmynDA.png" alt="schedule-trigger" border="0"></a>

3. Agregar un nodo de tipo `HTTP Request` para obtener los precios de las criptomonedas. Para este ejemplo, utilizaremos la API de CoinGecko para traer los precios de Bitcoin, Ethereum y Solana. Configura el nodo como se muestra a continuación:
   
   - **Method**: `GET`
   - **URL**: `https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&ids=bitcoin,ethereum,solana`

   <a href="https://ibb.co/LddZ6gDH"><img src="https://cdn-images-1.medium.com/max/1000/1*DhRm5yar7fsqCxaKxVlUjw.png" alt="Output" border="0"></a>

   Adicionalmente, puedes hacer click en el botón `Execute Step` para probar el nodo y ver los resultados. Deberías obtener una respuesta similar a la siguiente:

4. Ahora, agrega un nodo de tipo `Function` para formatear los datos obtenidos de la API. Configura el nodo como se muestra a continuación:

   **Language**: `JavaScript`

   **JavaScript Code**:

   ```javascript
   const results = []

   for (const item of $input.all()) {

   const crypto = item.json;
   
   results.push({
      json: {
         id: crypto.id,
         name: crypto.name,
         symbol: crypto.symbol,
         price: crypto.current_price,
         volume: crypto.total_volume
      }
   });
   }

   return results
   ```

   Esta función tomará los datos de las criptomonedas del anterior nodo y obtendrá el nombre, símbolo, precio y volumen de cada criptomoneda.

   <a href="https://ibb.co/mCQSvZ4k"><img src="https://cdn-images-1.medium.com/max/1000/1*DhRm5yar7fsqCxaKxVlUjw.png" alt="Filterdata" border="0"></a>

5. Agregamos un nodo de tipo `OpenAI` para generar el reporte con los datos obtenidos, debemos seleccionar la actions de `Message a model` y configurar el nodo como se muestra a continuación:


   ![OpenAI Node](https://cdn-images-1.medium.com/max/800/1*Y_ZujKpvSWDbUELtF0wUhw.png)

6. Para utilizar este nodo debemos tener una cuenta de OpenAI y una API Key. Si no sabes como obtener una, puedes consultar crearla en [OpenAI](https://platform.openai.com/api-keys). Seleccionar crear nueva credencial y aparecerá una ventana emergente donde debes ingresar tu API Key.

   ![OpenAI API Key](https://cdn-images-1.medium.com/max/800/1*0CHJDv2ASsGeouf8mUQ_vg.png)

7. Una vez configurada la credencial, debes configurar el nodo de OpenAI como se muestra a continuación:

   - **Model**: `gpt-3.5-turbo`
   - **Prompt**: 
   ```markdown
   Analiza la siguiente criptomoneda y proporciona un resumen de su situación actual, posibles riesgos y oportunidades de inversión:

   Nombre: {{ $json.name }}
   Símbolo: {{ $json.symbol }}
   Precio actual: {{ $json.price }}
   Volumen total: {{ $json.volume }}
   Máximo en 24h: {{ $json.high_24h }}
   Mínimo en 24h: {{ $json.low_24h }}
   Suministro total: {{ $json.total_supply }}
   Suministro máximo: {{ $json.max_supply }}

   Por favor, incluye:
   - Un análisis del comportamiento en las últimas 24 horas
   - Un consejo general sobre si es recomendable vigilar esta criptomoneda
   ```

   Cada valor entre llaves `{{ }}` corresponde a los datos que obtuvimos del nodo anterior. Este nodo generará un análisis detallado de las tres criptomonedas que estamos monitoreando.

   Si ejecutamos el nodo, deberíamos obtener un resultado similar al siguiente:

   ![OpenAI Response](https://cdn-images-1.medium.com/max/800/1*d01EwQNpVLPXhiKy-uZv7g.png)

8. Ahora, agregamos un nodo de tipo `Telegram` para enviar el reporte generado a nuestro Bot de Telegram. Configura el nodo como se muestra a continuación, de tipo `Send Message`:

   ![Telegram Node](https://cdn-images-1.medium.com/max/800/1*9Ck0YHhDXz6INopv9RUuBw.png)

9. En este punto utilizaremos las credenciales `HTTP API` Token que obtuvimos del BotFather. 
   Si no has creado una credencial de Telegram en n8n, debes crearla ahora. Haz click en `Create New Credential`. Luego, ingresa el token de API que obtuviste del BotFather.

   ![Telegram API Key](https://cdn-images-1.medium.com/max/800/1*Dnta2PonAkbckgYzGXb2mg.png)

   Luego, de que hayas creado la credencial, debes configurar el nodo de Telegram como se muestra a continuación:

   - **Chat ID**: `ID de chat que obtuviste anteriormente`
   - **Text**: 
   ```markdown
   {{ $json.message.content }}
   ```

   Este nodo enviará el mensaje generado por OpenAI a tu Bot de Telegram.

   ![Telegram Message](https://cdn-images-1.medium.com/max/800/1*Y3kI3lr_bQJsNoNMf43xgQ.png)

 10. Finalmente, regresa al canvas y ejecuta el workflow completo. Si todo está configurado correctamente, deberías recibir un mensaje en tu Bot de Telegram con el análisis de las criptomonedas.
 
   ![Telegram Message Received](https://cdn-images-1.medium.com/max/800/1*anz29qCLqqVqSHyb8DZP_w.png)

   Ejecución completa del workflow:


   ![Workflow Execution](https://cdn-images-1.medium.com/max/800/1*oh4JQnPl3romCFwSZwtbmg.png)


## **Conclusión**

En este artículo, hemos aprendido a automatizar un análisis de precios de criptomonedas utilizando **n8n**, **ChatGPT** y **Telegram** 🤖📈. Hemos configurado un workflow que se ejecuta diariamente 🕒, obtiene los precios de las criptomonedas 💰, genera un análisis detallado 📊 y envía un reporte a nuestro Bot de Telegram 📩.

⚠️ **Importante**: Este es solamente un ejemplo básico con fines educativos y **no representa un consejo de inversión**. Siempre realiza tu propia investigación 🔍 antes de invertir en criptomonedas.

Espero que este artículo te haya sido útil 🙌 y te animes a explorar más sobre **n8n** y la automatización de tareas 🤓⚙️.

