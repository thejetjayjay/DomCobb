_# Melhores Práticas de Prompting para Lovable.dev

## 1. Resumo Executivo

- **Foco em Desenvolvimento Full-Stack:** Lovable.dev é uma plataforma de IA projetada para construir aplicações web full-stack a partir de prompts. A engenharia de prompts deve ser abordada como se estivesse instruindo um engenheiro de software, não um assistente de texto.
- **Especificidade é Fundamental:** O modelo de IA do Lovable depende de instruções explícitas. Prompts devem detalhar o tech stack (React, Supabase, etc.), as funcionalidades principais e secundárias, e os requisitos de design para evitar resultados genéricos.
- **Estrutura de Prompt Hierárquica:** Lovable promove uma abordagem estruturada para prompts, usando níveis de cabeçalho (Contexto, Tarefa, Diretrizes, Restrições) para organizar as instruções e guiar a IA de forma eficaz.
- **Metaprompting e Reverse Metaprompting:** A plataforma incentiva o uso da própria IA para refinar e criar prompts (Metaprompting) e para documentar sessões de debugging, criando um ciclo de melhoria contínua (Reverse Metaprompting).
- **Controle de Modificações (Diff & Select):** Como a IA pode reescrever arquivos inteiros, é crucial usar prompts que instruam o modelo a fazer alterações precisas e limitadas, mencionando explicitamente quais arquivos ou funcionalidades não devem ser alterados.
- **Construção Incremental:** A prática recomendada é começar com um projeto em branco e construir a aplicação de forma incremental, em vez de tentar gerar tudo de uma vez com um único prompt massivo. Isso permite que a IA aprenda o contexto do projeto passo a passo.

## 2. Onde Brilha / Onde Evitar

| Onde Brilha (Casos de Uso Ideais) | Onde Evitar (Limites e Armadilhas) |
| :--- | :--- |
| **Prototipagem Rápida de Aplicações:** Ideal para transformar rapidamente uma ideia em um protótipo funcional de uma aplicação web full-stack, incluindo frontend, backend e banco de dados. | **Projetos sem Clareza de Tech Stack:** Se você não tem certeza de qual tecnologia usar, o Lovable pode fazer escolhas genéricas. É melhor definir o stack (ex: React, Next.js, Supabase) no prompt. |
| **Desenvolvimento Iterativo:** A plataforma é otimizada para um fluxo de trabalho onde você começa com uma base e adiciona funcionalidades de forma incremental, refinando a aplicação a cada passo. | **Grandes Alterações em um Único Prompt:** Tentar refatorar ou adicionar múltiplas funcionalidades complexas de uma só vez pode confundir a IA e levar a erros em cascata. Prefira alterações menores e focadas. |
| **Desenvolvedores e Não-Técnicos:** Com a abordagem de prompting estruturado, tanto desenvolvedores (que podem especificar detalhes técnicos) quanto usuários de negócios (que podem focar nas funcionalidades) conseguem construir aplicações. | **Falta de Controle de Versão:** A plataforma em si não substitui o Git. Para projetos sérios, é crucial ter um sistema de controle de versão externo para gerenciar as alterações geradas pela IA. |
| **Aplicações com Integrações:** Lovable pode ser estendido através de ferramentas de automação como make.com e n8n, permitindo a criação de aplicações que se conectam a outras APIs e serviços. | **Modificações "Cirúrgicas" sem Instruções Claras:** Sem um prompt cuidadoso usando as técnicas de "Diff & Select" ou "Lock Files", a IA pode acidentalmente reescrever ou quebrar partes não relacionadas da aplicação. |



## 3. Técnicas de Prompting (Model-Specific)

As técnicas de prompting no Lovable.dev são projetadas para espelhar a comunicação com um engenheiro de software. A clareza e a estrutura são mais importantes do que a conversação fluida.

### A Estrutura de 4 Níveis ("Training Wheels")

Lovable responde melhor a uma abordagem hierárquica e estruturada. O uso de Markdown para delinear seções do prompt é uma prática recomendada e eficaz.

**Formato Padrão:**

