# Introdução

A ideia por trás de testes não é nova. Você não pode dormir bem se não tiver confiante que seu último commit não derrubou a aplicação toda.
Tendo sua aplicação coberta por testes, lhe dá mais confiança na estabilidade de sua aplicação. Isso é tudo.

Na maioria dos casos os testes não garantem que a aplicação funciona 100%. Você não pode prever todos os cenários possiveis e situações excepcionais em apps complexos. Mas você pode cobrir com testes as partes mais importantes de suas aplicações e pelo menos ter a certeza que elas funcionam como o previsto.

Há muitas maneiras de testar sua aplicação. O paradigma mais popular é o [Teste de Unidade](http://pt.wikipedia.org/wiki/Teste_de_unidade). Para aplicações web, testar o Controller ou o Model isoladamente não comprova que sua aplicação está funcionando. Para testar sua aplicação como um todo, você deve escrever testes funcionais ou testes de aceitação.

O Framework de testes Codeception distingue estes niveis de teste, de uma forma criativa, você tem ferramentas para escrever testes de unidade, funcionais e de aceitação em uma única maneira.

Vamos rever os parádigmas de testes listados em uma ordem reversa.

### Testes de Aceitação (WebGuy)

Como o seu cliente, gerente, testador ou qualquer outra pessoa não-técnica, sabe que seu site está funcionando? Eles abrem o navegador, acessam o site, clicam nos links, preenchem os formulários e olham se as páginas apresentam os resultados esperados. Eles não tem nenhuma ideia de framework, banco de dados, servidor web ou linguagem de programação que você está usando, se eles acharem algum comportamento impróprio, eles iráo criar um report de bug. Essas pessoas não tem ideia por que a aplicação não funciona como o esperado.

Testes de aceitação podem cobrir cenários padrões mas complexos de uma perspectiva de usuário. Com os testes de aceitaçao você pode ficar confiante que se aqueles usuários seguirem cenários definidos, não vão ter erros.

O Codeception provê a emulação do navegador, oferecido pelo [Mink](http://mink.behat.org), para escrever e executar testes de aceitação. Eles podem ser feitos com ferramentas como o **Selenium** mas com o Mink é mais flexivel.

Por favor, note que **qualquer site** pode ser coberto com testes de aceitação. Mesmo se você usar um CMS muito personalizado ou um framework.

#### Exemplo de teste de aceitação

``` php
<?php
$I = new WebGuy($scenario);
$I->amOnPage('/');
$I->click('Sign Up');
$I->submitForm('#signup', array('username' => 'MilesDavis', 'email' => 'miles@davis.com'));
$I->see('Thank you for Signing Up!');
?>
```

#### Prós

* pode ser executado em qualquer website.
* pode testar javascript e requisições ajax.
* pode ser mostrado para seus clientes e gerentes.
* o mais estável: o menos afetado por alterações no código fonte ou tecnologias.

#### Contra

* poucos checks podem levar a resultados com falso-positivo.
* o mais lento: requer execução do navegador e repopulação do banco de dados.
* yep, ele é realmente lento.

### Testes Funcionais (TestGuy)

Vamos dizer que sua aplicação é testada por uma pessoa tecnicamente avançada. Ela vai abrir o navegador, entrar no site, clicar nos links e enviar formulários, mas quando um erro ocorre ela reporta para você a exceção que pegou ou verifica no banco de dados o valor esperado. Essa pessoa ja conhece alguns aspectos da sua aplicação e por conhecer estes, seus testes podem cobrir mais detalhes técnicos.

Testes funcionais são executados sem a emulação do navegador. Para os testes funcionais você emula a requisição web e envia isso para a sua aplicação. Ele deve retornar para você uma resposta, você pode fazer assertions com essa resposta e também acessar valores internos da aplicação.
Para os testes funcionais sua aplicação deve estar preparada para ser executada em modo de testes. Para os frameworks como o Symfony1, Symfony2 ou o Zend é fácil iniciar a aplicação em modo de teste.

O Codeception provê conectores para os mais populares frameworks de PHP, mas você pode escrever o seu próprio.

#### Exemplo de teste funcional

``` php
<?php
$I = new TestGuy($scenario);
$I->amOnPage('/');
$I->click('Sign Up');
$I->submitForm('#signup', array('username' => 'MilesDavis', 'email' => 'miles@davis.com'));
$I->see('Thank you for Signing Up!');
$I->seeEmailSent('miles@davis.com', 'Thank you for registration');
$I->seeInDatabase('users', array('email' => 'miles@davis.com'));
?>
```

#### Prós

* como os testes de aceitação mas mais rápido.
* pode prover relatórios mais detalhados.
* você pode mostrar este código para os gerentes e clientes.
* bastante estável: somente alterações maiores em código ou alterações para outro framework podem quebra-los. 

#### Contra

* javascript e ajax não podem ser testadas.
* por emulação de navegador você pode ter mais resultados falso-positivo.
* requer um framework.

### Testes de Unidade (CodeGuy)

Somente os desenvolvedores entendem como e o que é testado aqui. Ele pode ser tanto de unidade ou testes integrados mas são limitados a verificar um método por teste.

A única diferença entre os testes de unidade e os testes integrados é que os testes de unidade podem ser executados em totalmente isolados. Todos as outras classes ou métodos podem ser substituidas por stubs.

O Codeception é criado no topo do [PHPUnit](http://www.phpunit.de/). Se você te experiência escrevendo testes unitários com o PHPUnit pode continuar fazendo como já o faz, Codeception suporta testes padrão PHPUnit.

Mas o Codeception provê algumas boas ferramentas para fazer seus testes unitários simples e limpos. Mesmo os desenvolvedores inexperientes devem entender o que é testado e como. Requisitos e códigos podem ser alterados rapidamente e os testes unitários deve ser atualizados para atender os requisitos. Quanto melhor você entender o cenário de teste, mais rápido você pode atualizá-lo para o novo comportamento.

#### Exemplo de teste integrado

``` php
<?php
// nos estamos testando o método publico da classe User.
// E requerido o user_id e um array de parametros.

$I = new CodeGuy($scenario);
$I->testMethod('User.update');
$I->haveStubClass($unit = Stub::make('User'));
$I->dontSeeInDatabase('users', array('id' => 1, 'username' => 'miles'));
$I->executeTestedMethodOn($unit, 1, array('username' => 'miles'));
$I->seeMethodInvoked($unit, 'save');
$I->seeInDatabase('users', array('id' => 1, 'username' => 'miles'));
?>
```

#### Prós

* muito rápido (bem, neste exemplo você precisa de repopulação de banco de dados).
* pode cobrir funcionalidades raramente utilizadas.
* pode testar a estabilidade do core da aplicação.
* você somente pode ser considerado um bom desenvovledor se você escrever estes :)

#### Contra

* não testa conexão entre unidades.
* mais instável: muito sensivel com alterações de código.

## Conclusão

Apesar da grande popularidade do TDD, poucos desenvolvedores PHP escrevem testes automatizados para suas aplicações. O framework Codeception foi desenvolvido para fazer o teste realmente divertido. Ele permite escrever testes unitários, funcionais, integrados e de aceitação em um mesmo estilo.

Ele pode ser chamado de um frameowrk de BDD. Todos os testes do Codeception são escritos de uma maneira descritiva. Simplesmente olhandos para o corpo do teste você pode entender claramente o que esta sendo testado e como ele é realizado. Todo teste complexo com muitas assertions são escritos em uma simples PHP DSL.