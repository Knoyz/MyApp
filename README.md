# MyApp - Similar Products Service

## Descripción General

**MyApp** es una solución de backend desarrollada en Java y Spring Boot que actúa como middleware entre clientes y un servicio externo de productos. Expone una API REST reactiva para consultar productos similares y sus detalles, integrando tecnologías modernas como Kafka, Redis y WebClient para lograr alta disponibilidad, escalabilidad y eficiencia en el manejo de datos y eventos.

---

## Arquitectura y Diseño

### Arquitectura General

- **Hexagonal (Ports & Adapters):**  
  El proyecto sigue el patrón de arquitectura hexagonal (también conocido como Ports and Adapters), separando la lógica de negocio de las dependencias externas (REST, Kafka, Redis, WebClient). Esto facilita la mantenibilidad, escalabilidad y testabilidad del sistema.

- **API First:**  
  El diseño y la documentación de la API se realizan antes de la implementación, garantizando contratos claros y consistentes entre equipos y servicios.

- **Reactive Programming con WebFlux:**  
  Se utiliza Spring WebFlux y Project Reactor para construir una API completamente reactiva y no bloqueante, ideal para escenarios de alta concurrencia y baja latencia.

### Componentes Principales

- **API REST:**  
  Expone endpoints para consultar productos similares y sus detalles.
- **Adaptadores de Entrada:**  
  - REST Controller (`ProductsController`)
  - Kafka Consumer (`KafkaConsumerAdapter`)
- **Adaptadores de Salida:**  
  - **WebClient** para comunicación con servicios REST externos (microservicios).
  - Redis para cacheo reactivo.
  - Kafka Producer/Consumer para eventos de cambios en productos.
- **Dominio:**  
  - Modelos de negocio (`ProductDetails`, `ProductDetailsChangedEvent`)
  - Puertos (`SimilarProductsUseCase`, `EventConsumerPort`, etc.)
  - Servicios de aplicación (`SimilarProductsUseCaseService`)
- **Infraestructura:**  
  - Configuración de Kafka, Redis, WebClient, gRPC, etc.
  - Utilidades de serialización/deserialización (`JsonUtils`)

### Patrones y Buenas Prácticas

- **Inyección de Dependencias:**  
  Uso de Spring DI y anotaciones como `@Service`, `@Component`, `@Configuration`.
- **DTOs y Mappers:**  
  Separación entre modelos de dominio y DTOs para desacoplar la lógica interna de la representación externa.
- **Manejo Centralizado de Errores:**  
  `GlobalExceptionHandler` para respuestas consistentes ante errores.
- **Testing:**  
  Pruebas unitarias y de integración con Mockito, JUnit 5 y MockWebServer.
- **Documentación:**  
  Integración con Swagger/OpenAPI para documentación automática de la API.

---

## Estructura del Proyecto

### Descripción de las capas

