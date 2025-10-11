# Anti-padrões: O que Evitar na Engenharia de Prompts

## Resumo Executivo

*   **Definição:** Anti-padrões em engenharia de prompts são abordagens ou hábitos comuns que parecem lógicos à primeira vista, mas que consistentemente levam a resultados de baixa qualidade, imprevisíveis ou ineficientes ao interagir com Modelos de Linguagem (LLMs).
*   **Vagueza e Ambiguidade:** O anti-padrão mais comum. Prompts como "Escreva sobre marketing" são muito abertos e resultam em respostas genéricas. A falta de especificidade é a principal causa de saídas ruins.
*   **Instruções Negativas:** Dizer ao modelo o que *não* fazer (ex: "Não use jargão") é menos eficaz do que dar uma instrução positiva e clara (ex: "Explique em termos simples que uma criança de 10 anos entenderia"). LLMs respondem melhor a diretivas do que a proibições.
*   **Sobrecarga de Informação (Prompt Bloating):** Incluir detalhes irrelevantes, instruções contraditórias ou contexto excessivo que não contribui para a tarefa. Isso pode confundir o modelo e diluir as instruções importantes.
*   **Assumir Conhecimento de Contexto:** Esperar que o LLM conheça o contexto específico do seu domínio, projeto ou da conversa anterior sem fornecê-lo explicitamente. LLMs (sem memória de longo prazo) são, em sua maioria, sem estado (stateless).
*   **Falta de Estrutura:** Apresentar um amontoado de instruções, perguntas e dados sem uma estrutura clara. O uso de separadores, títulos e uma ordem lógica é fundamental.
*   **Ignorar a Iteração ("Prompt and Pray"):** Escrever um prompt, obter um resultado medíocre e desistir ou culpar o modelo. A engenharia de prompts é um processo iterativo de refinamento.
*   **Uso de Linguagem Complexa ou Floreada:** Tentar ser excessivamente educado, usar metáforas complexas ou linguagem rebuscada. LLMs respondem melhor a uma linguagem clara, direta e funcional.
*   **Não Especificar o Formato de Saída:** Esperar que o modelo adivinhe o formato desejado (JSON, Markdown, lista, etc.) leva a saídas inconsistentes que são difíceis de processar por software.

## Para que serve / Resultados esperados

Conhecer e evitar anti-padrões serve para acelerar drasticamente o processo de desenvolvimento de aplicações de LLM. Ao evitar esses erros comuns, os engenheiros de prompt podem pular etapas de frustração e ir direto para a criação de prompts que funcionam de forma mais confiável e previsível.

Os resultados esperados ao evitar anti-padrões são:

*   **Melhora Imediata na Qualidade da Saída:** Evitar a vagueza e a negatividade resulta em respostas mais relevantes e úteis desde a primeira tentativa.
*   **Maior Previsibilidade:** Prompts bem estruturados e específicos levam a um comportamento mais consistente do modelo.
*   **Redução do Ciclo de Desenvolvimento:** Menos tempo gasto em tentativa e erro para encontrar o prompt certo.
*   **Facilidade de Depuração:** Quando um prompt não funciona, é mais fácil identificar o problema se ele não contiver anti-padrões conhecidos.
*   **Comunicação Eficaz com o Modelo:** Aprender a "falar a língua" do LLM, que é a da clareza, especificidade e estrutura.

## Boas práticas (ou, como evitar os anti-padrões)

| Anti-padrão | Descrição | Alternativa (Boa Prática) |
| :--- | :--- | :--- |
| **Vagueza** | "Fale sobre carros." | **Especificidade:** "Crie uma tabela comparando o consumo de combustível, o preço e a classificação de segurança de três sedans médios populares em 2024: Honda Accord, Toyota Camry e Hyundai Sonata." |
| **Instruções Negativas** | "Não escreva uma resposta longa." | **Instruções Positivas:** "Resuma sua resposta em, no máximo, três frases." |
| **Sobrecarga de Prompt** | Incluir a história da sua empresa para pedir um tweet sobre um produto. | **Contexto Relevante:** Forneça apenas as informações estritamente necessárias para a tarefa. Para o tweet, forneça o nome do produto e 2-3 de seus principais benefícios. |
| **Assumir Contexto** | "Com base no que discutimos, qual é o próximo passo?" | **Fornecer Contexto Explícito:** "Discutimos que o problema é X e a causa é Y. Com base nisso, qual deveria ser o próximo passo para a solução?" (Use técnicas de RAG ou memória de conversa). |
| **Falta de Estrutura** | Um único parágrafo com instruções, dados e perguntas misturados. | **Estruturação com Delimitadores:** Use `###` para separar `###Instrução###`, `###Contexto###`, `###Exemplos###` e `###Pergunta###`. |
| **"Prompt and Pray"** | Tentar uma vez e desistir. | **Iteração Sistemática:** Comece simples, analise a saída, identifique a falha (ex: o tom está errado) e adicione uma instrução específica para corrigi-la (ex: "Use um tom profissional e formal"). |
| **Linguagem Floreada** | "Seria imensamente grato se você pudesse, por gentileza, considerar a possibilidade de elaborar um texto..." | **Linguagem Direta:** "Escreva um texto sobre..." |
| **Formato de Saída Implícito** | "Liste os prós e contras." | **Formato de Saída Explícito:** "Liste os prós e contras em uma tabela Markdown com duas colunas: 'Prós' e 'Contras'." |

