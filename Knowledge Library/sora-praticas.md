# Melhores Práticas de Prompting para OpenAI Sora

## 1. Resumo Executivo

- **Pense como um Cineasta, Não como um Escritor:** A filosofia de prompting para o Sora, o modelo de texto-para-vídeo da OpenAI, é fundamentalmente diferente dos LLMs de texto. O objetivo não é descrever uma história, mas sim escrever uma **lista de cenas (shot list)**. A linguagem do cinema (cinematografia, enquadramento, movimento de câmera) é a chave para o controle.
- **A Especificidade é Visual e Física:** Prompts eficazes para o Sora são densos em detalhes visuais e físicos. Descrever a iluminação, o tipo de lente, o movimento da câmera, a composição e até mesmo a física dos objetos (peso, força, impacto) resulta em vídeos mais realistas e controlados.
- **Estrutura de Prompt como Roteiro:** Em vez de um parágrafo único, a abordagem mais eficaz é estruturar o prompt como uma sequência de ações ou cenas, muitas vezes separadas por ponto e vírgula, imitando um roteiro ou uma shot list.
- **Áudio é Parte do Prompt:** O design de som não é um pensamento posterior. O prompt deve incluir descrições explícitas de diálogo, efeitos sonoros (SFX) e som ambiente para gerar um vídeo com áudio sincronizado e imersivo.
- **Iteração é Edição:** O processo de criação com o Sora é análogo à edição de um filme. A prática recomendada é gerar múltiplas variações de um prompt, comparar os resultados lado a lado e refinar iterativamente o prompt para se aproximar da visão desejada.
- **O Gancho Vem Primeiro:** Os primeiros segundos de um vídeo são cruciais. A parte mais importante e cativante do seu prompt deve vir no início para garantir que o Sora a priorize na geração.

## 2. Onde Brilha / Onde Evitar

| Onde Brilha (Casos de Uso Ideais) | Onde Evitar (Limites e Armadilhas) |
| :--- | :--- |
| **Cenas Cinematográficas Curtas e Coerentes:** Sora se destaca na criação de clipes curtos e de alta fidelidade que parecem ter sido filmados com equipamento profissional, desde que o prompt seja focado em uma única cena ou ação contínua. | **Múltiplas Cenas Desconexas em um Único Prompt:** Tentar gerar um vídeo com várias cenas completamente diferentes (ex: "um cavaleiro lutando contra um dragão, depois um astronauta no espaço") em um único prompt levará a resultados caóticos e incoerentes. |
| **Tradução de Linguagem Cinematográfica:** Prompts que usam terminologia de cinema (ex: `close-up`, `dolly shot`, `lente de 85mm`, `golden hour`) são interpretados com alta fidelidade, dando ao criador um controle de nível profissional. | **Prompts Vagos e Genéricos:** Prompts como "um vídeo de um cachorro feliz" resultarão em clipes genéricos. A falta de detalhes sobre a raça, o ambiente, a iluminação e a ação limita o potencial do modelo. |
| **Simulação de Física e Interações do Mundo Real:** Descrever o peso, o impacto e as forças em um prompt (ex: "uma bola de boliche pesada bate no chão com um baque surdo") ajuda o Sora a gerar interações físicas mais realistas. | **Ignorar as Restrições do Modelo:** Tentar gerar vídeos muito longos ou em proporções não suportadas levará a falhas ou resultados cortados. Os prompts devem ser projetados dentro das limitações conhecidas do Sora. |
| **Criação de Estilos Visuais Específicos:** Sora é capaz de imitar estilos visuais distintos, desde "filme antigo em preto e branco" até "animação 3D no estilo Pixar", se esses estilos forem descritos com detalhes no prompt. | **Controles Não Documentados:** Tentar usar parâmetros de linha de comando como `--ar 16:9` não funciona. O controle deve vir da linguagem descritiva dentro do próprio prompt (ex: "um vídeo cinematográfico em widescreen"). |



## 3. Técnicas de Prompting (Model-Specific)

O prompting para Sora é uma disciplina de escrita visual. As técnicas se concentram em traduzir uma visão cinematográfica em texto que o modelo possa interpretar.

### A Estrutura da "Shot List"

A técnica mais poderosa é abandonar o formato de parágrafo e estruturar seu prompt como uma lista de cenas ou uma sequência de ações, muitas vezes usando ponto e vírgula para separar os "cortes".

