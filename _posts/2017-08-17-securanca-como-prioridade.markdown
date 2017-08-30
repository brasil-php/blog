---
layout: post
title:  "Segurança como prioridade"
date:   2017-08-17 21:46:00 -0300
categories: securança
author: 
  name: "William Correa"
  email: "wilcorrea@gmail.com"
  author_url: "https://wilcorrea.rocks"
twitter: "https://twitter.com/wilcorrea"
facebook: "https://fb.com/wilcorrea.rocks"
telegram: "https://t.me/wilcorrea"
gravatar: true
---
O assunto desse post é simples, muito simples. É dar um recado na verdade.

> Cuide do seu projeto antes que seja tarde demais

Indiferente a forma com que você escreve seu código ou crie o seu projeto, se usa orientaço a objeto, programação funcional, procedural, nada disso, ou se você faz testes, TDD, BDD, independente de SOLID, DRY, ou qualquer outro acrônimo, até de PSR... tem uma única coisa que TODOS, eu disse, invariavelmente TODOS os programadores (principalmente os da web) precisam ter como premissa básica: SEGURANÇA.

Sim, um mínimo de SEGURANÇA, é só isso que peço!

Se até ontem você fazia `$_POST['var']` você vai parar de fazer isso e vai criar uma função como essa abaixo
```php
function post($index, $filter = FILTER_DEFAULT)
{
    return filter_input(INPUT_POST, $index, $filter);
}
```

Se até agora a pouco você fazia `"SELECT * FROM posts WHERE id = $_GET['id']"` você vai apagar isso e vai usar no mínimo `"SELECT * FROM posts WHERE id = '" . get('id', FILTER_SANITIZE_NUMBER_INT) . "'"` usando
```php
function get($index, $filter = FILTER_DEFAULT)
{
    return filter_input(INPUT_GET, $index, $filter);
}
```

Nossa, mas de onde você está tirando esses filtros? Daqui ó [http://php.net/filters](http://php.net/manual/en/filter.filters.php)

Obviamente que seria possível melhorar outras coisas, mas vamos começando por ai; é o suficiente por agora.

# Ah, não preciso de nada disso, porque eu uso um framework!

Sério?! Então você acha que de forma milagrosa o fato de você estar usando um framework resolve todos os seus problemas?!

![.env](https://snag.gy/r4aBiE.jpg)

Essa imagem acima é de um site em produção feito em Laravel. Note que não é culpa da ferramenta porque a forma com que ela foi usada está INAPROPRIADA. Sabe aquele caso de usar um serrote para bater um prego numa madeira?

Quando for trabalhar em uma tecnologia, esqueça atalhos, use conteúdos de blogs com moderação, procure ajuda de pessoas que já tenham usado as ferramentas em que você está indo atuar. Estude com dedicação e siga firme estabelecendo metas e arquitetando sua carreira.

Do ponto de vista técnico, cheque TODOS os arquivos envolvidos no seu projeto, mesmo o de terceiros... Se não puder verificar o de terceiros, repense o seu uso. Deixe com acesso externo APENAS o que você tem CERTEZA que não expôe nenhum dado sensível da sua aplicação e que não pode ser executado.

O recado está dado! Nunca se esqueça que programar é pensar! Abraços e até a próxima.
