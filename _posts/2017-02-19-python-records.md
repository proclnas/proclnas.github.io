---
layout: post
title:  "Queries SQL com Python e Records"
date:   2017-02-19 21:00:00
categories: dev
---

Fala galera!
A algum tempo atrás precisei fazer algumas queries em tabelas para utilizar os resultados em outros processos.
Lembro que no momento precisava de algo simples para que outros programadores pudessem dar manutenção no código, então resolvi desenvolver algo simples com python.

![Snake](http://bucarotechelp.com/howto/images/snake_final.jpg)

Com a biblioteca padrão vi que precisava de muitas coisas e que era muito "complexo" estabelecer uma conexão com um banco seja lá qual fosse ele (Eu geralmente trabalho com mysql, postgresql ou sqlite)

Na hora lembrei de um dos principios do Zen do python:

```
Complex is better than complicated.
```

Mesmo levando a regra em consideração, procurei outras alternativas
para o trabalho proposto e assim encontrei a excelente biblioteca Records.

Do mesmo criador da biblioteca [requests](http://docs.python-requests.org/en/master/). A introdução já adianta o que vem pela frente:

```
Records is a very simple, but powerful, library for making raw SQL queries to most relational databases.
Just write SQL. No bells, no whistles. This common task can be surprisingly difficult with the standard tools available. 
This library strives to make this workflow as simple as possible, while providing an elegant interface to work with your query results.
```

Com o mesmo principio da biblioteca requests a records foi desenvolvida
em cima da seguinte filosofia:

```
A library for humans
```

Para quem já utilizou requests sabe que realmente é simples
fazer uma requisição http e essa mesma facilidade se aplica a Records.
Se ainda não utilizou, o convido para que possa testar!!! :)

A partir da instalação irei mostrar alguns exemplos de utilização
com alguns dados fakes (mock data). Para que sirva de overview e quem sabe
sirva também como "criador" de coragem para seu próximo projeto.

## Instalação

É recomendado instalar a bibiloteca via pip:

```
pip install records
```

Para demonstrar ações simples, criei um banco
com algumas informações sobre conferências.


### Conexão

Para criar a conexão com o banco precisamos utilizar um [Dialect](http://docs.sqlalchemy.org/en/latest/dialects/)
Estou utilizando postgtresql então prossigo da seguinte forma:

```
postgresql://user:password@host/db
```

Para facilitar criei um método que retorna a instancia do db:

```python
import records # Importar a biblioteca

# Criação de constantes
ADAPTER = 'postgresql'
USER = 'postgres'
PASSWORD = 'postgres'
HOST = 'localhost'
DATABASE = 'mydb'


def get_db():
    """
    Método para conexão com o banco
    """
    return records.Database(
        '{}://{}:{}@{}/{}'.format(ADAPTER, USER, PASSWORD, HOST, DATABASE)
    )
    
try:
    db = get_db()
    ...
except Exception as e:
    print e.message

```

Agora já podemos executar ações no nosso banco.


### SELECT

Para executar um select em uma tabela:

```python
rows = db.query('select * from bar')
```


#### Formas de leitura dos resultados

O banco que irei utilizar possui as seguintes tabelas:

- conferences (Conferências)
- speakers (Speakers)
- talks (Speakers em x conferência, apresentando y talk)

#####  Uma linha por vez:
```python
rows = db.query('SELECT * FROM conferences')
print rows[0]
<Record {"id_conf": 1, "title": "DEF CON"}>
```

#####  Iteração:
```python
rows = db.query('SELECT * FROM conferences')
for row in rows:
    print row
    
<Record {"id_conf": 1, "title": "DEF CON"}>
<Record {"id_conf": 2, "title": "H2HC"}>
<Record {"id_conf": 3, "title": "BLACKHAT"}>
<Record {"id_conf": 4, "title": "OWASP"}>
<Record {"id_conf": 5, "title": "RSA"}>
<Record {"id_conf": 6, "title": "TROOPERS"}>
<Record {"id_conf": 7, "title": "NUIT DU HACK"}>
<Record {"id_conf": 8, "title": "BSIDES"}>
<Record {"id_conf": 9, "title": "PHPCONF"}>
<Record {"id_conf": 10, "title": "PYCONF"}>
```

#####  Todos os resultados de uma vez (Para utilização futura):
```python
rows = db.query('SELECT * FROM conferences')
print rows.all()

[<Record {"id_conf": 1, "title": "DEF CON"}>, <Record {"id_conf": 2, "title": "H2HC"}>, ...]
```

Baseado nisso podemos então conhecer um pouco mais de nossas tabelas:

```python
rows = db.query('SELECT * FROM conferences LIMIT 4')
    for row in rows:
        print row

<Record {"id_conf": 1, "title": "DEF CON"}>
<Record {"id_conf": 2, "title": "H2HC"}>
<Record {"id_conf": 3, "title": "BLACKHAT"}>
<Record {"id_conf": 4, "title": "OWASP"}>
```

```python
rows = db.query('SELECT * FROM speakers LIMIT 4')
for row in rows:
    print row
    
<Record {"id_speaker": 1, "name": "BSDaemon"}>
<Record {"id_speaker": 2, "name": "C00F3r"}>
<Record {"id_speaker": 4, "name": "Stefan Esser"}>
<Record {"id_speaker": 5, "name": "Nikita Popov"}>
```

```python
rows = db.query('SELECT * FROM talks LIMIT 4')
for row in rows:
    print row
    
<Record {"id_talk": 1, "id_conf": 1, "id_speaker": 1, "title": "KIDS - Kernel Intrusion Detection System"}>
<Record {"id_talk": 4, "id_conf": 1, "id_speaker": 6, "title": "Exploiting nginx logrotate"}>
<Record {"id_talk": 5, "id_conf": 1, "id_speaker": 9, "title": "Improving crypto"}>
<Record {"id_talk": 6, "id_conf": 1, "id_speaker": 14, "title": "Kernel exploitation in a nutshell"}>
```

No último snippet se repararem irão ver que possuimos apenas os id's das tabelas: conferences (id_conf) e speakers(id_speaker).
É comum vermos esse tipo de [Relacionamento](https://pt.wikipedia.org/wiki/Banco_de_dados_relacional#Relacionamentos).

Para ligar os dados podemos executar um JOIN simples e saber quem é o speaker, e a confêrencia no qual ele irá apresentar essa talk:

```python
print 'Speakers agenda'
raw = 'SELECT conferences.title as conf, speakers.name as speaker, ' \
      'talks.title as talk FROM talks ' \
      'JOIN conferences ON conferences.id_conf = talks.id_conf ' \ # Aqui relacionamos talks com conferences para pegar o nome da conferencia
      'JOIN speakers ON speakers.id_speaker = talks.id_speaker ' \ # E aqui relacionamos talks com speakers para pegar o nome do speaker
      'ORDER BY conferences.title'
rows = db.query(raw)
for row in rows:
    print 'Conf: {}, Speaker: {}, Talk: {}'.format(
        row['conf'],
        row['speaker'],
        row['talk']
    )
    
Speakers agenda
Conf: BLACKHAT, Speaker: Rodrigo Strauss, Talk: Soft driver exploitation
Conf: DEF CON, Speaker: KingCope, Talk: Kernel exploitation in a nutshell
Conf: DEF CON, Speaker: Diego Aranha, Talk: Improving crypto
Conf: DEF CON, Speaker: Dawid Golunski, Talk: Exploiting nginx logrotate
Conf: DEF CON, Speaker: BSDaemon, Talk: KIDS - Kernel Intrusion Detection System
Conf: DEF CON, Speaker: Stefan Esser, Talk: Ios jailbreak
Conf: H2HC, Speaker: Stefan Esser, Talk: Smashing php serialize
Conf: OWASP, Speaker: C00F3r, Talk: "Web Spiders" - Automation para Web Hacking
Conf: OWASP, Speaker: Merces, Talk: LULa project
Conf: PHPCONF, Speaker: Gabriel Couto, Talk: PHP Gui
Conf: PHPCONF, Speaker: Gabriel Couto, Talk: PHP Threading
Conf: PHPCONF, Speaker: Julien Pauli, Talk: Exploiting zend engine
```

Simples não?

Podemos também executar queries com parametros seguros:

```python
rows = db.query(
    'SELECT * FROM talks WHERE id_talk = :id_talk',
    False,
    id_talk=1
)

print rows[0]

<Record {"id_talk": 1, "id_conf": 1, "id_speaker": 1, "title": "KIDS - Kernel Intrusion Detection System"}>
```

### UPDATE

Para executar updates também não há segredos:

```python
print db.query(
    'SELECT * FROM talks WHERE id_talk = :id_talk',
    False,
    id_talk=1
)[0]

<Record {"id_talk": 1, "id_conf": 1, "id_speaker": 1, "title": "KIDS - Kernel Intrusion Detection System"}>

db.query(
    'UPDATE talks SET id_conf = :id_conf WHERE id_talk = :id_talk',
    False,
    id_conf=3,
    id_talk=1
)

print db.query(
    'SELECT * FROM talks WHERE id_talk = :id_talk',
    False,
    id_talk=1
)[0]
<Record {"id_talk": 1, "id_conf": 3, "id_speaker": 1, "title": "KIDS - Kernel Intrusion Detection System"}>
```

Filtrando novamente nossa query agora completa teremos nossos resultados atualizados:

```python
print '\nSpeakers agenda'
raw = 'SELECT conferences.title as conf, speakers.name as speaker, ' \
      'talks.title as talk FROM talks ' \
      'JOIN conferences ON ' \
      'conferences.id_conf = talks.id_conf ' \
      'JOIN speakers ON speakers.id_speaker = talks.id_speaker ' \
      'WHERE talks.id_conf = :id_conf ' \
      'ORDER BY conferences.title'
rows = db.query(raw, False, id_conf=3)
for row in rows:
    print 'Conf: {}, Speaker: {}, Talk: {}'.format(
        row['conf'],
        row['speaker'],
        row['talk']
    )
    
Speakers agenda
Conf: BLACKHAT, Speaker: Rodrigo Strauss, Talk: Soft driver exploitation
Conf: BLACKHAT, Speaker: BSDaemon, Talk: KIDS - Kernel Intrusion Detection System
```


### DELETE

```python
db.query(
    'DELETE FROM talks WHERE id_conf = :id_conf',
    False,
    id_conf=3
)

# Talks by conference
print '\nSpeakers agenda'
raw = 'SELECT conferences.title as conf, speakers.name as speaker, ' \
      'talks.title as talk FROM talks ' \
      'JOIN conferences ON ' \
      'conferences.id_conf = talks.id_conf ' \
      'JOIN speakers ON speakers.id_speaker = talks.id_speaker ' \
      'WHERE talks.id_conf = :id_conf ' \
      'ORDER BY conferences.title'
rows = db.query(raw, False, id_conf=3)
for row in rows:
    print 'Conf: {}, Speaker: {}, Talk: {}'.format(
        row['conf'],
        row['speaker'],
        row['talk']
    )
    
Speakers agenda
```

### Exportação

A biblioteca records possui integração completa com a biblioteca Tablib
então é possível exportar data de várias formas. Retirei os exemplos abaixo  da documentação oficial

```python
>>> print rows.dataset
username|active|name      |user_email       |timezone
--------|------|----------|-----------------|--------------------------
hansolo |True  |Henry Ford|hansolo@gmail.com|2016-02-06 22:28:23.894202
...

CSV:

>>> print rows.dataset.csv
username,active,name,user_email,timezone
hansolo,True,Henry Ford,hansolo@gmail.com,2016-02-06 22:28:23.894202
...

YAML:

>>> print rows.dataset.yaml
- {active: true, name: Henry Ford, timezone: '2016-02-06 22:28:23.894202', user_email: hansolo@gmail.com, username: hansolo}
...

JSON:

>>> print rows.dataset.json
[{"username": "hansolo", "active": true, "name": "Henry Ford", "user_email": "hansolo@gmail.com", "timezone": "2016-02-06 22:28:23.894202"}, ...]

Excel:

with open('report.xls', 'wb') as f:
    f.write(rows.dataset.xls)
```


### Conclusão
Esse foi um post introdutório. Sinta-se livre para deixar um comentário ou sugerir melhorias!

Forte abraço!

### Referência
- [https://www.kennethreitz.org/essays/introducing-records-just-write-sql](https://www.kennethreitz.org/essays/introducing-records-just-write-sql)
- [https://github.com/kennethreitz/records](https://github.com/kennethreitz/records)
- [http://docs.sqlalchemy.org/en/latest/dialects/](http://docs.sqlalchemy.org/en/latest/dialects/)
- [https://www.concise-courses.com/security/conferences-top-ten-must-go-to/](http://docs.sqlalchemy.org/en/latest/dialects/)
- [https://pt.wikipedia.org/wiki/Banco_de_dados_relacional#Relacionamentos](https://pt.wikipedia.org/wiki/Banco_de_dados_relacional#Relacionamentos)