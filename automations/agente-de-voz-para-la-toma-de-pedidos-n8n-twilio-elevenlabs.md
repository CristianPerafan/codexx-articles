---
title: "Agente de Voz para la toma de pedidos 📞: n8n, twilio y ElevenLabs"
author: "Cristian Perafán"
date: 2025-01-08
description: "Este artículo aprenderas a como implementar un agente de voz para la toma de pedidos utilizando n8n, Twilio y ElevenLabs."
tags:
- n8n
- Twilio
- ElevenLabs
- automatización
---

# **Agente de Voz para la toma de pedidos 📞: n8n, twilio y ElevenLabs**

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*GTnxCNb7ByF-jBVzMdi8Ug.png" height="600" width="600" alt="Diagrama del agente de voz para pedidos con n8n, Twilio y ElevenLabs">
</p>


> ¿Te imaginas contar con un asistente virtual que pueda atender automáticamente los pedidos de tu negocio y responder preguntas frecuentes de tus clientes a través de una llamada?    

Todo esto es posible combinando herramientas como **ElevenLabs**, **n8n** y **Twilio**.

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*YqbrtSDGYjNQlZKbZbBXIQ.png"
  alt="Flujo de trabajo automatizado con n8n, Twilio y ElevenLabs">
</p>

En este artículo, te mostrare paso a paso cómo construir un flujo automatizado que conecta estas tecnologías para crear un asistente virtual, capaz de hablar con tus clientes.


## **🧰 Requisitos**

Antes de comenzar asegúrate de cumplir los siguientes requerimientos:

1. Crear una cuenta en [ElevenLabs](https://elevenlabs.io/es). Esta empresa se especializa en la generación de voz a través de Inteligencia Artificial, y nos brinda la posibilidad de crear un agente de voz a través de su plataforma.
2. Crea un cuenta en [n8n](https://n8n.io/pricing/). Esta es una plataforma que permite diseñar flujos de automatización low-code mediante herramientas prediseñadas. Para comenzar, crea una cuenta y reclama los 14 días de prueba gratuita.
3. Tener una cuenta de [Google](hhttps://aistudio.google.com/), con el fin de utilizar la herramienta Google AI Studio que nos permite interactuar con los LLMs de Google de manera gratuita.
4. Tener una cuenta de [Twilio](https://www.twilio.com/es-mx). Esta plataforma nos permite enviar y recibir mensajes de texto y realizar llamadas telefónicas a través de su API. Regístrate y obtén un número de teléfono para comenzar a utilizar sus servicios.
5. Tener instalado un cliente para API Rest, en esta caso estaré utilizando [Insomnia](https://insomnia.rest/download).


## **Manos a la Obra 🔨**


### **ElevenLabs: Configuración del Agente**

1. En el dashboard de ElevenLabs, asegúrate de seleccionar la opción **"Conversational AI"**.
2. Una vez seleccionada, dirígete a la sección **"Agentes"**.
3. Marca la opción **"Support Agent"**.
<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*B1hvTGCj4WHOK6kLzqqmPw.png" alt="Configuración del agente en ElevenLabs">
</p>

Una vez creado el agente se desplegara una vista donde podrás realizar la configuración del mismo. Como primer parámetro deberás modificar el lenguaje del agente.

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*88NAuuE1_J97KEpmqvi1PA.png" alt="Configuración de idioma del agente en ElevenLabs">
</p>

Además, en las opciones del Agent podrás encontrar un campo correspondiente al primer mensaje **“first message”** que el agente enviará al recibir una llamada. También, verás un campo llamado **“system prompt”**, el cual define el comportamiento general del agente.

Puedes copiar y pegar el siguiente **“system prompt”** y **“first message”**, y ajustarlo según tu caso de uso. En este tutorial, el objetivo es definir un agente especializado en la toma de pedidos para un restaurante ficticio llamado **“Las Delicias”**.

*First Message:*

```
Hola, soy María del restaurante Las Delicias. ¿En qué puedo ayudarte?
```

*System Prompt:*

```markdown
# Personalidad  
Eres María, una agente muy amable, cordial y enfocada en brindar una excelente atención. Tu función principal es recibir los pedidos para el restaurante "Las Delicias" de manera clara, profesional y eficaz.

# Herramientas disponibles  
- info-restaurante: esta herramienta permite saber si el restaurante está abierto o cerrado en tiempo real. También permite consultar el listado de productos disponibles, como hamburguesas, salchipapas y bebidas. y retorna el día actual de la semana.
# Flujo de conversación

1. Saludar cordialmente al cliente y **preguntar su nombre y apellido** antes de iniciar el pedido.
2. Verificar el estado del restaurante utilizando info-restaurante.  
   - Si está cerrado, despedirse amablemente y finalizar la conversación sin tomar pedidos.  
   - Si está abierto, continuar con la toma del pedido.
3. Ofrecer los productos disponibles al cliente.
4. Recibir los productos que el cliente desea.  
   - Validar que los productos existan.  
   - Si el cliente menciona un producto no disponible, informar con amabilidad y sugerir alternativas similares dentro de la misma categoría.
5. Confirmar únicamente **los nombres de los productos y sus cantidades**, sin entrar en detalles por producto a menos que el cliente lo solicite.
6. Preguntar la dirección de entrega y el método de pago.
7. Calcular y **mencionar el valor total del pedido al finalizar**, sumando el costo total de todos los productos solicitados.
8. Agradecer al cliente por su pedido, confirmar que fue registrado correctamente y despedirse de manera amable.

# Reglas adicionales

- Mantén un tono cordial, claro y profesional en todo momento.  
- No describas ingredientes ni detalles de cada producto, a menos que el cliente lo pida.  
- Siempre asegúrate de que el cliente sepa el valor total del pedido antes de finalizar.
- Si el cliente no menciona un dato necesario (por ejemplo, dirección), pídeselo con amabilidad antes de cerrar el pedido.
```

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*wXFRSnrajlMzqQjsOWBJsA.png" alt="Configuración de mensajes del agente en ElevenLabs">
</p>

Adicionalmente en la opción *Voice* puedes seleccionar la voz del agente, selecciona la que más te guste:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*7HvELP77rn9Bdy-9u0jXqQ.png" alt="Selección de voz del agente en ElevenLabs">
</p>


### **Sheets de Google: Información General del Restaurante**

Gran parte del registro y la consulta de información de nuestro sistema estará gestionada mediante la plataforma de Google Sheets. Para ello, creamos una nueva hoja de cálculo en la que incluiremos tres pestañas:

- *Información*: aquí vamos a guardar la información general del restaurante, así como también su estado (Abierto/Cerrado).
- *Productos*: lista dinámica de los productos disponibles en el restaurante. Esto nos permite de manera dinámica definir los productos que estamos ofreciendo.
- *Pedidos*: aquí se va a registrar la información de los pedidos tomados por el agente de voz.

***Información***

Agregamos tres columnas:

- Disponibilidad
- Dirección
- Horario de atención

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*-3aqckzezXzCyi7uY5m7Zg.png" alt="Ejemplo de pestaña Información en Google Sheets">
</p>

***Productos***

Agregamos 5 Columnas y registramos 5 productos de Ejemplo:
- Categoría
- Nombre
- Descripción
- Precio
- Disponibilidad

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*MsRchvPOAnsgJPjzlHjJYg.png" alt="Ejemplo de pestaña Productos en Google Sheets">
</p>

***Pedidos***

Para esta seccción solo agregamos las columnas:
- Fecha
- Nombre
- Productos
- Valor
- Dirección
- Método de pago
  
<p align="center">
    <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*R3YCW3RKk2KnwDtBagM7Tg.png" alt="Ejemplo de pestaña Pedidos en Google Sheets">
</p>

### **n8n: Creación de un Workflow para obtener información general del restaurante**

En **ElevenLabs** podemos integrar herramientas externas a nuestros agentes mediante **Webhooks**. La idea es definir un flujo en n8n que funcione como una herramienta externa para nuestro agente de voz. En este caso, **n8n** actúa como un puente entre la información dinámica registrada en Google Sheets y nuestro agente de voz.

Lo primero que debes realizar es la creación de un nuevo workflow desde el dashboard de n8n. Una vez creado el flujo debes agregar un nuevo nodo de tipo Webhook y llamarlo Información General.

> **Webhook**: es un mecanismo que nos permite enviar notificaciones en tiempo real desde **ElevenLabs** a **n8n**.


<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*fBTjrjH6Yuek9tl1HAmFNQ.png" alt="Configuración del Webhook en n8n">
</p>

Para poder probar este tipo de flujo, debemos hacer clic en el nodo *“Información General”*, copiar la *Test URL* y realizar una petición GET desde Postman o cualquier cliente de tu preferencia. En esta caso estaré usando [Insomnia](https://insomnia.rest/download).

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*LE88AvJxNSYAmySjkV39zQ.png" alt="URL de prueba del Webhook en n8n">
</p>

Si ejecutamos el flujo y realizamos una petición a *Test URL* obtendremos la siguiente información en Insomnia, lo que confirma que hemos configurado correctamente el **Webhook**.

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:828/format:webp/1*akSzqAgN7z4PE5Z7dTIKWw.png" alt="Respuesta del Webhook en Insomnia">
</p>

También debemos agregar dos nodos de tipo *“Google Sheets”* con la operación *“Get Rows”* y conectarlos a nuestro **Webhook**. Estos nodos estarán orientados a extraer la información de la hoja de cálculo que creamos previamente en Google Sheets.

Comenzamos agregando el primer nodo, llamado **“General Info”**, que traerá la información general de la primer pestaña de nuestra hoja (Información).

La primera vez que agregamos un nodo de tipo ‘Google Sheets’, debemos configurar las credenciales. Para ello, seleccionamos: 

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*7a7nRdZFwEky_YLcgVSeDA.png" alt="Configuración de credenciales de Google Sheets en n8n">
</p>

Si la cuenta se conectó correctamente obtendremos el siguiente mensaje:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*Fxho27W4QopwbQbNHfUVfA.png" alt="Configuración de credenciales de Google Sheets en n8n">
</p>

Luego de que configuramos las credenciales, podemos terminar de agregar los parámetros de este primer nodo **“General Info”**:

- **Document**: corresponde al nombre de la hoja de calculo que creamos en Google Sheets.
- **Sheet**: corresponde al nombre de la pestaña a la cual queremos acceder.

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*xJkf7wlsvzlMzq5vFJsT3g.png" alt="Configuración del nodo General Info en n8n">
</p>


Repetimos el mismo paso anterior, conectando un segundo nodo del mismo tipo, llamado **“Lista de productos”**, al **Webhook**, pero esta vez con los siguientes parámetros:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*rT1sDU3IUfmXQxa0dx7gYg.png" alt="Configuración del nodo Lista de productos en n8n">
</p>

Una vez hemos agregado estos dos nodos, debería verse así nuestro flujo:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*FqnZG2bb7vLONXNqQC-NQw.png" alt="Flujo de trabajo en n8n con Webhook y Google Sheets">
</p>    

A continuación, vamos a realizar los siguientes pasos para finalizar este flujo, el cual permite obtener la información general de la tienda, así como los productos disponibles.

**Paso 1:**

Conectamos a cada nodo de tipo “Google Sheets” un nodo de tipo “Code”. A cada uno de estos nuevos nodos los llamaremos **“Code Información”** y **“Code Productos”**, respectivamente. Estos nodos serán los encargados de procesar la información proveniente de la hoja de cálculo.

*Code Información:*

```javascript
const adress = $input.first().json["Dirrección"];
const available = $input.first().json.Disponibilidad;
const schedule = $input.first().json['Horatio de atención']

return [{
  json: {
    "información": {
      "disponibilidad": available,
      "direccion": adress,
      "horario": schedule,
      "hoy": $today.weekdayLong
    }
  }
}];
```

<p>
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*ttixHETk4PoBpsRD36RSVw.png" alt="Configuración del nodo Code Información en n8n">
</p>

Como podemos ver, esta parte de código estará encargada de extraer la información proveniente de cada columna de la hoja **“Información” **y la estructura en un formato JSON para su procesamiento.

*Code Productos:*

Aquí se extraerán los productos de las filas de la hoja **“Productos”** y se exportarán en formato JSON:

```javascript
const inputs = $input.all();

const productos = inputs.map(item => ({
  categoria: item.json["Categoría"],
  nombre: item.json["Producto"],
  descripcion: item.json["Descripción"],
  precio: item.json["Precio"],
  disponible: item.json["Disponibilidad"]
}));

return [{
  json: {
    productos
  }
}];
```

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*Qpp_adTbkuT6NGkLnGREcw.png" alt="Configuración del nodo Code Productos en n8n">
</p>

Al finalizar el paso 1 usted debería tener algo como:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*_N0NMHXpBsvtJx8vxFf3fA.png" alt="Flujo de trabajo en n8n con nodos Code">
</p>

**Paso 2:**

Ahora la información general de la tienda se encuentra en dos nodos diferentes. Para solucionar este tipo de problemas **n8n** dispone de un nodo de tipo Merge el cual recibe dos entradas y las combina. Conectamos las dos entradas de Google Sheets y configuramos el nodo “Merge” con los siguientes parámetros:


<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*FcNEkWdu2BNmMVOtNWiLWA.png" alt="Configuración del nodo Merge en n8n">
</p>

Al finalizar este paso usted debería tener algo como:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*PSXMvUaKXY-jXb5bF2OR1Q.png" alt="Flujo de trabajo en n8n con nodo Merge">
</p>

**Paso 3:**

Una vez hemos completado todo el flujo de procesamiento de información podemos pasar a retornarla, para ello agregamos un nuevo nodo conectado al **“Merge”** de tipo **“Respond to Webhook”** con los siguientes parámetros:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*oAIpPtlcI-r5AORocENKIw.png" alt="Configuración del nodo Respond to Webhook en n8n">
</p>

En el primer nodo **“Información General”** debemos asegurarnos de que el parámetro *Respond* tenga el valor *Using ‘Respond to Webhook*’ Node, que permite que la información general del restaurante se retorne como respuesta al llamado del **Webhook**:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:640/format:webp/1*rLuuTm6e53m0WUu09q_Fhw.png" alt="Configuración del nodo Información General en n8n">
</p>

Al finalizar este paso usted debería tener algo como:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*hIGuWv1Bu6JNhhViFPOp2A.png" alt="Flujo de trabajo completo en n8n con Webhook, Google Sheets y nodos de procesamiento">
</p>

Ya hemos completado este primer flujo, el cual se encarga de retornar la información general del restaurante. Ahora podemos ejecutarlo y realizar una petición desde Insomnia, si todo esta configurado correctamente debería obtener:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:828/format:webp/1*mrOWttuyrFyshW2suat8og.png" alt="Respuesta del flujo de n8n en Insomnia">
</p>

### **ElevenLabs: Definición de una herramienta para nuestro agente de voz**

Con la creación del flujo en **n8n**, encargado de retornar la información actual del restaurante registrada en Google Sheets, podemos proceder a agregar este Webhook como una herramienta para nuestro agente de voz en ElevenLabs.

Antes de agregar la herramienta, debemos activar nuestro flujo en n8n como se indica a continuación:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*13fhrKYlredJCLoLpG4gGw.png" alt="Activación del flujo en n8n">
</p>

Con el flujo activado procedemos a seleccionar nuestro nodo de entrada y copiamos la URL de producción del **Webhook** que definimos anteriormente:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*v4os4z2K-wP_qxHe5a-8zg.png" alt="URL de producción del Webhook en n8n">
</p>

Finalmente, agregamos la herramienta en ElevenLabs. Para ello nos dirigimos a: Tools > Add tool y registramos los siguientes valores:

> ***Name***: info-restaurante
>
> ***Description***: Esta herramienta se encarga de obtener la información actual del restaurante y retorna campos como la disponibilidad del restaurante (Abierto/Cerrado), el día actual de la semana y los productos con su respectiva información.
>
> ***Method***: GET.
>
> ***URL***: URL de producción de tu Webhook.

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*sG1RibI7vN4fTSEyiPypVQ.png" alt="Configuración de la herramienta info-restaurante en ElevenLabs">
</p>

### **n8n: Postprocesamiento de la llamada entre el agente y el cliente**

Desde **ElevenLabs** tenemos la posibilidad de exportar la transcripción de la llamada utilizando un **Webhook**. Para registrar el pedido, vamos a enviar esta información a un nuevo **Webhook** en **n8n**, el cual estará conectado a un agente encargado de guardar los datos en la hoja *Pedidos* de Google Sheets.

**Paso 1:**

Agregamos un nuevo flujo con un **Webhook** como punto de entrada llamado **“Postprocesamiento”**, esta vez el **Webhook** va a ser de tipo *POST* en el campo HTTP Method, y conectamos un nodo de tipo **Code** llamado **“Obtener conversación”** con el siguiente código JavaScript:

```javascript
const transcript = $input.first().json.body.data.transcript;

const conversation = transcript.map(item => ({
  role: item.role,
  mensaje: item.message
}));

return [{
  json: {
    "conversacion": conversation
  }
}];
```

Al finalizar este paso usted debería tener algo como:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*_6blgTnvr-wHbWIqLhnI5w.png" alt="Flujo de trabajo en n8n con Webhook y nodo Code para obtener conversación">
</p>

**Paso 2:**

En n8n también tenemos la posibilidad de crear un agente. En este caso, crearemos uno que procese el texto presente en la transcripción de la interacción entre el agente de voz y el cliente. Para ello, agregamos un nuevo nodo de tipo *AI Agent* con la siguiente configuración:

*Prompt:*
```markdown
A partir de la siguiente transcripción de un pedido por voz realizado por un agente de toma de pedidos, extrae la siguiente información y guardala en una hoja de calculo:

- Fecha y hora en formato YYYY-MM-DD HH-MM
- Nombre del cliente
- Lista de productos
- Precio final
- Dirección de envio
- Método de pago 

Importante:
Si no es posible obtener información suficiente o confiable para alguno de los campos, no lo registres (déjalo en blanco).

La fecha y hora actual es: {{ $now }}

Transcripción:
{{ JSON.stringify($json.conversacion) }}
```

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*S7NiZwphQSb8j-2C8Sn-Nw.png" alt="Configuración del nodo AI Agent en n8n">
</p>

Al agente debemos agregar un LLM, por lo tanto seleccionamos + en Chat Model y añadimos un nuevo nodo de tipo **“Google Gemini Chat Model”**.

Aquí también debemos configurar una credencial, para obtener la credencial nos dirigimos a [Google AI](https://aistudio.google.com/apikey) Studio y obtenemos la el API Key.

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*VZGI_9TREngt5fQDjtf8tw.png" alt="Obtener API Key de Google AI Studio">
</p>

En n8n seleccionamos dentro del nodo **“Google Gemini Chat Model”**: *Select Credential > Create new Credential* y pegamos el **API Key**. Si todo salió correcto deberíamos obtener el siguiente mensaje:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*-JHkQ5RApZVkJ-dBsmKrIQ.png" alt="Configuración de credenciales de Google Gemini Chat Model en n8n">
</p>

Esta sería la configuración del nodo “Google Gemini Chat Model”:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*hrAWRYlr_g4dI7sApcZgLw.png" alt="Configuración del nodo Google Gemini Chat Model en n8n">
</p>

Igualmente, dentro de la configuración de esta herramienta, debemos activar la opción para que el modelo de lenguaje (LLM) se encargue de registrar cada valor de su respuesta en la columna correspondiente de la hoja *Pedidos*. Para ello, seleccionamos el ícono *Let the model define this parameter* en cada columna.

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*vAkqw1--39GF5lX8Rpd_Wg.png" alt="Configuración de parámetros en Google Gemini Chat Model en n8n">
</p>

Al finalizar este paso usted debería tener algo como:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*aJ3MweA4BuuGCBwlnO5xDg.png" alt="Flujo de trabajo en n8n con Webhook, nodo Code y AI Agent">
</p>

### **ElevenLabs: transferencia de la transcripción a n8n**

En el anterior paso creamos un nuevo flujo que se encarga de procesar la conversación que realizó el agente de voz en ElevenLabs, ahora debemos conectar nuestro agente de voz a este flujo.

Antes de realizar la conexión debemos obtener la URL del Webhook en producción, esto lo hacemos haciendo click en el Webhook *“Postprocesamiento”* y copiando la URL:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*viVycMATtZ64Kal8EdYdHg.png" alt="URL de producción del Webhook Postprocesamiento en n8n">
</p>

Para registrar el Webhook en ElevenLabs nos dirigimos a *Security* en la barra de navegación superior dentro de la configuración de nuestro agente.

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*Hq-GhucMGG9ZshfujtWvkA.png" alt="Configuración de seguridad en ElevenLabs">
</p>

Encontraremos un botón llamado *Create Webhook*, lo seleccionamos y en el modal registramos los siguiente campos:

>**Webhook name:** Postprocesamiento
>
>**Callback URL:** URL del Webhook de Postprocesamiento

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*BZ_oFO7-WXoGu-fiUTjzUQ.png" alt="Configuración del Webhook Postprocesamiento en ElevenLabs">
</p>

> **Importante** ⚠️: Al agregar nuestro **Webhook** de post-call, se genera una llave. En un flujo más avanzado, esta llave debería utilizarse para validar que el mensaje recibido en el **Webhook** de **n8n** proviene efectivamente de nuestro agente de voz. Sin embargo, por motivos de simplicidad, esta validación no se implementa en este caso.

### **Twilio: Alquiler de un número de teléfono**

Para que nuestro agente de voz pueda recibir llamadas reales, es necesario alquilar un número de teléfono. Para alquilar un número de teléfono en Twilio debemos dirigirnos a la consola y en el menu lateral seleccionar: *Phone Number > Buy a number > Buy*.

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*PBAu9VsCfN3iRQ9zpRlFsg.png" alt="Alquiler de número de teléfono en Twilio">
</p>

Una vez hemos alquilado el número de teléfono, debemos ir a [Twilio Summary](https://www.twilio.com/console/projects/summary) , aquí podremos obtener el SID de nuestra cuenta que corresponde a un identificador único:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*BCHIqFe9lvuNSdv82bTaEw.png" alt="SID de cuenta en Twilio">
</p>

En la barra de búsqueda de la consola debemos ingresar el termino **“Auth token”** y seleccionar la primera opción:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*vsM3_GrqIE2brPTIuwBJFw.png" alt="Token de autenticación en Twilio">
</p>

En este nuevo menu, seleccionamos en la barra lateral la opción *API keys & tokens* y en Live credentials copiamos y guardamos nuestro **Auth token**:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*NEgyr7LlBI7kiErqPt4XDg.png" alt="Token de autenticación en Twilio">
</p>

### **ElevenLabs: registro del número de teléfono**

En el dashboard de **ElevenLabs** seleccionan la opción: *Phone number > Import number > From Twilio.*

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*LLTWD8Zf66VQc1GNKZU4oA.png" alt="Registro de número de teléfono en ElevenLabs">
</p>

En el modal registras los campos que anteriormente obtuvimos de Twilio, como se muestra a continuación:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:640/format:webp/1*4A71Z7cTJE1AGRmd9Q3heA.png" alt="Configuración del número de teléfono en ElevenLabs">
</p>

Finalmente asignamos el agente al número de teléfono:

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*MLUp6p7q7WJLRLlp7ciK1w.png" alt="Asignación del agente al número de teléfono en ElevenLabs">
</p>

Una vez finalizada la configuración, nuestro agente ya estará habilitado para tomar pedidos utilizando el número de teléfono. Como estamos utilizando una cuenta *Trial* de **Twilio**, al realizar la llamada escucharemos un mensaje de advertencia. Simplemente, una vez finalizado dicho mensaje, presionamos cualquier tecla para continuar.

## **Resultados**

A continuación, se muestra una demo funcional del comportamiento del agente a través de una llamada real 📞.

[Demo del agente de voz en acción](https://www.youtube.com/watch?v=eCpZTklBWJ0)

## **Conclusiónes**

Este es un primer acercamiento a la creación de una agente de voz que responde llamadas telefónicas utilizando **ElevenLabs**, **n8n** y el **API de Google** para LLMs.

Es importante resaltar que esto es un enfoque inicial, que aun requiere revisiones y ajustes. Por ejemplo, en algunas ocasiones el agente ha registrado pedidos aunque la información no esta completa, lo que evidencia la necesidad se afinar los flujos y prompts.

Además, por temas de costos y consumos de créditos, en esta versión no se están utilizando los modelos más avanzados disponibles en las plataformas, pero se podrían implementar para mejorar la compresión y respuestas del agente.

Aún así la implementación es un buen punto de partida para darse una idea de como funciona este tipo de soluciones, y como se pueden construir experiencias conversacionales automatizadas utilizando **herramientas no-code** y **APIs modernas**.

✅ **¡Espero que esta guía te haya sido de ayuda!**

Si tienes alguna duda, sugerencia o simplemente quieres compartir tu opinión, puedes escribirme a través de un mensaje en mis redes sociales.

¡Hasta pronto! 👋😊
