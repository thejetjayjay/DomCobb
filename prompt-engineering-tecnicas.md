# Prompt Engineering: Técnicas Avançadas

## Resumo Executivo

*   **Fundamentos:** A engenharia de prompts vai além de simplesmente fazer perguntas. Trata-se de estruturar a entrada de uma forma que guie o modelo de linguagem (LLM) para o resultado desejado de forma consistente e eficiente.
*   **Frameworks Estruturados (RICCE/CRISPE):** A utilização de frameworks como RICCE (Role, Input, Context, Constraints, Evaluation) ou CRISPE (Capacity, Request, Information, Style, Parameters, Examples) fornece uma base sólida para a construção de prompts robustos, garantindo que todos os aspectos da solicitação sejam considerados.
*   **Zero-Shot Prompting:** A técnica mais básica, onde o LLM é solicitado a realizar uma tarefa sem nenhum exemplo prévio. Funciona bem para tarefas gerais, mas carece de precisão para tarefas complexas ou com formatos de saída específicos.
*   **Few-Shot Prompting:** Fornecer alguns exemplos (geralmente de 1 a 5) de entradas e saídas desejadas no próprio prompt. Esta técnica melhora drasticamente a precisão e a aderência ao formato, ensinando o modelo pelo exemplo.
*   **Chain-of-Thought (CoT) Prompting:** Instruir o modelo a "pensar passo a passo" e a externalizar sua cadeia de raciocínio antes de dar a resposta final. É extremamente eficaz para problemas que exigem lógica, matemática ou raciocínio complexo.
*   **Self-Consistency:** Uma evolução do CoT, onde o modelo gera múltiplas cadeias de raciocínio para o mesmo problema e a resposta final é escolhida por uma "votação" da maioria. Aumenta a robustez e a precisão em tarefas de raciocínio.
*   **Prompt Chaining:** Dividir uma tarefa complexa em uma sequência de prompts mais simples, onde a saída de um prompt se torna a entrada do próximo. Permite a construção de fluxos de trabalho sofisticados e modulares.
*   **Meta Prompting:** Utilizar um LLM para gerar ou otimizar outros prompts, automatizando o processo de engenharia de prompts e garantindo a aplicação de boas práticas em escala.

## Para que serve / Resultados esperados

A aplicação de técnicas avançadas de engenharia de prompts serve para desbloquear o verdadeiro potencial dos modelos de linguagem, permitindo que eles executem tarefas complexas com alta precisão e confiabilidade. Em vez de tratar o LLM como uma caixa preta imprevisível, essas técnicas fornecem as ferramentas para controlar e direcionar seu comportamento.

Os resultados esperados são:

*   **Aumento da Precisão:** Redução significativa de erros, alucinações e respostas irrelevantes.
*   **Controle sobre o Formato:** Capacidade de gerar saídas em formatos estruturados e consistentes, como JSON ou XML.
*   **Resolução de Problemas Complexos:** Habilidade de resolver tarefas que exigem raciocínio lógico, planejamento e múltiplos passos.
*   **Eficiência:** Redução do número de tentativas necessárias para obter a resposta correta, economizando tempo e custos computacionais.
*   **Confiabilidade:** Construção de aplicações de IA mais robustas e previsíveis, prontas para serem integradas em sistemas de produção.

## Boas práticas

| Técnica | Descrição | Quando Usar |
| :--- | :--- | :--- |
| **Zero-Shot** | Pedido direto, sem exemplos. | Tarefas simples e diretas, conversas gerais, quando a velocidade é mais importante que a precisão. |
| **Few-Shot** | Fornecer 1-5 exemplos de entrada/saída. | Quando o formato de saída é específico, para tarefas de classificação, ou para ensinar um estilo particular. |
| **Chain-of-Thought (CoT)** | Instruir o modelo a pensar passo a passo. | Problemas de matemática, lógica, raciocínio espacial, ou qualquer tarefa que um humano resolveria em etapas. |
| **Self-Consistency** | Gerar múltiplos raciocínios (CoT) e escolher a resposta mais comum. | Tarefas críticas de raciocínio onde a precisão é a maior prioridade e o custo computacional é secundário. |
| **Prompt Chaining** | Quebrar uma tarefa grande em uma sequência de prompts menores. | Fluxos de trabalho complexos, como "pesquisar um tópico, escrever um rascunho, revisar o rascunho". |
| **Frameworks (RICCE/CRISPE)** | Usar uma estrutura para construir o prompt. | Como ponto de partida para quase todos os prompts, para garantir que nada seja esquecido. |
| **Meta Prompting** | Usar um LLM para criar ou refinar prompts. | Para escalar a criação de prompts, para otimizar prompts existentes, ou para descobrir novas formas de perguntar. |

