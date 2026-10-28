---
title: "Exercício 2 - Agente de nuvem do GitHub Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Lição anterior: Instruções personalizadas][previous-lesson] |
|:--|

É provável que muitas organizações enfrentem algum tipo de dívida técnica: problemas de segurança não resolvidos, código legado que precisa de atualização ou solicitações de recursos que permanecem no backlog por falta de tempo. O agente de nuvem do GitHub Copilot pode atualizar código e adicionar funcionalidades de forma autônoma. Ao concluir o trabalho, ele cria um pull request de rascunho para revisão por uma pessoa desenvolvedora. Assim, você pode delegar tarefas repetitivas e se concentrar em questões mais abrangentes.

Neste exercício, você vai explorar como:

- personalizar o ambiente de geração de código.
- garantir que as operações sejam executadas com segurança.
- criar issues com escopo claro.
- atribuir issues ao Copilot.

## Cenários

A Tailspin Toys quer reduzir a dívida técnica. As pessoas contratadas para criar a primeira versão do site deixaram a documentação em um estado insatisfatório: na prática, ela está ausente. Como primeiro passo, a equipe quer adicionar comentários TSDoc a todas as funções exportadas do aplicativo.

Além disso, a equipe de design quer melhorar a descoberta de jogos. Cada página de detalhes deve mostrar outros jogos da mesma categoria para que as pessoas continuem explorando o catálogo. A equipe ainda não precisa de uma implementação refinada; quer algo que possa usar para testes de aceitação da experiência. Embora essa tarefa seja um bloqueio, há outras issues com prioridade maior no momento.

Esses são exemplos de tarefas que podem ser adiadas e que são ideais para o agente de nuvem do Copilot. Ele trabalha de forma assíncrona enquanto você se dedica a outras atividades e, depois, você pode revisar o resultado.

## Conhecer o agente de nuvem do GitHub Copilot

O [agente de nuvem do GitHub Copilot](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) executa tarefas em segundo plano de forma semelhante a uma pessoa desenvolvedora. Você pode, por exemplo, [atribuir uma issue do GitHub ao Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions). Depois da atribuição, o Copilot cria um pull request de rascunho para acompanhar o progresso, configura um ambiente e começa a trabalhar. Você pode acompanhar a sessão enquanto o trabalho está em andamento ou depois de concluído. Quando a solução estiver pronta para revisão, o Copilot mencionará você no pull request.

## A importância de instruções com escopo claro

Não há mágica no GitHub Copilot: ainda é necessário pensar. Mesmo tarefas aparentemente simples podem esconder complexidade. Por isso, [defina com cuidado o escopo das tarefas para o agente de nuvem][cloud-agent-best-practices]. Trate-o como um assistente de programação com IA: trabalhe em etapas, aprenda, experimente e adapte o processo. Os fundamentos do desenvolvimento de software não mudam com a IA generativa.

## Instruções personalizadas deste repositório

Nas lições anteriores, você conheceu as instruções personalizadas e como elas orientam o Copilot. Se já concluiu o exercício prático de instruções personalizadas, viu `.github/copilot-instructions.md` em ação. Caso contrário, aproveite para examiná-lo. Antes de atribuir trabalho ao agente de nuvem, leia os arquivos de instruções do repositório sem alterá-los para observar o efeito deles mais tarde.

Abra os arquivos a seguir na interface Web do GitHub ou em um Codespace:

- `.github/copilot-instructions.md`: examine a seção **Code standards**, principalmente as convenções de TypeScript e os comentários TSDoc.
- `.github/instructions/unit-tests.instructions.md`: observe o frontmatter `applyTo`, que limita as instruções aos arquivos `**/*.test.ts`.

Ao atribuir a issue `Code lacks documentation` ao agente de nuvem na próxima seção, observe se o pull request inclui comentários TSDoc, segue as convenções de TypeScript e adiciona comentários aos arquivos. Essas orientações vêm dos arquivos de instruções e serão mencionadas novamente em uma lição posterior.

## Configurar o ambiente de desenvolvimento do agente de nuvem

Desenvolver código normalmente exige um ambiente específico e scripts de configuração. O mesmo vale para o Copilot, que executa tarefas de maneira semelhante a uma pessoa engenheira de software.

O agente de nuvem usa o [GitHub Actions][github-actions] para configurar o ambiente. Você pode personalizá-lo com um [fluxo de trabalho de configuração][setup-workflow] em `.github/workflows/copilot-setup-steps.yml`, executado antes do início do trabalho. O modelo já inclui esse fluxo para facilitar o laboratório. Ele instala Node.js e as dependências do projeto, os navegadores do Playwright para testes de ponta a ponta e configura o banco de dados SQLite migrado e preenchido com dados para o aplicativo Astro:

```yaml
name: "Copilot Setup Steps"

# Permite testar as etapas de configuração na guia "Actions" do repositório
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Conceda somente as permissões mínimas necessárias às suas etapas. O Copilot usa seu próprio token para as operações dele.
    permissions:
      # Se as etapas de configuração clonarem o repositório, por exemplo, para instalar dependências, será necessária a permissão `contents: read`. Caso contrário, o Copilot clonará o repositório automaticamente depois que as etapas forem concluídas.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Configuração do aplicativo: Node.js (o aplicativo agora é Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # Migrar e preencher o banco de dados SQLite local para que compilações e testes tenham dados.
      - name: Set up the database
        run: npm run db:setup
```

