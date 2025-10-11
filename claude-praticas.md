_# Melhores Práticas de Prompting para Anthropic Claude

## 1. Resumo Executivo

- **Foco em Explicitude e Estrutura:** A filosofia de prompting da Anthropic para os modelos Claude (especialmente a família Claude 3 e 4) enfatiza a clareza, a explicitude e, crucialmente, a **estrutura**. O uso de tags XML para delinear diferentes partes do prompt é uma técnica de assinatura e altamente recomendada.
- **Dar um Papel é Essencial:** Atribuir um papel claro ao Claude através do `system_prompt` (ex: "Você é um advogado especialista em patentes") é uma das maneiras mais eficazes de direcionar o tom, o estilo, a persona e o nível de conhecimento da resposta.
- **"Pense Antes de Falar" (Chain-of-Thought):** Claude se beneficia enormemente da instrução para "pensar passo a passo". A técnica de pedir ao modelo para colocar seu raciocínio dentro de tags `<thinking>` antes de fornecer a resposta final em tags `<answer>` melhora drasticamente a qualidade em tarefas complexas.
- **Pré-preenchimento para Controle de Formato:** A técnica de pré-preencher o início da resposta do Claude (ex: `Aqui está a resposta em formato JSON:
{`) é uma maneira poderosa e direta de forçar a saída para um formato específico.
- **Contexto é Rei, mas a Motivação é a Rainha:** Além de fornecer contexto, explicar o "porquê" de uma instrução (a motivação) ajuda o Claude a generalizar melhor e a produzir resultados mais alinhados com o objetivo do usuário.
- **Gerenciamento de Estado para Tarefas Longas:** Para tarefas que excedem a janela de contexto, a Anthropic promove um paradigma de gerenciamento de estado explícito, onde o modelo é instruído a usar ferramentas (como `git` ou salvar arquivos de progresso) para manter o controle de seu trabalho de forma incremental.

## 2. Onde Brilha / Onde Evitar

| Onde Brilha (Casos de Uso Ideais) | Onde Evitar (Limites e Armadilhas) |
| :--- | :--- |
| **Tarefas que Exigem Raciocínio Complexo:** A técnica de Chain-of-Thought, especialmente quando estruturada com tags XML, torna o Claude excelente para problemas de lógica, planejamento e análise profunda. | **Instruções Vagas e Implícitas:** Claude 4+ é menos propenso a "adivinhar" a intenção do usuário. Prompts como "melhore este texto" são menos eficazes do que prompts explícitos que definem *como* o texto deve ser melhorado. |
| **Geração de Conteúdo Estruturado (com XML e Prefill):** Ideal para gerar código, JSON, XML ou qualquer outro texto que precise seguir uma estrutura rígida, graças ao forte condicionamento do modelo em tags e pré-preenchimento. | **Exemplos Inconsistentes ou com Erros:** Claude presta muita atenção aos detalhes nos exemplos. Se seus exemplos contiverem erros ou inconsistências, o modelo provavelmente os replicará fielmente na saída. |
| **Adoção de Personas e Papéis Específicos:** O uso de `system_prompts` para definir um papel é extremamente eficaz, tornando o Claude uma ótima ferramenta para simulações, redação em estilos específicos e atuação como especialista de domínio. | **Prompts Negativos e Proibições:** Assim como outros LLMs, Claude responde melhor a instruções positivas (o que fazer) do que a instruções negativas (o que não fazer). Em vez de "Não use jargões", prefira "Explique em termos simples". |
| **Tarefas de Longa Duração com Gerenciamento de Estado:** Com as técnicas corretas para salvar o progresso, o Claude pode lidar com projetos de codificação complexos, pesquisa extensiva ou escrita de documentos longos que abrangem múltiplas interações. | **Confiar na Memória Implícita de Longo Prazo:** Não espere que o Claude se "lembre" de detalhes de conversas anteriores sem que esse contexto seja explicitamente fornecido novamente no prompt atual. O gerenciamento de estado deve ser explícito. |
_


## 3. Técnicas de Prompting (Model-Specific)

As técnicas de prompting da Anthropic são focadas em fornecer uma estrutura clara e explícita para o modelo seguir.

### A Onipresença das Tags XML

