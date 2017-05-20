---
layout: post
title:  "[PHP] Array nosso de cada dia [Parte 1]"
date:   2017-05-12 22:00:00
categories: dev
---

Arrays são indispensáveis na vida do programador PHP!
O objetivo desses artigos é ajudar aquele que esteja querendo aprender sobre arrays em php e também aqueles que já estão acostumados com, e estão procurando alguma referência à respeito.

É natural a utilização desse tipo de dado no desenvolvimento PHP (Há quem defenda a ideia de não abusar da utilização simples de arrays e utilizar soluções da SPL, mas fica para uma próxima essa reflexão... E você que está começando agora não se assuste com o termo SPL que em breve saberá do que se trata). Vamos deixar aqui o mais claro possível o que é um array e assim podemos introduzir o assunto aos poucos para aqueles que estão começando agora. Em termos de complexidade, como em toda linguagem, basta aprofundar um pouco o assunto e iremos encontrar definições e outras coisas que confundem. O que quero ressaltar aqui é que se alguém pode fazer algo, você também é capaz! Deixo essa mensagem para quem está iniciando e se sente pávido ao encontrar alguns termos estranhos no inicio mas que naturalmente com o tempo são compreendidos! :)

![Array](http://vegibit.com/wp-content/uploads/2013/11/php-array-functions1.png "arrays")

#### O que é um array?
Utilizo aqui a própria definição da documentação para explanar o que é um array (Traduzido a grosso modo):

Um array no PHP é atualmente um mapa ordenado.
Um mapa é um tipo que associa valores à chaves. Este é otimizado para diferentes tipos de uso; Ele pode ser tratado como um array, uma lista (Vector), Hash Table (Uma implementação de um mapa), dicionário (dictionary), coleção (collection), pilha (stack), fila (queue), e provavelmente mais. Como valores de arrays podem ser outros arrays, árvores e arrays multidimensionais também são possíveis. 

Então entenda um array como um mapa associativo/caixa com itens guardados.
Na sua casa provavelmente você possui um array físico. Se imaginar seu guarda-roupas... Lembre dele por fora com todas suas portas. Lá dentro você guarda seus itens e de alguma forma associa essas roupas à algum lugar específico do guarda-roupas, como por exemplo a gaveta para meias, as portas da direita para camisas, outras gavetas para camisetas e assim por diante.
Na programação o array tem o mesmo papel, servir de caixa para itens.
Simplesmente identificamos um valor com uma chave, uma ou mais vezes. Então ao invés de declarar 10 variáveis que estão relacionadas, criamos apenas um array com 10 elementos. Pense bem... Se tenho a opção de optar por comodidade e espaço para quer ter 10 guarda-roupas de 1 porta se posso ter um com 6 portas e 4 gavetas? :)

#### Declaração de arrays
Podemos criar um array de algumas formas no PHP, e provavelmente a mais amigável (declação curta) foi implementada na versão 5.4 do PHP:

{% highlight bash %}
$meuArray = [];
{% endhighlight %}

Dessa forma será criado um array na variável **meuArray**.
A outra forma (clássica) também não deixa de ser simples:

{% highlight bash %}
$meuArray = array();
{% endhighlight %}

Um array pode ser criado com valores iniciais, seja ele indexado ou associativo.

Arrays simples:
{% highlight bash %}
// Array indexado (As chaves são criadas pelo PHP automaticamente já que só temos valores aqui presentes)
$meuArray = [
    'Marte',
    'Vênus',
    'Sol'
];

// Array associativo (Definimos uma chave para nosso valor (Lembra da gaveta? :)), que poderemos acessar mais tarde via nossa chave (Associação)
$meuOutroArray = [
    'nome' => 'Rodrigo',
    'idade' => 24
];

// Aqui poderíamos exibir as variáveis da seguinte forma:
// echo $meuOutroArray['nome'] . PHP_EOL; Saída -> Rodrigo
// echo $meuOutroArray['idade'] . PHP_EOL; Saída -> 24
{% endhighlight %}

Os valores dos arrays no php não se limitam a só um tipo de dado, significando que podemos criar arrays de várias formas:

{% highlight bash %}
// Array de inteiros
$arrayValido = [
    0, 1, 2, 3, 4, 5
];

// Mixed. Inteiros e string
$arrayValido = [
    0, 1, 2, 3, 4, 'minha string'
];

// String
$arrayValido = [
    'pato', 'cachorro', 'dinossauro', 'ovelha', 'nasa', 'terra'
];

// Array associativo e com uma index
$arrayValido = [
    0 => 'Um valor qualquer',
    'nome' => 'Linus',
    'foo' => 'bar'
];
{% endhighlight %}

Como mencionado na documentação é possível a criação de arrays de arrays:

{% highlight bash %}
// Array associativo de um array indexado
$meuArray = [
    'outro_array' => [0, 1, 2, 3, 4, 5]
];
// echo $meuArray['outro_array'][0]; -> Saída: 0
// echo $meuArray['outro_array'][1]; -> Saída: 1
{% endhighlight %}

Então repare que não é obrigatório definirmos uma chave para um valor, o php se encarrega disso se nenhuma chave for declarada:

{% highlight bash %}
// Array associativo e indexado
$meuArray = [
    'minha_chave' => 'ok',
    1,
    2,
    3
    4,
    5
];
{% endhighlight %}

Pela função [*var_dump*](http://php.net/manual/pt_BR/function.var-dump.php) a saída fica:
{% highlight bash %}
array(6) {
  'minha_chave' =>
  string(2) "ok"
  [0] => // Aqui o php começa o array com index 0 e incrementa conforme o mesmo cresce
  int(1)
  [1] =>
  int(2)
  [2] =>
  int(3)
  [3] =>
  int(4)
  [4] =>
  int(5)
}
{% endhighlight %}

#### Obtendo valores de arrays indexados/associativos:

Para obter o valor de um array utilizamos 'square bracket' ou 'colchetes': *[* e *]*. Semelhante a declaração. Ex:
{% highlight bash %}
// Array indexado
$meuArray = [1,2,3,4];
echo $meuArray[0];
// Saída: 1

// Array associativo
$meuArray = ['nome' => 'Rodrigo'];
echo $meuArray['nome'];
// Saída: Rodrigo
{% endhighlight %}



### Conclusão
Os arrays estarão sempre presentes no nosso código e é essencial aprendermos a manipular e otimizar a utilização dos mesmos!
E não pare por aqui, leia a documentação: [Arrays/PHP.net](http://php.net/manual/pt_BR/language.types.array.php). Ela é a nossa melhor amiga quando estamos programando (ou não).

Em breve finalizo a segunda parte! Aguarde!
Forte Abraço!

### Referência
- [Arrays/PHP.net](http://php.net/manual/pt_BR/language.types.array.php)