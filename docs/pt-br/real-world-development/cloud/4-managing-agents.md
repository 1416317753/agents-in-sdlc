---
title: "Exercício 4 - Monitorar e gerenciar agentes"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

Nos últimos exercícios, você atribuiu três tarefas diferentes ao agente de nuvem do Copilot para melhorar a experiência e adicionar funcionalidades. Embora o agente trabalhe de forma assíncrona e autônoma, ainda é importante acompanhar essas tarefas.

Há várias ferramentas para gerenciar as tarefas atribuídas ao agente de nuvem, incluindo a [página de agentes][agents-page] do GitHub.com. Nesse painel central, você pode ver as tarefas de agentes com pull requests (PRs) abertos, explorar as operações realizadas e orientar uma sessão em andamento.

Nesta lição, você vai:

- explorar a página de agentes para acompanhar as tarefas do agente de nuvem.
- orientar uma sessão em andamento para solicitar funcionalidade adicional.

## Cenário

Depois que você atribuiu ao agente o modo de alto contraste, a equipe decidiu que também seria útil adicionar um modo claro. Como o trabalho já envolve atualizar o estilo do site e adicionar alternância de temas, faz sentido incluir esse recurso. Oriente o agente para adicionar também o modo claro.

## Revisar as tarefas do agente de nuvem do Copilot

Veja o estado atual das tarefas atribuídas ao agente de nuvem do Copilot.

1. Acesse a [página de agentes do Copilot](https://github.com/copilot/agents).
2. Examine as tarefas no painel principal e no painel esquerdo. Você deverá ver, entre outras:
   - atualização da documentação do código.
   - geração de APIs para modificar jogos.
   - adição do modo de alto contraste ao site.
3. Selecione uma tarefa em andamento e revise as operações do Copilot, que podem incluir:
   - obter o código do repositório.
   - configurar o ambiente de trabalho.
   - configurar servidores MCP.
   - executar etapas para concluir a tarefa.

> [!NOTE]
> As etapas exibidas variam de acordo com o estado do trabalho e a abordagem do Copilot.

4. Examine também o painel do pull request à direita, que mostra o PR e os arquivos alterados.

## Orientar o agente de nuvem

Agora que você conhece as tarefas em andamento, peça ao Copilot que adicione o modo claro enquanto trabalha no modo de alto contraste.

1. Selecione a sessão de adição do modo de alto contraste. O título exato varia de acordo com o nome que o Copilot atribuiu e com o estado do trabalho.

    ![Sessão de acessibilidade no painel de agentes](../../../_images/ex6-accessibility-session.png)

2. Acompanhe a sessão por alguns minutos, até que a configuração seja concluída e o trabalho comece. Você verá mensagens de progresso semelhantes às do exemplo.
3. Na caixa de diálogo **Steer active session while Copilot is working**, envie este prompt:

   ```plaintext
   While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
   ```

    ![Tarefa do agente de nuvem na página de agentes, com a caixa de diálogo Steer active session while Copilot is working destacada](../../../_images/ex6-steer-cloud-agent-task.png)

4. Pressione <kbd>Enter</kbd> para enviar o prompt.
5. Observe como o Copilot confirma o recebimento e inclui a solicitação no trabalho.

## Deixar o Copilot trabalhar

O Copilot continuará com a tarefa atualizada. Ele incorporará a nova solicitação depois de concluir a etapa em andamento quando você enviou a mensagem.

Isso pode levar alguns minutos. Aproveite para fazer uma pausa e refletir sobre o que aprendeu até aqui.

## Resumo e próximos passos

Neste exercício, você explorou a página de agentes do Copilot, o painel central para monitorar e orientar tarefas do agente de nuvem do GitHub Copilot. Você pode acompanhar tarefas ativas e concluídas, revisar o trabalho e redirecionar uma tarefa em andamento para ajustar o escopo ou fornecer mais orientações.

Você explorou:

- o painel de agentes para monitorar tarefas do agente de nuvem.
- como redirecionar uma sessão em andamento para solicitar funcionalidade adicional.

Enquanto o Copilot conclui as alterações de acessibilidade, você pode [iterar sobre os pull requests criados pelo Copilot][next-lesson].

## Recursos

- [Página de agentes do Copilot][agents-page]
- [Sobre agentes personalizados][custom-agents]

---

| [← Lição anterior: Agentes personalizados][previous-lesson] | [Próxima lição: Iterar sobre o trabalho do Copilot →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
