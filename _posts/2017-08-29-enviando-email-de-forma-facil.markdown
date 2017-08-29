---
layout: post
title:  "Enviar e-mail nunca foi tão facil!"
date:   2017-08-29 05:20:00 -0300
categories: "tutorial-dicas-surpresas"
author:
    name: "Rafael Gomes"
    email: "rafaelgomesgouve@gmail.com"
    author_url: "https://www.rafaelgomes.ga"
twitter: null
facebook: null
telegram: "https://t.me/raitoya"
gravatar: true
---

## PHPMailer para iniciantes!


As vezes alguém se depara com algum obstáculo enquanto está programado e precisa mandar **e-mail's**, e-mail's  são importante para um sistema, as vezes se precisa confirmar um cadastro, mandar relatórios, ou um simples formulário de contato.  Alguns iniciantes quebram a cabeça e acaba usando o método `mail()` do PHP sem tratar os dados de envio e localmente é preciso configurar o ambiente geralmente com o protocolo de envio.
 Mas com a classe do **PHPMailer** fica tudo mais fácil e prático.

Vamos começar, precisamos das seguintes classes.

1.  `PHPMailer.php`
2.  `SMTP.php`
3.  `PHPMailerException.php`

Para isso irei usar o repositorio da [PHPZM ](https://github.com/PHPMailer/PHPMailer/tree/master/src) no GitHub.
Link no GitHub: https://github.com/PHPMailer/PHPMailer/tree/master/src

### Vamos começar.


Nesse exemplo não irei utilizar o composer por razões tecnicas.

Iremos criar um arquivo PHP, você pode renomear conforme sua vontade, mas aqui chamarei ela de `sendMail.php`.
Então com a função `require_once`, importo as classes de forma obrigatória para que o sistema funcione. 

  **sendMail.php**
```php
<?php
require_once 'PHPMailer.php';
require_once 'SMTP.php';
require_once 'PHPMailerException.php';

```
**Obs: os arquivos devem estar na mesma ordem.**

Depois de importar as classes do **Mailer** iremos iniciar nossa classe, e dar o ponta pé inicial para começar a tratar nossos dados.

Ainda em **sendMail.php**

```php
<?php
$email = new PHPMailer(true); // Inicia nossa classe.
try {
    $mail->isSMTP(); // Aqui vc indica seu protocolo nesse exemplo estamos utilizando SMTP.                           
    $mail->Host = 'smtp1.exemplo.com'; // Host do SMTP você pode usar múltiplos apenas separando por ";" (Ponto e Vírgula).
    $mail->SMTPAuth = true; // Ativa a autenticação para o SMTP.
    $mail->Username = 'user@example.com';// Usuario SMTP.
    $mail->Password = 'secret'; // Senha do SMTP.
    $mail->SMTPSecure = 'tls'; // Ative a encriptação para o envio.. pode ser tbm "SSL".
    $mail->Port = 587; // Aqui eu coloc a porta do TCP.

    // DEFININDO REMETENTE E DESTINATARIO
    $mail->setFrom('Remetente@exemplo.com', 'Fulano'); // Aqui Se coloca o Remetente.
    $mail->addAddress('ciclano@exemplo.com.br', 'Ciclano'); // Aqui colocamos o destinatario 
    $mail->addAddress('irmaodociclano@example.net', 'Irmão do Ciclano'); // Opcionalmente podemos colocar mais de um destinario.
   
    // CORPO DO EMAIL
    $mail->isHTML(true); // Definimos o formato do e-mail como HTML.
    $mail->Subject = 'Convite para os Ciclanos'; // Defimos o Assunto do E-mail.
    $mail->Body    = 'Familia <b>Ciclano</b> está convidada para nosso evento!'; // Definimos o Corpo do e-mail em HTML.
    $mail->AltBody = 'Familia Ciclano está convidada para nosso evento!'; // Aqui é um corpo de texto puro!(SEM TAGS HTML)

    $mail->send(); // Usamos o metodo Send para enviar o E-mail.
    echo 'A mensagem foi enviada com sucesso'; // Retornamos que a mensagem foi enviada.
} catch (Exception $e) {
    echo 'A Mensagem Falhou.'; // Definimos uma mensagem para caso haja erro.
    echo 'Erro no Mailer: ' . $mail->ErrorInfo; // Retornamos o Error.
}
```

O nosso código está configurado, setamos as informações do servidor e os dados de envio no final temos nosso método `$mail->send()`, 
caso ele falhe nosso `Exception` retornará o erro. 

É importante frisar que os quando vc trata os campos de seus e-mails a chance deles irem para caixa de SPAM/Lixo é menor, mas também ter um dominio confiavel é importante, não adianta fazer tudo bunitinho com um dominio suspeito ou irregular.


#### No proximo Artigo:
 Vamos criar um sistema de template para dar vida aos nossos e-mails. Até lá!
