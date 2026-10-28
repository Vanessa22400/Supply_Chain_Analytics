
# Supply Chain Analytics: Previsão de Demanda, Otimização de Estoques e Pesquisa Operacional

*Um estudo de caso de análise de negócios que combina previsão de demanda, otimização matemática e análise de riscos para apoiar decisões de estoque no varejo.*

**Autora:** Vanessa Donato  
**Dados:** 180.519 linhas de pedidos · 53 colunas originais  
**Ferramentas:** Python · Google Colab · Pandas · NumPy · Statsmodels · PuLP · MLflow · Matplotlib · Seaborn  
**Métodos:** Análise Exploratória de Dados (EDA) · Previsão de Demanda · Validação Walk-Forward · Programação Linear Inteira · Simulação de Monte Carlo · Segmentação RFM · Análise de Rentabilidade · Avaliação de Respostas de LLMs

📓 **[Implementação Técnica Completa: Disponível Mediante Solicitação](notebook/NOTEBOOK_ACCESS.md)**  
🌐 **[English version](README.md)**

---

## Principais Resultados

| Área | Resultado | Relevância para o negócio |
|---|---|---|
| Previsão de demanda | **7,4% de WAPE** | Métodos selecionados por produto, frente a **9,9%** do modelo Naive |
| Otimização de estoques | **50,2% de economia potencial** | Custo simulado de reposição reduzido de **US$ 6.538,60** para **US$ 3.253,50** |
| Alocação de estoque | **120 unidades transferidas + 65 compradas** | Plano de reposição para **185 unidades em falta** no cenário simulado |
| Risco de demanda | **91,3%** | Percentual de cenários de Monte Carlo com pelo menos uma falta de estoque |
| Retenção de clientes | **77,1% da receita** | Participação dos segmentos Champions e At-Risk High Value na receita de pedidos concluídos |
| Perdas de rentabilidade | **US$ 1,26 milhão em perdas brutas** | Lucro negativo em **18,6%** das linhas de pedidos concluídos |

*Importante: os volumes de estoque, custos de reposição, economias e cenários de risco são simulados. Os resultados de clientes e rentabilidade são observações dos dados históricos. O desempenho do método de previsão selecionado foi avaliado no mesmo período de 20 semanas usado para escolher os modelos; portanto, a acurácia apresentada pode ser otimista.*

---

## Contexto de Negócio

Operações de varejo precisam equilibrar demanda, disponibilidade de produtos, custos de transporte e decisões de reposição entre diferentes mercados. Comprar mais estoque pode resolver faltas locais, mas também gerar custos desnecessários quando outras regiões possuem produtos excedentes.

Com base em registros históricos de transações de varejo, o projeto investiga como métodos analíticos podem apoiar essas decisões. **A otimização de estoques é o problema central**, enquanto retenção de clientes e rentabilidade são abordadas como desafios complementares.

### Três Perguntas de Negócio

1. **Planejamento de estoques:** Como redistribuir os produtos disponíveis e reduzir o custo necessário para atender à demanda prevista?
2. **Retenção de clientes:** Quais segmentos devem ser priorizados em iniciativas de fidelização e reengajamento?
3. **Perdas de rentabilidade:** Onde se concentram as transações com lucro negativo e o que deve ser investigado primeiro?

### Panorama dos Problemas de Negócio

![Panorama dos Problemas de Negócio](images/business_problem_overview.png)

*Os dados históricos mostram 57,3% de linhas de pedidos com entrega atrasada, 55,3% de clientes com apenas uma compra concluída e 18,6% de linhas de pedidos concluídos com lucro negativo. O benchmark de previsão e o desequilíbrio de estoque são elementos da modelagem, não indicadores observados dos armazéns da empresa.*

---

## Dados e Escopo da Análise

A base utilizada contém **180.519 linhas de pedidos** e **53 colunas originais**, com informações sobre produtos, clientes, pedidos, vendas, lucro e entregas.

Esses registros sustentam o diagnóstico histórico do negócio. Para o planejamento de estoques, porém, **não estão disponíveis saldos reais de armazéns, preços de aquisição dos fornecedores nem custos de transferência entre regiões**. Por isso, esses elementos foram definidos como hipóteses explícitas, e não apresentados como dados operacionais reais.

A etapa de previsão de demanda concentra-se em **cinco produtos**. Na POC de otimização, parte das previsões é distribuída entre **quatro regiões selecionadas**. As análises RFM e de rentabilidade consideram somente pedidos com status **COMPLETE**, para trabalhar com transações confirmadas como concluídas.

