# Melhores Práticas de Prompting para Google Gemini

## 1. Resumo Executivo

- **Foco em Clareza e Contexto:** A filosofia de prompting do Google Gemini gira em torno de fornecer instruções claras, específicas e com o máximo de contexto relevante possível. A abordagem é conversacional, mas precisa.
- **Few-Shot Prompting é Chave:** Gemini se beneficia enormemente de exemplos no prompt (`few-shot prompting`). Fornecer alguns pares de entrada/saída de alta qualidade é uma das maneiras mais eficazes de guiar o modelo para o formato e o conteúdo de resposta desejados.
- **Estruturação do Prompt:** Dividir prompts complexos em componentes lógicos (como papel, tarefa, exemplos, e formato de saída) ajuda o modelo a processar a solicitação de forma mais eficaz. O uso de prefixos de resposta (ex: `Output: {`) também é uma técnica poderosa para controlar a formatação.
- **Multimodalidade Nativa:** Gemini foi construído desde o início para ser multimodal. A engenharia de prompts para Gemini frequentemente envolve a combinação de texto com imagens, áudio ou vídeo para realizar tarefas de análise ou geração que cruzam modalidades.
- **Controle de Criatividade vs. Precisão:** A experimentação com parâmetros como `temperature` é incentivada. Valores baixos (`0.2`, `0.3`) são recomendados para tarefas que exigem precisão e respostas determinísticas (como extração de dados ou geração de código), enquanto valores mais altos (`0.7`, `0.8`) são melhores para tarefas criativas.
- **Iteração e Refinamento:** A documentação do Google enfatiza que a engenharia de prompt é um processo iterativo. Comece com um prompt simples, analise a resposta, identifique as falhas e refine o prompt adicionando mais contexto, exemplos ou restrições.

## 2. Onde Brilha / Onde Evitar

| Onde Brilha (Casos de Uso Ideais) | Onde Evitar (Limites e Armadilhas) |
| :--- | :--- |
| **Tarefas Multimodais:** Análise de imagens, transcrição de áudio, descrição de vídeos. A capacidade nativa de processar múltiplos tipos de mídia em um único prompt é um diferencial. | **Prompts Excessivamente Complexos e sem Estrutura:** Tentar colocar uma tarefa muito complexa em um único parágrafo longo e desorganizado pode confundir o modelo. É melhor quebrar o prompt em seções. |
| **Geração de Respostas em Formatos Específicos (com Few-Shot):** Excelente para gerar JSON, XML, Markdown ou qualquer outro formato estruturado, desde que você forneça exemplos claros no prompt. | **Assumir Conhecimento de Domínio Específico:** O modelo não conhece a terminologia interna da sua empresa ou os detalhes do seu projeto. Esse contexto deve ser fornecido no prompt. |
| **Tarefas Criativas e de Brainstorming:** Com um `temperature` mais alto, Gemini é uma ferramenta poderosa para gerar ideias, escrever rascunhos de texto criativo ou explorar diferentes estilos de linguagem. | **Inconsistência nos Exemplos (Few-Shot):** Se os exemplos fornecidos no prompt tiverem formatos ou estilos inconsistentes, o modelo ficará confuso e a saída será imprevisível. A consistência nos exemplos é crucial. |
| **Raciocínio Lógico e Matemático (Chain-of-Thought):** Para problemas que exigem raciocínio passo a passo, a técnica de `Chain-of-Thought` (CoT), especialmente quando combinada com `few-shot`, funciona muito bem. | **Informação Irrelevante no Contexto:** Adicionar informações desnecessárias ou tangenciais ao prompt pode desviar o modelo e levá-lo a focar nos detalhes errados, degradando a qualidade da resposta. |



## 3. Técnicas de Prompting (Model-Specific)

As técnicas para Gemini focam em como estruturar a informação para que o modelo aprenda o padrão desejado em tempo de execução (in-context learning).

### A Arte do Few-Shot Prompting

Esta é a técnica mais enfatizada pelo Google. Em vez de apenas dizer ao modelo o que fazer (zero-shot), você mostra a ele. Um prompt few-shot contém um ou mais exemplos completos de entrada e saída.

