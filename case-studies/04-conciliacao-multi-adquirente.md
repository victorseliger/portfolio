# Conciliação de repasses multi-adquirente para marketplace

> **Setor:** **Marketplace de mobilidade / transporte rodoviário**
> **Tema técnico:** Parsing de **arquivos bancários de largura fixa** · modelo normalizado · matching por chave
> **Stack:** Python · pandas · Bash

---

## A dor manual

O marketplace recebe pagamentos por **dois adquirentes diferentes** (cartão de crédito e
desembolso/wallet). Cada um entrega os dados em **layout próprio de arquivo bancário** (formato de
largura fixa, tipo `.RET`). O time precisava conciliar os repasses e descobrir, todo dia, **quais
pagamentos chegaram errados, em duplicidade ou simplesmente não chegaram** — comparando layouts que
não conversam entre si.

Conferência de arquivo bancário na unha é tedioso, lento e o tipo de tarefa em que um erro vira
prejuízo financeiro direto.

## A especificação / abordagem

Padronizei os dois mundos em um **modelo único** antes de comparar:

```
adquirente A (.RET largura fixa) ┐
                                 ├─→ parser por layout ─→ modelo normalizado ─→ matching por chave ─→ divergências
adquirente B (relatório CSV)     ┘                       (referência, valor, data)
```

- **Parsers orientados a layout**: cada adquirente tem seu mapa de campos (posição/tamanho);
  o parser lê o registro de largura fixa campo a campo.
- **Normalização**: tudo converge para um esquema comum (referência, valor, data, status).
- **Matching por chave** com sinalização de: ausente, divergente em valor, e duplicado.
- **Relatório de divergências** pronto para o time financeiro agir.

## Arquitetura & stack

- **Python + pandas** para parsing, normalização e matching.
- **Bash** para orquestração de pastas (recebidos → processados → diferença).
- Estrutura de diretórios que separa **entrada bruta**, **processado** e **divergências** — simples,
  auditável e fácil de operar.

## O papel do Claude Code

- Acelerou a escrita dos parsers de largura fixa (a parte mais chata e propensa a off-by-one).
- Ajudou a desenhar o modelo normalizado comum aos dois adquirentes.
- Iteração rápida sobre os casos de divergência (valor com centavos, data em formatos distintos).

## Resultado / impacto

- Conciliação diária de **dois adquirentes** com layouts incompatíveis virou rotina automatizada.
- Divergências (faltantes/duplicados/valor) **sinalizadas automaticamente**, reduzindo risco
  financeiro e tempo de fechamento.
- Parsers de largura fixa **reutilizáveis** para novos adquirentes.

---

> Case anonimizado. Nomes de adquirente, IDs de merchant, valores e datas reais foram omitidos.
> O foco é o **padrão de parsing + normalização + matching multi-fonte**.
