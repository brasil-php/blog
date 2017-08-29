---
layout: post
title:  "Como contribuir!"
date:   2017-08-15 21:43:19 -0300
categories: ajuda
author: "William Correa"
---
Sejam bem-vindos ao nosso blog!

Estamos muito felizes de compartilhar com vocês os conhecimentos que são discutidos e trocados no nosso grupo.
Nossa [comunidade no Telegram](https://t.me/phpbrasil) tem mais de 1.7k de membros e há troca de mensagens com volumes impressionantes. São abordados temas desde os mais básicos sobre configuração de ambiente e programação até discussões avançadas sobre abstração e metodologias de engenharia e arquitetura de software. Com esse canal vamos publicar materiais que visam a deixar disponível de forma organizada os assuntos abordados.

Se você chegou até aqui está procurando saber como participar do blog e vou tentar ser o mais breve e prático possível.

Estamos usando Jekyll como plataforma para publicação dos artigos, então recomendo dar uma olhada na [documentação](https://jekyllrb.com/) dele para esclarecer dúvidas quanto a como formatar o texto.

Vou mostrar como contribuir em 4 passos sem ter que instalar nada, usando apenas o próprio Github:

**Passo 1.** Acesse com sua conta o [Github](https://github.com) e crie um fork do [projeto](https://github.com/brasil-php/blog)

**Passo 2.** Vá até a pasta `_posts` e veja que tem alguns documentos lá com a data e o título como nome. Crie um novo arquivo informando a data e o título (sem acentos e com hífen no lugar dos espaços)

**Passo 3.** No começo do arquivo crie uma tabela e descreva os detalhes do `post` no seguinte formato 

![passo-3](https://snag.gy/fbe8Ag.jpg)

No topo do arquivo você configura conforme a seguir:
 * **layouts**: define o layout desta página, não altere
 * **title**: Título do artigo
 * **header-img**: Aqui vai a sua imagem(que deve ficar no diretório assets/img).
 * **date**: Data da pubicação no formato YYYY-MM-DD HH:MM:SS, o -0200 é o timezone
 * **categories**: Categorias que este artigo pertence, caprixe nisso
 * **author**: Seu nome
 * **author_url**: Um link que você queira postar, para que as pessoas te encontrem, seu portifólio, enfim...
 * **author_ur_label**: O texto ancora para seu link
 * **comments**: habilita e desabilita comentários
 
O Jekyll que faz o build para o HTML fornece alguns recursos legais, como este **code highlight** a seguir:

{% highlight php %}
<?php

namespace App\Controller;

class UsersController extends AppController
{
    public function login()
    {
        /**...**/
    }
}

{% endhighlight %}


**Passo 4.** Salve o arquivo com o conteúdo e submeta um PR ao repositório principal, indo até a tab de `Pull requests` do seu fork e clicando em `New pull request`

![passo-3](https://snag.gy/EJerxK.jpg)

<br>
<br>
<hr>
<br>
<br>
P.S.:
Se quiser testar no seu projeto antes de enviar o PR (aka Pull Pequest), você pode configurar o seu repositório para gerar uma URL com o Jekyll indo até a tab Settings na seção GitHub Pages e deixando-a configurada como a imagem abaixo

![passo-3](https://snag.gy/GVbsZI.jpg)

PS.: Não esqueçam de adicionar seu nome e um link no fim do artigo para que quem ler e quiser algum serviço ou trocar uma ideia com vocês, possam encontrá-los.

