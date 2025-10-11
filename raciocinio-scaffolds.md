# Raciocínio: Scaffolds (CoT, Self-Consistency, ToT, ReAct, Reflexion)

## Resumo Executivo

*   **O Desafio do Raciocínio:** Por padrão, LLMs são "pense rápido", gerando o próximo token mais provável. Isso é insuficiente para problemas que exigem deliberação, planejamento e raciocínio complexo.
*   **Scaffolding (Andaimes):** São estruturas de prompt que não apenas pedem uma resposta, mas guiam o LLM através de um processo de raciocínio explícito, forçando-o a "pensar devagar" e a mostrar seu trabalho.
*   **Chain-of-Thought (CoT):** A técnica fundamental de scaffolding. Ao adicionar "Pense passo a passo" ou fornecer um exemplo de raciocínio, o CoT induz o modelo a decompor o problema em etapas intermediárias, melhorando drasticamente o desempenho em tarefas de lógica e matemática.
*   **Self-Consistency:** Uma camada de robustez sobre o CoT. Em vez de aceitar a primeira cadeia de raciocínio, a técnica gera múltiplas cadeias de pensamento e escolhe a resposta final por consenso (votação majoritária), reduzindo a chance de erros aleatórios.
*   **Tree of Thoughts (ToT):** Generaliza o CoT ao permitir que o modelo explore *múltiplos* caminhos de raciocínio em paralelo, formando uma "árvore de pensamentos". O modelo pode avaliar o progresso em cada "galho" e decidir qual caminho seguir, podando os menos promissores. É ideal para problemas que exigem planejamento e exploração.
*   **ReAct (Reasoning and Acting):** Combina raciocínio com ação. O LLM não apenas pensa, mas também interage com ferramentas externas (ex: uma API de busca, uma calculadora). O ciclo é: Pensamento -> Ação (ferramenta) -> Observação -> Pensamento, permitindo que o modelo colete informações externas para informar seu raciocínio.
*   **Reflexion (ou Reflection):** Introduz um loop de feedback. Após gerar uma resposta, um "agente crítico" (que pode ser o mesmo LLM com um prompt diferente) avalia a resposta, identifica falhas e gera um feedback textual. Esse feedback é então adicionado ao contexto para a próxima tentativa, permitindo que o modelo aprenda com seus próprios erros.

## Para que serve / Resultados esperados

Scaffolds de raciocínio servem para transformar LLMs de simples completadores de texto em solucionadores de problemas mais deliberados e confiáveis. Eles fornecem uma estrutura para o "pensamento" do modelo, tornando o processo de resolução de problemas mais transparente e menos propenso a erros impulsivos.

Os resultados esperados são:

*   **Melhora Drástica na Resolução de Problemas:** Aumento significativo na precisão de tarefas que envolvem matemática, lógica, programação e planejamento.
*   **Transparência e Auditabilidade:** Ao externalizar a cadeia de raciocínio, torna-se possível entender *como* o modelo chegou a uma conclusão, permitindo a depuração e a verificação.
*   **Capacidade de Usar Ferramentas:** Com o ReAct, os LLMs podem superar sua limitação de conhecimento estático, buscando informações em tempo real ou realizando cálculos precisos.
*   **Auto-Correção e Aprendizado:** Com o Reflexion, os modelos podem iterativamente melhorar suas respostas sem a necessidade de re-treinamento, aprendendo com a experiência em tempo de execução.
*   **Resolução de Tarefas Mais Complexas:** A combinação dessas técnicas permite a criação de agentes de IA capazes de realizar tarefas que seriam impossíveis com um único prompt direto.

## Boas práticas

| Técnica | Descrição | Quando Usar |
| :--- | :--- | :--- |
| **Chain-of-Thought (CoT)** | Instruir o modelo a pensar passo a passo. | O padrão para qualquer tarefa não trivial que envolva lógica, cálculo ou inferência. É o ponto de partida para o raciocínio. |
| **Self-Consistency** | Gerar várias respostas com CoT e usar a mais frequente. | Em tarefas críticas onde a precisão é primordial e o custo computacional extra é aceitável. Melhora a robustez do CoT. |
| **Tree of Thoughts (ToT)** | Explorar e avaliar múltiplos caminhos de raciocínio. | Problemas que têm um grande espaço de busca ou que exigem planejamento e lookahead, como jogos (xadrez) ou design de soluções. |
| **ReAct** | Intercalar raciocínio (thought) com o uso de ferramentas (action). | Quando o LLM precisa de informações externas (ex: buscar na web, consultar uma API) ou precisa realizar uma ação no mundo (ex: enviar um e-mail). |
| **Reflexion** | Avaliar a própria saída e usar o feedback para tentar novamente. | Tarefas iterativas ou de geração, como escrever um código, um ensaio ou um plano, onde a primeira tentativa raramente é perfeita. |

