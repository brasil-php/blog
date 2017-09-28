---
layout: post
title: "Você não é, você está! State"
date: 2017-09-28 16:43:19 -0300
categories: dica
author_url: "https://medium.com/@principe.borodin"
description: "Apaixonado por tecnologia e artes, seu vocabulário desconhece a palavra 'limites', atua em todas as áreas tecnologias, música, desenho e pintura, e literatura."
author:
    name: "Igor C. de Paula"
    email: "principe.borodin@gmail.com"
gravatar: true
---
# Você não é, você está! State

Uma outra forma de definir um if de forma elegante é usando o State Pattern. Este padrão difere do Strategy na forma como “produz” esse if: o objetivo do strategy é alternar algoritmo poliformicamente, enquanto o state o faz de forma a introduzir uma regra nessa troca.
Vamos dar uma olhada na definição do padrão:

```
Permite a um objeto alterar seu comportamento quando o seu estado interno muda. O objeto parecerá ter mudado sua classe.
```

## Vamos ver sua aplicabilidade:

```
1- o comportamento de um objeto depende do seu estado e ele pode mudar seu comportamento em tempo de execução, dependendo desse estado;
2- operações têm comandos condicionais grandes, de várias alternativas, que dependem do estado do objeto. Esse estado é normalmente representado por uma ou mais constantes enumeradas.
```

Isto é ideal para definir certos workflows. Vamos a um exemplo?

Imaginemos que temos um processo seletivo no qual um operador do sistema avalia determinado documento de um candidato específico. O documento tem um workflow específico: quando ele é cadastrado no sistema ele entra com o status aguardando (waiting), e quando o operador o baixa para análise, o sistema indica que ele está sendo analisado (in analisis — regra de negócio). Posteriormente ele pode ser negado (denied) ou aprovado (approved), e numa eventual conversa entre candidato-instituição o documento negado pode se transformar em aprovado:

![Processo de aprovação de documentos](https://cdn-images-1.medium.com/max/800/1*uBArHzx3qu69yYr4w0Juzg.png)

Bom, para começar, vamos definir os estados do documento, vamos fazer isso através de uma interface:

```php
<?php
/**
 * Created by PhpStorm.
 * User: igor
 * Date: 29/08/17
 * Time: 12:15
 */
namespace App\States;
interface DocumentStateInterface
{
    public function inAnalisis();
    public function denied();
    public function approve();
}
```

Agora vamos definir um comportamento padrão para iniciar a definição do workflow, através de uma classe abstrata:

```php
<?php
/**
 * Created by PhpStorm.
 * User: igor
 * Date: 29/08/17
 * Time: 12:03
 */
namespace App\States;
use LogicException;
abstract class AbstractDocumentState implements DocumentStateInterface
{
    public function inAnalisis()
    {
        throw new LogicException("This document can't be in analisis");
    }
    public function denied()
    {
        throw new LogicException("This document can't be denied");
    }
    public function approve()
    {
        throw new LogicException("This document can't be approved");
    }
}
```

Mas para uma classe ser abstrata, ela precisa ter ao menos um método abstrato:

```php
<?php
/**
 * Created by PhpStorm.
 * User: igor
 * Date: 29/08/17
 * Time: 12:03
 */
namespace App\States;
use LogicException;
abstract class AbstractDocumentState implements DocumentStateInterface
{
    public function inAnalisis()
    {
        throw new LogicException("This document can't be in analisis");
    }
    public function denied()
    {
        throw new LogicException("This document can't be denied");
    }
    public function approve()
    {
        throw new LogicException("This document can't be approved");
    }
    abstract public function __toString();
}
```

Agora vamos definir o workflow, começando pelo “aguardando”:

```php
<?php
/**
 * Created by PhpStorm.
 * User: igor
 * Date: 29/08/17
 * Time: 12:08
 */
namespace App\States;
class WaitingState extends AbstractDocumentState
{
    public function inAnalisis()
    {
        return new InAnalisisState();
    }
    public function __toString()
    {
        return 'WAITING';
    }
}
```

Perceba que WaitingState herda de AbstractDocumentState, e ao sobrescrever o método inAnalisis eu estou permitindo meu documento transitar apenas para este estado (lembra que o padrão é lançar uma exception?).

O próximo estado é o InAnalisis:

```php
<?php
/**
 * Created by PhpStorm.
 * User: igor
 * Date: 29/08/17
 * Time: 12:09
 */
namespace App\States;
class InAnalisisState extends AbstractDocumentState
{
    public function denied()
    {
        return new DeniedState();
    }
    public function approve()
    {
        return new ApproveState();
    }
    public function __toString()
    {
        return 'IN_ANALISIS';
    }
}
```

Assim como o WaitingState, o InAnalisisState só permite a transição do estado para aprovado ou negado. Se por acaso tentar voltar para “aguardando” (waiting) será lançada uma exception.

Agora o DeniedState:

```php
<?php
/**
 * Created by PhpStorm.
 * User: igor
 * Date: 29/08/17
 * Time: 12:09
 */
namespace App\States;
class DeniedState extends AbstractDocumentState
{
    public function approve()
    {
        return new ApproveState();
    }
    public function __toString()
    {
        return 'DENIED';
    }
}
```

Note que este estado só permite transitar para “aprovado” (approved). Qualquer tentativa de voltar para “aguardando” ou “em análise” também lançará exceptions.

E por último o ApprovedState:

```php
<?php
/**
 * Created by PhpStorm.
 * User: igor
 * Date: 29/08/17
 * Time: 12:10
 */
namespace App\States;
class ApproveState extends AbstractDocumentState
{
    public function __toString()
    {
        return 'APPROVED';
    }
}
```

Este estado não permite voltar para nenhum estado anterior.

Como se dá a mágica?

```php
<?php
/**
 * Created by PhpStorm.
 * User: igor
 * Date: 29/08/17
 * Time: 12:11
 */
namespace App;
use App\States\DocumentStateInterface;
class Document implements DocumentStateInterface
{
    /*
     * @var string
     */
    private $name = '';
    /*
     * @var DocumentStateInterface
     */
    private $state = '';
    /**
     * Document constructor.
     * @param string $name
     * @param string $state
     */
    public function __construct($name, DocumentStateInterface $state)
    {
        $this->setName($name);
        $this->setState($state);
    }
    /**
     * @return string
     */
    public function getName()
    {
        return $this->name;
    }
    /**
     * @param string $name
     */
    public function setName($name)
    {
        $this->name = $name;
    }
    public function inAnalisis()
    {
        $this->setState($this->getState()->inAnalisis());
    }
    /**
     * @return string
     */
    public function getState()
    {
        return $this->state;
    }
    /**
     * @param DocumentStateInterface $state
     */
    public function setState(DocumentStateInterface $state)
    {
        $this->state = $state;
    }
    public function denied()
    {
        $this->setState($this->getState()->denied());
    }
    public function approve()
    {
        $this->setState($this->getState()->approve());
    }
}
```

Aqui está nosso documento, com métodos para transitar entre estados. Vamos aos testes?

Quem prefere fazer na mão?

```php
<?php
require 'vendor/autoload.php';
use App\Document;
use App\States\WaitingState;
$d = new Document('RG', new WaitingState());
var_dump($d);
echo($d->getState());
echo "\n";
$d->inAnalisis();
var_dump($d);
echo($d->getState());
echo "\n";
$d->denied();
var_dump($d);
echo($d->getState());
echo "\n";
$d->approve();
var_dump($d);
echo($d->getState());
echo "\n";
```

Quem prefere usar ferramenta de teste?

```php
<?php
/**
 * Created by PhpStorm.
 * User: igor
 * Date: 29/08/17
 * Time: 16:09
 */
namespace DocumentTest;
use App\Document;
use App\States\ApproveState;
use App\States\DeniedState;
use App\States\InAnalisisState;
use App\States\WaitingState;
class DocumentTest extends \PHPUnit_Framework_TestCase
{
    protected $obj = null;
    public function setUp()
    {
        parent::setUp();
        $this->obj = new Document('RG', new WaitingState());
    }
    public function testInitialState()
    {
        $this->assertEquals(new WaitingState(), $this->obj->getState());
    }
    public function testInitialForCoverageState()
    {
        $waiting = new WaitingState();
        $this->assertEquals($waiting->__toString(), $this->obj->getState());
    }
    public function testDocuentForCoverageState()
    {
        $this->assertEquals('RG', $this->obj->getName());
    }
    public function testInAnalisesState()
    {
        $this->obj->inAnalisis();
        $this->assertEquals(new InAnalisisState(), $this->obj->getState());
    }
    public function testDenyState()
    {
        $obj = new Document('RG', new WaitingState());
        $obj->inAnalisis();
        $obj->denied();
        $this->assertEquals(new DeniedState(), $obj->getState());
    }
    public function testApproveState()
    {
        $obj = new Document('RG', new WaitingState());
        $obj->inAnalisis();
        $obj->approve();
        $this->assertEquals(new ApproveState(), $obj->getState());
    }
    /**
     * @expectedException LogicException
     */
    public function testApproveStateException()
    {
        $obj = new Document('RG', new WaitingState());
        $obj->approve();
        $this->assertEquals(new ApproveState(), $obj->getState());
    }
    /**
     * @expectedException LogicException
     */
    public function testApprovedStateForDeniedStateException()
    {
        $obj = new Document('RG', new WaitingState());
        $obj->inAnalisis();
        $obj->approve();
        $obj->denied();
        $this->assertEquals(new DeniedState(), $obj->getState());
    }
    /**
     * @expectedException LogicException
     */
    public function testApprovedStateForInAnalisisException()
    {
        $obj = new Document('RG', new WaitingState());
        $obj->inAnalisis();
        $obj->approve();
        $obj->inAnalisis();
        $this->assertEquals(new DeniedState(), $obj->getState());
    }
    /**
     * @expectedException LogicException
     */
    public function testDeniedStateException()
    {
        $obj = new Document('RG', new WaitingState());
        $obj->denied();
        $this->assertEquals(new DeniedState(), $obj->getState());
    }
    /**
     * @expectedException LogicException
     */
    public function testDeniedStateForInAnalisisException()
    {
        $obj = new Document('RG', new WaitingState());
        $obj->inAnalisis();
        $obj->denied();
        $obj->inAnalisis();
        $this->assertEquals(new DeniedState(), $obj->getState());
    }
}
```
![Resultado dos testes](https://cdn-images-1.medium.com/max/800/1*iA2y6O5dZk3ff0N5x9NXXA.png)

O mais interessante de usar ferramentas de testes é testar as exceptions previstas. ;)

Até a parte 2 do artigo.

PS2.: este é o [repo](https://github.com/IgorDePaula/StatePattern).
