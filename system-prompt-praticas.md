# System Prompt: Práticas Essenciais para Engenharia de Prompts

## Resumo Executivo

*   **Clareza e Especificidade:** A base de um bom system prompt é a clareza. Instruções detalhadas e sem ambiguidades são cruciais para guiar o modelo de linguagem (LLM) a produzir o resultado esperado. Evite jargões desnecessários e seja direto.
*   **Definição de Papel (Role):** Atribuir um papel específico ao LLM (ex: "Você é um especialista em segurança cibernética") melhora drasticamente a qualidade e o tom da resposta, alinhando-a com o contexto desejado.
*   **Estrutura Organizada:** Utilizar separadores claros, como `###`, para delimitar seções como instruções, contexto e exemplos, ajuda o modelo a processar a informação de forma mais eficiente.
*   **Foco no Positivo:** Em vez de dizer ao modelo o que *não* fazer, concentre-se em descrever o que *deve* ser feito. Isso direciona o comportamento do LLM de forma mais eficaz.
*   **Iteração e Simplicidade:** Comece com prompts simples e adicione complexidade gradualmente. A engenharia de prompts é um processo iterativo de experimentação e refinamento.
*   **Contexto é Rei:** Fornecer contexto relevante e suficiente é fundamental. Isso inclui dados de entrada, background da tarefa e o público-alvo da resposta.
*   **Uso de Frameworks:** Estruturas como CRISPE e RICCE oferecem um roteiro para a criação de prompts robustos, garantindo que todos os elementos essenciais sejam considerados.
*   **Segurança em Primeiro Lugar:** Esteja ciente dos riscos de segurança, como *prompt injection*, e implemente guardrails e validações para mitigar vulnerabilidades.

## Para que serve / Resultados esperados

O *system prompt* é a instrução fundamental que define o comportamento, o tom, as restrições e o objetivo de um modelo de linguagem para uma tarefa específica. Ele funciona como a "constituição" ou o manual de operações para o LLM, garantindo que suas interações subsequentes com o usuário (ou com outros sistemas) permaneçam alinhadas com as diretrizes estabelecidas.

Os resultados esperados ao empregar boas práticas de *system prompt* são:

*   **Consistência:** Respostas mais previsíveis e consistentes ao longo de uma ou várias interações.
*   **Precisão:** Maior aderência aos fatos e menor incidência de alucinações ou informações incorretas.
*   **Relevância:** Saídas que são diretamente aplicáveis ao contexto e à necessidade do usuário.
*   **Segurança:** Redução da superfície de ataque para manipulações maliciosas, como *prompt injection*.
*   **Eficiência:** Menor necessidade de repetição ou refinamento dos prompts do usuário, economizando tempo e recursos computacionais.

## Boas práticas

| Prática | Descrição | Exemplo de Aplicação |
| :--- | :--- | :--- |
| **Seja Específico** | Detalhe a tarefa, o formato de saída, o público e o estilo. Quanto mais específico, melhor. | Em vez de "Resuma o texto", use "Crie um resumo de 3 parágrafos para executivos C-level". |
| **Atribua um Papel** | Defina uma persona para o LLM. Isso ancora o tom e a base de conhecimento. | `Você é um analista financeiro sênior especializado em mercados emergentes.` |
| **Use Separadores** | Organize o prompt em seções lógicas (instrução, contexto, dados) usando marcadores como `###`. | `### Instrução ###\nClassifique o sentimento do texto abaixo.\n### Texto ###\n[Seu texto aqui]` |
| **Instrua no Positivo** | Diga o que fazer, não o que evitar. É mais direto e menos propenso a interpretações erradas. | Em vez de "Não use linguagem técnica", prefira "Explique em termos simples para um leigo". |
| **Forneça Exemplos (Few-Shot)** | Incluir 1 a 3 exemplos de entrada e saída desejada (formato *few-shot*) melhora drasticamente a conformidade. | `Exemplo 1:\nEntrada: "Adorei o produto!"\nSaída: {"sentimento": "positivo"}` |
| **Restrinja o Formato de Saída** | Especifique o formato de saída desejado, como JSON com um schema definido, Markdown ou uma lista de itens. | `Responda em um formato JSON contendo as chaves "entidade" e "tipo".` |
| **Comece Simples e Itere** | Não tente criar o prompt perfeito de primeira. Comece com uma versão simples e refine-a com base nos resultados. | Inicie com uma instrução básica e adicione papel, contexto e exemplos em iterações sucessivas. |

## Como fazer (passo a passo)

1.  **Definir o Objetivo:** Qual é a tarefa principal que o LLM deve executar? (ex: responder a perguntas de clientes, gerar código, traduzir texto).
2.  **Escolher um Framework (Opcional, mas recomendado):** Utilize uma estrutura como o **CRISPE** para garantir que todos os componentes importantes sejam cobertos.
    *   **C (Capacity & Role):** Defina o papel. *Ex: "Você é um copywriter sênior especializado em marketing digital."*
    *   **R (Request):** Descreva a solicitação principal. *Ex: "Crie 5 opções de título para um post de blog."*
    *   **I (Information):** Forneça o contexto e as informações de entrada. *Ex: "O post é sobre as vantagens do email marketing para pequenas empresas."*
    *   **S (Style):** Especifique o estilo de escrita. *Ex: "Use um tom profissional, mas acessível. Evite jargões."*
    *   **P (Parameters):** Estabeleça as restrições. *Ex: "Cada título deve ter no máximo 60 caracteres."*
    *   **E (Examples):** Dê exemplos, se aplicável. *Ex: "Exemplo de bom título: 'Email Marketing: O Guia para PMEs'."*