Esse arquivo se parece com outros fluxos de trabalho do GitHub, mas tem alguns pontos importantes:

- Ele contém um único trabalho, `copilot-setup-steps`, executado no GitHub Actions antes de o Copilot começar a trabalhar no pull request.
- O gatilho `workflow_dispatch` permite executar o fluxo manualmente na guia **Actions** do repositório. Isso é útil para verificar se ele funciona antes de aguardar uma execução do Copilot.

## Adicionar documentação

Muitos projetos têm documentação desatualizada ou inexistente. Essa dívida técnica reduz a produtividade e dificulta a manutenção do código e a integração de novas pessoas à equipe. O Copilot pode ajudar a criar a documentação, por isso esta é uma issue adequada para atribuir ao agente de nuvem. Em um exercício futuro, você voltará para revisar o resultado.

1. Em uma nova guia do navegador, acesse seu repositório no github.com.
2. Selecione a guia **Issues**.
3. Selecione **New issue** para abrir a caixa de criação de issue.
4. Selecione **Blank issue**.
5. Defina o **Title** como `Code lacks documentation`.
6. Defina a **Description** como:

   ```plaintext
   Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
   ```

7. Selecione **Create** para criar a issue.
8. À direita, selecione **Assign to Copilot** para abrir a caixa de atribuição.

   ![Atribuir uma issue ao Copilot](../../../_images/shared-assign-copilot.png)

9. Selecione **Assign**.
10. Selecione a guia **Pull Requests**.
11. Abra o pull request (PR) recém-criado, com um título semelhante a `[WIP]: Code lacks documentation`. Se ele ainda não aparecer, aguarde um ou dois minutos e atualize a página.
12. Depois de alguns minutos, o Copilot deverá criar uma lista de tarefas.

> [!NOTE]
> A lista de tarefas pode levar alguns minutos para aparecer no PR. O Copilot está configurando o ambiente com o fluxo de trabalho descrito acima, analisando o projeto e definindo uma abordagem.

13. Revise a lista e as tarefas que o Copilot pretende executar.
14. Role a linha do tempo do pull request e procure uma atualização informando que o Copilot começou a trabalhar na issue.
15. Selecione **View session**.

   ![Visualização da sessão do Copilot](../../../_images/ex4-view-session.png)

> [!CAUTION]
> Talvez seja necessário atualizar a página para ver o indicador atualizado.

16. Acompanhe a sessão em tempo real. O Copilot explora o código, analisa seu estado, decide uma abordagem e cria código.

Isso pode levar alguns minutos. Uma das principais vantagens do agente de nuvem é executar tarefas de forma assíncrona, liberando você para trabalhar em outras atividades. Aproveite esse recurso: atribua outra tarefa ao agente e, em seguida, desenvolva uma funcionalidade no aplicativo.

## Adicionar uma seção de jogos relacionados à página de detalhes

O agente de nuvem permite dividir o trabalho: você pode se concentrar em uma tarefa enquanto ele executa outra. Mesmo que a seção de jogos relacionados não demore muito, delegá-la libera seu tempo. Vamos atribuí-la ao agente de nuvem.

1. Volte ao repositório no github.com.
2. Selecione a guia **Issues**.
3. Selecione **New issue** e, em seguida, **Blank issue**.
4. Defina o **Title** como `Show related games on the game details page`.
5. Defina a **Description** como:

   ```markdown
   We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
   ```

6. Selecione **Create** e, à direita, **Assign to Copilot**.
7. Selecione **Assign**.

Pouco depois, um emoji 👀 deverá aparecer no primeiro comentário da issue, indicando que o Copilot está trabalhando nela.

![O Copilot usa o emoji de olhos para indicar que está trabalhando na issue](../../../_images/ex4-issue-eyes-emoji.png)

O agente de nuvem está trabalhando na segunda solicitação. Como ele atua de forma semelhante a uma pessoa engenheira de software, não é necessário monitorá-lo continuamente. Revise o resultado quando estiver pronto. Enquanto isso, vamos criar e usar agentes personalizados.

## Resumo e próximos passos

Neste exercício, você conheceu o [agente de nuvem do GitHub Copilot][copilot-agents]. Você pode atribuir issues ao agente para que ele trabalhe de forma assíncrona. Use-o para resolver dívida técnica, criar recursos ou ajudar na migração de código entre frameworks.

Você explorou:

- a personalização do ambiente de geração de código.
- a execução segura de operações.
- a importância de issues com escopo claro.
- a atribuição de issues ao Copilot.

Enquanto o agente trabalha, você pode criar e usar agentes personalizados. O [agente de nuvem do Copilot também pode usar servidores MCP][cloud-agent-mcp] e as instruções personalizadas que você explorou nas lições anteriores.

## Recursos

- [Sobre o agente de nuvem do Copilot][copilot-agents]
- [Atribuir issues do GitHub ao Copilot][assign-issue]
- [Práticas recomendadas para o fluxo de configuração do agente de nuvem][cloud-agent-best-practices]

| [Próxima lição: Agentes personalizados →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
