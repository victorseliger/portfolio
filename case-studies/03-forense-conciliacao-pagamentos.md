# Forense de conciliação: caçando registros de liquidação ausentes

> **Setor:** Multinacional de **cosméticos / varejo** (operação internacional)
> **Tema técnico:** **Matching forense** com normalização multivariante · cruzamento exaustivo · diagnóstico de causa raiz
> **Stack:** Python · pandas · parsing de arquivo de largura fixa · Excel

---

## A dor manual

A liquidação diária de cartões enviada pelo **processador de pagamentos** não batia com o **razão
de pedidos interno**. O time esperava um punhado de transações em um dia específico (algumas a
débito, outras a crédito), mas o arquivo recebido não trazia os créditos esperados — e o razão
interno tinha **dezenas de milhares de linhas**, tornando a conferência manual inviável.

A pergunta de negócio era simples e cara: **"onde estão as transações que faltam, e por quê?"**

## A especificação / abordagem

Tratei como um **problema forense de dados**, não como uma conciliação rotineira:

1. **Extração total de campos**: parser do arquivo de largura fixa do processador, abrindo todos os
   ~36 campos de cada registro (em vez de olhar só os "óbvios").
2. **Normalização multivariante**: como identificadores divergem entre sistemas, cada chave foi
   testada em **5 variantes** — original, sem zeros à esquerda, com sufixo, só dígitos, e formato
   monetário.
3. **Cruzamento exaustivo** contra o razão interno (dezenas de milhares de linhas), medindo o
   casamento em cada variante.
4. **Relatório de variância**: quantificação do que casou, do que não casou e da assinatura do que
   faltava.

## Diagnóstico (o entregável real)

O cruzamento exaustivo mostrou que **todos os registros recebidos eram débitos** — nenhum crédito.
Conclusão: não era erro de conciliação, e sim um **gap de integração** — o processador envia débitos
e créditos em **arquivos separados**. A recomendação foi solicitar o arquivo de créditos
correspondente, em vez de "caçar" linhas que nunca chegaram.

Esse é o valor de uma boa investigação: transformar um "os números não batem" em uma **causa raiz
acionável**.

## Arquitetura & stack

- **Python + pandas** para parsing, normalização e cruzamento.
- Parser de **arquivo de largura fixa** orientado a layout (campo a campo).
- Geração de **Excel multi-aba** (detalhe, por data, resumo) para evidência e auditoria.

## O papel do Claude Code

- Acelerou a escrita do parser de largura fixa e das funções de normalização por variante.
- Ajudou a estruturar o **cruzamento exaustivo** e o relatório de variância de forma legível.
- Permitiu iterar rápido sobre hipóteses ("e se a chave estiver com zeros à esquerda? e com
  sufixo?") até isolar a causa raiz.

## Resultado / impacto

- Um impasse de fechamento ("faltam transações") foi resolvido com **diagnóstico de causa raiz**
  em vez de retrabalho manual.
- Padrão de **matching forense reutilizável** (normalização multivariante + cruzamento exaustivo +
  variância) aplicável a qualquer conciliação de pagamentos.

---

> Case anonimizado. Identificadores de pedido/consultor, valores, e nomes de processador/sistema
> interno foram omitidos. O foco é a **metodologia forense de conciliação**.
