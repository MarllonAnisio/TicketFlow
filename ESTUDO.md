# 📚 TicketFlow — Guia de Estudos Mapeado na Árvore do Projeto

> **Como usar este guia**: Cada pasta e módulo do repositório **TicketFlow** exige conhecimentos específicos. Siga este mapa estruturado na árvore de diretórios para saber exatamente **o que estudar, por onde começar e como aplicar** cada conceito diretamente nos arquivos do projeto.

---

## 🌳 Árvore de Aprendizado do Repositório

```
ticketflow/
├── 📄 pom.xml                             ← 1. Parent POM & Maven Multi-Module
├── 📄 docker-compose.yml                  ← 2. Orquestração de Containers & Infraestrutura
├── 📄 Makefile                            ← 3. Automação & Developer Experience
├── 📄 .gitignore                          ← 4. Higiene e Segurança de Repositório
│
├── 📁 libs/                               ← 5. Bibliotecas Compartilhadas (Base do Monorepo)
│   ├── 📁 commons/                        ← 5.1 Exceções Globais, Handler & DTOs Genéricos
│   └── 📁 kafka-events/                   ← 5.2 Contratos de Eventos (Event-Driven Architecture)
│
├── 📁 apps/                               ← 6. Microserviços de Aplicação
│   ├── 📁 eureka-server/                  ← 6.1 Service Discovery (Netflix Eureka)
│   ├── 📁 api-gateway/                    ← 6.2 API Gateway Reativo & Roteamento
│   ├── 📁 event-service/                  ← 6.3 Gestão de Eventos, Venues & Migrations SQL
│   ├── 📁 ticket-service/                 ← 6.4 Core: Locks Distribuídos, Concorrência & Outbox Pattern
│   ├── 📁 payment-service/                ← 6.5 Pagamentos, Saga Pattern & Idempotência
│   ├── 📁 queue-service/                  ← 6.6 Fila Virtual com Redis Sorted Sets
│   └── 📁 notification-service/           ← 6.7 Consumidor Event-Driven & Envio de E-mails
│
├── 📁 infra/                              ← 7. Infraestrutura & Observabilidade
│   ├── 📁 docker/                         ← 7.1 Dockerfiles Multi-Stage
│   └── 📁 observability/                  ← 7.2 Prometheus, Grafana, Loki & Jaeger
│
└── 📁 tests/                              ← 8. Testes de Performance & Carga
    └── 📁 load-tests/                     ← 8.1 Simulações de Carga com Gatling
```

---

## 📖 Mapeamento Detalhado por Módulo

---

### 1. 📄 `pom.xml` (Parent POM)
**Responsabilidade**: Gerenciar versões globais, declarar submódulos e centralizar dependências do monorepo.

* 🧠 **O que estudar**:
  * **Maven Multi-Module Architecture**: diferença entre `<modules>`, `<dependencyManagement>` e `<dependencies>`.
  * **Spring Boot Parent & Spring Cloud BOM**: como importar versões compatíveis de dependências via Bill of Materials.
  * **Annotation Processors**: funcionamento do `maven-compiler-plugin` com Lombok e MapStruct em tempo de compilação.
* 🛠️ **Tecnologias**: Maven, Spring Boot Starter Parent `3.3.2`, Spring Cloud BOM `2023.0.3`.
* ✅ **Check de Conclusão**: Sabe explicar por que as versões ficam no parent POM e como os sub-módulos herdam essas configurações sem duplicar tags `<version>`.

---

### 2. 📄 `docker-compose.yml` & `.env`
**Responsabilidade**: Subir toda a infraestrutura de apoio em ambiente local com um único comando (`PostgreSQL x3`, `Redis`, `Kafka`, `Kafka UI`, `Mailhog`, etc.).

* 🧠 **O que estudar**:
  * **Docker Fundamentals**: diferença entre Imagem, Container, Volumes e Networks.
  * **Docker Compose Syntax**: declaração de `services`, `ports`, `environment`, `depends_on` e `healthcheck`.
  * **Kafka com KRaft Mode**: como o Kafka roda sem dependência de Zookeeper usando `CP-Kafka 7.6+`.
* 🛠️ **Tecnologias**: Docker, Docker Compose, PostgreSQL 16, Redis 7 Alpine, Confluent Kafka, Provectus Kafka UI, Mailhog.
* ✅ **Check de Conclusão**: O comando `docker compose up -d` sobe todos os containers com status `healthy` e os bancos são acessíveis em suas respectivas portas (`5433`, `5434`, `5435`).