```markdown
# Contexto
(Opcional: Forneça o contexto geral do projeto ou da tarefa)

## Tarefa
(Obrigatório: Descreva a tarefa específica a ser executada de forma clara e direta)

### Diretrizes
(Opcional: Forneça regras, padrões de estilo, ou exemplos de como a tarefa deve ser executada)

#### Restrições
(Opcional: Especifique o que a IA deve evitar, como tecnologias a não usar ou arquivos a não modificar)
```

**Exemplo Prático:**

```markdown
# Contexto
Estou construindo um painel de administração para um e-commerce.

## Tarefa
Crie um novo componente React para exibir uma lista de produtos.

### Diretrizes
- O componente deve ser nomeado `ProductList.tsx`.
- Use a biblioteca `Tailwind CSS` para estilização.
- A lista deve ser uma tabela com as colunas: 'ID do Produto', 'Nome', 'Preço' e 'Estoque'.
- Obtenha os dados de um endpoint de API fictício em `/api/products`.

#### Restrições
- Não use bibliotecas de componentes de UI de terceiros, como Material-UI ou Ant Design.
- Não modifique o arquivo de layout principal `App.tsx`.
```

### O Mindset da IA: Seja Explícito

A IA do Lovable não "entende" o contexto implícito. Ela prevê o próximo passo com base nos padrões que aprendeu. Portanto, a ambiguidade é o inimigo.

- **Seja Específico sobre a Tecnologia:** Em vez de "Adicione autenticação", diga "Implemente autenticação de e-mail e senha usando Supabase Auth".
- **Priorize o Início e o Fim:** A IA tende a dar mais peso às instruções no início e no final do prompt. Coloque os detalhes mais críticos, como a tarefa principal, no início, e as restrições importantes no final.

### Controle de Modificações: "Diff & Select" e "Lock Files"

Este é talvez o aspecto mais crucial do prompting no Lovable para evitar que a IA cause danos não intencionais ao reescrever código.

- **Prompt de Modificação Segura ("Diff & Select"):** Ao pedir uma alteração em um recurso existente, instrua a IA a ser cirúrgica.
  > **Prompt:** "Implemente modificações na funcionalidade de carrinho de compras para adicionar um campo de cupom de desconto. **Assegure que a funcionalidade principal de adicionar/remover produtos, outros recursos e processos permaneçam inalterados.** Avalie o comportamento e as dependências para identificar riscos potenciais e discuta quaisquer preocupações antes de prosseguir. Realize testes completos para verificar que não há regressões."

- **Simulando "Lock Files":** Como não há um sistema de bloqueio de arquivos, você deve criá-lo no prompt. A cada solicitação, lembre a IA do que ela *não* deve tocar.
  > **Prompt:** "Refatore o componente de login para usar hooks do React. **Por favor, não altere as páginas `Dashboard.tsx` ou `Settings.tsx`. Concentre as alterações exclusivamente no arquivo `Login.tsx`.**"


_## 4. Metaprompting: Usando a IA para Melhorar a Si Mesma

Lovable incentiva ativamente o uso de metaprompting para refinar a comunicação com a IA. Isso transforma a engenharia de prompts de uma arte em uma ciência, usando a própria ferramenta para otimizar seus inputs.

### Metaprompting Padrão

Use a IA para refinar um prompt que você já escreveu. Isso é útil quando você tem uma ideia, mas não tem certeza de como expressá-la da forma mais eficaz para o Lovable.

**Exemplo de Metaprompt:**

> "Você é um engenheiro de prompts especialista na plataforma Lovable.dev. Reescreva o seguinte prompt para ser mais conciso, detalhado e estruturado, seguindo as melhores práticas do Lovable (estrutura de 4 níveis, especificidade de tecnologia, etc.).
> 
> **Prompt Original:** 'Eu quero uma página de perfil de usuário. Ela deve mostrar o nome, a foto e a biografia do usuário. Também deve ter um botão para editar o perfil.'
> 
> Gere um novo prompt otimizado que resultará em um componente React (`UserProfile.tsx`) usando `Tailwind CSS` e que obtenha dados de um hook `useUser()`."

### Reverse Metaprompting

Esta é uma técnica poderosa para aprender com os erros. Após uma sessão de debugging onde você e a IA corrigiram um problema, peça à IA para documentar o processo e criar um prompt que evite o mesmo erro no futuro.

**Exemplo de Reverse Metaprompt:**

