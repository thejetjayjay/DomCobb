# Melhores Práticas de Prompting para ChatGPT-5

## 1. Resumo Executivo

- **Foco em Agentes e Ferramentas:** O ChatGPT-5 representa um salto significativo em direção a fluxos de trabalho agentic, com melhorias substanciais em `tool calling`, `function calling` e compreensão de contexto longo. A engenharia de prompts deve priorizar a clareza na definição de tarefas, ferramentas e critérios de parada.
- **Controle de Raciocínio:** A introdução do parâmetro `reasoning_effort` permite um controle granular sobre a profundidade da exploração do modelo. Valores mais baixos favorecem a eficiência e a baixa latência, enquanto valores mais altos incentivam uma análise mais profunda e autônoma.
- **API de Respostas (Responses API):** Para fluxos de trabalho agentic complexos, a OpenAI recomenda o uso da nova `Responses API`, que persiste o estado do raciocínio entre as chamadas de ferramentas, resultando em saídas mais eficientes e inteligentes.
- **Preâmbulos de Ferramentas (Tool Preambles):** O modelo é treinado para fornecer planos e atualizações de progresso através de "preâmbulos de ferramentas". Os prompts podem e devem direcionar a frequência, o estilo e o conteúdo dessas atualizações para melhorar a experiência do usuário.
- **Estratégias de Coleta de Contexto:** O GPT-5 é otimizado para estratégias de busca paralelas e eficientes. Os prompts devem guiar o modelo a começar com uma busca ampla e depois afunilar para subconsultas focadas, com critérios de parada antecipada para evitar buscas desnecessárias.
- **Prompting Estruturado é Rei:** Mais do que nunca, o uso de tags estruturadas (semelhantes a XML ou HTML), delimitadores claros e formatação (como Markdown) é crucial para guiar o modelo e garantir saídas previsíveis e de alta qualidade.

## 2. Onde Brilha / Onde Evitar

| Onde Brilha (Casos de Uso Ideais) | Onde Evitar (Limites e Armadilhas) |
| :--- | :--- |
| **Aplicações Agentic Complexas:** Orquestração de múltiplas ferramentas, execução de tarefas de longa duração e fluxos de trabalho que exigem planejamento e adaptação. | **Tarefas Simples e de Baixa Latência (com `reasoning_effort` alto):** Usar a capacidade total de raciocínio para tarefas triviais é um desperdício de recursos e aumenta a latência. Ajuste o `reasoning_effort` para baixo. |
| **Geração e Refatoração de Código:** Com o `GPT-5-Codex`, o modelo demonstra uma capacidade aprimorada para tarefas de engenharia de software, como criação de features, testes, debugging e revisões de código. | **Ambientes sem Acesso a Ferramentas:** O verdadeiro poder do GPT-5 é desbloqueado quando ele pode interagir com o mundo exterior. Usá-lo em um vácuo limita severamente seu potencial agentic. |
| **Análise e Síntese de Longo Contexto:** A janela de contexto expandida, combinada com um melhor raciocínio, torna-o ideal para analisar documentos extensos, repositórios de código ou transcrições longas. | **Geração de Conteúdo sem Verificação:** Apesar das melhorias, o modelo ainda pode alucinar. Para tópicos que exigem alta precisão factual, a verificação humana ou o uso de RAG (Retrieval-Augmented Generation) continua sendo essencial. |
| **Prototipagem Rápida de Aplicações:** A capacidade de gerar código e interagir com APIs permite a criação rápida de protótipos funcionais diretamente a partir de uma descrição em linguagem natural. | **Decisões de Alto Risco sem Supervisão:** A autonomia do modelo não substitui o julgamento humano. Decisões críticas (financeiras, médicas, etc.) devem sempre ter um humano no loop para aprovação final. |


_## 3. Técnicas de Prompting (Model-Specific)

O GPT-5 introduz novas nuances na engenharia de prompts, movendo-se de simples instruções para a orquestração de um agente. As técnicas a seguir são específicas para extrair o máximo de performance do modelo.