**Exemplo Prático:**

- **Antes (Formato de Parágrafo):**
  > "Um vídeo de uma mulher andando por uma rua de Tóquio à noite. Ela está usando um casaco de couro e a rua está molhada da chuva, refletindo as luzes de neon. Ela olha para a câmera."

- **Depois (Estrutura de Shot List):**
  > "Plano de estabelecimento de uma rua de Tóquio à noite, asfalto molhado refletindo letreiros de neon coloridos; plano médio de uma mulher com um casaco de couro preto andando em direção à câmera; close-up extremo em seus olhos enquanto ela olha diretamente para a lente."
  > *(**Por que funciona:** A estrutura de shot list dá ao Sora uma sequência clara de enquadramentos e ações, imitando o processo de direção e resultando em uma cena mais dinâmica e controlada.)*

### O Léxico da Cinematografia

Incorporar termos técnicos de cinema no seu prompt é a maneira mais direta de controlar a aparência do seu vídeo.

- **Tipos de Plano:** `establishing shot` (plano de estabelecimento), `medium shot` (plano médio), `close-up` (primeiro plano), `extreme close-up` (primeiríssimo plano).
- **Ângulos de Câmera:** `low-angle shot` (contra-plongée), `high-angle shot` (plongée), `eye-level shot` (plano normal), `dutch angle` (ângulo holandês).
- **Movimentos de Câmera:** `dolly in/out` (carrinho para frente/trás), `crane up/down` (grua para cima/baixo), `handheld` (câmera na mão), `steadicam shot`, `slow push-in` (aproximação lenta).
- **Lentes e Foco:** `24mm wide-angle lens` (lente grande angular), `85mm portrait lens` (lente de retrato), `shallow depth of field` (baixa profundidade de campo), `rack focus` (mudança de foco).
- **Iluminação:** `golden hour` (hora dourada), `blue hour` (hora azul), `harsh midday sun` (sol forte do meio-dia), `cinematic lighting`.

**Exemplo com Cinematografia:**

> "Um close-up de um velho relojoeiro trabalhando em uma mesa de madeira, iluminado por uma única lâmpada quente; a câmera faz um `dolly out` lento para revelar a oficina bagunçada ao seu redor; `shallow depth of field` com foco no relógio delicado."

### Codificando a Física e os Detalhes Sensoriais

Sora tem um entendimento do mundo físico. Descrever as interações físicas e os detalhes sensoriais torna o vídeo mais crível.

- **Peso e Massa:** `um elefante pesado`, `uma pena leve flutuando`.
- **Força e Impacto:** `a onda quebra violentamente contra as rochas`, `uma bola de neve macia se desfaz no impacto`.
- **Textura e Material:** `superfície de madeira áspera`, `tecido de seda liso`, `metal enferrujado e corroído`.
- **Emoção e Expressão:** `um sorriso sutil de canto de boca`, `olhos arregalados de surpresa`.

### Design de Som Integrado

O áudio deve ser parte integrante do prompt, não um pensamento posterior.

- **Diálogo:** `Diálogo: "Acho que não estamos mais no Kansas."`
- **Efeitos Sonoros (SFX):** `SFX: trovão distante, rangido de uma porta velha.`
- **Ambiente:** `Ambiente: som de uma floresta movimentada com pássaros cantando, som de uma cidade movimentada com sirenes.`

**Exemplo com Áudio:**

> "Plano médio de um detetive em um beco escuro e chuvoso; `SFX: som constante da chuva caindo no asfalto, sirene de polícia distante`; ele acende um isqueiro, o som do clique ecoa no beco."


## 4. Metaprompting: Usando um Copiloto de Prompt

Dado que o prompting para Sora é altamente especializado, usar um LLM de texto (como ChatGPT ou o próprio Claude) como um "Copiloto de Prompt" é uma estratégia de metaprompting extremamente eficaz. A ideia é traduzir uma ideia conceitual em uma shot list detalhada que o Sora possa entender.

### O Metaprompt "Diretor de Fotografia"

Você pode instruir um LLM a atuar como um diretor de fotografia e converter sua ideia em um prompt para o Sora.

**Exemplo de Metaprompt para um Copiloto:**