Esta é a técnica mais característica e recomendada para o Claude. Usar tags XML para estruturar o prompt ajuda o modelo a entender as diferentes partes da sua solicitação e a focar no que é importante.

**Estrutura Comum com Tags XML:**

```xml
<prompt>
  <instrucao>Sua tarefa é analisar o seguinte documento e resumí-lo.</instrucao>

  <documento>
    {Texto do documento aqui...}
  </documento>

  <formato_saida>O resumo deve ter 3 parágrafos.</formato_saida>
</prompt>
```

**Por que funciona:** As tags criam uma separação lógica clara, muito mais robusta do que simplesmente usar títulos ou quebras de linha. O modelo é treinado para prestar atenção especial ao conteúdo dentro dessas tags.

### Deixe o Claude Pensar (Chain-of-Thought com Estrutura)

Esta técnica força o Claude a detalhar seu processo de raciocínio antes de dar a resposta final, o que melhora a precisão em tarefas complexas. A combinação com tags XML é particularmente poderosa.

**Exemplo Prático:**

> "Analise a seguinte solicitação do cliente e determine se ela se qualifica para um reembolso com base em nossa política.
> 
> **Política de Reembolso:**
> <politica>
> - Reembolsos são aceitos em até 30 dias após a compra.
> - O produto não deve ter sido usado.
> </politica>
> 
> **Solicitação do Cliente:**
> <solicitacao>
> Comprei o produto há 2 semanas e o usei uma vez, mas não gostei.
> </solicitacao>
> 
> Primeiro, pense passo a passo sobre a solicitação em relação à política dentro de tags `<thinking>`. Em seguida, forneça sua resposta final ('Sim' ou 'Não') dentro de tags `<answer>`."

**Resposta Esperada do Claude:**

```xml
<thinking>
1.  O cliente comprou o produto há 2 semanas, o que está dentro do prazo de 30 dias.
2.  No entanto, o cliente afirma que usou o produto uma vez.
3.  Nossa política exige que o produto não tenha sido usado.
4.  Portanto, a solicitação não atende a todos os critérios.
</thinking>
<answer>Não</answer>
```

### Pré-preenchendo a Resposta de Claude (`Prefill`)

Esta é uma maneira direta de forçar o Claude a iniciar sua resposta de uma maneira específica, garantindo que ele siga o formato desejado.

**Como funciona:** Na API, você pode fornecer o início da resposta do `assistant`. O modelo então continuará a partir desse ponto.

**Exemplo Prático (Conceitual):**

- **Seu Prompt:**
  > "Resuma o texto a seguir em três pontos principais, em formato JSON.
  > <texto>...</texto>"

- **Pré-preenchimento que você fornece:**
  > `Aqui está o resumo em JSON:
  > {
    "pontos_principais": [
  `

- **Resultado:** O Claude é forçado a completar a estrutura JSON, tornando a saída muito mais confiável.


_

_
## 4. Metaprompting: Usando Claude para Melhorar Prompts

A Anthropic oferece uma ferramenta de "Prompt Improver" e incentiva o metaprompting como uma forma de refinar as instruções para o próprio Claude. A ideia é usar a inteligência do modelo para otimizar a maneira como você se comunica com ele.

### O Metaprompt Padrão da Anthropic

A Anthropic sugere um metaprompt robusto que pede ao Claude para atuar como um engenheiro de prompt e reescrever uma instrução para ser mais clara, explícita e eficaz.

**Exemplo de Metaprompt:**

> "Você é um engenheiro de prompt especialista em otimizar prompts para o modelo de IA Claude. Sua tarefa é reescrever o prompt do usuário para ser mais claro, explícito e fácil de entender para o Claude. Use tags XML para estruturar o prompt reescrito e siga as melhores práticas da Anthropic.
> 
> **Prompt Original do Usuário:**
> `<prompt_original>`
> Resuma nosso último relatório de vendas e me diga o que é importante.
> `</prompt_original>`
> 
> Agora, reescreva este prompt."

### Reverse Metaprompting para Documentação

Após uma interação bem-sucedida (ou malsucedida), você pode pedir ao Claude para analisar a conversa e gerar um prompt que capture o processo de forma otimizada para uso futuro.

