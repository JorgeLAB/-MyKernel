# Rails

## Configuring Rails Application

[Rails DOC][configuring_rails_application_doc]

> Anotações das configurações e recursos de inicialização do rails.

## Localização

O rails oferece 4 padrões para inicialização de código - **Essa é a ordem de inicialização **

*  config/application
* Environments-specific configuration files
* Initializers
* After-initializers

## Rodar um código antes do rails

Para rodarmos um código antes do carregamento da applicação utilizamos o arquivo ` ` ` config/application.rb`, um caso raro de exemplo seria de imprimir um mensagem na tela:

```ruby
require_relative 'boot'

puts "Antes de importarmos o rails/all estamos chamando esta mensagem"
require 'rails/all'

Bundler.require(*Rails.groups)

module ApplicationForTests
  class Application < Rails::Application

    config.load_defaults 6.0
  end
end
```

##  Configurando componetes  do rails 

Configurar o Rails significa configurar suas componentes e o **rails é um componente**. Quando configuramos o `config/application.rb` ou o `environment-specific configuration files` (  aqueles arquivos em environments, production, development ou tests  ) basicamente estamos permitindo a especificação de variáveis de configuração para todos os componentes.

Por exemplo em `config/application.rb`:

```ruby
config.time_zone = 'Central Time (US & Canada)'
```

Caso queiremos configurar componentes específicas do rails podemos fazê-lo de forma a usar o objeto `config` a componente que gostatiamos de ser modificada.

Por exemplo em `config/application.rb`:

```ruby
config.active_record.schema_format = :ruby
```

* [ postgres_with_rails ]( http://lucasnogueira.me/PostgreSQL-e-Rails )

* [stack_overflow_issue](https://stackoverflow.com/questions/41369035/in-rails-5-setting-config-active-record-schema-format-sql-but-still-getting)

O exemplo mostrado acimo já é default do rails mas tomando o artigo como exemplo para podermos implementar sql direto em uma schema rails temos de modificar sua configuração em ` config/application.rb`:

```ruby
config.active_record.schema_format = :sql
```

##  Configuração geral do Rails

Esses métodos de configuração serão chamado no objeto <u>Rails::Railtie</u> , assim como pelas suas subclasses <u>Rails::Engine</u> ou <u>Rails::Application</u>.

 Vamos analisar o que temos por default no ambiente de desenvolvimento: 

* **config.cache_classes** : It determines whether or not your application classes are reloaded on each request. If it's true, you have to restart your server for code changes to take effect (i.e. you set it to true in production, false in development.) . - [x1a4](https://stackoverflow.com/users/340799/x1a4)  [ stack_overflow](https://stackoverflow.com/questions/2919988/rails-4-what-is-cached-when-using-config-cache-classes-true) ( Este comentário achei o mais esclarecedor, veja em ambiente de desenvolvimento deixar está componente em true não é interessante pois toda vez que modificarmos uma classe automaticamente server irá recarregar sem nem terminarmos a implementação mais em produção uma modificação relalizada é algo mais certeiro que já passou por desenvolvimento, então deixa em true asegura o recarregamento.  )

  * [performance_stackOverflow - como usar esse método ](https://stackoverflow.com/questions/2879891/config-cache-classes-true-in-production-mode-has-problems-in-ie)

  ```ruby
   # In the development environment your application's code is reloaded on
    # every request. This slows down response time but is perfect for development
    # since you don't have to restart the web server when you make code changes
  ```

  Setting cache_classes to false will have big impact on your app performance.

* 

[configuring_rails_application] : https://guides.rubyonrails.org/configuring.html