> "Você é um Diretor de Fotografia especialista em criar prompts para o modelo de IA de texto-para-vídeo, Sora. Sua tarefa é pegar a ideia do usuário e traduzi-la em um prompt detalhado e estruturado como uma shot list, usando linguagem cinematográfica.
> 
> **Regras:**
> 1.  Seja específico sobre o tipo de plano, ângulo, lente e movimento da câmera.
> 2.  Descreva a iluminação, o ambiente e o estilo visual.
> 3.  Inclua detalhes sobre o design de som (diálogo, SFX, ambiente).
> 4.  Mantenha o prompt focado em uma cena coerente.
> 
> **Ideia do Usuário:**
> "Eu quero um vídeo de ficção científica sobre um robô solitário em um planeta deserto."
> 
> Agora, gere o prompt otimizado para o Sora."

### Reverse Metaprompting para Análise de Estilo

Se você gerou um vídeo no Sora de que gostou, mas não sabe por que funcionou, pode usar um modelo de visão (como o GPT-4o ou o Claude 3.5 Sonnet) para analisar o vídeo e gerar um prompt que recrie esse estilo.

**Exemplo de Reverse Metaprompt (com um modelo de visão):**

> **[Upload de um clipe de vídeo gerado pelo Sora]**
> 
> **Prompt:** "Analise este clipe de vídeo. Atue como um especialista em engenharia de prompt para o Sora e gere um prompt de texto que descreva este vídeo em detalhes cinematográficos. Descreva o enquadramento, o movimento da câmera, a iluminação, a paleta de cores e o estilo geral para que eu possa usar este prompt para criar outros vídeos com uma estética semelhante."

## 5. Tool/Function Calling & Saídas Estruturadas

Atualmente, o Sora não possui uma API pública com `tool calling` no mesmo sentido que os LLMs de texto. A "saída estruturada" do Sora é o próprio vídeo. No entanto, o conceito de estruturação se aplica inteiramente ao **prompt de entrada**.

### A Estrutura de Prompt como uma "API Call"

Pense no seu prompt como a carga útil (payload) de uma chamada de API, onde cada cláusula é um "parâmetro" que você está passando para o modelo.

**Estrutura de Prompt Parametrizada:**

```
// Cena 1
Plano: [Tipo de plano], Câmera: [Movimento], Lente: [Tipo de lente]
Sujeito: [Descrição do sujeito]
Ação: [Descrição da ação]
Iluminação: [Descrição da iluminação]
Áudio: [Descrição do áudio]

// Cena 2
...
```

**Exemplo Preenchido:**

> "Plano: `establishing shot`, Câmera: `crane down`, Lente: `24mm wide`
> Sujeito: `Uma cidade futurista com carros voadores`
> Ação: `A câmera desce lentamente do céu, revelando o tráfego movimentado abaixo`
> Iluminação: `Pôr do sol, com tons de laranja e roxo`
> Áudio: `Ambiente de cidade futurista, zumbido de motores de carros voadores`"

### Geração de Variações (Iteração)

O processo de refinar um vídeo no Sora envolve a geração de múltiplas variações. Isso pode ser pensado como um loop de `tool calling` manual.

1.  **Gere a Cena Base:** Use seu prompt inicial para gerar o primeiro vídeo.
2.  **Analise e Identifique o Delta:** Assista ao vídeo e identifique o que você quer mudar (ex: "a câmera se move muito rápido").
3.  **Modifique o "Parâmetro" no Prompt:** Altere a parte correspondente do seu prompt (ex: mude `dolly in` para `slow dolly in`).
4.  **Re-gere e Compare:** Gere o novo vídeo e compare-o com a versão anterior. Repita até ficar satisfeito.



## 6. Boas Práticas por Tarefa (Exemplos)

A seguir, exemplos práticos de prompts "antes e depois" que demonstram as melhores práticas para o Sora.

### Exemplo 1: Cena de Ação

- **Antes (Prompt Vago):**
  > "Um carro persegue outro carro em uma cidade."
  > *(Resultará em uma perseguição genérica, com ângulos de câmera e movimentos imprevisíveis.)*