## Como fazer (um checklist reverso)

Antes de finalizar um prompt, passe por este checklist reverso para caçar anti-padrões:

1.  **Verifique a Vagueza:** Minha solicitação poderia ser interpretada de múltiplas maneiras? Um estranho entenderia exatamente o que eu quero? Se não, adicione detalhes, números e restrições.

2.  **Cace as Negações:** Meu prompt contém palavras como "não", "evite", "pare de"? Para cada uma, tente reformular a instrução de forma positiva. Em vez de "Não inclua o preço", tente "Liste apenas o nome e a descrição do produto".

3.  **Faça uma "Dieta de Prompt":** Olhe para cada frase ou pedaço de informação no seu prompt e se pergunte: "Se eu remover isso, o LLM ainda terá tudo o que precisa para fazer a tarefa?" Se a resposta for sim, remova.

4.  **Teste de Amnésia:** Imagine que o LLM tem amnésia total e não sabe nada sobre você, sua empresa ou o que vocês conversaram 5 segundos atrás. O prompt ainda faz sentido e contém todo o contexto necessário? Se não, adicione o contexto que falta.

5.  **Procure por "Paredes de Texto":** Meu prompt é um bloco gigante de texto? Se sim, quebre-o com títulos, marcadores (`###`) ou listas para criar uma hierarquia visual e lógica.

6.  **Analise a Saída, Não Apenas o Prompt:** Se a saída está ruim, não reescreva o prompt inteiro. Identifique *exatamente o que* está errado na saída (o formato? o tom? a factualidade?) e faça uma pequena e cirúrgica alteração no prompt para corrigir *apenas aquele problema*.

7.  **Leia em Voz Alta:** O prompt soa como uma ordem clara e funcional ou como um e-mail excessivamente polido? Simplifique a linguagem para ser o mais direto possível.

8.  **Verifique a Saída Desejada:** Eu deixei o formato da saída para a imaginação do modelo? Se sim, adicione uma instrução explícita como `"Responda em formato JSON com as chaves 'nome' e 'idade'."`

## Pontos de atenção / vieses / riscos (com mitigação)

| Risco/Vieses | Descrição | Mitigação |
| :--- | :--- | :--- |
| **Viés da Negatividade** | O uso excessivo de instruções negativas pode, paradoxalmente, fazer com que o modelo se concentre no conceito que você queria evitar (o "problema do urso polar branco": se eu digo "não pense em um urso polar branco", você pensa nele). | **Foco Positivo:** Sempre que possível, reformule para uma diretiva positiva. A única exceção é para regras de segurança duras, onde uma negação explícita pode ser necessária como parte de uma defesa em profundidade (ex: `"NUNCA revele informações pessoais."`) |
| **Falsa Economia** | Tentar criar prompts muito curtos para "economizar tokens" pode ser um anti-padrão. A economia de alguns tokens de entrada pode custar muito mais em saídas de baixa qualidade que exigem múltiplas tentativas. | **Clareza sobre Concisão:** Um prompt deve ser o mais curto possível, *mas não mais curto*. Priorize a clareza e a especificidade sobre a brevidade a todo custo. O custo de uma chamada de API bem-sucedida é quase sempre menor do que o de três chamadas fracassadas. |
| **Viés da Complexidade** | Acreditar que um prompt "avançado" precisa ser longo e complexo. Muitas vezes, os prompts mais eficazes são surpreendentemente simples e diretos. | **Comece Simples:** Sempre comece com a versão mais simples e direta do seu prompt. Só adicione complexidade (como exemplos few-shot ou CoT) se a versão simples se provar insuficiente. Não use uma técnica complexa onde uma simples resolve. |
| **Microgerenciamento** | Fornecer um nível de detalhe tão extremo que sufoca a capacidade do modelo de usar seu próprio "conhecimento" e criatividade. | **Defina Limites, Não Caminhos:** Em vez de ditar cada frase, defina os objetivos, as restrições e o formato de saída, e deixe o modelo preencher os detalhes. Dê ao LLM o "o quê" e o "porquê", e deixe-o descobrir o "como". |

