# Dinâmica: Design Estratégico do Projeto

## Objetivo
Identificar os subdomínios do projeto, classificá-los (Core, Supporting, Generic) e desenhar os bounded contexts, incluindo suas interações. Esse exercício ajudará a criar uma visão clara e estratégica do domínio.

---

## 1. Nome do Projeto
**[SwiftEats Delivery]**

---

## 2. Objetivo Principal do Projeto
Conectar clientes, restaurantes e entregadores com rapidez, confiabilidade e transparência no acompanhamento do pedido - do carrinho à entrega - maximizando taxa de conversão e SLAs.

---

## 3. Identificação dos Subdomínios

| Subdomínio | Descrição | Tipo | Por que está nessa categoria |
|---|---|---|---|
| Gestão de Pedidos & Checkout | Orquestra o ciclo de vida do pedido (carrinho -> confirmação -> preparação -> retirada -> entrega), estados, idempotência e consistência de eventos. | Core Domain | Coração do negócio: tudo gira em torno do pedido e da confiabilidade do seu fluxo. |
| Matching de Entregadores & Roteirização | Atribui entregadores com base em proximidade, capacidade e tempo/rota estimados, garantindo SLAs. | Core Domain | Diferencial competitivo direto (previsão de chegada precisa e baixa latência de alocação). |
| Precificação, Taxas & Promoções | Calcula preço final do pedido (itens, frete, taxas e cupons) e aplica regras e limites. | Core Domain | Motor de preço e incentivos afeta margem/GMV; precisa ser proprietário. |
| Catálogo & Disponibilidade | Gerencia cardápios, variações, combos, horários e indisponibilidades. | Supporting | Essencial para conversão; pode aproveitar ferramentas/processos existentes. |
| Pagamentos & Liquidação | Integrações com PSPs; split, repasses, conciliação e estornos. | Supporting | Importante, mas acelerável via provedores de mercado mantendo regras próprias. |
| Mapas & Geocodificação | Geocoding, cálculo de distâncias/matriz e mapas de navegação. | Generic | Serviço commodity (Google/Mapbox/OSRM); integrar/contratar é mais eficiente. |
| Autenticação & Identidade | Login, cadastro, OAuth/OIDC, recuperação de acesso e sessões. | Generic | Não gera diferenciação direta; soluções prontas (Auth0/Keycloak/IdP). |

---

## 6. Definição da Linguagem Ubíqua

| Termo | Descrição |
|---|---|
| Carrinho | Conjunto de itens antes da confirmação do pedido. |
| Pedido | Entidade principal; contém itens, endereço, método de pagamento e estado atual. |
| Estado do Pedido | CRIADO -> CONFIRMADO -> EM_PREPARO -> PRONTO_PARA_RETIRADA -> EM_ROTA -> ENTREGUE / FALHA_ENTREGA / CANCELADO. |
| Previsão de Chegada | Estimativa de chegada do pedido ao cliente (preparo + coleta + deslocamento). |
| Janela de Coleta | Intervalo no qual o pedido deve ser coletado no restaurante. |
| Matching | Processo de atribuição do entregador ao pedido com critérios e score. |
| Rota | Sequência otimizada do ponto A (restaurante) ao ponto B (cliente). |
| Taxa de Entrega (Frete) | Valor variável em função de distância, demanda e promoções. |
| Cupom | Benefício aplicado ao preço final segundo regras de elegibilidade. |
| SLA | Compromisso de tempo (preparo, coleta, entrega) monitorado por métricas. |
| Evento de Domínio | Ocorrências assíncronas como "PedidoConfirmado", "EntregadorAtribuido", "PedidoSaiuParaEntrega", "PedidoEntregue". |
| Repasse | Liquidação financeira para restaurante e repasse de ganhos ao entregador. |
| Chargeback/Estorno | Devolução de valor por contestação ou falha; tratada pelo contexto de Pagamentos. |

---

## 7. Estratégia de Desenvolvimento

Critério geral:
- Core Domains -> Build interno (proprietário; foco em latência, precisão e evolução do modelo de domínio).
- Supporting -> Build interno leve + integrações (acelera time-to-market mantendo controle de regra).
- Generic -> Buy/Integrar (não gera diferenciação direta; otimizar custo e confiabilidade).

| Subdomínio | Estratégia | Ferramentas ou Serviços (se aplicável) |
|---|---|---|
| Gestão de Pedidos & Checkout (Core) | Build interno completo (estados, idempotência, eventos e retries). | PostgreSQL; mensageria (Kafka/RabbitMQ); Redis para locks/filas curtas. |
| Matching & Roteirização (Core) | Build interno (heurísticas/ML e previsão de chegada). | Serviços de rota; motor de otimização; workers de baixa latência. |
| Precificação, Taxas & Promoções (Core) | Build interno (motor de regras e simulações). | Rules engine; feature flags/experimentos; storage de regras versionado. |
| Catálogo & Disponibilidade (Supporting) | Build interno leve + admin e cache por loja. | SPA Admin + APIs; cache/eventos para (in)disponibilidade; auditoria. |
| Pagamentos & Liquidação (Supporting) | Integrar com PSPs/split + conciliação própria. | Stripe/Mercado Pago/Adyen/Pagar.me; serviço de conciliação; webhooks. |
| Mapas & Geocodificação (Generic) | Buy/Integrar. | Google Maps; Mapbox; OSRM/GraphHopper (on-prem). |
| Autenticação & Identidade (Generic) | Buy/Integrar. | Auth0/Okta; Keycloak; provedores OIDC/SAML. |

---

> Observações:
> - Tabelas simplificadas e apenas caracteres ASCII para maior compatibilidade de renderização.
> - O glossário (linguagem ubíqua) deve ser documentado e evoluído conforme o domínio for refinado com especialistas.