## Como fazer (passo a passo)

1.  **Comece com Chain-of-Thought (CoT):** Para qualquer problema complexo, a primeira etapa é modificar seu prompt para incluir uma instrução de CoT.
    *   **Zero-Shot CoT:** Simplesmente adicione a frase `"Vamos pensar passo a passo."` (ou em inglês, `"Let's think step by step."`), que é surpreendentemente eficaz.
    *   **Few-Shot CoT:** Forneça um exemplo completo de raciocínio no prompt.

2.  **Implemente ReAct para Conhecimento Externo:** Se a tarefa requer informações que não estão no prompt, use o padrão ReAct.
    *   **Defina as Ferramentas:** Crie funções que o LLM pode chamar (ex: `search(query)`, `calculator(expression)`).
    *   **Modifique o Prompt:** Instrua o LLM que ele pode usar essas ferramentas. O prompt deve incluir o formato para a chamada da ferramenta.
    *   **Ciclo de Execução:**
        1.  O LLM gera um `Pensamento` e uma `Ação` (ex: `Ação: search("preço do dólar hoje")`).
        2.  Seu código intercepta a `Ação`, executa a função correspondente e obtém um resultado (`Observação`).
        3.  Você alimenta a `Observação` de volta para o LLM, que continua o ciclo até gerar a `Resposta Final`.

3.  **Use Tree of Thoughts (ToT) para Exploração:** Para problemas de planejamento, estruture a interação como uma busca em árvore.
    *   **Geração de Candidatos:** Peça ao LLM para gerar múltiplos "pensamentos" ou próximos passos possíveis a partir do estado atual.
    *   **Avaliação de Candidatos:** Peça ao LLM (ou use uma heurística) para avaliar cada candidato, dando uma nota de quão promissor ele é.
    *   **Expansão:** Escolha o melhor candidato e repita o processo a partir dele, construindo a árvore.

4.  **Adicione Reflexion para Auto-Correção:** Para tarefas de geração que precisam de refinamento, implemente um loop de feedback.
    *   **Geração Inicial:** Peça ao LLM para gerar a primeira versão da resposta (ex: um bloco de código).
    *   **Prompt de Reflexão:** Crie um segundo prompt (`Reflexion Prompt`) que peça ao LLM para atuar como um crítico. Este prompt deve conter a resposta gerada e pedir para identificar falhas, bugs ou áreas de melhoria.
    *   **Geração Refinada:** Adicione o feedback gerado pelo prompt de reflexão ao contexto do prompt original e peça ao LLM para gerar uma nova versão, levando o feedback em consideração.

5.  **Combine as Técnicas:** As técnicas mais poderosas surgem da combinação. Um agente pode usar ReAct para buscar informações, ToT para planejar os próximos passos e Reflexion para refinar sua saída final.

## Pontos de atenção / vieses / riscos (com mitigação)

| Risco/Vieses | Descrição | Mitigação |
| :--- | :--- | :--- |
| **Complexidade e Custo** | Cada uma dessas técnicas aumenta o número de chamadas ao LLM, a complexidade do código de orquestração e o custo geral. | **1. Comece Simples:** Não implemente ToT ou Reflexion se um simples CoT for suficiente. **2. Modelos em Cascata:** Use modelos maiores e mais caros para as etapas de raciocínio mais críticas e modelos menores e mais rápidos para avaliações ou gerações preliminares. |
| **Alucinação no Raciocínio** | O LLM pode "alucinar" um passo lógico incorreto em sua cadeia de pensamento, o que invalida todo o resto do raciocínio. | **1. Grounding com Ferramentas (ReAct):** Force o modelo a verificar fatos e realizar cálculos usando ferramentas externas, em vez de confiar em seu conhecimento interno. **2. Self-Consistency:** A votação majoritária ajuda a filtrar cadeias de pensamento que contêm erros aleatórios. |
| **Looping Infinito** | Em ciclos como ReAct ou Reflexion, o modelo pode entrar em um loop, repetindo os mesmos pensamentos e ações sem progredir. | **1. Limite de Iterações:** Sempre implemente um número máximo de passos ou iterações. **2. Detecção de Repetição:** Monitore o histórico de pensamentos e ações. Se o modelo repetir o mesmo estado, force-o a tentar uma abordagem diferente ou encerre o processo. |
| **Avaliação Ineficaz (ToT, Reflexion)** | A capacidade do LLM de avaliar a qualidade de um pensamento ou de uma resposta pode ser fraca, levando-o a podar galhos promissores ou a fornecer feedback inútil. | **1. Prompts de Avaliação Específicos:** Em vez de pedir uma avaliação genérica, faça perguntas específicas (ex: "Este código lida com o caso de entrada nula?"). **2. Heurísticas Externas:** Quando possível, use código ou regras externas para avaliar os candidatos, em vez de depender apenas do LLM. |