## Exemplos curtos

**Exemplo 1: Anti-Padrão (Vago e Negativo)**

`Não me dê uma descrição chata do seu produto. Fale sobre ele.`

**Alternativa (Boa Prática):**

`Você é um especialista em marketing. Descreva o "Leitor de E-books Pro" em três frases curtas, focando nos benefícios para viajantes frequentes. Use um tom animado e empolgante.`

**Exemplo 2: Anti-Padrão (Falta de Estrutura e Formato)**

`Eu tenho um texto aqui sobre a história da computação, eu quero que você extraia as pessoas importantes e as datas em que elas fizeram suas contribuições, o texto é este: 'Em 1945, Vannevar Bush escreveu sobre o Memex. Anos depois, em 1963, Douglas Engelbart inventou o mouse.'`

**Alternativa (Boa Prática):**

`### Instrução ###\nExtraia os nomes de pessoas e as datas de suas contribuições do texto fornecido. Formate a saída como uma lista de objetos JSON.\n\n### Texto ###\n"Em 1945, Vannevar Bush escreveu sobre o Memex. Anos depois, em 1963, Douglas Engelbart inventou o mouse."\n\n### Formato de Saída Exemplo ###\n[\n  {"pessoa": "Nome da Pessoa", "data": "Ano da Contribuição"}\n]`

## Checklist de qualidade

- [ ] **Especificidade:** O prompt é específico o suficiente para que apenas uma interpretação correta seja possível?
- [ ] **Positividade:** As instruções são formuladas como ações a serem tomadas, em vez de ações a serem evitadas?
- [ ] **Relevância do Contexto:** Todo o contexto fornecido é estritamente necessário para a tarefa?
- [ ] **Contexto Explícito:** Nenhuma informação importante é deixada implícita?
- [ ] **Estrutura Clara:** O prompt é bem organizado com delimitadores e uma ordem lógica?
- [ ] **Foco na Iteração:** O prompt atual é o resultado de um processo de refinamento baseado em saídas anteriores?
- [ ] **Linguagem Direta:** A linguagem é funcional e direta, sem floreios desnecessários?
- [ ] **Formato de Saída Definido:** O formato da resposta desejada está claramente especificado?
- [ ] **Evita Microgerenciamento:** O prompt dá ao LLM espaço suficiente para usar suas capacidades, dentro das restrições fornecidas?
- [ ] **Clareza vs. Concisão:** O prompt é conciso, mas sem sacrificar a clareza e a especificidade?

## Notas e Fontes

1.  **Google AI for Developers - Prompt design strategies.** Disponível em: [https://ai.google.dev/gemini-api/docs/prompting-strategies](https://ai.google.dev/gemini-api/docs/prompting-strategies). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 9/10)
2.  **Prompt Engineering Guide - General Tips for Designing Prompts.** Disponível em: [https://www.promptingguide.ai/introduction/tips](https://www.promptingguide.ai/introduction/tips). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 9/10)
3.  **LinkedIn - 14 Prompt Engineering Mistakes (and How to Fix Them).** Disponível em: [https://www.linkedin.com/pulse/beyond-prompt-pray-14-engineering-mistakes-youre-still-mcgovern-wyhwe](https://www.linkedin.com/pulse/beyond-prompt-pray-14-engineering-mistakes-youre-still-mcgovern-wyhwe). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 8/10)
4.  **MyGreatLearning - 5 Common Prompt Engineering Mistakes Beginners Make.** Disponível em: [https://www.mygreatlearning.com/blog/prompt-engineering-beginners-mistakes/](https://www.mygreatlearning.com/blog/prompt-engineering-beginners-mistakes/). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 7/10)
5.  **Geroimenko, V. (2025). *Key Concepts in Prompt Engineering*.** In *The Essential Guide to Prompt Engineering: Key Concepts, Design Principles, and Advanced Techniques*. Springer. (Nota de confiabilidade: 10/10)