**Exemplo de Reverse Metaprompt:**

> "Nós acabamos de ter uma longa conversa para depurar um problema em um script Python. Analise nosso histórico de chat, identifique os passos chave que tomamos para encontrar e corrigir o bug, e então crie um prompt único e bem-estruturado que um futuro usuário poderia usar para guiar o Claude através do mesmo processo de depuração de forma muito mais eficiente."

## 5. Tool/Function Calling & Saídas Estruturadas

O `tool use` no Claude é projetado para ser robusto e confiável, e ele se beneficia enormemente das mesmas técnicas de clareza e estruturação usadas em prompts de texto.

### Definindo Ferramentas com Clareza

A definição da ferramenta (seu nome, descrição e parâmetros) é o seu "prompt" para o `tool use`. Uma descrição clara e detalhada é crucial.

**Exemplo de Definição de Ferramenta:**

```json
{
  "name": "send_slack_message",
  "description": "Envia uma mensagem para um canal ou usuário específico no Slack. Use isso para notificações, alertas ou para comunicar resultados de tarefas.",
  "input_schema": {
    "type": "object",
    "properties": {
      "channel_id": {
        "type": "string",
        "description": "O ID do canal ou usuário do Slack para onde a mensagem será enviada (ex: C12345, U67890)."
      },
      "message_text": {
        "type": "string",
        "description": "O conteúdo da mensagem a ser enviada. Pode incluir formatação Markdown do Slack."
      }
    },
    "required": ["channel_id", "message_text"]
  }
}
```

### Forçando Saídas Estruturadas com XML e `Prefill`

Mesmo sem `tool use`, você pode obter saídas estruturadas de forma confiável combinando tags XML e pré-preenchimento.

**Exemplo de Prompt para JSON:**

> **Prompt:**
> "Extraia as informações do usuário em um formato JSON. O objeto JSON deve estar contido dentro de tags `<json>`.
> 
> <usuario>
> Nome: João da Silva, Idade: 42, Email: joao.silva@example.com
> </usuario>"
> 
> **Pré-preenchimento:**
> `<json>
{`

*(**Por que funciona:** O prompt define a estrutura com a tag `<json>`, e o pré-preenchimento força o Claude a começar a escrever um objeto JSON, garantindo que a saída seja um JSON válido dentro das tags esperadas.)*

### Gerenciamento de Estado para Tarefas de Longa Duração

Para tarefas que exigem múltiplas chamadas de ferramentas e manutenção de estado, a melhor prática é instruir o Claude a salvar seu progresso em um formato estruturado.

**Exemplo de Prompt para Agente de Codificação:**

> "Sua tarefa é construir uma aplicação web. Após cada passo, atualize o arquivo `progress.json` com o status da tarefa.
> 
> **Formato do `progress.json`:**
> `{"completed_steps": [], "next_step": ""}`
> 
> Comece criando a estrutura de diretórios inicial. Depois, atualize o `progress.json`."



## 6. Boas Práticas por Tarefa (Exemplos)

A seguir, exemplos práticos de prompts "antes e depois" que demonstram as melhores práticas para o Claude.

### Exemplo 1: Geração de Código com Persona

- **Antes (Prompt Genérico):**
  > "Escreva um script Python para fazer upload de um arquivo para o S3."
  > *(Funciona, mas o código pode ser simplista, sem tratamento de erros ou boas práticas.)*

- **Depois (Prompt com Persona e Estrutura):**
  > **System Prompt:** "Você é um Engenheiro de DevOps Sênior da AWS, especialista em Python e automação de nuvem. Você escreve código limpo, robusto e pronto para produção."
  > 
  > **User Prompt:**
  > "<tarefa>
  > Escreva uma função Python chamada `upload_to_s3`.
>   </tarefa>
  > 
  > <requisitos>
  > - A função deve aceitar `file_path`, `bucket_name`, e `object_name` como argumentos.
  > - Use a biblioteca `boto3`.
  > - Inclua tratamento de erros completo para credenciais ausentes, bucket não encontrado e outros erros comuns do S3.
  > - Adicione log detalhado para cada etapa do processo (ex: "Iniciando upload...", "Upload concluído com sucesso.").
  > - Inclua docstrings no estilo Google e anotações de tipo (type hints).
  > </requisitos>"
  > *(**Por que funciona:** O `system_prompt` define um alto padrão de qualidade. As tags XML estruturam a solicitação, e os requisitos detalhados garantem que o código seja robusto, bem documentado e pronto para uso em produção.)*

