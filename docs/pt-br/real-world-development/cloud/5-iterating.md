---
title: "Exercício 5: Iterar sobre o trabalho do GitHub Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Lição anterior: Gerenciar agentes][previous-lesson] |
|:--|

## Revisar o trabalho

Durante este laboratório, você trabalhou com o GitHub Copilot em várias tarefas para melhorar a experiência e adicionar funcionalidades. Você pediu ao Copilot que documentasse o código, criasse um recurso de jogos relacionados para a equipe de design testar e implementasse recursos de acessibilidade, incluindo modos de alto contraste e claro. Agora, revise as alterações e, se necessário, envie comentários ao Copilot para aprimorar o trabalho.

### Cenário

Os fundamentos de design de software e DevOps não mudam com a IA generativa. Sempre revise o código gerado e siga o fluxo de DevOps habitual. Vamos revisar as propostas do GitHub Copilot para a documentação, os jogos relacionados e os recursos de acessibilidade antes de habilitar a revisão pela equipe.

## Segurança e agente de nuvem do GitHub Copilot

Como o agente de nuvem do Copilot executa tarefas de forma assíncrona e sem supervisão direta, há limites de segurança:

- O Copilot tem acesso de leitura ao repositório e acesso de escrita **somente** à branch que usará para o código.
- O agente de nuvem executa o trabalho em um ambiente separado e temporário no GitHub Actions.
- Uma pessoa precisa aprovar a execução de fluxos de trabalho do GitHub Actions.
- [O acesso a recursos externos é limitado por padrão](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall), incluindo o acesso a servidores MCP.

## Revisar a documentação gerada

Comece pelo primeiro pull request (PR) criado pelo agente de nuvem do GitHub Copilot: o que adiciona documentação ao código. Use a interface padrão de PR do GitHub.com.

> [!NOTE]
> Talvez o PR mostre um aviso de que o firewall bloqueou o GitHub Copilot. Isso é **esperado**: por padrão, o Copilot tem acesso limitado a recursos externos, inclusive a servidores MCP. Se quiser, você pode [personalizar ou desabilitar o firewall do agente de nuvem][agent-firewall].

1. Volte ao repositório no github.com.
2. Selecione **Pull Requests** para abrir a lista de PRs.
3. Abra o PR com um título semelhante a **Add missing documentation**.

> [!NOTE]
> Se o Copilot ainda estiver trabalhando, o PR estará marcado como **[WIP]**. Aguarde a conclusão, que pode levar alguns minutos.

4. Quando o PR estiver pronto, selecione **Files changed** e revise as alterações.

    ![Guia Files changed](../../../_images/shared-pr-files-changed.png)

5. Examine o código atualizado, que inclui comentários TSDoc e outra documentação. As alterações exatas variam. Procure comentários TSDoc, convenções de TypeScript e cabeçalhos de comentário: eles seguem os arquivos de instruções personalizados que você examinou no início do Exercício 2.
6. Quando terminar a revisão, volte à guia **Conversation** e role a tela para baixo.
7. Verifique se há um indicador de que os fluxos de trabalho aguardam aprovação.
8. Se houver fluxos de trabalho aguardando aprovação, selecione **Approve and run workflows**.

   ![Aprovar e executar fluxos de trabalho](../../../_images/shared-approve-workflows.png)

9. Os fluxos de trabalho aparecerão na seção de verificações do PR. Se tudo correr bem, as verificações do projeto para o único aplicativo Astro serão aprovadas. Isso pode levar alguns minutos.

## Pedir alterações ao GitHub Copilot

O trabalho com o Copilot em um PR não é unilateral. Você pode mencioná-lo em comentários gerais ou em comentários no código. O Copilot verá esses comentários e iniciará outra sessão para atendê-los. Como os resultados são não determinísticos, não há um único texto de solicitação. Por exemplo, você pode pedir que o Copilot:

- adicione no início de cada arquivo de código um cabeçalho com uma breve descrição da finalidade.
- adicione comentários TSDoc a arquivos TypeScript e Astro.
- crie um README que descreva a estrutura do aplicativo Astro.

1. Comente no PR e mencione **@copilot** como faria com qualquer pessoa. Use uma das sugestões acima ou peça outra melhoria de documentação.
2. Selecione **View Session** para acompanhar o trabalho. O Copilot iniciará uma nova sessão para fazer as atualizações.
3. Para voltar ao PR, selecione **Back to pull request**.

    ![Voltar ao pull request](../../../_images/ex7-back-to-pr.png)

4. Quando o Copilot concluir as alterações, um novo commit aparecerá no PR.
5. Selecione **Files changed** para revisar as mudanças.

Continue iterando até ficar satisfeito. Depois, marque o PR como pronto para revisão e faça o merge na branch principal.

![Marcar o PR como pronto para revisão](../../../_images/ex7-ready-for-review.png)

## Revisar o recurso de jogos relacionados

Agora, revise o PR criado pelo Copilot para mostrar jogos relacionados na página de detalhes de cada jogo.

1. Volte ao repositório no GitHub.com.
2. Selecione **Pull Requests**.
3. Abra o PR com um título semelhante a **Show related games on the game details page**.
4. Selecione **Files changed** e revise o código.
5. Quando terminar, volte à guia **Conversation** e role a tela para baixo.
6. Se houver fluxos de trabalho aguardando aprovação, selecione **Approve and run workflows**.

   ![Aprovar e executar fluxos de trabalho](../../../_images/shared-approve-workflows.png)

