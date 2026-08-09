# 🎫 TicketFlow — Sistema Distribuído de Venda de Ingressos

**TicketFlow** é uma plataforma de venda de ingressos construída com arquitetura de microserviços em Java 21 e Spring Boot 3.3, projetada para lidar com **alta concorrência**, **consistência distribuída** e **resiliência**. O sistema simula cenários reais como vendas flash, filas virtuais e prevenção de overselling.

---

## 👨‍💻 Desenvolvedor

| Nome | GitHub | LinkedIn |
| :--- | :--- | :--- |
| **Marllon Anisio** | [![GitHub](https://img.shields.io/badge/GitHub-MarllonAnisio-181717?style=flat&logo=github)](https://github.com/MarllonAnisio) | [![LinkedIn](https://img.shields.io/badge/LinkedIn-marllon--anisio-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/marllon-anisio) |

---

## 🏗️ Arquitetura do Monorepo

```
ticketflow/
├── pom.xml                                 ← Parent POM (Módulo Pai Maven)
│
├── 📁 apps/                                ← Microserviços
│   ├── eureka-server                       ← Service Discovery (:8761)
│   ├── api-gateway                         ← Spring Cloud Gateway (:8080)
│   ├── event-service                       ← Gestão de Eventos (:8081)
│   ├── ticket-service                      ← Core - Ingressos & Reservas (:8082)
│   ├── payment-service                     ← Pagamentos (:8083)
│   ├── queue-service                       ← Fila Virtual & Rate Limiting (:8084)
│   └── notification-service                ← Envio de Notificações (:8085)
│
├── 📁 libs/                                ← Bibliotecas Compartilhadas
│   ├── commons                             ← Exceções e DTOs utilitários
│   └── kafka-events                        ← DTOs de Eventos do Kafka
│
├── 📁 infra/                               ← Infraestrutura e Observabilidade
│   └── observability/                      ← Prometheus, Grafana, Loki, Jaeger
│
└── 📁 tests/                               ← Testes de Carga
    └── load-tests                          ← Simulações com Gatling
```

---

## 🛠️ Tecnologias Utilizadas

- **Linguagem:** Java 21
- **Framework:** Spring Boot 3.3.2 / Spring Cloud 2023.0.3
- **Mensageria:** Apache Kafka
- **Cache & Locks:** Redis & Redisson
- **Banco de Dados:** PostgreSQL & Flyway Migration
- **Resiliência:** Resilience4j (Circuit Breaker, Retry, Rate Limiter)
- **Observabilidade:** Prometheus, Grafana, Loki, Jaeger
- **Testes de Carga:** Gatling