### Exemplo 2: Análise de Documento com Chain-of-Thought

- **Antes (Prompt Direto):**
  > "Leia este contrato e me diga se é um bom negócio."
  > *(A resposta será uma opinião superficial e pouco confiável.)*

- **Depois (Prompt com CoT e Estrutura):**
  > "Você é um advogado especialista em análise de contratos. Analise o contrato abaixo e identifique os pontos de risco para o nosso lado (A Empresa Contratante).
  > 
  > <contrato>
  > {Texto completo do contrato...}
  > </contrato>
  > 
  > <instrucao>
  > Primeiro, dentro de tags `<thinking>`, pense passo a passo. Identifique cada cláusula, analise suas implicações para a Empresa Contratante e avalie o nível de risco (Baixo, Médio, Alto).
  > 
  > Depois, dentro de tags `<resumo_riscos>`, forneça uma lista com marcadores dos 3 principais riscos identificados.
  > </instrucao>"
  > *(**Por que funciona:** O prompt força um processo de análise metódico (CoT), tornando o raciocínio transparente e a conclusão mais fundamentada. A estruturação da saída com tags garante que a resposta seja fácil de consumir.)*

### Exemplo 3: Extração de Dados para JSON

- **Antes (Prompt Ambíguo):**
  > "Pegue os detalhes deste email e coloque em JSON.
  > Email: ..."
  > *(O esquema do JSON será inconsistente e imprevisível.)*

- **Depois (Prompt com Exemplo e Estrutura XML):**
  > "Sua tarefa é extrair informações de emails de confirmação de pedido para um formato JSON estruturado.
  > 
  > <exemplo>
  >   <email_input>
  >     De: loja@exemplo.com
  >     Assunto: Seu pedido #123 foi confirmado!
  >     Corpo: Olá, seu pedido contendo 1x Laptop (R$ 5000) e 2x Mouse (R$ 150 cada) foi confirmado.
  >   </email_input>
  >   <json_output>
  >     {"order_id": "123", "total_value": 5300, "items": [{"name": "Laptop", "quantity": 1, "price": 5000}, {"name": "Mouse", "quantity": 2, "price": 150}]}
  >   </json_output>
  > </exemplo>
  > 
  > Agora, extraia as informações do seguinte email:
  > <email_input>
  >   {Novo texto de email aqui...}
  > `</email_input>`"
  > *(**Por que funciona:** O uso de tags `<exemplo>` com sub-tags `<email_input>` e `<json_output>` cria um exemplo few-shot perfeitamente estruturado. Claude aprenderá o mapeamento exato do texto para o esquema JSON, garantindo alta precisão.)*


## 7. Avaliação & Métricas

A avaliação de prompts para Claude deve se concentrar na robustez do raciocínio e na precisão da aderência à estrutura solicitada.

- **Validação da Estrutura de Saída:** A resposta está em conformidade com a estrutura solicitada? Se você pediu uma saída dentro de tags `<answer>`, ela está lá? Se pediu JSON, é um JSON válido? Esta é uma verificação binária e fácil de automatizar.

- **Análise do Raciocínio (Chain-of-Thought):** A avaliação do conteúdo dentro das tags `<thinking>` é crucial. O raciocínio é lógico? Ele leva corretamente à conclusão? Um raciocínio falho, mesmo com uma resposta final correta, indica um prompt frágil. Isso pode ser avaliado por humanos ou por um LLM-as-Judge com uma rubrica específica.

- **Acurácia da Adoção de Persona:** O quão bem o modelo incorporou o papel definido no `system_prompt`? A avaliação pode ser feita através de uma rubrica que analisa o tom, o vocabulário, o estilo e o nível de conhecimento da resposta em relação à persona solicitada.

- **Teste de Robustez a Variações:** Um bom prompt para Claude é robusto a pequenas variações na entrada. Teste o mesmo prompt com diferentes formulações da mesma pergunta ou com dados de entrada ligeiramente diferentes para garantir que o desempenho seja consistente.

