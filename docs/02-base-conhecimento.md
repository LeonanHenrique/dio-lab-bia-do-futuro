# Base de Conhecimento

## Dados Utilizados

| Arquivo | Formato | Para que serve no Salomão |
|---------|---------|---------------------|
| `historico_atendimento.csv` | CSV | Contextualizar interações anteriores ou seja dar continuidade ao atendimento de forma mais eficiente. |
| `perfil_investidor.json` | JSON | Personalizar as explicações dobre as duvidas e nessecidades e aprendizado dos clientes. |
| `produtos_financeiros.json` | JSON | Conhecer os produtos disponiveis para que eles possam ser ensinado ao cliente. |
| `transacoes.csv` | CSV | Analisar padrão de gastos do cliente e usar essas informações de forma didatica.|

---

## Adaptações nos Dados

> Você modificou ou expandiu os dados mockados? Descreva aqui.

O produto Fundo Imobiliario (FII) substituiu o fundo Multimercado, pois pessoalmente me sinto mais confiante em usar apenas produtos financeiros que eu coneheço assim poderei validar as  respostas do salomão de forma acertiva.

---

## Estratégia de Integração

### Como os dados são carregados?
> Descreva como seu agente acessa a base de conhecimento.

Existem duas possibilidades, injetar os dados diretamente no prompt(Ctrl + C, Ctrl + V) ou carregar os arquivos via codigo, como no exemplo abaixo.

```python

import pandas as pd
import json

perfil = json.load(open('./data/perfil_investidor.json'))
transacoes = pd.read_csv('./data/transacoes.csv')
historico = pd.read_csv('./data/historico_atendimento.csv')
produtos = json.load(open('./data/produtos_financeiros.json'))
---
````
### Como os dados são usados no prompt ?
> Os dados vão no System prompt? São consultados dinamicamente?

Para simplificar, podemos simplesmente "Injetar" os dados em nosso prompt, garantindo que o agente tenha melhor contexto possivel. Lembrando que, em soluções mais robustas, o ideal é que essas informações sejam carregadas dinamicamente para que possamos ganhar flexibilidade.

````text
DADOS DO CLIENTE E PERFIL:
{
  "nome": "João Silva",
  "idade": 32,
  "profissao": "Analista de Sistemas",
  "renda_mensal": 5000.00,
  "perfil_investidor": "moderado",
  "objetivo_principal": "Construir reserva de emergência",
  "patrimonio_total": 15000.00,
  "reserva_emergencia_atual": 10000.00,
  "aceita_risco": false,
  "metas": [
    {
      "meta": "Completar reserva de emergência",
      "valor_necessario": 15000.00,
      "prazo": "2026-06"
    },
    {
      "meta": "Entrada do apartamento",
      "valor_necessario": 50000.00,
      "prazo": "2027-12"
    }
  ]
}

TRASAÇÕES DO CLIENTES:
data,descricao,categoria,valor,tipo
2025-10-01,Salário,receita,5000.00,entrada
2025-10-02,Aluguel,moradia,1200.00,saida
2025-10-03,Supermercado,alimentacao,450.00,saida
2025-10-05,Netflix,lazer,55.90,saida
2025-10-07,Farmácia,saude,89.00,saida
2025-10-10,Restaurante,alimentacao,120.00,saida
2025-10-12,Uber,transporte,45.00,saida
2025-10-15,Conta de Luz,moradia,180.00,saida
2025-10-20,Academia,saude,99.00,saida
2025-10-25,Combustível,transporte,250.00,saida

HISTORICO DE ATENDIMENTO DO CLIENTES:
data,canal,tema,resumo,resolvido
2025-09-15,chat,CDB,Cliente perguntou sobre rentabilidade e prazos,sim
2025-09-22,telefone,Problema no app,Erro ao visualizar extrato foi corrigido,sim
2025-10-01,chat,Tesouro Selic,Cliente pediu explicação sobre o funcionamento do Tesouro Direto,sim
2025-10-12,chat,Metas financeiras,Cliente acompanhou o progresso da reserva de emergência,sim
2025-10-25,email,Atualização cadastral,Cliente atualizou e-mail e telefone,sim

PRODUTOS DISPONIVEIS PARA ENSINO:
[
  {
    "nome": "Tesouro Selic",
    "categoria": "renda_fixa",
    "risco": "baixo",
    "rentabilidade": "100% da Selic",
    "aporte_minimo": 30.00,
    "indicado_para": "Reserva de emergência e iniciantes"
  },
  {
    "nome": "CDB Liquidez Diária",
    "categoria": "renda_fixa",
    "risco": "baixo",
    "rentabilidade": "102% do CDI",
    "aporte_minimo": 100.00,
    "indicado_para": "Quem busca segurança com rendimento diário"
  },
  {
    "nome": "LCI/LCA",
    "categoria": "renda_fixa",
    "risco": "baixo",
    "rentabilidade": "95% do CDI",
    "aporte_minimo": 1000.00,
    "indicado_para": "Quem pode esperar 90 dias (isento de IR)"
  },
  {
    "nome": "Fundo Imobiliario (FII)",
    "categoria": "fundo",
    "risco": "medio",
    "rentabilidade": "Investimento Imobiliário (FIIs) no Brasil costumam apresentar uma rentabilidade anual média de aproximadamente 6% a 12% ao ano",
    "aporte_minimo": 100.00,
    "indicado_para": "Perfil moderado que busca diversificação e renda recorente mensal"
  },
  {
    "nome": "Fundo de Ações",
    "categoria": "fundo",
    "risco": "alto",
    "rentabilidade": "Variável",
    "aporte_minimo": 100.00,
    "indicado_para": "Perfil arrojado com foco no longo prazo"
  }
]


### Exemplo de Contexto Montado

> Mostre um exemplo de como os dados são formatados para o agente.

```
Dados do Cliente:
- Nome: João Silva
- Perfil: Moderado
- Saldo disponível: R$ 5.000

Últimas transações:
- 01/11: Supermercado - R$ 450
- 03/11: Streaming - R$ 55
...
````
###Exemplo do contexto montado

O exemplo de contexto montado abaixo se baseia nos dados originais da base de conhecimento, mas os sintetisa deixando apenas as informações mais relevantes, utilizando assim o consumo de tokens. entretanto, vale lembrar que mais importante do que economizar tokens e ter todas as informações relevantes disponiveis em seu contexto.
```

Você é o agente financeiro Salomão.

A seguir estão as informações do cliente que devem ser utilizadas para responder às perguntas.

PERFIL DO CLIENTE
- Nome: João Silva
- Perfil: Moderado
- Objetivo: Construir reserva de emergência
- Patrimônio: R$ 15.000

HISTÓRICO DE ATENDIMENTO
- Já perguntou sobre CDB.
- Já recebeu explicação sobre Tesouro Selic.
- Acompanhou a meta da reserva de emergência.

TRANSAÇÕES
- Receita mensal: R$ 5.000
- Gastos com moradia: R$ 1.380
- Gastos com alimentação: R$ 570
- Gastos com transporte: R$ 295
...

PRODUTOS DISPONÍVEIS
- Tesouro Selic
- CDB Liquidez Diária
- LCI/LCA
- Fundo Imobiliário (FII)
- Fundo de Ações

Responda sempre considerando essas informações e adapte as recomendações ao perfil do cliente.
