---
layout: post
title: "Como atualizar repositórios forcados a partir do original"
date: 2018-05-14 23:00:00 -0300
categories: Git
author_url: "https://medium.com/@erikfigueiredo"
description: "Desenvolvedor full-stack, músico e professor apaixonado por código e nerdices de todos os tipos..."
author:
  name: "Erik Figueiredo"
  email: "erik.figueiredo@gmail.com"
  author_url: "https://www.erikfigueiredo.com.br"
twitter: "https://twitter.com/erikfig"
facebook: https://www.facebook.com/erikfigueiredods"
telegram: "https://t.me/erikfigueiredods"
gravatar: true
---

Eu adoro GitHub, sério!

Muita gente acaba preferindo GitLab, BitBucket (aquele ipelines é monstrão), mas cara o GitHub tem algo que me cativa, não sei bem o que é, talvez aquele feed na página inicial que me mostra as atividades das pessoas que eu decido seguir, sinceramente não sei, só sei que gosto.

O grande ponto aqui é que eu tinha um problema sério com o GitHub, eu odiava ter que apagar um repositório que forkei (essa palavra existe? eu vou usar, me processem) para forkar de novo e pegar atualizado, nossa como eu era noob. Tinha que ter um jeito mais simples, sem eu precisar ficar fazendo gambiarras dignas do "Batman e Robin" do George Clooney. E tem!

Antes de qualquer coisa, tudo que eu disse acima é mentira, eu não apagava os repositórios pra forkar de novo e adoro o Batman com mamilos do George.

## Criando um novo remote com o repositório original

O primeiro passo para atualizar seu repositório forkado é você ter um remote nele apontando para o original, isso demanda que você tenha o seu fork clonado no seu computador para rodar o comando a seguir:

```
git remote add upstream [repositorio-original]
```

Não esqueça de trocar `[repositorio-original]` pelo link do repositório, pode ser o SSH se você tiver as chaves configuradas ou o HTTPS.

O repositório pode ter outro nome diferente de `upstream`, mas vou deixar esse por convenção.

## Reescrita do histórico de commits

Agora precisamos reescrever nossos commits com o histórico original, mas antes precisamos buscar todas as novas atualizações desde que você clonou o projeto. Fazemos assim:

```
git fetch upstream
```

E então nos certificamos que estamos no repositório certo e reescrevemos as alterações garantindo que qualquer commit nosso esteja na lista de commits.

```
git checkout master
git rebase upstream/master
```

Pronto, agora é só publicar no seu repositório remoto:

```
git push origin master
```

## Resumo

Para atualizar seu repositório apenas adicione o repositório original para um remote chamado upstream:

```
git remote add upstream [repositorio-original]
```

E atualize:

```
git fetch upstream
git checkout master
git rebase upstream/master
```

Observação: A dica funciona com outros servidores além do GitHub.

Observação 2: Batman e Robin ainda passa no SpaceHD (510 na Sky).