### Instruções de Sistema vs. Usuário

- **System Prompt:** É aqui que a identidade, as restrições de alto nível e as regras fundamentais do agente devem ser definidas. Devido à otimização de cache do GPT-5, o system prompt deve ser o mais estável possível. Evite informações dinâmicas como timestamps, que podem invalidar o cache e aumentar custos e latência. Pense no system prompt como a "constituição" do seu agente.
- **User Prompt:** Deve conter a tarefa imediata ou a pergunta do usuário. É a parte dinâmica da interação. Para tarefas agentic, o prompt do usuário pode ser uma meta de alto nível, e o modelo usará suas ferramentas e raciocínio para quebrá-la em etapas.

### Role Framing (Definição de Papel)

Atribuir um papel ao GPT-5 é mais crucial do que nunca. No entanto, em vez de um simples "Você é um assistente prestativo", os papéis para o GPT-5 devem ser mais parecidos com descrições de cargo para um agente autônomo.

**Exemplo de Role Framing para um Agente de Pesquisa:**

> "Você é um Agente de Pesquisa Autônomo. Seu objetivo é responder a perguntas complexas de forma abrangente e precisa. Você deve usar suas ferramentas de busca para coletar informações de múltiplas fontes, sintetizar os resultados, e apresentar um relatório final bem estruturado. Você opera de forma autônoma, decidindo os melhores passos para atingir o objetivo, e só deve pedir esclarecimentos ao usuário se estiver completamente bloqueado."

### Controle de Raciocínio e "Eagerness"

Uma das inovações mais importantes do GPT-5 é a capacidade de controlar o comportamento agentic.

- **`reasoning_effort`**: Este parâmetro é um controle deslizante para a profundidade do raciocínio. Para tarefas diretas, como extrair uma informação de um texto, use um `reasoning_effort` baixo. Para tarefas complexas que exigem planejamento e múltiplas ferramentas, use um valor mais alto.
- **Prompting for Less Eagerness (Menos Ímpeto):** Para limitar a exploração e reduzir a latência, instrua o modelo a ser mais direto. Isso é útil em cenários onde a eficiência é mais importante que a profundidade.
  > **Prompt:** "Resolva a tarefa com o mínimo de chamadas de ferramentas possível. Priorize a ação sobre a pesquisa extensiva. Se você tiver 70% de confiança na resposta, prossiga."

- **Prompting for More Eagerness (Mais Ímpeto):** Para encorajar a autonomia e a persistência, especialmente em tarefas de pesquisa ou debugging, use um prompt que incentive o modelo a continuar até a resolução completa.
  > **Prompt de Persistência:** "Você é um agente persistente. Continue trabalhando até que a consulta do usuário seja completamente resolvida. Nunca pare ou retorne ao usuário em caso de incerteza; em vez disso, pesquise ou deduza o próximo passo mais razoável. Decida o que fazer e aja."

### Delimitação de Escopo e Critérios de Aceitação (DoD)

Para fluxos de trabalho agentic, é fundamental definir claramente o que significa "concluído".

- **Delimitadores:** Use tags como `<context_gathering>`, `<planning>`, `<execution>` para estruturar o processo de pensamento do modelo. Dentro dessas tags, defina as regras.
- **Critérios de Parada Antecipada (Early Stop Criteria):** Dê ao modelo condições explícitas para parar de pesquisar ou executar. Isso economiza custos e tempo.
  > **Exemplo:** "Critérios de parada para a coleta de contexto: (1) Você pode nomear o conteúdo exato a ser alterado. (2) Os principais resultados da busca convergem (~70%) em uma única área/caminho. (3) Você coletou informações de pelo menos 3 fontes primárias."

- **Definition of Done (DoD):** Defina explicitamente o que constitui uma tarefa concluída. Isso é especialmente importante para tarefas de geração de código ou relatórios.
  > **Exemplo:** "A tarefa está concluída quando o código React for gerado, todos os testes unitários passarem, e a documentação para o componente for escrita em Markdown."