- **Depois (Prompt Cinematográfico):**
  > "Uma perseguição de carros em alta velocidade pelas ruas de uma cidade chuvosa à noite. `Low-angle shot` de um muscle car preto derrapando em uma esquina, pneus cantando no asfalto molhado. `Handheld shot` do interior do carro, mostrando o rosto tenso do motorista. `Drone shot` aéreo seguindo os dois carros enquanto eles desviam do tráfego. `SFX: motores roncando, pneus cantando, buzinas de carro.`"
  > *(**Por que funciona:** Usa uma variedade de planos e ângulos de câmera para criar uma sequência de ação dinâmica e emocionante, com design de som integrado.)*

### Exemplo 2: Cena de Natureza Documental

- **Antes (Prompt Simples):**
  > "Um vídeo de uma baleia jubarte pulando fora da água."
  > *(Pode gerar um bom clipe, mas sem o drama e a escala de um documentário.)*

- **Depois (Prompt Cinematográfico):**
  > "Estilo de documentário da BBC. `Extreme wide shot` do oceano Pacífico calmo ao amanhecer, `golden hour`. Uma baleia jubarte gigante quebra a superfície em `slow motion`, água espirrando em todas as direções. A câmera, em um `gimbal shot` a partir de um barco, treme ligeiramente com o impacto. `Close-up` na cauda da baleia enquanto ela mergulha de volta. `Ambiente: som suave das ondas, som majestoso da baleia.`"
  > *(**Por que funciona:** Define um estilo (`BBC documentary`), especifica a iluminação (`golden hour`), usa `slow motion` para dar drama e descreve o movimento da câmera e o som para criar uma sensação de realismo e escala.)*

### Exemplo 3: Animação de Personagem

- **Antes (Prompt Básico):**
  > "Uma animação de um robô fofo."
  > *(O estilo da animação e a personalidade do robô são deixados ao acaso.)*

- **Depois (Prompt Cinematográfico):**
  > "Animação 3D no estilo da Pixar. Um pequeno robô com um único olho grande e expressivo tropeça em seus próprios pés enquanto persegue uma borboleta brilhante em um campo verdejante. `Close-up` em seu olho, que pisca em confusão e depois se ilumina com determinação. A câmera segue o robô em um `low-angle tracking shot`, fazendo-o parecer pequeno e heróico. `SFX: sons de bipe eletrônicos fofos, zumbido da borboleta.`"
  > *(**Por que funciona:** Especifica um estilo de animação bem conhecido (`Pixar style`), descreve a personalidade e a ação do personagem, e usa ângulos de câmera para evocar emoção.)*

### Exemplo 4: Clipe de Comida (Food Porn)

- **Antes (Prompt Simples):**
  > "Um vídeo de um hambúrguer sendo feito."
  > *(Provavelmente resultará em um vídeo plano e pouco apetitoso.)*

- **Depois (Prompt Cinematográfico):**
  > "Comercial de comida em `extreme close-up`. Um hambúrguer suculento é montado em `slow motion`. `Macro shot` do queijo derretendo sobre a carne grelhada. Uma folha de alface crocante e gotas de condensação em um tomate vermelho vivo. A câmera faz um `orbit shot` de 360 graus em torno do hambúrguer finalizado. Iluminação de estúdio, quente e apetitosa. `SFX: chiado da carne na grelha.`"
  > *(**Por que funciona:** Usa técnicas de macro e slow motion, comuns em comerciais de comida, para tornar o sujeito visualmente atraente. A descrição detalhada dos ingredientes e da iluminação aumenta o apelo.)*



## 7. Avaliação & Métricas

A avaliação de prompts para Sora é inerentemente visual e subjetiva, mas pode ser estruturada em torno de critérios cinematográficos.

- **Fidelidade ao Prompt:** A métrica mais fundamental. O vídeo gerado contém os elementos-chave (sujeito, ação, cenário) descritos no prompt? (Passa/Falha)

- **Qualidade Cinematográfica:** O quão bem o modelo interpretou as instruções cinematográficas? Se você pediu um `dolly shot`, a câmera se moveu como esperado? A iluminação de `golden hour` foi alcançada? Isso requer um olho treinado, mas pode ser pontuado em uma escala.

- **Coerência Temporal e Espacial:** O vídeo mantém a consistência ao longo de sua duração? Os objetos permanecem os mesmos? O personagem não muda de roupa no meio da cena? A ausência de "artefatos" temporais é um indicador de alta qualidade.

