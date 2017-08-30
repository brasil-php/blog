---
layout: post
title: "Relatórios em PHP(Laravel) com JasperReports  - Parte 1"
date: 2017-08-29 16:24:00 -0300
categories: dicas-tutoriais
twitter: "@eltonnicolau"
facebook: https://www.facebook.com/elton182
telegram: "http://t.me/elton182"
author: 
    name: "Elton Nicolau"
    author_url: 'https://medium.com/@eltonantunes_85873'
    author_url_label: 'Medium do Elton'
    email: eltonantunes@hotmail.com
comments: true
---
# Relatórios em PHP(Laravel) com JasperReports — Parte 1

Olá Nerds!

Há algum tempo procuro uma maneira mais eficiente de desenvolver relatórios nas minhas aplicações, já tentei usar PHPExcel e converter pra PDF, mas o layout nunca fica 100% e tentei outras formas também, como transformar HTML em pdf com várias outras libs.

Todo o processo é muito trabalhoso e acaba virando um ciclo de “tentativa e erro”, que é horrível, chato e desanimador.

Foi ai que encontrei uma forma de usar o Jasper Reports, que é uma solução de “Report Designer” onde você configura uma conexão direto com o banco, monta uma query e desenha o relatório . No final é gerado um “xml” que através de uma lib chamada jasperstater é possivel chamar via linha de comando passando os parâmetros necessários. E para minha felicidade no Github já havia varias libs usando esse processo.

_**Agora menos papo e show me the code**_

**Obs: Estou assumindo que você já sabe usar o composer e já usa o laravel**

1. Crie uma nova aplicação
Vamos criar uma nova aplicação Laravel, no console digite:
```laravel new clientes```

