---
layout: post
title:  "Testando clientes http com httpbin"
date:   2016-07-09 19:34:00
categories: dev
---

Nos dias de hoje é comum utilizarmos api's e clientes http diversos, seja para estudo, projetos pessoais ou até mesmo integrações lado a lado com o sistema que trabalhamos.

Independente da linguagem que utilizamos para automatizar uma tarefa, sempre surgirá novas tecnologias e particularidades que precisam ser testadas para que o resultado final seja satisfatório, e é nessa linha de raciocínio que é interessante utilizar algum serviço de teste.

É claro que podemos fazer muito das coisas manualmente local em nossos ambientes de desenvolvimento, entretanto hora ou outra alguns backgrounds podem se tornar cansativos e uma ferramenta de testes se torna indispensável :).

Pensando nisso Keineth Reitz (O mesmo cara que criou a biblioteca requests do python) teve a grande ideia de implementar
um serviço endpoint para testes http. É um pack da felicidade esse, com requests e o httpbin por exemplo tu testa possibilidades
até num querer mais! haha! ;)

A definição do serviço já diz tudo:

{% highlight text %}
Testing an HTTP Library can become difficult sometimes. 
RequestBin is fantastic for testing POST requests, but doesn't let you control the response. 
This exists to cover all kinds of HTTP scenarios. Additional endpoints are being considered.

All endpoint responses are JSON-encoded.
{% endhighlight %}

Certamente encontraremos liberdade para testar nossas requisições utilizando o [httpbin](http://httpbin.org).

Eu irei deixar também outros serviços para testes no final do artigo caso alguém queira se aventurar :)

A estrutura do httpbin para testes se baseia na ação desejada, logo encontraremos desde verbs http até algumas operações mais peculiares:

{% highlight text %}
https://httpbin.org/get
https://httpbin.org/headers
https://httpbin.org/post
https://httpbin.org/html
{% endhighlight %}

E muitos outros.

No momento que escrevo esse texto, existem 42 endpoints para testes, todos eles com retorno em json. Então basicamente temos tudo o que precisamos para testar diversas tarefas, sejam elas:
Download de arquivos, requisições post com estruturas complexas, teste de encodings e muitas outras coisas.

Algo que acho legal é a comparação da mesma tarefa em linguagens diversas, então deixarei snippets da mesma operação em php, python, js, etc.

### PHP
No php possuímos diversos meios de executar uma requisição http, desde funções que trabalham com criações de streams como o fopen, file, file_get_contents, etc e também a api do curl que trabalha como wrapper para a libcurl ([libcurl site](https://curl.haxx.se/libcurl/php/))

#### Get request

Utilizando file_get_contents:

{% highlight php %}
<?php

$context = stream_context_create(
    array(
        'http' => array(
            'method' => 'GET',
            'header' => 'User-agent: Foo-bar'
        )
    )
);

$resultado = file_get_contents(
    'http://httpbin.org/get',
    false,
    $context
);

echo $resultado;
{% endhighlight %}

output
{% highlight text %}
{
  "args": {},
  "headers": {
    "Host": "httpbin.org",
    "User-Agent": "Foo-bar"
  },
  "origin": "0.0.0.0",
  "url": "http://httpbin.org/get"
}
{% endhighlight %}

#### Post request
{% highlight php %}
<?php

$post_payload = http_build_query(
    array(
        'Name'    => 'Rodrigo',
        'Lang'    => 'PHP',
        'Service' => 'httpbin.org'
    )
);

$headers = array(
    'User-agent: Foo-bar',
    'Content-type: application/x-www-form-urlencoded'
);

$context = stream_context_create(
    array(
        'http' => array(
            'method'  => 'GET',
            'header'  => implode("\r\n", $headers),
            'content' => $post_payload
        )
    )
);

$resultado = file_get_contents(
    'http://httpbin.org/get',
    false,
    $context
);

echo $resultado;
{% endhighlight %}

output
{% highlight text %}
{
  "args": {},
  "data": "",
  "files": {},
  "form": {
    // Meu post payload aqui :)
    "Lang": "PHP",
    "Name": "Rodrigo",
    "Service": "httpbin.org"
  },
  "headers": {
    "Content-Length": "41",
    "Content-Type": "application/x-www-form-urlencoded",
    "Host": "httpbin.org",
    "User-Agent": "Foo-bar"
  },
  "json": null,
  "origin": "0.0.0.0",
  "url": "http://httpbin.org/post"
}
{% endhighlight %}

Utilizando curl:
{% highlight php %}
<?php

$post_payload = http_build_query(
    array(
        'Name'    => 'Rodrigo',
        'Lang'    => 'PHP',
        'Service' => 'httpbin.org'
    )
);

$ch = curl_init();

curl_setopt_array($ch, array(
    CURLOPT_URL        => 'http://httpbin.org/post',
    CURLOPT_POST       => true,
    CURLOPT_POSTFIELDS => $post_payload,
    CURLOPT_USERAGENT  => 'Foo-bar',
));

$resultado = curl_exec($ch);
echo $resultado;

{% endhighlight %}

output
{% highlight text %}
{
  "args": {},
  "data": "",
  "files": {},
  "form": {
    "Lang": "PHP",
    "Name": "Rodrigo",
    "Service": "httpbin.org"
  },
  "headers": {
    "Accept": "*/*",
    "Content-Length": "41",
    "Content-Type": "application/x-www-form-urlencoded",
    "Host": "httpbin.org",
    "User-Agent": "Foo-bar"
  },
  "json": null,
  "origin": "0.0.0.0",
  "url": "http://httpbin.org/post"
}
{% endhighlight %}

### Python
Como mencionei no início do texto podemos executar muitos testes utilizando a biblioteca requests e o httpbin, então nada mais justo do que utilizar essa dupla marota. E para quem está começando agora e está procurando uma linguagem por conta da facilidade, repare no número de linhas em cada snippet ;)

#### Get request:

{% highlight python %}
import requests

r = requests.get('http://httpbin.org/get')
print r.text
{% endhighlight %}

output
{% highlight text %}
{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Accept-Encoding": "gzip, deflate",
    "Host": "httpbin.org",
    "User-Agent": "python-requests/2.10.0"
  },
  "origin": "0.0.0.0",
  "url": "http://httpbin.org/get"
}
{% endhighlight %}