> "Acabamos de corrigir um bug onde o estado do formulário de login não estava sendo atualizado corretamente devido ao uso incorreto do `useState` no React.
> 
> **Sua tarefa:**
> 1.  Resuma os erros que encontramos durante a configuração do estado do formulário e como eles foram resolvidos.
> 2.  Crie um prompt detalhado e otimizado para o Lovable que eu possa usar no futuro para criar um formulário de login em React, garantindo que ele inclua o gerenciamento de estado correto, a validação de campos e o tratamento de submissão, evitando os erros que acabamos de corrigir."

## 5. Tool/Function Calling & Saídas Estruturadas

Embora o Lovable não exponha um `tool calling` explícito como as APIs da OpenAI, sua funcionalidade principal é, em essência, uma forma de `tool calling` focada no desenvolvimento de software. Os "tools" são as capacidades da IA de criar, modificar e deletar arquivos, estruturas de diretórios e configurações.

### Estruturando a Saída (O Projeto)

O "formato de saída" no Lovable é a própria estrutura do seu projeto de software. Você controla essa saída através de prompts que especificam a arquitetura.

**Prompt para Estruturar um Novo Projeto:**

> "Preciso de uma nova aplicação web de blog com as seguintes especificações:
> 
> - **Frontend:** Next.js 14 com App Router.
> - **Estilização:** Tailwind CSS.
> - **Autorização:** NextAuth.js com provedor de e-mail/senha.
> - **Banco de Dados:** Supabase para armazenar posts e usuários.
> 
> **Funcionalidades Principais:**
> 1.  Criação, leitura, atualização e exclusão (CRUD) de posts.
> 2.  Autenticação de usuários.
> 3.  Página de listagem de posts e página de detalhes do post.
> 
> **Comece criando a estrutura de diretórios inicial e configure a conexão com o Supabase.**"

### Integrações com Ferramentas Externas (make.com, n8n)

O verdadeiro poder de `tool calling` no Lovable vem de sua capacidade de se integrar com plataformas de automação. Você pode instruir o Lovable a criar uma aplicação que interage com webhooks, que por sua vez podem acionar fluxos de trabalho no make.com ou n8n para se conectar a qualquer outra API.

**Exemplo de Prompt para Integração:**

> "Crie um formulário de contato na minha aplicação. O formulário deve ter os campos 'Nome', 'Email' e 'Mensagem'.
> 
> **Ao submeter o formulário, em vez de salvar no banco de dados, envie os dados via uma requisição `POST` para um webhook em `https://hook.n8n.io/your-webhook-url`."
> 
> *(Isso permite que você use o n8n para pegar os dados do formulário e enviá-los para o Slack, Trello, um CRM, ou qualquer outro serviço.)*



## 6. Boas Práticas por Tarefa (Exemplos)

A seguir, exemplos práticos de prompts "antes e depois" que demonstram as melhores práticas para o Lovable.dev.

### Exemplo 1: Iniciar um Novo Projeto

- **Antes (Prompt Vago):**
  > "Crie um site de e-commerce."
  > *(Este prompt é extremamente vago e levará a uma aplicação genérica com um tech stack imprevisível.)*

- **Depois (Prompt Otimizado para Lovable):**
  > "Preciso de uma nova aplicação de e-commerce com as seguintes especificações:
  > 
  > - **Frontend:** Next.js 14 com App Router.
  > - **Estilização:** Tailwind CSS e ShadCN para componentes.
  > - **Autorização:** NextAuth.js com provedores do Google e de e-mail/senha.
  > - **Banco de Dados:** Supabase para gerenciar usuários, produtos e pedidos.
  > 
  > **Funcionalidades Principais:**
  > 1.  Página de produtos com filtro por categoria.
  > 2.  Carrinho de compras.
  > 3.  Checkout com Stripe.
  > 
  > **Comece configurando a página inicial (`/`) com um header, um footer e uma seção de produtos em destaque. Não implemente a lógica de dados ainda, use dados mockados.**"
  > *(**Por que funciona:** Define o tech stack completo, as funcionalidades principais e um ponto de partida claro e incremental, permitindo que a IA construa uma base sólida.)*

### Exemplo 2: Adicionar uma Nova Funcionalidade

