# Extração e sumarização de metadados de arquivos bancários

> **Setor:** **Fintech de conciliação bancária**
> **Tema técnico:** Parsing de JSON · extração por regex · agregação com pandas
> **Stack:** Python · pandas · regex · Excel

---

## A dor manual

A operação recebia, por e-mail, **centenas de arquivos bancários** de múltiplos adquirentes e
processadores (cartão, PIX, cobrança, desembolso), com os metadados despejados em um **JSON grande**
(resposta de API). A tarefa recorrente: **filtrar os arquivos de um merchant específico**, extrair
os campos relevantes e gerar um resumo — feito na unha, registro a registro.

## A especificação / abordagem

Um utilitário pequeno e direto, mas que elimina um trabalho manual repetitivo:

1. **Parsing do JSON** de metadados (centenas de registros de arquivos).
2. **Filtro por merchant** (prefixo de nome de arquivo).
3. **Extração por regex**: identificador do emissor, data, hora e sequência embutidos no nome/campo.
4. **Agregação (pandas groupby/agg)**: resumo por data e total geral.
5. **Excel de 3 abas**: detalhe, por data e resumo geral.

## Arquitetura & stack

- **Python + pandas** para parsing, filtro e agregação.
- **regex** para extrair identificadores estruturados embutidos em strings.
- Saída **Excel multi-aba** como entregável de auditoria.

## O papel do Claude Code

- Escreveu o parser de JSON e as expressões regulares de extração rapidamente.
- Estruturou o `groupby/agg` e a geração do Excel multi-aba.

## Resultado / impacto

- Um filtro/resumo **manual e recorrente** virou um script reexecutável de segundos.
- Padrão **reutilizável** para qualquer extração de metadados bancários a partir de dumps JSON.

---

> Case anonimizado. Identificadores reais (CNPJ, IDs de merchant), nomes de emissor e arquivos
> foram omitidos. O foco é o **padrão de extração + agregação**.