- **Métrica de "Explicitude Necessária":** Uma métrica mais subjetiva, mas útil: quão explícito você precisou ser para obter o resultado desejado? Prompts mais eficientes são aqueles que alcançam o resultado desejado com o mínimo de instruções excessivamente detalhadas, pois indicam que o modelo generalizou bem a partir de instruções de alto nível.

- **Rubricas de Avaliação para Claude:**

| Critério | Descrição da Avaliação |
| :--- | :--- |
| **Aderência à Estrutura XML** | A saída utilizou corretamente as tags XML especificadas no prompt (ex: `<thinking>`, `<answer>`)? (Sim/Não) |
| **Qualidade do Raciocínio CoT** | O processo de pensamento dentro das tags `<thinking>` é lógico, correto e completo? (Subjetivo, 1-5) |
| **Fidelidade à Persona** | A resposta reflete de forma convincente o papel, o tom e o conhecimento definidos no `system_prompt`? (Subjetivo, 1-5) |
| **Correção da Resposta Final** | A resposta final dentro das tags `<answer>` está correta e completa? (Passa/Falha) |
| **Robustez a Variações de Input** | O prompt produz resultados consistentes mesmo com pequenas alterações na formulação da pergunta? (Subjetivo, 1-5) |



## 8. Anti-padrões (o que evitar) e Alternativas

| Anti-padrão | Descrição do Problema | Alternativa / Solução |
| :--- | :--- | :--- |
| **O "Muro de Texto"** | Fornecer um prompt longo e não estruturado, misturando instruções, contexto e exemplos em um único bloco de texto. Isso sobrecarrega o modelo e torna difícil para ele discernir as diferentes partes da tarefa. | **Estrutura com Tags XML:** Sempre use tags XML (`<documento>`, `<instrucao>`, `<exemplo>`) para delinear e separar claramente as diferentes seções do seu prompt. Isso melhora drasticamente a capacidade do Claude de entender a solicitação. |
| **Confiar na Ambiguidade** | Fazer perguntas abertas como "O que você acha deste documento?" esperando uma análise profunda. Os modelos Claude 4+ são otimizados para seguir instruções explícitas, não para adivinhar a intenção. | **Seja Explícito e Direto:** Diga exatamente o que você quer. Em vez de "O que você acha?", diga "Analise este documento e identifique os três principais pontos fortes e as três principais fraquezas". |
| **Exemplos Mal Formatados ou Incorretos** | Fornecer exemplos (few-shot) que não seguem as melhores práticas, contêm erros ou são inconsistentes entre si. Claude presta muita atenção aos exemplos e replicará os erros. | **Cure Seus Exemplos:** Trate os exemplos como a parte mais importante do seu prompt. Garanta que eles sejam perfeitos, consistentes e um modelo exato do que você espera na saída. Use tags XML dentro dos seus exemplos para máxima clareza. |
| **Ignorar o `system_prompt`** | Colocar todas as instruções no prompt do usuário, incluindo a definição da persona. Isso funciona, mas é menos eficaz do que usar o `system_prompt` para seu propósito designado. | **Use o `system_prompt` para Definir o Papel:** Reserve o `system_prompt` para definir a persona, o tom e as regras de alto nível que devem se aplicar a toda a conversa. Coloque as instruções da tarefa específica no prompt do usuário. |
| **Pedir a Resposta Direta para Problemas Complexos** | Pedir a solução para um problema de lógica ou matemática sem pedir o raciocínio. Isso aumenta a chance de uma resposta incorreta, pois o modelo tenta "adivinhar" a resposta em um único passo. | **Exija o Chain-of-Thought (CoT):** Sempre instrua o Claude a "pensar passo a passo" dentro de tags `<thinking>` antes de fornecer a resposta final. Isso força um processo de raciocínio mais deliberado e aumenta a precisão. |
| **Esquecer de Pré-preencher (`Prefill`)** | Ao precisar de um formato de saída muito rígido como JSON, apenas pedir por ele no prompt e esperar que o modelo acerte sempre. | **Force o Formato com `Prefill`:** Para garantir a máxima confiabilidade na formatação, sempre pré-preencha o início da resposta do assistente. Se você quer JSON, comece a resposta com `{"`. Isso remove a ambiguidade e força o modelo no caminho certo. |


