# QA Ninja

Dia 17 de Maio de 2021

Dia 18 de Maio de 2021

Clonando repositório https://gitlab.com/papito/rocklov-dc.

Configurando host - No ubuntu https://king.host/wiki/artigo/como-editar-o-arquivo-hosts-no-linux-ubuntu/

**Hosts**
127.0.0.1 rocklov-db
127.0.0.1 rocklov-api
127.0.0.1 rocklov-web

Escrevendo cenário de teste para a submissão de um formulário.

'Dado que acesso a página de cadastro' -> 'E preencho o campo nome com Jorge Borges' -> 'E preencho o campo email com jorgeBorges@gmai.com' -> 'E preenchoa  a senha com 12345678' -> 'Quando clico no botão cadastrar' -> 'Então sou cadastrado com sucesso.'

>  1º) O BDD de fato, não te obriga a escrever os cenários com gherkin, porque ele não é uma metodologia de teste, mas sim, uma metodologia de **desenvolvimento de software.** Gherkin é apenas uma das várias formas que se pode descrever o comportamento de uma determinada funcionalidade. Se você escreveu o comportamento de uma feature, a equipe toda chegou em um consenso sobre como o software deve funcionar, e o **desenvolvimento** da feature é feito em cima da definição do comportamento (A automação de testes pode ser também), você está praticando BDD. 

>  2°) Se tiver tempo, sim. Mas se o tempo for mais escasso, eu levaria os cenários de auto nível para a reunião (Escrevendo ele em tópicos), anotaria os comportamentos, e depois escreveria os bdd's mais detalhados. Ou até mesmo, escreveria a maior parte dos cenários antes mesmo da reunião de planejamento, e já iria pra ela com muitos cenários já escritos e validaria com o PO e DEV se se eram aqueles cenários mesmo apenas, ou se algum precisava ser melhorado ou adicionado. E lembrando que o BDD não te obriga a escrever os cenários necessariamente em gherkin. Se teu time entender os objetivos e o comportamento da feature apenas com uma descrição básica, e desenvolverem em cima disso, você já estará fazendo o BDD ;)

>  2°) Quando precisei implementar esquemas de cenário na automação onde trabalhei, por um tempo escrevi em gherkin, e antes de começar, toda a equipe de negócio e stakeholders estava alinhada sobre o formato da escrita, etc.. Logo não tive problemas com isso...

Escrever cenários em BDD deve ser uma especificação para guiar processos em desenvolvimento. A motivação não é testar. Trazer uma documentação rica e colaborative.

A melhor prática de se escrever cenários é colocar em primeira pessoa, colocando-se no lugar de quem irá utilizar o software.

Dando ao PO a responsabilidade de decisão final de um cenário. 

>     Dado que acesso a página de cadastro
>     Quando submeto o meu cadastro completo
>     Então ???

Dia 20 de Maio de 2021

> Sendo um músico que possui equipamentos musicais - Ator 
> Quero fazer meu cadastro no site - funcionalidade a ser desenvolvida
> Para que eu possa disponibilizá-los para locação - Valor de negócio 

O objetivo é desenvolver a história.

Usano testes automatizados, é necessário pensar em regressão quando se fala em testes. 
Usando o **Cucumber** para realizar esses testes, o cucumber reconhece a sintaxe Gherkin com isso podemos usar Dado, Quando, Então ... 

```shell
cucumber --init 
```

Irá gerar a estrutura de pastas.
Nota-se um padrão do arquivo que deve ser seguido como:

`categoria.feature`

```ruby
#language: pt

Funcionalidade: Fazer algo 
  Dado alguma coisa
  Quando ocorre tal coisa
  Então fará isso
  Cenário: Fazer algo
    Dado alguma coisa
    Quando ocorre tal coisa
    Então fará isso
  Cenário: Fazer outra coisa
    Dado alguma coisa
    Quando ocorre tal coisa
    Então fará isso
```

O comentário `#language: pt` serve para identificar a linguagem empregada.
O arquivo de tipo `feature` serve para identificar seus cenário já o arquivo de `steps` irá setar uma função ao `Dado` - `Quando` - `Então`

```ruby
Dado('que acesso a página de cadastro') do
  visit 'rocklov-web:3000'
end

Quando('submeto o meu cadastro completo') do
  pending # Write code here that turns the phrase above into concrete actions
end

Então('sou redirecionado para o Dashboard') do
  pending # Write code here that turns the phrase above into concrete actions
end
```

Mas veja, por sí só o cucumber não é capaz de abrir um browser e manipular. Sendo assim, deve-se instalar a gem `capybara` .

```ruby
# folder: support

require 'capybara'
require 'capybara/cucumber'

Capybara.configure do |config|
  config.default_driver = :selenium_chrome
end 
```



Dia 24 de Maio de 2021