---

### 3. 📁 `libs/` — Bibliotecas Compartilhadas

#### 3.1 📁 `libs/commons/`
**Responsabilidade**: Centralizar exceções customizadas de negócio, tratamentos de erro globais e DTOs utilitários.

* 🧠 **O que estudar**:
  * **Hierarquia de Exceções em Java**: diferença entre `Checked` e `Unchecked Exceptions` (`RuntimeException`).
  * **Design Pattern: Template Exception**: criação de `BusinessException`, `NotFoundException` e `ConflictException`.
  * **Bean Validation Specification**: uso de `jakarta.validation` (`@NotNull`, `@NotBlank`, `@Size`).
* 🛠️ **Arquivos Práticos a Criar**:
  * `BusinessException.java` (base para exceções de domínio)
  * `NotFoundException.java` (mapeada para HTTP 404)
  * `ErrorResponse.java` (payload padronizado de erro HTTP)

#### 3.2 📁 `libs/kafka-events/`
**Responsabilidade**: Guardar os registros (Records Java) de todos os eventos trocados via Kafka entre os microserviços.

* 🧠 **O que estudar**:
  * **Java Records (Java 14+)**: imutabilidade, construtores compactos e sintaxe limpa de dados.
  * **Event-Driven Architecture (EDA)**: contratos de eventos, semântica de tópicos e evolução de schemas.
  * **Jackson JSON Serialization**: serialização/desserialização de tipos modernos como `LocalDateTime` e `UUID`.
* 🛠️ **Arquivos Práticos a Criar**:
  * `TicketReservedEvent.java` (record com `reservationCode`, `userId`, `totalPrice`, `expiresAt`)
  * `PaymentCompletedEvent.java` (record com `paymentId`, `reservationCode`, `status`)
  * `SessionOpenedEvent.java` (record com `sessionId`, `eventId`, `sectors`)

---

### 4. 📁 `apps/` — Microserviços do Sistema

#### 4.1 📁 `apps/eureka-server/` (`:8761`)
**Responsabilidade**: Servidor de Service Discovery (registro e localização dinâmicos de serviços).

* 🧠 **O que estudar**:
  * **Service Registry Pattern**: por que microserviços não devem usar IPs/URLs estáticas em produção.
  * **Spring Cloud Netflix Eureka**: anotação `@EnableEurekaServer` e ciclo de vida de heartbeat/eviction.
* 🛠️ **Configuração Chave (`application.yaml`)**:
  ```yaml
  server:
    port: 8761
  eureka:
    client:
      register-with-eureka: false
      fetch-registry: false
  ```

#### 4.2 📁 `apps/api-gateway/` (`:8080`)
**Responsabilidade**: Ponto de entrada único HTTP (Reverse Proxy), roteamento reativo e filtro de requisições.

* 🧠 **O que estudar**:
  * **API Gateway Pattern**: centralização de CORS, autenticação, roteamento e rate-limiting.
  * **Spring Cloud Gateway Reativo**: uso de **Project Reactor** (Netty) ao invés do Spring MVC tradicional.
  * **Predicates & Filters**: mapear `/api/v1/events/**` para o serviço `event-service` via nomenclatura Eureka (`lb://EVENT-SERVICE`).
* 🛠️ **Anotações & Classes**: `@SpringBootApplication`, `RouteLocatorBuilder`.

#### 4.3 📁 `apps/event-service/` (`:8081`)
**Responsabilidade**: CRUD de eventos, locais (venues), sessões e setores. Publicação de abertura de vendas.

* 🧠 **O que estudar**:
  * **Database Migrations com Flyway**: escrita de scripts SQL versionados (`V1__create_venues.sql`, `V2__create_events.sql`).
  * **Spring Data JPA avançado**: relacionamentos `@OneToMany`, `@ManyToOne`, `@Enumerated(EnumType.STRING)` e enums.
  * **MapStruct**: geração de mappers `EventMapper.java` para conversão de `Entity ↔ DTO` sem código legado.
  * **Kafka Producer**: injeção de `KafkaTemplate<String, Object>` para notificar abertura de vendas no tópico `ticketflow.session.opened`.

#### 4.4 📁 `apps/ticket-service/` (`:8082`) ⭐ (CORE CRÍTICO)
**Responsabilidade**: Controle de inventário, reservas de ingressos, prevenção de overselling, expiração automática e Outbox Pattern.

