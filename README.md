# OrchidLog — Análise de Perdas na Logística de Orquídeas

## Sobre o Projeto

A OrchidLog é uma empresa de agricultura de precisão especializada no cultivo 
e transporte nacional de orquídeas de alto padrão. Recentemente, a diretoria 
identificou um aumento preocupante nas entregas com status "Cancelado" — 
cargas que chegam ao destino murchas ou danificadas, gerando prejuízo total. 
A equipe de agronomia suspeitava que falhas no ar-condicionado dos caminhões, 
durante o transporte, poderiam estar expondo as orquídeas — plantas 
hipersensíveis à temperatura — a condições prejudiciais.

Este projeto tem como objetivo investigar essas perdas: quantificar o 
impacto real das entregas canceladas e identificar em que ponto da operação 
está o principal gargalo.

## Dados Utilizados

O projeto utiliza três bases de dados fornecidas em formato CSV, cada uma representando uma parte diferente da operação da OrchidLog.

### entregas_orquideas.csv

| Coluna | Descrição |
|---|---|
| `ID_Carga` | Identificador único de cada carga despachada |
| `Placa_Caminhao` | Placa do caminhão responsável pelo transporte |
| `Estufa_Origem` | Código da estufa de onde a carga saiu |
| `UF_Destino` | Estado brasileiro de destino da entrega |
| `Qtd_Orquideas` | Quantidade de orquídeas na carga |
| `Status_Entrega` | Situação final da entrega (`Entregue`, `Atrasada` ou `Cancelado`) |

### telemetria_iot.csv

| Coluna | Descrição |
|---|---|
| `Placa_Caminhao` | Placa do caminhão monitorado pelo sensor IoT |
| `Temperatura_C` | Temperatura média registrada durante a viagem (°C) |
| `Alerta_Calor` | Indicador original de alerta de calor (`SIM` ou `NAO`) |

### estufas_qualidade.csv

| Coluna | Descrição |
|---|---|
| `id_estufa` | Identificador único da estufa de origem |
| `especie_predominante` | Espécie de orquídea predominante cultivada na estufa |
| `umidade_media_pct` | Umidade média registrada na estufa (%) |
| `responsavel_tecnico` | Nome do responsável técnico pela estufa |
| `indice_qualidade_lote` | Índice de qualidade do lote produzido (escala de 0 a 1) |

## Principais Descobertas

Ao todo, foram transportadas **55.425 orquídeas**, das quais **6.349 foram perdidas** — uma taxa geral de perda de aproximadamente **11,5%**. A temperatura registrada nos caminhões variou entre uma média de **27°C** e um pico de **34°C**, ultrapassando em alguns casos o limite de 28°C considerado seguro para o transporte das flores. Olhando apenas o número absoluto de cancelamentos por estado, **São Paulo foi o estado com mais ocorrências**.

### Indo além: taxa de cancelamento por estado

Para complementar essa análise, decidi ir além do que o case pedia e investigar os cancelamentos sob uma outra perspectiva: em vez de olhar apenas a quantidade absoluta de cancelamentos, calculei a **taxa de cancelamento por estado** (cancelados ÷ total de cargas daquele estado), utilizando `groupby`. Essa métrica considera o volume de operação de cada estado, permitindo uma comparação proporcional entre eles:

| Estado | Taxa de cancelamento |
|---|---|
| MG | 22,2% |
| PR | 18,8% |
| SC | 15,8% |
| RJ | 15,6% |
| SP | 7,8% |

Sob essa ótica, **MG se destaca como o estado com a maior taxa de cancelamento**, enquanto **SP, apesar de aparecer com mais ocorrências absolutas, apresenta a menor taxa relativa** — um reflexo do seu volume de operação maior.

Essa análise adicional reforça a importância de olhar os dados sob diferentes perspectivas (absoluta e relativa) para enriquecer o diagnóstico do negócio.

## Abordagem

O projeto seguiu uma sequência simples de investigação: primeiro carreguei os três arquivos CSV e explorei os campos de cada um para entender a estrutura dos dados disponíveis. Em seguida, criei a coluna `status_temperatura`, classificando cada registro de temperatura como "Alerta" ou "OK" através de um laço de repetição (`for`) combinado com uma estrutura condicional (`if`/`else`) — essa etapa foi feita propositalmente de forma manual, mesmo já existindo uma coluna semelhante (`Alerta_Calor`) no CSV original, como forma de praticar manipulação de DataFrames e estruturas de repetição.

Com os dados organizados, calculei as métricas solicitadas pelo case: total de orquídeas transportadas, total de perdas, temperatura máxima e média, e o estado com mais ocorrências de cancelamento.

Ao observar que São Paulo aparecia tanto com muitas entregas quanto com muitos cancelamentos, fiquei curioso para entender essa relação sob outra perspectiva: será que SP realmente tinha o pior desempenho, ou isso era apenas reflexo do seu volume de operação? Essa curiosidade me levou a calcular a taxa de cancelamento por estado, complementando a análise original com uma investigação por iniciativa própria.

Projeto desenvolvido durante o bootcamp de Análise de Dados da Generation Brasil. Este repositório deve continuar evoluindo à medida que eu aprofundar a análise com novas perguntas sobre os dados.

## Como Rodar o Projeto

Este projeto foi desenvolvido utilizando o Google Colab, então a forma mais simples de executá-lo é abrindo o notebook diretamente no navegador, sem necessidade de instalação local.

### Opção 1 — Abrir direto no Google Colab (recomendado)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/LucasArtoni1983/orchid-log-analise-dados/blob/main/orchidLog_case.ipynb)

1. Clique no botão acima para abrir o notebook diretamente no Google Colab.
2. No Colab, faça upload dos três arquivos CSV da pasta `dados/` para o ambiente de execução (ou monte seu Google Drive, se preferir manter os arquivos lá).
3. Execute as células em sequência (Runtime > Run all).

### Opção 2 — Rodar localmente

```bash
# Clone o repositório
git clone https://github.com/LucasArtoni1983/orchid-log-analise-dados.git
cd orchid-log-analise-dados

# Instale as dependências
pip install pandas jupyter

# Abra o notebook
jupyter notebook orchidLog_case.ipynb
```

### Dependências

- Python 3.x
- pandas
