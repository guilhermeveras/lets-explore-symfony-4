# Deixe Twig fazer o trabalho duro


No vídeo anterior, criamos (tecnicamente: geramos) a nossa primeira classe Controladora, juntamente com um único método de controle que processou e retornou nosso template *index.html.twig*.

Mantendo as coisas simples, colocamos tudo dentro de *templates/welcome/index.html.twig*.
Ao fazê-lo, criamos uma página da web de boa aparência para a raiz do nosso site (/), mas, ao visitar nossa outra rota (*/hello_page*), vimos uma página básica e não editada.

O que não queremos fazer é copiar/colar todo o layout e a barra de navegação de um template para outro, sempre que criamos um novo template ou atualizamos um existente. Isso seria uma má ideia.

Em vez disso, o que faremos é extrair essas partes comuns do nosso layout e, em seguida, criar blocos que podemos preencher pelo conteúdo de uma página. É muito mais fácil de ver na prática, então vamos mergulhar diretamente.

## Tudo sobre a base

Uma coisa que eu não mencionei no vídeo anterior, mas com *symfony/website-skeleton* ganhamos grátis o *templates/base.html.twig*

Este é um modelo muito simples:

```terminal
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>{% block title %}Welcome!{% endblock %}</title>
        {% block stylesheets %}{% endblock %}
    </head>
    <body>
        {% block body %}{% endblock %}
        {% block javascripts %}{% endblock %}
    </body>
</html>
```

É difícil perder todas essas block tags.

Um bloco nos permite definir áreas que podem ser alteradas.

Tomando o título do bloco como um exemplo, provavelmente queremos definir uma tag de título única para cada página em nosso site.
Podemos fazer isso definindo uma nova tag *{% block title%}* ... *{% endblock%}* em qualquer template que estenda esse template base.

Observe também que o *bloco de título* acima vem com um texto já configurado dentro das tags.
Se nenhum novo conteúdo for definido em um template filho, então o valor padrão, *Welcome!* será usado.