- **Antes (Prompt Arriscado):**
  > "Adicione um sistema de comentários aos posts do blog."
  > *(Este prompt pode fazer com que a IA modifique múltiplos arquivos de forma inesperada, potencialmente quebrando a funcionalidade existente.)*

- **Depois (Prompt Otimizado para Lovable):**
  > "Adicione uma seção de comentários abaixo do conteúdo de cada post do blog.
  > 
  > **Diretrizes:**
  > - Crie um novo componente `CommentSection.tsx`.
  > - O componente deve incluir um formulário para adicionar um novo comentário e uma lista de comentários existentes.
  > - Use a API do Supabase para buscar e inserir comentários na tabela `comments`.
  > 
  > **Restrições:**
  > - **Concentre todas as alterações nos arquivos `[slug]/page.tsx` (para renderizar o novo componente) e no novo arquivo `CommentSection.tsx`.**
  > - **Não modifique o layout principal ou a navegação.**"
  > *(**Por que funciona:** Isola a alteração em um novo componente e usa uma restrição explícita ("Lock File") para proteger o resto da aplicação, promovendo uma modificação segura e previsível.)*

### Exemplo 3: Fazer Alterações de Design

- **Antes (Prompt Ambíguo):**
  > "Deixe o site mais bonito."
  > *(Isto é subjetivo e pode resultar em alterações de design que quebram a lógica da aplicação.)*

- **Depois (Prompt Otimizado para Lovable):**
  > "Faça apenas melhorias visuais no componente `Header.tsx`. **Assegure que a funcionalidade, a lógica e o estado permaneçam inalterados.**
  > 
  > **Alterações Desejadas:**
  > 1.  Altere a cor de fundo para um azul escuro (`#1A202C`).
  > 2.  Aumente o tamanho da fonte dos links de navegação.
  > 3.  Adicione um efeito de `hover` sutil nos links.
  > 
  > **Teste extensivamente para verificar que a aplicação opera exatamente como antes.** Pare se houver qualquer incerteza sobre consequências não intencionais."
  > *(**Por que funciona:** Usa o prompt de "UI Changes" para focar estritamente no design, proibindo alterações na lógica e forçando a IA a ser cautelosa e a testar seu trabalho.)*

### Exemplo 4: Otimização para Dispositivos Móveis

- **Antes (Prompt Genérico):**
  > "Faça o site funcionar em celulares."
  > *(Pode levar a IA a aplicar breakpoints de forma inconsistente ou a fazer alterações desnecessárias.)*

- **Depois (Prompt Otimizado para Lovable):**
  > "Torne a página de listagem de produtos (`/products`) totalmente responsiva, adotando uma estratégia mobile-first.
  > 
  > **Diretrizes:**
  > - Em telas pequenas (abaixo de 640px), os produtos devem ser exibidos em uma única coluna (grid de 1 coluna).
  > - Em telas médias (640px e acima), os produtos devem ser exibidos em uma grade de 2 colunas.
  > - Em telas grandes (1024px e acima), a grade deve ser de 3 colunas.
  > - Use os breakpoints padrão do Tailwind CSS. **Não crie breakpoints personalizados.**
  > 
  > **Preserve o design e a funcionalidade existentes em desktops.**"
  > *(**Por que funciona:** Fornece regras de design responsivo claras e explícitas para diferentes breakpoints, guiando a IA a implementar uma solução consistente e previsível sem adivinhação.)*


_## 7. Avaliação & Métricas

A avaliação no Lovable.dev é menos sobre a "correção" de uma resposta de texto e mais sobre a "funcionalidade" do código gerado. A métrica final é: "A aplicação ou a funcionalidade funciona como esperado?"

- **Funcionalidade (Passa/Falha):** Este é o teste mais importante. O código gerado compila? A funcionalidade executa a tarefa descrita no prompt sem erros? A avaliação é binária: funciona ou não.

- **Aderência aos Requisitos:** A IA seguiu todas as diretrizes e restrições do prompt? Se você pediu React com Supabase, foi isso que ela entregou? A avaliação aqui é um checklist baseado no seu prompt original.

- **Qualidade do Código:** Embora mais subjetivo, você pode avaliar a qualidade do código gerado. Ele segue as melhores práticas padrão para o framework solicitado? É legível e bem estruturado? Com o tempo, você pode usar metaprompts para instruir a IA a seguir padrões de codificação específicos.