## Fluxo Analítico

```text
Preparação e validação dos dados
    → Diagnóstico do negócio e EDA
    → Previsão de demanda por produto
    → Distribuição da demanda prevista entre regiões
    → Otimização de estoques e comparação de custos
    → Simulação de Monte Carlo e análise de risco
    → Segmentação RFM e análise de rentabilidade
    → Recomendações de negócio e entregáveis reutilizáveis
```

---

## 1. Diagnóstico do Negócio e Análise Exploratória

A análise histórica identificou desafios operacionais e financeiros relevantes:

- **57,3% das linhas de pedidos** estavam associadas a entregas atrasadas. As taxas mensais de atraso foram relativamente estáveis, sugerindo que o problema não se limitava a um pico sazonal.
- **55,3% dos clientes com pedidos concluídos** realizaram apenas uma compra no período observado.
- **18,6% das linhas de pedidos concluídos** apresentaram lucro negativo.

Os atrasos de entrega são observações dos registros históricos, mas **os dados não permitem concluir que tenham sido causados por falta de estoque**. O modelo de otimização desenvolvido posteriormente é um exercício de planejamento simulado e independente dessa hipótese causal.

---

## 2. Previsão de Demanda

Foram analisados cinco produtos de alto volume, utilizando **143 semanas completas** de histórico de vendas, após a exclusão de duas semanas incompletas nas extremidades da série. As últimas **20 semanas** foram reservadas para avaliação *walk-forward*, com previsões de uma semana à frente.

### Demanda Histórica por Produto

![Demanda Semanal por Produto](images/weekly_product_demand.png)

*As vendas semanais foram agregadas por produto para evitar que diferenças na cobertura histórica das regiões fossem confundidas com mudanças reais na demanda.*

### Comparação dos Modelos de Previsão

| Método de previsão | WAPE geral |
|---|---:|
| Naive | 9,9% |
| Média Móvel de 4 Semanas (MA4) | 8,2% |
| Suavização Exponencial Simples (SES) | 7,5% |
| **Melhor método selecionado por produto** | **7,4%** |

SES teve o melhor desempenho para quatro produtos, enquanto MA4 apresentou o menor erro para *O'Brien Life Vest*. A abordagem final combinou o método mais preciso para cada produto.

![Demanda Real vs. Prevista](images/actual_vs_forecast.png)

*Comparação da demanda real com a prevista ao longo das 20 semanas de avaliação. Oscilações repentinas continuam sendo difíceis de capturar, mesmo com um erro agregado relativamente baixo.*

A previsão final para a semana seguinte totalizou **1.796 unidades** considerando todas as regiões registradas. Em seguida, a participação histórica de cada região nas vendas dos produtos foi utilizada para distribuir **818 unidades** entre as quatro regiões do cenário de planejamento. Essa distribuição é uma **hipótese operacional, não uma previsão regional validada de maneira independente**.

**Limitação da validação:** o WAPE de 7,4% foi obtido no mesmo período usado para selecionar os melhores métodos. É um resultado útil para comparar modelos, mas seria necessário um período de teste independente para estimar seu desempenho futuro de forma menos sujeita a otimismo.

---

## 3. Otimização de Estoques e POC de Reposição

### Cenário de Planejamento Simulado

A demanda regional estimada a partir das previsões foi comparada com disponibilidades hipotéticas de estoque:

| Indicador de estoque | Unidades |
|---|---:|
| Demanda prevista nas quatro regiões | 818 |
| Estoque disponível simulado | 753 |
| Faltas brutas de estoque | **185** |
| Excedentes brutos de estoque | **120** |
| Compras mínimas após redistribuição viável | **65** |

Um modelo de **Programação Linear Inteira**, desenvolvido com PuLP, minimiza os custos adicionais de compra e transferência, atendendo à demanda de cada produto e região. Os custos de aquisição e as rotas são simulados; as transferências permitidas devem respeitar o horizonte de planejamento de uma semana.

### Apenas Compras vs. Reposição Otimizada

| Estratégia | Transferências | Compras | Custo simulado |
|---|---:|---:|---:|
| Apenas compras | 0 | 185 | **US$ 6.538,60** |
| Plano otimizado | **120** | **65** | **US$ 3.253,50** |

**Economia potencial: US$ 3.285,10 (50,2%)**, sob as premissas modeladas.

O plano recomenda transferir **62 unidades da América do Sul para a América Central** e **58 unidades do Norte da Europa para a Europa Ocidental**. As quantidades e os custos são ilustrativos: não representam instruções para armazéns reais nem economias efetivamente realizadas.

