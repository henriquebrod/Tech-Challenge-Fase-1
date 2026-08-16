# Relatório Executivo: Diagnóstico Operacional e Predição de NPS

---

## 1. Resumo dos Insights da Análise Exploratória (EDA)

- **Detração Crítica:** **84,36%** dos clientes da base foram classificados como **detratores** ($NPS < 7$)[cite: 1].
- **Impacto Direto do Atraso:** O ponto de ruptura do NPS ocorre com apenas **1 dia de atraso na entrega**[cite: 1]. A nota média despenca de **6,85** (0 dias de atraso) para **5,54** (1 dia) e segue em queda contínua[cite: 1].
- **Fricção do Atendimento:** Clientes com zero dias de atraso ainda apresentam insatisfação devido a altas taxas de reclamação (média de ~3 reclamações) e tempo prolongado para resolução de problemas (média de 5,6 dias)[cite: 1].
- **Perfil de Clientes e Região:** Não há variação significativa no perfil demográfico ou regional entre promotores e detratores, o que confirma que o problema é estritamente **operacional**[cite: 1].

---

## 2. Desempenho dos Modelos Preditivos

Para estimar a satisfação dos clientes com base nas variáveis operacionais (_dias de atraso, reclamações, contatos com atendimento e tempo de resolução_), foram desenvolvidos dois modelos[cite: 2]:

### A. Modelo de Regressão Linear (OLS) - Predição de Nota

- **Métrica R²:** **0.556** (o modelo explica 55,6% da variação na nota do NPS)[cite: 2].
- **Erro Médio Absoluto (MAE):** **1.32 pontos** no NPS[cite: 2].
- **RMSE:** **1.67 pontos**[cite: 2].
- **Maior Coeficiente Negativo:** Cada dia de atraso reduz o NPS em aproximadamente **-0.94 pontos**[cite: 2].

### B. Modelo de Classificação (Regressão Logística) - Predição de Detração

- **Objetivo:** Identificar se o cliente se tornará um detrator ($is\_detractor = 1$)[cite: 2].
- **Acurácia Geral:** **86%**[cite: 2].
- **Recall (Detratores):** **95%** (o modelo consegue capturar 95% de todos os clientes que viram detratores)[cite: 2].
- **Precision (Detratores):** **89%**[cite: 2].

---

## 3. Visualizações Principais

As imagens dos gráficos analisados estão salvas em `reports/figures/`:

1. `queda_do_nps_medio_por_dias_de_atraso.png`: Curva de queda do NPS por dias de atraso[cite: 1].
2. `distribuicoes_operacionais.png`: Histogramas das variáveis operacionais do negócio[cite: 1].
3. `correlacao_nps.png`: Heatmap das correlações lineares com a nota do NPS[cite: 1].