- **Eficiência da Geração (Generation Efficiency):** Métricas para avaliar o processo:
    - **Número de Prompts:** Quantos prompts foram necessários para chegar ao resultado desejado? Um número menor indica um prompt inicial mais eficaz.
    - **Tempo de Iteração:** Quanto tempo foi gasto em ciclos de prompt-debug-prompt? O objetivo é reduzir esse tempo através de prompts mais claros e técnicas de metaprompting.

- **Testes de Regressão:** Após cada alteração significativa, a métrica mais crucial é a ausência de regressões. As funcionalidades existentes ainda funcionam perfeitamente? A avaliação é feita através de testes manuais ou, idealmente, automatizados (se você os tiver configurado).

- **Rubricas de Avaliação para Lovable:**

| Critério | Descrição da Avaliação |
| :--- | :--- |
| **Execução do Código** | O código gerado é executado sem erros de compilação ou de tempo de execução? (Passa/Falha) |
| **Aderência ao Tech Stack** | A IA usou o frontend, backend, banco de dados e bibliotecas especificados no prompt? (Sim/Não) |
| **Implementação da Funcionalidade** | A funcionalidade principal descrita na seção "Tarefa" do prompt foi implementada corretamente? (Sim/Não) |
| **Respeito às Restrições** | A IA evitou modificar os arquivos ou usar as tecnologias listadas na seção "Restrições"? (Sim/Não) |
| **Qualidade da UI/UX (Subjetivo)** | O design gerado é limpo, responsivo e intuitivo? (Escala de 1-5) |
_


## 8. Anti-padrões (o que evitar) e Alternativas

| Anti-padrão | Descrição do Problema | Alternativa / Solução |
| :--- | :--- | :--- |
| **O Prompt "Monolítico"** | Tentar construir uma aplicação inteira com um único prompt gigante que descreve todas as páginas, funcionalidades e detalhes de uma só vez. Isso sobrecarrega a IA, leva a resultados imprevisíveis e é impossível de depurar. | **Construção Incremental:** Comece com um prompt que define a estrutura básica e o tech stack. Em seguida, use prompts separados e focados para construir cada funcionalidade, uma de cada vez. Pense como um desenvolvedor: construa a base, depois os recursos. |
| **Ambiguidade de Tecnologia** | Prompts como "Use um bom banco de dados" ou "Adicione um estilo legal". A IA não tem opinião; ela tem padrões. A ambiguidade leva a escolhas genéricas que podem não ser as que você deseja. | **Seja um Arquiteto de Software:** Especifique *exatamente* qual tecnologia você quer. "Use o Supabase como banco de dados PostgreSQL", "Use Tailwind CSS com a paleta de cores do Material Design". Você está no comando das decisões de arquitetura. |
| **Confiança Cega nas Modificações** | Pedir uma alteração e assumir que a IA só modificará o que é estritamente necessário. A IA pode reescrever um arquivo inteiro para mudar uma única linha, introduzindo erros de regressão. | **Use os Prompts de "Diff & Select" e "Lock File":** Sempre que modificar algo, inclua restrições explícitas. "Altere apenas o arquivo X", "Não toque na funcionalidade Y". Force a IA a ser cirúrgica em suas alterações. |
| **Ignorar o Metaprompting** | Escrever prompts repetidamente do zero para tarefas semelhantes, ou lutar para obter o resultado certo através de tentativa e erro. | **Crie uma Biblioteca de Prompts:** Use o Metaprompting e o Reverse Metaprompting para construir sua própria biblioteca de prompts otimizados para as tarefas que você executa com frequência. Peça à IA para criar o prompt perfeito para "criar um novo serviço de API" e salve-o. |
| **Desenvolver sem Controle de Versão** | Depender apenas do histórico do Lovable para gerenciar as alterações. Se a IA cometer um erro grave, pode ser difícil reverter para um estado funcional. | **Integre com Git:** Trate o código gerado pelo Lovable como se fosse escrito por um desenvolvedor júnior. Após cada funcionalidade bem-sucedida, faça o commit das alterações em um repositório Git. Isso lhe dá uma rede de segurança e controle profissional sobre o projeto. |
| **Falta de Testes Manuais** | Assumir que, se o código foi gerado sem erros, a funcionalidade está perfeita. | **Seja o QA (Quality Assurance):** Após cada geração ou modificação, teste a funcionalidade manualmente. Clique em tudo, preencha os formulários, tente quebrar a aplicação. A IA é a desenvolvedora; você é o testador. |


