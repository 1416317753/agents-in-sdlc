---
title: "Lição 5 - Personalizar e usar a skill quality-checks"
description: "Explore a skill quality-checks existente, personalize o formato do relatório e use-a para verificar a filtragem."
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

Desenvolver software envolve mais do que escrever código. Você já verificou manualmente se o código funciona e usou arquivos de instruções para garantir que ele siga os padrões da equipe. Mas e os testes, o lint e as outras partes da integração contínua (CI)?

Para essas tarefas, as **skills de agente** são ideais. Elas ajudam o Copilot a executar operações de forma consistente.

Nesta lição, você vai:

- explorar a skill `quality-checks` existente.
- personalizar o formato dos resultados.
- recarregar e executar a skill.

## Cenário

A Tailspin Toys usa a skill `quality-checks` para testes unitários, lint e verificações de tipos. A equipe quer melhorar o relatório para facilitar a leitura dos resultados.

## Instruções, scripts e recursos

As skills de agente reúnem instruções reutilizáveis, scripts executáveis e recursos de apoio que um agente carrega quando necessário. Em geral, uma skill é uma pasta com um arquivo Markdown `SKILL.md`. O arquivo inclui frontmatter com `name` e `description`, uma visão geral da tarefa e orientações sobre quando chamar a skill. A pasta também pode conter scripts e outros recursos.

> [!NOTE]
> Uma skill não precisa de arquivos ou pastas adicionais. A skill `quality-checks` da Tailspin Toys contém somente `SKILL.md`, pois usa os comandos existentes do projeto.

As skills podem ficar na pasta `.github/skills` do projeto para serem compartilhadas pela equipe ou na pasta pessoal `~/.copilot/skills`.

## Explorar a skill

Examine a skill `quality-checks`, criada pela equipe Tailspin Toys para executar testes unitários, lint e verificações de tipos.

1. Volte ao Codespace. No editor do Codespaces, abra `.github/skills/quality-checks/SKILL.md`.
2. Leia `name` e `description` no início do arquivo. A descrição ajuda o Copilot a decidir quando chamar a skill.
3. Leia as instruções e observe como orientam o Copilot nas etapas de teste e lint.
4. Observe que a skill ainda não inclui uma seção **Formato dos resultados**.

## Executar a skill antes da alteração

Você pode chamar skills diretamente no Copilot CLI ou usar linguagem natural. Peça ao Copilot que execute as três verificações da skill.

1. Volte à conversa de filtragem no modo Interactive.
2. Use este prompt:

   ```plaintext
   Run the quality-checks skill for unit tests, lint, and type checks.
   ```

3. Observe o relatório final.

## Personalizar o relatório

O relatório deve indicar o que foi executado, se cada verificação foi aprovada e o que as ferramentas informaram. Atualize a skill para produzir esse formato.

1. Volte a `.github/skills/quality-checks/SKILL.md`.
2. Adicione esta seção ao final do arquivo:

   ```markdown
   ## Formato dos resultados

   Ao concluir, informe cada comando executado e se foi aprovado, falhou ou ficou bloqueado. Inclua contagens de testes, durações, erros, avisos e outras métricas somente quando a ferramenta as informar. Indique a próxima ação para cada falha ou bloqueio e nunca descreva uma verificação ignorada ou incompleta como aprovada.
   ```

3. O arquivo será salvo automaticamente.

## Executar a skill atualizada

O Copilot CLI pode recarregar skills editadas sem reiniciar a conversa.

1. Digite:

   ```plaintext
   /skills reload
   ```

2. Use exatamente o mesmo prompt:

   ```plaintext
   Run the quality-checks skill for unit tests, lint, and type checks.
   ```

3. Observe o relatório final e compare-o com o primeiro.

## Resumo e próximos passos

Você personalizou e usou uma skill de agente existente. Nesta lição, você:

- explorou a skill `quality-checks` para testes unitários, lint e verificações de tipos.
- personalizou o formato dos resultados.
- recarregou e executou a skill.

Essa alteração acompanhará a filtragem no PR do recurso. Em seguida, permita que o Copilot interaja diretamente com o site [por meio do servidor MCP do Playwright][next-lesson].

## Mais exemplos de skills

Estes exemplos da comunidade servem como referência, não como tarefas adicionais:

- [Especificação de Agent Skills][skill-spec]
- [Fluxo de contribuição: `make-repo-contribution`][contribution-example]
- [Documentos de requisitos: `prd`][prd-example]
- [Diagramas e script de exportação: `drawio`][drawio-example]
- [Testes no navegador: `webapp-testing`][browser-example]

[previous-lesson]: ../4-custom-instructions/
[next-lesson]: ../6-mcp-playwright/
[skill-spec]: https://agentskills.io/specification
[contribution-example]: https://github.com/github/awesome-copilot/tree/main/skills/make-repo-contribution
[prd-example]: https://github.com/github/awesome-copilot/tree/main/skills/prd
[drawio-example]: https://github.com/github/awesome-copilot/tree/main/skills/drawio
[browser-example]: https://github.com/github/awesome-copilot/tree/main/skills/webapp-testing
