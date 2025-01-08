---
title: "Monitoreo de Aplicación de Spring Boot: Prometheus y Grafana"
author: "Cristian Perafán"
date: 2025-01-08
description: "Tutorial para monitorear una aplicación Spring Boot con Prometheus y Grafana"
tags:
  - Spring Boot
  - Prometheus
  - Grafana
  - Java
  - Programación
---


# Monitoreo de Aplicación de Spring Boot: Prometheus y Grafana

*En este artículo, te mostraré cómo monitorear una aplicación Spring Boot exponiendo métricas de la aplicación, recopilándolas con Prometheus y visualizándolas en Grafana.*

![Spring Boot Prometheus Grafana](https://miro.medium.com/v2/resize:fit:1400/format:webp/0*Ojd-oBjTfCclGefq.jpg)

## Introducción

Imagina una aplicación crítica con un gran número de usuarios diarios. Es importante comprender el estado de la aplicación, ya que esto puede ayudarte a identificar y abordar de manera proactiva posibles problemas de rendimiento. Sin un monitoreo adecuado, no tendrías visibilidad sobre la salud, las métricas o el estado general de la aplicación. Aquí es donde el monitoreo se vuelve crucial.

**Spring Boot** proporciona un conjunto de herramientas listas para producción que permiten monitorear nuestra aplicación, como **Spring Boot Actuator** y **Micrometer**. **Micrometer** nos ayuda a recopilar métricas de manera sencilla y se integra perfectamente con Actuator.

Una vez que nuestra aplicación esté lista para exponer sus métricas, hay algunas herramientas que pueden ayudarnos a recopilar y mostrar estos datos:

- **Prometheus**: Una herramienta de monitoreo y alertas de código abierto que recopila datos en series temporales desde múltiples fuentes.  
- **Grafana**: Una plataforma de análisis de código abierto que permite a los usuarios monitorear y visualizar datos de diversas fuentes.

## Manos a la obra

### Prerrequisitos

Si deseas seguir este tutorial, necesitarás los siguientes requisitos previos:

- Una aplicación **Spring Boot** ya en ejecución. Si lo prefieres, puedes consultar el siguiente ejemplo de [Spring Boot App](https://github.com/CristianPerafan/Spring-Boot-Monitoring)
- Docker para ejecutar las imágenes de Prometheus y Grafana.

### Configuración de la Aplicación para habilitar el Monitoreo

Necesitas agregar las siguientes dependencias al archivo `pom.xml` de tu aplicación:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
    <version>3.3.2</version>
</dependency>
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
    <version>1.13.6</version>
</dependency>
```

- **Spring Boot Actuator** es un *starter* de Spring Boot que nos permite recopilar información de todos los componentes de nuestras aplicaciones Spring Boot, incluyendo logs, *beans* y trazas HTTP.  
- **Micrometer Prometheus** permite que nuestra aplicación exponga sus métricas de una forma que Prometheus pueda entender y utilizarlas.

Después de agregar estas dependencias, debes especificar en el archivo `application.properties` qué *endpoints* deseas exponer:

```properties
management.metrics.tags.application=${spring.application.name}
management.endpoints.web.exposure.include=info,health,prometheus
```

Una vez que hayas configurado las propiedades de la aplicación y agregado las dependencias, tu aplicación expondrá algunos *endpoints* con estas métricas. Puedes verlos en la siguiente URL: [http://localhost:8080/actuator](http://localhost:8080/actuator)

![Spring Boot Actuator](https://miro.medium.com/v2/resize:fit:828/format:webp/1*EGLGFVwP2m08xcNQP-du-w.png)

Tambié puedes acceder a las métricas de Prometheus en la siguiente URL, [http://localhost:8080/actuator/prometheus](http://localhost:8080/actuator/prometheus)

![Spring Boot Prometheus](https://miro.medium.com/v2/resize:fit:828/format:webp/1*D6Y3DBoXgbbVGLewoTgA6Q.png)

Estas métricas están en un formato que Prometheus puede entender y utilizar.

### Prometheus y Grafana en Docker

Para configurar fácilmente Grafana y Prometheus, utilizaremos Docker Compose para gestionar estas herramientas.

#### Crear un archivo `docker-compose.yml`

Contenido del `docker-compose.yml`:

```yaml
services:
  prometheus:
    image: prom/prometheus:latest
    ports:
      - "9090:9090"
    volumes:
        - ./config/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    environment:
      GF_SECURITY_ADMIN_USER: admin
      GF_SECURITY_ADMIN_PASSWORD: admin
```

### Crear un archivo `prometheus.yml`

Recuerda modificar la configuración del volumen para que apunte a la ruta relativa donde guardaste el archivo 
`prometheus.yml`. En este ejemplo, se encuentra en `./config/prometheus/prometheus.yml`.

![Ruta del archivo prometheus.yml](https://miro.medium.com/v2/resize:fit:750/format:webp/1*Us-B6InNltJzIOZoIiZegw.png)

`Prometheus.yml`:

```yaml
scrape_configs:
  - job_name: 'spring-boot-monitoring-app'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['host.docker.internal:8080']
```

Este archivo define un trabajo (*job*) de Prometheus que apunta a nuestra aplicación Spring Boot ejecutándose en [http://localhost:8080](http://localhost:8080). Este trabajo es responsable de recopilar las métricas en [http://localhost:8080/actuator/prometheus](http://localhost:8080/actuator/prometheus).

Finalmente, para usar Grafana y Prometheus, ejecuta el siguiente comando en el directorio donde se encuentra el archivo `docker-compose.yml`:

```bash
docker compose up
```

Una vez que ejecutes el comando, puedes acceder a Prometheus en [http://localhost:9090/targets](http://localhost:9090/targets). En este endpoint, verás los *targets* configurados en Prometheus. Los *targets* son fuentes de datos que Prometheus puede consultar para recopilar métricas.

![Prometheus Targets](https://miro.medium.com/v2/resize:fit:828/format:webp/1*OWs8CVVZ7ckl8fTnJy5Fnw.png)

Como puedes ver en la última imagen, un *target* está apuntando a nuestro ejemplo de aplicación Spring Boot ejecutándose en [http://localhost:8080](http://localhost:8080), y su estado es "UP".

![Prometheus Metrics](https://miro.medium.com/v2/resize:fit:828/format:webp/1*RHFjvKaawAdABv1OuRAMlw.gif)

### Configuración de Grafana

La primera vez que accedemos a Grafana, necesitamos usar las credenciales definidas en el archivo `docker-compose.yml`. Nuestras credenciales son **admin/admin**. Más adelante, será necesario cambiar la contraseña, pero inicialmente puedes ingresar con la misma.

[http://localhost:3000](http://localhost:3000)

![Grafana Login](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*2eD86rb74Ssxyh-0JEneMA.png)

#### Inicio de sesión en Grafana
En Grafana, puedes configurar diversas fuentes de datos (*datasources*), incluyendo bases de datos de series temporales como Prometheus, bases de datos relacionales, bases de datos de logs y más. Grafana te permite realizar consultas a estas fuentes de datos y extraer información relevante.

En Grafana, puedes configurar diversas fuentes de datos (*datasources*), incluyendo bases de datos de series temporales como Prometheus, bases de datos relacionales, bases de datos de logs y más. Grafana te permite realizar consultas a estas fuentes de datos y extraer información relevante.

#### Agregar Prometheus como fuente de datos en Grafana

1. **Fuentes de datos (Datasources)**: En la barra lateral izquierda, haz clic en **Connections** y luego selecciona **Datasources**.  
2. **Seleccionar Prometheus**: Elige Prometheus de la lista de fuentes de datos disponibles.  
3. **Configurar la fuente de datos**: En el campo **Prometheus server URL**, ingresa `http://prometheus:9090`.  
4. **Guardar y probar**: Finalmente, haz clic en **Save & Test**. Verás un mensaje de éxito si la conexión se establece correctamente.

![Agregar Prometheus a Grafana](https://miro.medium.com/v2/resize:fit:828/format:webp/1*k0pHZshQshSJD8uy5kF1sA.gif)

#### Agregar un panel en Grafana

Es bien conocido que Grafana es una herramienta madura que ya ha sido explorada para muchas soluciones. Por esa razón, podemos encontrar tableros preconstruidos para diferentes aplicaciones en [Grafana Dashboards](https://grafana.com/grafana/dashboards/). Aquí, podemos encontrar tableros para todo tipo de herramientas como Jira, bases de datos como MongoDB y aplicaciones como Spring Boot. En este tutorial, utilizaremos el tablero [Spring Boot 2.1 System Monitor](https://grafana.com/grafana/dashboards/11378-justai-system-monitor/). Podemos descargar este tablero como un archivo JSON e importarlo en Grafana.

### Para importar un tablero en formato JSON en Grafana:

1. **Dashboards**: En la barra lateral izquierda, haz clic en **Dashboards**.  
2. Selecciona **New** y luego haz clic en **Import**.  
3. Sube el archivo JSON del tablero previamente descargado de **Spring Boot 2.1 System Monitor**.  
4. Selecciona la fuente de datos de Prometheus que ya has añadido y, finalmente, haz clic en **Import**.  

![Importar Tablero en Grafana](https://miro.medium.com/v2/resize:fit:828/format:webp/1*X85eFpIvEDu4LAfklTpH4g.gif)


¡Felicidades! 🎉 Este es el final de este artículo. Ahora tienes el conocimiento para monitorear tu aplicación Spring Boot con Grafana y Prometheus. ¡Recuerda seguir explorando estas poderosas herramientas para mejorar tus capacidades de visualización de datos y monitoreo!