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

##  4 - rails/commands.rb

Depois de executada a invoção o rails vai verificar se o commando passado existe em sua lista se não existir será passado o comando para o Rake para cerificar se alguma variável é compatível.

Mas se o comando for passado em branco algo como: `Rails::Command.invoke "", ARGV` aparecerá uma lista de sugestões. 

Vamos ver o que acontece se tudo dar certo:

```ruby
# frozen_string_literal: true

require "active_support"
require "active_support/core_ext/enumerable"
require "active_support/core_ext/object/blank"

require "thor"

module Rails
  module Command
    extend ActiveSupport::Autoload

    autoload :Spellchecker
    autoload :Behavior
    autoload :Base

    include Behavior

    HELP_MAPPINGS = %w(-h -? --help)

    class << self
      def hidden_commands # :nodoc:
        @hidden_commands ||= []
      end

      def environment # :nodoc:
        ENV["RAILS_ENV"].presence || ENV["RACK_ENV"].presence || "development"
      end

      # Receives a namespace, arguments and the behavior to invoke the command.
      def invoke(full_namespace, args = [], **config)
        namespace = full_namespace = full_namespace.to_s

        if char = namespace =~ /:(\w+)$/
          command_name, namespace = $1, namespace.slice(0, char)
        else
          command_name = namespace
        end

        command_name, namespace = "help", "help" if command_name.blank? || HELP_MAPPINGS.include?(command_name)
        command_name, namespace = "version", "version" if %w( -v --version ).include?(command_name)

        # isolate ARGV to ensure that commands depend only on the args they are given
        args = args.dup # args might *be* ARGV so dup before clearing
        old_argv = ARGV.dup
        ARGV.clear

        command = find_by_namespace(namespace, command_name)
        if command && command.all_commands[command_name]
          command.perform(command_name, args, config)
        else
          find_by_namespace("rake").perform(full_namespace, args, config)
        end
      ensure
        ARGV.replace(old_argv)
      end

      # Rails finds namespaces similar to Thor, it only adds one rule:
      #
      # Command names must end with "_command.rb". This is required because Rails
      # looks in load paths and loads the command just before it's going to be used.
      #
      #   find_by_namespace :webrat, :rails, :integration
      #
      # Will search for the following commands:
      #
      #   "rails:webrat", "webrat:integration", "webrat"
      #
      # Notice that "rails:commands:webrat" could be loaded as well, what
      # Rails looks for is the first and last parts of the namespace.
      def find_by_namespace(namespace, command_name = nil) # :nodoc:
        lookups = [ namespace ]
        lookups << "#{namespace}:#{command_name}" if command_name
        lookups.concat lookups.map { |lookup| "rails:#{lookup}" }

        lookup(lookups)

        namespaces = subclasses.index_by(&:namespace)
        namespaces[(lookups & namespaces.keys).first]
      end

      # Returns the root of the Rails engine or app running the command.
      def root
        if defined?(ENGINE_ROOT)
          Pathname.new(ENGINE_ROOT)
        elsif defined?(APP_PATH)
          Pathname.new(File.expand_path("../..", APP_PATH))
        end
      end

      def print_commands # :nodoc:
        commands.each { |command| puts("  #{command}") }
      end

      private
        COMMANDS_IN_USAGE = %w(generate console server test test:system dbconsole new)
        private_constant :COMMANDS_IN_USAGE

        def commands
          lookup!

          visible_commands = (subclasses - hidden_commands).flat_map(&:printing_commands)

          (visible_commands - COMMANDS_IN_USAGE).sort
        end

        def command_type # :doc:
          @command_type ||= "command"
        end

        def lookup_paths # :doc:
          @lookup_paths ||= %w( rails/commands commands )
        end

        def file_lookup_paths # :doc:
          @file_lookup_paths ||= [ "{#{lookup_paths.join(',')}}", "**", "*_command.rb" ]
        end
    end
  end
end
```

Bem você pode achar esse arquivo em ... `  ` `/usr/local/bundle/gems/railties-6.1.1/lib/rails`

Vamos analisar: 

* [Explicação do Katz][metaprogramacao_com_katz]
* %w(-h -? --help) - Você estará criando um array para ser atribuido à `HELP_MAPPINGS`





 



[doc_rails_initialization_process]: https://guides.rubyonrails.org/initialization.html
[ frozen_string_literal]: https://stackoverflow.com/questions/37799296/ruby-what-does-the-comment-frozen-string-literal-true-do
[metaprogramacao_com_katz]: https://yehudakatz.com/2009/11/15/metaprogramming-in-ruby-its-all-about-the-self/