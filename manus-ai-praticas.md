_# Melhores Práticas de Prompting para Manus.AI

## 1. Resumo Executivo

- **Foco em Context Engineering:** A filosofia da Manus.AI não é sobre o prompt perfeito, mas sobre a **engenharia do contexto** perfeito. A plataforma é projetada para otimizar o fluxo de informações para o modelo de linguagem, tratando o prompt como parte de um contexto maior que inclui o histórico de ações e observações.
- **Otimização de KV-Cache é Crucial:** A métrica mais importante para a Manus.AI é a taxa de acerto do KV-cache. Prompts eficazes são aqueles que maximizam a reutilização do cache, mantendo o prefixo do prompt (especialmente o `system_prompt`) estável e livre de dados dinâmicos.
- **Contexto Append-Only:** A abordagem da Manus.AI trata o contexto como um log imutável. As interações são sempre adicionadas ao final. Isso exige que os prompts sejam pensados não como comandos isolados, but como passos em uma cadeia de raciocínio que se constrói ao longo do tempo.
- **Máquina de Estados para Controle de Ferramentas:** A Manus.AI gerencia a disponibilidade de ferramentas através de uma máquina de estados sensível ao contexto. Em vez de remover ferramentas, ela mascara os logits dos tokens durante a decodificação para forçar ou proibir a seleção de certas ações, garantindo que o agente se comporte de maneira previsível.
- **Design de Ferramentas com Prefixos:** As ferramentas são projetadas com prefixos consistentes (ex: `browser_*`, `shell_*`). Isso permite que os prompts (ou a máquina de estados) restrinjam facilmente o agente a um subconjunto de ferramentas relevantes para o estado atual da tarefa.
- **Stochastic Graduate Descent:** A Manus.AI reconhece que a engenharia de prompt é uma ciência experimental. O processo, apelidado de "Stochastic Graduate Descent", envolve busca de arquitetura, ajustes de prompt e experimentação empírica para encontrar o "ótimo local" para uma determinada tarefa.

## 2. Onde Brilha / Onde Evitar

| Onde Brilha (Casos de Uso Ideais) | Onde Evitar (Limites e Armadilhas) |
| :--- | :--- |
| **Tarefas Agentic de Longa Duração:** Ideal para tarefas complexas que exigem múltiplos passos, uso de ferramentas e manutenção de estado ao longo do tempo, como pesquisa aprofundada, análise de dados ou desenvolvimento de software. | **Prompts com Dados Dinâmicos no Início:** Colocar um timestamp ou um ID de usuário no início do `system_prompt` é o anti-padrão número um, pois destrói a otimização do KV-cache, aumentando custos e latência. |
| **Ambientes com Custo e Latência Sensíveis:** A forte ênfase na otimização do KV-cache torna a Manus.AI ideal para aplicações em produção onde o custo e a velocidade da inferência são críticos. | **Modificação do Histórico de Contexto:** Tentar modificar ou reescrever observações ou ações passadas no contexto vai contra a filosofia de "append-only" e pode quebrar a lógica do agente. |
| **Fluxos de Trabalho Estruturados e Previsíveis:** A máquina de estados e o controle de ferramentas tornam a Manus.AI excelente para automatizar processos de negócios onde a sequência de ações precisa ser confiável e previsível. | **Uso de Ferramentas sem Nomenclatura Consistente:** Se você definir ferramentas personalizadas sem seguir o padrão de prefixo, perderá a capacidade da máquina de estados de restringir o agente a grupos de ferramentas contextualmente relevantes. |
| **Desenvolvimento e Experimentação de Agentes:** A filosofia de "Context Engineering" e "Stochastic Graduate Descent" faz da Manus.AI uma plataforma poderosa para pesquisadores e desenvolvedores que estão construindo e otimizando seus próprios agentes de IA. | **Tarefas de "Tiro Único" sem Estado:** Para tarefas simples que não requerem memória ou uso de ferramentas (ex: traduzir uma frase), a complexidade da arquitetura da Manus.AI pode ser um exagero. |



## 3. Técnicas de Prompting (Model-Specific)

Na Manus.AI, a engenharia de prompt é, na verdade, **engenharia de contexto**. O objetivo não é apenas dar uma instrução, mas moldar todo o histórico de interação para guiar o modelo de forma eficiente.

### A Santidade do `system_prompt` (Prefixo Estável)

Esta é a regra de ouro da Manus.AI. O `system_prompt` é o prefixo de cada chamada para o LLM. Se ele for estável, o KV-cache pode ser reutilizado, resultando em uma economia de custos e latência de até 10x.

