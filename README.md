# 📊 Tech Challenge, Fase 1: NPS Preditivo

Este projeto investiga quais fatores operacionais influenciam a satisfação e o NPS (Net Promoter Score) dos clientes de um e-commerce, e propõe um modelo preditivo capaz de antecipar a insatisfação antes mesmo da pesquisa de NPS ser enviada.

## 🎯 Objetivo do Projeto

A empresa hoje só descobre se um cliente está satisfeito depois que a pesquisa de NPS é respondida, quando muitas vezes já é tarde para agir. A pergunta que guiou o trabalho foi: **quais fatores operacionais realmente afetam a satisfação do cliente, e como a empresa pode agir antes disso virar uma nota baixa?**

Com isso em mente, o projeto buscou:

- Entender quais variáveis (entrega, atendimento, pedido) mais pesam na nota do NPS;
- Traduzir esses achados em recomendações práticas para áreas como logística, atendimento, produto e pricing;
- Testar se é viável prever o NPS (regressão) ou o risco de o cliente virar detrator (classificação) usando dados operacionais.

A ideia não foi buscar o modelo mais sofisticado possível, e sim entender bem o problema e contar essa história de forma clara para quem toma decisão.

## 🗂️ Descrição da Base de Dados

A base traz um histórico de pedidos, entregas e atendimentos, com um registro por pedido.

**Arquivo fonte:** `desafio_nps_fase_1.csv`

| Coluna (original)           | O que representa                              |
| --------------------------- | --------------------------------------------- |
| `customer_id`               | Identificador único do cliente                |
| `order_id`                  | Identificador único do pedido                 |
| `customer_age`              | Idade do cliente                              |
| `customer_region`           | Região geográfica do cliente                  |
| `customer_tenure_months`    | Tempo de relacionamento com a empresa (meses) |
| `order_value`               | Valor total do pedido                         |
| `items_quantity`            | Quantidade de itens no pedido                 |
| `discount_value`            | Valor de desconto aplicado                    |
| `payment_installments`      | Número de parcelas                            |
| `delivery_time_days`        | Tempo total de entrega (dias)                 |
| `delivery_delay_days`       | Dias de atraso na entrega                     |
| `freight_value`             | Valor do frete                                |
| `delivery_attempts`         | Número de tentativas de entrega               |
| `customer_service_contacts` | Contatos do cliente com o SAC                 |
| `resolution_time_days`      | Tempo para resolver o problema (dias)         |
| `complaints_count`          | Número de reclamações registradas             |
| `repeat_purchase_30d`       | Recompra em até 30 dias (0 = não, 1 = sim)    |
| `csat_internal_score`       | Score interno de satisfação                   |
| `nps_score`                 | Nota de NPS, de 0 a 10 (nossa variável-alvo)  |

> 💡 Nos notebooks, essas colunas foram renomeadas para português (`dias_atraso_entrega`, `qtd_reclamacoes`, `contatos_sac`, `tempo_resolucao_dias`, `nota_nps` etc.) para facilitar a leitura.

**Por que `nps_score` é a variável-alvo?** Porque é ela que mostra a disposição do cliente em recomendar a empresa, o que permite classificá-lo como Promotor (nota ≥ 9), Neutro (7 ou 8) ou Detrator (abaixo de 7). Um cuidado importante: pesquisas de satisfação costumam ter baixa adesão espontânea e atraem mais os extremos (quem amou ou quem odiou), então isso pode distorcer a leitura do comportamento médio dos clientes.

## 🔍 Metodologia Utilizada

O trabalho seguiu quatro etapas:

**1. Entendimento do negócio.** Reflexão sobre por que o NPS importa, quem se beneficia dos insights (logística, atendimento, pricing, produto, CRM) e como ele se conecta com recompra, boca a boca e market share.

**2. Definição da target.** Justificativa de por que `nps_score` foi escolhida, quando ela é coletada na jornada do cliente e quais riscos existem em usá-la sem cuidado.

**3. Análise Exploratória (EDA), feita em `eda.ipynb`:**

- A base é dominada por detratores: 84,36% dos clientes têm NPS abaixo de 7, contra só 4,40% de promotores.
- Um único dia de atraso já derruba a nota média de 6,85 para 5,54. É um baque rápido.
- Mesmo sem atraso, clientes que precisam abrir reclamação ou ligar pro SAC seguem com nota baixa. Entrega no prazo evita o pior cenário, mas não garante uma boa avaliação.
- O mapa de correlação confirma: `dias_atraso_entrega`, `qtd_reclamacoes`, `contatos_sac` e `tempo_resolucao_dias` são os vilões da história.
- Cruzando atraso e reclamações num mapa de calor (e filtrando combinações com poucos registros, para não deixar outliers distorcerem a leitura), dá pra ver dois territórios bem definidos: um de Promotores/Neutros (entrega sem atraso ou no máximo 1 dia, com menos de 2 reclamações) e uma zona crítica de detração, que começa a partir do 2º dia de atraso ou da 3ª reclamação.
- Idade, tempo de relacionamento, valor do pedido e região não explicam a variação do NPS, o que reforça que o problema é operacional, não de perfil de cliente.

**4. Modelagem preditiva (desafio opcional), feita em `modelo.ipynb`,** usando as 4 variáveis operacionais acima, com separação treino/teste de 80/20 (`random_state=42`):

- **Regressão (OLS):** explica 55,6% da variação do NPS (R² = 0,556), com todas as variáveis estatisticamente significativas. A equação encontrada foi aproximadamente: `NPS ≈ 9,34 − 0,94 × dias_atraso − 0,39 × reclamações − 0,33 × contatos_sac − 0,15 × tempo_resolução`. Erro médio de cerca de 1,3 a 1,7 pontos de NPS.
- **Classificação (Regressão Logística):** identifica 95% dos clientes que realmente viram detratores (recall), acertando 89% das vezes que aponta um risco (precisão).
- Os dois modelos também foram testados sem outliers, e os resultados ficaram praticamente iguais, o que mostra que a relação entre operação e NPS é consistente, não fruto de dado fora da curva.

Por fim, os achados viraram quatro frentes de recomendação: resolutividade no primeiro contato, gestão de prazos logísticos, comunicação proativa com o cliente e monitoramento preditivo do risco de detração.

## ⚙️ Como Reproduzir os Resultados

### Pré-requisitos

Python 3.9+, Jupyter e as bibliotecas: `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`, `statsmodels`.

```bash
pip install pandas numpy matplotlib seaborn scikit-learn statsmodels jupyter
```

### Estrutura do repositório

```
├── data/            # base de dados (ou referência à fonte)
├── notebooks/
│   ├── eda.ipynb        # análise exploratória
│   └── modelo.ipynb     # modelos de regressão e classificação
├── reports/
│   ├── Tech Challenge - Análise de Fatores Operacionais e NPS no E-commerce.pdf  # respostas de negócio
│   └── Apresentacao_Final.pdf                                                    # apresentação para stakeholders
└── README.md
```

### Passo a passo

1. Clone o repositório.
2. Instale as dependências acima.
3. Rode `notebooks/eda.ipynb` do início ao fim (a base é lida direto de uma URL pública em CSV, sem precisar baixar nada manualmente).
4. Rode `notebooks/modelo.ipynb` do início ao fim para reproduzir os modelos, com e sem tratamento de outliers.
5. Para o contexto de negócio completo, veja `reports/Documentacao_escrita.pdf`, e para a versão executiva, `reports/apresentacao_final.pptx`.

> 🔁 Os notebooks usam `random_state=42` na separação treino/teste, então os números deste README devem bater exatamente ao reproduzir.