### Controle de Estilo/Voz e Antídotos contra Alucinação

- **Tool Preambles:** Para controlar como o modelo se comunica com o usuário durante a execução, você pode especificar o formato dos "tool preambles".
  > **Prompt:** "<tool_preambles>Antes de cada chamada de ferramenta, forneça um plano de uma única frase sobre o que você está prestes a fazer. Ex: 'Agora vou pesquisar os resultados financeiros da Empresa X no último trimestre.'</tool_preambles>"

- **Groundedness (Ancoragem):** Para combater alucinações, especialmente em tarefas de síntese, instrua o modelo a basear cada afirmação em fontes explícitas coletadas durante a fase de pesquisa e a citá-las.
  > **Prompt:** "Para cada afirmação no relatório final, você deve incluir uma citação no formato [fonte_id] que corresponda à fonte de onde a informação foi extraída. Não inclua nenhuma informação que não possa ser rastreada a uma fonte específica."
_


## 4. Metaprompting: Criando Prompts que Geram Prompts

Com o GPT-5, o metaprompting evolui de uma técnica de refinamento para uma ferramenta de criação de agentes. Em vez de pedir ao modelo para simplesmente "melhorar um prompt", você pode pedir a ele para "projetar o system prompt completo para um agente com um objetivo específico".

**Exemplo de Metaprompt para Criar um Agente de Debugging:**

> "Você é um Arquiteto de Agentes de IA. Sua tarefa é projetar o system prompt para um agente de debugging de software chamado 'CodeHealer'.
> 
> **Objetivo do Agente:** Identificar e corrigir bugs em trechos de código Python.
> 
> **Ferramentas Disponíveis:**
> - `execute_code(code: str)`: Executa um trecho de código e retorna a saída ou o erro.
> - `read_file(file_path: str)`: Lê o conteúdo de um arquivo.
> - `write_file(file_path: str, content: str)`: Escreve conteúdo em um arquivo.
> - `search_web(query: str)`: Pesquisa na web por documentação ou soluções de erro.
> 
> **O system prompt que você criar deve incluir:**
> 1.  Um **papel** claro e conciso para o agente 'CodeHealer'.
> 2.  Um **processo de raciocínio passo a passo** que o agente deve seguir (ex: 1. Entender o erro, 2. Formular hipóteses, 3. Testar hipóteses com as ferramentas, 4. Propor uma correção, 5. Verificar a correção).
> 3.  **Regras de engajamento**, incluindo quando persistir na solução e quando pedir mais informações ao usuário.
> 4.  Instruções sobre como usar as **ferramentas de forma eficaz e segura** (ex: não executar código desconhecido sem análise).
> 5.  Um guia sobre o **formato da resposta final**, que deve incluir o código corrigido e uma explicação da correção.
> 
> Gere o system prompt completo para o agente 'CodeHealer' em um bloco de código Markdown."

## 5. Tool/Function Calling & Saídas Estruturadas

A capacidade aprimorada de `tool calling` do GPT-5 é um de seus principais diferenciais. A chave para o sucesso é a clareza na definição das ferramentas e na especificação do formato de saída desejado.

### Como Pedir JSON Válido e Schemas

O GPT-5 tem uma compreensão muito mais robusta de esquemas JSON. A melhor prática é fornecer um esquema JSON completo na sua definição de ferramenta ou no prompt.

**Exemplo de Definição de Ferramenta com Schema:**

```json
{
  "name": "create_user_profile",
  "description": "Cria um novo perfil de usuário no sistema.",
  "parameters": {
    "type": "object",
    "properties": {
      "full_name": {
        "type": "string",
        "description": "O nome completo do usuário."
      },
      "email": {
        "type": "string",
        "description": "O endereço de e-mail do usuário, deve ser único."
      },
      "age": {
        "type": "integer",
        "description": "A idade do usuário."
      },
      "is_premium_member": {
        "type": "boolean",
        "description": "Indica se o usuário é um membro premium.",
        "default": false
      }
    },
    "required": ["full_name", "email", "age"]
  }
}
```