- **O que fazer:** Defina a identidade, as regras de alto nível, as ferramentas e as restrições do agente no `system_prompt` e **mantenha-o idêntico** entre as execuções da mesma tarefa.
- **O que NÃO fazer:** Nunca inclua informações dinâmicas como `timestamp`, `user_id`, `session_id` ou qualquer outro dado que mude a cada chamada.

**Exemplo de `system_prompt` Otimizado para Manus.AI:**

```
Você é um agente de análise de dados. Seu objetivo é responder a perguntas sobre dados contidos em arquivos CSV. Você tem acesso às seguintes ferramentas: `python.execute_code`.

Regras:
1. Sempre examine os dados primeiro para entender a estrutura.
2. Gere código Python para responder à pergunta do usuário.
3. Apresente a resposta final de forma clara, juntamente com quaisquer visualizações geradas.
```

### Contexto Append-Only e Serialização Determinística

O agente da Manus.AI constrói seu contexto passo a passo, adicionando a ação e a observação a cada iteração. Seus prompts devem respeitar essa estrutura.

- **Não modifique o passado:** Ao criar prompts para tarefas de múltiplos passos, não peça ao agente para "voltar e mudar" uma ação anterior. Em vez disso, adicione um novo passo que corrija ou compense o anterior.
- **Serialização Estável:** Se você estiver construindo uma aplicação sobre a Manus.AI e fornecendo contexto em formato JSON, certifique-se de que a serialização do JSON seja determinística (as chaves estão sempre na mesma ordem). Muitas bibliotecas não garantem isso por padrão, o que pode silenciosamente quebrar o cache.

### Controle de Ferramentas via Máquina de Estados e Nomenclatura

Você pode guiar a seleção de ferramentas do agente através de prompts que interagem com sua máquina de estados interna.

- **Restringindo a um Grupo de Ferramentas:** Ao projetar suas próprias ferramentas, use prefixos consistentes. Em seguida, você pode instruir o agente a focar em um modo específico.
  > **Prompt:** "Agora estamos na fase de exploração web. Concentre-se em usar as ferramentas `browser_*` para coletar informações. Não use as ferramentas `shell_*` ou `file_*` nesta fase."
  *(Isso funciona porque a máquina de estados da Manus.AI pode usar essa instrução para mascarar os logits das ferramentas que não correspondem ao prefixo `browser_*`.)*

- **Forçando uma Ferramenta Específica:** A Manus.AI suporta o preenchimento de prefixo para forçar a chamada de uma ferramenta específica, o que é útil para fluxos de trabalho rígidos.
  > **Prompt (Conceitual):** "A próxima ação deve ser chamar a ferramenta `file.write` para salvar os resultados."
  *(Internamente, isso pré-preenche a resposta do modelo até o início da chamada da função, forçando-o a completar o resto.)*


_
## 4. Metaprompting: A Filosofia do "Stochastic Graduate Descent"

A Manus.AI adota uma visão pragmática sobre a criação de agentes, reconhecendo que não existe um prompt "perfeito" universal. O processo, apelidado de "Stochastic Graduate Descent" (SGD), é uma filosofia de experimentação e refinamento contínuo.

O metaprompting na Manus.AI não é sobre pedir à IA para "melhorar um prompt", mas sobre usar a IA como um parceiro no processo de SGD para projetar e refinar o comportamento de um agente.

### Usando a IA para Explorar o Espaço de Soluções

Em vez de pedir um `system_prompt` final, peça à IA para gerar *variações* de um `system_prompt` com base em diferentes estratégias, e então teste-as.

**Exemplo de Metaprompt para Exploração:**

> "Você é um Arquiteto de Agentes de IA praticando 'Stochastic Graduate Descent'. Estou construindo um agente para escrever código Python.
> 
> **Objetivo:** Gerar três `system_prompts` alternativos para este agente, cada um focando em uma filosofia de desenvolvimento diferente:
> 1.  **Filosofia 1: 'Move Fast and Break Things'.** O agente deve priorizar a velocidade e a geração rápida de código funcional, mesmo que não seja perfeito.
> 2.  **Filosofia 2: 'Test-Driven Development'.** O agente deve sempre escrever testes (`pytest`) antes de escrever o código da funcionalidade.
> 3.  **Filosofia 3: 'Secure by Design'.** O agente deve priorizar a segurança, a validação de entradas e o tratamento de erros acima de tudo.
> 
> Gere cada `system_prompt` em um bloco de código separado para que eu possa testá-los."

