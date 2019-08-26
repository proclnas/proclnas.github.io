---
layout: post
title:  "[PHP] Splat operator"
date:   2017-07-23 19:00:00
categories: dev
---

Em alguns momentos pode surgir a necessidade de se criar um método/função com argumentos variáveis.
O próprio php possui funções que aceitam "n" argumentos, como por ex:

- [var_dump](http://php.net/manual/en/function.var-dump.php)
- [isset](http://php.net/manual/en/function.isset.php)

Entre outros. 
A partir da versão 5.6 do PHP é possível utilizar o "splat operator (...)" que nos da a capacidade de criar método/funções com argumentos variáveis. Ex:

```php
<?php
function evaluateString($callback, ...$strings) {
    $myStr = 'INITIAL: ';
    foreach ($strings as $stringPiece) {
        $myStr .= $stringPiece;
    }

    return call_user_func($callback, $myStr);
}

$strAllUp = evaluateString('strtoupper', 'rodrigo', ' nascimento');
var_dump($strAllUp);
// Saída: string(27) "INITIAL: RODRIGO NASCIMENTO"
```

Ex de utilização:

```php
<?php

function soma(...$numbers) {
    var_dump($numbers, count($numbers));
}

soma(1, 2, 3, 4);

// Saída
array(4) {
  [0]=>
  int(1)
  [1]=>
  int(2)
  [2]=>
  int(3)
  [3]=>
  int(4)
}
int(4) // Quantidade de parâmetros recebidos pela função
```

A partir dai podemos fazer algumas operações. Uma soma por exemplo utilizando o mesmo código acima:

```php
<?php

function soma(...$numbers) {
    return array_sum($numbers);
}

var_dump(soma(5, 5, 5, 5));

// Saída
int(20)
```

E em alguns casos mais complexos podemos até fazer operações mais distintas:

Consulta de arquivos:

```php
<?php

// Versão utilizando splat operator
function checkFiles(...$files) {
    return array_map(function($file){
        return [$file => file_exists($file) ? 'Found' : 'Not found'];
    }, $files);
}

$busca = checkFiles('1.txt', 'about.md', 'ex.php', 'foo.php');

foreach ($busca as $arquivos) {
    foreach ($arquivos as $nomeArquivo => $status)
        echo sprintf('%s => %s' . PHP_EOL, $nomeArquivo, $status);
}

// Saída
1.txt => Not found
about.md => Found
ex.php => Found
foo.php => Not found
```
**OBS**: *O exemplo acima é somente um exemplo e não visa substituir as capacidades nativas do php! (Não me diga...)
(Nativamente o php trabalha mto bem com arquivos: [GlobIterator](http://php.net/manual/en/class.globiterator.php) entre outros...)*

Requisições http, gerar logs, etc:

```php
<?php

// Versão utilizando splat operator
function gerarLogs(...$urls) {
    array_walk($urls, function($url) {
        // lógica aqui... Requisições http, etc
        echo sprintf('[+] Gerando logs para: %s' . PHP_EOL, $url);
    });
}

gerarLogs(
    'http://www.google.com',
    'http://php.net',
    'http://theuselessweb.com'
);

// Output
[+] Gerando logs para: http://www.google.com
[+] Gerando logs para: http://php.net
[+] Gerando logs para: http://theuselessweb.com
```

## Utilizando type hinting com splat operator
É possível especificar os tipos de argumentos esperados pela função utilizando type hiting normalmente:

```php
<?php

function expectCarObject(Car ...$cars) {
    foreach ($cars as $car) {
        var_dump($car);
    }
}

class Car {}
expectCarObject(new Car, new Car, new Car);

// Saida
object(Car)#1 (0) {
}
object(Car)#2 (0) {
}
object(Car)#3 (0) {
}
```

Caso seja passado paramêtros diferentes do tipo "Car" um fatal error será emitido pelo php.

## Unpacking de argumentos
Com splat operator é possível também fazer unpacking de argumentos, o que significa
que é possível escrever algo como:

```php
<?php

function soma($numA, $numB) {
    return $numA + $numB;
}

$nums = [1, 2];
var_dump(soma(...$nums));

// Saída:
int(3)
```

Os exemplos estão longe de serem algo definitivo. Da próxima vez que for criar uma classe útil
que possui métodos flexíveis, de uma chance ao splat operator.

Dúvidas, sugestões, tomar uma breja?
Deixe seu comentário!

Forte abraço!

Referências:
---
- [splat operator php.net](http://php.net/manual/en/functions.arguments.php#functions.variable-arg-list)
- [func_num_args php.net](http://php.net/manual/en/function.func-num-args.php)
- [func_get_args php.net](http://php.net/manual/en/function.func-get-args.php)
- [StackOverflow question1](https://stackoverflow.com/questions/41124015/meaning-of-three-dot-in-php)
- [StackOverflow question2](https://stackoverflow.com/a/11480452/3941753)