**Estrutura de um Prompt Few-Shot:**

```
<Instrução geral da tarefa>

Input: <Exemplo de entrada 1>
Output: <Exemplo de saída 1>

Input: <Exemplo de entrada 2>
Output: <Exemplo de saída 2>

Input: <Sua nova entrada>
Output:
```

**Exemplo Prático: Classificação de Sentimento**

- **Zero-shot (Menos Eficaz):**
  > "Classifique o sentimento do seguinte texto como Positivo, Negativo ou Neutro.
  > Texto: Eu amei o novo filme!"

- **Few-shot (Mais Eficaz):**
  > "Classifique o sentimento do seguinte texto como Positivo, Negativo ou Neutro.
  > 
  > Texto: O serviço ao cliente foi terrível.
  > Sentimento: Negativo
  > 
  > Texto: O produto funciona exatamente como anunciado.
  > Sentimento: Neutro
  > 
  > Texto: Eu amei o novo filme!
  > Sentimento:"
  > *(**Por que funciona:** Os exemplos ensinam ao modelo o formato exato da saída e o critério de classificação, resultando em uma resposta mais precisa e previsível.)*

### Chain-of-Thought (CoT) Prompting

Para problemas que exigem raciocínio, o CoT incentiva o modelo a "pensar em voz alta", detalhando os passos que o levaram à resposta. Isso melhora drasticamente o desempenho em tarefas de lógica, matemática e raciocínio de senso comum.

**Exemplo Prático: Problema Matemático**

- **Prompt Padrão:**
  > "João tem 5 maçãs. Ele come 2 e depois compra mais 10. Quantas maçãs ele tem?"

- **Prompt com Chain-of-Thought (Few-Shot):**
  > "Q: Maria tinha 3 bolas de gude. Ela ganhou mais 4 de seu amigo. Quantas bolas de gude ela tem no total?
  > A: Maria começou com 3 bolas de gude. Ela ganhou mais 4. Então, 3 + 4 = 7. A resposta é 7.
  > 
  > Q: João tem 5 maçãs. Ele come 2 e depois compra mais 10. Quantas maçãs ele tem?
  > A:"
  > *(**Por que funciona:** O exemplo mostra ao modelo não apenas a resposta, mas o processo de raciocínio. Ao gerar a resposta para a segunda pergunta, ele imitará esse processo, detalhando os passos e chegando a uma conclusão mais confiável.)*

### Controle de Formato com Prefixos

Uma maneira simples e eficaz de garantir o formato da saída é fornecer o início da resposta que você espera.

**Exemplo Prático: Geração de JSON**

