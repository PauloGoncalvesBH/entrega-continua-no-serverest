---
title: "Entrega contínua no ServeRest 🚀"
published: true
description: "Nesse texto irei relatar os ganhos e a implementação da tão desejada Entrega Contínua no SerVerest."
tags: continuousdelivery, serverest, ptbr
canonical_url:
cover_image:
series:
---

<!--- Acima está o cabeçalho, com as informações do seu post, edite-as de acordo com sua necessidade. Para saber mais: https://dev.to/p/editor_guide
Apague tudo que está abaixo dessa linha e escreva seu post --->

## Sumário
- [❓ O que é entrega contínua (Continuous delivery)?](#-o-que-é-entrega-contínua-continuous-delivery)
- [❓ O que é o _ServeRest_?](#-o-que-é-o-serverest)
- [🏁 Resultado](#-resultado)
- [🔨 Desafios](#-desafios)
- [📁 Libs NPM utilizadas](#-libs-npm-utilizadas)
- [📑 Fluxo de trabalho](#-fluxo-de-trabalho)
  - [1️⃣ Issue](#1%EF%B8%8F⃣-issue)
  - [2️⃣ Desenvolvimento local](#2%EF%B8%8F⃣-desenvolvimento-local)
  - [3️⃣ Pull Request](#3%EF%B8%8F⃣-pull-request)
  - [4️⃣ Criação da release](#4%EF%B8%8F⃣-criação-da-release)
  - [5️⃣ Comunicação de release gerada](#5%EF%B8%8F⃣-comunicação-de-release-gerada)

---

Nesse texto vou relatar todas as etapas necessárias para a implementação da  _entrega contínua_ no _ServeRest_, as vantagens dela e porque não é apenas inserir `npm publish` na pipeline do projeto, mas sendo um conjunto de boas práticas.

> Não é possível entregar código de qualidade sem um bom processo de desenvolvimento 

**⛔ O QUE NÃO IREI ABORDAR:** _Detalhes de implementação o suficiente para configurar a entrega contínua no seu projeto. Porém é possível utilizar as informações passadas para tal fim, já que é um agregado de informações coletadas e que deram certo._

Antes de abordarmos a entrega contínua aplicada no _ServeRest_, temos que entender 2 coisas:

##### ❓ O que é entrega contínua (Continuous delivery)?

A definição de entrega contínua fornecida pelo continuousdelivery.com é:

> Entrega Contínua é a capacidade de obter alterações de todos os tipos - incluindo novos recursos, alterações de configuração, correções de bugs e experimentos - em produção ou nas mãos dos usuários, com **segurança e rapidez**, de maneira sustentável.

##### ❓ O que é o _ServeRest_?

O [ServeRest](https://www.npmjs.com/package/serverest) é um projeto NPM open source de ensino de testes de API para QAs. Ele fornece APIs Rest de forma fácil e bem documentada para que seja possível testar os verbos GET, POST, DELETE e PUT, autenticação e segurança de API.

Sendo o _ServeRest_ um projeto com foco em qualidade, não seria menos esperado dele de que possuísse um processo de desenvolvimento de qualidade.

---

## 🏁 Resultado

Antes de passarmos por detalhes da entrega contínua, é importante entendermos o ganho adquirido.

A entrega contínua foi importante para garantir a qualidade de software do _ServeRest_ e rapidez na entrega de novas releases. Com ela apenas código bem estruturado, que passa em todos os testes e possui boa mensagem de commit é integrado ao código. Além de que as releases são geradas apenas se necessário, de acordo com alguns critérios.

Para o mantenedor do projeto, basta realizar code review em PR de outros colaboradores e realizar o merge com a master. Não terá mais a preocupação em atualizar a master local, criar tag, atualizar o changelog, commitar, executar `git pull` e `npm publish`.

O impacto para o usuário é de que correções sejam liberadas de forma mais rápida e, principalmente, bugs em produção são menos frequentes.

---

## 🔨 Desafios

Para entregar o código em produção com rapidez e segurança, alguns itens teriam que ser solucionados:

1. Todos os cenários deveriam ser cobertos por teste;
1. O código deveria seguir o [padrão do JS](https://standardjs.com/);
1. As mensagens de commit deveriam seguir o padrão do [Conventional Commit](https://www.conventionalcommits.org/);
1. Todas as alterações que geraram release deveriam estar documentadas no [Changelog](https://github.com/PauloGoncalvesBH/ServeRest/blob/master/CHANGELOG.md);
1. O versionamento deveria corresponder às alterações desde a última release e ao [Semantic versioning](https://semver.org/);
1. A estratégia de branches deveria permitir integrar código na `master` apenas se todas as validações fossem validadas;
1. Somente deveria ser feita publicação automática caso a alteração realizada necessitasse de publicação.

---

## 📁 Libs NPM utilizadas

Para solucionar todos os desafios listados acima e poder entregar uma release com qualidade e manter o código bem estruturado, foram utilizadas diversas libs NPM em conjunto, cada qual com sua responsabilidade e sendo executadas em etapas diferentes no fluxo de trabalho.

1. [Standard](https://www.npmjs.com/package/standard) - `JavaScript style guide, linter, and formatter`;
1. [Husky](https://www.npmjs.com/package/husky) - Executa ações em git hooks, como `pre-commit` e `commit-msg`;
1. [Commitlint](https://www.npmjs.com/package/@commitlint/cli) - Valida se a mensagem de commit segue o conventional commits;
1. [Supertest](https://www.npmjs.com/package/supertest) - Testes de APIs Rest;
1. [Semantic-release/commit-analyzer](https://www.npmjs.com/package/@semantic-release/commit-analyzer) - Analisa as mensagens de commit de acordo com o [conventional-changelog](https://github.com/conventional-changelog/conventional-changelog) e informa qual tipo de versão deve ser gerada (`major`, `minor` ou `patch`);
1. [Semantic-release/release-notes-generator](https://www.npmjs.com/package/@semantic-release/release-notes-generator) - Gera as notas da release;
1. [Semantic-release/changelog](https://www.npmjs.com/package/@semantic-release/changelog) - Gera/atualiza o changelog com as notas da release;
1. [Semantic-release/npm](https://www.npmjs.com/package/@semantic-release/npm) - Atualiza a versão do projeto e publica o pacote NPM;
1. [Semantic-release/git](https://www.npmjs.com/package/@semantic-release/git) - Commita o `package.json`, `package-lock.json` e o `CHANGELOG.md`;
1. [Semantic-release/github](https://www.npmjs.com/package/@semantic-release/github) - Publica uma release no Github com as notas da release.

> Para mais detalhes visualize a seção _devDependencies_ do [package.json](https://github.com/PauloGoncalvesBH/ServeRest/blob/master/package.json)

---

## 📑 Fluxo de trabalho

Abaixo será relatado todo o fluxo de trabalho, desde o momento que um bug é relatado até o momento que a correção está publicada em nova release.

### 1️⃣ Issue

Um usuário abre uma nova [issue](https://github.com/PauloGoncalvesBH/ServeRest/issues) no projeto, relatando um bug e seguindo o template de issue.

---

### 2️⃣ Desenvolvimento local

Libs: `husky`, `commitlint` e `standard`.

1. O desenvolvedor irá atuar em uma branch a partir da _master_ (seguindo o _Github Flow_).
1. Serão feito os ajustes afim de corrigir o bug, e então será realizado o commit. Nesse momento será utilizado a lib `husky`.
    1. _pre-commit_: Será executado o `standard`, validando se o código respeita ao padrão de código do JS.
    1. _commit-msg_: Será executado o `commitlint`, validando se a mensagem de commit segue o conventional commit.

Caso alguma validação encontre problema, o commit será abortado, e então o dev terá que ajustar o código ou a mensagem de commit. Com isso todo commit realizado estará bem estruturado.

---

### 3️⃣ Pull Request

[![Continuous Integration](https://github.com/PauloGoncalvesBH/serverest/workflows/Continuous%20Integration/badge.svg)](https://github.com/PauloGoncalvesBH/ServeRest/actions)

Libs: `supertest`, `commitlint` e `standard`

O desenvolvedor irá abrir no Github uma Pull Request para integrar a sua branch com a _master_. O PR somente será mergeado caso as seguintes etapas sejam aprovadas:

1. Code review de outro desenvolvedor;
1. Validação dos testes de API criados com `supertest`. Executado na pipeline de integração contínua;
1. Validação da mensagem de commit com `commitlint`. Executado na pipeline de integração contínua;
1. Validação da estrutura do código com `standard`. Executado na pipeline de integração contínua.

**Print de validações feitas em um Pull Request:**
![](https://raw.githubusercontent.com/PauloGoncalvesBH/entrega-continua-no-serverest/master/images/ChecksPR.png)

---

### 4️⃣ Criação da release

[![Continuous Delivery](https://github.com/PauloGoncalvesBH/ServeRest/workflows/Continuous%20Delivery/badge.svg)](https://github.com/PauloGoncalvesBH/ServeRest/actions)

Lib: `semantic-release/*`

Após o PR ser aprovado e integrado com a _master_, começa todo o processo de entrega contínua.

Inicialmente são executados as mesmas validações feitas na PR (_commitlint_, _standard_ e _testes de API_). Apenas com a execução com sucesso de todas essas validações é que é iniciado o processo de entrega contínua.

**Print da pipeline de Continuous Delivery:**
![](https://raw.githubusercontent.com/PauloGoncalvesBH/entrega-continua-no-serverest/master/images/CDGithubActions.png)

Na atividade _release_ é executado o `semantic-release`, que utiliza a configuração implementada e realiza as tarefas de entrega contínua.

Inicialmente é validado se há commit, desde a última git tag, que gera uma nova release `major`, `minor` ou `patch`. Caso esse critério seja atendido as seguintes ações são realizadas:

1. A versão do _package.json_ e do _package-lock.json_ são atualizadas;
1. É gerada as notas de release contendo informação dos últimos commits;
1. O _CHANGELOG_ é atualizado com as notas de release;
1. É realizado publicação no _NPM_, e o artefato é armazenado temporariamente;
1. _package.json_, _package-lock.json_ e _CHANGELOG_ são commitados com nova tag e informação da nova release. É utilizado o usuário `semantic-release-bot`;
1. É criado nova release no Github utilizando a tag gerada, o artefato armazenado temporariamente e as notas de release;
1. É feita comunicação aos interessados sobre a nova release. Veja mais na seção [Comunicação de release gerada](#comunicacao-de-release-gerada).

**Print do commit do `semantic-release`:**
![](https://raw.githubusercontent.com/PauloGoncalvesBH/entrega-continua-no-serverest/master/images/commitGithub.png)

**Print da release gerada no github:**
![](https://raw.githubusercontent.com/PauloGoncalvesBH/entrega-continua-no-serverest/master/images/releaseGithub.png)

> Caso queira verificar com mais detalhes a configuração feita quanto ao `semantic-release`, verifique o arquivo [.releaserc.js](https://github.com/PauloGoncalvesBH/ServeRest/blob/master/.releaserc.js) e o [workflow de continuous delivery](https://github.com/PauloGoncalvesBH/ServeRest/blob/master/.github/workflows/continuous_delivery.yml) (a partir da linha 53).

---

### 5️⃣ Comunicação de release gerada

Lib: `semantic-release`

Após a publicação da release em produção é preciso comunicar aos interessados de que suas respectivas issues e PR foram corrigidas.

O `semantic-release` verifica quais issues foram fechadas e quais PRs foram aprovados e que fazem parte da nova release. Com isso ele adiciona comentário informando de que uma nova release está disponível com a solução para a situação relatada e adiciona a label _released_.

**Print de comentário e label automático em PR:**
![](https://raw.githubusercontent.com/PauloGoncalvesBH/entrega-continua-no-serverest/master/images/comentarioPR.png)

---

Com isso passamos por todas as etapas da entrega contínua no contexto do _ServeRest_. Caso tenha dúvidas ou sugestões, deixe um comentário abaixo. Não deixe de visitar o repositório do [ServeRest](https://github.com/PauloGoncalvesBH/serverest) e deixar a sua estrelinha ⭐.

---

###### _Esse post está sendo versionado e hospedado no [Github](https://github.com/PauloGoncalvesBH/entrega-continua-no-serverest)_