### Validação e Recuperação (Retry) em Caso de JSON Inválido

Mesmo com esquemas, o modelo pode ocasionalmente gerar um JSON malformado. Seu código deve antecipar isso. Uma boa prática é criar um loop de recuperação que captura o erro, informa o modelo sobre o erro específico e pede uma correção.

**Exemplo de Prompt de Recuperação:**

> "A chamada de função anterior falhou com o seguinte erro de validação JSON: `[insira o erro de validação aqui]`.
> 
> A saída gerada foi:
> ```json
> [insira o JSON inválido aqui]
> ```
> 
> Por favor, corrija o JSON para que ele se adeque ao esquema da ferramenta e tente a chamada de função novamente. Preste atenção especial aos tipos de dados e aos campos obrigatórios."

### Controle de Passos, Guardrails e Verificações Automáticas

Para agentes autônomos, é vital ter `guardrails` (barreiras de proteção) para evitar ações indesejadas.

- **Constraining Tool Selection (Restringindo a Seleção de Ferramentas):** Conforme mencionado no blog da Manus AI, é possível usar o preenchimento de prefixo para forçar o modelo a usar uma ferramenta específica ou um subconjunto de ferramentas. Por exemplo, em um estado de "pesquisa", você pode restringir o modelo a usar apenas as ferramentas que começam com `search_`.
- **Confirmation Steps (Passos de Confirmação):** Para ações destrutivas (como `delete_file` ou `execute_payment`), o prompt deve instruir o modelo a sempre pedir confirmação ao usuário antes de executar a ação. Isso pode ser implementado como uma ferramenta `ask_user_confirmation(question: str)`.
  > **Prompt de Guardrail:** "Antes de usar qualquer ferramenta marcada como 'destrutiva' (ex: `delete_file`), você deve primeiro chamar a função `ask_user_confirmation` com uma pergunta clara sobre a ação que está prestes a tomar. Não prossiga sem a confirmação explícita do usuário."



## 6. Boas Práticas por Tarefa (Exemplos)

A seguir, exemplos práticos de prompts "antes e depois" que demonstram as melhores práticas para o GPT-5.

### Exemplo 1: Síntese de Múltiplos Documentos

- **Antes (Prompt Genérico):**
  > "Resuma estes artigos sobre a crise climática."
  > *(Este prompt é vago, não define o formato da saída nem o público-alvo.)*

- **Depois (Prompt Otimizado para GPT-5):**
  > **System Prompt:**
  > "Você é um Analista de Pesquisa especializado em políticas ambientais. Sua tarefa é sintetizar informações de múltiplas fontes em um resumo executivo claro e objetivo para legisladores.
  > 
  > **Processo:**
  > 1.  Leia todos os documentos fornecidos no contexto.
  > 2.  Identifique os 3-5 principais pontos de concordância e discordância entre os autores.
  > 3.  Estruture a saída em formato Markdown.
  > 4.  Para cada ponto-chave, cite as fontes de onde a informação foi extraída usando o formato `[fonte_id]`.
  > 5.  A síntese final não deve exceder 500 palavras."
  > 
  > **User Prompt:**
  > "Sintetize os seguintes artigos sobre a crise climática: `[Artigo 1]`, `[Artigo 2]`, `[Artigo 3]`."
  > *(**Por que funciona:** Define um papel, um processo claro, um formato de saída, a necessidade de ancoragem (groundedness) e restrições de tamanho, guiando o modelo para uma resposta de alta qualidade e relevância.)*

### Exemplo 2: Geração de Código com Testes

- **Antes (Prompt Simples):**
  > "Escreva uma função em Python para verificar se um e-mail é válido."
  > *(Este prompt provavelmente gerará uma função simples usando regex, mas sem testes ou contexto de implementação.)*

