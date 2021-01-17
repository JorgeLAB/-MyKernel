# The rails initialization process

[Rails DOC][doc_rails_initialization_process]

### O que acontece quando rodamos <span style="color:red">` bin/rails server`</span>

## 1 - bin/rails 

​	Arquivo de inicialização vamos dar uma olhada no que há dentro:

``` ruby
#!/usr/bin/env ruby
APP_PATH = File.expand_path('../config/application', __dir__)
require_relative '../config/boot'
require 'rails/commands
```

Essa constande ` APP_PATH` será usada depois no arquivo `rails/commands` . O require de '../config/boot' , um arquivo reposável pela inicialização do Bundler e configurações iniciais.

## 2 - config/boot.rb

Vamos dar uma olhada dentro

```ruby
ENV['BUNDLE_GEMFILE'] ||= File.expand_path('../Gemfile', __dir__)

require 'bundler/setup' # Set up gems listed in the Gemfile.
require 'bootsnap/setup' # Speed up boot time by caching expensive operations.
```

Nas aplicações rails o Gemfile é onde são declaradas todas as dependencias da aplicação. O arquivo `config/boot.rb` inicializa o bundler para carregar as dependencias se a variável de ambinete BUNDLE_GEMFILE  existir. Caso contrário com o método  `File.expand_path` nós poderemos acessar esse arquivo Gemfile direto.

Agora as gems padrões do rails:

- actioncable
- actionmailer
- actionpack
- actionview
- activejob
- activemodel
- activerecord
- activestorage
- activesupport
- actionmailbox
- actiontext
- arel
- builder
- bundler
- erubi
- i18n
- mail
- mime-types
- rack
- rack-test
- rails
- railties
- rake
- sqlite3
- thor
- tzinfo

## 3 - rails/command.rb

```ruby
# frozen_string_literal: true

require "rails/command"

aliases = {
  "g"  => "generate",
  "d"  => "destroy",
  "c"  => "console",
  "s"  => "server",
  "db" => "dbconsole",
  "r"  => "runner",
  "t"  => "test"
}

command = ARGV.shift
command = aliases[command] || command

Rails::Command.invoke command, ARGV
```

Podemos encontrar esse arquivo em ` /usr/local/bundle/gems/railties-6.1.1/lib/rails` , fica mais fácil usando uma máquina virtual no caso de usar um gerenciador de versões como rvm, rbenv ou asdf já não sei o caminho.

Vamos analisar:

* frozen_string_literal: true - esta primeira linha transforma qualquer outro arquivo `freeze`.

  ```ruby
  > primeiro_nome = "Meu nome"
  > primeiro_nome.freeze
  > primeiro_nome << "r"
  = ERROR
  ```

  Ou seja, tornas os elementos de tipo String imutáveis, [ uma resposta do Stack overflow][ frozen_string_literal]

* require "rails/command"  -  <span style="color:red"> outro arquivo interno do rails</span>
* aliases - <span style="color:red"> Estamos fazendo shortcuts para comandos ( bem interessante )</span>
* command = ARGV.shift -  <span style="color:red"> ARGV é uma forma de gerar array e ` shift` faz a remoção do primeiro elemento da lista.</span>
*  command = aliases[command] || command - <span style="color:red"> se o comando passado pelo usuário for um atalho 'g' ou 's' então o alias será usado, isso houver a chave passada. Caso contrário  será o comando digitado será atribuido.</span>
* Rails::Command.invoke command, ARGV - <span style="color:red"> essa cadeia de classes e modulos executará o comando passado.</span>









 





[doc_rails_initialization_process]: https://guides.rubyonrails.org/initialization.html
[ frozen_string_literal]: https://stackoverflow.com/questions/37799296/ruby-what-does-the-comment-frozen-string-literal-true-do