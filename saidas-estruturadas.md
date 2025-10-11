# Saídas Estruturadas: JSON, Function Calling e Avaliação Automática

## Resumo Executivo

*   **O Problema da Saída em Texto Livre:** Por padrão, LLMs geram texto não estruturado, o que torna a extração de informações específicas uma tarefa difícil, propensa a erros e que exige parsing complexo. Para integrar LLMs em aplicações de software, a saída precisa ser previsível e legível por máquina.
*   **Saídas Estruturadas:** É a prática de forçar o LLM a gerar respostas em um formato de dados específico e bem definido, como JSON. Isso é fundamental para a interoperabilidade entre o LLM e outros componentes de um sistema.
*   **JSON Schema:** A abordagem mais robusta para garantir saídas em JSON é especificar um **JSON Schema** no prompt. Isso define a estrutura exata, os tipos de dados, os campos obrigatórios e as restrições que a saída JSON deve seguir.
*   **Function Calling / Tool Calling:** É um avanço das saídas estruturadas, onde o LLM não apenas gera um JSON, mas o JSON gerado corresponde à assinatura de uma função ou ferramenta que o sistema pode executar. O modelo entende a semântica da função e gera os argumentos corretos para a chamada.
*   **Como Funciona o Function Calling:** 1) Você define as ferramentas (funções) disponíveis e as fornece na chamada da API. 2) O LLM, com base na query do usuário, decide se uma ferramenta deve ser chamada. 3) Se sim, ele gera um objeto JSON contendo o nome da função e os argumentos. 4) Seu código executa a função com os argumentos fornecidos e 5) Opcionalmente, envia o resultado de volta ao LLM para que ele possa gerar uma resposta final em linguagem natural.
*   **Vantagens:** Permite que LLMs interajam com o mundo exterior, acessem informações em tempo real (via APIs), consultem bancos de dados e executem ações, transformando-os em um "cérebro" de orquestração para sistemas complexos.
*   **Avaliação Automática:** Saídas estruturadas são a base para a avaliação automática de LLMs. Ao forçar a saída em um formato previsível, é possível escrever testes unitários e de integração para validar a precisão, a conformidade e a qualidade das respostas do modelo em escala, sem a necessidade de avaliação humana manual para cada resposta.
*   **LLM-as-Judge:** Uma técnica de avaliação onde um segundo LLM é usado para julgar a qualidade da saída do primeiro, comparando a saída estruturada gerada com uma resposta de referência (ground truth).

## Para que serve / Resultados esperados

O controle sobre a estrutura da saída é o que transforma um LLM de um brinquedo de conversação em uma ferramenta de engenharia de software confiável. Serve para construir pontes entre a natureza probabilística da linguagem e a natureza determinística do código.

Os resultados esperados são:

*   **Confiabilidade:** Garantia de que a saída do LLM será sempre legível por máquina e seguirá um formato esperado, eliminando a necessidade de parsing frágil baseado em regex ou heurísticas.
*   **Integração com Sistemas:** Capacidade de integrar LLMs de forma nativa em qualquer aplicação de software, tratando a saída do LLM como qualquer outra resposta de API.
*   **Agentes Autônomos:** É o mecanismo fundamental que permite a criação de agentes de IA que podem raciocinar (ReAct) e interagir com ferramentas para realizar tarefas complexas.
*   **Fluxos de Trabalho Automatizados:** Construção de pipelines onde a saída de um LLM pode acionar diretamente outras APIs ou processos de negócio.
*   **Avaliação em Escala:** Possibilidade de avaliar e monitorar o desempenho de aplicações de LLM de forma contínua e automatizada, garantindo a qualidade e detectando regressões.

## Boas práticas