- **Depois (Prompt Otimizado para GPT-5):**
  > **System Prompt:**
  > "Você é um Engenheiro de Software Sênior que segue os princípios de Test-Driven Development (TDD). Ao receber uma solicitação de código, seu processo é:
  > 1.  Primeiro, escrever uma suíte de testes unitários usando a biblioteca `pytest` que cubra os casos de sucesso, casos de borda e casos de falha.
  > 2.  Em seguida, escrever o código Python que faça todos os testes passarem.
  > 3.  Finalmente, fornecer o código da função e o código dos testes em blocos de código separados e nomeados."
  > 
  > **User Prompt:**
  > "Preciso de uma função Python chamada `is_valid_email` que valide um endereço de e-mail. Considere e-mails com subdomínios e extensões de domínio mais recentes."
  > *(**Por que funciona:** O prompt define um papel e um fluxo de trabalho profissional (TDD), forçando o modelo a pensar nos casos de teste antes de escrever o código, o que resulta em uma solução mais robusta e confiável.)*

### Exemplo 3: Análise de Dados com Ferramentas

- **Antes (Prompt Ambíguo):**
  > "Analise estes dados de vendas."
  > *(Este prompt não especifica o objetivo da análise nem as ferramentas a serem usadas.)*

- **Depois (Prompt Otimizado para GPT-5):**
  > **System Prompt:**
  > "Você é um Analista de Dados. Sua tarefa é analisar conjuntos de dados para extrair insights acionáveis. Você tem acesso à ferramenta `data_analyzer`, que possui as seguintes funções:
  > - `data_analyzer.load_data(file_path: str)`
  > - `data_analyzer.get_summary_statistics()`
  > - `data_analyzer.find_correlations()`
  > - `data_analyzer.plot_trends(column: str)`
  > 
  > **Processo de Análise:**
  > 1.  Carregue os dados.
  > 2.  Gere estatísticas descritivas para entender a distribuição.
  > 3.  Identifique as correlações mais fortes entre as variáveis.
  > 4.  Plote as tendências de vendas ao longo do tempo.
  > 5.  Responda à pergunta do usuário com base nos insights encontrados, incluindo os gráficos gerados."
  > 
  > **User Prompt:**
  > "Analise o arquivo `sales_data_2025.csv` e me diga qual produto teve o maior crescimento de vendas no último ano."
  > *(**Por que funciona:** Define claramente as ferramentas disponíveis e um processo de análise estruturado, permitindo que o agente GPT-5 execute um fluxo de trabalho de análise de dados de forma autônoma e eficaz.)*

### Exemplo 4: Escrita Longa (Criação de Artigo)

- **Antes (Prompt Curto):**
  > "Escreva um artigo sobre os benefícios da inteligência artificial."
  > *(Gera um artigo genérico, sem estrutura ou profundidade.)*

- **Depois (Prompt Otimizado para GPT-5):**
  > **System Prompt:**
  > "Você é um Redator de Conteúdo Técnico especializado em IA. Sua tarefa é escrever artigos detalhados e bem estruturados.
  > 
  > **Estrutura do Artigo:**
  > 1.  **Introdução:** Apresente o tema e a tese principal.
  > 2.  **Seção 1: Eficiência e Automação:** Detalhe como a IA está otimizando processos.
  > 3.  **Seção 2: Inovação e Descoberta:** Explore como a IA está acelerando a pesquisa e o desenvolvimento.
  > 4.  **Seção 3: Desafios e Considerações Éticas:** Discuta os riscos e as mitigações.
  > 5.  **Conclusão:** Recapitule os pontos principais e ofereça uma visão para o futuro.
  > 
  > O artigo deve ter entre 1.500 e 2.000 palavras e ser escrito em um tom profissional, mas acessível."
  > 
  > **User Prompt:**
  > "Escreva um artigo completo sobre os benefícios da inteligência artificial para pequenas e médias empresas."
  > *(**Por que funciona:** Fornece uma estrutura detalhada (um "scaffold") para o artigo, garantindo que o resultado seja abrangente, bem organizado e atenda aos requisitos de profundidade e tamanho.)*

