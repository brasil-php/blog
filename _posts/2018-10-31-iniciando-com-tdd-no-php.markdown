---
layout: post
title: "TDD: Iniciando com TDD no PHP"
date: 2018-10-31 21:16:00 -0300
categories: TDD
author_url: "https://medium.com/@eduardofg87"
description: "DevOps e Software Engineer apaixonado por minha família, meus amigos, cães, tecnologia, meu time (GALO!) e MG uai!"
author:
  name: "Eduardo Figueiredo Gonçalves"
  email: "goncalves.ef@gmail.com"
  author_url: "https://www.eduardofg.com"
twitter: "https://twitter.com/eduardofg87"
linkedin: "https://www.linkedin.com/in/eduardofg87"
telegram: "https://t.me/eduardofg87"
gravatar: true
---

## TDD

Estava estudando um pouco sobre TDD esses dias e vi o [artigo](https://code.tutsplus.com/tutorials/lets-tdd-a-simple-app-in-php--net-26186) que me fez pensar em algumas coisas interessantes e resolvi escrever esse post.

Vou fazer algumas colocações sobre o material e mostrar alguns exemplos dele, recomendo darem uma lida no texto antes que eu curti demais. Durante sprints de desenvolvimento em SCRUM há situações que podem ser resumidas à imagem: 

![Persista!](https://snag.gy/WTI4w8.jpg)

Há momentos do dia em que estamos com muito gás e outros que a nossa produtividade se esgota. Desde sistemas de pequena complexidade até em sistemas mais complexos há alguns CRUDs que se repetem como por exemplo CRUD de Autenticação e CRUD de ACL (Controle de Acesso), será que quando formos unir todas os módulos tudo funcionará conforme esperado? Acredito que a grande maioria saiba que as chances de erros inesperados é enorme e para contornarmos esse problema falaremos sobre a técnica de TDD que é uma grande aliada do programador. As regras de TDD são: escreva testes, escreva código, refatore.

TDD do inglês Test Driven Development ou em nossa língua nativa Desenvolvimento Guiado por Testes basicamente consiste em seguir um simples algoritmo de 3 passos para o desenvolvimento de software.

1. Você não tem permissão de escrever um código de produção se não tiver escrito um teste antes.
1. Escreva um teste em que o seu código de produção falhe.
1. Escreva o código de produção que faça seu teste que falhou no passo anterior passar. 

Os passos devem ser repetidos até termos um código que cubra o máximo de possibilidade de falhas possível.

Basicamente TDD é uma técnica de desenvolvimento e design de software em que devemos escrever testes antes de código de produção. Geralmente times ágeis adotam essa prática e é uma das principais ferramentas no desenvolvimento de software ágil.
 
Segundo a Wikipedia:

>Test Driven Development (TDD) ou em português Desenvolvimento guiado por testes é uma técnica de desenvolvimento de software que se relaciona com o conceito de verificação e validação e se baseia em um ciclo curto de repetições: Primeiramente o desenvolvedor escreve um caso de teste automatizado que define uma melhoria desejada ou uma nova funcionalidade.

>Desenvolvimento dirigido por testes requer dos desenvolvedores criar testes de unidade automatizados que definam requisitos em código antes de escrever o código da aplicação. Os testes contém asserções que podem ser verdadeiras ou falsas. Após as mesmas serem consideradas verdadeiras após sua execução, os testes confirmam o comportamento correto, permitindo os desenvolvedores evoluir e refatorar o código.

Fonte: [https://pt.wikipedia.org/wiki/Test_Driven_Development](https://pt.wikipedia.org/wiki/Test_Driven_Development)

## PHP Unit

PHP Unit é uma ferramenta que permite desenvolvedores PHP a escreverem testes unitários e praticarem o TDD. PHP Unit é um framework completo criado por Sebastian Bergmann [https://github.com/sebastianbergmann](https://github.com/sebastianbergmann) com suporte à tecnica de mocking onde criamos objetos falsos que simulam comportamentos de objetos reais.

Não entrarei nos detalhes da instalação desta ferramenta pois a própria documentação é bem rica e inclusive traduzida em português, o composer novamente é uma mão na roda para adicionar o PHP Unit ao nosso projeto.

Fonte: [https://phpunit.de/](https://phpunit.de/)

## O que TDD não é:

- Apenas criar testes unitários;
- É ter testes unitários;
- Perda de tempo;
- TDD = software sem bug.

## O que ganho implementando TDD?

- Menor custo para manutenção do software; 
- Melhorar nas estimativas; 
- Legibilidade do código; 
- Design guiado por Testes.

Deixo um [repositório](https://github.com/eduardofg87/lets-tdd-a-simple-app) para quem está iniciando com TDD no PHP. 

A estrutura do projeto foi criada utilizando o comando **composer init** que facilita para seguirmos o [PSR-4](https://www.php-fig.org/psr/psr-4/), já os nomes de variáveis e o estilo de codificação procurei seguei [PSR-1](https://www.php-fig.org/psr/psr-1/) e [PSR-2](https://www.php-fig.org/psr/psr-2/), abaixo temos a estrutura do projeto:


```
- app
  - Wrapper.php
- tests/
  - FirstTest.php
- .gitignore
- LICENSE
- README.MD
- composer.json
```


No arquivo FirstTest.php temos a implementação dos testes, eles foram escritos seguindo os passos de 1 a 3 e assim que foi escrito cada método de teste logo em seguida foi escrito cada método de produção:

### Testes - FirstTest.php

```

<?php

require_once dirname(__DIR__) . '/vendor/autoload.php';

use PHPUnit\Framework\TestCase;
use App\Wrapper as Wrapper;
 
class WrapperTest extends TestCase {
 
    private $wrapper;
 
    function setUp() {
        $this->wrapper = new Wrapper();
    }
    //Test 1
    function testItShouldWrapAnEmptyString() {
        $this->assertEquals('', $this->wrapper->wrap('', 0));
    }
    
    //Test 2
    function testDoesNotWrapAShorterThanMaxCharsWord() {
        $wrapper = new Wrapper();
        $this->assertEquals('word', $wrapper->wrap('word', 5));
    }
    //Test 3
    function testItWrapsAWordSeveralTimesIfItsTooLong() {
        $textToBeParsed = 'averyverylongword';
        $maxLineLength = 5;
        $this->assertEquals("avery\nveryl\nongwo\nrd", $this->wrapper->wrap($textToBeParsed, $maxLineLength));
    }
    //Test 4
    function testItWrapsTwoWordsWhenSpaceAtTheEndOfLine() {
        $textToBeParsed = 'word word';
        $maxLineLength = 5;
        $this->assertEquals("word\nword", $this->wrapper->wrap($textToBeParsed, $maxLineLength));
    }
    //Test 5
    function testItWrapsTwoWordsWhenLineEndIsAfterFirstWord() {
        $textToBeParsed = 'word word';
        $maxLineLength = 7;
        $this->assertEquals("word\nword", $this->wrapper->wrap($textToBeParsed, $maxLineLength));
    }
    //Test 6
    function testItWraps3WordsOn2Lines() {
        $textToBeParsed = 'word word word';
        $maxLineLength = 12;
        $this->assertEquals("word word\nword", $this->wrapper->wrap($textToBeParsed, $maxLineLength));
    }
    //Test 7
    function testItWraps2WordsOn3Lines() {
        $textToBeParsed = 'word word';
        $maxLineLength = 3;
        $this->assertEquals("wor\nd\nwor\nd", $this->wrapper->wrap($textToBeParsed, $maxLineLength));
    }
    //Test 8
    function testItWraps2WordsAtBoundry() {
        $textToBeParsed = 'word word';
        $maxLineLength = 4;
        $this->assertEquals("word\nword", $this->wrapper->wrap($textToBeParsed, $maxLineLength));
    }
    
}
```

### Código de produção Wrapper.php

```
<?php
namespace App;
class Wrapper {
    
    protected $wrap;
    //Passes tests 1
    /* function wrap($text, $lineLength) {
        if (strlen($text) > $lineLength)
            return substr ($text, 0, $lineLength) . "\n" . substr ($text, $lineLength);
        return $text;
    } */
    
    //Passes tests 1-2
    /* function wrap($text, $lineLength) {
        if (strlen($text) > $lineLength)
            return substr ($text, 0, $lineLength) . "\n" . $this->wrap(substr($text, $lineLength), $lineLength);
        return $text;
    } */
    
    //Passes tests 1-3
   /*  function wrap($text, $lineLength) {
        if (substr($text, $lineLength - 1, 1) == ' ')
            return substr ($text, 0, strpos($text, ' ')) . "\n" . $this->wrap(substr($text, strpos($text, ' ') + 1), $lineLength);
        if (strlen($text) > $lineLength)
            return substr ($text, 0, $lineLength) . "\n" . $this->wrap(substr($text, $lineLength), $lineLength);
        return $text;
    } */
    
    //Passes tests 1-4
    /* function wrap($text, $lineLength) {
        if (strlen($text) > $lineLength) {
            if (strpos(substr($text, 0, $lineLength), ' ') != 0)
                return substr ($text, 0, strpos($text, ' ')) . "\n" . $this->wrap(substr($text, strpos($text, ' ') + 1), $lineLength);
            return substr ($text, 0, $lineLength) . "\n" . $this->wrap(substr($text, $lineLength), $lineLength);
        }
        return $text;
    } */
    
    //Passes tests 1-5
    /* function wrap($text, $lineLength) {
        if (strlen($text) <= $lineLength)
            return $text;
        if (strpos(substr($text, 0, $lineLength), ' ') != 0)
            return substr ($text, 0, strpos($text, ' ')) . "\n" . $this->wrap(substr($text, strpos($text, ' ') + 1), $lineLength);
        return substr ($text, 0, $lineLength) . "\n" . $this->wrap(substr($text, $lineLength), $lineLength);
    } */
    //Passes tests 1-7
    /*function wrap($text, $lineLength) {
        if (strlen($text) <= $lineLength)
            return $text;
        if (strpos(substr($text, 0, $lineLength), ' ') != 0)
            return substr ($text, 0, strrpos($text, ' ')) . "\n" . $this->wrap(substr($text, strrpos($text, ' ') + 1), $lineLength);
        return substr ($text, 0, $lineLength) . "\n" . $this->wrap(substr($text, $lineLength), $lineLength);
    }*/
    //Passes all tests 1-8
    public function wrap($text, $lineLength) {
        $text = trim($text);
        if (strlen($text) <= $lineLength)
            return $text;
        if (strpos(substr($text, 0, $lineLength), ' ') != 0)
            return substr ($text, 0, strrpos($text, ' ')) . "\n" . $this->wrap(substr($text, strrpos($text, ' ') + 1), $lineLength);
        return substr ($text, 0, $lineLength) . "\n" . $this->wrap(substr($text, $lineLength), $lineLength);
    }
}
```

Os testes foram criados na classe WrapperTest e possui 8 métodos que são:

- testItShouldWrapAnEmptyString
- testDoesNotWrapAShorterThanMaxCharsWord
- testItWrapsAWordSeveralTimesIfItsTooLong
- testItWrapsTwoWordsWhenSpaceAtTheEndOfLine
- testItWrapsTwoWordsWhenLineEndIsAfterFirstWord
- testItWraps3WordsOn2Lines
- testItWraps2WordsOn3Lines
- testItWraps2WordsAtBoundry

O projeto se resume em uma classe de produção chamada Wrapper (Embalagem) e seu único método wrap que significa embrulhar/envolver recebe 2 argumentos $text e $lineLength, sendo o primeiro o texto que será envolvido e o segundo o tamanho da linha. O código de produção foi refatorado e a última versão do método wrap é capaz de passar todos os testes. Deixei comentado as versões anteriores do método wrap que passam os testes

Chegamos ao fim, espero ter contribuído com o aprendizado sobre TDD!

p.s.: Agradecimentos especiais ao [Will Correa](https://github.com/wilcorrea) pela revisão.
