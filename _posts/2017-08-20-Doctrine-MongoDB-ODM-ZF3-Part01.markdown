---
layout: post
title: "Doctrine MongoDB ODM com ZF3 - Instalação, configuração e primeiro uso"
date: 2017-08-20 14:39:38 -0300
categories: deselvolvimento
author: 
    name: "Jociel Souza"
---
# Doctrine MongoDB ODM com ZF3 - Instalação, configuração e primeiro uso


O Doctrine MongoDB ODM (Object Document Mapper), é um framework de mapeamento documento-objeto que fornece uma melhor forma de manipular, persistir objetos do PHP no MogoDB

Antes de começar iremos assumir as seguintes premissas (entre parenteses está a versão que estou utilizando):

Que já tenha instalado em sua maquina  
Composer(1.4.2)  
PHP(7.1.8)  
MongoDB(3.4.3)  
Conhecimento básico de ZendFramework, PHP e Mongo

Eu estou utilizando o Manjaro Linux como Sistema Operacional.  
Então... mão na massa, ou melhor, nos comandos e códigos :)

**Instalação**

Primeiramente vamos instalar o driver do MongoDB para o PHP usando o pecl

```
pecl install mongodb
```
Para verificar se esta ativa é só executar o comando `php -m` e a extensão `mongodb` deve aparecer na lista.

Após vamos criar um projeto chamado `doctrineBrasilPHP` com o skeleton MVC do ZendFramework 3

```
composer create-project -n -sdev zendframework/skeleton-application doctrineBrasilPHP
```

Entramos no diretório do novo projeto com o comando `cd doctrineBrasilPHP` para instalarmos as seguintes dependências utilizando o composer

```
composer require alcaeus/mongo-php-adapter

composer require doctrine/mongodb

composer require doctrine/mongodb-odm

composer require doctrine/doctrine-mongo-odm-module
```

Estamos instalando a biblioteca `alcaeus/mongo-php-adapter` pois a versão do DoctrineODM ainda não suportar a versão da nova extensão do mongo, funcionando como um adapter.  
Ao instalar o `doctrine-mongo-odm-module` talvez o zend faça algumas perguntas de configuração, selecione a opção `1` para ambas.

No seu `composer.json` você terá algo semelhante a isso

```json
    "minimum-stability": "dev",
    "prefer-stable": true,
    "require": {
        "php": "^5.6 || ^7.0",
        "zendframework/zend-component-installer": "^1.0 || ^0.7 || ^1.0.0-dev@dev",
        "zendframework/zend-mvc": "^3.0.1",
        "zfcampus/zf-development-mode": "^3.0",
        "alcaeus/mongo-php-adapter": "^1.1",
        "doctrine/mongodb": "^1.6",
        "doctrine/mongodb-odm": "^1.1",
        "doctrine/doctrine-mongo-odm-module": "^1.0"
    },
    "autoload": {
        "psr-4": {
            "Application\\": "module/Application/src/"
        }
    },
```
  
Por enquanto temos o necessário instalado.

**Configuração**

Agora precisamos criar o nosso arquivo de configuração local do DoctrineODM com os dados da conexão e outros,  
mas para nossa felicidade o doctrine já traz um arquivo pré pronto(rsrs), vamos copiá-lo para o diretório de configuração
do projeto do zend e renomeá-lo, assumindo que estamos na raiz do projeto execute o seguinte comando:

```
cp vendor/doctrine/doctrine-mongo-odm-module/config/module.doctrine-mongo-odm.local.php.dist config/autoload/doctrine-mongo-odm.local.php
```  

Nesse arquivo, da linha `7` até a linha `17`, temos a sessão de configuração da conexão com o mongo, no meu caso
coloquei a seguinte configuração:

```php
<?php
'connection' => array(
    'odm_default' => array(
        'server'           => 'localhost',
        'port'             => '27017',
//                'connectionString' => null,
//                'user'             => null,
//                'password'         => null,
        'dbname'           => "artigoBrasilPhp",
        'options'          => array()
    ),
),
```