## Exemplos curtos

**Exemplo 1: ReAct (Raciocínio + Ação)**

*Query:* `Qual a população da capital da França?`

*Trajetória:* 
**Pensamento 1:** Eu preciso descobrir qual é a capital da França e depois qual é a sua população. 
**Ação 1:** `search("capital da França")` 
**Observação 1:** A capital da França é Paris. 
**Pensamento 2:** Agora eu sei que a capital é Paris. Preciso encontrar a população de Paris. 
**Ação 2:** `search("população de Paris 2024")` 
**Observação 2:** A população de Paris em 2024 é de aproximadamente 2.1 milhões de habitantes. 
**Pensamento 3:** Eu tenho a resposta. 
**Resposta Final:** A população de Paris, a capital da França, é de cerca de 2.1 milhões de habitantes.

**Exemplo 2: Reflexion (Simplificado)**

*Prompt Inicial:* `Escreva uma função em Python que some dois números.`

*Resposta 1:* `def soma(a, b): return a+b`

*Prompt de Reflexão:* `Critique o código a seguir. Ele é robusto? Faltam docstrings ou type hints?`

*Feedback de Reflexão:* `O código está funcionalmente correto, mas não é robusto. Faltam type hints para os argumentos e para o retorno, e não há uma docstring explicando o que a função faz.`

*Prompt Final (com feedback):* `Você escreveu: "def soma(a, b): return a+b". O feedback foi: "Faltam type hints e docstring". Por favor, reescreva a função incorporando o feedback.`

*Resposta 2 (Refinada):*
```python
def soma(a: int, b: int) -> int:
    """Soma dois números inteiros e retorna o resultado."""
    return a + b
```

## Checklist de qualidade

- [ ] **Necessidade de Raciocínio:** A tarefa realmente se beneficia de um scaffold de raciocínio, ou um prompt direto seria suficiente?
- [ ] **Escolha da Técnica:** A técnica escolhida (CoT, ToT, ReAct, etc.) é a mais adequada para a natureza do problema?
- [ ] **Clareza do Prompt-Guia:** O prompt que inicia o processo de raciocínio é claro e fornece todas as instruções necessárias?
- [ ] **Definição de Ferramentas (ReAct):** As ferramentas disponíveis para o LLM estão bem definidas, com descrições claras de seus argumentos e saídas?
- [ ] **Critérios de Avaliação (ToT/Reflexion):** Os critérios para avaliar os pensamentos ou as respostas são específicos e eficazes?
- [ ] **Controle de Ciclo:** Existem mecanismos para prevenir loops infinitos e limitar o número de iterações?
- [ ] **Tratamento de Erros:** O sistema consegue lidar com erros de ferramentas ou com feedback inútil do LLM?
- [ ] **Transparência:** O processo de raciocínio é registrado de forma que possa ser auditado e depurado?
- [ ] **Custo-Benefício:** O ganho de performance justifica a complexidade e o custo adicionais da técnica?
- [ ] **Composição:** Foi considerada a possibilidade de combinar múltiplas técnicas para resolver problemas ainda mais complexos?

## Notas e Fontes

1.  **Yao, S., et al. (2022). *ReAct: Synergizing Reasoning and Acting in Language Models*.** arXiv preprint arXiv:2210.03629. Disponível em: [https://arxiv.org/abs/2210.03629](https://arxiv.org/abs/2210.03629). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 10/10)
2.  **Yao, S., et al. (2023). *Tree of Thoughts: Deliberate Problem Solving with Large Language Models*.** arXiv preprint arXiv:2305.10601. Disponível em: [https://arxiv.org/abs/2305.10601](https://arxiv.org/abs/2305.10601). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 10/10)
3.  **Shinn, N., et al. (2023). *Reflexion: An Autonomous Agent with Dynamic Memory and Self-Reflection*.** arXiv preprint arXiv:2303.11366. Disponível em: [https://arxiv.org/abs/2303.11366](https://arxiv.org/abs/2303.11366). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 10/10)
4.  **Prompt Engineering Guide - Advanced Prompting.** Disponível em: [https://www.promptingguide.ai/techniques](https://www.promptingguide.ai/techniques). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 9/10)
5.  **Wang, X., et al. (2022). *Self-Consistency Improves Chain of Thought Reasoning in Language Models*.** arXiv preprint arXiv:2203.11171. Disponível em: [https://arxiv.org/abs/2203.11171](https://arxiv.org/abs/2203.11171). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 10/10)

