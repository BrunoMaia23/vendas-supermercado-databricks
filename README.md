# Vendas de supermercado no Databricks

MVP de engenharia de dados da pós-graduação, feito em abril de 2025. É um notebook do Databricks que
leva o dataset público "Supermarket Sales", do Kaggle, por três camadas (bronze, silver e gold) e
responde a perguntas de negócio com Spark SQL. O dataset tem 1.000 vendas de três filiais de uma rede
em Mianmar, de janeiro a março de 2019.

O notebook, com as saídas da execução, é o [vendas_supermercado.ipynb](vendas_supermercado.ipynb).

## Camadas

- **Bronze:** lê o CSV com inferência de schema e grava do jeito que veio, em Parquet.
- **Silver:** converte a data; cria dia da semana, mês, ano e margem (lucro bruto sobre o total);
  passa algumas colunas para snake_case; grava em Parquet.
- **Gold:** uma view temporária sobre a silver, consultada com `%sql`.

## Perguntas

| Pergunta | Resultado |
|---|---|
| Linha de produto com maior faturamento | Food and beverages: 56.144,84 em 174 vendas. As seis linhas ficam entre 49 mil e 56 mil |
| Filial que mais fatura | C (Naypyitaw), com 110.568,71. A e B empatam perto de 106,2 mil |
| Forma de pagamento | Em número de vendas, carteira digital (345) e dinheiro (344) empatam. Em valor, dinheiro fica na frente: 112.206,57 |
| Tipo de cliente e gênero | Mulheres com cartão de membro têm o maior faturamento (88.146,94) e o maior ticket médio (337,73) |
| Mês | Janeiro, com 116.291,87. Fevereiro foi o mais fraco |
| Dia da semana | Sábado: 164 vendas e 56.120,81 |
| Avaliação por linha de produto | Food and beverages tem a maior nota média, 7,11. Todas ficam entre 6,84 e 7,11 |
| Margem por filial | 4,76% nas três filiais (explicação abaixo) |

## Limitações

- A margem é a mesma em todas as filiais porque, neste dataset, o lucro bruto é sempre igual ao
  imposto de 5%. Então a margem é 5/105 = 4,76% em qualquer venda, e a pergunta não separa nada.
- A coluna `Datetime` da silver ficou nula. O `inferSchema` leu a hora como timestamp, com a data do
  dia em que o notebook rodou, e a junção de data com hora não bateu com o formato esperado.
- O arredondamento dos valores foi feito num DataFrame que não é gravado nem consultado. A silver
  gravada não está arredondada.
- As camadas são arquivos Parquet, não tabelas Delta, e a gold é uma view temporária, que só existe
  enquanto a sessão está aberta.

## Rodando

Importe o notebook no Databricks e envie o CSV do dataset. O notebook lê o arquivo em
`dbfs:/FileStore/supermarket_sales___Sheet1.csv`; se ele for parar em outro caminho, ajuste a célula
da camada bronze. Depois é só rodar as células na ordem.