- **Realismo da Física:** As interações físicas no vídeo são críveis? Os objetos têm peso? A gravidade funciona como esperado? Para cenas que não são de fantasia, o realismo físico é uma métrica chave.

- **Qualidade da Animação (se aplicável):** Para personagens ou objetos em movimento, a animação é fluida e natural, ou é rígida e robótica? A avaliação aqui é semelhante à da animação tradicional.

- **Teste de Variação (A/B Testing Visual):** A melhor maneira de refinar um prompt é gerar duas ou mais variações e compará-las lado a lado. Qual versão captura melhor a emoção? Qual tem a composição mais forte? Esta é uma forma de teste A/B qualitativo.

- **Rubricas de Avaliação para Sora:**

| Critério | Descrição da Avaliação |
| :--- | :--- |
| **Interpretação Cinematográfica** | O modelo executou corretamente os tipos de plano, movimentos de câmera e ângulos especificados no prompt? (Subjetivo, 1-5) |
| **Coerência da Cena** | A cena permanece visualmente e espacialmente consistente do início ao fim, sem objetos ou personagens se transformando? (Sim/Não) |
| **Realismo Físico** | As interações entre objetos e personagens obedecem a uma física crível (para o mundo retratado)? (Subjetivo, 1-5) |
| **Estilo Visual** | A estética geral do vídeo (cores, iluminação, textura) corresponde ao estilo descrito no prompt? (Subjetivo, 1-5) |
| **Qualidade do Áudio (se aplicável)** | O áudio gerado (diálogo, SFX, ambiente) está sincronizado e alinhado com a descrição no prompt? (Sim/Não) |



## 8. Anti-padrões (o que evitar) e Alternativas

| Anti-padrão | Descrição do Problema | Alternativa / Solução |
| :--- | :--- | :--- |
| **O Prompt "Romance"** | Escrever um parágrafo longo e narrativo descrevendo uma história, em vez de uma cena. Sora não é um contador de histórias; é um simulador de câmera. Ele se confunde com enredos complexos. | **Pense como um Roteirista de Cenas:** Foque em descrever o que a câmera vê e ouve em um momento específico. Use a estrutura de shot list. Em vez de "um herói triste lamenta sua perda", escreva "`Close-up` no rosto de um homem, uma única lágrima escorre por sua bochecha; `SFX: chuva suave`". |
| **Ambiguidade Visual** | Usar termos vagos como "um lugar bonito" ou "uma cena interessante". A beleza e o interesse são subjetivos e não dão ao modelo nenhuma informação visual concreta para trabalhar. | **Pinte uma Imagem com Palavras:** Seja ultra-específico. Em vez de "um lugar bonito", descreva "uma praia de areia branca com água azul-turquesa cristalina, coqueiros balançando suavemente ao vento sob um céu sem nuvens". |
| **O Prompt "Lista de Desejos"** | Tentar colocar várias ideias, cenas ou personagens não relacionados em um único prompt, esperando que o Sora os junte de alguma forma. | **Um Prompt, Uma Cena:** Cada prompt deve se concentrar em uma única cena ou ação contínua. Se você quer um vídeo com várias cenas, gere cada uma separadamente e edite-as juntas depois. |
| **Ignorar a Cinematografia** | Descrever apenas o sujeito e a ação, sem dar nenhuma instrução sobre como a cena deve ser filmada. | **Seja o Diretor:** Sempre inclua instruções de câmera. Mesmo algo simples como "`medium shot`" ou "`handheld camera`" dá ao Sora um controle muito maior e resulta em um vídeo mais intencional. |
| **Contradições Físicas ou Lógicas** | Descrever algo que é fisicamente impossível ou logicamente contraditório (ex: "um cubo redondo", "um peixe voando no deserto"), a menos que o objetivo seja o surrealismo. | **Aterre seu Prompt na Realidade (ou em uma Realidade Consistente):** Mesmo em cenas de fantasia, mantenha as regras do seu mundo consistentes. Se os porcos podem voar, descreva como suas asas batem e como o vento interage com elas. |
| **Negligenciar o Áudio** | Escrever um prompt visualmente detalhado, mas não mencionar o som. Isso resulta em um vídeo silencioso ou com áudio genérico e desalinhado. | **Faça o Design de Som no Prompt:** Sempre inclua descrições de diálogo, efeitos sonoros e ambiente para criar uma experiência imersiva. O áudio é metade da experiência do vídeo. |



