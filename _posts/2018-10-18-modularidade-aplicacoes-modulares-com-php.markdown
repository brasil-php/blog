---
layout: post
title: "Modularidade: Aplicações modulares com PHP"
date: 2018-10-18 17:00:00 -0300
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

O sonho máximo de todo desenvolvedor é chegar em um nível em que o trabalho é tão pouco repetitivo e produtivo quanto instalar biblioteca de terceiros e configurar, e além de possível, também tem nome, se chama modularidade.

Segundo a Wikipedia:

>Modularização em tecnologia da informação é um conceito onde o sistema ou software é dividido em partes distintas. Compõe o ferramental necessário para um programa mais legível com uma melhor manutenção e melhor desempenho por meio da programação estruturada.

>Pode ser caracterizado da seguinte forma: Elemento separadamente endereçável do sistema, menor parte do sistema que realiza uma função completa independente de outras funções, conjunto de instruções de um programa que pode ser chamado por um nome, sendo ideal que para os outros módulos seja uma caixa preta.

Fonte: [https://pt.wikipedia.org/wiki/Modularidade](https://pt.wikipedia.org/wiki/Modularidade)

Criar aplicações modulares é uma realidade presente para todos nós e já é nativa nos principais frameworks do mercado a muito tempo. No começo assusta, é preciso estruturar as coisas, saber onde cada feature se encaixa, muitos se enfiam de cabeça no DDD, mas eu adianto que modularidade é simples, um pouco de organização, algumas boas práticas e em pouco tempo você vai estar baixando um módulo que já está com toda a lógica, tabelas para o banco de dados e templates de um sistema completo de usuários, com permissões, administração, autenticação com as mais diversas redes sociais, lembrete de senha e tudo o que você tem direito, e ai meu irmão/irmã, é só configurar e usar.

## Composer é realmente necessário?

Sim!!! Mas claro que é!

Composer foi uma das melhores coisas que aconteceu com o PHP, com ele você instala bibliotecas externas com extrema facilidade, atualiza, registra seus autoloads, inclui scripts... **Não usar** é loucura.

A grande questão é "como devo lidar com os arquivos de configuração do Composer dentro dos módulos enquanto eu estou desenvolvendo?"

Uma das bibliotecas mais legais que usei até agora foi o [wikimedia/composer-merge-plugin](https://github.com/wikimedia/composer-merge-plugin), que inclusive, tem uma [versão para o Laravel](https://github.com/wikimedia/composer-merge-plugin).

Ele lê o conteúdo de arquivos `composer.json` nos módulos e inclui no original do projeto, na verdade ele não altera realmente o seu `composer.json` original, apenas o faz "virtualmente", imagine a estrutura a seguir:

- modules
  - Module1
    - src/*
    - composer.json
  - Module2
    - src/*
    - composer.json
  - Module3
    - src/*
    - composer.json
- src/*
- bootstrap.php
- composer.json
- composer.lock

Basicamente temos diretórios e arquivos, os `*` significam que temos arquivos e diretórios ali dentro que não são importantes para nosso exemplo.

Cada `composer.json` tem suas dependências, autoloads... configurações comuns e que você já deve conhecer, a grande novidade está no arquivo da raiz:

```
{
    "name": "erik_/composer-merge-plugin",
    "authors": [
        {
            "name": "Erik Figueiredo",
            "email": "erik.figueiredo@gmail.com"
        }
    ],
    "require": {
        "wikimedia/composer-merge-plugin": "dev-master"
    },
    "extra": {
        "merge-plugin": {
            "include": [
                "modules/*/composer.json"
            ]
        }
    },
    "autoload": {
        "psr-4": {
            "App\\": "src"
        }
    }
}

```

Para respeitar as regras de copyright e também em respeito que eu devo a todos os envolvidos, este arquivo é parte de um exemplo maior que ministrei em um Direto ao Ponto na School of Net, chamado [PHP e modularização - trabalhando com Composer.json em subdiretórios](https://www.schoolofnet.com/canal-direto-ao-ponto/php-e-modularizacao-trabalhando-com-composerjson-e/). Recado dado, seguimos em frente.

Note que temos informações comuns como nome do pacote, dependências e registro de autoload. O importante é o `extra`, nele eu digo para o `merge-plugin` incluir os `composer.json` dentro do diretório `modules` e o próximo nível dentro dele, quero dizer `modules/Module1/composer.json`, `modules/Module2/composer.json`... O caracter `*` casa qualquer diretório.

Sempre que você atualizar um Composer dentro de um sub diretório, vai precisar atualizar o principal rodando `composer update --lock`.

Agora é só incluir o diretório `modules` no `.gitignore` principal e versionar cada um dos módulos individualmente, muito mais fácil de trabalhar, né?

O [wikimedia/composer-merge-plugin](https://github.com/wikimedia/composer-merge-plugin), faz muito mais do que eu mostrei aqui veja na documentação oficial.

Sério, não sei como eu vivi tanto tempo sem esse cara.

## Packagist versus pacotes privados

O Packagist é outro cara que vale atenção se você for modularizar pacotes Open Source, mas se vai fazer o mesmo com pacotes privados, talvez queira dar uma olhada em outras soluções.

O próprio Composer da suporte a várias formas de se trazer um pacote que não esteja no Packagist, mas a mais interessante pra mim [é o Satis](https://getcomposer.org/doc/articles/handling-private-packages-with-satis.md), ele gera uma lista de repositórios que você pode baixar, facilita porque só precisa de uma única informação no `repositories` do Composer. Com chaves ssh, nem senha para os privados ele vai pedir na sua maquina. Vale a pena conferir e utilizar, uma hora destas eu crio um artigo só sobre o Satis e como ele funciona, então fica ligado aqui no Blog ou no [link oficial](https://getcomposer.org/doc/articles/handling-private-packages-with-satis.md).

Se você ficou interessado sobre como usar o Packagist para pacotes de código aberto de uma olhada neste [video do André Cardoso](https://www.youtube.com/watch?v=VNn3JUPsHLo), "old, but gold!".

Estas são algumas dicas que considero valiosas, se você quiser enriquecer este artigo faça um [fork do blog](https://brasil-php.github.io/blog/como-contribuir/) ou mande nos comentários!

É isso! Até a próxima.

p.s.: Agradecimentos especiais ao [Will Correa](https://github.com/wilcorrea) pela revisão.
