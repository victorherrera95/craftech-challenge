# Prueba Técnica AWS - Craftech 🚀

Este repositorio contiene la solución completa para la prueba técnica que abarca tres áreas principales: **Arquitectura en AWS**, **Despliegue Dockerizado** y **CI/CD**. A continuación, se describen cada uno de estos puntos.

---

## Tabla de Contenidos

1. [Diagrama de Red en AWS 🌐](#1-diagrama-de-red-en-aws-)
   - [Componentes del Diagrama](#componentes-del-diagrama)
   - [Flujo de Solicitudes](#flujo-de-solicitudes)

2. [Despliegue Dockerizado 🐳](#2-despliegue-dockerizado-)
   - [Estructura del Proyecto](#estructura-del-proyecto)
   - [Componentes Dockerizados](#componentes-dockerizados)
   - [Despliegue Local 💻](#despliegue-local-)
   - [Despliegue en AWS ☁️](#despliegue-en-aws)

3. [CI/CD ⌛](#3-cicd-)
   - [Pipeline en GitHub Actions](#pipeline-en-github-actions)

4. [Mejoras Futuras](#mejoras-aplicables-para-un-futuro)

---

## 1. Diagrama de Red en AWS 🌐

El diagrama de red refleja la arquitectura de la aplicación diseñada para alta disponibilidad en AWS. Esta solución incluye:

- **VPC (Virtual Private Cloud):** Proporciona aislamiento en la red.
- **Subredes públicas y privadas:** Para una mejor organización y seguridad.
- **Instancias EC2:** Se utilizan para alojar los contenedores de la aplicación frontend y backend.
- **Base de datos (relacional y no relacional):** AWS RDS y AWS DynamoDB.
- **Grupos de seguridad y ACLs:** Para controlar el acceso a los recursos.
- **Conexion con microservicios externos**
- **Amazon S3**: Almacenamiento de archivos estáticos.
- **Amazon CloudFront**: CDN para entregar los archivos estáticos con baja latencia.
- **Elastic Beanstalk**: Gestión de la aplicación backend.
- **API Gateway**: Gestión de solicitudes API y enrutamiento a Lambda o backend.
- **AWS Lambda**: Funciones serverless para tareas específicas.
- **AWS CloudWatch**: Monitoreo, logging y alarmas.
 

### Componentes del Diagrama:

1. **Amazon S3**:
   - Se usa para almacenar los archivos estáticos de la aplicación frontend, como imágenes, CSS, JavaScript, y otros activos estáticos.
   - El acceso a estos archivos se gestiona de manera segura mediante políticas de acceso.

2. **Amazon CloudFront**:
   - Es un servicio de distribución de contenido (CDN) que sirve los archivos estáticos almacenados en S3 a los usuarios finales con baja latencia.
   - CloudFront también puede hacer caching de las respuestas de las API y otros datos que no cambian con frecuencia.

3. **Elastic Beanstalk**:
   - Elastic Beanstalk se utiliza para desplegar y administrar la aplicación backend (por ejemplo, un servidor web con Django, Node.js, o cualquier otra tecnología) de forma completamente gestionada.
   - Elastic Beanstalk se encarga de la administración de la infraestructura, como el balanceo de carga y el escalado automático de las instancias EC2.

4. **API Gateway**:
   - AWS API Gateway proporciona un punto de entrada único para acceder a los servicios backend expuestos por la aplicación. 
   - También gestiona la seguridad, autenticación y la limitación de solicitudes (rate limiting).
   - El API Gateway dirige las solicitudes a las funciones Lambda o a las instancias de backend de Elastic Beanstalk.

5. **AWS Lambda**:
   - Lambda se utiliza para manejar ciertas funciones de negocio o procesamiento de datos en la nube. En este caso, puede ser usado para procesar solicitudes de API Gateway o realizar tareas automáticas como la manipulación de datos o la integración con los microservicios externos.
   - Es un servicio sin servidor, lo que significa que solo se paga por el tiempo de ejecución.

6. **Microservicios Externos**:
   - La aplicación puede interactuar con 2 microservicios externos para obtener o enviar datos.
   - Estos microservicios pueden estar alojados fuera de AWS, y la comunicación puede hacerse a través de solicitudes HTTP o utilizando protocolos estándar como REST o gRPC.
   - Los microservicios externos pueden ser servicios de terceros o API privadas que proporcionan funcionalidad adicional a la aplicación.

7. **CloudWatch**:
   - AWS CloudWatch se utiliza para la recopilación de métricas y logs desde todos los servicios de AWS, incluidas las instancias de Elastic Beanstalk, las funciones Lambda, y el API Gateway.
   - Las métricas de CloudWatch pueden ser usadas para monitorear el rendimiento de la aplicación y activar alarmas si ciertos umbrales se superan (por ejemplo, altas latencias o errores).
   - Los logs pueden ser enviados a CloudWatch Logs para su análisis y depuración.

---

## Flujo de Solicitudes:

1. **Cliente** accede a la aplicación web a través de CloudFront (CDN) para obtener los archivos estáticos, como el frontend. 
   - CloudFront entrega los archivos de S3 con baja latencia.
   
2. Cuando el cliente necesita interactuar con el backend (por ejemplo, realizar una autenticación o consultar datos), envía una solicitud al **API Gateway**.

3. **API Gateway** recibe la solicitud y la redirige:
   - A una función **Lambda** para procesar ciertas operaciones, como cálculos o validación de datos.
   - O bien, a la aplicación backend desplegada en **Elastic Beanstalk**, si es una solicitud que requiere acceso a la base de datos o lógica de negocio más compleja.

4. Si la solicitud implica obtener información de los **microservicios externos**, la Lambda o el backend de Elastic Beanstalk se comunica con esos servicios a través de HTTP o un protocolo apropiado.

5. **CloudWatch** se utiliza para monitorear todos los servicios involucrados en el flujo de trabajo (Elastic Beanstalk, Lambda, API Gateway, etc.). Si algo falla o se exceden ciertos límites, se generan alertas y logs para su análisis posterior.

---

Puedes consultar el diagrama de red en el archivo [Diagrama de red craft challenge](https://github.com/user-attachments/assets/a5ee15bc-f03c-4e2f-a924-a71a0a30a4b6)


---

## 2. Despliegue Dockerizado 🐳

 ## Estructura del proyecto
El proyecto está organizado de la siguiente manera:
- `/backend`: Contiene el código del backend en Django, el Dockerfile y los scripts relacionados.
- `/frontend`: Contiene el código de React.js, el Dockerfile y los scripts necesarios para la construcción del frontend.
- `/nginx`: Contiene la configuración de Nginx para servir el frontend y hacer proxy inverso al backend.
- `docker-compose.yml`: Archivo para orquestar los contenedores de la aplicación.

El despliegue de la aplicación se realiza utilizando **Docker** para garantizar portabilidad, escalabilidad y facilidad de gestión. Se usan tres contenedores separados para el frontend, backend y Nginx. Cada componente se ejecuta de forma independiente utilizando Docker Compose.

### Componentes Dockerizados:

- **Frontend**:
  - Aplicación construida con **React.js**.
  - Durante el desarrollo, el contenedor expone el puerto **3000**.
  - En producción, puede configurarse para exponer el puerto **80** luego de compilar y optimizar la aplicación.

- **Backend**:
  - Aplicación construida con **Django**.
  - Utiliza una base de datos **relacional** (PostgreSQL).
  - El contenedor expone el puerto **8000**.

- **Nginx**:
  - Configurado como un servidor web para servir el frontend y realizar proxy inverso al backend.
  - El contenedor expone el puerto **80**.

### Despliegue Local 💻

Para ejecutar la aplicación localmente en tu máquina, sigue estos pasos:

1. **Clona el repositorio:**

   ```bash
   git clone https://github.com/victorherrera95/craftech-challenge.git
   
2. **Navega al directorio backend:**

    ```bash
     cd craftech-challenge/test/test/devops-interview-ultimate/backend

 3. **Ejecuta los contenedores con Docker Compose:**

    ```bash
     docker-compose up --build -d
    ```

    En tu navegador, podrás acceder a la aplicación colocando http://localhost:80 

  Esto construirá y ejecutará los contenedores en segundo plano. Para detenerlos:

   ```bash
     docker-compose down
```


  

  ## 2.1 Despliegue en AWS ☁️

  ## Para desplegar la aplicación en AWS se utilizan los siguientes recursos:


- **VPC**: Para crear una red privada en la que se colocan los recursos.
- **Grupos de seguridad**: Para gestionar el acceso a las instancias EC2.
- **AWS EC2**: Instancia con Ubuntu que aloja la aplicación Dockerizada.
- **EBS**: Se utiliza para ampliar el volumen de la instancia EC2 y asegurar que el despliegue sea exitoso.
- **Docker**: Utilizado para contenerizar el frontend, el backend y Nginx, cada uno en contenedores orquestados con sus respectivos Dockerfiles.
- **Nginx**: Configurado como proxy inverso entre el frontend y el backend, sirviendo también el archivo `index.html`.
- **VPC**: Configuración de la red para asegurar la seguridad y disponibilidad.

  ## Pasos para el Despliegue en AWS:

  ### 1. **Configurar la VPC**:
   - Creacion de una VPC con subredes públicas y privadas.

  ### 2. **Configurar la Instancia EC2**:
   - Lanzamiento de una instancia EC2 t3.medium con Ubuntu.
   - Configuracion de el grupo de seguridad para permitir acceso SSH y tráfico en los puertos 80 (frontend) y 8000 (backend).
   - Ampliacion el volumen de la instancia EC2 utilizando EBS para disponer del espacio necesario para instalaciones y disponibilidad.

  ### 3. **Instalar Docker y Docker Compose**:

    En la instancia EC2, instalacion de Docker y Docker Compose:

    ```bash
      sudo apt update
      sudo apt install docker.io docker-compose
    ```

  ### 4. **Desplegar la aplicacion**:

  ### Clonar el repositorio en la instancia EC2:

     ```bash
      git clone https://github.com/victorherrera95/craftech-challenge.git
     ```
     2. **Navegar al directorio backend:**

    ```bash
     cd craftech-challenge/test/test/devops-interview-ultimate/backend
    ```
     3. **Ejecutar en el directorio backend:**
   ```bash
     docker-compose up --build
    ```
     4. **Acceder a la aplicacion:**  **(ACTUALMENTE NO DISPONIBLE)**
        La aplicacion ya esta desplegada y se puede probar en el navegador, podras acceder colocando http://18.191.95.73


 ## 3. **CI/CD** :hourglass_flowing_sand:

Se configuró un pipeline en **GitHub Actions** para automatizar el proceso de construcción y despliegue. 🚀

1. 🏗️ Reconstruye la imagen del contenedor.
2. 🚢 Despliega los contenedores actualizados en la instancia **EC2**.

El pipeline realiza los siguientes pasos:

1. **Construcción de la imagen Docker**:
   - Cada vez que se realiza un cambio en la rama `main` en el repositorio, GitHub Actions ejecuta un workflow que construye una nueva imagen Docker para el frontend, backend y Nginx. Esto asegura que siempre se esté trabajando con la versión más reciente del código.
   
2. **Despliegue en AWS**:
   - Una vez que la imagen Docker se construye con éxito, el pipeline despliega automáticamente la nueva imagen en AWS. Los pasos del despliegue incluyen:
     - **Conexión a la instancia EC2** mediante SSH.
     - **Detención de contenedores antiguos** en EC2 con `docker-compose down`.
     - **Construcción y ejecución de contenedores nuevos** en EC2 usando `docker-compose up --build -d`.
     - Esto asegura que la última versión del código esté siempre desplegada en el entorno de producción.

3. **Notificación de éxito o error**:
   - Si el pipeline se ejecuta correctamente, GitHub Actions envía una confirmación de éxito indicando que el despliegue se completó correctamente. En caso de fallos, se notifica el error para facilitar la solución.
   
4. **Ejecución manual (workflow_dispatch)**:
   - Además de las ejecuciones automáticas al hacer push en la rama `main`, el pipeline también permite su ejecución manual desde la interfaz de GitHub Actions. Esto es útil si se desea realizar un despliegue en cualquier momento sin necesidad de realizar un cambio en el código.

**El codigo del pipeline se encuentra en el directorio .github/workflows/main.yml**  

## 4. Mejoras aplicables para un futuro

A pesar de que se seleccionaron pocos recursos en la nube debido al tamaño de la aplicación, se considera continuar trabajando en las siguientes mejoras:

- 🚀 **Automatización del despliegue**: Implementar herramientas como **Terraform** para optimizar los procesos de infraestructura.
- 📊 **Monitoreo y visualización**: Integrar herramientas de monitoreo como **Prometheus** y **Grafana** para obtener datos más detallados sobre el rendimiento de los procesos.
- 🔒 **Seguridad**: Mejorar la seguridad asignando roles adecuados con **IAM** (Identity and Access Management) para gestionar el acceso a los recursos de manera más eficiente.
- 📦 **Contenedores y Orquestación:** Utilizar Kubernetes para gestionar los contenedores de forma eficiente y permitir una escalabilidad aún mayor.



  



  

