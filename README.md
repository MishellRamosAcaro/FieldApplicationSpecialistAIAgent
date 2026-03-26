<p align="center">
  <img src="images/logo.png" alt="FAS AI Agent Logo" width="200" />
</p>

# Field Application Specialist AI Agent

En este README.md se presenta el proyecto asociado al Trabajo de Fin del Máster (TFM) en Desarrollo con Inteligencia Artificial de la escuela Big School.

El objetivo de este proyecto es crear un Field Application Specialist AI Agent que pueda apoyar a los Field Application Specialists (FAS) en su trabajo, ayudándoles a resolver los problemas que se les planteen sobre los documentos de los sistemas que utilizan. Se ha desarrollado aprendiendo y aplicando distintos lenguajes de programación, frameworks, diseños, arquitecturas y estructuras que, en conjunto, forman el agente de inteligencia artificial.

La pregunta que seguramente se plantean es: **¿Qué hace un Field Application Specialist (FAS)?**

Un **Field Application Specialist (FAS)** es un profesional que se encarga de la aplicación de soluciones técnicas. Estas soluciones técnicas comprenden:

- La **preparación y puesta a punto de los sistemas**.  
- El **entrenamiento del personal** para la correcta manipulación de los sistemas.  
- El **soporte técnico**, tanto *in situ* como remoto, para **troubleshooting**.  
- La **garantía de satisfacción del cliente** mediante el correcto funcionamiento de los sistemas.

Es importante aclarar que el proyecto se centra en **entrenar nuestro Field Application Specialist AI Agent en el ámbito de Life Science**, que engloban disciplinas como Biología, Biotecnología, Bioquímica, Microbiología etc, para que pueda ayudar a los FAS en su trabajo.

Se establece como un SaaS (Software as a Service) porque es una aplicación web que se puede acceder desde cualquier dispositivo con conexión a internet y que se puede utilizar sin necesidad de instalar nada en el dispositivo.

## Repositorios de código

