# Instalação

**Phar**

Baixe o Codeception phar executável de  http://codeception.com/thanks

~~~
wget http://codeception.com/codecept.phar .
~~~

Preparar o diretório de testes e os arquivos de configuração

~~~
php codecept.phar bootstrap
~~~

**Composer**

Instale o Composer na raiz do seu projeto http://getcomposer.org/

Run

~~~
php composer.phar require "codeception/codeception:*"
~~~

De agora em diante o Codeception (com o PHPUnit instalado) pode ser executado como:

~~~
vendor/bin/codecept
~~~

Inicialize seu ambiente de testes com:

~~~
vendor/bin/codecept bootstrap
~~~

**Git**

Método de instalação alternativa para bugfixing, contribuição e hacking.

Clone do GitHub:

~~~
git clone git@github.com:Codeception/Codeception.git
~~~

Instale as dependências com o Composer:

~~~
cd Codeception
curl -s http://getcomposer.org/installer | php
php composer.phar install
~~~

Execute o bootstrap, especificando o caminho para o seu  diretório

~~~
php codecept bootstrap /path/to/my/project
~~~

Para executar os testes use a opção -c para especificar o caminho.

~~~
php codecept run -c /path/to/my/project
~~~

Se você quiser empacotar um phar execute: php bin/build_phar.php

Não se esqueça de mandar Pull Requests!