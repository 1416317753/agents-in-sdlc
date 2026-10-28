---
title: "Exercício 5 - Monitorar e gerenciar agentes"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Lição anterior: Agentes personalizados][previous-lesson] |
|:--|

No modo de agente, o GitHub Copilot trabalha de forma autônoma: explora o código, propõe alterações, edita arquivos e executa comandos. Como esse trabalho ocorre em tempo real na sua máquina, o Copilot Chat no VS Code mostra cada chamada de ferramenta e edição de arquivo. Revise cada diff no chat, aceite ou rejeite alterações individuais e envie prompts de acompanhamento para orientar ou ampliar o trabalho sem sair do editor.

Neste exercício, você vai:

- acompanhar em tempo real o trabalho do agente de acessibilidade.
- revisar e aceitar as edições propostas.
- orientar o agente durante a sessão para adicionar um seletor de modo claro junto ao modo de alto contraste.

## Cenário

O agente personalizado de acessibilidade usado no [Exercício 4][previous-lesson] está implementando o modo de alto contraste. Durante o trabalho, a equipe de design pediu também um seletor de modo claro para quem considera o tema padrão escuro demais. Em vez de começar uma nova conversa, oriente a sessão existente para ampliar o trabalho.

## Acompanhar o progresso do Copilot

A visualização Copilot Chat mostra cada etapa do agente: os arquivos que explora, os comandos que executa e as alterações propostas. Cada edição aparece no chat com um diff e os controles **Keep** e **Undo**.

1. Volte à visualização Copilot Chat do Exercício 4.
2. Enquanto o agente trabalha, role a conversa para acompanhar as chamadas de ferramentas. Você verá leituras e edições de arquivos e comandos de terminal que ele solicita executar.
3. Quando o agente propuser uma edição:
   - selecione **Keep** para aceitar a alteração.
   - selecione **Undo** para revertê-la e permitir que o agente tente novamente.
4. Para ver todos os arquivos alterados na sessão, abra o indicador **Files changed** na parte superior do chat.

> [!NOTE]
> Se uma chamada de ferramenta pedir permissão, por exemplo, para executar um comando de terminal, o agente pausará e mostrará a opção **Allow**. Aprove somente os comandos que você considerar seguros.

5. Aguarde o agente concluir o modo de alto contraste. Ele apresentará uma mensagem final com o resumo das alterações.

## Orientar o agente para adicionar o modo claro

Agora que o modo de alto contraste está implementado, amplie a mesma conversa para adicionar um seletor de modo claro. Como você continuará na mesma conversa, o agente manterá o contexto dos componentes alterados, da abordagem de estilo e do padrão de armazenamento local usado.

1. No campo de entrada do Copilot Chat, envie este prompt:

   ```plaintext
   Nice work! Now add a light-mode toggle alongside the high-contrast toggle. Light mode should follow the same pattern — persist via local storage and be accessible from the same area of the UI. The user should be able to enable high-contrast and light mode independently.
   ```

2. O agente continuará o trabalho, examinará as alterações já feitas e proporá mudanças para o modo claro.
3. Revise cada alteração e selecione **Keep** ou **Undo** no diff.
4. Se o agente seguir uma direção indesejada — por exemplo, refatorar os seletores de uma forma que você não quer —, interrompa-o com o botão **Stop** ao lado do campo de entrada e envie uma mensagem para esclarecer a solicitação.

> [!TIP]
> Orientar uma sessão em andamento é mais rápido do que recomeçar. Use prompts de acompanhamento para pedir ajustes, corrigir o rumo ou ampliar o escopo. Por exemplo: “simplifique o CSS usando variáveis”, “não adicione um terceiro seletor; integre o modo claro ao painel de configurações existente” ou “adicione também atalhos de teclado para os dois seletores”.

## Revisar o conjunto de alterações

Antes de fazer commit, revise rapidamente tudo que o agente alterou.

1. Abra a visualização **Source Control** no VS Code.
2. Examine a lista completa de arquivos alterados. Ela deve incluir componentes Astro, estilos e testes relacionados.
3. Abra alguns arquivos e revise os diffs. Confirme se a implementação segue os padrões de acessibilidade do agente personalizado: atributos ARIA, navegação por teclado, HTML semântico e persistência com armazenamento local.
4. Quando estiver satisfeito, prepare e faça commit das alterações no painel Source Control. Você as publicará na [próxima lição][next-lesson].

## Resumo e próximos passos

Você usou o modo de agente do Copilot Chat para acompanhar e orientar uma sessão em andamento: revisou cada edição, aceitou ou rejeitou alterações e enviou prompts de acompanhamento sem perder o contexto.

Neste exercício, você:

- acompanhou o trabalho do agente em tempo real.
- aceitou e rejeitou edições individuais.
- orientou a sessão com prompts de acompanhamento.

Na [próxima lição][next-lesson], você publicará o trabalho de acessibilidade em um pull request e revisará as alterações feitas pelo agente personalizado local durante o laboratório.

## Recursos

- [Copilot Chat no VS Code][copilot-chat-vscode]
- [Usar o modo de agente][agent-mode]

---

| [← Lição anterior: Agentes personalizados][previous-lesson] | [Próxima lição: Iterar sobre o trabalho do Copilot →][next-lesson] |
|:--|--:|

[previous-lesson]: ../4-custom-agents/
[next-lesson]: ../6-iterating/
[copilot-chat-vscode]: https://code.visualstudio.com/docs/copilot/chat/copilot-chat
[agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
