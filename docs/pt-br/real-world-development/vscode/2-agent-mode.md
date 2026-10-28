---
title: "Exercício 2 - Adicionar funcionalidade com o Agent Mode do Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

Até a alteração mais simples de um aplicativo pode exigir mudanças em vários arquivos e a execução de tarefas, como testes. Normalmente, a pessoa desenvolvedora localiza os arquivos necessários, faz as alterações, executa testes, depura, identifica arquivos que faltaram e volta a alterar o código. O processo pode continuar por várias etapas.

É aí que entra o Agent Mode do Copilot.

O Agent Mode funciona de forma mais autônoma no IDE. Ele explora a estrutura existente do projeto, faz as alterações necessárias, executa tarefas como testes e corrige automaticamente os problemas encontrados. Veja como usá-lo para adicionar funcionalidade ao site.

> [!NOTE]
> Apesar dos nomes parecidos, o modo de agente e o agente de nuvem oferecem experiências diferentes. O modo de agente executa tarefas no IDE, permitindo ciclos rápidos de feedback e interação. O agente de nuvem trabalha de forma assíncrona, como uma pessoa da equipe, e interage por meio de issues e pull requests.

Neste exercício, você vai aprender como:

- o Agent Mode do Copilot explora o projeto, identifica arquivos relevantes e coordena alterações.
- o Agent Mode do GitHub Copilot implementa recursos na interface e na camada de dados.
- revisar alterações e testes gerados pelo Agent Mode antes de integrá-los ao código.

## Cenário

À medida que a lista de jogos cresce, você quer permitir que as pessoas filtrem por categoria e distribuidora. No exercício anterior, você adicionou um helper de distribuidoras. Agora, conclua com o Agent Mode do Copilot o restante do trabalho na camada de dados, na interface e nos testes.

## Executar o site Tailspin Toys

Antes de alterar o código, explore o site para conhecer o comportamento atual.

A Tailspin Toys é uma plataforma de financiamento coletivo para jogos de tabuleiro com tema de desenvolvimento. O site lista jogos e exibe seus detalhes. É um único aplicativo Astro que gera páginas HTML estáticas durante a compilação. As páginas consultam diretamente um banco de dados SQLite local por meio do Drizzle ORM; não há uma API de backend separada nem um framework de interface no cliente. Os helpers reutilizáveis de acesso a dados ficam em `src/lib/`, e a interatividade usa pequenos elementos `<script>` do Astro com APIs padrão do DOM.

### Iniciar o site

O comando `npm run dev` inicia o servidor de desenvolvimento do Astro.

1. Volte ao Codespace. Ao terminar o exercício anterior, você deve estar na branch `main`.
2. Abra um terminal com <kbd>Control</kbd>+<kbd>`</kbd> (Mac) ou <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux).
3. Crie uma branch para o trabalho de filtragem:

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. Inicie o site:

   ```bash
   npm run dev
   ```

   Quando o servidor Astro estiver pronto, será exibido um endereço semelhante a:

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> Se uma caixa de diálogo solicitar a abertura de `http://localhost:4321` no navegador, feche-a selecionando **x**.

5. Para abrir o site, mantenha pressionada a tecla <kbd>Command</kbd> (Mac) ou <kbd>Ctrl</kbd> (Windows/Linux) e selecione o endereço `http://localhost:4321` no terminal.

> [!NOTE]
> Em um Codespace, selecionar o link local no terminal redireciona automaticamente para `https://<your-codespace-name>-4321.app.github.dev/`. Esse endereço é um túnel privado para o servidor em execução no Codespace.

### Explorar o site

Os principais recursos do site são:

- **Página inicial**: lista jogos de tabuleiro com títulos, imagens e descrições.
- **Página de detalhes do jogo**: ao selecionar um jogo, você vê mais informações, como título, descrição, distribuidora e categoria.

## Explorar o backlog com o Copilot

> [!TIP]
> **Abrir o Copilot Chat**
>
> Antes de começar, volte ao Codespace, abra o painel do Copilot Chat e selecione **New Chat** para iniciar uma conversa limpa. O modo e o modelo variam conforme o exercício; as etapas indicarão as opções relevantes.

O site já funciona, mas você vai adicionar novos recursos. Comece examinando o backlog que foi criado automaticamente a partir do modelo.

1. Na visualização Chat, selecione **Agent** no seletor de agentes. O Agent pode planejar e implementar alterações em vários arquivos, executar comandos de terminal e chamar ferramentas.

   ![Seletor de agente na visualização Chat](../../../_images/shared-chat-mode-selector.png)

2. Selecione **Claude Sonnet 4.5** na lista de modelos disponíveis.

> [!CAUTION]
> Os autores deste workshop não recomendam um modelo em particular. As instruções mencionam Claude Sonnet 4.5 porque ele foi usado durante a criação do workshop e ajuda a manter a experiência consistente. Como os modelos de linguagem são probabilísticos, as sugestões podem ser diferentes dos exemplos. Isso é normal.