7. Os fluxos de trabalho aparecerão na seção de verificações do PR. Se tudo correr bem, as verificações do projeto para o único aplicativo Astro serão aprovadas. Isso pode levar alguns minutos.
8. **Opcional:** teste manualmente o recurso no Codespace. Abra o terminal e execute os comandos a seguir, substituindo `<branch-name>` pelo nome da branch criada pelo Copilot, por exemplo, `copilot/fix-8`:

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

O Copilot criou o recurso de jogos relacionados. Você pode pedir outras alterações por meio de comentários na guia **Conversation**; o Copilot iniciará outra sessão. Por exemplo, peça para ajustar a quantidade de jogos exibidos ou adicionar cabeçalhos de comentário e comentários TSDoc. Lembre-se: essa issue foi atribuída antes de você atualizar as instruções personalizadas.

## Revisar os recursos de acessibilidade

Por fim, revise os recursos de acessibilidade implementados pelo agente personalizado. O PR deve incluir o modo de alto contraste solicitado no Exercício 3 e o modo claro solicitado na sessão do painel de agentes no Exercício 4.

1. Volte ao repositório no GitHub.com.
2. Selecione **Pull Requests**.
3. Abra o PR com um título semelhante a **Add high contrast mode to website**.

> [!NOTE]
> Se o Copilot ainda estiver trabalhando, o PR estará marcado como **[WIP]**. Aguarde a conclusão, que pode levar alguns minutos.

4. Selecione **Files changed** e revise o código gerado.
5. Examine a implementação, principalmente:
   - os componentes de alternância dos modos.
   - o uso do armazenamento local do navegador para persistir as preferências.
   - as alterações de CSS ou estilo para os modos de alto contraste e claro.
   - os recursos de acessibilidade, como atributos ARIA e navegação por teclado.
   - o JavaScript ou TypeScript responsável pela alternância dos modos.

6. Quando terminar, volte à guia **Conversation** e role a tela para baixo.
7. Se houver fluxos de trabalho aguardando aprovação, selecione **Approve and run workflows**.

   ![Aprovar e executar fluxos de trabalho](../../../_images/shared-approve-workflows.png)

8. Os fluxos de trabalho aparecerão na seção de verificações do PR. Se tudo correr bem, as verificações do projeto para o único aplicativo Astro serão aprovadas. Isso pode levar alguns minutos.
9. **Opcional:** teste manualmente os recursos de acessibilidade no Codespace. Abra o terminal e execute os comandos a seguir, substituindo `<branch-name>` pelo nome da branch criada pelo Copilot:

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

   Inicie o aplicativo e teste os modos de alto contraste e claro no navegador. Confirme se as preferências persistem após recarregar a página.

O agente personalizado de acessibilidade ajudou a orientar a implementação segundo práticas recomendadas. Se encontrar problemas ou melhorias de acessibilidade, mencione **@copilot** em um comentário e peça as alterações.

## Exercício opcional — continuar delegando

O agente de nuvem é mais útil quando você pode atribuir itens reais do backlog e se dedicar a outras atividades. Para praticar, crie algumas issues no repositório e atribua-as ao Copilot. Algumas ideias:

- criar um formulário de interesse para pessoas apoiadoras na página de detalhes do jogo.
- implementar paginação na lista de jogos.
- adicionar validação de entrada e tratamento de erros aos helpers de acesso a dados.

## Resumo

Você concluiu o percurso do agente de nuvem. Nestas lições, você:

- **Examinou os arquivos de instruções personalizados** do repositório para observar o efeito deles no trabalho do agente.
- **Atribuiu issues ao agente de nuvem do Copilot** e acompanhou a configuração, o planejamento e a execução assíncrona.
- **Criou e usou um agente personalizado** de acessibilidade para adicionar os modos de alto contraste e claro.
- **Usou a página de agentes do Copilot como painel central** para monitorar e orientar a sessão de acessibilidade.
- **Revisou e refinou os pull requests do agente de nuvem**, mencionando `@copilot` para solicitar alterações e aprovando fluxos de trabalho.

## Revisão e próximos passos

Você concluiu o percurso do agente de nuvem. Para continuar explorando, experimente os outros percursos:

- 🖥️ **[Percurso do VS Code](../../vscode/)** — explore o modo de agente do Copilot Chat e a integração com MCP no IDE.
- 💻 **[Percurso da CLI](../../cli/)** — entregue duas alterações revisadas pelo terminal com os modos Plan e Autopilot, skills de agente, agente personalizado de QA, Playwright MCP, Agent Merge e a integração opcional `/delegate` com o agente de nuvem.

No seu repositório, experimente também:

- atribuir ao agente de nuvem uma issue de refatoração ou de cobertura de testes.
- criar um agente personalizado para outra área.
- configurar `copilot-setup-steps.yml` para outra pilha de tecnologias.

## Recursos

- [GitHub Copilot][github-copilot]
- [Sobre os agentes do Copilot][copilot-agents]
- [Atribuir issues do GitHub ao Copilot][assign-issue]
- [Práticas recomendadas para o fluxo de configuração do agente de nuvem][cloud-agent-best-practices]
- [Configurar o firewall do agente de nuvem do Copilot][agent-firewall]

---

| [← Lição anterior: Gerenciar agentes][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
