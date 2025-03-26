# Dash---Taxa-de-Recompra
📊 Documentação do Dashboard de Taxa de Recompra, contendo estrutura de tabelas, KPIs e medidas DAX. O projeto analisa recompra de clientes, taxa de fidelização, receita e ticket médio. Desenvolvido em Power BI/DAX com dados de um ERP. Contribuições são bem-vindas! 🚀

# Documentação - Dashboard Taxa de Recompra

## Bases de Dados Utilizadas

- **Fonte**: sistema.erp.com.br
- **Fluxo de Dados**: Base ERP

## Tabelas e Colunas

### Lojas

- `cod_loja`
- `loja`
- `rede`
- `ordem_rede`
- `ordem_loja`
- `Ordem`

### dCalendário

- `Data`
- `Ano`
- `Mês`
- `NomeMês`
- `Trimestre`

### ClientesERP

- `CPF/CNPJ`
- `NomeCompleto`
- `Tickets`
- `diasentrevendas`
- `idfaixa`
- `idtickets`

### DiasEntreVendas

- `cpf`
- `ano`
- `mês`
- `cod_lojas`
- `tickets`
- `receita_total`
- `qtde_total`
- `diasentrevendas`
- `idfaixa`
- `idtickets`
- `data`

### Vendas

- `data`
- `cod_lojas`
- `cpf`
- `controle`
- `faturamento`
- `qtde`
- `idfaixa`
- `idtickets`

## KPI’s e Medidas DAX

### Quantidade de Clientes

Quantidade de clientes que realizaram compras.

```DAX
Qtde Clientes =
CALCULATE(
    DISTINCTCOUNT(Vendas[cpf]), -- Conta a quantidade distinta de clientes (CPF)
    Vendas[controle] = 2 -- Considera apenas transações de venda
)
```

### Quantidade de Clientes Ano Anterior

Calcula a quantidade de clientes no mesmo período do ano anterior.

```DAX
Qtde Clientes LY =
    CALCULATE(
        [Qtde Clientes],
        SAMEPERIODLASTYEAR(dCalendario[Data]) -- Utiliza o mesmo período do ano anterior
    )
```

### Crescimento da Quantidade de Clientes

Calcula a taxa de crescimento da quantidade de clientes em relação ao ano anterior.

```DAX
Crescimento Qtde Clientes =
    DIVIDE(
        [Qtde Clientes], [Qtde Clientes LY]
    ) - 1
```

### Taxa de Recompra

Percentual de clientes que realizaram mais de uma compra.

```DAX
% Taxa de Recompra =
    DIVIDE([Clientes por Faixa], [Qtde Clientes])
```

### Taxa de Recompra Ano Anterior

Calcula a taxa de recompra no mesmo período do ano anterior.

```DAX
Taxa de Recompra LY =
    CALCULATE(
        [% Taxa de Recompra],
        SAMEPERIODLASTYEAR(dCalendario[Data])
    )
```

### Crescimento da Taxa de Recompra

Calcula o crescimento da taxa de recompra em relação ao ano anterior.

```DAX
Crescimento Taxa de Recompra =
    [% Taxa de Recompra] - [Taxa de Recompra LY]
```

### Clientes Fidelizados

Quantidade de clientes que já haviam comprado antes do período analisado.

```DAX
Clientes Fidelizados =
    [Qtde Clientes] - [Novos Clientes] -- Clientes totais menos novos clientes
```

### Novos Clientes

Calcula quantos clientes compraram pela primeira vez no período analisado.

```DAX
Novos Clientes =
    COUNTROWS(
        FILTER(
            ADDCOLUMNS(VALUES(Vendas[cpf]),
            "Novos Clientes",
            CALCULATE(COUNTROWS(Vendas),FILTER(ALL(dCalendario),dCalendario[Data]<MIN(dCalendario[Data])))),
            [Novos Clientes] = 0) -- Mantém apenas os clientes que não possuem compras anteriores
    )
```

### Receita

Soma do faturamento total das vendas realizadas.

```DAX
Receita Por Faixa =
CALCULATE(
    SUM(DiasEntreVendas[receita_total]), -- Soma o total de receita
    USERELATIONSHIP(Faixas[id], ClientesERP[idfaixa]),
    USERELATIONSHIP(DiasEntreVendas[cod_lojas], Lojas[cod_loja])
)
```

### Receita Ano Anterior

Calcula a receita total no mesmo período do ano anterior.

```DAX
Receita LY =
CALCULATE(
    [Receita Por Faixa],
    SAMEPERIODLASTYEAR(dCalendario[Data])
)
```

### Ticket Médio

Média de faturamento por transação (ticket).

```DAX
Ticket Médio = DIVIDE([Receita Por Faixa], [Tickets])
```

## Tabelas Criadas

### Faixas

- `id`
- `Min`
- `Max`
- `Faixa`

### Faixas de Tickets

- `idTickets`
- `Faixa`
- `LimiteInferior`
- `LimiteSuperior`

---

Essa documentação detalha as tabelas, colunas e medidas DAX utilizadas no dashboard de Taxa de Recompra, facilitando a manutenção e entendimento do fluxo de dados.

