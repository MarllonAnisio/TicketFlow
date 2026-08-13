# 📚 TicketFlow — Guia de Estudos Mapeado na Árvore do Projeto

> **Como usar este guia**: Cada pasta e módulo do repositório **TicketFlow** exige conhecimentos específicos. Siga este mapa estruturado na árvore de diretórios para saber exatamente **o que estudar, onde estudar (links, documentações, vídeos) e como estudar (exercícios práticos e desafios)** diretamente nos arquivos do projeto.

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

### 1. 📄 `pom.xml` (Parent POM & Maven Multi-Module)
**Responsabilidade**: Gerenciar versões globais, declarar submódulos e centralizar dependências do monorepo.

* 🧠 **O que estudar**:
  * **Maven Multi-Module Architecture**: diferença entre `<modules>`, `<dependencyManagement>` e `<dependencies>`.
  * **Spring Boot Parent & Spring Cloud BOM**: importação de versões compatíveis via Bill of Materials.
  * **Annotation Processors**: compilador do Maven integrando Lombok e MapStruct.
* 📍 **Onde estudar**:
  * 🇧🇷 [Maven do Básico ao Avançado — DevDojo (YouTube)](https://www.youtube.com/results?search_query=devdojo+maven)
  * 🇺🇸 [Maven Multi-Module Project Guide — Baeldung](https://www.baeldung.com/maven-multi-module)
  * 🇺🇸 [Spring Boot Dependency Management — Official Docs](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems.dependency-management)
* 🧪 **Como estudar (Prática)**:
  1. Abra o `pom.xml` raiz e observe como `<dependencyManagement>` declara a versão do Spring Cloud BOM (`2023.0.3`).
  2. Adicione uma nova propriedade no parent POM e referencie-a em um dos módulos filhos usando `${propriedade}`.
  3. Rode `./mvnw clean compile` na raiz e verifique como todos os módulos compilam em ordem de dependência.
* ✅ **Check de Conclusão**: Sabe explicar por que as versões ficam no parent POM e por que sub-módulos não devem declarar a tag `<version>` nas dependências herdadas.

---

### 2. 📄 `docker-compose.yml` & `.env`
**Responsabilidade**: Subir toda a infraestrutura de apoio em ambiente local (`PostgreSQL x3`, `Redis`, `Kafka`, `Kafka UI`, `Mailhog`, etc.).

* 🧠 **O que estudar**:
  * **Docker Fundamentals**: Imagens vs Containers, mapeamento de portas, volumes e redes isoladas.
  * **Docker Compose Syntax**: declaração de `services`, `ports`, `environment`, `depends_on` e `healthcheck`.
  * **Kafka com KRaft Mode**: execução do Kafka sem Zookeeper (`cp-kafka:7.6+`).
* 📍 **Onde estudar**:
  * 🇧🇷 [Docker e Docker Compose do Zero — Fabricio Veronez (YouTube)](https://www.youtube.com/watch?v=ntbpIfS44Gw)
  * 🇺🇸 [Docker Compose Overview — Docker Official Docs](https://docs.docker.com/compose/)
  * 🇺🇸 [Kafka without Zookeeper (KRaft) Quickstart — Confluent](https://docs.confluent.io/platform/current/kafka/kraft.html)
* 🧪 **Como estudar (Prática)**:
  1. Crie o arquivo `docker-compose.yml` contendo os containers `postgres-event`, `postgres-ticket`, `postgres-payment`, `redis` e `kafka`.
  2. Execute `docker compose up -d` e rode `docker compose ps` para verificar se todos estão com status `healthy`.
  3. Abra o terminal do container do Redis (`docker exec -it ticketflow-redis redis-cli`) e execute os comandos `PING`, `SET` e `GET`.
* ✅ **Check de Conclusão**: O comando `docker compose up -d` sobe todos os containers sem erro e os 3 bancos PostgreSQL aceitam conexões nas portas `5433`, `5434` e `5435`.

---

### 3. 📁 `libs/` — Bibliotecas Compartilhadas

#### 3.1 📁 `libs/commons/`
**Responsabilidade**: Centralizar exceções customizadas de negócio, tratamentos de erro globais e DTOs utilitários.

* 🧠 **O que estudar**:
  * **Hierarquia de Exceções**: diferença entre Checked e Unchecked Exceptions (`RuntimeException`).
  * **Design Pattern: Template Exception**: padronização de respostas de erro HTTP 4xx e 5xx.
  * **Bean Validation Specification**: uso de `jakarta.validation` (`@NotNull`, `@NotBlank`, `@Size`).
* 📍 **Onde estudar**:
  * 🇧🇷 [Tratamento de Exceções com @ControllerAdvice — Michelli Brito (YouTube)](https://www.youtube.com/watch?v=R5D6SwhK6y0)
  * 🇺🇸 [Error Handling for REST with Spring — Baeldung](https://www.baeldung.com/exception-handling-for-rest-with-spring)
  * 🇺🇸 [Java Custom Exceptions — GeeksforGeeks](https://www.geeksforgeeks.org/user-defined-custom-exception-in-java/)
* 开启 **Como estudar (Prática)**:
  1. Crie a classe base `BusinessException` estendendo `RuntimeException`.
  2. Crie `NotFoundException` estendendo `BusinessException`.
  3. Monte a classe `ErrorResponse` (record ou POJO) contendo `timestamp`, `status`, `error` e `message`.
* ✅ **Check de Conclusão**: Módulos como `event-service` conseguem importar `com.ticketflow.commons.exception.BusinessException` sem erros de compilação.

#### 3.2 📁 `libs/kafka-events/`
**Responsabilidade**: Contratos imutáveis de eventos trocados via Kafka entre microserviços.

* 🧠 **O que estudar**:
  * **Java Records**: dados imutáveis com getters implícitos, `equals`, `hashCode` e `toString`.
  * **Event-Driven Architecture (EDA)**: desacoplamento via mensagens assíncronas.
  * **Jackson JSON Serialization**: serialização de Java Records contendo `LocalDateTime` e `UUID`.
* 📍 **Onde estudar**:
  * 🇧🇷 [Java Records na Prática — DevDojo (YouTube)](https://www.youtube.com/watch?v=QZ0v40G4Ssk)
  * 🇺🇸 [Java 14 Records Tutorial — Baeldung](https://www.baeldung.com/java-record-keyword)
  * 🇺🇸 [Event-Driven Architecture Patterns — Martin Fowler](https://martinfowler.com/articles/201701-event-driven.html)
* 🧪 **Como estudar (Prática)**:
  1. Crie os records `TicketReservedEvent`, `PaymentCompletedEvent` e `SessionOpenedEvent`.
  2. Escreva um teste unitário simples instanciando o record e convertendo-o para String JSON com `ObjectMapper`.
* ✅ **Check de Conclusão**: Os eventos são compilados como `jar` reutilizável para produtores e consumidores.

---

### 4. 📁 `apps/` — Microserviços do Sistema

#### 4.1 📁 `apps/eureka-server/` (`:8761`)
**Responsabilidade**: Servidor de Service Discovery.

* 🧠 **O que estudar**: Service Registry Pattern, anotação `@EnableEurekaServer`, Heartbeats e Self-Preservation Mode.
* 📍 **Onde estudar**:
  * 🇧🇷 [Spring Cloud Eureka Service Discovery — Giuliana Bezerra (YouTube)](https://www.youtube.com/watch?v=sFhsEq2pHOI)
  * 🇺🇸 [Service Discovery with Spring Cloud Netflix Eureka — Baeldung](https://www.baeldung.com/spring-cloud-netflix-eureka)
* 🧪 **Como estudar (Prática)**:
  1. No `application.yaml`, defina `server.port: 8761`.
  2. Inicie a aplicação com `./mvnw spring-boot:run -pl apps/eureka-server`.
  3. Abra `http://localhost:8761` no navegador e confirme a exibição do dashboard do Eureka.
* ✅ **Check de Conclusão**: Dashboard acessível e pronto para registrar clientes.

#### 4.2 📁 `apps/api-gateway/` (`:8080`)
**Responsabilidade**: Reverse Proxy, roteamento reativo e ponto de entrada único.

* 🧠 **O que estudar**: API Gateway Pattern, Spring Cloud Gateway Reativo (Project Reactor / Netty), Predicates e Filters.
* 📍 **Onde estudar**:
  * 🇧🇷 [Spring Cloud Gateway — Michelli Brito (YouTube)](https://www.youtube.com/watch?v=bRnkSIMrPxM)
  * 🇺🇸 [Spring Cloud Gateway Official Reference Guide](https://docs.spring.io/spring-cloud-gateway/reference/)
* 🧪 **Como estudar (Prática)**:
  1. Configure rotas dinâmicas no `application.yaml` usando `lb://EVENT-SERVICE`.
  2. Faça uma requisição para `http://localhost:8080/api/v1/events` e observe o Gateway redirecionar para a porta do `event-service`.
* ✅ **Check de Conclusão**: O Gateway se registra no Eureka e redireciona chamadas com sucesso.

#### 4.3 📁 `apps/event-service/` (`:8081`)
**Responsabilidade**: CRUD de eventos, locais, sessões e setores.

* 🧠 **O que estudar**: Flyway SQL Migrations, JPA Avançado (`@OneToMany`, `@ManyToOne`), MapStruct, Spring Kafka Producer.
* 📍 **Onde estudar**:
  * 🇧🇷 [Flyway Migrations com Spring Boot — Fernanda Kipper (YouTube)](https://www.youtube.com/watch?v=dPH0VXxwxEw)
  * 🇺🇸 [Quick Guide to MapStruct — Baeldung](https://www.baeldung.com/mapstruct)
  * 🇺🇸 [Spring Kafka Producer Configuration — Baeldung](https://www.baeldung.com/spring-kafka)
* 🧪 **Como estudar (Prática)**:
  1. Crie os arquivos SQL `V1__create_venues.sql` e `V2__create_events.sql` em `src/main/resources/db/migration`.
  2. Implemente o `EventController` e teste o endpoint `POST /api/v1/events`.
  3. Publique um evento no Kafka ao abrir uma sessão (`ticketflow.session.opened`) e confirme no Kafka UI (`http://localhost:8090`).
* ✅ **Check de Conclusão**: O banco de dados é migrado pelo Flyway e os eventos chegam ao Kafka.

#### 4.4 📁 `apps/ticket-service/` (`:8082`) ⭐ (CORE CRÍTICO)
**Responsabilidade**: Reservas, controle de estoque, Locks Distribuídos, Optimistic Locking e Outbox Pattern.

* 🧠 **O que estudar**:
  * **Race Conditions & Concorrência**: Optimistic Locking com `@Version` do JPA.
  * **Distributed Lock**: Redisson (`RLock`) com Redis para travar concorrência entre instâncias.
  * **Transactional Outbox Pattern**: garantir escrita atômica no banco relacional + disparo assíncrono no Kafka.
  * **Scheduler**: tarefas agendadas com `@Scheduled` para expiração de reservas (10 min TTL).
* 📍 **Onde estudar**:
  * 🇧🇷 [Controle de Concorrência com JPA e Locking — Giuliana Bezerra (YouTube)](https://www.youtube.com/watch?v=8ABe3A6eAMI)
  * 🇺🇸 [Distributed Locking with Redis Redisson — Baeldung](https://www.baeldung.com/redis-redisson)
  * 🇺🇸 [Pattern: Transactional Outbox — Microservices.io](https://microservices.io/patterns/data/transactional-outbox.html)
  * 🇺🇸 [Designing Data-Intensive Applications (Capítulo 7 - Transactions)](https://dataintensive.net/)
* 🧪 **Como estudar (Prática)**:
  1. Escreva um teste de integração com `ExecutorService` e 100 threads tentando reservar o último ingresso disponível.
  2. Verifique se o Optimistic Locking ou o Redisson Lock impede o overselling (apenas 1 reserva tem sucesso).
  3. Crie a tabela `outbox_events` e um scheduler `@Scheduled(fixedRate = 1000)` para ler e enviar eventos pendentes.
* ✅ **Check de Conclusão**: Zero overselling em testes simultâneos e outbox processando eventos sem perdas.

#### 4.5 📁 `apps/payment-service/` (`:8083`)
**Responsabilidade**: Processar pagamentos simulados, Idempotência e Saga Coreografada.

* 🧠 **O que estudar**: Saga Pattern (Coreografia baseada em eventos), Header `Idempotency-Key`, Resilience4j Retry/CircuitBreaker.
* 📍 **Onde estudar**:
  * 🇧🇷 [Saga Pattern em Microserviços — DevEfficient (YouTube)](https://www.youtube.com/results?search_query=saga+pattern+java)
  * 🇺🇸 [Pattern: Saga — Microservices.io](https://microservices.io/patterns/data/saga.html)
  * 🇺🇸 [Guide to Resilience4j — Baeldung](https://www.baeldung.com/resilience4j)
* 🧪 **Como estudar (Prática)**:
  1. Envie a mesma requisição de pagamento com o mesmo `Idempotency-Key` duas vezes e confirme que a segunda retorna o pagamento existente sem re-cobrar.
  2. Simule uma falha no pagamento e verifique se o evento `payment.failed` faz o `ticket-service` cancelar a reserva e devolver o estoque (compensação Saga).
* ✅ **Check de Conclusão**: Transações idempotentes e fluxo de compensação da Saga operante.

#### 4.6 📁 `apps/queue-service/` (`:8084`)
**Responsabilidade**: Fila virtual para vendas de alta demanda.

* 🧠 **O que estudar**: Redis Sorted Sets (`ZADD`, `ZRANK`, `ZPOPMIN`), Rate Limiting, Algoritmo Token Bucket.
* 📍 **Onde estudar**:
  * 🇺🇸 [Redis Sorted Sets Explained — Redis Official Docs](https://redis.io/docs/data-types/sorted-sets/)
  * 🇺🇸 [Token Bucket Rate Limiting Algorithm — System Design Primer](https://github.com/donnemartin/system-design-primer)
* 🧪 **Como estudar (Prática)**:
  1. Adicione 5 usuários ao Redis Sorted Set usando o timestamp atual como score.
  2. Execute um worker agendado que faz `ZPOPMIN` de N usuários por segundo e gera um `accessToken` assinado no Redis com TTL de 5 minutos.
* ✅ **Check de Conclusão**: Posição na fila informada corretamente e liberação de acesso controlada por tempo.

#### 4.7 📁 `apps/notification-service/` (`:8085`)
**Responsabilidade**: Consumo de eventos Kafka e disparo de e-mails.

* 🧠 **O que estudar**: Kafka Listener (`@KafkaListener`), Consumer Groups, JavaMailSender, Templates Thymeleaf.
* 📍 **Onde estudar**:
  * 🇧🇷 [Envio de E-mails com Spring Boot e Thymeleaf — Algaworks (YouTube)](https://www.youtube.com/results?search_query=algaworks+spring+email+thymeleaf)
  * 🇺🇸 [Spring Email Tutorial — Baeldung](https://www.baeldung.com/spring-email)
* 🧪 **Como estudar (Prática)**:
  1. Suba o container Mailhog (`localhost:8025`).
  2. Consuma o evento `ticket.confirmed` e envie um e-mail HTML dinâmico com o código da reserva.
  3. Abra o Mailhog no navegador e confirme o recebimento da mensagem.
* ✅ **Check de Conclusão**: E-mails formatados capturados com sucesso no dashboard do Mailhog.

---

### 5. 📁 `infra/` — Observabilidade & Infraestrutura

#### 5.1 📁 `infra/observability/`
* 🧠 **O que estudar**: Metrics (Prometheus), Dashboards (Grafana), Centralized Logs (Loki), Distributed Tracing (Jaeger / OpenTelemetry).
* 📍 **Onde estudar**:
  * 🇧🇷 [Observabilidade no Spring Boot com Prometheus e Grafana — Giuliana Bezerra (YouTube)](https://www.youtube.com/watch?v=mB2nDJ0wKKE)
  * 🇺🇸 [Spring Boot 3 Observability — Baeldung](https://www.baeldung.com/spring-boot-3-observability)
  * 🇺🇸 [Jaeger Distributed Tracing Overview](https://www.jaegertracing.io/docs/)
* 🧪 **Como estudar (Prática)**:
  1. Habilite o Micrometer Prometheus nos serviços e abra `http://localhost:8082/actuator/prometheus`.
  2. Importe um dashboard de Spring Boot no Grafana (`http://localhost:3000`).
  3. Faça uma requisição e busque pelo `traceId` nos logs do Loki e no painel do Jaeger.
* ✅ **Check de Conclusão**: Requisições rastreáveis de ponta a ponta com dashboards gráficos ativos.

---

### 6. 📁 `tests/load-tests/` — Testes de Carga com Gatling

* 🧠 **O que estudar**: Engenharia de Performance, Throughput (RPS), Latência P99, Gatling DSL em Java/Scala.
* 📍 **Onde estudar**:
  * 🇺🇸 [Gatling Quickstart Guide — Official Docs](https://docs.gatling.io/tutorials/quickstart/)
  * 🇺🇸 [Load Testing Spring Boot with Gatling — Baeldung](https://www.baeldung.com/introduction-to-gatling)
* 🧪 **Como estudar (Prática)**:
  1. Crie uma simulação Gatling `HighDemandSimulation.java`.
  2. Dispare 1.000 usuários virtuais simulando compras de ingresso em um intervalo de 30 segundos.
  3. Analise o relatório HTML gerado pelo Gatling e identifique a taxa de sucesso e tempo de resposta P99.
* ✅ **Check de Conclusão**: Relatório do Gatling gerado demonstrando resiliência da aplicação.

---

## 🎯 Roteiro Prático de Início (Passo a Passo)

1. **Passo 1 (Hoje)**: Crie o arquivo `docker-compose.yml` contendo PostgreSQL (`event-db`, `ticket-db`, `payment-db`), Redis e Kafka.
2. **Passo 2**: Implemente as classes da `libs/commons` e `libs/kafka-events`.
3. **Passo 3**: Configure o `eureka-server` e suba o painel em `http://localhost:8761`.
4. **Passo 4**: Desenvolva o `event-service` com Flyway Migrations e teste a publicação no Kafka UI.
5. **Passo 5**: Desenvolva o `ticket-service` focando na reserva com Redisson e `@Version`.