### Documentando Experimentos (Reverse Metaprompting)

Após testar diferentes abordagens, use a IA para documentar os resultados e extrair aprendizados, que informarão a próxima iteração do seu agente.

**Exemplo de Documentação de Experimento:**

> "Realizei um teste com três `system_prompts` para um agente de codificação. Aqui estão os resultados:
> 
> - **Prompt 1 ('Move Fast'):** Gerou código rapidamente, mas continha bugs sutis e não tratava casos de borda.
> - **Prompt 2 ('TDD'):** Foi o mais lento, mas produziu o código mais robusto e confiável.
> - **Prompt 3 ('Secure'):** Produziu código seguro, mas era excessivamente verboso e complexo para tarefas simples.
> 
> **Sua tarefa:**
> Com base nesses resultados, crie um novo `system_prompt` **híbrido** que combine o melhor dos três mundos: ele deve adotar o TDD como prática padrão, mas com uma instrução para simplificar o código se a tarefa for de baixa complexidade, e sempre incluir validação de entrada para dados externos."

## 5. Tool/Function Calling & Saídas Estruturadas

A abordagem da Manus.AI para `tool calling` é profundamente integrada com sua arquitetura de máquina de estados e otimização de cache. A clareza e a consistência na definição das ferramentas são primordiais.

### Design de Ferramentas com Prefixos Consistentes

Esta é a prática mais importante para o `tool calling` na Manus.AI. A nomenclatura de suas ferramentas afeta diretamente a capacidade do agente de ser controlado.

- **Padrão:** `familia_acao` (ex: `browser_navigate`, `browser_click`, `shell_exec`, `file_write`).
- **Por que funciona:** Permite que a máquina de estados (ou um prompt) restrinja o agente a um modo de operação. Por exemplo, em um "modo de pesquisa", o agente pode ser limitado a usar apenas ferramentas `browser_*`, impedindo-o de escrever arquivos ou executar comandos no shell, o que aumenta a segurança e a previsibilidade.

### Forçando a Seleção de Ferramentas com `Response Prefill`

A Manus.AI utiliza uma técnica chamada `response prefill` para forçar o agente a tomar certas ações. Embora isso seja geralmente gerenciado pela máquina de estados interna, você pode simular esse comportamento em seus prompts para criar fluxos de trabalho rígidos.

**Exemplo Conceitual de Prompt:**

> "Após salvar o arquivo com a ferramenta `file.write`, a próxima e única ação permitida é notificar o usuário usando a ferramenta `message.send`."

Internamente, a Manus.AI implementa isso pré-preenchendo a resposta do LLM com o início da chamada da ferramenta (`<tool_call>{"name": "message_send"...`), o que força o modelo a completar essa chamada específica.

### Saídas Estruturadas e Serialização Determinística

Para garantir a otimização do cache, qualquer saída estruturada (como JSON) que é alimentada de volta ao contexto deve ser serializada de forma determinística.

- **O Problema:** Muitos serializadores JSON não garantem a ordem das chaves. `{"a": 1, "b": 2}` e `{"b": 2, "a": 1}` são semanticamente idênticos, mas textualmente diferentes, o que quebra o cache do prompt.
- **A Solução:** Ao construir uma aplicação sobre a Manus.AI, sempre use um serializador JSON que permita ordenar as chaves (`sort_keys=True` em Python, por exemplo). Isso garante que a mesma estrutura de dados sempre resulte no mesmo texto, mantendo o prefixo do prompt estável e o cache intacto.



## 6. Boas Práticas por Tarefa (Exemplos)

Os exemplos a seguir ilustram como aplicar os princípios de engenharia de contexto da Manus.AI a tarefas práticas.

### Exemplo 1: Agente de Pesquisa de Longa Duração

- **Antes (Prompt Ineficiente):**
  > **System Prompt:** "Você é um pesquisador. A data de hoje é `2025-10-04`. Responda à pergunta do usuário."
  > *(Este prompt é terrível para a Manus.AI. O timestamp dinâmico no `system_prompt` invalida o cache a cada execução, tornando-o lento e caro.)*