![Criando uma nova aplicação Laravel](https://cdn-images-1.medium.com/max/800/1*e5Hz5OWxRaAOsCSZA5DXDw.png)

2. Crie um novo banco e vamos criar uma tabela chamada customers

```php artisan make:model Customer -m```

a tabela deverá ter os seguintes campos:

* id: campo auto incremental e chave única
* name: nome do cliente (string)
* address: endereço do cliente (string)
* city: cidade do cliente (string)
* phone: telefone do cliente (string)
* since: campo de data que identifica desde quando ele é nosso cliente (coloquei só pra fazer where de data)

Abaixo o schema da migration
```
Schema::create('customers', function (Blueprint $table) {
    $table->increments('id');
    $table->string('name');
    $table->string('address');
    $table->string('city');
    $table->string('phone');
    $table->datetime('since');
    $table->timestamps();
});
```

Rode a migration!

4. Criando dados com factory

Vamos precisar de dados para poder testar o relatório

Crie a factory abaixo e rode via tinker

```
$factory->define(App\Customer::class, function (Faker\Generator $faker) {
    
    return [
        'name'    => $faker->name,
        'address' => $faker->address, 
        'city'    => $faker->city,
        'phone'   => $faker->phoneNumber,
        'since'   => $faker->datetime(),
    ];
});
```

5. Adicione via composer o pacote do phpjasper do lavela

```composer require lavela/phpjasper```

6. Baixe e instale o [Jaspersoft® Studio](http://community.jaspersoft.com/project/jaspersoft-studio)

7. Desenhando o relatório

No JasperStudio criei um novo projeto e adicione um relatório com o template em branco

![Relatório em branco](https://cdn-images-1.medium.com/max/800/1*ONexHS4XZQyXlEXiTuMHSg.png)

Nos relatórios feitos em ferramentas como essa é comum a pagina ser dividia sem sessões ou “bands” nesse ![Link](http://community.jaspersoft.com/wiki/report-structure-jaspersoft-studio) tem uma explicação detalhada

Para simplificar eu tirei o Column Header e Column footer

Agora adicione um novo Data Adapter, que será uma conexão local para o seu banco/tabela, e com isso conseguir desenvolver o relatório

![Adicione um novo Data Adapter](https://cdn-images-1.medium.com/max/800/1*A4ILVZ67MdTryxFMVCz1hA.png)

Coloque um nome de sua preferência e configure a conexão, é um processo bem simples

![Depois de configurado Teste e Finalize](https://cdn-images-1.medium.com/max/800/1*1i-eqVf0W-QIrpY5uUChMQ.png)

Agora vamos fazer a query

![Query](https://cdn-images-1.medium.com/max/800/1*5nmfVutZPg4Vt4rHa5Ux8g.png)
Clique no botão em detalhe para editar a query

Selecione o Adapter criado, faça a query e clique em Read Fields que o jasper irá automaticamente mapear os campos do relatório

```select name, address, city, phone, since from customers```

![](https://cdn-images-1.medium.com/max/800/1*l8zkch0bHcD3wTiIAjfhKw.png)

Agora será possível pegar os campos e colocar no relatório

![](https://cdn-images-1.medium.com/max/800/1*lz9RgMHFIFjjjAG4zcbY3g.png)

No sessão Outline é possível arrastar os campos para o relatório

No final o relatório ficou assim ( ficou bem simples, mas vai servir de exemplo)
![](https://cdn-images-1.medium.com/max/800/1*9AUq7uKeF3zdspwwQ1tnrQ.png)

E ao clicar em “Preview” podemos ver como está ficando
![](https://cdn-images-1.medium.com/max/800/1*42JSwRXd9NQBLIRcTUaHAQ.png)

8) Publicando o relatório no projeto

Na pasta public do projeto laravel crie um diretório chamado reports e cole o arquivo do relatório Customers.jrxml
![](https://cdn-images-1.medium.com/max/800/1*VklaFt52_3yMujk5FyNqHQ.png)

Agora crie um controller chamado ReportController, abaixo está todo o conteúdo do controller com os comentários
```
<?php
namespace App\Http\Controllers;
use Illuminate\Http\Request;
use JasperPHP\JasperPHP;
use App\Customer;
class ReportController extends Controller
{
/**
     * Reporna um array com os parametros de conexão
     * @return Array
     */
 public function getDatabaseConfig()
    {
        return [
            'driver'   => env('DB_CONNECTION'),
            'host'     => env('DB_HOST'),
            'port'     => env('DB_PORT'),
            'username' => env('DB_USERNAME'),
            'password' => env('DB_PASSWORD'),
            'database' => env('DB_DATABASE'),
            'jdbc_dir' => base_path() . env('JDBC_DIR', '/vendor/lavela/phpjasper/src/JasperStarter/jdbc'),
        ];
    }
public function index()
    {
     // coloca na variavel o caminho do novo relatório que será gerado
        $output = public_path() . '/reports/' . time() . '_Clientes';
// instancia um novo objeto JasperPHP
         
        $report = new JasperPHP;
// chama o método que irá gerar o relatório
        // passamos por parametro:
        // o arquivo do relatório com seu caminho completo
        // o nome do arquivo que será gerado
        // o tipo de saída
        // parametros ( nesse caso não tem nenhum)         
        $report->process(
            public_path() . '/reports/Customers.jrxml',
            $output,
            ['pdf'],
            [],
            $this->getDatabaseConfig()
        )->execute();
$file = $output . '.pdf';
        $path = $file;
        
        // caso o arquivo não tenha sido gerado retorno um erro 404
        if (!file_exists($file)) {
            abort(404);
        }
//caso tenha sido gerado pego o conteudo
        $file = file_get_contents($file);
//deleto o arquivo gerado, pois iremos mandar o conteudo para o navegador
        unlink($path);
// retornamos o conteudo para o navegador que íra abrir o PDF
        return response($file, 200)
            ->header('Content-Type', 'application/pdf')
            ->header('Content-Disposition', 'inline; filename="cliente.pdf"');
     
    }
}
```

9) Relatório pronto

Aponte uma rota para o novo controller, suba a aplicação e teste, se seguiu todos os passos o relatório será exibido conforme imagem abaixo
![](https://cdn-images-1.medium.com/max/800/1*m7FZ59K-xXTlbKmhUZozzg.png)

Resolvi quebrar esse artigo em dois, acabou ficando grande.

No próximo iremos melhorar o relatório colocando filtros e passando por parâmetro.

PS: Se estiver usando o linux talvez tenha problemas de permissão, veja no repositório https://github.com/lavela/phpjasper/ , lá tem algumas dicas de como resolver.