_

_## 9. Checklist Final (Pronto para Uso)

- [ ] **Uso de Tags XML:** O seu prompt está estruturado com tags XML para separar claramente as instruções, o contexto, os exemplos e outros componentes?
- [ ] **Persona no `system_prompt`:** Você definiu um papel claro e específico para o Claude no `system_prompt` para guiar seu comportamento geral?
- [ ] **Instruções Explícitas:** As instruções da tarefa são diretas, claras e explícitas, evitando ambiguidades?
- [ ] **Chain-of-Thought (CoT) para Raciocínio:** Para tarefas complexas, você está instruindo o Claude a pensar passo a passo dentro de tags `<thinking>` antes de dar a resposta final?
- [ ] **Exemplos de Alta Qualidade:** Se estiver usando exemplos (few-shot), eles são consistentes, corretos e bem estruturados (idealmente com tags XML)?
- [ ] **Contexto e Motivação:** Você forneceu não apenas o contexto necessário, mas também o "porquê" por trás da tarefa para ajudar o Claude a entender o objetivo?
- [ ] **Pré-preenchimento (`Prefill`) para Formato:** Para formatos de saída rígidos como JSON, você está pré-preenchendo o início da resposta para forçar o formato correto?
- [ ] **Divisão de Tarefas Complexas:** Tarefas muito grandes estão sendo divididas em uma cadeia de prompts menores e mais gerenciáveis?
- [ ] **Gerenciamento de Estado Explícito:** Para tarefas de longa duração, o prompt inclui instruções para o Claude salvar seu progresso em arquivos ou usar ferramentas como o `git`?
- [ ] **Instruções Positivas:** O prompt foca em dizer ao Claude o que fazer, em vez de se concentrar no que evitar?

## 10. Notas e Fontes

Este documento foi compilado a partir da documentação oficial da Anthropic, dos guias de engenharia da empresa e de melhores práticas compartilhadas pela comunidade de desenvolvedores.

1.  **Anthropic Docs: Claude 4 prompt engineering best practices.** A fonte primária e mais detalhada sobre as técnicas de prompt para os modelos Claude 4, enfatizando a explicitude e o uso de exemplos. URL: `https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/claude-4-best-practices`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 10/10.
2.  **Anthropic Docs: Prompt engineering overview.** Uma visão geral dos princípios de engenharia de prompt para Claude. URL: `https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 10/10.
3.  **Anthropic Engineering: Claude Code: Best practices for agentic coding.** Um post de blog detalhado sobre como usar o Claude para tarefas de codificação de longa duração, com foco em gerenciamento de estado. URL: `https://www.anthropic.com/engineering/claude-code-best-practices`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 9/10.
4.  **Anthropic News: Prompt improver.** Anúncio e explicação da ferramenta da Anthropic para melhorar prompts automaticamente, revelando a filosofia de metaprompting da empresa. URL: `https://www.anthropic.com/news/prompt-improver`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 9/10.
5.  **GitHub: Anthropic Claude Cookbooks.** Uma coleção de notebooks Jupyter com exemplos de código e receitas para realizar tarefas comuns com o Claude, incluindo o uso de ferramentas. URL: `https://github.com/anthropics/claude-cookbooks`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
6.  **Anthropic Engineering: Effective context engineering for AI agents.** Artigo que, embora focado em agentes, descreve princípios de engenharia de contexto que são fundamentais para o prompting eficaz no Claude. URL: `https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 9/10.
7.  **Dev.to: Claude Prompting Guide.** Um guia da comunidade que resume dicas gerais para um prompting eficaz com o Claude. URL: `https://dev.to/shawon/claude-prompting-guide-general-tips-for-effective-prompting-5hi5`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 7/10.
8.  **AWS Docs: Anthropic Claude 3.5 Sonnet Judge Prompt.** Documentação da AWS que mostra como o Claude é usado como um "juiz" para avaliar outras respostas de LLM, revelando como ele pode ser solicitado para tarefas de avaliação lógica. URL: `https://docs.aws.amazon.com/bedrock/latest/userguide/model-evaluation-type-judge-prompt-claude-sonnet.html`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
_