> "Extraia as informações do pedido a seguir em formato JSON. Os campos válidos são `item`, `quantidade` e `cliente`.
> 
> Pedido: 'Eu gostaria de dois hambúrgueres para o João.'
> 
> Saída:
> ```json
> {
>   "item": "hambúrguer",
>   "quantidade": 2,
>   "cliente": "João"
> }
> ```
> 
> Pedido: 'Três pizzas para a Maria, por favor.'
> 
> Saída:
> ```json
> {
>"
  > *(**Por que funciona:** O prefixo `{"` força o modelo a iniciar a resposta como um objeto JSON, aumentando drasticamente a probabilidade de gerar um JSON válido e completo.)*



## 4. Metaprompting: Refinando Prompts com Gemini

O metaprompting com Gemini é uma técnica conversacional para refinar a qualidade dos seus prompts. A ideia é tratar o Gemini como um parceiro de brainstorming para construir o prompt perfeito para uma tarefa.

### Metaprompting para Análise de Persona

Em vez de simplesmente pedir ao modelo para adotar uma persona, peça a ele para analisar as características dessa persona primeiro. Isso resulta em uma imitação muito mais autêntica.

**Exemplo de Metaprompt:**

> "Quero que você atue como um crítico de cinema cético e sarcástico. Antes de começar, analise e liste as características específicas que compõem a abordagem única de um crítico com esse perfil. Considere o tom, o vocabulário, a estrutura das frases e o tipo de observações que ele faria. Depois de listar essas características, use-as para escrever uma crítica do filme 'A Vingança do Unicórnio'."

### Metaprompting para Criação de Prompts Few-Shot

Criar bons exemplos para prompts few-shot pode ser trabalhoso. Você pode usar o Gemini para gerar esses exemplos para você.

**Exemplo de Metaprompt:**

> "Estou criando um prompt few-shot para ensinar uma IA a converter linguagem natural em consultas SQL. A tarefa é gerar uma consulta que selecione o nome e o email de usuários de uma tabela `users` com base em seu país.
> 
> **Sua tarefa:**
> Gere três exemplos de pares `Input`/`Output` de alta qualidade para este prompt. Os exemplos devem cobrir diferentes países e usar uma formatação consistente.
> 
> **Exemplo de como um par deve se parecer:**
> Input: "Mostre os usuários do Brasil"
> Output: `SELECT name, email FROM users WHERE country = 'Brazil';`"

## 5. Tool/Function Calling & Saídas Estruturadas

O Gemini possui uma capacidade robusta de `tool calling` (também conhecido como `function calling`), permitindo que ele interaja com sistemas externos. A chave para o sucesso é fornecer uma definição clara da ferramenta e de seus parâmetros, preferencialmente com um esquema JSON.

### Definindo Ferramentas

Você deve fornecer ao Gemini uma lista de ferramentas que ele pode chamar. Cada ferramenta deve ter um nome, uma descrição e um esquema de parâmetros.

**Exemplo de Definição de Ferramenta (Conceitual):**

```json
{
  "name": "get_stock_price",
  "description": "Obtém o preço atual de uma ação com base em seu símbolo.",
  "parameters": {
    "type": "object",
    "properties": {
      "symbol": {
        "type": "string",
        "description": "O símbolo da ação no mercado de ações (ex: GOOG, AAPL)."
      }
    },
    "required": ["symbol"]
  }
}
```

### Garantindo Saídas Estruturadas com Few-Shot

A maneira mais confiável de obter uma saída JSON ou estruturada do Gemini é usar a técnica de `few-shot prompting`. Mostre ao modelo exatamente o que você quer.

**Exemplo de Prompt para JSON:**

> "Extraia as informações do usuário em um objeto JSON.
> 
> Texto: 'O nome dela é Ana, ela tem 30 anos e mora em São Paulo.'
> JSON: `{"nome": "Ana", "idade": 30, "cidade": "São Paulo"}`
> 
> Texto: 'João, 45 anos, de Belo Horizonte.'
> JSON:"
> *(**Por que funciona:** O exemplo estabelece um precedente claro. O modelo entende que deve extrair as entidades e formatá-las como um objeto JSON com chaves específicas.)*

### Prompting Multimodal

O Gemini pode processar informações de múltiplas modalidades (texto, imagem, áudio, vídeo) em um único prompt. A prática recomendada é fornecer os arquivos de mídia e, em seguida, um prompt de texto que instrua o modelo sobre a tarefa a ser realizada.

**Exemplo de Prompt Multimodal (Imagem e Texto):**

> **[Imagem de um gráfico de pizza de vendas]**
> 
> **Prompt de Texto:** "Com base no gráfico de pizza fornecido, qual categoria de produto representa a maior porcentagem de vendas? Apresente a resposta em um objeto JSON com as chaves `categoria` e `porcentagem`."



## 6. Boas Práticas por Tarefa (Exemplos)

A seguir, exemplos práticos de prompts "antes e depois" que demonstram as melhores práticas para o Gemini.

### Exemplo 1: Extração de Entidades para JSON

- **Antes (Prompt Zero-Shot Ambíguo):**
  > "Extraia as informações deste texto em JSON: O pedido #123 foi feito por Carlos Silva para 2 caixas de parafusos."
  > *(Pode funcionar, mas o formato do JSON (nomes das chaves, tipos de dados) é deixado a critério do modelo.)*

- **Depois (Prompt Few-Shot Específico):**
  > "Extraia as informações do pedido em um objeto JSON com as chaves `order_id`, `customer_name` e `items` (uma lista de objetos com `product` e `quantity`).
  > 
  > **Exemplo 1:**
  > Input: "O pedido #101 foi feito por Ana para 1 laptop."
  > Output: `{"order_id": 101, "customer_name": "Ana", "items": [{"product": "laptop", "quantity": 1}]}`
  > 
  > **Exemplo 2:**
  > Input: "O pedido #123 foi feito por Carlos Silva para 2 caixas de parafusos."
  > Output:"
  > *(**Por que funciona:** O exemplo few-shot define inequivocamente o schema do JSON, incluindo a estrutura aninhada para `items`, garantindo uma saída consistente e previsível.)*

### Exemplo 2: Resumo de Texto com Restrições

- **Antes (Prompt Simples):**
  > "Resuma este artigo."
  > *(O comprimento e o estilo do resumo são imprevisíveis.)*

- **Depois (Prompt com Restrições Claras):**
  > "Resuma o seguinte artigo em exatamente 3 frases. O resumo deve focar nos impactos econômicos discutidos no texto.
  > 
  > Artigo: [Texto longo do artigo]"
  > *(**Por que funciona:** O prompt fornece restrições claras (`3 frases`) e um foco específico (`impactos econômicos`), guiando o modelo para um resumo conciso e relevante.)*

### Exemplo 3: Geração de Código com Contexto

- **Antes (Prompt sem Contexto):**
  > "Escreva uma função em Python para chamar a API do Stripe."
  > *(Muito vago. Qual endpoint? Como lidar com a autenticação?)*

- **Depois (Prompt com Contexto e Papel):**
  > "Atue como um desenvolvedor Python sênior. Escreva uma função chamada `create_stripe_charge` que usa a biblioteca `stripe` do Python.
  > 
  > **Requisitos:**
  > - A função deve aceitar os argumentos: `amount` (em centavos), `currency` (ex: 'usd') e `source` (ex: 'tok_visa').
  > - A chave de API do Stripe deve ser lida de uma variável de ambiente chamada `STRIPE_API_KEY`.
  > - Inclua tratamento de erros para possíveis exceções da API do Stripe.
  > - Adicione docstrings claras explicando como usar a função."
  > *(**Por que funciona:** Define um papel, nome da função, parâmetros, requisitos de segurança (variável de ambiente), tratamento de erros e documentação, tratando o LLM como um engenheiro de software.)*

### Exemplo 4: Análise de Imagem (Multimodal)

- **Antes (Prompt de Texto Vago):**
  > "O que há nesta imagem?"
  > *(Gera uma descrição genérica.)*

- **Depois (Prompt Multimodal Específico):**
  > **[Imagem de uma prateleira de supermercado com vários produtos]**
  > 
  > **Prompt de Texto:** "Analise a imagem da prateleira do supermercado e atue como um gerente de inventário. Crie uma lista em formato de tabela Markdown com as seguintes colunas: `Produto`, `Quantidade Estimada` e `Ação Recomendada` (ex: 'Reabastecer', 'OK'). Foque apenas nos produtos na prateleira do meio."
  > *(**Por que funciona:** Combina a entrada de imagem com um prompt de texto que define um papel, uma tarefa específica (contagem de inventário), um formato de saída (tabela Markdown) e um escopo (prateleira do meio), resultando em uma análise útil e acionável.)*



## 7. Avaliação & Métricas

A avaliação de prompts para Gemini deve ser focada na qualidade e na consistência da resposta, especialmente ao usar técnicas como few-shot e chain-of-thought.

- **Acurácia da Resposta (Fact-Checking):** Para tarefas baseadas em conhecimento, a resposta está factualmente correta? Isso muitas vezes requer a comparação com uma fonte de verdade ("gold standard") ou verificação manual.

- **Aderência ao Formato:** A métrica mais fácil de automatizar. A saída corresponde exatamente ao formato solicitado no prompt (ex: é um JSON válido? Segue a estrutura do exemplo few-shot?). A avaliação pode ser feita com parsers e validadores de esquema.

- **Qualidade do Raciocínio (para CoT):** Ao usar Chain-of-Thought, o processo de raciocínio gerado é lógico e correto? Avaliar o caminho é tão importante quanto avaliar a resposta final. Isso pode ser feito através de revisão por pares ou usando um LLM-as-Judge com um prompt focado em avaliar a lógica.

- **Generalização do Padrão (para Few-Shot):** O modelo aprendeu o padrão dos exemplos ou apenas os memorizou? Teste com entradas que são estruturalmente semelhantes, mas diferentes em conteúdo, para ver se ele generaliza o padrão corretamente.

- **Testes A/B de Prompts:** A melhor maneira de avaliar dois prompts concorrentes é através de testes A/B. Envie o mesmo conjunto de entradas para ambos os prompts e compare a qualidade das saídas lado a lado. Use uma rubrica para pontuar qual prompt se sai melhor de forma consistente.

- **Rubricas de Avaliação para Gemini:**

| Critério | Descrição da Avaliação |
| :--- | :--- |
| **Aderência ao Exemplo (Few-Shot)** | A saída segue o mesmo formato, estilo e estrutura dos exemplos fornecidos no prompt? (Sim/Não) |
| **Lógica do Raciocínio (CoT)** | O caminho de raciocínio passo a passo é lógico, coerente e leva corretamente à resposta final? (Subjetivo, 1-5) |
| **Correção da Resposta Final** | A resposta final para a pergunta ou tarefa está correta? (Passa/Falha) |
| **Criatividade (se aplicável)** | Para tarefas criativas, a resposta é original, interessante e relevante? (Subjetivo, 1-5) |
| **Multimodalidade (se aplicável)** | A resposta integra corretamente as informações de todas as modalidades fornecidas (texto, imagem, etc.)? (Sim/Não) |



## 8. Anti-padrões (o que evitar) e Alternativas

| Anti-padrão | Descrição do Problema | Alternativa / Solução |
| :--- | :--- | :--- |
| **O Prompt "Tudo em Um"** | Tentar misturar a instrução, os exemplos e a nova entrada em um único parágrafo confuso. Isso dificulta para o modelo distinguir entre a tarefa, os exemplos e a pergunta real. | **Estrutura Clara com Rótulos:** Use rótulos claros como `Instrução:`, `Exemplo 1 Input:`, `Exemplo 1 Output:`, e `Input Final:`. Essa estrutura ajuda o modelo a entender as diferentes partes do seu prompt. |
| **Exemplos Inconsistentes no Few-Shot** | Fornecer exemplos que usam formatos de saída diferentes (ex: um exemplo retorna uma lista separada por vírgulas, outro retorna uma lista com marcadores). Isso confunde o modelo sobre qual padrão seguir. | **Consistência é a Chave:** Garanta que todos os exemplos no seu prompt few-shot sigam exatamente o mesmo formato de entrada e saída. A consistência é mais importante do que o número de exemplos. |
| **Falta de Contexto de Domínio** | Pedir ao modelo para realizar uma tarefa que requer conhecimento especializado (ex: "Resuma nosso relatório de engenharia interna") sem fornecer o contexto necessário. | **Forneça o Conhecimento no Prompt:** Se a tarefa requer conhecimento que não é público, você deve fornecer esse conhecimento no próprio prompt. Copie e cole o texto relevante, defina a terminologia interna ou forneça as regras de negócios necessárias. |
| **Usar Zero-Shot para Formatos Complexos** | Pedir ao modelo para gerar uma saída em um formato complexo e específico (como um JSON aninhado ou um arquivo de configuração YAML) sem fornecer um exemplo. | **Sempre Use Few-Shot para Formatos Complexos:** Para qualquer tarefa que exija uma saída estruturada, a melhor prática é sempre fornecer pelo menos um exemplo completo (few-shot) que demonstre a estrutura exata que você espera. |
| **Ignorar o Parâmetro `temperature`** | Usar o valor padrão de `temperature` para todas as tarefas. Usar uma `temperature` alta para tarefas que exigem precisão (como geração de código ou extração de fatos) pode levar a respostas incorretas ou "criativas" demais. | **Ajuste a `temperature` à Tarefa:** Para tarefas factuais, analíticas ou de código, use uma `temperature` baixa (ex: `0.2`). Para tarefas criativas, de brainstorming ou de resumo, uma `temperature` mais alta (ex: `0.7`) pode ser benéfica. |
| **Prompts Negativos** | Dizer ao modelo o que *não* fazer (ex: "Não inclua o preço no resumo"). Embora às vezes funcione, os LLMs geralmente respondem melhor a instruções positivas sobre o que *fazer*. | **Instruções Positivas e Afirmativas:** Em vez de dizer o que evitar, diga o que incluir. Por exemplo, em vez de "Não inclua o preço", diga "Crie um resumo que inclua apenas o nome do produto e a categoria". |


_
## 9. Checklist Final (Pronto para Uso)

- [ ] **Clareza e Especificidade:** O seu prompt é claro, conciso e específico sobre a tarefa a ser realizada?
- [ ] **Uso de Few-Shot:** Para tarefas com formatos de saída específicos ou que exigem um padrão, você está fornecendo pelo menos um exemplo de alta qualidade (few-shot)?
- [ ] **Consistência nos Exemplos:** Os exemplos no seu prompt few-shot são consistentes em formato, estilo e estrutura?
- ] **Chain-of-Thought (CoT):** Para problemas de raciocínio, você está incentivando o modelo a "pensar em voz alta" para melhorar a precisão?
- [ ] **Estrutura do Prompt:** O prompt está bem estruturado com rótulos claros para separar instruções, exemplos e a entrada final?
- [ ] **Contexto Relevante:** Você forneceu todo o contexto de domínio necessário para que o modelo entenda a tarefa sem ambiguidades?
- [ ] **Controle de Formato:** Você está usando técnicas como prefixos de resposta ou exemplos few-shot para garantir que a saída venha no formato desejado?
- [ ] **Ajuste de `temperature`:** A `temperature` está ajustada de acordo com a natureza da tarefa (baixa para precisão, alta para criatividade)?
- [ ] **Instruções Positivas:** O seu prompt foca em dizer ao modelo o que fazer, em vez de o que não fazer?
- [ ] **Prompt Multimodal:** Se estiver usando imagens ou outros meios, o seu prompt de texto instrui claramente o modelo sobre o que fazer com eles?