* 🧠 **O que estudar (CONCEITOS MAIS IMPORTANTES DO PROJETO)**:
  * **Controle de Concorrência (Race Conditions)**:
    * **Optimistic Locking**: anotação `@Version` do JPA na entidade `TicketInventory` para detectar atualizações conflitantes.
    * **Distributed Locks com Redis (Redisson)**: uso de `RLock` para travar o par `sessionId + sectorId` durante a reserva.
  * **Outbox Pattern**:
    * Salvar o evento na tabela `outbox_events` **na mesma transação relacional** (`@Transactional`) da reserva.
    * Criar um job agendado (`@Scheduled`) para ler a outbox e disparar para o Kafka, garantindo at-least-once delivery.
  * **Agendamento de Tarefas (`@Scheduled`)**:
    * Task de expiração: buscar reservas não pagas a cada 60s (`expires_at < NOW()`) e devolver os ingressos ao estoque.
* 🛠️ **Tecnologias**: Spring Data JPA, Redis, Redisson Client, Spring Kafka, Outbox Scheduler.

#### 4.5 📁 `apps/payment-service/` (`:8083`)
**Responsabilidade**: Processamento de pagamentos simulados, garantia de idempotência e orquestração de Saga.

* 🧠 **O que estudar**:
  * **Saga Pattern (Coreografia)**: reagir ao evento `ticket.reserved`, processar o pagamento e emitir `payment.completed` ou `payment.failed`.
  * **Idempotência de APIs**: uso de header `Idempotency-Key` e verificação no banco antes de autorizar a transação para evitar cobrança dupla.
  * **Resilience4j Retry & Circuit Breaker**: resiliência na integração com gateway externo de pagamento simulado.

#### 4.6 📁 `apps/queue-service/` (`:8084`)
**Responsabilidade**: Fila virtual para eventos de altíssima demanda (Flash Sales) controlando a taxa de acesso ao `ticket-service`.

* 🧠 **O que estudar**:
  * **Redis Sorted Sets (ZSET)**: uso de `ZADD` com score de timestamp para ordenar a fila por ordem de chegada.
  * **Rate Limiting & Token Bucket**: liberação agendada de N usuários por segundo fornecendo um `accessToken` assinado de curta duração (TTL 5min).

#### 4.7 📁 `apps/notification-service/` (`:8085`)
**Responsabilidade**: Envio de notificações e e-mails de confirmação totalmente orientados a eventos.

* 🧠 **O que estudar**:
  * **Kafka Consumer Groups**: anotação `@KafkaListener` escutando múltiplos tópicos (`ticket.confirmed`, `payment.receipt`).
  * **Spring Boot Mail & Thymeleaf**: renderização de templates HTML dinâmicos (`ticket-confirmed.html`) e envio via SMTP (Mailhog).

---

### 5. 📁 `infra/` — Observabilidade & Infraestrutura

#### 5.1 📁 `infra/observability/`
* 🧠 **O que estudar**:
  * **Métricas com Prometheus**: raspagem de dados no endpoint `/actuator/prometheus` via Micrometer.
  * **Dashboards Grafana**: visualização gráfica de vazão (RPS), latência (p95, p99) e tamanho das filas do Redis.
  * **Centralização de Logs com Loki**: envio de logs estruturados em formato JSON usando `loki-logback-appender`.
  * **Tracing Distribuído com Jaeger & OpenTelemetry**: acompanhamento da jornada de um request via `traceId` único passando por Gateway → Ticket → Payment → Kafka.

---

### 6. 📁 `tests/load-tests/` — Testes de Carga com Gatling

* 🧠 **O que estudar**:
  * **Engenharia de Performance**: conceito de Throughput, Latência, P99, Ramp-up e Stress Testing.
  * **Gatling Framework**: escrita de cenários de teste de carga simulando 1.000 a 10.000 usuários concorrentes tentando comprar o mesmo ingresso durante uma venda flash.

---

## 🎯 Roteiro Prático de Início (Passo a Passo)

1. **Passo 1 (Hoje)**: Crie o arquivo `docker-compose.yml` contendo PostgreSQL (`event-db`, `ticket-db`, `payment-db`), Redis e Kafka.
2. **Passo 2**: Implemente as classes da `libs/commons` e `libs/kafka-events`.
3. **Passo 3**: Configure o `eureka-server` e suba o painel em `http://localhost:8761`.
4. **Passo 4**: Desenvolva o `event-service` com Flyway Migrations e teste a publicação no Kafka UI.
5. **Passo 5**: Desenvolva o `ticket-service` focando na reserva com Redisson e `@Version`.
