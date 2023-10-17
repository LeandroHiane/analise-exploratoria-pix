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

Outro jeito de ver itens faltantes é através do `isnull()`:
```python
data_treat.isnull().sum()
```
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/c5d79d74-2526-4a59-8e71-08b49063ba1f)

Por se tratar de dados de série temporal, provavelmente não teremos linhas repetidas. Mas, podemos verificar:
```python
data_treat.duplicated().sum()
```
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/600be907-c5c6-46e2-8fb1-54baf76e89cc)

Também é importante buscar por outliers e o gráfico boxplot é um jeito rápido de notá-los:
```python
ax = sns.boxplot(data_treat, orient = 'h')
ax.figure.set_size_inches(12, 8)
ax
```
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/e380d83b-2d9d-41b5-8fd4-72661c920b46)

Podemos ver que as variáveis `Boleto`, `DOC`, `TEC` e `TED` apresentaram outliers. Vamos olhar com detalhes para elas:
```python
ax = sns.boxplot(data_treat[['Boleto', 'DOC', 'TEC', 'TED']], orient = 'h')
ax.figure.set_size_inches(12, 8)
ax
```
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/b01c7683-0cdf-4a4e-97b5-5ee0ef06c41e)

O primeiro ímpeto é sempre limpar os outliers, principalmente se o objetivo for a elaboração de modelos estatísticos. Porém, primeiramente é necessário entender a causa dos outliers no dataset, que pode ser erro de digitação, separador de milhares ou decimais inconsistentes, sazonalidade do negócio, mudanças abruptas causadas por eventos externos, etc.

Assim, vamos dar uma olhada no comportamento de cada uma delas ao longo do tempo:
```python
data_treat.plot(subplots = True, figsize = [14, 20])
```
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/4635e443-5391-4c28-a8af-192ed8d409c5)
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/e2b6781f-7b49-451f-8174-a64ec29e307c)
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/f03b74ac-2d29-4647-bce1-0494e64bd7c7)
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/e623541c-2f79-40d3-8265-cc50b5f7ff2d)

Se admitirmos como premissa que a base oficial do BACEN não contém erros de digitação ou formatação, nenhuma das variáveis apresenta comportamento anormal além da própria variação causada pelo ambiente de negócio, o que é relevante para a análise do nosso questionamento. Portanto, vamos manter os outliers no dataset.

Agora sim, podemos utilizar a matriz de correlação para observar as relações entre as variáveis estudadas:
```python
data_treat.corr(numeric_only = True)
```
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/3d5b6ef4-57a6-4735-b4d0-ad00032a9246)

Ok, não é o melhor jeito de observar essa matriz. Vamos plotar em um gráfico:
```python
sns.heatmap(data_treat.corr(numeric_only = True), annot = True, cmap = "Blues")
```
![image](https://github.com/LeandroHiane/analise-exploratoria-pix/assets/90648655/d834b408-3355-4efc-beb5-63a72cb18060)

Cada número representa o coeficiente de correlação entre as variáveis da interseção em que o coeficiente se encontra. Por exemplo, o valor `0,29` no segundo quadrado, na interseção entre as variáveis `Pix` e `Boleto` representa o coeficiente de correlação entre essas variáveis.

Resumidamente, quanto mais próximo de 1 é o valor, mais diretamente as variáveis se relacionam. Ou seja, se uma aumenta, a outra também aumenta. Se uma reduz, a outra também reduz. Por isso a interseção de uma variável com ela mesma é sempre 1, pois, naturalmente, apresentam relação direta perfeita.

O inverso é válido. Quanto mais próximo de -1 é o valor, mais inversamente as variáveis se relacionam. Ou seja, se uma aumenta, a outra reduz. Se uma reduz, a outra aumenta.

A partir da análise da correlação, consegumimos responder ao nosso questionamento:
> [!NOTE]
> O aumento expressivo da quantidade de transações realizadas por Pix acontece somente pela indisponibilidade de outros meios de transferência e pagamentos para parcela considerável da população brasileira ou o Pix avança sobre outros meios existentes?

Provavelmente, pela representatividade dos valores das variáveis na série temporal, o Pix se mostrou importante ferramenta de inclusão financeira, atingindo parte da população que não dispunha de outros meios de transferência ou pagamento.

Porém, os altos coeficientes de correlação negativos entre a variável dependente `Pix` e as variáveis explicativas `DOC`, `Cheque` e `TED` nos mostram que o Pix também avançou na preferência de parcela da população que já dispunha de meios de transferência ou pagamento "tradicionais". Quanto mais o Pix foi ganhando adesão no uso por parte dos brasileiros, mais esses outros meios foram perdendo espaço.