### Exemplo 5: Planejamento de Projeto

- **Antes (Prompt Vago):**
  > "Planeje o lançamento de um novo aplicativo."
  > *(Não há detalhes suficientes para criar um plano acionável.)*

- **Depois (Prompt Otimizado para GPT-5):**
  > **System Prompt:**
  > "Você é um Gerente de Projetos Sênior especializado em lançamentos de software. Sua tarefa é criar um plano de projeto detalhado usando o formato de tabela Markdown.
  > 
  > **Colunas da Tabela:**
  > - `Fase`: (ex: Pré-Lançamento, Lançamento, Pós-Lançamento)
  > - `Atividade`: A tarefa específica a ser realizada.
  > - `Responsável`: O time ou pessoa responsável (ex: Marketing, Engenharia).
  > - `Duração Estimada (dias)`: A duração estimada para a atividade.
  > - `Dependências`: Quais atividades devem ser concluídas antes desta.
  > 
  > O plano deve cobrir desde a fase final de testes até as primeiras duas semanas após o lançamento."
  > 
  > **User Prompt:**
  > "Crie um plano de projeto para o lançamento do nosso novo aplicativo de produtividade, 'TaskMaster Pro'. O lançamento está previsto para daqui a 60 dias."
  > *(**Por que funciona:** Exige uma saída altamente estruturada (tabela Markdown) e define as colunas exatas, forçando o GPT-5 a pensar de forma organizada e a gerar um plano de projeto claro, detalhado e imediatamente utilizável.)*



## 7. Avaliação & Métricas

A avaliação de um sistema agentic como o GPT-5 vai além da simples verificação da correção da resposta. É preciso medir a eficiência e a robustez do processo.

- **Groundedness (Ancoragem):** Para tarefas baseadas em conhecimento, a métrica mais importante é a ancoragem. A resposta é totalmente suportada pelas fontes fornecidas ou recuperadas? Isso é crucial para mitigar alucinações. A avaliação pode ser feita comparando cada afirmação da resposta com o texto original da fonte.

- **Factualidade e Completude:** A resposta está factualmente correta e aborda todos os aspectos da pergunta do usuário? Para isso, podem ser usados conjuntos de dados de "gold standard" ou a técnica de LLM-as-Judge, onde um outro LLM (ou o próprio GPT-5 com um prompt de avaliação) julga a qualidade da resposta com base em uma rubrica.

- **Eficiência do Processo (Process Efficiency):** Em um sistema agentic, a eficiência é chave. Métricas a serem rastreadas:
    - **Número de Chamadas de Ferramentas:** Quantas ferramentas foram necessárias para chegar à resposta? Menos é geralmente melhor, desde que a qualidade não seja comprometida.
    - **Latência Total:** Quanto tempo demorou desde a pergunta do usuário até a resposta final?
    - **Custo Total:** Qual foi o custo em tokens (entrada e saída) para completar a tarefa?

- **Testes Rápidos de Sanidade:** Antes de uma avaliação completa, execute alguns testes de sanidade:
    - **Prompt de Contradição:** Dê ao agente uma instrução e, em seguida, uma contraditória. Ele deve identificar o conflito.
    - **Teste de Ferramenta Inválida:** Peça ao agente para usar uma ferramenta que não existe. Ele deve lidar com o erro graciosamente.
    - **Teste de Limite de Contexto:** Envie um prompt que exceda o limite de contexto para ver como o agente reage.

- **Rubricas de Scoring (LLM-as-Judge):** Para escalar a avaliação, use um LLM como juiz com uma rubrica clara.