- **Depois (Prompt Otimizado para Manus.AI):**
  > **System Prompt (Estável):**
  > "Você é um Agente de Pesquisa Autônomo. Seu objetivo é responder a perguntas complexas de forma abrangente. Você deve usar as ferramentas `browser_*` para coletar informações e a ferramenta `file.write` para salvar suas descobertas. Ao final, use `message.send` para entregar o relatório final."
  > 
  > **User Prompt (com Dados Dinâmicos):**
  > "A data de hoje é `2025-10-04`. Por favor, pesquise as implicações da recente legislação sobre IA na União Europeia."
  > *(**Por que funciona:** O `system_prompt` permanece estável, maximizando o reuso do KV-cache. Os dados dinâmicos (data) são movidos para o `user_prompt`, que é a parte variável do contexto por design.)*

### Exemplo 2: Agente de Desenvolvimento de Software

- **Antes (Prompt sem Controle):**
  > "Crie uma função em Python para analisar um arquivo CSV e depois salve um gráfico. Use as ferramentas `file` e `python`."
  > *(Este prompt é muito aberto e não impõe um fluxo de trabalho seguro ou previsível.)*

- **Depois (Prompt Otimizado para Manus.AI):**
  > **System Prompt:**
  > "Você é um Desenvolvedor de IA. Você opera em uma máquina de estados com duas fases: `ANALISE` e `ESCRITA`.
  > - Na fase `ANALISE`, você só pode usar as ferramentas `file.read` e `python.execute_code` para explorar os dados. Você não pode modificar o sistema de arquivos.
  > - Na fase `ESCRITA`, após a análise ser concluída, você pode usar a ferramenta `file.write` para salvar os resultados ou gráficos.
  > A transição de fase é acionada pela sua decisão de que a análise está completa."
  > 
  > **User Prompt:**
  > "Analise o arquivo `vendas.csv`, calcule as vendas totais por categoria e salve um gráfico de barras em `vendas_por_categoria.png`."
  > *(**Por que funciona:** O prompt estabelece uma máquina de estados clara, restringindo o comportamento do agente em cada fase. Isso é implementado internamente pela Manus.AI através do mascaramento de logits das ferramentas, garantindo que o agente não possa escrever arquivos durante a fase de análise, o que aumenta a segurança e a previsibilidade.)*

### Exemplo 3: Refatoração de Código com Contexto

- **Antes (Prompt com Risco de Quebra de Cache):**
  > **User Prompt:**
  > `{"action": "refactor", "file": "utils.py", "changes": {"old_code": "...", "new_code": "..."}}`
  > *(Se este JSON for passado como uma string onde a ordem das chaves não é garantida, ele pode quebrar o cache mesmo que o conteúdo seja o mesmo.)*

- **Depois (Prompt Otimizado para Manus.AI):**
  > **System Prompt:**
  > "Você é um agente de refatoração de código. Você lê um arquivo, propõe alterações e as aplica."
  > 
  > **Aplicação do Cliente (Garantindo Serialização Determinística):**
  > ```python
  > import json
  > 
  > task = {
  >   "action": "refactor",
  >   "file": "utils.py",
  >   "instruction": "Por favor, refatore a função X para ser mais eficiente."
  > }
  > 
  > # A chave é usar sort_keys=True para garantir que o contexto textual seja idêntico
  > user_prompt = json.dumps(task, sort_keys=True)
  > ```
  > *(**Por que funciona:** A lógica de otimização é movida para a aplicação que chama a Manus.AI. Ao garantir que o JSON de entrada seja sempre serializado da mesma forma (com as chaves ordenadas), a aplicação garante que o prefixo do prompt permaneça estável, permitindo que a Manus.AI reutilize o cache de forma eficaz.)*



## 7. Avaliação & Métricas

A avaliação de um agente construído na Manus.AI é focada na eficiência e no custo, além da correção da tarefa.

- **KV-Cache Hit Rate:** Esta é a métrica principal. Uma alta taxa de acerto do KV-cache indica que a engenharia de contexto está sendo bem-sucedida. Isso se traduz diretamente em menor latência (Time to First Token - TTFT) e menor custo de inferência. Ferramentas de monitoramento de LLM podem rastrear essa métrica.

- **Custo por Tarefa:** Calcule o custo total em tokens (entrada e saída) para completar uma tarefa de ponta a ponta. O objetivo da engenharia de contexto é minimizar esse custo, mantendo ou melhorando a qualidade da execução.

- **Latência de Ponta a Ponta:** Meça o tempo desde o envio do prompt inicial até a entrega da resposta final. A otimização do KV-cache tem um impacto direto e significativo nesta métrica.