### POC de Otimização Reutilizável

A lógica de otimização foi organizada em uma função reutilizável, que recebe dados de estoque, custos de compra e rotas de transferência. A validação reproduziu a solução original, incluindo o custo total de **US$ 3.253,50**, **120 unidades transferidas** e **65 unidades compradas**.

A POC exporta seis arquivos CSV: `inventory_scenario.csv`, `purchase_costs.csv`, `transfer_routes.csv`, `transfer_plan.csv`, `purchase_plan.csv` e `cost_summary.csv`.

Esses arquivos permitem consultar as premissas e as decisões recomendadas sem depender apenas dos resultados exibidos no notebook.

---

## 4. Simulação de Monte Carlo e Análise de Risco

O plano otimizado foi testado sob incerteza de demanda por meio de **1.000 cenários simulados**, construídos com base nos erros históricos das previsões.

- **91,3%** dos cenários apresentaram pelo menos uma falta de estoque.
- A falta média foi de **27,4 unidades**, com percentil 90 de **56 unidades**.
- O custo médio hipotético de reposição emergencial foi de **US$ 1.252,91**.

Esses resultados mostram por que um plano de menor custo, baseado apenas em previsões pontuais, também precisa ser avaliado quanto à exposição a riscos. A simulação utiliza premissas simplificadas e **não indica que essas faltas ou despesas tenham ocorrido nas operações reais**.

---

## 5. Análise de Retenção de Clientes

A segmentação RFM utilizou **21.716 pedidos concluídos** de **12.523 clientes**, considerando recência, frequência de compra e valor monetário.

- **44,7%** desses clientes realizaram pelo menos dois pedidos concluídos.
- **Champions:** 18,9% dos clientes, responsáveis por **36,0%** da receita de pedidos concluídos.
- **At-Risk High Value:** 27,3% dos clientes, responsáveis por **41,1%** da receita de pedidos concluídos.
- Juntos, esses dois segmentos representaram **77,1% da receita**.

![Segmentação RFM de Clientes](images/rfm_segments.png)

*Participação dos segmentos na base de clientes versus participação na receita. O rótulo “At-Risk” descreve a recência histórica de compra e não significa abandono confirmado.*

Os resultados sugerem priorizar o reengajamento de clientes historicamente valiosos, fortalecer a fidelização dos *Champions* e incentivar a segunda compra entre consumidores que compraram apenas uma vez recentemente.

---

## 6. Análise de Perdas de Rentabilidade

Entre **59.491 linhas de pedidos concluídos**, **11.079 (18,6%)** registraram lucro negativo, totalizando **US$ 1.264.213,06** em perdas brutas. Esse valor equivale a **48,9%** do lucro positivo bruto das demais transações.

As cinco categorias com maiores perdas concentraram **60,9%** das perdas brutas totais. *Fishing* apresentou o maior valor individual entre as categorias (**US$ 235.616,63**).

Faixas de desconto mais elevadas apresentaram taxas ligeiramente maiores de lucro negativo e margens menores, mas as diferenças foram modestas. Trata-se de **uma associação descritiva, não de evidência de que os descontos tenham causado as perdas**. Além disso, as perdas brutas observadas não devem ser interpretadas como valores necessariamente recuperáveis.

---

## Panorama dos Resultados do Projeto

![Painel de Resultados do Projeto](images/project_results_dashboard.png)

*Síntese visual da precisão das previsões, economia potencial de reposição, redistribuição de estoques, incerteza da demanda, concentração de receita entre clientes e perdas de rentabilidade observadas.*

---

## Recomendações de Negócio Priorizadas

**1. Testar a redistribuição regional antes de comprar mais estoque.** Combinar previsões de demanda e saldos por produto para identificar transferências operacionalmente viáveis e menos custosas do que novas compras. Validar os custos simulados antes de qualquer aplicação real.

**2. Incorporar a incerteza ao planejamento de reposição.** Avaliar políticas de estoque de segurança e planos de contingência em conjunto com a otimização baseada em previsões pontuais, especialmente quando compras emergenciais podem ser caras. Validar as previsões em um período independente.

**3. Proteger clientes de alto valor e investigar perdas de rentabilidade.** Priorizar experimentos de retenção para os segmentos *Champions* e *At-Risk High Value* e revisar produtos com prejuízos recorrentes, preços, descontos e custos antes de alterar políticas comerciais.

---

## Implementação Técnica e Entregáveis Adicionais

### Rastreamento de Experimentos com MLflow

