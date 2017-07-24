---
layout: post
title:  "[PHP] Funções e métodos com argumentos variáveis"
date:   2017-07-23 19:00:00
categories: dev
---

Em alguns momentos pode surgir a necessidade de se criar um método/função com argumentos variáveis.
O próprio php possui funções que aceitam "n" argumentos, como por ex:

- [var_dump](http://php.net/manual/en/function.var-dump.php)
- [isset](http://php.net/manual/en/function.isset.php)

Entre outros. Para criarmos funções/métodos com essa capacidade podemos utilizar a função
func_get_args (*Que inclusive ta no php ó, faz tempo!!!*) que retorna um array com todos os parâmetros utilizados na chamada do método/função.
Da mesma familia tbm temos a função func_num_args que retorna o número de parâmetros que foram passados.

Ex de utilização:

```php
<?php

function soma() {
    var_dump(func_get_args(), func_num_args());
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

function soma() {
    return array_sum(func_get_args());
}

var_dump(soma(5, 5, 5, 5));

// Saída
int(20)
```

E em alguns casos mais complexos podemos até fazer operações mais distintas:

Consulta de arquivos:

```php
<?php

function checkFiles() {
    return array_map(function($file){
        return [$file => file_exists($file) ? 'Found' : 'Not found'];
    }, func_get_args());
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

function gerarLogs() {
    $urls = func_get_args();
    array_walk($urls, function($url){
        // lógica aqui... Requisição http, etc
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

Os exemplos estão longe de serem algo definitivo. Da próxima vez que for criar uma classe útil
que possui métodos flexíveis, de uma chance as funções [func_get_args](http://php.net/manual/en/function.func-get-args.php) e [func_num_args](http://php.net/manual/en/function.func-num-args.php)! :)

Dúvidas, sugestões, tomar uma breja?
Deixe seu comentário!

Forte abraço!

Referências:
---
- [func_num_args php.net](http://php.net/manual/en/function.func-num-args.php)
- [func_get_args php.net](http://php.net/manual/en/function.func-get-args.php)
- [StackOverflow question](https://stackoverflow.com/a/11480452/3941753)