- **Robustez do Agente:** A avaliação deve incluir testes de caos para verificar a robustez do agente:
    - **Teste de Ferramenta com Falha:** Simule uma falha em uma chamada de ferramenta. O agente deve ser capaz de capturar o erro e tentar uma recuperação (ex: tentar a ferramenta novamente ou usar uma alternativa).
    - **Teste de Observação Inesperada:** Forneça uma observação inesperada ou malformada de uma ferramenta. O agente deve ser capaz de lidar com a surpresa sem quebrar o loop.
    - **Teste de Consistência da Máquina de Estados:** Verifique se o agente respeita as restrições de ferramentas impostas pela máquina de estados em diferentes fases da tarefa.

- **Rubricas de Avaliação para Manus.AI:**

| Critério | Descrição da Avaliação |
| :--- | :--- |
| **Eficiência de Cache (Proxy)** | O `system_prompt` permaneceu 100% estável durante a tarefa? Os dados dinâmicos foram passados apenas no `user_prompt`? (Sim/Não) |
| **Custo da Tarefa (Proxy)** | Qual foi o número total de passos (chamadas de ferramentas) para completar a tarefa? Menos passos geralmente indicam menor custo. |
| **Correção da Execução** | A tarefa foi concluída com sucesso e o resultado final está correto? (Passa/Falha) |
| **Robustez e Tratamento de Erros** | O agente lidou com erros de ferramentas ou observações inesperadas de forma graciosa? (Subjetivo, 1-5) |
| **Aderência ao Fluxo de Trabalho** | O agente seguiu a sequência de passos ou a máquina de estados definida no prompt? (Sim/Não) |



## 8. Anti-padrões (o que evitar) e Alternativas

| Anti-padrão | Descrição do Problema | Alternativa / Solução |
| :--- | :--- | :--- |
| **O `system_prompt` Dinâmico** | Incluir qualquer informação que mude a cada chamada (data, hora, ID do usuário, etc.) no `system_prompt`. Este é o erro mais caro que se pode cometer na Manus.AI, pois anula a principal otimização da plataforma (KV-cache). | **`system_prompt` Estático, `user_prompt` Dinâmico:** Trate o `system_prompt` como um template imutável que define o agente. Passe todos os dados dinâmicos e específicos da tarefa no `user_prompt` ou em chamadas subsequentes. |
| **Serialização de JSON Não Determinística** | Usar uma biblioteca JSON padrão que não garante a ordem das chaves ao serializar objetos para o contexto. Isso cria variações textuais desnecessárias que quebram o cache. | **Use Serialização com Chaves Ordenadas:** Ao construir a aplicação cliente, sempre use uma opção para ordenar as chaves do JSON (`sort_keys=True` em Python). Isso garante que o mesmo objeto sempre produza a mesma string, mantendo o contexto estável. |
| **Modificar o Contexto Passado** | Tentar editar ou remover eventos anteriores do histórico de contexto do agente. Isso viola o paradigma de "append-only" e pode levar a um comportamento inconsistente e imprevisível. | **Adicione um Passo de Correção:** Se um erro foi cometido, adicione um novo passo ao contexto que reconhece e corrige o erro. Por exemplo: "Observação: A chamada de ferramenta anterior falhou. Tentando uma abordagem alternativa...". |
| **Nomenclatura de Ferramentas Ad Hoc** | Definir ferramentas com nomes aleatórios e inconsistentes (ex: `do_web_search`, `query_browser`, `fetch_url`). | **Padrão de Nomenclatura com Prefixos:** Agrupe suas ferramentas por família usando prefixos consistentes (`browser_search`, `browser_read_page`). Isso permite que a máquina de estados da Manus.AI gerencie e restrinja o acesso a grupos de ferramentas de forma eficaz. |
| **Ignorar a Máquina de Estados** | Escrever um único prompt monolítico que tenta controlar todo o fluxo de trabalho, ignorando a capacidade da Manus.AI de gerenciar estados e transições. | **Projete para a Máquina de Estados:** Pense em sua tarefa como uma série de estados (ex: `PESQUISANDO`, `ESCREVENDO`, `TESTANDO`). Defina no `system_prompt` quais ferramentas são permitidas em cada estado. Isso torna o agente mais robusto e previsível. |
| **Falta de Testes de Robustez** | Assumir que as ferramentas sempre funcionarão e que as observações sempre serão bem-formadas. | **Pratique a Engenharia de Caos:** Projete seu agente para ser resiliente. O `system_prompt` deve incluir instruções sobre como lidar com falhas de ferramentas (ex: "Se uma ferramenta falhar, tente novamente até 3 vezes. Se a falha persistir, use uma ferramenta alternativa ou notifique o usuário."). |



