# Otimização & Compressão de Prompts: Eficiência e Custo

## Resumo Executivo

*   **O Problema do Custo:** O custo de operar aplicações de LLM em escala é diretamente proporcional ao número de tokens processados (tanto na entrada quanto na saída). Prompts longos e detalhados, especialmente em sistemas RAG com muito contexto, podem se tornar proibitivamente caros.
*   **Otimização de Prompts:** É o processo de refinar um prompt para alcançar o mesmo resultado (ou melhor) com o menor número de tokens possível. Não se trata apenas de encurtar, mas de aumentar a "densidade de informação" de cada token.
*   **Compressão de Prompts:** É uma técnica mais avançada onde algoritmos são usados para remover tokens "desnecessários" de um prompt antes de enviá-lo ao LLM, preservando a informação semântica essencial. O objetivo é reduzir o comprimento do prompt sem degradar a qualidade da resposta.
*   **Técnicas de Compressão:**
    *   **LLMLingua:** Uma abordagem popular que usa um LLM menor (e mais barato) para analisar o prompt e prever quais tokens são menos importantes para a tarefa, removendo-os.
    *   **Selective Context:** Em sistemas RAG, em vez de enviar todos os chunks de contexto recuperados, usa-se um re-ranker para selecionar apenas os trechos mais relevantes e concisos.
    *   **Gist Tokens:** Uma técnica de fine-tuning onde o modelo aprende a comprimir um prompt longo em um conjunto de "tokens de essência" (gist tokens) virtuais, que podem ser cacheados e reutilizados.
*   **Otimização Manual vs. Automática:**
    *   **Manual:** Envolve a reescrita cuidadosa do prompt, removendo palavras de preenchimento, usando abreviações e reestruturando frases para serem mais diretas (ver seção de Anti-padrões).
    *   **Automática (APO - Automated Prompt Optimization):** Usa algoritmos de busca (como otimização bayesiana ou algoritmos genéticos) para explorar o "espaço de prompts" e descobrir automaticamente as formulações mais eficientes e eficazes. O LLM é usado para gerar e avaliar variações de prompts.
*   **Prompt Caching:** Uma estratégia de otimização crucial. Se a primeira parte de um prompt é sempre a mesma (ex: o prompt de sistema e os exemplos few-shot), ela pode ser cacheada. Em chamadas subsequentes, apenas a parte nova do prompt (a pergunta do usuário) é enviada, economizando tokens e reduzindo a latência.
*   **Trade-off Custo-Qualidade:** A otimização de prompts é um exercício de equilíbrio. Uma compressão muito agressiva pode economizar custos, mas degradar a qualidade da resposta. É essencial medir o impacto da otimização usando um pipeline de avaliação robusto.

## Para que serve / Resultados esperados

A otimização e compressão de prompts são cruciais para a viabilidade econômica de aplicações de LLM em larga escala. Elas permitem que as empresas reduzam os custos operacionais, melhorem a latência (prompts mais curtos são processados mais rapidamente) e, em alguns casos, até melhorem a qualidade da resposta ao remover ruído do contexto.

Os resultados esperados são:

*   **Redução de Custos:** Diminuição significativa nos custos de API devido ao menor número de tokens de entrada e saída.
*   **Menor Latência:** Melhora na velocidade de resposta da aplicação, proporcionando uma melhor experiência do usuário.
*   **Aumento da Janela de Contexto Efetiva:** Ao comprimir o contexto, é possível encaixar mais informações relevantes na janela de contexto finita do modelo.
*   **Melhora na Qualidade (às vezes):** Remover informações irrelevantes ou redundantes do prompt pode ajudar o modelo a focar nos detalhes importantes, reduzindo a confusão e melhorando a precisão.
*   **Viabilidade de Casos de Uso:** Torna viáveis aplicações que seriam muito caras de operar com prompts não otimizados.

## Boas práticas