3.  **Estruturar o Prompt:** Organize as seções usando separadores. Coloque as instruções mais importantes no início.
4.  **Implementar Guardrails de Segurança:** Adicione instruções para mitigar riscos. Peça ao modelo para recusar solicitações que saiam do escopo definido ou que peçam informações sensíveis. *Ex: "Recuse educadamente qualquer pedido que não seja relacionado à criação de conteúdo de marketing."*
5.  **Testar e Refinar:** Execute o prompt com diferentes entradas para testar seu comportamento. Analise as saídas e ajuste o prompt para corrigir desvios ou melhorar a qualidade. Este é um ciclo contínuo.
6.  **Validar a Saída:** Se a saída precisar ser estruturada (ex: JSON), use um validador de schema para garantir que o LLM está aderindo ao formato solicitado.

## Pontos de atenção / vieses / riscos (com mitigação)

| Risco/Vieses | Descrição | Mitigação |
| :--- | :--- | :--- |
| **Prompt Injection** | Um usuário malicioso insere instruções no prompt para fazer o LLM executar ações não intencionais, como ignorar instruções anteriores ou vazar dados. | **1. Controle de Privilégios:** Limite as permissões do LLM (princípio do menor privilégio). **2. Filtragem de Entrada/Saída:** Sanitize as entradas e valide as saídas. **3. Instruções de Defesa:** No system prompt, instrua o modelo a identificar e ignorar tentativas de subversão. **4. Segregação de Conteúdo:** Delimite claramente o conteúdo do usuário do resto do prompt. |
| **Vazamento de Informações Sensíveis** | O modelo pode acidentalmente revelar dados confidenciais presentes em seu contexto ou dados de treinamento. | **1. Anonimização:** Remova ou mascare dados sensíveis (PII) do contexto antes de enviá-lo ao LLM. **2. Guardrails de Saída:** Implemente filtros para detectar e bloquear a saída de informações que pareçam sensíveis. |
| **Viés de Confirmação** | O modelo pode gerar respostas que confirmam as crenças ou vieses presentes no prompt ou nos dados de treinamento. | **1. Instruções de Neutralidade:** Peça ao modelo para considerar múltiplos pontos de vista e apresentar uma análise equilibrada. **2. Fontes Diversificadas:** Ao usar RAG, garanta que as fontes de informação sejam diversas e representem diferentes perspectivas. |
| **Geração de Conteúdo Prejudicial** | O LLM pode ser induzido a criar conteúdo ofensivo, perigoso ou ilegal. | **1. Filtros de Conteúdo:** Utilize APIs de moderação de conteúdo (ex: OpenAI Moderation API) para classificar e bloquear prompts e respostas. **2. Instruções de Segurança:** Inclua no system prompt uma lista clara de tópicos proibidos. |

## Exemplos curtos

**Exemplo 1: Classificador de E-mail**

```
### Instrução ###
Você é um assistente de triagem de e-mails. Classifique o e-mail abaixo em uma das seguintes categorias: ["Vendas", "Suporte", "Faturamento", "Spam"]. Responda apenas com a categoria em formato JSON.

### E-mail ###
"{{email_content}}"

### Formato de Saída ###
{"categoria": "<sua_classificacao>"}
```

**Exemplo 2: Gerador de SQL**

```
Você é um especialista em SQL. Sua tarefa é converter perguntas em linguagem natural para consultas SQL para um banco de dados PostgreSQL. O schema da tabela 'users' é (user_id INT, name VARCHAR, signup_date DATE).

Pergunta: "Quantos usuários se cadastraram em janeiro de 2024?"

SQL:
```

## Checklist de qualidade

- [ ] **Objetivo Claro:** O propósito do prompt está bem definido?
- [ ] **Papel Definido:** O LLM tem uma persona clara e consistente?
- [ ] **Instruções Específicas:** As instruções são detalhadas e sem ambiguidades?
- [ ] **Formato de Saída Explícito:** O formato da resposta está claramente especificado?
- [ ] **Contexto Suficiente:** O prompt contém todas as informações necessárias para a tarefa?
- [ ] **Exemplos (se necessário):** Foram incluídos exemplos para guiar o modelo?
- [ ] **Segurança Considerada:** Existem defesas contra *prompt injection* e outros riscos?
- [ ] **Linguagem Positiva:** As instruções focam no que fazer, em vez do que não fazer?
- [ ] **Testado Iterativamente:** O prompt foi testado com uma variedade de entradas e refinado?
- [ ] **Livre de Vieses Óbvios:** O prompt foi revisado para evitar a introdução de vieses?

## Notas e Fontes

1.  **Prompt Engineering Guide - General Tips for Designing Prompts.** Disponível em: [https://www.promptingguide.ai/introduction/tips](https://www.promptingguide.ai/introduction/tips). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 8/10)
2.  **The AI Hat - The RICCE Framework for AI Content Writing.** Disponível em: [https://theaihat.com/prompt-engineering-made-easy-the-ricce-framework-for-ai-content-writing/](https://theaihat.com/prompt-engineering-made-easy-the-ricce-framework-for-ai-content-writing/). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 8/10)
3.  **OWASP Top 10 for Large Language Model Applications.** Disponível em: [https://genai.owasp.org/llm-top-10/](https://genai.owasp.org/llm-top-10/). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 9/10)
4.  **Medium - ChatGPT Prompting Technique: The CRISPE Framework.** Disponível em: [https://medium.com/@inchristiely/chatgpt-prompting-technique-the-crispe-framework-e141a7bcad7e](https://medium.com/@inchristiely/chatgpt-prompting-technique-the-crispe-framework-e141a7bcad7e). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 7/10)