Da linha `19` até a linha `45` temos configurações de cache, hydrator, etc, irremos descomentar essa sessão.  
Notem na linha `26` e `30` onde indica caminho de dirretórios, precisamos criar esses diretórios.

```
mkdir data
mkdir data/DoctrineMongoODMModule
mkdir data/DoctrineMongoODMModule/Proxy
mkdir data/DoctrineMongoODMModule/Hydrator
```
Agora vamos configurar onde o DoctrineODM ira encontrar nossas classes que representam os documentos do mongo  
abra o arquivo `module\Application\config\module.config.php`, abaixo da sessão `controllers` adicione o seguinte trecho
de código:

```php
<?php
'doctrine' => [
    'driver' => [
        __NAMESPACE__ . '_driver' => [
            'class' => 'Doctrine\ODM\MongoDB\Mapping\Driver\AnnotationDriver',
            'paths' => [__DIR__ . '/../src/Document']
        ],
        'odm_default' => [
            'drivers' => [
                __NAMESPACE__ . '\Document' => __NAMESPACE__ . '_driver'
            ]
        ]
    ]
],
```

Notem a linha `'paths' => [__DIR__ . '/../src/Document']`, é aqui que indicamos para o Doctrine onde ele vai encontrar nossas classes dos documentos,  
então temos que criar esse diretório, novamente assumindo que estamos na raiz do projeto.

```
mkdir module/Application/src/Document
```
A configuração esta pronta.

**Primeiro uso**

Para mapear as classes utilizamos "Notations" para definirmos as propriedades, tipos, etc. Sem mais conversa vamos aos códigos.  
Vamos criar dentro do dirétorio "Documents" uma classe de nome User, no arquivo iremos importar o namespace "Annotations" do Doctrine
```
use Doctrine\ODM\MongoDB\Mapping\Annotations as ODM;
```

Logo acima do nome da classe iremos dizer que ela é um documento da collection User atravéz de notations, ficando assim:
```php
<?php
/** @ODM\Document(collection="User") */
class User
{

}
```

Vamos definir alguns atributos para essa classe, "nome", "email", "idade", e vamos mapear esses atributos para o DoctrineODM,  
a classe final ficando dessa forma:
```php
<?php

namespace Application\Document;

use Doctrine\ODM\MongoDB\Mapping\Annotations as ODM;

/** @ODM\Document(collection="User") */
class User
{
    /** @ODM\Id */
    private $id;
        
    /** @ODM\Field(type="string") */  
    private $nome;  
  
    /** @ODM\Field(type="string") */  
    private $email;  
  
    /** @ODM\Field(type="int") */  
    private $idade;  
    
    public function getId()
    {
        return $this->id;
    }
  
    public function setId($id)
    {
        $this->id = $id;
    }
    
    public function getNome()
    {
        return $this->nome;
    }
  
    public function setNome($nome)
    {
        $this->nome = $nome;
    }
  
    public function getEmail()
    {
        return $this->email;
    }
  
    public function setEmail($email)
    {
        $this->email = $email;
    }
  
    public function getIdade()
    {
        return $this->idade;
    }
  
    public function setIdade($idade)
    {
        $this->idade = $idade;
    }
}
```
Na linha `10` estamos dizendo que o atributo "id" é o identificador do documento, nas linhas `13` e `16` estamos dizendo que os atributos `nome` e `email` são fields do tipo string, e na linha `19` o atributo `idade` é um field do tipo inteiro.

Agora precisamos criar a collection, o Doctrine traz alguns commandos que facilitam a nossa vida, por enquanto vamor ver apenas o de criação, então para criar essa collection basta executar esse comando.
```
php vendor/doctrine/doctrine-module/bin/doctrine-module odm:schema:create
```

Se tudo estiver correto, agora temos uma collection chamada "User" na base de dados que definimos nas configurações.  

