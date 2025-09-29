# Conventional Commits

# Site

[Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)

## Tipos mais usados

| Prefixo       | Quando usar                                                                                                                             | Impacto no versionamento semântico              |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------- |
| **feat:**     | Quando você **adiciona uma nova funcionalidade**. <br>Ex.: `feat: adicionar autenticação com Google`                                    | **Minor** → `1.2.3 → 1.3.0`                     |
| **fix:**      | Quando você **corrige um bug**. <br>Ex.: `fix: corrigir erro no cálculo de desconto`                                                    | **Patch** → `1.2.3 → 1.2.4`                     |
| **docs:**     | Alterações **somente na documentação**. <br>Ex.: `docs: atualizar README com instruções de instalação`                                  | Nenhum impacto na versão                        |
| **style:**    | Mudanças **visuais ou de formatação do código**, sem alterar a lógica. <br>Ex.: `style: aplicar prettier no projeto`                    | Nenhum impacto                                  |
| **refactor:** | **Refatoração** de código, sem mudar comportamento. <br>Ex.: `refactor: melhorar estrutura do serviço de pagamento`                     | Nenhum impacto direto                           |
| **perf:**     | Melhorias de **performance**. <br>Ex.: `perf: otimizar algoritmo de busca`                                                              | Patch (se corrigir algo), caso contrário nenhum |
| **test:**     | Alterações em **testes**, sem afetar o código de produção. <br>Ex.: `test: adicionar testes unitários para UserService`                 | Nenhum impacto                                  |
| **build:**    | Mudanças em **builds, dependências ou ferramentas**. <br>Ex.: `build: atualizar versão do webpack`                                      | Nenhum impacto                                  |
| **ci:**       | Alterações em **pipelines de integração contínua** (GitHub Actions, GitLab CI, etc). <br>Ex.: `ci: corrigir script de deploy`           | Nenhum impacto                                  |
| **chore:**    | Alterações gerais que **não se encaixam nos outros tipos**, como atualizar pacotes. <br>Ex.: `chore: atualizar dependências do projeto` | Nenhum impacto                                  |
| **revert:**   | Para **reverter** um commit anterior. <br>Ex.: `revert: remover funcionalidade de login social`                                         | Segue a regra do commit revertido               |

## Resumo rápido

| Tipo          | Quando usar                           |
| ------------- | ------------------------------------- |
| **feat:**     | Nova funcionalidade                   |
| **fix:**      | Correção de bug                       |
| **docs:**     | Apenas documentação                   |
| **style:**    | Mudança visual ou formatação          |
| **refactor:** | Refatoração sem alterar comportamento |
| **perf:**     | Melhorias de performance              |
| **test:**     | Alterações em testes                  |
| **build:**    | Alterações em ferramentas ou builds   |
| **ci:**       | Scripts de CI/CD                      |
| **chore:**    | Tarefas gerais                        |
| **revert:**   | Reversão de commits                   |
