# analise-exploratoria-pix
Análise exploratória da quantidade de transações Pix em comparação com outros meios de transferência de dinheiro e meios de pagamento, utilizando estatística e Python (pandas e seaborn)

## Objeto do estudo
O Pix é um meio de pagamento eletrônico instantâneo criado pelo Banco Central do Brasil (BACEN) e lançado em meados de outubro de 2020. Desde então, é notável sua adesão por parte dos brasileiros, como demonstra o gráfico abaixo:

![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/3934d070-fb1d-4e82-ab2a-6a1ad8b58378)
Fonte: BACEN

Além disso, o Pix é reconhecido internacionalmente como importante ferramenta de inclusão financeira, como pode ser comprovado em notícias da mídia:

![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/4ee1b73f-062c-4a92-84d4-9724ea094738)
Fonte: [LinkedIn](https://www.linkedin.com/news/story/pix-%C3%A9-reconhecido-por-inclus%C3%A3o-financeira-em-pr%C3%AAmio-internacional-5773596/?utm_source=rss&utm_campaign=storylines_pt)

![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/9d3b54c2-da67-4b91-ba73-1762c3e3f633)
Fonte: [Exame](https://exame.com/economia/inclusao-financeira-com-pix-alcanca-49-milhoes-de-pessoas-ate-2021-mostra-bc/)

> [!NOTE]
> Assim, surge o questionamento: o aumento expressivo da quantidade de transações realizadas por Pix acontece somente pela indisponibilidade de outros meios de transferência e pagamentos para parcela considerável da população brasileira ou o Pix avança sobre outros meios existentes?

## Análise exploratória
O próprio BACEN disponibiliza alguns dados publicamente sobre meios de transferência e pagamentos, disponíveis aqui:
https://www.bcb.gov.br/estatisticas/spbadendos

Para responder ao questionamento, vamos utilizar duas bases de dados baixadas do link acima, disponíveis na pasta Data do projeto. A primeira base de dados disponibiliza a quantidade de transações realizadas por diversos meios de transferência em milhares, distribuídas mensalmente de outubro/2020 a agosto/2023.

Para iniciar, vamos importar as libs necessárias no projeto:
```python
import pandas as pd
import seaborn as sns
```

Para, então, importar a primeira base para análise:
```python
data = pd.read_csv('../Data/Meios_pgto_qtde.csv', sep = ';', decimal = ',')
data.head()
```
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/9cb9b8e5-dd6e-42ab-bcbf-5cea91c710dc)

É possível notar que o primeiro mês da série temporal possui valor zerado para a variável Pix. Em notícias públicas, constatamos que o Pix foi lançado em outubro/2020, mas somente em novembro/2020 foram realizadas as primeiras transações reais. Como esta é a nossa variável dependente, não faz sentido mantermos as entradas sem valores de Pix cadastrados. Vamos retirá-las:
```python
data_filter = data['Pix'] > 0
data_treat = data[data_filter]
data_treat.head()
```
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/677cb273-c64f-4b57-84a9-3d173125776e)

Vamos dar mais uma olhada no dataframe:
```python
data_treat.info()
```
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/15b88109-203f-45c8-93f6-0fcf10faecea)

Nenhum dado faltante e variáveis com formato correto para análise.

Também é importante buscar por outliers e o gráfico boxplot é um jeito rápido de notá-los:
```python
ax = sns.boxplot(data_treat, orient = 'h')
ax.figure.set_size_inches(12, 8)
ax
```
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/46261b9d-58ef-47d7-9f55-002289ea2f72)