Este proyecto se compone de dos repositorios:
   - [Website3.0](https://github.com/mishellramos/Website3.0)
   - [Atlas](https://github.com/mishellramos/Atlas)

En cada uno de los mismos se encuentra un fichero README.md que incluye la documentación completa y detallada con todos los puntos de la memoria del TFM que desde la escuela nos han propuesto.

   - Descripción general del repositorio.
   - Stack tecnológico utilizado.
   - Información sobre su instalación y ejecución.
   - Estructura del proyecto.
   - Funcionalidades principales.

---

## Objetivos del Proyecto

El objetivo principal del proyecto es **dar soporte al FAS** mediante:

1. **Extracción de conocimiento técnico** de manuales y protocolos utilizando parcialmente modelos de inteligencia artificial (**LLMs**).  

2. **Extracción de keywords por sección** de cada protocolo para optimizar las búsquedas y mejorar los resultados.  

3. **Optimización y simplificación de archivos PDF** (y en el futuro **TXT, DOCX, etc.**) para eliminar información irrelevante en los documentos asociados.  

4. **Clasificación y estandarización de documentos** mediante secciones que se transformen en **chunks**, lo que permitirá, en el futuro, crear la **base de datos de vectores de un sistema RAG**.

## Alcance del Proyecto

El proyecto realizado es un **Producto Mínimo Viable (MVP)** centrado en la **extracción, simplificación y clasificación del conocimiento técnico de manuales y protocolos** mediante el uso de **LLMs**.  

El siguiente paso será la creación del **RAG**, que se implementará en otra fase del proyecto y no forma parte de este **TFM**.

## Despliegue del proyecto en producción

### Introducción

En cada uno de los repositorio Website3.0 y Atlas existe un apartado sobre instalación y ejecución en local (Ver sección 3. Instalación y ejecución) orientado al desarrollo de código localmente.

Se ha seleccionado **Google Cloud Platform (GCP)** como proveedor de infraestructura, desplegando la solución sobre una **máquina virtual** que actúa como entorno de ejecución aislado. El motivo principal es la capa gratuita de 300 USD / 90 días que ofrece GCP para el uso de sus servicios. Gracias a esto, se ha podido desplegar la solución en producción de forma gratuita.

En el momento de la redacción de este documento **(25/03/2026)**, el crédito disponible dentro del plan gratuito se encuentra próximo a finalizar **(6 días restantes)**, por lo que el despliegue debe considerarse temporal y orientado a la validación del TFM.  

Puede ocurrir que, en el momento de la corrección de este trabajo, no sea posible acceder a la IP pública para verificar el despliegue. No obstante, a lo largo del documento se aportarán evidencias que demuestran la correcta implementación y funcionamiento del sistema.

Si fuese necesaria una comprobación adicional por parte del profesor encargado de la corrección, puede ponerse en contacto conmigo a través del correo electrónico **`[nombre]`mishellramosacaro@gmail.com**, y me encargaré, en un corto intervalo de tiempo, de restaurar el entorno de producción, permitiendo nuevamente el acceso a la IP pública del servidor.

> Nota: Sustituir `[nombre]` por el nombre antes del punto del correo electrónico asociado a la cuenta utilizada para el acceso a la escuela.

### Decisiones técnicas

- Se ha decidido utilizar una maquina virtual para el despliegue de la solución, ya que es la opción más sencilla y económica para el MVP. En el futuro se podría utilizar un servicio de contenedores como Google Cloud Run por cada uno de los servicios de la aplicación.

- Se ha decidido utilizar Docker Compose para el despliegue de la solución, ya que es la herramienta más sencilla para el despliegue de contenedores y la orquestación de los mismos.


### Servicios desplegados en producción

Para poder realizar el despliegue en producción, se instaló previamente **Docker** y **Docker Compose** en el servidor. (Ver [Docker](https://docs.docker.com/get-docker/) y [Docker Compose](https://docs.docker.com/compose/install/)).

También es necesario clonar cada uno de los repositorios mediante `git clone <url_del_repositorio>` , ya que, cada archivo Dockerfile que se encuentra en el mismo, copia el código en cada contenedor junto con las variables de entorno requeridas para la correcta creación y ejecución de los contenedores
Para mayor facilidad en el despliegue, se ha creado un script (code.sh) en la raíz de este repositorio que se encarga de clonar los repositorios.

Los servicios desplegados son los siguientes:

| Servicio   | Descripción                    | Puerto | Imagen/Build              |
|-----------|--------------------------------|--------|---------------------------|
| **nginx** | Website3.0 (Frontend)   | 80  | Build desde `./Website3.0` |
| **fastapi** | Atlas (Backend) | 8000 | Build desde `./Atlas`     |
| **postgres** | PostgreSQL (BBDD)  | 5432   | `postgres:16.10-alpine`   |
| **dbeaver** | CloudBeaver (Gestor de BBDD)| 8978 | `dbeaver/cloudbeaver:25.2.0` |

### Variables de entorno

Aparte de las variables de entorno definidas en el archivo .env.example de cada repositorio, se tienen que definir las siguientes variables de entorno para el correcto funcionamiento de la aplicación:

```env
# DBeaver / CloudBeaver
DBEAVER_SERVER_NAME="[nombre_del_servidor]"
DBEAVER_ADMIN_NAME="[nombre_del_administrador]"
DBEAVER_ADMIN_PASSWORD="[contraseña_del_administrador]"

# PostgreSQL
POSTGRES_PASSWORD="[contraseña_de_la_base_de_datos]"
POSTGRES_USER="[nombre_del_usuario]"
POSTGRES_DB="[nombre_de_la_base_de_datos]"

# Zona horaria (común a todos los servicios)
TZ="[zona_horaria]"

# Frontend: URL base de la API (usada en build del frontend)
VITE_API_BASE_URL="http://[ip_del_servidor]:[puerto_de_la_api]"
```

### Configuraciones de la máquina virtual en GCP
A continuación se muestran las configuraciones de la máquina virtual en GCP, que se han realizado con e objetivo de facilitar el despliegue de la aplicación en producción.

Se ha creado una máquina virtual con las siguientes configuraciones:

- Sistema operativo: Ubuntu 24.04 LTS
- Tipo de máquina: e2-micro
- Zona horaria: Europe/Madrid
- Disco: 10GB
- Firewall: Reglas de entrada y salida

![Configuración de la VM](/images/VM-data.png)

*Figura 1. Detalles de la máquina virtual utilizada como entorno de despliegue en GCP.*

También se muestran las versiones instaladas de docker y docker compose en el servidor, que se ha obtenido mediante el comando `docker version` y `docker compose version`.

![Versiones de docker y docker compose](/images/dockers-version.png)

*Figura 2. Versiones de docker y docker compose en el servidor.*

Por último, se muestra la estructura de las carpetas en el servidor, donde se encuentra el código de los repositorios Website3.0 y Atlas, la ddbb, los datos(data: pdf y jsons extraidos) y la variables de entorno definidas en el archivo .env.

![Estructura del entorno de producción](/images/ls-server.png)

*Figura 3. Estructura de las carpetas en el servidor.*

Se muestra por último un extracto del comando docker compose ps -a, que se ha obtenido mediante el comando `docker compose ps -a`.

![Extracto del comando docker compose ps -a](/images/docke-ps-command.png)

*Figura 4. Extracto del comando docker compose ps -a.*

En la figura 4 se puede ver que los contenedores están en estado **Up (healthy)**, lo que significa que están funcionando correctamente. No esta levantado el contenedor **dbeaver**, ya que, no es necesario para el correcto funcionamiento de la aplicación, solo para verificación de errores en la ddbb.

### URL de acceso a la aplicación

La URL de acceso a la aplicación es **http://34.140.134.1/**, que se puede ver en la figura 5.

![Vista de inicio de la aplicación](/images/home-fasai.png)
*Figura 5. Vista de inicio de la aplicación.*

![Vista de analisis de archivo en la aplicación](/images/analysis-fasai.png)
*Figura 6. Vista de analisis de archivos en la aplicación.*
  
> Como se ve en la imagen 5, la URL de acceso a la aplicación no es segura, ya que, no se ha configurado el certificado SSL, por lo que, se debe acceder a la aplicación mediante http en lugar de https. Esta configuración se realizará más adelante en el proyecto. Ya que se debe comprar el dominio fas-agent.com y configurar el certificado SSL en el servidor (nginx) asociado a este dominio.

## Video de demostración

En la carpeta **videos** de este repositorio se encuentra el video de demostración de la funcionalidad principal de la aplicación puesta en producción, que es la extracción, simplificación y clasificación de archivos. Es el mismo video que en la sección **View Our Agent** del home page de la aplicación. 

Se tiene que descargar el video ya que desde Github no se puede verlo. También estará presente en la slides de la presentación del TFM.

![Video de demostración](/videos/fas-agent-demostration.mp4)
*Figura 7. Video de demostración.*

También se puede ver el video en YouTube:
[Video de demostración](https://youtu.be/3s490JfwWpo)

## Visualización del resultado de un protocolo extraido

En la carpeta **data** de Atlas se encuentra el resultado de la extracción, simplificación y clasificación de archivos, y ya que esta parte no se muestra a los usuarios de la aplicación, se ha incluido una imagen de una extracción de un protocolo Geneplus 1021:

![Protocolo Geneplus 1021](/images/protocol-part1.png)
*Figura 8. Protocolo Geneplus 1021 parte 1.*

![Protocolo Geneplus 1021](/images/protocol-part2.png)
*Figura 9. Protocolo Geneplus 1021 parte 2.*

Como se puede ver en la imagen 8, se ha extraido la información de cabercera, se ha simplificado y se ha clasificado en secciones y se han obtenido las keywords de cada sección.

## Conclusión

En este documento se ha presentado el proyecto asociado al TFM en Desarrollo con Inteligencia Artificial de la escuela Big School.

Se ha explicado el objetivo del proyecto, el alcance del mismo, las decisiones técnicas, los servicios desplegados en producción, las variables de entorno, las configuraciones de la máquina virtual en GCP, la URL de acceso a la aplicación, el video de demostración, la visualización del resultado de la extracción, simplificación y clasificación de un protocolo y la conclusión.

Se ha proporcionado evidencias de la correcta implementación y funcionamiento de la aplicación en producción, que se puede verificar accediendo a la URL de acceso a la aplicación.

Agradecimientos a la escuela Big School por haber proporcionado los conocimientos necesarios para el desarrollo de este proyecto.