| Critério | Pontuação (1-5) | Descrição |
| :--- | :--- | :--- |
| **Relevância da Resposta** | 1-5 | A resposta aborda diretamente a pergunta do usuário? |
| **Ancoragem nas Fontes** | 1-5 | Todas as afirmações são suportadas pelas fontes citadas? |
| **Clareza e Formatação** | 1-5 | A resposta é bem escrita, estruturada e fácil de entender? |
| **Eficiência do Agente** | 1-5 | O agente usou o caminho mais direto e o menor número de ferramentas para chegar à solução? |



## 8. Anti-padrões (o que evitar) e Alternativas

| Anti-padrão | Descrição do Problema | Alternativa / Solução |
| :--- | :--- | :--- |
| **Prompts de "Tiro Único" para Tarefas Complexas** | Tentar resolver uma tarefa agentic complexa com um único prompt enorme e detalhado. Isso é frágil e difícil de depurar. | **Dividir para Conquistar:** Quebre a tarefa em fases lógicas. Use um prompt de sistema para definir o objetivo geral e, em seguida, use prompts de usuário menores para guiar o agente através de cada fase do plano. |
| **Incluir Dados Dinâmicos no System Prompt** | Colocar informações que mudam a cada execução (como data/hora atual ou ID do usuário) no system prompt. Isso invalida o KV-cache, aumentando drasticamente a latência e os custos. | **Mantenha o System Prompt Estável:** O system prompt deve ser como a "classe" de um objeto (estável e definindo o comportamento), enquanto o prompt do usuário é a "instância" (com os dados dinâmicos). Passe dados variáveis no prompt do usuário. |
| **Microgerenciamento do Agente** | Dizer ao agente exatamente qual ferramenta usar e com quais parâmetros em cada etapa. Isso anula o poder de raciocínio do GPT-5. | **Defina Metas, Não Passos:** Em vez de dizer "Chame a API de busca com a query 'X'", diga "Pesquise as últimas notícias sobre 'X' e me dê um resumo". Deixe o agente decidir a melhor forma de usar suas ferramentas para atingir a meta. |
| **Falta de Guardrails para Ações Destrutivas** | Permitir que o agente chame ferramentas que modificam o estado (escrever arquivos, apagar dados, fazer pagamentos) sem um mecanismo de confirmação. | **Implemente um Passo de Confirmação:** Crie uma ferramenta `ask_user_confirmation(question: str)` e instrua o modelo no system prompt a sempre chamar essa ferramenta antes de executar qualquer ação destrutiva, explicando claramente o que ele pretende fazer. |
| **Assumir que o Modelo "Lembra"** | Em interações longas, assumir que o modelo se lembrará de um detalhe mencionado muitos passos atrás. O contexto é finito e a atenção pode se degradar. | **Resuma e Reafirme o Contexto:** Para tarefas de longa duração, instrua o agente a periodicamente resumir o estado atual e os principais aprendizados. Inclua este resumo no contexto das etapas seguintes para manter o modelo "focado". |
| **Ignorar o Custo do Raciocínio** | Usar o `reasoning_effort` máximo para todas as tarefas, mesmo as mais simples, resultando em custos e latência desnecessários. | **Calibre o `reasoning_effort`:** Use o `reasoning_effort` como um controle. Para tarefas simples de extração ou formatação, use um valor baixo. Para planejamento complexo e autonomia, use um valor mais alto. Crie perfis de configuração para diferentes tipos de tarefas. |



## 9. Checklist Final (Pronto para Uso)