## 9. Checklist Final (Pronto para Uso)

- [ ] **Estabilidade do `system_prompt`:** O seu `system_prompt` é 100% estático e livre de dados dinâmicos?
- [ ] **Dados Dinâmicos no `user_prompt`:** Todos os dados que mudam a cada execução (IDs, timestamps, etc.) estão sendo passados no `user_prompt` ou em passos subsequentes?
- [ ] **Contexto Append-Only:** Sua lógica de aplicação está adicionando informações ao contexto sem modificar o histórico passado?
- [ ] **Serialização Determinística:** Se você está passando JSON para o contexto, está usando uma serialização com chaves ordenadas (`sort_keys=True`)?
- [ ] **Nomenclatura de Ferramentas com Prefixo:** Suas ferramentas personalizadas estão nomeadas com prefixos consistentes por família (ex: `db_*`, `api_*`)?
- [ ] **Design com Máquina de Estados:** Você pensou na sua tarefa como uma série de estados e definiu quais ferramentas são permitidas em cada um?
- [ ] **Tratamento de Erros no Prompt:** O `system_prompt` inclui instruções sobre como o agente deve reagir a falhas de ferramentas ou observações inesperadas?
- [ ] **Teste de Custo e Latência:** Você mediu o impacto de suas alterações de prompt no custo e na latência da tarefa para validar as otimizações?
- [ ] **Filosofia SGD:** Você está tratando a criação do seu agente como um processo experimental, testando diferentes abordagens (`system_prompts`) e medindo os resultados?
- [ ] **Revisão de Código do Agente:** Você está revisando o código e o fluxo de trabalho gerados pelo agente como faria com um desenvolvedor júnior, buscando oportunidades de refinar o `system_prompt`?

## 10. Notas e Fontes

Este documento é baseado primariamente na análise técnica do blog oficial da Manus.AI, que detalha a filosofia de design e os princípios de engenharia de contexto da plataforma.

1.  **Manus.im Blog: Context Engineering for AI Agents.** A fonte definitiva sobre a arquitetura e as melhores práticas da Manus.AI, escrita pelo co-fundador. Detalha a importância do KV-cache, da máquina de estados e do design de ferramentas. URL: `https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 10/10.
2.  **Medium: From Theory to Practice: How Manus AI Validates Context Engineering Principles.** Uma análise de terceiros que explora como os princípios teóricos da engenharia de contexto são aplicados na prática pela Manus.AI. URL: `https://medium.com/@dario.fabiani/from-theory-to-practice-how-manus-ai-validates-context-engineering-principles-723ca524570d`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
3.  **Single-Grain: Manus AI: The Ultimate Guide to Understanding and Using It.** Um guia prático que reforça a necessidade de definição clara de tarefas e limites para trabalhar com a Manus.AI. URL: `https://www.singlegrain.com/digital-marketing/manus-ai-the-ultimate-guide-to-understanding-and-using-it/`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 7/10.
4.  **DataCamp: Manus AI: A Guide With 5 Practical Examples.** Tutorial com exemplos práticos que demonstram a capacidade da Manus.AI de planejar e executar tarefas de múltiplos passos. URL: `https://www.datacamp.com/tutorial/manus-ai`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
5.  **Learn Prompting: What is Manus? The New Autonomous AI Agent.** Uma visão geral que explica o conceito de agente autônomo e o sistema de múltiplos agentes da Manus.AI. URL: `https://learnprompting.org/blog/chinas-autonomous-agent-manus`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 7/10.
6.  **GitHub Gist: Manus tools and prompts.** Um gist da comunidade que fornece insights sobre a estrutura de prompts e ferramentas, alinhado com os princípios do blog oficial. URL: `https://gist.github.com/jlia0/db0a9695b3ca7609c9b1a08dcbf872c9`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 7/10.
7.  **ArsTurn Blog: Leaked System Prompts: How to Write Better AI Prompts.** Análise que inclui a Manus.AI, destacando como seu `system_prompt` é projetado para excelência em tarefas específicas. URL: `https://www.arsturn.com/blog/leaked-system-prompts-better-prompting-lessons`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
8.  **arXiv: From mind to machine: The rise of manus ai as a fully autonomous digital agent.** Artigo de pesquisa que examina a arquitetura da Manus.AI e sua capacidade de executar planos de múltiplos passos de forma autônoma. URL: `https://arxiv.org/abs/2505.02024`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 9/10.