## Como fazer (passo a passo)

1.  **Analisar a Tarefa:** Determine a complexidade da sua tarefa. É uma pergunta simples ou um problema de múltiplos passos?

2.  **Escolher a Técnica Base:**
    *   **Para tarefas simples:** Comece com **Zero-Shot Prompting**, aplicando um framework como o **RICCE** para garantir a clareza.
        *   *Exemplo (RICCE):* `(Role) Você é um tradutor. (Input) Traduza a frase 'Hello, world!'. (Context) O destino é o português do Brasil. (Constraints) Use um tom informal. (Evaluation) A tradução deve ser natural.`
    *   **Para tarefas com formato específico:** Use **Few-Shot Prompting**.
        *   *Exemplo:* `Traduza para o francês:\n'sea otter' -> 'loutre de mer'\n'plush toy' -> 'peluche'\n'cheese' ->`

3.  **Adicionar Raciocínio (se necessário):**
    *   Se a tarefa envolve lógica ou cálculo, adicione a instrução do **Chain-of-Thought (CoT)**.
        *   *Exemplo:* `Q: Roger tem 5 bolas de tênis. Ele compra mais 2 latas de bolas de tênis. Cada lata tem 3 bolas. Quantas bolas ele tem agora?\nA: Roger começou com 5 bolas. 2 latas de 3 bolas cada são 6 bolas. 5 + 6 = 11. A resposta é 11. \nQ: [Sua pergunta aqui]\nA:`

4.  **Aumentar a Robustez (para tarefas críticas):**
    *   Se a precisão for absolutamente crucial, implemente **Self-Consistency** sobre o CoT. Isso envolve executar o mesmo prompt CoT várias vezes (com uma temperatura > 0) e pegar a resposta majoritária.

5.  **Modularizar (para fluxos de trabalho complexos):**
    *   Se a tarefa for muito grande, quebre-a usando **Prompt Chaining**. Crie um prompt para cada etapa.
        *   *Prompt 1:* `Gere 5 tópicos para um blog sobre os benefícios da IA.`
        *   *Prompt 2 (usando a saída do 1):* `Escreva um rascunho de 500 palavras para o tópico: 'Como a IA pode aumentar a produtividade'.`
        *   *Prompt 3 (usando a saída do 2):* `Revise o rascunho a seguir, corrigindo erros gramaticais e melhorando a clareza.`

6.  **Otimizar e Escalar:**
    *   Use **Meta Prompting** para refinar seus prompts ou para gerar novos prompts para tarefas semelhantes.

## Pontos de atenção / vieses / riscos (com mitigação)

| Risco/Vieses | Descrição | Mitigação |
| :--- | :--- | :--- |
| **Custo Computacional** | Técnicas como Self-Consistency e Prompt Chaining aumentam significativamente o número de chamadas à API e, portanto, o custo. | **1. Uso Seletivo:** Aplique essas técnicas apenas em tarefas de alto valor onde a precisão justifica o custo. **2. Otimização de Prompts:** Invista tempo em criar um prompt CoT de alta qualidade para reduzir a necessidade de múltiplas execuções. |
| **Complexidade da Implementação** | Gerenciar o estado e o fluxo de dados em um Prompt Chaining pode se tornar complexo. | **1. Frameworks de Orquestração:** Utilize bibliotecas como LangChain ou LlamaIndex, que são projetadas para gerenciar cadeias de prompts e interações com LLMs. **2. Design Modular:** Projete cada prompt na cadeia para ser uma unidade independente e testável. |
| **Viés de Exemplo (Few-Shot)** | Os exemplos fornecidos no prompt podem introduzir vieses ou limitar a gama de respostas do modelo. | **1. Exemplos Diversificados:** Escolha exemplos que cubram diferentes aspectos da tarefa. **2. Ordem dos Exemplos:** Teste diferentes ordens para os exemplos, pois a ordem pode influenciar o resultado. **3. Instruções Claras:** Combine Few-Shot com instruções claras para que o modelo entenda o princípio, não apenas copie o padrão. |
| **Erro em Cascata (Prompt Chaining)** | Um erro em um dos primeiros prompts da cadeia pode se propagar e comprometer todo o resultado final. | **1. Validação Intermediária:** Adicione etapas de validação entre os prompts. Use um LLM ou código para verificar se a saída de uma etapa é válida antes de passá-la para a próxima. **2. Tratamento de Erros:** Implemente lógica para lidar com saídas inesperadas ou erros em qualquer ponto da cadeia. |