| Prática | Descrição | Exemplo de Aplicação |
| :--- | :--- | :--- |
| **Use JSON Schema** | Em vez de apenas dizer "responda em JSON", forneça um JSON Schema detalhado. Isso dá ao modelo um guia explícito sobre a estrutura, tipos e restrições. | Inclua um bloco `"$schema": "http://json-schema.org/draft-07/schema#"` e defina `properties`, `type` e `required` para cada campo. |
| **Instruções Claras no Prompt** | Mesmo com o schema, reforce no prompt a instrução para aderir estritamente ao formato. | `"Sua resposta DEVE ser um objeto JSON válido que se conforma com o schema fornecido. Não adicione nenhum texto antes ou depois do JSON." ` |
| **Descrições Semânticas (Function Calling)** | Ao definir uma função, use nomes e descrições que comuniquem claramente o que a função faz. O LLM usa essa semântica para decidir quando chamá-la. | Para uma função que busca o clima: `name: "get_weather"`, `description: "Obtém o clima atual para uma localização específica."`. |
| **Validação e Tratamento de Erros** | Sempre valide a saída do LLM contra o schema ou a assinatura da função no seu código. Esteja preparado para lidar com saídas malformadas ou chamadas de função incorretas. | Use uma biblioteca de validação de JSON. Se a validação falhar, retorne um erro ao LLM e peça para ele corrigir sua saída anterior. |
| **Mantenha as Ferramentas Simples** | Projete ferramentas que sejam atômicas e façam uma única coisa bem. É melhor ter várias ferramentas simples do que uma ferramenta monolítica com muitos modos de operação. | Em vez de uma função `manage_user(action, id, data)`, crie `create_user(data)`, `get_user(id)`, `update_user(id, data)`. |
| **Use a Saída da Ferramenta** | Após executar uma ferramenta, passe o resultado de volta para o LLM. Isso permite que ele use a informação para gerar uma resposta final coerente ou decidir o próximo passo. | Se a `Ação` foi `calculator(2+2)`, a `Observação` de volta para o LLM deve ser `4`. |
| **Avaliação Baseada em Schema** | Para avaliação automática, compare o JSON gerado com um JSON de referência (ground truth). A avaliação pode verificar a presença de chaves, a correção dos tipos e a precisão dos valores. | Use um framework de teste para fazer um "deep equal" entre o objeto JSON gerado e o esperado. |

## Como fazer (passo a passo)

**Implementando Function Calling (Exemplo com a API da OpenAI):**

1.  **Definir sua Função em Código:** Crie a função Python que você quer que o LLM seja capaz de chamar.
    ```python
    import json

    def get_user_details(user_id: int):
        """Obtém detalhes de um usuário a partir de seu ID."""
        if user_id == 1:
            return json.dumps({"name": "Alice", "email": "alice@example.com"})
        return json.dumps({"error": "User not found"})
    ```

2.  **Criar a Definição da Ferramenta (JSON Schema):** Descreva a função em um formato que a API do LLM entenda.
    ```json
    tools = [
        {
            "type": "function",
            "function": {
                "name": "get_user_details",
                "description": "Obtém detalhes de um usuário a partir de seu ID.",
                "parameters": {
                    "type": "object",
                    "properties": {
                        "user_id": {
                            "type": "integer",
                            "description": "O ID do usuário."
                        }
                    },
                    "required": ["user_id"]
                }
            }
        }
    ]
    ```

3.  **Chamar a API do LLM com as Ferramentas:** Envie a query do usuário e a definição das ferramentas para o LLM.
    ```python
    from openai import OpenAI
    client = OpenAI()

    messages = [{"role": "user", "content": "Pode me dar os detalhes do usuário com ID 1?"}]
    response = client.chat.completions.create(
        model="gpt-4.1-mini",
        messages=messages,
        tools=tools,
        tool_choice="auto"
    )
    ```

4.  **Analisar a Resposta e Executar a Função:** Verifique se o LLM decidiu chamar uma função. Se sim, execute-a.
    ```python
    response_message = response.choices[0].message
    tool_calls = response_message.tool_calls

    if tool_calls:
        # O LLM quer chamar uma função
        function_name = tool_calls[0].function.name
        function_args = json.loads(tool_calls[0].function.arguments)

        if function_name == "get_user_details":
            function_response = get_user_details(user_id=function_args.get("user_id"))
    ```

5.  **Enviar o Resultado de Volta ao LLM:** Adicione a resposta da função ao histórico da conversa e chame o LLM novamente para que ele possa formular uma resposta final em linguagem natural.
    ```python
    messages.append(response_message) # Adiciona a resposta do assistente (com a chamada de ferramenta)
    messages.append(
        {
            "tool_call_id": tool_calls[0].id,
            "role": "tool",
            "name": function_name,
            "content": function_response,
        }
    )
    
    final_response = client.chat.completions.create(
        model="gpt-4.1-mini",
        messages=messages
    )
    print(final_response.choices[0].message.content)
    # Saída esperada: "Claro, os detalhes do usuário com ID 1 são: Nome: Alice, Email: alice@example.com."
    ```

## Pontos de atenção / vieses / riscos (com mitigação)