No arquivo `module/Application/config/module.config.php`, na linha `40` até `46` deixe esse trecho de código como este:

```php
<?php
'controllers' => [
    'factories' => [
        Controller\IndexController::class => function ($sm){
            return new IndexController($sm);
        },
    ],
],
```
Nós temos um controller existente no diretório `Application/src/Cntroller`, para fins didáticos e evitar configurações vamos utilizar este controller,  
vamos acrecentar o seguinte código acima do método indexAction:

```php
<?php
private $sm;
  
function __construct($sm)
{
    $this->sm = $sm;
}
  
```

**Nota:** Não é uma boa prática fazermos como citado acima, mas para facilitar e evitar criação de arquivos e configurações, irremos proceguir desta forma.

Vamos acrescentar o o seguinte código na `indexAction` existente nesse controller:

```php
<?php
$documentManager = $this->sm->get('doctrine.documentmanager.odm_default');

$newUser = new User();

$newUser->setNome("Jociel Souza");
$newUser->setEmail("teste@gmail.com");
$newUser->setIdade(25);

$documentManager->persist($newUser);
$documentManager->flush();
```  

Ficando desta forma

```php
<?php
public function indexAction()
{
    $documentManager = $this->sm->get('doctrine.documentmanager.odm_default');

    $newUser = new User();

    $newUser->setNome("Jociel Souza");
    $newUser->setEmail("teste@gmail.com");
    $newUser->setIdade(25);

    $documentManager->persist($newUser);
    $documentManager->flush();

    return new ViewModel();
}
```


Na linha `25` estamos recuperando o DocumentManager do DoctrineODM, responsável pelas transações do banco;
```php
<?php
$documentManager = $this->sm->get('doctrine.documentmanager.odm_default');
```

Da linha `27` até a `31` estamos instanciando e setando os atributos de um usuário;
```php
<?php
$newUser = new User();
  
$newUser->setNome("Jociel Souza");
$newUser->setEmail("teste@gmail.com");
$newUser->setIdade(25);
```

Na linha `33` e `34` estamos persistindo e commitando a mudança para o banco de dados
```php
<?php
$documentManager->persist($newUser);
$documentManager->flush();
```

Estou usando o servidor interno do php escutando no localhost:8080, se acessarmos esse endereço ira aparecer a home do Zend Framework,  
Se tudo ocorreu bem e olharmos na nossa base de dados do mongo vamos ter um documento salvo com esses dados que definimos.  
Vamos copiar o `id` desse documento.  

Agora vamos comentar esse código da linha `27` até a `34` e acrescentar o seguinte:
```php
<?php
$user = $documentManager->find(User::class, "5999fb484d1d3443c01cd4f1");
  
var_dump($user);
exit;
```
Ficando desta forma
```php
<?php
public function indexAction()
{
    $documentManager = $this->sm->get('doctrine.documentmanager.odm_default');

//        $newUser = new User();
//
//        $newUser->setNome("Jociel Souza");
//        $newUser->setEmail("teste@gmail.com");
//        $newUser->setIdade(25);
//
//        $documentManager->persist($newUser);
//        $documentManager->flush();


    $user = $documentManager->find(User::class, "5999fb484d1d3443c01cd4f1");

    var_dump($user);
    exit;

    return new ViewModel();
}
```

Na linha `37` estamos recuperando um documento do tipo `User` com o ID que passamos
```php
<?php
$user = $documentManager->find(User::class, "5999fb484d1d3443c01cd4f1");
```

E damos um `var_dump` para vermos o documento recuperado.  
  
E assim podemos persistir, manipular, e recuperar documentos do mongo utilizando apenas objetos, mantendo o paradgma e facilitando a codificação.
  
Os códigos completos estão no repositório do github [aqui](https://github.com/Jciel/doctrineBrasilPHP).

Vlw galera e até o próximo artigo sobre Doctrine Mongo ODM.