| Prática | Descrição | Exemplo de Aplicação |
| :--- | :--- | :--- |
| **Medir Antes de Otimizar** | Não otimize cegamente. Primeiro, estabeleça um baseline de custo e qualidade com seu prompt original e seu pipeline de avaliação. | "Nosso prompt v1 custa em média $0.05 por chamada e atinge 90% de groundedness. O objetivo da otimização é reduzir o custo em 20% sem que a groundedness caia abaixo de 88%." |
| **Comece com Otimização Manual** | Antes de usar ferramentas complexas, aplique os princípios de evitar anti-padrões. Reescreva o prompt para ser claro, direto e sem palavras desnecessárias. | Substitua "Você poderia, por favor, me fornecer uma lista de..." por "Liste...". Use abreviações comuns se o modelo as entender bem. |
| **Use Ferramentas de Compressão** | Para sistemas RAG com contextos longos, use ferramentas como LLMLingua para comprimir o contexto recuperado antes de enviá-lo ao LLM. | Passe os chunks de texto recuperados por uma função `compress_context(text)` que implemente a compressão. |
| **Implemente Prompt Caching** | Verifique se sua plataforma de LLM (ou o framework que você usa, como LangChain) suporta caching. Ative-o para prompts com partes estáticas grandes. | O prompt de sistema e os exemplos few-shot são cacheados. Apenas a query do usuário é processada a cada nova chamada. |
| **Otimização de Tokens de Saída** | Otimize não apenas a entrada, mas também a saída. Instrua o modelo a ser conciso. | Adicione à sua instrução: `"Seja breve em sua resposta."`, `"Responda apenas com o JSON, sem preâmbulo."`. |
| **Escolha do Modelo Certo** | Use o modelo mais simples (e barato) que possa realizar a tarefa com a qualidade necessária. Não use GPT-4o para uma tarefa que o Gemini 2.5 Flash pode fazer. | Use um sistema de roteamento (router) que direcione queries simples para modelos mais baratos e queries complexas para modelos mais caros. |
| **Avaliação Contínua** | Sempre re-avalie a qualidade da saída após aplicar uma técnica de otimização ou compressão para garantir que não houve uma regressão inaceitável. | Após comprimir os prompts, execute o mesmo conjunto de testes de avaliação e compare as pontuações de qualidade com o baseline. |

## Como fazer (passo a passo)

**Implementando um Fluxo de Otimização com Compressão:**

1.  **Estabelecer o Baseline:**
    *   Pegue seu prompt não otimizado e seu conjunto de testes de avaliação.
    *   Execute a avaliação e registre o custo médio por chamada e as pontuações de qualidade (ex: LLM-as-Judge).

2.  **Otimização Manual:**
    *   Revise o prompt de sistema e as instruções, removendo qualquer palavra ou frase que não seja estritamente necessária.
    *   Reformule frases para serem mais diretas. Use listas em vez de parágrafos longos.
    *   Re-execute a avaliação. Se a qualidade se manteve e o custo caiu, este é seu novo baseline.

3.  **Implementar Compressão de Contexto (para RAG):**
    *   Integre uma biblioteca como `llmlingua` no seu pipeline de RAG.
    *   Após a etapa de recuperação (retrieval), mas antes da geração, passe os chunks de contexto recuperados pela função de compressão.
    *   *Exemplo de Código (conceitual):*
        ```python
        retrieved_docs = retrieve_documents(query)
        compressed_context = llmlingua.compress(retrieved_docs)
        response = generate_answer(query, compressed_context)
        ```

4.  **Avaliar o Impacto da Compressão:**
    *   Execute o pipeline de avaliação com a compressão ativada.
    *   Compare o novo custo e as novas pontuações de qualidade com o baseline.
    *   Ajuste o nível de compressão (ex: a taxa de compressão no LLMLingua) para encontrar o ponto ideal entre economia de custo e manutenção da qualidade.

5.  **Considerar Otimização Automática (Avançado):**
    *   Se a performance do prompt for extremamente crítica, explore frameworks de APO.
    *   Esses frameworks pegam seu prompt inicial, geram dezenas de variações e as testam contra seu conjunto de avaliação para encontrar uma versão que maximize a qualidade e minimize o custo.

## Pontos de atenção / vieses / riscos (com mitigação)

| Risco/Vieses | Descrição | Mitigação |
| :--- | :--- | :--- |
| **Perda de Informação Crítica** | A compressão automática pode acidentalmente remover um detalhe crucial do prompt (a "agulha no palheiro"), levando a uma resposta incorreta. | **1. Compressão Consciente do Contexto:** Use técnicas que permitem "proteger" certas palavras-chave ou frases da compressão. **2. Avaliação Rigorosa:** Tenha um conjunto de testes que especificamente verifique se esses detalhes críticos são compreendidos e usados corretamente pelo modelo. |
| **Otimização Excessiva** | Focar tanto na redução de tokens que o prompt se torna críptico e difícil de manter e depurar por outros humanos. | **Clareza para Humanos Primeiro:** O prompt ainda deve ser legível e compreensível por um desenvolvedor. Adicione comentários se a otimização tornar o prompt muito denso. A manutenibilidade é importante. |
| **Custo da Otimização** | O processo de otimização automática (APO) pode ser caro, pois envolve muitas chamadas ao LLM para testar diferentes variações de prompt. | **Retorno sobre o Investimento (ROI):** Calcule se o custo do processo de APO será compensado pela economia de custos em produção. APO é mais adequado para aplicações de alto volume, onde pequenas economias por chamada se somam. |
| **Dependência de Ferramentas** | Ficar dependente de uma ferramenta de compressão específica pode criar um ponto de falha ou dificultar a mudança para novos modelos de LLM no futuro. | **Abstração:** Envolva a ferramenta de compressão em sua própria função ou serviço interno, para que você possa trocá-la no futuro sem ter que mudar o código principal da sua aplicação. |