| Risco/Vieses | Descrição | Mitigação |
| :--- | :--- | :--- |
| **Chamadas de Função Incorretas** | O LLM pode alucinar argumentos, chamar a função errada ou chamá-la quando não deveria. | **1. Validação Estrita:** Sempre valide os argumentos recebidos antes de executar a função. Nunca confie cegamente na entrada do LLM. **2. Descrições Claras:** Quanto mais clara a descrição da função e de seus parâmetros, menor a chance de o LLM errar. **3. Few-Shot Prompting:** Forneça exemplos de conversas que levaram (ou não) a uma chamada de função. |
| **Segurança (Injeção de Código)** | Se a saída de uma função for usada para executar comandos no sistema (ex: `os.system`), um atacante pode criar um prompt para injetar comandos maliciosos através dos argumentos da função. | **1. NUNCA Execute Comandos Diretos:** As ferramentas devem ser seguras e isoladas. Elas devem interagir com APIs bem definidas, não com o shell do sistema. **2. Princípio do Menor Privilégio:** A aplicação que executa as funções deve ter as permissões mínimas necessárias. **3. Sanitize Todas as Entradas:** Trate os argumentos gerados pelo LLM como qualquer outra entrada de usuário não confiável. |
| **Parsing Frágil de JSON** | Antes do suporte nativo a JSON, era comum pedir ao LLM para gerar JSON e depois fazer o parsing do texto. Isso é frágil e pode quebrar se o modelo adicionar texto extra. | **1. Use Modos Nativos:** Sempre que possível, use os modos de saída JSON ou de function calling nativos da API do LLM (ex: `response_format={"type": "json_object"}` na API da OpenAI). Eles garantem que a saída será um JSON válido. |
| **Complexidade da Lógica de Ferramentas** | Gerenciar múltiplas chamadas de ferramentas sequenciais ou paralelas pode se tornar complexo. | **1. Frameworks de Agentes:** Utilize frameworks como LangChain, LlamaIndex ou o Assistants API da OpenAI, que abstraem grande parte da complexidade de gerenciar o estado, o histórico e a lógica de execução de ferramentas. |

## Exemplos curtos

**Exemplo 1: Extração de Dados com JSON Schema**

*Prompt:* `Extraia as informações do texto a seguir e formate-as de acordo com o JSON Schema fornecido. Texto: "João Silva, email joao.silva@email.com, tem 30 anos." JSON Schema: {"type": "object", "properties": {"nome": {"type": "string"}, "email": {"type": "string"}, "idade": {"type": "integer"}}}`

*Saída Esperada:* `{"nome": "João Silva", "email": "joao.silva@email.com", "idade": 30}`

**Exemplo 2: Function Calling para uma Calculadora**

*Query do Usuário:* `Quanto é 125 vezes 4?`

*Decisão do LLM:* Chamar a função `calculator`.

*Saída Estruturada (Chamada de Ferramenta):* `{"name": "calculator", "arguments": {"expression": "125 * 4"}}`

*Resultado da Ferramenta:* `500`

*Resposta Final do LLM:* `125 vezes 4 é igual a 500.`

## Checklist de qualidade

- [ ] **Necessidade de Estrutura:** A tarefa se beneficia de uma saída estruturada ou o texto livre é suficiente?
- [ ] **Uso de Modos Nativos:** O modo JSON ou de function calling nativo da API está sendo utilizado para garantir a validade da saída?
- [ ] **Schema Bem Definido:** O JSON Schema ou a definição da função é claro, completo e com descrições semânticas?
- [ ] **Validação de Saída:** Existe um passo de validação no código para garantir que a saída do LLM está correta antes de ser usada?
- [ ] **Segurança das Ferramentas:** As ferramentas são seguras e não expõem o sistema a riscos de injeção de código ou outros ataques?
- [ ] **Tratamento de Erros:** O sistema consegue lidar graciosamente com saídas malformadas ou chamadas de função com falha?
- [ ] **Atomicidade das Ferramentas:** As ferramentas são focadas em uma única tarefa?
- [ ] **Feedback para o LLM:** O resultado da execução da ferramenta é retornado ao LLM para que ele possa continuar a conversa?
- [ ] **Base para Avaliação:** A saída estruturada está sendo usada para permitir a avaliação automática do sistema?
- [ ] **Abstração (se necessário):** Para lógicas complexas, um framework de agentes está sendo considerado para gerenciar a complexidade?

## Notas e Fontes

1.  **OpenAI API Documentation - Function Calling.** Disponível em: [https://platform.openai.com/docs/guides/function-calling](https://platform.openai.com/docs/guides/function-calling). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 10/10)
2.  **JSON Schema Documentation.** Disponível em: [https://json-schema.org/](https://json-schema.org/). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 9/10)
3.  **LangChain - Tools.** Disponível em: [https://python.langchain.com/docs/modules/tools/](https://python.langchain.com/docs/modules/tools/). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 9/10)
4.  **Vellum - When should I use function calling, structured outputs or JSON mode.** Disponível em: [https://www.vellum.ai/blog/when-should-i-use-function-calling-structured-outputs-or-json-mode](https://www.vellum.ai/blog/when-should-i-use-function-calling-structured-outputs-or-json-mode). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 8/10)
5.  **Agenta - The guide to structured outputs and function calling with LLMs.** Disponível em: [https://agenta.ai/blog/the-guide-to-structured-outputs-and-function-calling-with-llms](https://agenta.ai/blog/the-guide-to-structured-outputs-and-function-calling-with-llms). Acessado em: 04 de outubro de 2025. (Nota de confiabilidade: 8/10)