## 9. Checklist Final (Pronto para Uso)

- [ ] **Estrutura de Shot List:** O seu prompt está estruturado como uma sequência de cenas ou ações, em vez de um parágrafo narrativo?
- [ ] **Linguagem Cinematográfica:** Você incluiu termos específicos de cinema para descrever os tipos de plano, ângulos e movimentos de câmera?
- [ ] **Especificidade Visual:** O prompt descreve em detalhes a iluminação, as cores, as texturas e o estilo visual da cena?
- [ ] **Física e Detalhes Sensoriais:** Você descreveu o peso, o movimento e a interação física dos objetos e personagens para aumentar o realismo?
- [ ] **Design de Som Integrado:** O prompt inclui descrições claras de diálogo, efeitos sonoros (SFX) e som ambiente?
- [ ] **Foco em Uma Cena Coerente:** O prompt se concentra em uma única cena ou ação contínua, evitando misturar ideias desconexas?
- [ ] **Gancho no Início:** A parte mais importante ou visualmente cativante do seu prompt está no início?
- [ ] **Linguagem Descritiva em Vez de Parâmetros:** Você está descrevendo o resultado desejado (ex: "vídeo em widescreen cinematográfico") em vez de tentar usar controles não documentados (ex: `--ar 16:9`)?
- [ ] **Pronto para Iterar:** Você está preparado para gerar múltiplas variações e refinar seu prompt com base nos resultados, como um editor de vídeo?
- [ ] **Uso de um Copiloto de Prompt:** Você considerou usar um LLM de texto para ajudar a traduzir sua ideia inicial em um prompt cinematográfico detalhado para o Sora?

## 10. Notas e Fontes

Este documento foi compilado a partir de análises de especialistas, guias da comunidade e engenharia reversa dos prompts de exemplos divulgados pela OpenAI para o Sora.

1.  **Skywork AI: 12 Essential Sora 2 Prompting Tips for Video Creators (2025).** Uma análise aprofundada e uma das fontes mais detalhadas sobre técnicas de prompting para Sora, focando na estrutura de shot list e na linguagem cinematográfica. URL: `https://skywork.ai/blog/sora-2-prompting-tips-2025/`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 9/10.
2.  **Prompt Perfect Daily: Sora Prompt Guide: Tips for Creating High-Quality Videos.** Um guia que enfatiza a importância de cenas coerentes e da especificidade visual. URL: `https://daily.promptperfect.xyz/p/sora-prompt-guide`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
3.  **Filmart.ai: Master Sora Prompts: 6 steps Guide to Perfect AI Videos.** Um guia passo a passo que reforça a necessidade de ser específico em todos os aspectos da cena. URL: `https://filmart.ai/guide-to-sora-prompts/`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 7/10.
4.  **OpenAI Technical Report (Análise da Comunidade):** Análise do paper técnico original do Sora por pesquisadores, que discute a capacidade do modelo de entender o mundo físico. URL: `https://arxiv.org/abs/2402.17177`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 9/10.
5.  **DataCamp: How to Use Sora AI: A Guide With 10 Practical Examples.** Um tutorial com exemplos práticos que ilustram diferentes tipos de prompts para o Sora. URL: `https://www.datacamp.com/tutorial/sora-ai`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.
6.  **SaasGenius: The Ultimate Guide to Sora AI + Prompts and Examples.** Um guia abrangente que cobre desde o básico até exemplos de prompts mais avançados. URL: `https://www.saasgenius.com/blog-business/the-ultimate-guide-to-sora/`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 7/10.
7.  **Reddit (r/OpenAI): Sora Analysis - 32 Experiments.** Uma análise da comunidade com base em experimentos, destacando a importância da brevidade e da especificidade. URL: `https://www.reddit.com/r/OpenAI/comments/1hbed8s/sora_analysis_32_experiments_what_works_what/`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 7/10.
8.  **OpenAI Help Center: Best practices for prompt engineering.** Embora não seja específico para o Sora, o guia geral de prompt da OpenAI fornece princípios fundamentais que ainda se aplicam. URL: `https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-the-openai-api`. Data da pesquisa: 2025-10-04. Nota de confiabilidade: 8/10.

