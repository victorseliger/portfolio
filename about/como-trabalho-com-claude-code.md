# Como eu trabalho com Claude Code

Minha forma de construir software com IA não é "pedir código e colar". É um **processo de
engenharia** em que o Claude Code é um multiplicador — e a especificação é o que faz a diferença.

## A filosofia: a spec é metade do trabalho

A maior parte das dores de negócio que resolvo não são problemas de "como codar", e sim de
**"o que exatamente precisa ser feito"**. Por isso invisto pesado em transformar conhecimento
disperso (planilhões, reuniões, regras tácitas) em **especificação executável e versionável** antes
de escrever a primeira linha de implementação.

## O fluxo

```
1. Pesquisa & reúso   → existe algo pronto/battle-tested? (libs, padrões, projetos)
2. Especificação      → dor manual → regras explícitas → modelo de dados → casos de borda
3. Implementação      → em camadas, determinística, testável (TDD quando faz sentido)
4. Revisão            → code review (qualidade + segurança) antes de confiar no resultado
5. Verificação        → backtest / amarração com a fonte de verdade
```

### 1. Pesquisa & reúso primeiro
Antes de escrever código novo, procuro implementações e padrões já provados. Reescrever o que já
existe é desperdício.

### 2. Especificação antes de código
Mapeio cada regra implícita (de uma planilha, de uma call) para uma regra **explícita**. Em vários
projetos, isso significou tirar a lógica de dentro de fórmulas de célula e colocá-la em **SQL
declarativo** — rastreável, revisável, auditável.

### 3. Implementação em camadas
Pipelines de dados que eu construo seguem camadas isoladas (`raw → staging → fact → aggregate →
matching → output`). Cada etapa é materializável e inspecionável sozinha — o oposto da "planilha
mágica" que ninguém entende.

### 4. Revisão de qualidade e segurança
Código gerado por IA **passa por revisão** como qualquer código. Segurança em especial: segredos
nunca entram no repositório, dados produtivos ficam fora, e entradas são validadas nas bordas.

### 5. Verificação contra a realidade
Não confio no output só porque "rodou". Faço **backtest** contra um período real e **amarro com a
fonte de verdade** (razão, balancete, golden set) antes de considerar pronto.

## Onde uso IA em produção

- **Conciliação de dados**: tradução de processos manuais em pipelines SQL/DuckDB determinísticos.
- **Automação**: orquestração com n8n + backends Python para fluxos que escalam.
- **IA aplicada**: visão computacional via LLM, sistemas multi-agente (PydanticAI), e o próprio
  Claude Code como ferramenta central de desenvolvimento.

## Princípios que carrego

- **Imutabilidade e determinismo** > efeitos colaterais escondidos.
- **Muitos arquivos pequenos e coesos** > poucos arquivos gigantes.
- **Erros tratados explicitamente** > falhas silenciosas.
- **Segredos fora do código, sempre** — sem exceção.

---

> Este portfólio é, ele mesmo, um exemplo do processo: cada case foi escrito a partir do
> entendimento real dos projetos, **anonimizado por design**, sem vazar nada confidencial.