## Exemplos curtos

**Exemplo 1: Otimização Manual**

*   **Antes:** `"Considerando todas as informações fornecidas, você poderia por favor gerar um resumo conciso do documento principal em cerca de 50 palavras?"` (26 tokens)
*   **Depois:** `"Resuma o documento em 50 palavras." ` (7 tokens) -> **Economia de ~73%**

**Exemplo 2: Compressão com LLMLingua (Conceitual)**

*   **Contexto Original:** `"A Geração Aumentada por Recuperação (RAG) é uma técnica para aprimorar a precisão e a confiabilidade de modelos de linguagem grandes (LLMs) com fatos de uma base de conhecimento externa. A ideia principal é que, em vez de o modelo gerar uma resposta baseada apenas em seu treinamento estático, ele primeiro recupera informações relevantes de uma fonte externa." ` (68 tokens)
*   **Contexto Comprimido (pelo LLMLingua):** `"RAG aprimora a precisão de LLMs com fatos de uma base de conhecimento externa. O modelo recupera informações relevantes antes de gerar a resposta." ` (26 tokens) -> **Economia de ~62%**

## Checklist de qualidade

- [ ] **Baseline Estabelecido:** Você mediu o custo e a qualidade do seu prompt *antes* de começar a otimizar?
- [ ] **Otimização Manual Aplicada:** Você já removeu os anti-padrões e a verbosidade desnecessária do seu prompt?
- [ ] **Avaliação Pós-Otimização:** Toda mudança de otimização foi seguida por uma execução do pipeline de avaliação para medir o impacto na qualidade?
- [ ] **Equilíbrio Custo-Qualidade:** Você encontrou o "ponto ideal" onde a economia de custos é maximizada sem uma degradação inaceitável da qualidade?
- [ ] **Prompt Caching Ativado:** O caching está sendo usado para as partes estáticas do seu prompt?
- [ ] **Otimização de Saída:** O prompt instrui o modelo a ser conciso em sua resposta?
- [ ] **Seleção de Modelo:** Você está usando o modelo mais eficiente em termos de custo para a complexidade da sua tarefa?
- [ ] **Legibilidade do Prompt:** O prompt otimizado ainda é legível e fácil de ser mantido por outros desenvolvedores?
- [ ] **Análise de ROI:** Para técnicas avançadas como APO, o custo da otimização é justificado pela economia esperada?
- [ ] **Monitoramento em Produção:** Você está monitorando os custos e a latência em produção para identificar oportunidades contínuas de otimização?

## Notas e Fontes

1.  **Jiang, H., et al. (2023). *LLMLingua: Compressing Prompts for Accelerated Inference of Large Language Models*.** arXiv preprint arXiv:2310.05736. Disponível em: [https://arxiv.org/abs/2310.05736](https://arxiv.org/abs/2310.05736). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 10/10)
2.  **Microsoft Research - LLMLingua: Innovating LLM efficiency with prompt compression.** Disponível em: [https://www.microsoft.com/en-us/research/blog/llmlingua-innovating-llm-efficiency-with-prompt-compression/](https://www.microsoft.com/en-us/research/blog/llmlingua-innovating-llm-efficiency-with-prompt-compression/). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 9/10)
3.  **Mu, J., et al. (2023). *Learning to compress prompts with gist tokens*.** Advances in Neural Information Processing Systems. Disponível em: [https://proceedings.neurips.cc/paper_files/paper/2023/hash/3d77c6dcc7f143aa2154e7f4d5e22d68-Abstract-Conference.html](https://proceedings.neurips.cc/paper_files/paper/2023/hash/3d77c6dcc7f143aa2154e7f4d5e22d68-Abstract-Conference.html). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 10/10)
4.  **Databricks Blog - Building State-of-the-Art Enterprise Agents 90x Cheaper with Automated Prompt Optimization.** Disponível em: [https://www.databricks.com/blog/building-state-art-enterprise-agents-90x-cheaper-automated-prompt-optimization](https://www.databricks.com/blog/building-state-art-enterprise-agents-90x-cheaper-automated-prompt-optimization). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 9/10)
5.  **DataCamp - Prompt Compression: A Guide With Python Examples.** Disponível em: [https://www.datacamp.com/tutorial/prompt-compression](https://www.datacamp.com/tutorial/prompt-compression). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 8/10)