> [!NOTE]
> Por causa da natureza probabilística dos modelos de linguagem, o Copilot pode usar um comando MCP diferente, mas ainda deve conseguir concluir a tarefa.

3. Envie este prompt para pedir ao Copilot que examine o backlog:

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. Selecione **Continue** para permitir que o comando liste todas as issues.
5. Revise a lista criada.

O Copilot também prioriza os itens com base na utilidade que estima para as pessoas usuárias.

## Revisar os arquivos de instruções

Antes de pedir ao agente que gere código, examine o arquivo de instruções que fornecerá contexto. O arquivo [de interface do usuário (UI)](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md) explica como adicionar funcionalidade ao site.

1. No Codespace, abra `.github/instructions/ui.instructions.md`.
2. Observe as orientações gerais, que incluem:
   - uma visão geral da arquitetura.
   - princípios de design de componentes, testabilidade e acessibilidade.
   - links para instruções específicas a tipos de arquivo, incluindo Astro, Tailwind CSS e Drizzle.

> [!TIP]
> Os arquivos de instruções podem referenciar outros arquivos de instruções e arquivos do projeto. Os caminhos são relativos ao próprio arquivo de instruções. Assim, você pode reutilizar orientações, dividir arquivos complexos em partes menores e fornecer exemplos e modelos.

## Implementar a filtragem

Para concluir a filtragem, são necessárias pelo menos três alterações:

- adicionar ou aprimorar a lógica de filtragem restante na camada de dados (`src/lib/`).
- adicionar ou atualizar testes do comportamento de filtragem.
- atualizar a página de listagem de jogos para incluir controles de filtragem.

Os testes também precisam ser executados e aprovados antes do merge. O Agent Mode do Copilot pode executar essas tarefas.

1. Continue na conversa atual ou selecione **New Chat** para iniciar outra.
2. Selecione **Add Context**, **Instructions** e **ui** para adicionar o arquivo de instruções.

   ![Exemplo da seleção do arquivo de instruções de interface](../../../_images/ex3-select-instructions-file.png)

3. Confirme se **Agent** continua selecionado.

   ![Seletor de agente na visualização Chat](../../../_images/shared-chat-mode-selector.png)

4. Confirme se **Claude Sonnet 4.5** continua selecionado.
5. Use este prompt para implementar a funcionalidade descrita na issue relacionada do backlog:

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Acompanhe o Copilot enquanto ele explora o projeto, localiza os arquivos relacionados, altera os helpers de dados e a interface e executa testes.

   ![Copilot explorando os arquivos do projeto](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> O Copilot executará várias tarefas, como explorar o projeto, alterar arquivos e rodar testes. Isso pode levar alguns minutos. Durante o trabalho, os botões **Keep** e **Undo** podem aparecer no editor. Aguarde a conclusão antes de aceitar ou desfazer as alterações.

7. Quando o Copilot solicitar, selecione **Continue** para executar os testes.

   ![Solicitação no painel Copilot Chat para confirmar a execução dos testes](../../../_images/ex3-agent-mode-run-tests.png)

8. Alguns testes podem falhar durante o processo. O Copilot alterna entre geração de código e testes até concluir a tarefa e não detectar erros.

   ![Sessão concluída do Copilot Agent Mode](../../../_images/ex3-agent-mode-proposed-changes.png)

9. Examine o código gerado para identificar possíveis problemas.

> [!CAUTION]
> Sempre revise o código gerado pelo Copilot ou por outras ferramentas de IA.

10. Volte ao navegador em que o site está em execução e experimente o recurso.
11. Depois de confirmar que o recurso funciona e revisar o código, selecione **Keep** na janela do Copilot Chat.

## Resumo e próximos passos

Neste exercício, você usou o Agent Mode do GitHub Copilot para adicionar recursos ao site da Tailspin Toys. Você aprendeu como:

- o Agent Mode do GitHub Copilot implementa recursos na interface e na camada de dados.
- o Agent Mode explora o projeto, identifica arquivos relevantes e coordena alterações.
- revisar as alterações e os testes gerados antes do merge.

Agora, [teste o recurso com o servidor MCP do Playwright][next-lesson] e abra um pull request.

### Exercício extra — implementar paginação

À medida que a lista de jogos crescer, será necessário adicionar paginação. Use o que aprendeu neste exercício para pedir ao Copilot que atualize o site. Considere:

- seguir as práticas existentes e usar os arquivos de instruções.
- decidir se a pessoa usuária poderá escolher o tamanho da página ou se ele será fixo.
- incluir no prompt as orientações necessárias para obter a implementação desejada.
- iterar com o GitHub Copilot, pedindo alterações e fornecendo contexto. Esse processo é normal ao trabalhar com o Copilot.

## Recursos

- [Copilot ask, edit e agent modes: para que servem e quando usar cada um][choose-mode]
- [Modo de agente no VS Code][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