_## 9. Checklist Final (Pronto para Uso)

- [ ] **Estrutura Hierárquica:** O seu prompt está estruturado com os 4 níveis (Contexto, Tarefa, Diretrizes, Restrições) para máxima clareza?
- [ ] **Especificidade do Tech Stack:** Você especificou claramente o frontend, backend, banco de dados e as bibliotecas a serem usadas?
- [ ] **Ponto de Partida Incremental:** Para novos projetos, o seu prompt define um ponto de partida pequeno e claro (ex: criar a página inicial com dados mockados) em vez de pedir a aplicação inteira?
- [ ] **Instruções de Modificação Segura:** Ao alterar código existente, o seu prompt inclui uma instrução para não afetar outras funcionalidades e para focar as alterações em arquivos específicos?
- [ ] **Restrições Explícitas ("Lock Files"):** O prompt menciona explicitamente quais arquivos ou funcionalidades *não* devem ser alterados?
- [ ] **Metaprompting para Refinamento:** Você considerou usar o Lovable para reescrever seu próprio prompt e torná-lo mais eficaz?
- [ ] **Reverse Metaprompting para Aprendizado:** Após uma sessão de debugging, você pediu ao Lovable para criar um prompt que evite o mesmo erro no futuro?
- [ ] **Integração com Ferramentas Externas:** Se a funcionalidade requer interação com outros serviços, você especificou o uso de webhooks para se conectar a plataformas como make.com ou n8n?
- [ ] **Teste Manual Pós-Geração:** Você testou manualmente a funcionalidade gerada para garantir que ela funciona como esperado e que não introduziu regressões?
- [ ] **Controle de Versão:** Você fez o commit da última alteração funcional em seu repositório Git?

## 10. Notas e Fontes

As informações neste documento são baseadas principalmente na documentação oficial e nos guias de melhores práticas fornecidos pelo Lovable.dev, complementadas por análises da comunidade de desenvolvedores.

1.  **The Lovable Prompting Bible.** A fonte primária e mais completa sobre a filosofia e as técnicas de prompting na plataforma. URL: `https://lovable.dev/blog/2025-01-16-lovable-prompting-handbook`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 10/10.
2.  **Lovable.dev Documentation: Prompting 1.1.** Guia oficial que cobre desde conceitos básicos até estratégias avançadas. URL: `https://docs.lovable.dev/prompting/prompting-one`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 10/10.
3.  **Lovable.dev Documentation: Best Practices.** Dicas e truques oficiais, reforçando a necessidade de prompts claros e verbosos. URL: `https://docs.lovable.dev/tips-tricks/best-practice`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 10/10.
4.  **Lovable.dev Documentation: Prompt Library.** Uma coleção de padrões de prompts reutilizáveis e exemplos para cenários comuns. URL: `https://docs.lovable.dev/prompting/prompting-library`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 9/10.
5.  **Community Discussion on Reddit (r/lovable).** Tópicos da comunidade discutindo dicas práticas, como o uso de outra IA para pré-gerar prompts para o Lovable. URL: `https://www.reddit.com/r/lovable/comments/1jxoiot/best_tips_when_using_lovable/`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 7/10.
6.  **RapiDevelopers Blog: The Lovable Prompting Bible Analysis.** Uma análise de terceiros sobre o guia de prompting do Lovable. URL: `https://www.rapidevelopers.com/blog/the-lovable-prompting-bible-complete-guide-to-ai-prompting-in-lovable-2025`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
7.  **Medium: From prompt to prototype: Get the most out of Lovable.** Artigo da comunidade focado em usar o Lovable para acelerar o processo de prototipagem. URL: `https://medium.com/design-bootcamp/from-prompt-to-prototype-get-the-most-out-of-lovable-3eedb5c8e756`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 7/10.
8.  **Lovable.dev: How to Build AI-powered Prompt Engineering Projects.** Guia sobre como usar a própria plataforma para criar aplicações que giram em torno da engenharia de prompts. URL: `https://lovable.dev/how-to/developer-tools/ai-powered-prompt-engineering`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 9/10.
_
