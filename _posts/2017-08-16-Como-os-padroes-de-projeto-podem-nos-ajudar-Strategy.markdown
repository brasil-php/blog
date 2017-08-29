---
layout: post
title: "Como os padrões de projeto nos ajudam a melhorar? Strategy"
date: 2017-08-16 12:43:19 -0300
categories: dica
author_url: "https://medium.com/@principe.borodin"
description: "Apaixonado por tecnologia e artes, seu vocabulário desconhece a palavra 'limites', atua em todas as áreas tecnologias, música, desenho e pintura, e literatura."
author:
    name: "Igor C. de Paula"
    email: "principe.borodin@gmail.com"
gravatar: true
---
# Como os padrões de projeto nos ajudam a melhorar? Strategy

Esta semana eu me deparei com um problema aparentemente difícil: usar uma mesma classe para ordenar diversos tipos de objetos.

Qual o contexto? A aplicação usa Zend Framework 2 e Doctrine, para quem não sabe ambos reforçam muito o uso do OO e principalmente, o doctrine segue especificações do JPA.

E nesse ínterim algumas coisas ficam difíceis de serem realizadas, como uma ordenação. Nossos nomes utilizam acento e por conta disso algumas funções de ordenação ficam malucas. Seguindo a dica do Vinicius73 (podem encher o saco dele a vontade) eu utilizei o pacote illuminate/support, do laravel. E mais ainda, seguindo os princípios do OO criei um novo método utilizando outro mediante um novo parâmetro:

```php
<?php
/**
 * Created by PhpStorm.
 * User: igor
 * Date: 16/05/17
 * Time: 16:33
 */
namespace App\Collection;
use Illuminate\Support\Collection as LaravelCollection;
class Collection extends LaravelCollection
{
    protected function _all_letters_to_ASCII( $string)
    {
        return strtr(utf8_decode($string->getNome()),
            utf8_decode('ŠŒŽšœžŸ¥µÀÁÂÃÄÅÆÇÈÉÊËÌÍÎÏÐÑÒÓÔÕÖØÙÚÛÜÝßàáâãäåæçèéêëìíîïðñòóôõöøùúûüýÿ'),
            'SOZsozYYuAAAAAAACEEEEIIIIDNOOOOOOUUUUYsaaaaaaaceeeeiiiionoooooouuuuyy');
    }
    public function sortName(){
        $result = $this->sortBy([$this, '_all_letters_to_ASCII'], SORT_FLAG_CASE | SORT_STRING | SORT_NATURAL);
        return new static($result);
    }
}
```

Assim sendo quaisquer entidade que eu jogar na nesse método será ordenado mediante nomes em português. Como a entidade abaixo (apenas exemplificando — extraído do problema real):

```php
<?php
namespace App;
class User
{
    public $nome;
    public function __construct($nome)
    {
        $this->nome = $nome;
    }
    public function getNome()
    {
        return $this->nome;
    }
}
```

Mas eis que surge o problema: tenho uma entidade que tem a propriedade nome, mas ela está agregada em outra entidade que não tem este atributo. Veja o exemplo abaixo:

```php
<?php
namespace App;
class Unidade
{
    private $nome;
    public function __construct($nome)
    {
        $this->nome = $nome;
    }
    public function getNome()
    {
        return $this->nome;
    }
}
```

Entidade com o atributo nome

```php
<?php
namespace App;
class UnidadeIntegradora
{
    /**
     * @var Unidade
     */
    private $unidadeAula;
    /**
     * @var Unidade
     */
    private $unidadeBeneficiada;
    /**
     * @return Unidade
     */
    public function getUnidadeAula()
    {
        return $this->unidadeAula;
    }
    /**
     * @param Unidade $unidadeAula
     * @return UnidadeIntegradora
     */
    public function setUnidadeAula(Unidade $unidadeAula)
    {
        $this->unidadeAula = $unidadeAula;
        return $this;
    }
    /**
     * @return Unidade
     */
    public function getUnidadeBeneficiada()
    {
        return $this->unidadeBeneficiada;
    }
    /**
     * @param Unidade $unidadeBeneficiada
     * @return UnidadeIntegradora
     */
    public function setUnidadeBeneficiada(Unidade $unidadeBeneficiada)
    {
        $this->unidadeBeneficiada = $unidadeBeneficiada;
        return $this;
    }
}
```

Entidade sem o atributo nome, a qual agrega a entidade anterior.

Veremos o comportamento da ordenação:

```php
<?php
require 'vendor/autoload.php';
use App\Collection\Collection;
use App\User;
use App\Unidade;
use App\UnidadeIntegradora;
$fr = new User('Francisca');
$fa = new User('Fábia');
$array = [$fr, $fa];
$col = new Collection($array);
var_dump($col->sortName()->toArray());
$unidade1 = new Unidade('Coração Norte');
$unidade2 = new Unidade('Instituto Norte Fluminense');
$unidade3 = new Unidade('Hospital do Coração');
$unidade4 = new Unidade('Maternidade das Irmas');
$ui1 = new UnidadeIntegradora();
$ui1->setUnidadeAula($unidade3);
$ui1->setUnidadeBeneficiada($unidade2);
$ui2 = new UnidadeIntegradora();
$ui2->setUnidadeAula($unidade3);
$ui2->setUnidadeBeneficiada($unidade1);
$array = [$ui1, $ui2];
$col = new Collection($array);
var_dump($col->sortName()->toArray());
```

Resultado:

```
php index.php 
array(2) {
  [1] =>
  class App\User#2 (1) {
    public $nome =>
    string(6) "Fábia"
  }
  [0] =>
  class App\User#3 (1) {
    public $nome =>
    string(9) "Francisca"
  }
}
PHP Fatal error:  Call to undefined method App\UnidadeIntegradora::getNome() in /home/igor/projetos/untitled/src/App/Collection/Collection.php on line 16
PHP Stack trace:
PHP   1. {main}() /home/igor/projetos/untitled/index.php:0
PHP   2. App\Collection\Collection->sortName() /home/igor/projetos/untitled/index.php:36
PHP   3. Illuminate\Support\Collection->sortBy() /home/igor/projetos/untitled/src/App/Collection/Collection.php:22
PHP 4. App\Collection\Collection->_all_letters_to_ASCII() /home/igor/projetos/untitled/vendor/illuminate/support/Collection.php:1222
```

Os nomes de usuários foram ordenados acentuadamente, mas a outra entidade não, simplesmente a entidade não tem o atributo nome, o que fazer? Preciso de algo para trazer este atributo nome, dinamicamente, para que a coleção seja ordenada. Vamos recorrer aos padrões de projeto.

Padrões de projeto são pequenas arquiteturas focadas em resolver problemas, neste caso, vamos usar o strategy, vejamos o que ele nos diz:

## Qual o objetivo do Strategy pattern?

Alterar um algoritmo em tempo de execução apenas conhecendo sua interface. Segundo Erich Gamma (et all — Gamma, Erich et all. Padrões de projeto — Soluções reutilizáveis de software orientado a objetos. 2000, Bookman):

```
Definir uma família de algoritmos, encapsular cada uma delas e torná-las intercambiáveis. 
Strategy permite que o algoritmo varie independentemente dos clientes que o utilizam.
```

## Aplicabilidade

Você necessita de variantes de um algoritmo. Por exemplo, pode definir algoritmos que refletem diferentes soluções de compromisso entre espaço/ tempo. As estratégias podem ser usadas quando essas variantes são implementadas como uma hierarquia de classes de algoritmos

## Vantagens, desvantagens e consequências

Estratégias eliminam comandos condicionais da linguagem de programação. O padrão Strategy oferece uma alternativa ao uso de comandos condicionais para a seleção de comportamentos desejados. Quando diferentes comportamentos são agrupados em uma classe é difícil evitar o uso de comandos condicionais para a seleção do comportamento correto.O encapsulamento do comportamento em classes Strategy separadas elimina estes comandos condicionais.

A possibilidade de escolha de implementações. As estratégias podem fornecer diferentes implementações do mesmo comportamento. O cliente pode escolher entre estratégias com diferentes compromissos entre tempo e espaço.

Eu quero que qualquer entidade que entre na ordenação possa me devolver um objeto que me traga seu nome para ordenar. Cada entidade fará isto de maneira diferente, por estratégia diferente, ok, let’s code.

Primeiro criamos a interface apropriada:

```php
<?php
/**
 * Created by PhpStorm.
 * User: igor
 * Date: 16/05/17
 * Time: 16:45
 */
namespace App\Contracts;
interface SortableNameInterface
{
    public function getSortableNomeProperty();
}
```

Agora implementamos a interface nas entidades:

```php
<?php
namespace App;
use App\Contracts\SortableNameInterface;
class UnidadeIntegradora implements SortableNameInterface
{
    /**
     * @var Unidade
     */
    private $unidadeAula;
    /**
     * @var Unidade
     */
    private $unidadeBeneficiada;
    /**
     * @return Unidade
     */
    public function getUnidadeAula()
    {
        return $this->unidadeAula;
    }
    /**
     * @param Unidade $unidadeAula
     * @return UnidadeIntegradora
     */
    public function setUnidadeAula(Unidade $unidadeAula)
    {
        $this->unidadeAula = $unidadeAula;
        return $this;
    }
    /**
     * @return Unidade
     */
    public function getUnidadeBeneficiada()
    {
        return $this->unidadeBeneficiada;
    }
    /**
     * @param Unidade $unidadeBeneficiada
     * @return UnidadeIntegradora
     */
    public function setUnidadeBeneficiada(Unidade $unidadeBeneficiada)
    {
        $this->unidadeBeneficiada = $unidadeBeneficiada;
        return $this;
    }
    public function getSortableNomeProperty()
    {
        return $this->getUnidadeAula();
    }
}
```

