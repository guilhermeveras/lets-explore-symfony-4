# Crie seu próprio controlador (Controller)

[Link para o video](https://www.codereviewvideos.com/course/symfony-4-beginners-tutorial/video/symfony-make-controller)


No final do vídeo anterior, criamos uma única rota (Route) que, quando acessada, renderizaria o conteúdo do nosso Template
(por exemplo, o hello_page.html.twig) e o retorna como conteúdo.

Embora não tenhamos necessidade de criar a nossa própria classe Controller, ou um método para o mesmo
(em versões anteriores do Symfony mais freqüentemente chamadas de Action) para fazer isso funcionar,
usamos o Controller fornecido pelo Symfony (TemplateController) e Action (templateAction) em background.

Eu também mencionei que em 99% dos casos, você provavelmente não usaria isso dessa maneira, no mundo real.

Ainda assim, é interessante saber sobre este recurso, além disso nos ajuda a começar a entender tais conceitos, para realmente
começarmos.

Vamos agora criar nossa própria classe Controller, e nesse controlador criaremos o nosso primeiro método.

## Criando uma Controller Class
Há várias maneiras de criar uma nova classe Controller.

Podemos:
* Copiar e colar código do [docs](http://symfony.com/doc/current/controller.html)
* Digitar manualmente tudo da memória
* Criar e usar um [Live Template](https://www.codereviewvideos.com/course/phpstorm-shortcuts/video/phpstorm-live-templates-are-epic)
* Copiar / colar uma classe de controlador de um projeto existente.

E assim por diante.
Ou, talvez, mais fácil, podemos fazer um *make*:

```terminal
php bin/console

# ...

 make
  make:auth                               Creates an empty Guard authenticator
  make:command                            Creates a new console command class
  make:controller                         Creates a new controller class
  make:entity                             Creates a new Doctrine entity class
  make:form                               Creates a new form class
  make:functional-test                    Creates a new functional test class
  make:serializer:encoder                 Creates a new serializer encoder class
  make:subscriber                         Creates a new event subscriber class
  make:twig-extension                     Creates a new Twig extension class
  make:unit-test                          Creates a new unit test class
  make:validator                          Creates a new validator and constraint class
  make:voter                              Creates a new security voter class
```

Isso significa que podemos digitar *php bin/console make:controller* e seguir o prompt para criar um controlador básico e uma ação &#39;demo&#39;.

Vamos tentar:

```terminal
php bin/console make:controller

 Choose a name for your controller class (e.g. BravePuppyController):
 > WelcomeController

 created: src/Controller/WelcomeController.php

  Success!

 Next: Open your new controller class and add some pages!  
```

Agora, se olharmos em nosso aplicativo, no diretório *src/Controller*, nós realmente vemos um arquivo *WelcomeController.php*.

O conteúdo do *WelcomeController.php* é:

```terminal
<?php

namespace App\Controller;

use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
use Symfony\Bundle\FrameworkBundle\Controller\Controller;
use Symfony\Component\HttpFoundation\Response;

class WelcomeController extends Controller
{
    /**
     * @Route("/welcome", name="welcome")
     */
    public function index()
    {
        // replace this line with your own code!
        return $this->render('@Maker/demoPage.html.twig',
          'path' => str_replace($this->getParameter('kernel.project_dir').'/', '', __FILE__)
        ]);
    }
}
```

Tomei a leve liberdade de ajustar o formato da declaração de retorno para facilitar a leitura.
O código gerado real será em uma única linha.

Há um monte de coisas interessantes que acontecem neste código e, embora seja legal mergulhar em cada parte, isso também tornará as coisas mais complexas do que precisam ser neste momento.

O que vou sugerir é que façamos duas mudanças rápidas no código gerado:

* Remova a linha  *use Symfony\Component\HttpFoundation\Response;*
* E substitua *extends Controller* por *extends AbstractController*

Remover *use Symfony\Component\HttpFoundation\Response*; é bastante fácil.
Como este componente não será usado podemos simplesmente excluir toda a linha.

Não tenho certeza por que esse resultado é fornecido, pode ser algo não exista em versões futuras.

Em seguida, mude:
*use Symfony\Bundle\FrameworkBundle\Controller\Controller;*

para:
*use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;*

E atualize:
*class WelcomeController extends Controller*

para:
*class WelcomeController extends AbstractController*

E por fim:
*return $this->render('@Maker/demoPage.html.twig', [ 'path' => str_replace($this->getParameter('kernel.project_dir').'/', '', __FILE__) ]);*

para:
*return $this->render('welcome/index.html.twig');*

Embora não seja algo que vamos cobrir imediatamente, esta é uma recomendação das [Boas Práticas para criar Controllers no Symfony](https://symfony.com/doc/current/best_practices/controllers.html).
Vamos abordar o que esta mudança faz um pouco mais tarde.

## Routin 'Tootin'

Para o resto deste vídeo, iremos nos concentrar nessa nova rota que agora está disponível em nosso aplicativo, graças à Anotação (Annotation) de roteamento gerada para nós:

```terminal
    /**
     * @Route("/welcome", name="welcome")
     */
    public function index()
```

Esta *routing annotation* mapeia uma rota ou *path* de */welcome* para o index method da Action.

Geek Trivia: Se você vier de Symfony 2 ou Symfony 3, você pode se perguntar o que aconteceu com o sufixo *Action*, como no *indexAction*. Isso é possível porque nosso *WelcomeController* é um serviço [(service)](https://symfony.com/doc/current/controller/service.html).

Podemos verificar novamente se esta rota está corretamente configurada executando o comando *bin/console debug:router*:

```terminal
bin/console debug:router
 -------------------------- -------- -------- ------ -----------------------------------
  Name                       Method   Scheme   Host   Path                               
 -------------------------- -------- -------- ------ -----------------------------------
  welcome                    ANY      ANY      ANY    /welcome                           
  _twig_error_test           ANY      ANY      ANY    /_error/{code}.{_format}           
  _wdt                       ANY      ANY      ANY    /_wdt/{token}                      
  _profiler_home             ANY      ANY      ANY    /_profiler/                        
  _profiler_search           ANY      ANY      ANY    /_profiler/search                  
  _profiler_search_bar       ANY      ANY      ANY    /_profiler/search_bar              
  _profiler_phpinfo          ANY      ANY      ANY    /_profiler/phpinfo                 
  _profiler_search_results   ANY      ANY      ANY    /_profiler/{token}/search/results  
  _profiler_open_file        ANY      ANY      ANY    /_profiler/open                    
  _profiler                  ANY      ANY      ANY    /_profiler/{token}                 
  _profiler_router           ANY      ANY      ANY    /_profiler/{token}/router          
  _profiler_exception        ANY      ANY      ANY    /_profiler/{token}/exception       
  _profiler_exception_css    ANY      ANY      ANY    /_profiler/{token}/exception.css   
  hello_page                 ANY      ANY      ANY    /hello                             
 -------------------------- -------- -------- ------ -----------------------------------
```

Não é imediatamente óbvio, mas a nova rota está no topo, e nossa rota existente está na parte inferior.

Se você ainda não o fez, vá para http://127.0.0.1:8000/welcome e confira a sua nova e brilhante saída do controlador:

![Symfony 4 Make Controller Output](https://codereviewvideos.com/blog/wp-content/uploads/2018/01/symfony-4-make-controller-output.png)

Por enquanto, tudo bem.

## Mudando as coisas

Vamos definir a nossa nova ação de controlador para responder na raiz do nosso site.
Em outras palavras, vamos configurar este controlador para responder a */*.

Isso é facilmente alcançado. Precisamos apenas atualizar a anotação de roteamento:

```terminal
  /**
   * @Route("/", name="welcome")
   */
   public function index()
```

Não hesite em verificar novamente a saída de *php bin/console debug:route* ao fazer as alterações.
Se você vir a mudança na saída do seu console, você sabe que o Symfony entendeu e aceitou a sua mudança.

Em seguida, em vez de renderizar o modelo gerado, vamos criar um novo nosso.

Vamos usar o Bootstrap 4. Sinta-se livre para mudar isso para qualquer outro framework CSS que você goste.
Ou simplesmente pule o CSS, se os visuais não são sua xícara de chá.

Começaremos criando um novo modelo dentro de um subdiretório do diretório de *templates*:

```terminal
mkdir templates/welcome
```

E no novo diretório *templates/welcome* vamos criar um novo modelo, *index.html.twig*:

```terminal
touch templates/welcome/index.html.twig
```

Esse nome corresponde ao do nosso método Controller, mas não é necessário.
Em projetos maiores, uma convenção de nomeação como essa tornará sua vida um pouco mais fácil.
Você pode usar o que quiser, é claro.

Bootstrap ofereça alguns exemplos de templates, e eu fiz a seguinte personalização:

```terminal
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="">

    <title>Let's Explore Symfony 4</title>

    <!-- Bootstrap core CSS -->
    <link rel="stylesheet"
          href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta.3/css/bootstrap.min.css"
          integrity="sha384-Zug+QiDoJOrZ5t4lssLdxGhVrurbmBWopoEl+M6BdEfwnCJZtKxi1KgxUyJq13dy"
          crossorigin="anonymous">
</head>

<body>
    <header>
        <nav class="navbar navbar-expand-sm navbar-dark bg-dark">
            <div class="container">

                <a class="navbar-brand" href="#">Home</a>

                <div class="collapse navbar-collapse">
                    <ul class="navbar-nav mr-auto">
                        <li class="nav-item">
                            <a class="nav-link" href="/hello">Hello Page</a>
                        </li>
                    </ul>
                </div>
            </div>

        </nav>
    </header>

    <main role="main" class="container main">
        <div>
            <h1>Let's Explore Symfony 4</h1>
            <p class="lead">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Cras rutrum sapien mauris, venenatis
                facilisis neque tincidunt vel. Maecenas vel felis vel turpis scelerisque eleifend. Fusce nec purus egestas,
                efficitur nisi ac, ultrices nulla. Pellentesque eu mollis tortor, in mollis nisl. Maecenas rhoncus quam non
                lacinia mollis.</p>
        </div>
    </main>
</body>
</html>
```

Você deve visualizar algo assim:

![Symfony 4 Lets Explore](https://codereviewvideos.com/blog/wp-content/uploads/2018/01/lets-explore-symfony-4.png)

Seria bom empurrar o h1 para baixo alguns pixels. Podemos adicionar isso como um elemento de *style* direto ao template, mas uma maneira melhor é mover estilos para um arquivo separado.

Precisamos que nosso(s) arquivo(s) de estilo sejam acessíveis ao público, então isso significa que eles precisam ficar no diretório público (public).

```terminal
mkdir public/css
touch public/css/custom-style.css
```

Lembre-se, touch é apenas uma maneira nerd de criar um novo arquivo vazio com o nome dado, diretamente do terminal (CLI).
Sinta-se livre para criar o diretório e o arquivo do seu IDE.

Em vez de estilizar a tag *h1* diretamente, vou *&quot;subir&quot;* para o elemento principal *main* e aplicar o meu estilo na classe *.main*:

```terminal
.main {
    margin-top: 20px;
}
```

Mas só isso não funciona. Precisamos contar o nosso template sobre a nossa folha de estilos.

Eu vou adicionar minha entrada de folha de estilo personalizada logo acima do fechamento da tag *head*.
Isso garante que o Bootstrap CSS seja executado primeiro, então meus estilos personalizados são aplicados &quot;no topo&quot;.

```terminal
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="">

    <title>Let's Explore Symfony 4</title>

    <!-- Bootstrap core CSS -->
    <link rel="stylesheet" href="{{ asset('css/custom-style.css') }}" />
</head>
```

Observe que a folha de estilos não está usando um caminho *hardcoded*. Em vez disso, podemos usar uma *asset helper function* para permitir que *Asset Component* se ocupe de descobrir onde nossos recursos estão localizados, o que é super útil quando nossas configurações de modelo são mais complexas.

Ao usar o *symfony/website-skeleton*, nós já temos acesso a uma biblioteca de recursos para o gerenciamento de ativos (assets) do front-end mais complexo: [Webpack Encore](https://symfony.com/doc/current/frontend.html). Ainda não estamos usando isso.
Você nem sempre precisa de tudo que o framework fornece, então não sinta que você precisa usar todas as coisas brilhantes.
Simplesmente use os bits que você precisa.

## Tente você mesmo

[As melhores práticas do Symfony 4](https://symfony.com/doc/current/frontend.html) para gerenciamento de assets pelo frontend (pense: JS e CSS, entre outros) é usar o Webpack Encore.

Tente usar [a documentação](http://symfony.com/doc/current/frontend.html#encore-toc), trocando de nossa abordagem CSS atual para usar o Webpack Encore.

Quais outras ferramentas / software que você precisava instalar?

Valeu o esforço extra?

Considere deixar um comentário abaixo compartilhando suas opiniões e experiências.

## Melhores Links

Na nossa barra de navegação (navbar), adicionamos alguns links:

```terminal
<nav class="navbar navbar-expand-sm navbar-dark bg-dark">
     <div class="container">

         <a class="navbar-brand" href="/">Home</a>

         <div class="collapse navbar-collapse">
             <ul class="navbar-nav mr-auto">
                 <li class="nav-item">
                     <a class="nav-link" href="/hello">Hello Page</a>
                 </li>
             </ul>
         </div>
     </div>
 </nav>
```

Nós temos o Home, que usa a raiz do site (*/*), e nossa página de hello no */hello*.

Você lembra que anteriormente mudamos o caminho da rota de */welcome* para */*, mas mantivemos o nome da rota como *welcome*?

O que isso significa na prática é que em vez de usar:

```terminal
<a class="navbar-brand" href="/">Home</a>
```

Nós ficaríamos melhor com:
```terminal
<a class="navbar-brand" href="{{ path('welcome') }}">Home</a>
```

Da mesma forma, para a Hello Page, ficaríamos melhor mudando para:

```terminal
<a class="nav-link" href="/hello">Hello Page</a>
```

para:

```terminal
<a class="nav-link" href="{{ path('hello_page') }}">Hello Page</a>
```

O que significa que nossa barra de navegação revisada parece assim:

```terminal
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
```

Ambos caminho *path* e *asset* são apenas algumas [das muitas funções disponíveis](https://symfony.com/doc/current/reference/twig_reference.html) dentro de seus templates Twig.
Alguns usarão mais do que outros.

Você pode testar o quão útil isso é alterando o *path* da sua *hello_page* para algo diferente de */hello*.
Assim que você fizer isso, e atualizar seu navegador, você verá as atualizações do link automaticamente para usar o novo caminho. Isso continuará funcionando, desde que você não altere o nome da rota. Se você alterar o nome, não se esqueça de atualizar quaisquer chamadas de função de *path* no seu(s) template(s).

## Sobre a página Hello

Um problema notório é que se navegarmos para a raiz do site (*/*), temos uma boa página Bootstrap 4 com nossa barra de navegação, um toque de estilo, e nos sentimos bem em relação a nós mesmos.

Em seguida, clicamos no link &quot;Hello Page&quot; e boom, sem estilos, sem barra de navegação e a única saída é acertar o botão Voltar no navegador.

Não seria melhor se nossa &quot;Hello Page&quot; e todas as páginas subseqüentes que adicionamos, reutilizaram o mesmo layout básico? Com ​​certeza, Kent.

Vamos explorar como podemos fazer exatamente isso, e muito mais, no mesmo próximo vídeo.
