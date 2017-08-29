---
layout: post
title: "Visual Studio Code com PHP  (debug, git e console)"
date: 2017-08-27 20:06:00 -0300
categories: dicas
author: 
    name: "Elton Nicolau"
    author_url: 'https://medium.com/@eltonantunes_85873'
    author_url_label: 'Medium do Elton'

comments: true
---

Olá Nerds,

Confesso que me impressionei, com o VSCode!

Sendo usuário de sublime a mais de 3 anos, resolvi testar o editor de textos da Microsoft e fiquei surpreso pela qualidade e velocidade.

Assim que você instala e abre pela primeira vez o VSCode ele já sugere instalar alguns pacotes prontos de plugins para a linguagem de escolha, basta clicar e pronto, a mágica acontece.

![Tela Inicial do VSCode](https://cdn-images-1.medium.com/max/1600/1*ekHMFTyoR0mpuJ5Gb9DVVw.png)

O pacote de PHP inicial já vem com o intelliSense e o Debugger para PHP.

![PHP Extension Pack](https://cdn-images-1.medium.com/max/1600/1*SHdkHfQBzpbSchNSqyrpeg.png)

Já fiquei de cara, pois o VSCode tem um terminal dentro dele, podendo até criar várias sessões de terminais diferentes.

![Terminal no VSCode](https://cdn-images-1.medium.com/max/1600/1*jArgBF3gOxdf2OtN-uFLLQ.png)

Para abrir uma sessão do VSCode com o diretório atual aberto basta rodar o comando ```code . ```

![Abrindo VSCode pela linha de comando para o diretório atual](https://cdn-images-1.medium.com/max/1600/1*n7s5pgADT4xxsGcxOullEg.png)

# Interface
1. A barra de atalhos a esquerda é bem legal. Com essa já temos acessos as principais funcionalidades da ferramenta.
2. Explorer: Visão dos arquivos do projeto atual
3. Search: Find & Replace nos arquivos
4. Source Control: Git integrado, com um click é possível iniciar um repositório, comitar, dar push e comparar arquivos.
5. Debugger: Visão de debug
6. Extensions: Abre a visão de extensões, onde é possível pesquisar no marketplace, listar as extensões já instaladas.

![Barra de Atalhos do VSCode](https://cdn-images-1.medium.com/max/1600/1*7_Eh91bPnV2vKjKOxW7oAA.png)

# Debugging

Foi muito fácil fazer debug do PHP no VSCode, habilitei o xdebug no php.ini e ativei no editor e voilá

![Habilitando XDebug no PHP.ini](https://cdn-images-1.medium.com/max/1600/1*R4-Y9r_0mwJxT8MHQGZ7Gw.png)

Coloquei um breakpoint na rota padrão do laravel para ilustrar.

Para ativar o debugger click no icone correspondente na barra de atalhos a esquerda, depois click no botão run no topo, ele irá perguntar qual o ambiente, escolha php de run novamente (deverá aparecer uma barra no topo com os comandos de debug) e suba o servidor do laravel.

![](https://cdn-images-1.medium.com/max/1600/1*pRgVsbyN5BMhSzTE6NZw0w.png)

Com isso você já conseguirá ir debugando a aplicando step by step.

![](https://cdn-images-1.medium.com/max/1600/1*KA8PZULk0BY04fI-X-peIw.png)

# Source Control: GIT

A visão de controle de versões é simples e direta, de cara já é possível inicializar um novo repositório, comitar,

![](https://cdn-images-1.medium.com/max/1600/1*_hvDOf8ZFm_t3LmLZnt6uw.png)

# Conclusão

Pela primeira vez estou pensando seriamente em trocar o sublime pelo VSCode, é bem completo, boa parte dos plugins do sublime também se encontra para VSCode. O Git integrado como também o debugger são grandes adições e pesam bastante.

Recomendo que testem também e comentem ai o que acharam.

# Atualização 

PS: Realmente ja tem uns 15 dias que só estou usando o VsCode e estou curtindo. As vezes bate uma saudade do sublime mas por enquanto ta de boa.

PS2: As vezes o debugger não vai nem com reza, alguem ja teve algum problema assim?