## Exemplos curtos

**Exemplo 1: Chain-of-Thought (CoT)**

*Prompt:* `Q: A cafeteria tinha 23 maçãs. Se eles usaram 20 para fazer torta e compraram mais 6, quantas maçãs eles têm? \nA: Primeiro, vamos começar com o número inicial de maçãs, que é 23. Eles usaram 20 para fazer torta, então subtraímos 20 de 23. 23 - 20 = 3. Depois, eles compraram mais 6 maçãs, então adicionamos 6 a 3. 3 + 6 = 9. A resposta é 9.`

**Exemplo 2: Few-Shot para Extração de Entidade**

*Prompt:* `Extraia o nome da empresa e o valor do investimento dos seguintes textos:\nTexto: "A Acme Corp anunciou um investimento de $10 milhões."\nSaída: {"empresa": "Acme Corp", "valor": "$10 milhões"}\n\nTexto: "A Globex Inc. recebeu um aporte de 5 milhões de euros."\nSaída: {"empresa": "Globex Inc.", "valor": "5 milhões de euros"}\n\nTexto: "A Stark Industries levantou $50M em uma rodada de financiamento."\nSaída:`

## Checklist de qualidade

- [ ] **Análise da Tarefa:** A complexidade da tarefa foi avaliada corretamente?
- [ ] **Técnica Apropriada:** A técnica de prompt escolhida (Zero-Shot, Few-Shot, CoT) é a mais adequada para a tarefa?
- [ ] **Clareza do Framework:** Se um framework (RICCE/CRISPE) foi usado, todos os seus componentes foram preenchidos?
- [ ] **Qualidade dos Exemplos (Few-Shot):** Os exemplos são claros, corretos e representativos da tarefa?
- [ ] **Lógica do Raciocínio (CoT):** A cadeia de pensamento é lógica e fácil de seguir?
- [ ] **Modularidade (Chaining):** Em uma cadeia, cada prompt é focado em uma única subtarefa?
- [ ] **Gerenciamento de Custo:** O custo computacional da técnica foi considerado e é justificável?
- [ ] **Tratamento de Erros:** Existem mecanismos para lidar com saídas inesperadas ou erros, especialmente em cadeias?
- [ ] **Prevenção de Vieses:** Os exemplos foram revisados para evitar a introdução de vieses?
- [ ] **Teste e Validação:** O prompt ou a cadeia de prompts foi testada com uma variedade de entradas para garantir sua robustez?

## Notas e Fontes

1.  **Prompt Engineering Guide - Prompting Techniques.** Disponível em: [https://www.promptingguide.ai/techniques](https://www.promptingguide.ai/techniques). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 9/10)
2.  **Wei, J., et al. (2022). *Chain-of-Thought Prompting Elicits Reasoning in Large Language Models*.** arXiv preprint arXiv:2201.11903. Disponível em: [https://arxiv.org/abs/2201.11903](https://arxiv.org/abs/2201.11903). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 10/10)
3.  **Wang, X., et al. (2022). *Self-Consistency Improves Chain of Thought Reasoning in Language Models*.** arXiv preprint arXiv:2203.11171. Disponível em: [https://arxiv.org/abs/2203.11171](https://arxiv.org/abs/2203.11171). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 10/10)
4.  **The AI Hat - The RICCE Framework for AI Content Writing.** Disponível em: [https://theaihat.com/prompt-engineering-made-easy-the-ricce-framework-for-ai-content-writing/](https://theaihat.com/prompt-engineering-made-easy-the-ricce-framework-for-ai-content-writing/). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 8/10)
5.  **Medium - ChatGPT Prompting Technique: The CRISPE Framework.** Disponível em: [https://medium.com/@inchristiely/chatgpt-prompting-technique-the-crispe-framework-e141a7bcad7e](https://medium.com/@inchristiely/chatgpt-prompting-technique-the-crispe-framework-e141a7bcad7e). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 7/10)