```php
<?php
namespace App;
use App\Contracts\SortableNameInterface;
class User implements SortableNameInterface
{
    public $nome;
    public function __construct($nome)
    {
        $this->nome = $nome;
    }
    public function getNome()
    {
        return $this->nome;
    }
    public function getSortableNameProperty()
    {
        return $this;
    }
}
```

E definimos na collection que queremos objetos do tipo SortableName, observe a nova assinatura do método de nomes ASCII

```php
<?php
/**
 * Created by PhpStorm.
 * User: igor
 * Date: 16/05/17
 * Time: 16:33
 */
namespace App\Collection;
use Illuminate\Support\Collection as LaravelCollection;
use App\Contracts\SortableNameInterface;
class Collection extends LaravelCollection
{
    protected function _all_letters_to_ASCII(SortableNameInterface $string)
    {
        return strtr(utf8_decode($string->getSortableNomeProperty()->getNome()),
            utf8_decode('ŠŒŽšœžŸ¥µÀÁÂÃÄÅÆÇÈÉÊËÌÍÎÏÐÑÒÓÔÕÖØÙÚÛÜÝßàáâãäåæçèéêëìíîïðñòóôõöøùúûüýÿ'),
            'SOZsozYYuAAAAAAACEEEEIIIIDNOOOOOOUUUUYsaaaaaaaceeeeiiiionoooooouuuuyy');
    }
    public function sortName(){
        $result = $this->sortBy([$this, '_all_letters_to_ASCII'], SORT_FLAG_CASE | SORT_STRING | SORT_NATURAL);
        return new static($result);
    }
}
```

Pronto, vamos testar? (Infelizmente o phpunit não tem este tipo de teste)

```php
<?php
require 'vendor/autoload.php';
use App\Collection\Collection;
use App\User;
use App\Unidade;
use App\UnidadeIntegradora;
$fr = new User('Francisca');
$fa = new User('Fábia');
$array = [$fr, $fa];
$col = new Collection($array);
var_dump($col->sortName()->toArray());
$unidade1 = new Unidade('Coração Norte');
$unidade2 = new Unidade('Instituto Norte Fluminense');
$unidade3 = new Unidade('Hospital do Coração');
$unidade4 = new Unidade('Maternidade das Irmas');
$ui1 = new UnidadeIntegradora();
$ui1->setUnidadeAula($unidade3);
$ui1->setUnidadeBeneficiada($unidade2);
$ui2 = new UnidadeIntegradora();
$ui2->setUnidadeAula($unidade3);
$ui2->setUnidadeBeneficiada($unidade1);
$array = [$ui1, $ui2];
$col = new Collection($array);
var_dump($col->sortName()->toArray());
```

E o resultado?

```
php index.php 
array(2) {
  [1] =>
  class App\User#2 (1) {
    public $nome =>
    string(6) "Fábia"
  }
  [0] =>
  class App\User#3 (1) {
    public $nome =>
    string(9) "Francisca"
  }
}
array(2) {
  [0] =>
  class App\UnidadeIntegradora#9 (2) {
    private $unidadeAula =>
    class App\Unidade#7 (1) {
      private $nome =>
      string(21) "Hospital do Coração"
    }
    private $unidadeBeneficiada =>
    class App\Unidade#5 (1) {
      private $nome =>
      string(26) "Instituto Norte Fluminense"
    }
  }
  [1] =>
  class App\UnidadeIntegradora#10 (2) {
    private $unidadeAula =>
    class App\Unidade#8 (1) {
      private $nome =>
      string(21) "Maternidade das Irmas"
    }
    private $unidadeBeneficiada =>
    class App\Unidade#6 (1) {
      private $nome =>
      string(15) "Coração Norte"
    }
  }
}
```

Perceba que a entidade unidade integradora foi ordenada apenas por atributo unidade aula. Não fiz nenhum if ou switch. Nenhum método extra foi criado para cada objeto diferente. Nenhuma mudança brusca foi feita no código, apenas mudança de estratégia. ;)

Perceba também que neste cenário o strategy foi implementado de forma diferente dos catálogos, mas que atingiu o meu objetivo e o dele.

PS.: Eu poderia fazer a consulta ordenada ao banco, mas eu perderia o aspecto OO do contexto e resolver o problema de forma elegante.