- **application/service/**  
  Implementa la lógica de negocio y orquesta los casos de uso principales.

- **domain/model/**  
  Define los modelos de dominio y entidades principales.

- **domain/events/**  
  Define los eventos de dominio relevantes para la aplicación.

- **domain/port/in/**  
  Define los puertos de entrada (interfaces de casos de uso y consumidores de eventos).

- **domain/port/out/**  
  Define los puertos de salida (interfaces para cache, servicios externos, publicación de eventos).

- **infrastructure/adapters/in/**  
  Adaptadores de entrada: exponen la API REST y consumen eventos de Kafka.

- **infrastructure/adapters/out/**  
  Adaptadores de salida: interactúan con servicios externos (REST, Redis).

- **infrastructure/config/**  
  Configuración de beans, clientes y dependencias externas.

- **infrastructure/dto/**  
  Objetos de transferencia de datos para la comunicación entre capas.

- **infrastructure/exceptions/**  
  Manejo centralizado de errores y excepciones.

- **infrastructure/mapper/**  
  Conversión entre modelos de dominio, DTOs y eventos.

- **infrastructure/utils/**  
  Utilidades generales (por ejemplo, serialización JSON).

- **proto/**  
  Contratos gRPC preparados para futura integración.

---

## Tecnologías Seleccionadas

| Tecnología         | Motivo de Selección                                                                 |
|--------------------|------------------------------------------------------------------------------------|
| **Java 21**        | Últimas características del lenguaje y soporte a largo plazo.                      |
| **Spring Boot 3**  | Framework robusto, productivo y ampliamente adoptado en la industria.              |
| **Spring WebFlux** | Programación reactiva, ideal para alta concurrencia y eficiencia de recursos.      |
| **API First**      | Contratos claros y mantenibles desde el inicio del desarrollo.                     |
| **Hexagonal**      | Arquitectura desacoplada, mantenible y testeable.                                  |
| **Kafka**          | Mensajería distribuida, desacoplamiento de servicios y procesamiento de eventos.    |
| **Redis**          | Cache distribuido, rápido y reactivo para mejorar el rendimiento de consultas.      |
| **WebClient**      | Cliente HTTP reactivo para comunicación entre microservicios.                      |
| **gRPC**           | Preparado para comunicación eficiente y tipada en el futuro.                       |
| **Project Reactor**| Soporte para flujos reactivos y no bloqueantes.                                    |
| **Lombok**         | Reducción de boilerplate en modelos y servicios.                                   |
| **JUnit 5/Mockito**| Testing moderno, flexible y fácil de mantener.                                     |
| **Swagger/OpenAPI**| Documentación interactiva y estandarizada de la API REST.                          |
| **Docker Compose** | Orquestación sencilla de servicios para desarrollo y pruebas locales.               |

> **Nota sobre la comunicación entre microservicios:**  
> Actualmente, la comunicación entre microservicios se realiza mediante **WebClient** (HTTP REST) ya que el servicio externo solo expone endpoints HTTP. Sin embargo, el proyecto ya incluye las clases `.proto` y dependencias necesarias para migrar a **gRPC** en el futuro, permitiendo una transición sencilla cuando el ecosistema lo permita.

---

## Pasos para levantar el proyecto

### 1. Clonar el repositorio

```bash
git clone https://github.com/Knoyz/MyApp.git
cd MyApp
```

### 2. Levantar dependencias externas con Docker Compose

Asegúrate de tener Docker y Docker Compose instalados.

```bash
docker-compose up -d
```

Esto levantará los servicios de Redis, Kafka y Zookeeper necesarios para la aplicación.

### 3. Construir el proyecto

```bash
./mvnw clean package
```

### 4. Ejecutar la aplicación
Puedes correr la aplicación localmente:


```bash
./mvnw spring-boot:run
```

O bien, construir y ejecutar el contenedor Docker:

```bash 
docker build -t myapp .
docker run --rm -p 5000:5000 --network="host" myapp
```

### 5. Acceder a la API y documentación

API REST: http://localhost:5000
Swagger UI: http://localhost:5000/swagger-ui.html

---

# Aclaraciones.
> Los tests de rendimiento y los servicios externos a los que este servicio se conectan, no son de mi propiedad.
> Añado los links y resultados para que puedan llegar a ellos más fácilmente. https://github.com/dalogax/backendDevTest.git

  scenarios: (100.00%) 5 scenarios, 200 max VUs, 1m30s max duration (incl. graceful stop):
           * normal: 200 looping VUs for 10s (exec: normal)
           * notFound: 200 looping VUs for 10s (exec: notFound, startTime: 10s)
           * error: 200 looping VUs for 10s (exec: error, startTime: 20s)
           * slow: 200 looping VUs for 10s (exec: slow, startTime: 30s, gracefulStop: 10s)
           * verySlow: 200 looping VUs for 10s (exec: verySlow, startTime: 50s, gracefulStop: 30s)


running (1m30.1s), 000/200 VUs, 4854 complete and 1000 interrupted iterations
normal   ✓ [======================================] 200 VUs  10s
notFound ✓ [======================================] 200 VUs  10s
error    ✓ [======================================] 200 VUs  10s
slow     ✓ [======================================] 200 VUs  10s
verySlow ✓ [======================================] 200 VUs  10s

    data_received..............: 10 MB  111 kB/s
    data_sent..................: 632 kB 7.0 kB/s
    http_req_blocked...........: avg=123.26µs min=1.97µs   med=8.79µs   max=17.76ms p(90)=24.3µs  p(95)=146.79µs
    http_req_connecting........: avg=108.99µs min=0s       med=0s       max=17.7ms  p(90)=0s      p(95)=0s      
    http_req_duration..........: avg=609.88ms min=5.53ms   med=69.32ms  max=9.77s   p(90)=3.08s   p(95)=3.54s   
    http_req_receiving.........: avg=1.09ms   min=19.65µs  med=572.25µs max=41.17ms p(90)=2.58ms  p(95)=3.76ms  
    http_req_sending...........: avg=46.45µs  min=7.47µs   med=34.1µs   max=3.22ms  p(90)=68.05µs p(95)=100.15µs
    http_req_tls_handshaking...: avg=0s       min=0s       med=0s       max=0s      p(90)=0s      p(95)=0s      
    http_req_waiting...........: avg=608.74ms min=5.24ms   med=68.26ms  max=9.77s   p(90)=3.08s   p(95)=3.54s   
    http_reqs..................: 5270   58.51767/s
    iteration_duration.........: avg=1.09s    min=506.39ms med=568.91ms max=9.96s   p(90)=3.53s   p(95)=4.01s   
    iterations.................: 4854   53.898439/s
    vus........................: 200    min=200 max=200
    vus_max....................: 200    min=200 max=200




