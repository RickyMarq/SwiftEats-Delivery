# 🚀 SwiftEats Delivery - Design Estratégico do Projeto

## 📌 Aula 1: Introdução ao Domain-Driven Design (DDD)

### 1️⃣ Revisão Rápida
- **DDD** ajuda a alinhar tecnologia e negócio, atacando a **complexidade essencial** (do domínio) e não só a acidental (técnica).  
- O domínio é dividido em **Core**, **Supporting** e **Generic Subdomains**.  
- Cada subdomínio pode ser implementado em um **Bounded Context** com regras e linguagem próprias.  

---

## 📌 Aula 2: Identificação dos Subdomínios

| **Subdomínio**                  | **Descrição**                                                                                   | **Tipo**       |
|---------------------------------|-------------------------------------------------------------------------------------------------|----------------|
| Gestão de Pedidos & Checkout    | Orquestra o ciclo de vida do pedido (carrinho → confirmação → preparo → entrega).               | Core Domain    |
| Matching & Roteirização         | Atribui entregadores e calcula rotas/previsão de chegada.                                       | Core Domain    |
| Precificação & Promoções        | Calcula preços, taxas, frete dinâmico e cupons.                                                 | Core Domain    |
| Catálogo & Disponibilidade      | Mantém cardápios, horários e itens disponíveis em tempo real.                                   | Supporting     |
| Pagamentos & Liquidação         | Processa pagamentos, splits e conciliação financeira.                                           | Supporting     |
| Mapas & Geocodificação          | Serviços de mapas, cálculo de distância e endereços.                                            | Generic        |
| Autenticação & Identidade       | Login, cadastro e permissões de usuários.                                                       | Generic        |

---

## 📌 Aula 2: Mapeamento de Contextos (Context Mapping)

### 1️⃣ Bounded Contexts Identificados
- **Pedidos & Checkout**  
- **Roteirização & Entregadores**  
- **Precificação & Promoções**  
- **Catálogo**  
- **Pagamentos**  
- **Mapas**  
- **Autenticação**  

### 2️⃣ Relacionamentos entre Contextos

| **Origem**                   | **Destino**                | **Relacionamento**          | **Justificativa** |
|------------------------------|----------------------------|-----------------------------|-------------------|
| Pedidos & Checkout           | Roteirização & Entregadores| **Customer-Supplier**       | O pedido depende da entrega; checkout envia ordem para roteirização. |
| Pedidos & Checkout           | Pagamentos                 | **Anticorruption Layer (ACL)** | O domínio de pedidos traduz dados financeiros antes de enviar ao contexto de pagamentos. |
| Catálogo & Disponibilidade   | Pedidos & Checkout         | **Shared Kernel**           | Catálogo de itens precisa estar consistente entre os dois contextos. |
| Precificação & Promoções     | Pedidos & Checkout         | **Conformist**              | Checkout apenas consome preços e regras do motor de precificação. |
| Mapas & Geocodificação       | Roteirização & Entregadores| **Open Host Service (OHS)** | Mapas expõem APIs externas (Google/Mapbox) consumidas pela roteirização. |
| Autenticação & Identidade    | Todos os contextos         | **Shared Kernel**           | Usuários e permissões são compartilhados em todo o domínio. |

---

## 📌 Aula 2: Definição da Linguagem Ubíqua

| **Termo**              | **Descrição** |
|-------------------------|---------------|
| **Carrinho**           | Itens selecionados antes da confirmação do pedido. |
| **Pedido**             | Entidade principal que contém itens, endereço e status. |
| **Status do Pedido**   | Criado → Confirmado → Em Preparo → Em Rota → Entregue/Cancelado. |
| **Previsão de Chegada**| Estimativa de tempo até a entrega, baseada em preparo e rota. |
| **Matching**           | Processo de atribuição de entregador ao pedido. |
| **Rota**               | Sequência otimizada do restaurante até o cliente. |
| **Cupom**              | Desconto aplicado segundo regras de elegibilidade. |
| **Frete**              | Taxa de entrega variável conforme distância/demanda. |
| **SLA**                | Tempo esperado para preparo, coleta e entrega. |
| **Repasse**            | Valor transferido para restaurantes e entregadores. |

---

## 📌 Aula 2: Estratégia de Desenvolvimento

Critério geral:  
- **Core Domains** → Build interno (maior controle e diferenciação).  
- **Supporting** → Build interno leve + integrações.  
- **Generic** → Buy/Integrar (não gera vantagem competitiva).  

| **Subdomínio**              | **Estratégia**            | **Ferramentas/Serviços** |
|-----------------------------|---------------------------|--------------------------|
| Gestão de Pedidos (Core)    | Build interno completo     | PostgreSQL, Kafka, Redis |
| Roteirização (Core)         | Build interno (heurísticas + ML) | Motor de rotas, Workers |
| Precificação (Core)         | Build interno (motor de regras) | Rules Engine, Feature Flags |
| Catálogo (Supporting)       | Build interno leve         | API + Cache distribuído |
| Pagamentos (Supporting)     | Integração com PSPs        | Stripe, Mercado Pago, Adyen |
| Mapas (Generic)             | Buy/Integrar               | Google Maps, Mapbox |
| Autenticação (Generic)      | Buy/Integrar               | Auth0, Keycloak, OIDC |

---

📢 **Observação Final:**  
Esse modelo reflete as práticas discutidas em aula com o professor Thiago Keller Torquato Vicco, reforçando conceitos como **Context Mapping, ACL, Shared Kernel e OHS**, além de aplicar o princípio **KISS** (Keep It Simple) para evitar **overengineering**.  
