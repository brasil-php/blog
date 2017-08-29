### Blog PHP Brasil

# Antes de um blog uma base de conhecimentos

Repositório para agregar posts do grupo do Telegram
----
Se você conhece o Grupo de Telegram sabe que lá é correria! Com base nisso decidimos criar um blog com materiais que são 
comentados e discutidos por lá ;)

## Como isso funciona?

O blog é mantido pela própria comunidade. Com o passar do tempo temos percebido que o conceito de fórum está ficando um pouco defasado em relação as necessidades que as pessoas tem demonstrado. Não que não tenha mais que existir essa modalidade de troca de conhecimentos, mas os chats e comunicação em tempo real trazem alguma facilidade e agilidade para a troca de informação sobre o problema.

Como nem tudo são flores o chat não é uma boa base de conhecimentos. Não fica claro como o problema se resolveu e o conhecimento adquirido não é formalizado para o grupo. A proposta do blog é que os envolvidos em determinada discussão criem um conteúdo que resuma o que se desenrolou : )

## "Issues" para dúvidas recorrentes

Além de persistir as threads também acreditamos que seja possível registrar a relevância de um tema para a comunidade. Sendo assim quem identificar essa demanda poderá criar uma issue para que alguém que conheça algo sobre o assunto crie uma `post` para toda a comunidade usufruir desse conhecimento

## Atualização 2.0
### Novo guia para postagens

O sistema conta com um novo guia para postagens com integração ao Gravatar, redes sociais e uma area sobre o postador.
obs: A sintax deve está extamente igual a baixo(Campos obrigatorios)

```
---
layout: post # Obrigatorio
title:  "Qual melhor framework PHP do mundo!" # Obrigatorio
date:   2017-08-16 21:43:19 -0300             # Obrigatorio
categories: reflexao        # Obrigatorio [SUPORTE A MUTIPLAS CATEGORIAS SEPARADOS POR HÍFEN]
author_url: UrlMedium       # Opcional
twitter: urlTwitter         # Opcional
facebook: urlFacebook       # Opcional
telegram: urlTelegram       # Opcional
description: "Sobre Você"   # Opcional
author: 
    name: "Seu Nome" # Obrigatorio
    email: "seuEmail@exemplo.com" #Obrigatorio para o Gravatar!
gravatar: true # Retire ou deixe em false para desativar o gravatar no seu post!

---
Obs: Se não usar gravatar, remova a variavel ou deixe null.

## Exemplo de Titulo do Post

O seu conteudo deve está abaixo do post recomenda se usar ## para o titulo!
```
----

Vida longa ao PHP