#### Post request:

{% highlight python %}
import requests

r = requests.post(
    'http://httpbin.org/post',
    data={
        'Name': 'Rodrigo',
        'Lang': 'Python',
        'Service': 'httpbin.org'
    },
    headers={
        'User-agent': 'Foo-bar'
    }
)

print r.text
{% endhighlight %}

output
{% highlight text %}
{
  "args": {},
  "data": "",
  "files": {},
  "form": {
    # Meu post payload aqui
    "Lang": "Python",
    "Name": "Rodrigo",
    "Service": "httpbin.org"
  },
  "headers": {
    "Accept": "*/*",
    "Accept-Encoding": "gzip, deflate",
    "Content-Length": "44",
    "Content-Type": "application/x-www-form-urlencoded",
    "Host": "httpbin.org",
    "User-Agent": "Foo-bar"
  },
  "json": null,
  "origin": "0.0.0.0",
  "url": "http://httpbin.org/post"
}
{% endhighlight %}


### JQuery:
Para alguns fins em projetos web diversos é comum utilizarmos AJAX para requisições web:

#### Post request:
{% highlight javascript %}
var jqxhr = $.post(
    "http://httpbin.org/post", 
    {
        'Name': 'Rodrigo',
        'Lang': 'JS',
        'Service': 'httpbin.org'    
    }
).done(function(data) {
    console.log(data);
});
{% endhighlight %}

output
{% highlight text %}
{
  "args": {}, 
  "data": "", 
  "files": {}, 
  "form": {
    # Post payload aqui
    "Lang": "JS", 
    "Name": "Rodrigo", 
    "Service": "httpbin.org"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Accept-Language": "pt-BR,pt;q=0.8,en-US;q=0.5,en;q=0.3", 
    "Content-Length": "40", 
    "Content-Type": "application/x-www-form-urlencoded; charset=UTF-8", 
    "Host": "httpbin.org", 
    "Origin": "http://api.jquery.com", 
    "Referer": "http://api.jquery.com/jQuery.post/", 
    "User-Agent": "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:47.0) Gecko/20100101 Firefox/47.0"
  }, 
  "json": null, 
  "origin": "0.0.0.0", 
  "url": "http://httpbin.org/post"
}
{% endhighlight %}


### Stream

{% highlight python %}
import requests

r = requests.get(
    'https://httpbin.org/stream/100',
    stream=True
)

for line in r.iter_lines():
    if line:
        print line
{% endhighlight %}

output
{% highlight text %}
{"url": "https://httpbin.org/stream/100", "headers": {"Host": "httpbin.org", "Accept-Encoding": "gzip, deflate", "Accept
": "*/*", "User-Agent": "python-requests/2.10.0"}, "args": {}, "id": 0, ""origin": "0.0.0.0""}
{"url": "https://httpbin.org/stream/100", "headers": {"Host": "httpbin.org", "Accept-Encoding": "gzip, deflate", "Accept
": "*/*", "User-Agent": "python-requests/2.10.0"}, "args": {}, "id": 1, ""origin": "0.0.0.0""}
{"url": "https://httpbin.org/stream/100", "headers": {"Host": "httpbin.org", "Accept-Encoding": "gzip, deflate", "Accept
": "*/*", "User-Agent": "python-requests/2.10.0"}, "args": {}, "id": 2, ""origin": "0.0.0.0""}
...
{% endhighlight %}

É isso!
Caso queira testar aquele seu novo cliente ou biblioteca de uma chance ao httpbin e veja se tudo corre bem!

Forte Abraço!

#### Similares:
- RequestBin - http://requestb.in/
- PutsReq - http://putsreq.com/
- Hurl.it - https://www.hurl.it/

