---
layout: post
title: "Como criar sistemas que evoluem"
date: 2017-10-08 21:00:00 -0300
categories: Arquitetura
author_url: "https://medium.com/@wilcorrea"
description: "Tem algumas coisas que a gente sabe que vão acontecer, estas são as que acontecem por acaso. Todo o resto é o que compõe a certeza."
author: 
  name: "William Correa"
  email: "wilcorrea@gmail.com"
  author_url: "https://wilcorrea.rocks"
twitter: "https://twitter.com/wilcorrea"
facebook: "https://fb.com/wilcorrea.rocks"
telegram: "https://t.me/wilcorrea"
gravatar: true
---
# Como criar sistemas que evoluem

Sempre que alguém vai começar a trabalhar em um projeto precisa escolher ou entender o como aquele projeto será ou foi feito.
Quando isso acontece é comum ter um desafio para o time porque não há um consenso claro sobre padrões a serem seguidos.
É comum surgirem muitos nomes de metodologias e frameworks, mas é totalmente possível seguir metodologias e utilizar frameworks de formas diferentes.

Essa segmentação no mundo PHP sempre foi um impasse até que a iniciativa do PHP-FIG trouxe a luz alguns padrões para algumas coisas. Estamos agora numa nova fase do PHP onde o fluxo de trabalho precisa ser revisto e o PHP da década passada erradicado dos projetos modernos. A imagem abaixo pode ser vista em seu contexto original [aqui](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/routes) onde ela é usada para explicar o funcionamento de uma aplicação feita usando Expresse, e pasmem, ela não é muito diferente do que você verá em ferramentas como CakePHP, Laravel, Symfony, Zends, Slim e etc.

![image](https://cdn-images-1.medium.com/max/800/0*VvmaP5PBCo-6n8sU.png)

A web está consolidada. É isso! Achamos nosso jeito de trabalhar. Um projeto em uma linguagem não é mais tão distante do ponto de vista da arquitetura das outras. Sendo assim aqui vão minhas dicas para criar projetos que sejam simples de escalar e fáceis de serem mantidos:

### 1. Centralize ações. 

Priorize criar funções e/ou classes para tudo o que for uma operação comum. Se quiser um nome para estudar pode pesquisar por DRY ou KISS que são conceitos que forjam essas ideias de não repetir trechos nem operações. Encapsular relações com o sistema é sempre útil.

#### 1.1. Acesso ao HTTP
Bad
```php
$var = $_POST['var'];
```

Good
```php
$var = post('var');
```

#### 1.2. Gerenciamento de estados
Bad
```php
if (isset($_SESSION['conected'])) {
    header('Location: index.php');
}
```

Good
```php
if (!Auth::isConnected()) {
    Request::redirect('/');
}
```

#### 1.3. Controllers Gordos
Bad
```php
function saveAllItems(Request $request, Validator $validator, Product $product, Item $item)
{
    $rules = [
      'field' => 'required', 'column' => 'required', 'treta' => 'required', 'whatever' => 'required'
    ];
    foreach($request->getBody as $key => $value) {
      if (isset($rules[$key]) && !$validator->validate($value, $rules[$key])) {
        throw new ValidationException($value, $rules[$key]);
      }
    }
    $id = $product->save([
      $request->post('name'),
      $request->post('value'),
    ]);

    foreach($request->post('items') as $productItem) {
      $item->save([
        $request->post('name'),
        $productItem,
      ]);
    }
    
    return true
}
```

Good
```php
function saveAllItems(Request $request, Product $repository)
{
    return $repository->saveAllItems($request->getBody)
}
```