- [ ] **System Prompt Estável:** O seu system prompt está estável, livre de dados dinâmicos (como timestamps) para otimizar o KV-cache?
- [ ] **Definição de Papel (Role Framing):** O papel do agente está claramente definido com um objetivo, um processo e as ferramentas disponíveis?
- [ ] **Calibração do Raciocínio:** Você está usando o parâmetro `reasoning_effort` de forma apropriada para a complexidade da tarefa (baixo para tarefas simples, alto para exploração complexa)?
- [ ] **Definição de "Concluído" (DoD):** O escopo da tarefa e a "Definition of Done" (DoD) estão explicitamente definidos no prompt para guiar o agente até a finalização?
- [ ] **Estrutura e Delimitadores:** Você está usando uma estrutura clara (ex: Markdown, tags XML) e delimitadores para organizar o prompt e a saída esperada?
- [ ] **Schemas de Ferramentas:** As definições de suas ferramentas (`tools` ou `functions`) incluem schemas JSON claros e bem definidos?
- [ ] **Loop de Recuperação (Retry Loop):** Seu código de aplicação possui um loop de recuperação para lidar com JSONs inválidos ou falhas na chamada de ferramentas, informando o modelo sobre o erro?
- [ ] **Guardrails de Segurança:** Existem `guardrails` e passos de confirmação (ex: usar uma ferramenta `ask_user_confirmation`) para ações destrutivas ou irreversíveis?
- [ ] **Ancoragem (Groundedness):** Para tarefas baseadas em conhecimento, você está instruindo o modelo a citar fontes para cada afirmação, garantindo a ancoragem das respostas?
- [ ] **Metas em Vez de Microgerenciamento:** O prompt define metas de alto nível e deixa o agente decidir a melhor forma de usar suas ferramentas, em vez de microgerenciar cada passo?

## 10. Notas e Fontes

As informações contidas neste documento foram compiladas a partir da documentação oficial da OpenAI, guias da comunidade e análises de especialistas, com foco em fontes publicadas nos últimos 12 meses.

1.  **OpenAI Cookbook: GPT-5 Prompting Guide.** Documentação oficial com exemplos e as melhores práticas diretas da OpenAI. URL: `https://cookbook.openai.com/examples/gpt-5/gpt-5_prompting_guide`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 10/10.
2.  **OpenAI: Introducing GPT-5.** Anúncio oficial do lançamento, destacando as principais capacidades e melhorias do modelo. URL: `https://openai.com/index/introducing-gpt-5/`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 10/10.
3.  **OpenAI Cookbook: GPT-5-Codex Prompting Guide.** Guia específico para as capacidades de geração de código do GPT-5. URL: `https://cookbook.openai.com/examples/gpt-5-codex_prompting_guide`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 10/10.
4.  **OpenAI Community Discussion.** Fórum com discussões e exemplos práticos de desenvolvedores usando o GPT-5. URL: `https://community.openai.com/t/how-to-write-effective-prompts-for-gpt-5/1347538`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 9/10.
5.  **Jeff Su: ChatGPT-5 Prompting Best Practices.** Análise de um especialista sobre as melhores práticas de prompting para o novo modelo. URL: `https://www.jeffsu.org/chatgpt-5-prompting-best-practices/`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
6.  **freeCodeCamp: Prompt Engineering Cheat Sheet for GPT-5.** Um guia prático e direto com exemplos para desenvolvedores. URL: `https://www.freecodecamp.org/news/prompt-engineering-cheat-sheet-for-gpt-5/`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
7.  **Reddit r/PromptEngineering Community.** Discussão da comunidade sobre as novas formas de interagir com o GPT-5. URL: `https://www.reddit.com/r/PromptEngineering/comments/1mq2b73/how_to_talk_to_gpt5_based_on_openais_official/`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 7/10.
8.  **Nate's Newsletter: 16 Production Prompting Signals.** Análise aprofundada de sinais de prompting de produção extraídos do system prompt do GPT-5. URL: `https://natesnewsletter.substack.com/p/cracking-the-agent-code-16-production`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
9.  **OpenAI Cookbook: GPT-5 Prompt Migration and Improvement.** Guia para otimizar e migrar prompts de modelos anteriores para o GPT-5. URL: `https://cookbook.openai.com/examples/gpt-5/prompt-optimization-cookbook`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 10/10.
10. **Forte Labs: A Guide to the Claude 4 and ChatGPT 5 System Prompts.** Análise comparativa dos system prompts, oferecendo insights sobre as filosofias de design. URL: `https://fortelabs.com/blog/a-guide-to-the-claude-4-and-chatgpt-5-system-prompts/`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.

