![Boleto Library Logo](http://a6.sphotos.ak.fbcdn.net/hphotos-ak-ash3/556061_10151041426758007_738498480_n.jpg)

# TOPICOS - Boleto Library PHP  
1. [Como Usar e Integrar esta Biblioteca à Sua Aplicação](#1-como-usar--testar-a-biblioteca)
2. [Reportando Bugs, Pedindo Ajuda e Fazendo Sugestões](#2-reportando-bugs-pedindo-ajuda-e-fazendo-sugest%C3%95es)
3. [Implementando novos Bancos e Carteiras](#3-implementando-novos-bancos-e-carteiras)
4. [Contribuindo com a Biblioteca Principal](#4-contribuindo-com-c%C3%93digo-em-geral)
5. [Testes de Unidades (Simple Test)](#5-testes-de-unidades-simple-test)
6. [Quem Usa Esta Biblioteca](#6-quem-usa-esta-biblioteca)

## 1. COMO USAR E INTEGRAR ESTA BIBLIOTECA À SUA APLICAÇÃO

**_1.1_** Vá até a pasta pública do seu servidor e baixe a cópia mais recente desta biblioteca
com o seguinte comando:  
`$ git clone --branch 1.x-dev https://github.com/drupalista-br/boleto.git boleto-lib`  

ou faça o Download da última versão estável em https://github.com/drupalista-br/Boleto/tags

***

**_1.2_** Instale pelo menos um plugin de um banco com os seguintes comandos:  

1. `$ cd boleto-lib`  
2. `$ cd bancos`  
3. `$ git clone --branch 1.x-1.x-dev https://github.com/USUARIO/Boleto-XXX.git XXX`  

Onde XXX deverá ser substituido pelo código do Banco e USUARIO ser substituido pelo nome do usuário
que é o mantenedor do plugin do banco.

ou faça o Download da última versão estável do plugin em https://github.com/drupalista-br/Boleto/tree/1.x-dev/bancos e:  
         
1. Extraia o arquivo baixado em `../boleto-lib/bancos`
2. Renomeie a pasta extraida com código do Banco ao qual o plugin pertence.  
   Exemplo: `../boleto-lib/bancos/001` para o Banco do Brasil.

***

**_1.3_** Para integrar esta biblioteca a qualquer aplicação PHP você precisará usar apenas dois métodos.
São eles:
>Para mais informações sobre todos os métodos setters and getters disponíveis, acesse a
[Documentação do API](http://drupalista-br.github.com/Boleto/classes/Boleto.html)

<table>
  <tr>
    <td>Boleto::load_boleto($argumentos = array())</td>
    <td>Este método é usado para instanciar um novo objeto.<br>Note que este método é chamado estaticamente.</td>
  </tr>
  <tr>
    <td>output($render = TRUE)</td>
    <td>Este método gera o html do boleto.
      <br>
      <br>Se você passar o parâmetro FALSE então
      <br>somente a propriedade $boleto->output é populada mas o html não é gerado.<br>
          Neste caso use o método outputPropertyGetter() para salvar o output<br>
          em uma variável. Por exemplo:<br>
          $output = $boleto->outputPropertyGetter();
    </td>
  </tr>
</table>

* O seu código de integração deverá ter esta estrutura:

>Note que apesar de todos os possíveis valores dos argumentos estarem listados abaixo, nem sempre você precisa
enviar todos eles, veja a documentação de cada plugin para determinar o que obrigatoriamente precisa ser
enviado.  

> Faça o download do exemplo abaixo em https://gist.github.com/3167145  

```php
<?php

include_once '../boleto-lib/Boleto.class.php';

$argumentos = array(
  'bank_code' => '104',
  'agencia' => 1234,
  'agencia_dv' => '2',
  'conta' => 12345678901,
  'conta_dv' => 3,
  'valor_boleto' => '2952.95',
  'numero_documento' => '27030195',
  'endereco' => 'street name and number',
  'cidade_uf'=> 'city and state',
  'cedente'  => 'ABC Company Ltd',
  'sacado' => 'John Doe',
  'carteira' => 'A',
  'carteira_nosso_numero' => '80',
  'nosso_numero' => '12345678',
  'cpf_cnpj' => '000.000.000-00', 
  'endereco1' => 'street name and number',
  'endereco2' => 'city and state',
  'demonstrativo1' => 'Your text here',
  'demonstrativo2' => 'Your text here',
  'demonstrativo3' => 'Your text here',
  'instrucoes1' => 'Your text here',
  'instrucoes2' => 'Your text here',
  'instrucoes3' => 'Your text here',
  'instrucoes4' => 'Your text here',
  'data_vencimento' => '25-07-2011',
  'desconto_abatimento' => '0.00',
  'outras_deducoes' => '0.00',
  'mora_multa' => '0.00',
  'outros_acrescimos' => '50.55',
  'title' => 'My title',
  'local_pagamento' => 'Your text here',
  'especie' => 'Your value here',
  'quantidade' => 'Your value here',
  'valor_unitario' => 'Your value here',
  'especie_doc' => 'Your value here',
  'data_processamento' => 'dd/mm/yyy',
  'avalista' => 'Michael Jackson',
  'aceite'=> 'Your value here',
  'merchant_logo' => 'images/logo.jpg',
);

// Instancia o objeto.
$boleto = Boleto::load_boleto($argumentos);

// Verifica se tudo correu bem.
if (is_object($boleto)) {
  // Imprime o boleto.
  $boleto->output();
}
else {
  // Seu error handler pois algo deu errado.
}
```

Encontre exemplos na pasta `../boleto-lib/bancos/XXX/example.php`. Para ver a demonstração acesse:  

`http://localhost/boleto-lib/bancos/XXX/example.php`

Onde XXX é o código do banco.

* Outro exemplo de como usar o seu próprio html template customizado:

>Faça o download deste example em https://gist.github.com/3167695  

```php
<?php

include_once 'boleto-lib/Boleto.class.php';

$argumentos = array(
  // Argumentos aqui.
);

// Instancia o objeto.
$boleto = Boleto::load_boleto($argumentos);

// Verifica se tudo correu bem.
if (is_object($boleto)) {
  // Use o arquivo boleto.tpl.php como exemplo para criar o seu template customizado.
  $settings = array('template' => '/home/minha_pasta/meu_layout_template.php');

  // Informa ao objeto a localização do seu template customizado.
  $boleto->settingsPropertySetter($settings);

  // Imprime o boleto.
  $boleto->output();
else {
  // Seu error handler pois algo deu errado.
}
```

### Antes de instanciar um objeto talvés seja interessante a sua aplicação verificar se:

1. Existe pelo menos um plugin de banco instalado
2. Os plugins de bancos presentes estão instalados corretamente e
3. Saber quais são os plugins de bancos que estão presentes e corretamente instalados.

Para isto existe o método `Boleto::installedPlugins()` que retorna uma array com os códigos dos bancos
os quais possuem plugin corretamente instalados.

>Note que este método deve ser chamado estáticamente.

## 2. REPORTANDO BUGS, PEDINDO AJUDA E FAZENDO SUGESTÕES

Abra chamados em https://github.com/drupalista-br/boleto-lib/issues

## 3. IMPLEMENTANDO NOVOS BANCOS E CARTEIRAS

>NOTA: Caso você queira alterar ou adicionar uma nova carteira a um plugin de banco já existente, então
vá direto para o passo 4.6.

**_3.1_** Dentro da pasta `../boleto/bancos` crie uma subpasta e a nomeie com o código
do banco que você irá implementar. Por exemplo:  

`../boleto/bancos/237`  

***

**_3.2_** Crie os seguintes arquivos dentro da subpasta que acabou de criar:
       
<table>
    <tr>
        <td>Obrigatório</td>
        <td>Banco_XXX.php</td>
        <td>Onde XXX é o código do banco</td>
    </tr>
    <tr>
        <td>Obrigatório</td>
        <td>logo.jpg</td>
        <td>Logo marca do banco</td>
    </tr>
    <tr>
        <td>Obrigatório</td>
        <td>README.txt ou README.md</td>
        <td>Instruções sobre a formatação dos campos do Boleto para este banco. 
Pode-se usar README.md ao invés de README.txt. Saiba mais sobre Markdown em http://github.github.com/github-flavored-markdown
</td>
    </tr>
    <tr>
        <td>opcional</td>
        <td>layout.tpl.php</td>
        <td>Se este arquivo existir então o template padrão será desconsiderado e este template 
será usado. Veja a implementação do Banco do Brasil como exemplo</td>
    </tr>
    <tr>
        <td>opcional</td>
        <td>style.css</td>
        <td>Mesmo caso do layout.tpl.php. Dê uma olhada na implementação do Banco do Brasil como exemplo</td>
    </tr>
    <tr>
        <td>obrigatório</td>
        <td>example.php</td>
        <td>Não é obrigatório adicionar código de exemplo, mas este arquivo precisa existir, mesmo que vazio.
        Este arquivo serve como "use case" para as pessoas poderem ver como fica o boleto gerado por seu plugin
        acessando `http://localhost/boleto-lib/bancos/XXX/example.php`. Onde XXX é o código do
        banco.

        Veja o exemplo do plugin da Caixa Econômica Federal.

        </td>
    </tr>
    
    
    <tr>
        <td>obrigatório</td>
        <td>unit-testing/simpletest.php</td>
        <td>Veja exemplo de código logo abaixo.</td>
    </tr>
</table> 

    
***

**_3.3_**  O arquivo `unit-testing/simpletest.php` deverá conter no mínimo o seguinte código:

>Faça o download do exemplo abaixo em https://gist.github.com/3167313  

```php
<?php
/**
* @file
* Unit testing.
*/

require_once "../../../unit-testing/boleto.test.php";

class TestOfXXX extends BoletoTestCase{
  protected $mockingArguments;

  function mockingArguments() {
    $this->mockingArguments = array(
      array(
        // Argumentos do Primeiro test case.
      ),
      array(
        // Argumentos do Segundo test case.
      ),
      // E assim por diante. Dê uma olhada no simpletest.php do
      // Banco do Brasil para um exemplo com mais de um test case.
    );
  }
}
```

Onde XXX em TestOfXXX é o código do banco.  

***

**_3.4_** Na classe Banco_XXX que acabara de criar você precisa implementar os seguintes métodos:  

<table>
    <tr>
        <td>Obrigatório</td>
        <td>setUp()</td>
        <td>Você precisa no mínimo popular a propriedade $bank_name com o nome do banco.</td>
    </tr>
    <tr>
        <td>Obrigatório</td>
        <td>febraban_20to44()</td>
        <td>A linha digitável e o código de barras são calculados com base num número com 44 digitos
        chamado de especificação febraban. Veja abaixo como este número é constituido.  
        <br><br>
        As posições 1 a 19 é padrão para todos os bancos. As posições 20 a 44 é livre para os bancos
        armazenem as informações que quizerem e na forma que quizerem.  
        <br><br>
        Assim, este método "febraban_20to44()" deverá construir este número com total de 25 digitos, de acordo
        com as espeficações das carteiras do banco, e armazena-lo na propriedade $this->febraban['20-44'].  
        </td>
    </tr>
    <tr>
        <td>opcional</td>
        <td>custom()</td>
        <td></td>
    </tr>
    <tr>
        <td>opcional</td>
        <td>outputValues()</td>
        <td></td>
    </tr>
</table>
    
Dê uma olhada nas implementações já existentes na pasta `../boleto-lib/bancos` para usar como exemplo.

***

### Especificação do Número Febraban.
>Este número é a base para calcular a linha digitável e o código de barras.  

>Você só precisa se preocupar em calcular as posições 20 ao 44.

>As posições 20 a 44 é a única coisa que diferencia boletos de um banco para outro ou mesmo de
uma carteira para outra de um mesmo banco.

<table>
    <tr>
        <td>Posição</td>
        <td>Quant. de Dígitos</td>
        <td>Descrição</td>
    </tr>
    <tr>
        <td>01-03</td>
        <td>3</td>
        <td>Código do banco sem o digito</td>
    </tr>
    <tr>
        <td>04-04</td>
        <td>1</td>
        <td>Código da Moeda (9-Real)</td>
    </tr>
    <tr>
        <td>05-05</td>
        <td>1</td>
        <td>Dígito verificador do código de barras</td>
    </tr>
    <tr>
        <td>06-09</td>
        <td>4</td>
        <td>Fator de vencimento</td>
    </tr>
    <tr>
        <td>10-19</td>
        <td>10</td>
        <td>Valor Nominal do Título</td>
    </tr>
    <tr>
        <td>20-44</td>
        <td>25</td>
        <td>Campo Livre. Reservado aos bancos.</td>
    </tr>
</table>
    
***

**_3.5_** No arquivo `Banco_XXX.php` você deverá criar uma classe chamada `Banco_XXX`  que extends Boleto.  

>Faça o download do exemplo abaixo em https://gist.github.com/3167333  

>A sua implementação deverá conter no mínimo o seguinte estrutura de código:

```php
<?php
 /**
 * This code is released under the GNU General Public License.
 * See COPYRIGHT.txt and LICENSE.txt.
 *
 * @author Fulano de Tal <fulanodetal@servidor.com>
 */

class Banco_XXX extends Boleto{
  function setUp(){
    $this->bank_name  = 'Nome do Banco Sendo Implementado';
  }

  function febraban_20to44() {
    // Calcule as posições 20 a 44 do número febraban de acordo com
    // os argumentos em $this->arguments e com as regras da(s)
    // carteira(s) do banco.
    // ...

    // Salve o número com 25 digitos na propriedade febraban['20-44'].
    $this->febraban['20-44'] = $numero_calculado;
  }
}  
```

Onde XXX em Banco_XXX é o código do banco sendo implementado.

***
**_3.6_** Recomenda-se que você nomeie o branch do seu repositório para 1.x-1.x-dev ao invés de master.  

Ou seja, o primeiro 1.x refere-se à versão da biblioteca boleto a qual o seu plugin suporta e o segundo 1.x
refere-se à versão do seu plugin.

***

**_3.7_** Uma vez que fizer o push dos seus commits, crie também marcações ( tags ) das versões estáveis do seu
plugin. Por exemplo:  

1. `$ git tag v1.x-1.0 -m "Beta1"`  
2. `$ git push origin v1.x-1.0`  

***

**_3.8_** Feito o release ( tag ), acesse https://github.com/drupalista-br/Boleto/issues
e crie um issue solicitando a inserção do link do seu novo plugin na nossa listagem de plugins em
https://github.com/drupalista-br/Boleto/tree/1.x-dev/bancos .

Não esqueça de informar o link para as tags do seu repositório.

## 4. CONTRIBUINDO COM A BIBLIOTECA PRINCIPAL  

Leia também:
>1. Como Forkear um Repositório e Solicitar Pull Requests no Github nos links http://help.github.com/fork-a-repo e
http://help.github.com/send-pull-requests
   
>2. Mais informações sobre as propriedades e métodos disponíveis na
[Documentação do API](http://drupalista-br.github.com/Boleto/classes/Boleto.html)

***

**_4.1_** Você deverá seguir o padrão Doxygen ao comentar o seu Código:  
   http://www.stack.nl/~dimitri/doxygen/docblocks.html  

***

**_4.2_** Acesse https://github.com/drupalista-br/boleto e clique e "Fork". Feito isto o github criará um
novo repositório em sua conta contendo uma cópia do repositório Boleto.

***

**_4.3_** Agora baixe o código de sua cópia forkeada com o seguinte comando:  

`$ git clone --branch 1.x-dev git@github.com:USUARIO/boleto.git boleto-lib`  

Onde USUARIO deverá ser substituido pelo seu usuario no Github.  

***

**_4.4_** Uma vez que você fizer as modificações / inserções / correções no código, então você deverá atualizar
o seu repositório forkeado. Aqui vão os comandos:

`$ git add . `  
`$ git commit -m "Sua mensagem explicando o que foi feito"`  
`$ git push origin 1.x-dev`  

A partir de agora o seu repositório forkeado possue um código diferente do repositório Boleto o qual você
inicialmente fez uma cópia.

O próximo passo mostra como mergir as suas alterações para o repositíro Boleto.

***

**_4.5_** Acesse o seu reposório forkeado no Github e clique em "Pull Request".  

O Pull Request irá criar automaticamente um issue solicitando que o seu código seja mergido.

Uma vez que o seu código for analisado, uma das seguintes ações poderão ser tomadas:  

1. O seu pull request é aceito e o seu código é mergido ou
2. Poderá ser solicitado que você faça algum ajuste antes que o pull request seja aceito. Neste caso basta repetir
o processo do passo 4.3 e o github automaticamente insere o seu novo commit no pull request.
3. Ou o seu pull request é recusado e o issue é fechado. Neste caso será argumentado as razões da recusa.

***

**_4.6_** Caso você queira por exemplo alterar ou adicionar uma nova carteira à um plugin de banco já existente
você deverá seguir os passos 4.1 ao 4.5, entretanto no passo 4.2 você irá forkear o repositório do plugin do banco
em questão ao invés de forkear o repositório do Boleto.  

Vale lembrar ainda que o passo 4.3 deverá ser feito dentro da pasta `../boleto/bancos` e a subpasta do repositório
sendo baixado deverá ser renomeada para o código do banco.

## 5. TESTES DE UNIDADES (SIMPLE TEST)

Leia também

>1. O que é Simple Test:  
http://pt.wikipedia.org/wiki/SimpleTest e  
http://www.simpletest.org/en/first_test_tutorial.html

![Simpletest for Boleto PHP Library](http://a2.sphotos.ak.fbcdn.net/hphotos-ak-ash4/394649_10151040968123007_358031888_n.jpg)

***

**_5.1_** Como testar

1. Faça o download da biblioteca [Simpletest](http://www.simpletest.org/en/download.html) e
extraia o arquivo compactado em `../boleto-lib/unit-testing/simpletest`  
2. No seu navegador acesse `http://localhost/boleto-lib/bancos/XXX/unit-testing/simpletest.php`.
Onde XXX é o código do banco.  

   Exemplo:  
   `http://localhost/boleto-lib/bancos/104/unit-testing/simpletest.php`

**_5.2_** Onde e Como escrever Testes de Unidades

O código dos Testes de Unidades estão alojados em dois locais. São eles:

1. No arquivo de testes principal em `../boleto-lib/unit-testing/boleto.test.php` e  
2. Nos arquivos localizados nas pastas de cada plugin, `../boleto-lib/bancos/XXX/unit-testing/simpletest.php`.
Onde XXX é o código do banco.

Provavelmente não seja necessário, mas caso queira adicionar testes de unidades em um plugin de banco, você deverá
adicionar o seu código de testes, além dos elementos obrigatório do item 3.3, em
`../boleto-lib/bancos/XXX/unit-testing/simpletest.php`

Para que os seus métodos de teste sejam chamados você deverá colocar o prefixo `test` no nome de seus métodos.

Por Exemplo:

```php
<?php
function testNomeExplicativoDoMeuTesteNoFormatoDesteExemplo() {  
   // testes aqui.  
}
```

Mais exemplos em http://www.simpletest.org/en/first_test_tutorial.html e também no arquivo
`../boleto-lib/unit-testing/boleto.test.php`

## 6. QUEM USA ESTA BIBLIOTECA

1. Commerce Boleto para Drupal Commerce - http://drupal.org/project/commerce_boleto

Abra um chamado em https://github.com/drupalista-br/boleto-lib/issues e fale a respeito do seu
projeto para que ele possa ser mencionado nesta sessão.