Quatro abordagens de previsão (**Naive, MA4, SES e Selected Models**) foram registradas no MLflow, com parâmetros de avaliação e métricas MAE/WAPE. O resumo está disponível em `outputs/mlflow_forecast_results.csv`; o banco local dos experimentos também foi salvo separadamente como backup.

### POC de Otimização de Estoques

A função reutilizável em PuLP reproduz o resultado da otimização e gera seis arquivos CSV para consulta de entradas e saídas em `outputs/inventory_poc/`. Trata-se de uma **prova de conceito para apoio à decisão**, e não de um sistema de gestão de estoques em produção.

### Avaliação de Respostas de LLMs

Um experimento complementar comparou respostas do **Claude e do ChatGPT** ao mesmo pedido de recomendações de negócio. Foram utilizados cinco critérios: precisão, fundamentação nos dados, clareza, aplicabilidade e comunicação das limitações.

As notas qualitativas foram **23/25 para Claude** e **24/25 para ChatGPT**, considerando apenas essa pergunta. A avaliação não constitui um benchmark geral dos modelos nem um estudo totalmente cego.

Consulte o [Caso de Avaliação de LLMs](llm_evaluation/llm_evaluation_case.md) para acessar o prompt, as respostas originais, as notas e os comentários.

---

## Limitações

- **Estoque e custos:** Saldos de armazéns, preços de aquisição, custos de transferência e prazos são hipotéticos; a **economia de 50,2%** não representa um resultado financeiro realizado.
- **Validação das previsões:** Os modelos foram selecionados no mesmo período em que se mediu o WAPE final de **7,4%**. A demanda regional foi distribuída por participações históricas.
- **Simulação de risco:** As estimativas de Monte Carlo dependem de uma amostra limitada de erros de previsão e de premissas operacionais simplificadas.
- **Clientes e rentabilidade:** Os segmentos RFM não comprovam abandono, e os padrões de lucro não estabelecem causas ou economias recuperáveis.
- **Implementação:** A POC e os registros do MLflow demonstram organização e reprodutibilidade dos experimentos, não um pipeline implantado em produção.

## Próximos Passos

- Substituir saldos simulados, custos de aquisição e rotas logísticas por dados operacionais reais.
- Validar os modelos em um período independente e avaliar previsões por produto e região.
- Comparar reposição otimizada, políticas de estoque de segurança e compras emergenciais sob cenários de risco consistentes.
- Testar iniciativas de retenção e investigar em maior detalhe os fatores associados à rentabilidade por produto.

---

## Estrutura do Repositório

```text
supply-chain-analytics/
├── README.md
├── README_PT.md
├── notebook/
│   └── NOTEBOOK_ACCESS.md
├── images/
│   ├── business_problem_overview.png
│   ├── weekly_product_demand.png
│   ├── actual_vs_forecast.png
│   ├── rfm_segments.png
│   └── project_results_dashboard.png
├── outputs/
│   ├── mlflow_forecast_results.csv
│   └── inventory_poc/
│       ├── inventory_scenario.csv
│       ├── purchase_costs.csv
│       ├── transfer_routes.csv
│       ├── transfer_plan.csv
│       ├── purchase_plan.csv
│       └── cost_summary.csv
├── llm_evaluation/
│   └── llm_evaluation_case.md
└── requirements.txt
```

## Acesso à Implementação Técnica

A análise foi desenvolvida em **Google Colab**. O notebook completo e os detalhes de implementação estão disponíveis mediante solicitação para fins de recrutamento e avaliação profissional.

Para solicitar acesso, consulte o arquivo [NOTEBOOK_ACCESS.md](notebook/NOTEBOOK_ACCESS.md) ou entre em contato pelo [LinkedIn](https://www.linkedin.com/in/vanessadonato/).

Os CSVs da POC e o resumo de experimentos MLflow permanecem disponíveis no repositório como entregáveis de referência. A execução integral do notebook depende dos arquivos de entrada e da configuração do ambiente.

---

## Conclusão

O projeto combina previsão de demanda, otimização, simulação, segmentação de clientes e análise financeira para conectar resultados analíticos a decisões de negócio. A POC de estoques demonstra **economia potencial sob um cenário simulado claramente definido**, enquanto as análises históricas de clientes e rentabilidade identificam outras oportunidades de investigação.

Além dos resultados analíticos, o rastreamento de experimentos com MLflow, os arquivos reutilizáveis da otimização e a avaliação estruturada de respostas de LLMs mostram aplicações práticas de documentação, transparência e reprodutibilidade em projetos de dados.