Você pode [ler mais sobre isso aqui](https://twig.symfony.com/doc/2.x/tags/extends.html).

Vamos extrair o HTML comum de nosso template *templates/welcome/index.html.twig* e mover isso para o arquivo *templates/base.html.twig*:

```terminal
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="">

    <title>{% block title %}Let's Explore Symfony 4{% endblock %}</title>

    <!-- Bootstrap core CSS -->
    <link rel="stylesheet"
          href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta.3/css/bootstrap.min.css"
          integrity="sha384-Zug+QiDoJOrZ5t4lssLdxGhVrurbmBWopoEl+M6BdEfwnCJZtKxi1KgxUyJq13dy"
          crossorigin="anonymous">
    <link rel="stylesheet" href="{{ asset('css/custom-style.css') }}" />
    {% block stylesheets %}{% endblock %}
</head>

<body>

<header>
    <nav class="navbar navbar-expand-sm navbar-dark bg-dark">
        <div class="container">

            <a class="navbar-brand" href="{{ path('welcome') }}">Home</a>

            <div class="collapse navbar-collapse">
                <ul class="navbar-nav mr-auto">
                    <li class="nav-item">
                        <a class="nav-link" href="{{ path('hello_page') }}">Hello Page</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
</header>

<main role="main" class="container main">
    {% block body %}{% endblock %}
</main>

{% block javascripts %}{% endblock %}
</body>
</html>
```

Acontece que a maior parte do nosso template foi genérico.

Poderemos adicionar o texto *h1* e &quot;lorum ipsum&quot; de volta através da tag *block body* do nosso template *welcome/index.html.twig*.

Entretanto, se atualizarmos a página no nosso navegador, não há mudança.

Symfony não pode ler mentes. Ainda não. Talvez no Symfony 5, quando implementarem a lógica da avançada série terminador T-1000.

Devemos informar o *welcome/index.html.twig* para *extends (extender)* o template *base.html.twig*.

Isso significa que precisamos atualizar o template *welcome/index.html.twig* para remover todas as coisas que acabamos de extrair.

```terminal
{% extends 'base.html.twig' %}

{% block body %}
    <div>
        <h1>Let's Explore Symfony 4</h1>
        <p class="lead">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Cras rutrum sapien mauris, venenatis
            facilisis neque tincidunt vel. Maecenas vel felis vel turpis scelerisque eleifend. Fusce nec purus egestas,
            efficitur nisi ac, ultrices nulla. Pellentesque eu mollis tortor, in mollis nisl. Maecenas rhoncus quam non
            lacinia mollis.</p>
    </div>
{% endblock %}
```

Tudo dentro da tag *block body* agora é exibido dentro da área definida como *block body* no template que estendemos.

Se você escrever incorretamente o nome do bloco, seu texto simplesmente não aparecerá.
O resto do template ainda será renderizado e todos os blocos com os nomes corretos serão exibidos.
Este é um erro fácil de acontecer, e pode ser confuso para rastrear.
Mas isso pode não ser tão problemático, dado que apenas temos quatro blocos atualmente, mas em estruturas de site maiores, é mais complexos e fácil de acontecer.

## Uma melhor Hello Page

Todo esse processo acima seria bem trabalhoso se tivéssemos um site como apenas uma página.

Felizmente, temos dois.

E agora que fizemos todo o &quot;trabalho duro&quot; para configurar o layout do nosso site, podemos aproveitar ao máximo isso alterando o *templates/hello_page.html.twig* e imediatamente obtendo os estilos legais e a barra de navegação e um titulo padrão de página, e assim por diante:

```terminal
{% extends 'base.html.twig' %}

{% block body %}
    Hello, CodeReviewVideos!
{% endblock %}
```

Tão fácil como isso, temos ambas as páginas sob um layout gerido de forma centralizada.

Nós podemos dar a este *hello_page* um *título* personalizado alterando o *block title*:

```terminal
{% extends 'base.html.twig' %}

{% block title %}Some custom title{% endblock title %}

{% block body %}
    Hello, CodeReviewVideos!
{% endblock %}
```

E, após a atualização da página, vemos nosso novo título de página.

Observe como eu usei o *endblock title*, mas para o *block body* eu acabei de usando o *endblock*?

Ambas as sintaxes são válidas. Normalmente, eu prefiro apenas o *endblock*, mas dar o nome do bloco específico no fechamento da tag pode ser útil para combinar tags em modelos mais completos.

Perceba que a ordem dos blocos não importa.

```terminal
{% block body %}
    Hello, CodeReviewVideos!
{% endblock %}

{% block title %}Some custom title{% endblock title %}

{% extends 'base.html.twig' %}
```

Mesmo que tudo seja &quot;de cabeça para baixo&quot;, sua página ainda irá renderizar corretamente.
A minha preferência é manter as tags na ordem em que aparecem no template pai, por simplicidade.

## O que é app?

Agora atenção: você pode pular esta seção inteira e ir para o próximo vídeo se você não estiver interessado em aumentar suas habilidades do Symfony geek. O que aprendemos aqui é bom saber, porém não é essencial.

Ainda aqui? Legal. Vamos tornar nossa &quot;Hello Page&quot; um pouco mais interessante.

Ao invés de hardcoding para &quot;Hello, CodeReviewVideos!&quot;, Não seria melhor passar um valor em tempo de execução?

Digamos, em vez de simplesmente acessar http://127.0.0.1:8000/hello, poderíamos acessar http://127.0.0.1:8000/hello?name=Chris e exibir &quot;Hello, Chris!&quot;.

Dado que não estamos usando uma classe Controller personalizada, ou um método de controle, parece que isso pode ser difícil de resolver.

No entanto, o Twig possui uma *app* variável implícita. E esta variável do *app* nos dá acesso a um monte de coisas interessantes, como o usuário atual (*app.user*), o ambiente, por exemplo, &#39;prod&#39;, &#39;dev&#39; (*app.environment*), a solicitação atual (*app.request*) e um [par de outras](https://symfony.com/doc/current/templating/app_variable.html).

Isso realmente não é imediatamente óbvio. Mas você pode ver isso por si mesmo usando outra função Twig útil: *dump*:

```terminal
{% extends 'base.html.twig' %}

{% block title %}Some custom title{% endblock title %}

{% block body %}

    {{ dump() }}

    Hello, CodeReviewVideos!
{% endblock %}
```

Agora atualize a página e você verá uma representação de um *Array Dumped* na tela contendo todas as variáveis ​​passadas para este render. No nosso caso, temos apenas essa variável de *app* implícito, mas em breve veremos mais aqui.

Isso está mostrando coisas mais avançadas do que você precisa, mas eu gosto de &quot;descascar&quot; as cortinas e dar um vislumbre do que está acontecendo, mesmo que você possa não entender (ou mesmo parcialmente, neste momento) qualquer uma destas coisas.

Conforme mencionado acima, se pudermos acessar a solicitação atual (*app.request*), podemos acessar os parâmetros da consulta para essa solicitação. Parâmetros de consulta são aqueles que você vê em um URL após o ponto de interrogação.

Se navegarmos em nossa página em http://127.0.0.1:8000/hello?name=Chris, expanda a saída de *dump* para:

*app > requestStack > requests > 0 > query > parameters*

Então você deve poder ver qualquer parâmetro de consulta de *name* que você definir:

![Symfony 4 Twig Dump Output](https://codereviewvideos.com/blog/wp-content/uploads/2018/01/twig-template-dump.png)

Vamos usar isso em nosso template.

```terminal
{% extends 'base.html.twig' %}

{% block title %}Some custom title{% endblock title %}

{% block body %}

    Hello, {{ app.request.query.get('name') }}!

{% endblock %}
```

E legal, vemos &quot;Hello, Chris!&quot; (ou o que você definiu), tudo embrulhado bem em nossos estilos de Bootstrap extravagantes.

Há uma desvantagem aqui.

E se nos ficarmos de mal humor e enviar um pedido para: http://127.0.0.1:8000/hello

Ou, ficamos preguiçosos com os dedos e escrevemos erroneamente o nosso parâmetro de consulta: http://127.0.0.1:8000/hello?nom=bob

Então vemos o menos atraente: &quot;Hello, !&quot;

Oh meu.

Bem, felizmente, Twig não nos da as costas aqui.

Podemos definir um padrão:

```terminal
{% extends 'base.html.twig' %}

{% block title %}Some custom title{% endblock title %}

{% block body %}

    Hello, {{ app.request.query.get('name') | default('CodeReviewVideos') }}!

{% endblock %}
```

Agora, se o parâmetro de consulta não estiver definido, ou com erros ortográficos, ou seja o que for, voltamos para um padrão legal: &quot;Hello, CodeReviewVideos!&quot;

Isso é bom, certo?

Bem, tipo isso.

Eu realmente não faria isso no mundo real.

Estou mostrando isso porque saber que você pode fazer isso é útil.

Mas na maioria das vezes, na maior parte do tempo no meu caso, você é muito mais propenso a querer/precisar passar nas variáveis ​​dos métodos do Controlador. Então, vamos fazer isso no próximo vídeo.