## 10. Notas e Fontes

Este documento foi compilado a partir da documentação oficial do Google para desenvolvedores de IA, guias da comunidade e melhores práticas publicadas por especialistas em engenharia de prompt.

1.  **Google AI for Developers: Prompt design strategies.** A documentação oficial e principal fonte para as melhores práticas de prompting com a API Gemini. URL: `https://ai.google.dev/gemini-api/docs/prompting-strategies`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 10/10.
2.  **Google for Developers: Write better prompts for Gemini.** Guia do Google Cloud com dicas para escrever prompts eficazes, focando em uma abordagem conversacional. URL: `https://cloud.google.com/gemini/docs/discover/write-prompts`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 10/10.
3.  **Prompting Guide: Gemini 1.5 Pro.** Análise e guia da comunidade sobre as capacidades do Gemini 1.5 Pro, incluindo raciocínio de longo contexto. URL: `https://www.promptingguide.ai/models/gemini-pro`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
4.  **Prompting Guide: Getting Started with Gemini.** Um guia introdutório que cobre os conceitos básicos de como usar e solicitar os modelos Gemini. URL: `https://www.promptingguide.ai/models/gemini`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
5.  **Google for Workspace Blog: Writing Effective AI Prompts.** Dicas do Google para usuários de negócios sobre como escrever prompts eficazes para o Gemini no Workspace. URL: `https://workspace.google.com/resources/ai/writing-effective-prompts/`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 9/10.
6.  **Medium: Prompt engineering techniques to try out with Gemini.** Artigo da comunidade com técnicas práticas, como a análise de persona via metaprompting. URL: `https://medium.com/google-cloud/prompt-engineering-techniques-to-try-out-with-gemini-872748940eb0`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 7/10.
7.  **Learn Prompting: Gemini 1.5 Pro.** Documentação da comunidade com foco em técnicas avançadas de engenharia de prompt para o Gemini. URL: `https://learnprompting.org/docs/models/gemini-1.5-pro`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
8.  **PDF Guide: A quick-start handbook for effective prompts.** Um guia em PDF do Google Workspace com exemplos práticos de prompts para tarefas de negócios. URL: `https://services.google.com/fh/files/misc/gemini-for-google-workspace-prompting-guide-101.pdf`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 9/10.
_
