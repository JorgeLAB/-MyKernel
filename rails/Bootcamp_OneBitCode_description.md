# Bootcamp OneBitCode 10

## Iniciando o projeto 

### Nesse aula vamos dar os primeiros passos para iniciar nosso projeto.

- Estamos utilizando o gerenciador de versões asdf. Optamos por seguir com ele por possuir plugins que conseguem gerenciar versões de diversas linguagens. Para instalar basta ir à este link: [ASDF](https://asdf-vm.com/#/core-manage-asdf-vm)

- Antes de começarmos a instalação do Ruby e Rails, vamos precisa instalar o pacote de desenvolvimento do openSSL.

  ```shell
  sudo apt install libssl-dev
  ```

- Agora vamos adicionar o plugin para o ASDF para Ruby. Para isso, execute no terminal:

  ```shell
  asdf plugin add ruby
  ```

- Após o plugin ser adicionado, vamos instalar a versão 2.7.1 do Ruby. Execute no terminal:

  ```shell
  asdf install ruby 2.7.1
  ```

- Vamos deixar a versão 2.7.1 como padrão:

  ```shell
  asdf global ruby 2.7.1
  ```

- Após isso podemos instalar o rails:

  ```shell
  gem install rails -v '6.0.3.3'
  ```

- Antes de inicializarmos o app, precisamos instalar um biblioteca que o drive do Postgres utiliza.

  ```shell
  sudo apt install libpq-dev
  ```

- Após a instalação podemos iniciar nossa api. Obserção: --api : instala apenas o necessário para uma api && -d postgresql ( istalação do banco ) && -T evita a instalação de testes automáticamente. 

  ```shell
  rails new ecommerce-api --api -d postgresql -T
  ```

- Vamos fazer uma pequena alteração no nosso arquivo **database.yml** para poder utilizar o configs do Postgres. Então vou deixar todo o conteúdo dele deste jeito:

  ```yml
  default: &default
    adapter: postgresql
    encoding: unicode
    username: <seu usuário>
    password: <sua senha>
    pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  
  development:
    <<: *default
    database: ecommerce_api_development
  
  test:
    <<: *default
    database: ecommerce_api_test
  
  production:
    <<: *default
    database: ecommerce_api_production
    username: ecommerce_api
    password: <%= ENV['ECOMMERCE_API_DATABASE_PASSWORD'] %>
  ```

- Com o rails inicializado, podemos entrar no diretório e criar o banco.

  ```shell
  cd ecommerce-api
  rails db:create
  ```

- Também vamos configurar a versão local do Ruby para 2.7.1.

  ```shell
  asdf local ruby 2.7.1
  ```

  > É importante lembrarmos que está configuração só é válida para o ASDF, não tem nenhuma ligação com Rails em si. Quando queremos travar uma versão para um projeto RAils, ela é feita no GemFile.
  >
  > 

- Agora podemos iniciar o servidor.

  ```shell
  rails s
  ```

- Agora basta irmos na nossa ferramenta de request e testar a URL railz. 

## Configurando o envio de email

> ​	Nesta aula vamos instalar o **Mailcatcher** , que é um servidor SMTP simplista que tem o objetivo de pegar as requisições SMTP que recebe e exibi-las numa interface-web. Ela é bem interessante para vermos o envio de email que está sendo feito no ambiente de desenvolvimento.

-   Antes de instalarmos o mailcatcher precisamos instalar algumas ferramentas de suporte.

  ```shell
  sudo apt install build-essential 
  ```

- Vamos instalar o SQLite3 e a ferramenta  de suporte para acesso a ele.

  ```shell
  sudo apt install libsqlite3-dev
  sudo apt install sqlite3
  ```

- O **Mailcatcher** deve ser instalado separado da nossa aplicação:

  ```shell
  gem install mailcatcher
  ```

- Com ele instalado vamos até as configurações de ambiente de desenvolvimento de nosso app, configurar o envio de email via **SMTP** .

  ```ruby
  #config/environments/development.rb
  config.action_mailer.raise_delivery_errors = false
  
  config.action_mailer.default_url_options = { host: "http://localhost:3000" }
  
  config.action_mailer.delivery_method = :smtp
  
  config.action_mailer.smtp_settings = {
    address: 'localhost',
    port: 1025,
  }
  ```

  >  A opção de default_url_options informamos  a URL de base que será utilizada nas rotas que forem chamadas a partir da view de um email. Estamos colocando por padrão o host da API porque apenas de uma API não ter rotas de views, podem haver roras que não rederizam uma view, mas enviam um arquivo, por exemplo. As rotas que forem necessárias que sejam enviadas pelo front-end, são utilizados helpers de rotas.
  >
  > As outras duas são para informar que camos usar o método de entrega via SMTP e as configurações para 	enviar para o **Mailcatcher** .
  >
  > 

- Para testar se está tudo functionando corretamente vamos testar executando mailcatcher.

  ```shell
  mailcatcher
  ```

  > Ele irá iniciar dois end-points: um na porta 1025 para o SMTP que configuramos acima e por outro na porta 1000 para acessarmos uma página web ond epodemos ver os emails forem enviados.
  >
  > 

- Agora vamos abrir o navegador em: http://localhost:1080

- Agora vamos rodar o console do Rails

  ```shell
  rails c
  ```

- Agora no console, vamos utlizar o ActionMailer para fazer o envio de teste.

  ```shell
  ActionMailer::Base.mail(to: "teste@teste.com", from:"anyone@test.com", subject: "Testando o mailer", body:"Mais um teste para nosso projeto").deliver_now!
  ```

- Agora vá até a página do **Mailcatcher** no navegador e veja se houve alguma alteração.

## Instalação do Devise Token Auth

- O **Devise Token Auth** utiliza uma tácnica de login que recebe usuário e senha para retornar um token para o usuário. A **cada request que o usuário envia, ele receberá a resposta e um novo token para utlizar no próximo request** . [gem](https://github.com/lynndylanhurley/devise_token_auth) Então caso o usuário venha a fazer uma requisiçaõ com token antigo ele será impedido.

- Vamos começar limpando o Gemfile, removendo comentários que tem no arquivo e dividindo por responsabilidade.

  ```ruby
  source 'https://rubygems.org'
  git_source(:github) { |repo| "https://github.com/#{repo}.git" }
  
  ruby '2.7.1'
  
  gem 'rails', '~> 6.0.3', '>= 6.0.3.3'
  
  # Basic
  gem 'bootsnap', '>= 1.4.2', require: false
  gem 'pg', '>= 0.18', '< 2.0'
  gem 'puma', '~> 4.1'
  
  group :development, :test do
    gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
    gem 'pry-byebug'
  end
  
  group :development do
    gem 'listen', '~> 3.2'
    gem 'spring'
    gem 'spring-watcher-listen', '~> 2.0.0'
  end
  
  gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
  ```

- Agora vamos adicionar no Gemfile o **Devise Token Auth** que utilizaremos para a autenticação do Token .

  ```ruby
  gem 'puma', '~> 4.1'
  
  # Auth
  gem 'devise_token_auth', '~> 1.1.4'
  ```

- Com ele adicionado podemos instalar.

  ```shell
  bundle install
  ```

  Deu erro não ??

  ```shell
  bundle update
  ```

  Isso ocorre por conta de conflito de versão com outras gems.

- Agora nosso próximo passo é inicializa o tanto o **devise** quanto o **devise_token_auth**. Perceba que não foi necessário instalar o devise separadamente. Então primeiramente iremos instalar os arquivos de inicialização do devise.

  ```shell
  rails g devise:install
  create  config/initializers/devise.rb
  create  config/locales/devise.en.yml
  ```

- Para o generator do **Devise Token Auth** nós precisaremos passar o model que queremos gerar e qual o escopo das rotas que serão utlizadas. Vamos geral o model User no escopo de rotas **auth/v1/user**.

  ```shell
  rails g devise_token_auth:install User auth/v1/user
  
  create  config/initializers/devise_token_auth.rb
  insert  app/controllers/application_controller.rb
  gsub  config/routes.rb
  create  db/migrate/20201112225021_devise_token_auth_create_users.rb
  create  app/models/user.rb
  ```

- O primerio do arquivo gerados foi o de configuração do devise token auth.  config/initializers/devise_token_auth.rb

  ```ruby
  # Comecemos habilitando o Token Refresh, para que o token seja modificado a cada request
  config.change_headers_on_each_request = true
  ```

- Vamos configurar também o tempo de expiração deste token para 2 semanas.

  ```ruby
  config.token_lifespan = 2.week
  ```

- Temos uma última configuração para habilitar que é o tempo em que o mesmo token pode ser utilizado em caso de ataque em massa.

  ```ruby
  config.batch_request_buffer_throttle = 5.seconds
  ```

- O **Devise Token Auth** gerou para nós também o model User com o seguinte conteúdo.

  ```ruby
  # frozen_string_literal: true
  
  class User < ActiveRecord::Base
    # Comentários podem ser deletados
    # Include default devise modules. Others available are:
    # :confirmable, :lockable, :timeoutable, :trackable and :omniauthable
    devise :database_authenticatable, :registerable,
           :recoverable, :rememberable, :validatable
    include DeviseTokenAuth::Concerns::User
  end
  ```

  > O **include** deve permanecer após as configurações do Devise porque ele possui uma configuração padrão e se colocarmos antes, esta configuração será usada ao invés da que definirmos.
  >
  > 

- Junto com o model também foi criado uma migration para o usuário. Nela existem campos que não vamos utilizar, como o nickname e o image.

  ```ruby
  t.string :nickname
  t.string :image
  ```

- Vamos na mesma migration definir o campo profile com valor padrão 1. Na parte de models nós vamos entender o porque desse valor padrão.

  ```ruby
  t.string :email
  t.integer :profile, default: 1
  ```

- Agora podemos executar as migration para gerar a tabela User.

  ```shell
  rails db:migrate
  ```

- Vamos criar um usuário para testarmos a tabela.

  ```shell
  rails c 
  ```

- Agora vamos criar um usuário pelo console. (   name, email, password e password_confirmation são colunas padrão do devise token auth )

  ```ruby
  User.create(name: "Test", email: "test@test.com", password: "123456", password_confirmation: "123456", profile: 0)
  ```

## Configurando a autenticação na API - Parte 1  e Parte 2

- Antes de configurar as autenticações, vamos dar uma olhada nas rotas que temos.

  ```ruby
  # config/routes.rb
  Rails.application.routes.draw do
    mount_devise_token_auth_for 'User', at: 'auth/v1/user'
  end
  ```

- Se você rodar ``rails routes`` conseguirá ver todas as rotas geradas pelo devise token auth.

- Nossos endpoints terão a parte de Admin e Storefront isoladas, cada um com sua própria estrutura. Vamos então começar criando a estrutura de diretórios para uma delas.

  ```shell
  mkdir -p app/controllers/admin/v1
  mkdir -p app/controllers/storefront/v1
  ```

- Junto com isso iremos criar um controller raiz para um deles chamando:

  ```shell
  rails g controller admin/v1/api
  rails g controller storefront/v1/api 
  
  create  app/controllers/admin/v1/api_controller.rb
  create  app/controllers/storefront/v1/api_controller.rb
  
  ```

  Os dois foram criados, porém vamos fazer uma adaptação nos dois para que ele siga as recomendações do styleguides do Ruby.

  ```ruby
  # app/controllers/admin/v1/api_controller.rb
  module Admin::V1
  	class ApiController < ApplicationController
  	end
  end
  ```

  ```ruby
  # app/controllers/storefront/v1/api_controller.rb
  module Storefront::V1
  	class ApiController < ApplicationController
  	end
  end
  ```

- Agora vamos até as rotas adicionar os escopos **/admin/v1** e **/storefront/v1**

  ```ruby
  #config/routes.rb
  Rails.application.routes.draw do 
      mount_devise_token_auth_for 'User', at: 'auth/v1/user'
      
      namespace :admin do
          namespace :v1 do
          end
      end
      
      namespace :storefront do
          namespace :v1 do
          end
      end
  end
  ```

   

- Agora vamos remover a configuração do **Devise Token Auth** que foi adicionado no **ApplicationController** junto ao generator, já que não o utilizaremos.

  ```ruby
  #app/controllers/application_controller.rb
  #Remova
  include DeviseTokenAuth::Concerns::SetUserByToken
  ```

- Para que possamos compartilhar essa autenticação nesses dois escopos , vamos adicionar um concern chamado **Authenticable** 

  ```shell
  touch app/controllers/concerns/authenticable.rb
  ```

- Adicionaremos em nosso concern o seguinte:

  ```ruby
  module Authenticable
  	extend ActiveSupport::Concern
  	
  	included do
      	include DeviseTokenAuth::Concerns::SetUserByToken
      	before_action :authentication_user!
  	end
  end
  ```

  **Perceba que neste concern estamos incluindo a autenticação do Devise Token Auth para autenticação por token e abaixo estamos chamando o método de autenticação. Ou seja, todo controller que incluir este concern será autenticado.**

- Agora vamos até os ApiControllers que criamos anteriormente e incluir este concern.

  ```ruby
  # app/controllers/admin/v1/api_controller.rb
  module Admin::V1 
  	class ApiController < ApplicationController
  		include Authenticable
  	end
  end
  ```

  ```ruby
  # app/controllers/admin/v1/api_controller.rb
  module Storefront::V1 
  	class ApiController < ApplicationController
  		include Authenticable
  	end
  end
  ```

- Agora, para finalizar, precisamos alterar os parâmetros default  que o Devise recebe no endpoint de cadastro. Por padrão o Devise recebe somente **email, senha e confirmação de senha** . Precisamos acrescentar o nome. Para isso iremos acrescentar um **before_action** no **ApplicationController** para configurar os parâmetros do Devise caso o controller chamado seja dele.

  ```ruby
  #app/controllers/application_controller.rb
  class ApplicationController < ActionController::API 
  	before_action :configure_permitted_parameters, if: :devise_controller?
  	
  	protected
  	
  	def configure_permitted_parameters
  		devise_parameter_sanitizer.permit(:sign_up, keys: [:name, :email, :password, :password_confirmation])
  	end
  end
  ```

  

  Este método **devise_parameter_sanitizer.permit** é padrão do Devise e com ele conseguimos definir parâmetros para o **Sign Up** .

  TODO: Colocar um comentário do whatsapp

- Agora para testarmos as nossas rotas, vamos criar uma rota **/home** dentro do escopo **/admin/v1** para tentarmos o login: 

  ```ruby
  #app/controllers/admin/v1/home_controller.rb
  module Admin::V1
  	class HomeController < ApiController
  		def index
  			render json: { message: "Uhu!"}
  		end
  	end
  end
  ```

  Perceba que criamos o controller herdando de ApiController, ou seja, o usuário deve ser autenticado. Agora vamos adicionar a rota para este controller.

  ```ruby
  # config/routes.rb
  namespace :admin do
  	namespace :v1 do
  		get :home => "home#index"
  	end
  end
  ```

- Vamos testar nossa rota de login. Necessita-se fazer um sign_up de um usuário para se logar. Já logado temos de ter os seguintes dados para podemos acessar um request.

  ```
  # Header que é retornado ao se fazer login.
  access-token → R5H71fyQ2HKU1giw9QSRfw
  client → MnGtHfjak92UbV11OMOqlw
  expiry → 1604267272
  token-type → Bearer
  uid → test@test.com
  ```

  Podemos pegar os dados retornados e podemos enviar uma requisição para a rota **home**.

  TODO: Esta parte tenho de trabalhar mais na descrição usando imagens com o POSTMAN.

## Endpoint para troca de Senha 

- O **Devise Token Auth** já vem por padrão com o endpoint para a troca de senha. Nesta aula nós vamos fazer uma configuração para que possamos relacionar o usuário para uma tela de frontend quando a troca de senha for requisitada.

  - Primeriro, é importante frisar que quando lidamos com API, é interessante termos um ambiente agnóstico. (Ele não responde somente a um tipo de interface). 
  - A requisição de troca é enviada com dois parâmetros: o email do usuário e um atributo **redirect_url**. O redirect_url é a URL da tela da troca de senha do usuário, já que sendo uma API agnóstica, ela não sabe o link da tela.
  - O usuário que requisitou receberá um email contendo as instruções de como poderá proceder para substituí-la. Irá também com um atributo chamado **reset_password_token**, que é o token que será necessário para a troca de senha.
  - Este **reset_password_token** será enviado pela tela de troca senha.

- Precisamos adicionar uma configuração ao **Devise Token Auth** para que a troca de token seja obrigatório.

  ```ruby
  #config/initializers/devise_token_auth.rb
  
  DeviseTokenAuth.setup do |config|
      config.require_client_password_reset_token = true
  
  ```

  

  TODO: Fazer o envio de confirmação de sign_up por meio do config.enable_standard_devise_support = false

  [Gem doc devise-token-auth](https://devise-token-auth.gitbook.io/devise-token-auth/config/omniauth)

- Agora vamos alterar o template do email de troca de senha para que possamos enviar o email com link do redirect_url junto com o atributo **reset_password_token**.

  ```shell
  #Geremos a view de troca do Devise
  rails g devise:views -v mailer
  
   create    app/views/devise/mailer
   create    app/views/devise/mailer/confirmation_instructions.html.erb
   create    app/views/devise/mailer/email_changed.html.erb
   create    app/views/devise/mailer/password_change.html.erb
   create    app/views/devise/mailer/reset_password_instructions.html.erb
   create    app/views/devise/mailer/unlock_instructions.html.erb
  
  ```

  *Dos arquivos que foram gerados precisamos apenas do **reset_password_instructions.html.erb** , então podemos remover os outros arquivos.

  ```shell
  confirmation_instructions.html.erb
  email_changed.html.erb
  password_change.html.erb
  unlock_instructions.html.erb
  #Podemos também remover o diretório shared do Devise que foi criado.
  ```

- Agora podemos alterar o link que existe no email de instruções de troca de senha para que ele utilize como link o que está em redirect_url.

  ```ruby
  # app/views/devise/mailer/reset_password_instructions.html.erb
  <p><%= link_to 'Change my password', "#{message['redirect-url']}?reset_password_token=#{@token}" %></p>
  ```

- Agora podemos fazer os testes de troca de senha. Para isso, precisamos antes iniciar o Mailcatcher, já que as instruções são enviadas por email.

  ```shell
  mailcatcher
  ```

  Abrindo o navegador no link http://localhost:1080 e podemos também iniciar o servidor do Rails.

  ```shell
  rails s
  ```

  Vamos fazer o seguinte teste:

  - Enviar um request **POST** http://locahost:3000/auth/v1/user/password com o email e redirect_url .
  - Ver o link com o reset_password_token no **Mailcatcher**
  - Enviar um request **PATCH**  http://locahost:3000/auth/v1/user/password com **reset_password_token** e a nova senha com confitmação.

## Criando um email responsivo com o Foundation

### 	Como pré-visualizar seu email

- Rode no console 

  ```shell
  mkdir -p test/mailers/previews/devise
  ```

- Dentro dessa pasta crie um arquivo chamado mailer_preview.rb

  ```ruby
  class Devise::MailerPreview < ActionMailer::Preview
  	def reset_password_instructions
  		Devise::Mailer.reset_password_instructions(User.first, {})
  	end
  end
  ```

- Subindo o servidor, poderemos acessar a visualização por: [path]( http://localhost:3000/rails/mailers/devise/mailer/reset_password_instructions)

1 - instalar as gems:

gem 'inky-rb', require: 'inky'
gem 'premailer-rails'
gem 'sass-rails'

Rodar os comandos de instalação das mesmas

2 - Em config/application.rb

Descomentar a linha require "sprockets/railtie"

3 - Em config/initializer/devise.rb

mudar a config: config.parent_mailer para config.parent_mailer = 'ApplicationMailer'

###    Passos para configurar sua API com gem foundation

### 	TODO : RETORNAR DEPOIS CONTINUANDO ....

[link1](https://get.foundation/emails/docs/gem-guide.html)

[link2](https://github.com/foundation/foundation-emails#using-the-ruby-gem)

[link3](https://github.com/foundation/foundation-emails/blob/master/migration.md)

[link4](https://www.sitepoint.com/responsive-emails-rails-ink/)

## Configurando o Rack CORS

- Significa "Cross-origin Resource Sharing" ou "Compartilhamento de Recurso de Origem Cruzada" , basicamente é a nossa decisão de aceitar responder a uma requisição que vem de outro domínio. Precisamos habilitar **quando estamos trabalhando com um API** , normalmente as informações de uma API são carregadas de uma origem diferente da que ela se encontra.

- Vamos adicionar a gem **Rack-cors** ao nosso Gemfile

  ```ruby
  gem 'devise_token_auth', '~> 1.1.4'
  # CORS
  gem 'rack-cors', '~> 1.1.1'
  ```

  ```shell
  bundle install
  ```

- Quando inicializamos o rails em modo api-only ele já vem com uma configuração comentada para o Rack cors, porém vamos usar uma diferente:

  ```ruby
  # config/initializers/cors.rb
  Rails.application.config.middleware.insert_before 0, Rack::Cors do
    allow do
      origins '*'
      resource '*', 
      headers: :any, 
      methods: [:get, :post, :put, :patch, :delete]
    end
  end
  ```

- Nós configuramos o **RackCORS** como middleware do Rails. Este _insert_before 0_  está informando que estamos adicionando o Rack::CORS antes do middleware 0, ou seja, ele vai ser o primeiro. Estamos configurando para aceitar requisições de qualquer origem com _origins '*'_ e também que seja consumido qualquer recurso com cabeçalho, desde que sejam os métodos HTTP **GET, POST, PUT, PATCH ou DELETE** com o *resource* *. Com essa configuração não teremos nenhum problema quando formos para o front, já que lá precisaremos iniciar o servidor numa porta diferente do servidor da API.

## Alteração do idioma para o Português 

- Por padrão, o Rails vêm com i18n configurado para inglês. Nesta aula faremos as configurações necessárias para que tenhamos eles em português.

- Primeiro precisamos alterar as configurações da aplicação.

  ```ruby
  # config/application
  config.load_defaults 6.0
  # I18n config
  config.i18n.default_locale = :'pt-BR'
  ```

- Como colocamos pt-BR, ele vai dentro de config/locales, carregar todos os YMLs e verificar aqueles que começam com **pt-BR**.

  - Vamos criar um diretório dentro de config/locales para cada idioma. Porém o Rails carrega somente os arquivos que estão dentro do config/locales sem considerar os subdiretórios. Então vamos adicionar mais uma configuração para ele carregar tudo que está neste diretório, considerando os subdiretórios.

    ```shell
    # config/application.rb
     config.i18n.load_path += Dir[Rails.root.join('config/locales/**/*.{rb,yml}')]
     config.i18n.default_locale = :'pt-BR'
    ```

    

  - Desta forma estaremos adicionando às configs no i18n uma configuração de carregamento. Com isso o Dir vai listar todos os arquivos, incluíndo subdiretórios, que estão dentro de config/locales e adicionar o path do i18n.

  - Agora iremos criar um diretório para colocarmos os locales para o idioma pt.

    ```shell
    mkdir config/locales/pt-BR
    ```

  - O rails possui as configurações em inglês por padrão. Como estamos alterando para o português, precisamos criar um arquivo locale em pt-BR para o ActiveRecord e o Devise, já que possuem mensagens de validação. Mas, graças à comunidade que temos, já existe disponível um arquivo contendo todas as chaves **i18n** do Active Record e do Devise em Português. Para isso, vamos criar os arquivos de YML para o Devise e o Active Record.

    ```shell
    touch config/locales/pt-BR/{active_record,devise}.yml
    ```

  - Com os arquivos criados vamos ao site: [i18n - pt-BR - activeRecord](https://github.com/svenfuchs/rails-i18n/blob/master/rails/locale/pt-BR.yml)

  - [i18n - pt-BR - Devise](https://gist.github.com/mateusg/924555) - Copie todo o conteúdo para o arquivo do **Devise**

  - Por fim podemos remover os antigos arquivos en.yml e do devise.yml, já que não serão utilizados.

    ```shell
    rm config/locales/{en,devise.en}.yml
    ```

  - Apenas uma última observação, se formos reparar o começo dos dois arquivos, ele possui uma chave *pt-BR*. É justamente ela que o Rails considera para selecionar as chaves do *locale*. Ela deve ser igual à que colocamos configuração do *default_locale* no *application.rb*.

    

  ## Configurando os testes com RSpec

  - Instalação do **RSpec** mais 3 ferramentas de suporte. **Factory Bot**, **Faker** e **Shoulda Matchers**.

    ```shell
    # adicionando no Gemfile
    group :development, :test do
      gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
      gem 'factory_bot_rails'
      gem 'faker'
      gem 'rspec-rails', '~> 4.0.1'
      gem 'shoulda-matchers', '~> 4.0'
    ```

  - Agora podemos instalar as ferramentas com o **Bundle**.

  - Vamos iniciar o RSpec.

    ```shell
    rails g rspec:install	
    create  .rspec
    create  spec
    create  spec/spec_helper.rb
    create  spec/rails_helper.rb
    ```

    

  - O RSpec gerou os arquivos de configuração dele **spec_helper.rb** e o de configuração dele com o Rails, é o **rails_helper.rb** .

  - Vamos remover todos os comentários do spec/rails_helper.rb. É importante te-lo bastante limpo.

    ```ruby
    require 'spec_helper'
    ENV['RAILS_ENV'] ||= 'test'
    require File.expand_path('../config/environment', __dir__)
    # Prevent database truncation if the environment is production
    abort("The Rails environment is running in production mode!") if Rails.env.production?
    require 'rspec/rails'
    
    begin
      ActiveRecord::Migration.maintain_test_schema!
    rescue ActiveRecord::PendingMigrationError => e
      puts e.to_s.strip
      exit 1
    end
    
    RSpec.configure do |config|
      config.fixture_path = "#{::Rails.root}/spec/fixtures"
      config.use_transactional_fixtures = true
      config.infer_spec_type_from_file_location!
      config.filter_rails_from_backtrace!
    end
    ```

  - Vamos configurar RSpec tanto para usar o **Factory Bot** quanto o **Shoulda Matchers**, para que possamos utilizar os recursos padrões deles dentro dos nossos testes, vamos criar um diretório chamado support.

    ```shell
    mkdir spec/support
    ```

    Dentro dele iremos criar um arquivo para cada uma das duas ferramentas.

    ```shell
    touch spec/support/{factory_bot, shoulda_matchers}.rb
    ```

  - Configurando o **Factory_Bot**.

    ```ruby
    # adicione em spec/support/factory_bot
    RSpec.configure do |config|
      config.include FactoryBot::Syntax::Methods
    end
    ```

    Estamos utilizando o método configure do RSpec para incluir o módulo **FactoryBot::Syntax::Methods** do FactoryBot.

  - Para o **Shoulda_Matchers**:

    ```ruby
    # spec/support/shoulda_matchers.rb
    Shoulda::Matchers.configure do |config|
      config.integrate do |with|
        with.test_framework :rspec
        with.library :rails
      end
    end
    ```

    Para o **Shoulda_Matchers** , nós estamos configurando o framework de teste como **RSpec** e a biblioteca de aplicação como sendo rails. ( Acho que shoulda_matchers pode ser usado em mais de um framework por isso ter apontar para a biblioteca.)

  - Porém, o **RSpec** não faz um carregamento automático deste diretório **support** . Para que este diretório seja incluído na execução dos testes, vamos adicionar.

    ```ruby
    #spec/rails_helper.rb
    require 'rspec/rails'
    Dir[Rails.root.join('spec', 'support', '**', '*.rb')].each { |f| require f }
    ```

  - Estamos lendo todo o conteúdo do diretório spec/support e dando um require no arquivo. Desta forma tudo que estiver dentro do diretório será carregado.

  - Com **RSpec** configurado, sempre que tivermos testes e quisermos executar, basta executarmos o seguinte comando:

    ```shell
    bundle exec rspec
    ```

## Criação do primeiro Model

  - Criando o model Category com o generator do Rails.

    ```shell
    rails g model Category name
          invoke  active_record
          create    db/migrate/20201115142722_create_categories.rb
          create    app/models/category.rb
          invoke    rspec
          create      spec/models/category_spec.rb
          invoke      factory_bot
          create        spec/factories/categories.rb
    ```

  - Vamos executar o migration para criar a tabela no banco.

    ```shell
    rails db:migrate
    ```

  - Vamos começar escrevendo teste para o nosso model **Category** . Para isso, vamos remover a mensagem de **pending** que é gerada  com o arquivo.

    - Para o Model **Category** , nós queremos validar sempre que o nome é obrigatório e único. Então vamos testar isso:

      ```ruby
      RSpec.describe Category, type: :model do 
      	it { is_expected.to validate_presence_of(:name) }
      ```

    - Este **validate_presence_of** é um matcher incluído pelo **Shoulda Matchers** e o método **is_expected** também é do **Shoulda_Matchers**, mas podemos usar o **subject** do **RSpec**.

      ```shell
      it { expect(subject).to validate_presence_of(:name)}
      ```

        O **subject** do RSpec nada mais é que uma instância da classe que estamos, que o **Rspec** sabe através do **describe** que colocamos.

      Relembrando o **it** não precisa ter uma descrição.
      
    - Para outro teste precisamos saber se o nome da  **Categoria** é único. Para isso vamos acrescentar em nossos testes:
    
      ```ruby
      it { is_expected.to validate_presence_of(:name) }
      it { is_expected.to validate_uniqueness_of(:name).case_insensitive }
      ```
    
      O **validate_uniqueness_of** também é um matcher do **Shoulda Matchers** e é capaz de validar se um atributo possui valor único. Este matcher ainda tem a capacidade de verificar se a validação é case sensitive.
    
    - Ao executar o teste veremos que haverá falhas:
    
      ```shell
      bundle exec rspec
      ```
    
    - Com os testes feitos, agora vamos adicionar estas validações no nosso **Category**. Então para validar se o campo **name** é obrigatório e único, precisamos adicionar no nosso model
    
      ```ruby
      # app/models/category.rb
      class Category < ApplicationRecord
      	validates :name, presence: true, uniqueness: { case_sensitive: false }  
      ```
    
    - Se executarmos os testes novamente veremos que passam:
    
      ```shell
      bundle exec rspec
      ```
    
      
      
    - Vamos configurar a factory de categories que já foi criada pelo generate, sabemos que ela precisa ter nome único:
    
      ```ruby
      # spec/factory/categories_spec.rb
      FactoryBot.define do 
      	factory :category do
      		name {"MyString"}
      	end
      end
      ```
    
      Desta forma sempre que formos utilizar o Factory_Bot para gerar uma categoria através da factory category, ele vai criar com mesmo nome. Se tivermos duas Categorias dentro de um mesmo teste, o Factory Bot  vai criar ambas com mesmo nome. E como temos a validação que impede isso, no momento de criar a segunda teremos erro.
    
      Para evitar esse problema podemos usar o um recurso do **Factory Bot** chamado sequence. 
    
      ```ruby
      # spec/factories/categories.rb
      FactoryBot.define do
      	factory :category do
      		sequece(:name) {|n| "Category #{n}"}
      	end
      end
      ```
    
      Com o **sequence**, cada vez que a _factory_ for chamada dentro de um teste, o **n** vai receberá um valor diferente. 
    
      TODO: "3- Vamos começar escrevendo teste para o nosso model **Category**. Para isso, vamos remover a mensagem de **pending** que é gerada com o arquivo - pending "add some examples to (or delete) #{__FILE__}""
    
    
    
## Model de Requisitos do Sistema

- Vamos começar gerando o model pelo rails 

  ```shell
  rails g model SystemRequirements name operational_system storage processor memory video_board
  ```

- Vamos migrar esse model

  ```shell
  rails db:migrate
  ```

- Como vimos antes, estes models são criados com peddong que podemos remover.

  ```ruby
  # spec/models/system_requirement_spec.rb
  pending "add some examples to (or delete) #{__FILE__}"
  ```

- Vamos adicionar uma validação a este model em que seu name tem de ser único

  ```ruby
  #spec/models/category_spec.rb
  RSpec.describe Category, type: :model do
  	it { is_expected.to validate_presence_of(:name) }
  	it { is_expected.to validate_uniqueness_of(:name).case_insensitive }
  ```

  ```ruby
  #spec/models/system_requirement_spec.rb
  RSpec.describe SystemRequirement, type: :model do
  	it { is_expected.to validate_presence_of(:name) }
  	it { is_expected.to validate_uniqueness_of(:name).case_insensitive }
  	it { is_expected.to validate_presence_of(:operational_system) }
  	it { is_expected.to validate_presence_of(:storage) }
  	it { is_expected.to validate_presence_of(:processor) }
  	it { is_expected.to validate_presence_of(:memory) }
  	it { is_expected.to validate_presence_of(:video_board) }
  	
  ```

- Se executarmos nosso teste agora haverá falhas pois devemos primeiro validar os atributos em nosso model.

- Vamos então adicionar as validações que são necessárias.

  ```ruby
  class SystemRequirement < ApplicationRecord
      validates :name, presence: true, uniqueness: {case_sensitive: false}
      validates :operatinal_system, presence: true
      validates :storage, presence: true
      validates :processor, presence: true
      validates :memory, presence: true
      validates :video_board, presence: true
  end
  ```

- Se executarmos os testes verificaremos que tudo irá passar 

  ```shell
  bundle exec rspec
  ```

- Agora, iremos implementar o factory_bot de system_requirement.

  ```ruby
  FactoryBot.define do
  	factory :system_requirement do
  		sequence(:name){|n| "Base #{n}"}
  		operacional_system { Faker::Computer.platform }
  		storage { "#{Faker::Number.number(digites: 3)} GB" }
  		processor { Faker::Computer.os }
  		memory { "#{Faker::Number.number(digites: 2)} GB" }
  		video_board { "N/A" }
  	end
  end
  ```

## Model Polimórfico de Produtos

- O model **Product** será polimórfico, ou seja, ele será um model que vai possuir uma associação polimórfica. Estamos ele de forma polimorfica pois por hora possuimos apenas game como produto de venda mas futuramente poremos expandir para outros tipos de produto.

  Vamos lá criar o model, **Product** .

  ```shell
  rails g model Product name description:text "price:decimal{10,2}" productable:references{polymorphic} 
  
  Running via Spring preloader in process 701363
        invoke  active_record
        create    db/migrate/20201119203701_create_products.rb
        create    app/models/product.rb
        invoke    rspec
        create      spec/models/product_spec.rb
        invoke      factory_bot
        create        spec/factories/products.rb
  ```

   Estamos criando o model **Productable** com name, description, price sendo um decimal com tamanho de 10 e precisão 2 casas decimais e um campo polimórfico que chamamos de productable.

  Este campo **productable** gerou o seguinte:

  ```shell
  t.references :productable, polymorphic: true, null: false
  ```

  Esta linha cria duas colunas na tabela products no banco: **productable_type** e **productable_id**. Ele servirão para armazenar o nome do model e o ID de registro que **Product** está referenciando. São esses atributos no qual rails emprega para administrar a entidade polimórfica. E ambos os campos terão uma restrição no banco que **impede que sejam nulos**.

- Vamos migrar os dados agora

  ```shell
  rails db:migrate
  ```

- Com o model **Product** criado, vamos adicionar alguns testes de validação que queremos do model.

  ```ruby
  #spec/models/product_spec.rb
  RSpec.describe Product, type: :model do
  	it { is_expected.to validate_presence_of(:name) }
      it { is_expected.to validate_uniqueness_of(:name).case_insensitive}
      it { is_expected.to validate_presence_of(:description) }
      it { is_expected.to validate_presence_of(:price) }
      it { is_expected.to validate_numericality_of(:price).is_greater_than(0) }
  end
  ```

  Estamos adicionando testes para validações para name, description, price e um teste novo para validar se o valor de price é maior que o número 0, com o validate_numericality_of.

- Com os testes feitos, precisamos adicionar validações em nosso model. Se formos ver o model **Product** que foi criado, ele tem o seguinte conteúdo, que é para testar a existência da associação:

  ```ruby
  # app/model/product.rb
  class Product < ApplicationRecord
  	belongs_to :productable, polymorphic: true
  end
  ```

  Quando digitamos o comando de criação, nós definimos o productable como references, então ele já cuidou de colocar esse **belongs_to** no model. Bem eficiente mas não podemos de deixar de testá-lo.

  Para vamos adicionar o teste de associação, também com shoulda Matchers.

  ```ruby
  # spec/models/product_spec.rb
  
  RSpec.define :Product, type: :model do
  	it { is_expected.to belong_to :productable }
  	....
  ```

   Simples desse jeito, **Shoulda Matchers** tem o matcher _belong_to_ que testa se o model tem uma associação belongs_to.

- Se executarmos os testes agora teremos vários erros de validação:

  ```shell
  bundle exec rspec
  ```

  A associação não dará erro porque, ela já existe no model **Product**.

- Pois bem vamos adicionar as validações nos models.

  ```ruby
  #app/model/product.rb
  belongs_to :productable, polymorphic: true
  validates :name, presence: true, uniqueness: { case_sensitive: false }
  validates :description, presence: true
  validates :price, presence: true , numericality: { greater_than: 0}
  ```

- Podemos testar novamente:

  ```shell
  bundle exec rspec
  
    Shoulda::Matchers::ActiveRecord::ValidateUniquenessOfMatcher::ExistingRecordInvalid:
         validate_uniqueness_of works by matching a new record against an
       existing record. If there is no existing record, it will create one
         using the record you provide.
     
         While doing this, the following error was raised:
       
         PG::NotNullViolation: ERROR:  null value in column "productable_type" violates not-null constraint
           DETAIL:  Failing row contains (1, null, null, null, null, null, 2020-11-19 20:51:27.876204, 2020-11-19 20:51:27.876204).
     
         The best way to fix this is to provide the matcher with a record where
         any required attributes are filled in with valid values beforehand.
  ```
  
  Quase todos nossos testes são executados exceto pelo teste de unicidade do campo **name** que da um erro de violação e de chave estrangeira em **productable_type**.
  
  Isso ocorre pois é definido na criação da associação productable uma restrição que impede a validação de uma instância productable sem **productable_type** e **productable_id**.
  
  <blockquote>
      Quase todos os nossos testes passaram, exceto pelo teste de unicidade do campo name que da um erro de violação e chave estrangeira em productable_type.Este erro ocorre porque na criação da associação productable, foi criado a restrição que impede que productable_type e productable_id sejam nulos.
  
  ​    
  
  Esse erro ocorre somente com o uniqueness porque para testar a unicidade de nosso model, ele precisa resgistrar outro no banco e verificar se ocorre  o erro quando tentar salvar um segundo com o mesmo nome. Porém, o **subject** do RSpec considera apenas o model atual quando cria o objeto, não considera as associações. Para funcionar, nós precisamos configurar o subject para que possua um **productable**.

-  Vamos configurar o factory do model product para adequalo.

  ```ruby
  #spec/factories/products.rb
  
  factory :product do
      sequence(:name) { |n| "Product #{n}"}
      description { Faker::Lorem.paragraph}
      price{ Faker::Comerce.price(range: 100.0..400.0 ) }
  end
  ```

  Agora podemos criar o model **Game** que associarar-se model **Product**.

## Model de Games

- ​	O  model game fará associação polimórfica com o model Product. Vamos começar criando o model:

  ```shell
  rails g model Game mode:integer release_date:datetime developer system_requirement:references
  
  Running via Spring preloader in process 760344
        invoke  active_record
        create    db/migrate/20201120105337_create_games.rb
        create    app/models/game.rb
        invoke    rspec
        create      spec/models/game_spec.rb
        invoke      factory_bot
        create        spec/factories/games.rb
  
  ```

  Além dos campos mode, release_date e developer nosso model terá o campo system_requirement que será uma associação.

- Podemos executar os migrations para gerar a criação das tabelas

  ```shell
  rails db:migrate
  ```

- Agora vamos adicionar os testes de validação que queremos fazer para o model Game.

  ```ruby
  # spec/models/game_spec.rb
  RSpec.decribe :game do
  	it { is_expected.to validate_presence_of(:mode) }
  	it { is_expected.to validate_presence_of(:release_date) }
  	it { is_expected.to validate_presence_of(:developer) }
      
  	it { is_expected.to belong_to :system_requirement }
  end
  ```

- Antes de fazermos as validações no model Game, é interessante notar que o campo mode será enum, um recurso padrão do Rails permite que a gente faça o mapeamento de valores. Para vermos se um **enum** está sendo aplicado, podemos implementar o seguinte matcher do **Shoulda Matcher**

  ```ruby
  # spec/models/game_spec.rb
  it { is_expected.to define_enum_for(:mode).with_values( {pvp: 1, pve: 2, both: 3} ) }
  ```

  **Também não podemos esquecer que Game tem uma associação  polimórfica com Product, então vamos 	adicionar o teste para ela também.**

  ```ruby
  # spec/models/game_spec.rb
  it { is_expected.to have_one :product }
  ```

  **Podemos estar passando product sem precisarmos criar um campo na tabale de 	Game** o rails é inteligente suficiente para fazer essa associação campo sozinho.

- Agora podemos executar os testes e ver as falhas.

  ```shell
  bundle exec rspec
  ```

- Com os erros, agora podemos preencher o model Game. Vamos começar adicionando a associação com **Product** através da associação polimórfica **productable**.

  ```ruby
  class Game < ApplicationRecord
  	belongs_to :system_requirement
  	has_one :product, as: :productable # realmente não entendo o as ( acho que é um alias)
  end
  ```

- Então podemos adicionar as validações que fizemos para cada campo.

  ```ruby
  class Game < ApplicationRecord
  	belongs_to :system_requirement
  	has_one :product, as: :productable
  	
  	validate :mode, presence: true
  	enum mode:{ pvp:1, pve:2, both:3}
  	validates :release_date, presence: true
  	validates :developer, presence: true
  end
  ```

  Como podemos ver a definição de enum é bastante simples. E nele estamos passando uma hash com os valores que queremos mapeado para cada valor numérico.

- Execute os testes novamente

  ```shell
  bundle exec rspec
  ```

- Quase todos os testes foram executados, exeto o **Product** .

- Para corrijir isso vamos ao factory de game.

  ```ruby
  #spec/factories/game.rb
  FactoryBot.define do
    factory :game do
      mode { %i(pvp pve both).sample }
      release_date { '2020-06-01' }
      developer { Faker::Company.name }
      system_requirement
    end
  end
  ```

- Estamos informando todos os campos do model **Game**. Repare também, no último item, temos um system_requirement que é basicamente um atalho do factory. Toda a vez que criamos um model game também será criado um model system_requirement associado. 

  <blockquote>
      O Factory Bot tem um recurso que nos permite alterar um model que está sendo construído antes dele ser criado no banco. Então vamos utililizar isso na factory de Product
  </blockquote>

  
- Podemos melhor agora nossa factory **Product**.

  ```ruby
  #spec/factories/products.rb
  
  price { Faker::Commerce.price(range: 100.0..400.0) }
  after :build do |product|
  	product.productable = create(:game)
  end
  ```
  

Este recurso after :build permite que após a contrução de um objeto **Product** pelo factory, nós acessemos ele e crie um model Game utilizando a factory e atribuindo em productable. Este productable é um recurso da associação polimórfica que tem objeto <span style="color:red">que tem um objeto associado com o model utilizando</span> os campos **productable_type** e **productable_id**. 

- Podemos resolver agora o problema com os testes. Ele está dando erro pois o **subject** do **RSpec** está sendo criado como um **objeto** **Product** <span style="color:red">tem productable</span>. (TODO: Tenho de consertar o conteúdo do texto.)

  ```ruby
  RSpec.decribe Product, type: :model do
  	subject { build(:product) }
  
  ```

- Estamos criando um produto utilizando o método **create** do FactoreBot, chamando a factory product e atribuindo o **subject** do RSpec.

- Podemos então executar os testes:

  ```shell
  bundle exec rspec
  .....................
  Finished in 0.43693 seconds (files took 0.86602 seconds to load)
  21 examples, 0 failures
  
  ```

- Agora devemos fazer a associação de **SystemRequirement** com **Game**. A associação **belongs_to** que parte de **Game** nós já temos, agora fazemos uma **has_many** que parte de **SystemRequirement** , já que um mesmo requisito pode ser aplicado a mais de um **Game**.

  Para isso vamos começar adicionando o teste de associação **has_many**.

  ```ruby
  #spec/models/system_requirement_spec.rb
  
  it { is_expected.to have_many(:games).dependent(:restrict_with_error)}
  ```

- O **matcher** do **Shoulda matchers** para validar uma associação **has_many** é o **have_many** . É importante notar que estamos validando uma associação **has_many** porém com uma dependência restritiva com **Game**. Isso quer dizer que toda vez que um SystemRequirement for excluído, ele será impedido caso haja algum **Game** <span style="color:red">escaneado</span>.(TODO: acho que esse "escaneado" não foi bem empregado, eu colocaria "caso haja algum game que o possua".) 

- Agora podemos adicionar esta associação no model:

  ```ruby
  #app/models/system_requirement.rb
  class SystemRequirement < ApplicationRecord 
  	has_many :games, dependent: :restrict_with_error
  	
  	validates :name, presence: true, uniqueness: {case_insensitive}
  ```

- Por fim vamos excutar nosso RSpec

  ```shell
  bundle exec rspec
  ```

- Todos os testes rodando

  ```shell
  ......................
  Finished in 0.47112 seconds (files took 0.83658 seconds to load)
  22 examples, 0 failures
  ```

## Model ProductCategory e outras associações 

- Vale ressaltar que esse model é uma entidade associativa, serve apenas para associar dois models **Product** e **Category**. Nós precisamos desse model pelo seguinte motivo: um Produto pode ter diversas **Categorias** e uma **Categoria**, por sua vez, pode pertencer a diversos produtos, fazendo com que os dois tenham um relacionamento N x N. E para suprir esta associação, precisamos colocar uma nova entidade no meio do caminho que quebre esta associação  N x N em duas N x 1.

- Criaremos o model **ProductCategory**:

  ```shell
  rails g model ProductCategory product:references category:references
  
  Running via Spring preloader in process 789536
        invoke  active_record
        create    db/migrate/20201120181437_create_product_categories.rb
        create    app/models/product_category.rb
        invoke    rspec
        create      spec/models/product_category_spec.rb
        invoke      factory_bot
        create        spec/factories/product_categories.rb
  ```

  Ele vai ser apenas um model com duas referencias uma para o **Product** e o outro para **Category**.

- Criaremos então a tabela com o migrate:

  ```shell
  rails db:migrate
  
  == 20201120181437 CreateProductCategories: migrating ==========================
  -- create_table(:product_categories)
     -> 0.1359s
  == 20201120181437 CreateProductCategories: migrated (0.1361s) =================
  ```

- Para testar este model, precisamos apenas verificar se ele possui as associações **belongs_to** Product e Category.

  ```ruby
  RSpec.describe ProductCategory, type: :model do
  	it { is_expected.to belong_to :product }
  	it { is_expected.to belong_to :category }
  end
  ```

- O model foi criado com o comando que executamos, ele já veio com este conteúdo

  ```ruby
  class ProductCategory < ApplicationRecord
  	belongs_to :product
  	belongs_to :category
  end
  ```

  - Também precisamos criar as associações a partir de product e category para **ProductCategory**. Para **Category**, vamos adicionar aos testes:

  ```ruby
  #spec/models/category_spec.rb
  
  it { is_expected.to have_many(:product_categories).dependent(:destroy) }
  it { is_expected.to have_many(:products).through(:product_categories) }
  ```

  Queremos uma associação **has_many** de **Category** para **ProductCategory** com remoção de dependentes, ou seja, quando **Category** for removido, todos os Registros de **ProductCategory** também serão. Além disso, estamos adicionando uma associação has_many direto em **Product** através da associação com **ProductCategory**.

  E para o **Product** podemos adicionar os mesmos testes.

  ```ruby
  # spec/models/product_spec.rb
  it { is_expected.to belongs_to :productable }
  it { is_expected.to have_many(:product_categories).dependent(:destroy) }
  it { is_expected.to have_many(:categories).through(:product_categories) }
  ```

- Podemos executar nossos testes agora, teremos erros por conta das associações adicionadas.

  ```nshell
  bundle exec rspec
  ```

- Então vamos adicionar as novas associações nos models **Category** e **Product**:

  ```ruby
  #app/models/category.rb
  has_many :product_categories, dependent: :destroy
  has_many :products, through: :product_categories
  ```

  ```ruby
  #app/models/product.rb
  has_many :product_categories, dependent: :destroy
  has_many :categories, through: :product_categories
  ```

- Executemos os testes 

  ```shell
  bundle exec rspec
  
  ............................
  Finished in 0.45815 seconds (files took 0.84211 seconds to load)
  28 examples, 0 failures
  ```

- Vamos alterar nosso factory de ProductCategory: 

  ```ruby
  #spec/factories/product_categories.rb
  Factory.define do
      factory :product_category do
          product
          category
      end
  end
  ```

- Para o factory estamos apenas chamando os factories de product e category.

## Melhorando o Model User

- ​	Nosso model **User** ainda não arquivo de testes nem de factory. 

  ```shell
  touch spec/factories/users.rb
  touch spec/models/user_spec.rb
  ```

- Para o **User** vamos nos limitar aos campos que nós criamos - **name e profile**. Os outros campos são gerenciados pelo **Devise Token Auth**, uma ferramenta já testada.

- Vamos adicionar os testes de **validação e enum** que precisamos no model **User**.

  ```ruby
  # spec/model/user_spec.rb
  
  RSpec.describe User, type: :model do
  	it { is_expected.to validate_presence_of(:name) }
      it { is_expected.to validate_presence_of(:profile) }
      it { is_expected.to define_enum_for(:profile).with_values( {admin: 0, client: 1 }) }
  end
  ```

  Como o arquivo foi criado do zero, precisamos adicionar o **require** do **rails_helper**.

- Vamos executá-lo

  ```shell
  bundle exec rspec
  ............................FF.
  ```

  Bem, tivemos erros.

- Vamos adicionar as validações e o **enum** que precisamos em **profile**.

  ```ruby
  # app/models/user.rb
  
  include DeviseTokenAuth::Concerns::User
  
  validates :name, presence: true
  validates :profile, presence: true
  enum profile: {admin: 0, client: 1 }
  ```

- Vamos executar novamente os testes.

  ```shell
  bundle exec rspec
  ...............................
  Finished in 0.54334 seconds (files took 0.88323 seconds to load)
  31 examples, 0 failures
  ```

- Ok, os testes passaram.

- Para **finalizar**, vamos preencher a factory de **User**

  ```ruby
  # spec/factories/users.rb
  
  FactoryBot.define do
  	factory :user do
  		name { Faker::Name.first_name }
  		email { Faker::Internet.email }
  		password { "123456" }
  		password_confirmation { "123456" }
  		profile { :admin }
  	end
  end	
  ```

## Criação do Model de Cupons

- <span style="color:green">Nosso último model nesta parte de Admin.</span>

  ```shell
  rails g model Coupon code status:integer "disount_value:decimal{5,2}" due_date:datetime 
  
  Running via Spring preloader in process 803402
        invoke  active_record
        create    db/migrate/20201120201309_create_coupons.rb
        create    app/models/coupon.rb
        invoke    rspec
        create      spec/models/coupon_spec.rb
        invoke      factory_bot
        create        spec/factories/coupons.rb
  ```

- Vamos executar os migrations 

  ```shell
  rails db:migrate
  
  == 20201120201309 CreateCoupons: migrating ====================================
  -- create_table(:coupons)
     -> 0.1180s
  == 20201120201309 CreateCoupons: migrated (0.1182s) ===========================
  ```

- Como todo bom model que fizemos até aqui, vamos adicionar alguns testes de campos que queremos validar.

  ```ruby
  #spec/models/coupon_spec.rb
  FactoryBot.define do
      factory :coupon do
  		it { is_expected.to validate_presence_of(:code) }
          it { is_expected.to validate_uniqueness_of(:code).case_insensitive }
          it { is_expected.to validate_presence_of(:status) }
  		it { is_expected.to define_enum_for(:status).with_values({active: 1, inactive: 2 })}
  		it { is_expected.to validate_presence_of(:discount_value) }
          it { is_expected.to validate_numericality_of(:discount_value).is_greater_than(0) }
  		it { is_expected.to validate_presence_of(:due_date) }
      end
  end
  ```

- Vamos executar os testes e vê-los quebrando.

  ```shell
  bundle exec rspec
  ```

- Com os testes feitos podemos preencher o model **Coupon**:

  ```ruby
  # app/models/coupon.rb
  class Coupon < ApplicationRecord
      
      validates :code, presence: true, uniqueness: { case_sensitive: false }
      validates :status, presence: true
      validates :discount_value, presence: true, numericality: { greater_than: 0 }
      validates :due_date, presence: true
      enum status: { active: 1, inactive: 2 }
  end
  ```

  ```shell
  bundle exec rspec
  
  ......................................
  
  Finished in 0.60095 seconds (files took 0.83863 seconds to load)
  38 examples, 0 failures
  
  ```

- Vamos modificar o factory bot de Cupons

  ```ruby
  FactoryBot.define do
  	factory :coupon do
     		code { Faker::Commerce.unique.promotion_code(digits: 4) }
     		status { :active }
     		discount_value { 25 }
     		due_date { 3.days.from_now }
     end
  end
  ```

  Em code nós poderimos ter colocado **sequence** , mas temos um problema que o **Faker** para determinados tipos de valores que vamos utilizar, ele possui uma lista limitada de dados. Para este code especificamente, nós utilizamos o **Faker::Commerce.promotion_code** e como colocamos um número considerável de dígitos, dificilmente ele será um fator limitante. Podemos notar que o metodo **unique** também foi empregado para que não tenhamos valores repetidos.

- <span style="color:red">Se há só um erro no model, todos os testes falharam.</span>

## Criando uma validação customizada Parte 1

- ​	<span style="color:red">Estamos com o seguinte problema: nós temos um campo de data de vencimento no model **Coupon** e ela não pode ser uma data do passado, afinal, não faz sentido cadastrarmos um Coupon que já venceu. </span>

  <span style="color: green">Para isso criaremos um validação customizada, também chamado de **custom validator** para que possamos validar o campo do mesmo jeito que validamos para presence, uniqueness e outros. </span>

- Para criarmos o **custom validator** , vamos criar um diretório pra ele. Este diretório está de acordo com as convenções do **Rails Styleguides**.

  ```shell
  mkdir app/validators
  ```

- Vamos chamar nosso validator de **future_date** , vamos criar um arquivo para ele:

  ```ruby
  touch app/validators/future_date_validator.rb
  ```

- Vamos adicionar apenas a declaração de classe para ver como funciona:

  ```ruby
  #app/validators/future_date_validator.rb
  
  class FutureDateValidator < ActiveModel::EachValidator
  end
  ```

  Para que possamos construir o validator por campo como queremos, ele precisa herdar de **ActiveModel::EachValidator**.

  O nome da nossa classe de validação é muito importante pois quando formos adicionar a validação no nosso model,  é uma convenção. 

- Temos de configurar o Rails para que ele possa carregar este diretório, já que ele não carrega por padrão.

  ```ruby
  #config/application.rb
  
  config.api_only = true
  config.autoload_paths += %w["#{config.root}/app/validators/"]
  ```

- Agora vamos criar o arquivo de teste do validator. Primeiro vamos criar o diretório spec/validators.

  ```shell
  mkdir spec/validators
  ```

  Também iremos criar o arquivo de testes.

  ```shell
  touch spec/validators/future_date_validator_spec.rb
  ```

  - Para testar o **FutureDateValidator** vamos utilizar uma técnica onde criaremos uma classe dentro de nosso teste para simular a utilização da validação, faremos os testes em cima dessa classe e iremos	 verificar se recebemos os valores esperados.

  Vamos começar declarando essa classe dentro do arquivo de teste.

  ```ruby
  #spec/validators/future_date_validator_spec.rb
  
  require "rails_helper"
  
  class Validatable 
  	include ActiveModel::Validations
  	attr_accessor :date
  	validates :date, future_date: true
  end
  ```

  Perceba que criado a classe **Validatable** e incluindo o **ActiveModel::Validations**, que é o módulo responsável por fazer as validações dentro de um model Rails. Junto com ele criamos um campo chamado **date** com **attr_accessor**.

  Com o **ActiveModel::Validations** importado, nós conseguimos utilizar o método _validates_ e indicar o nosso campo _date_. Aquele _future_date: true_ que colocamos é bem interessante pois ele interpretando este campo que descobre que o validator **FutureDateValidator** que declaramos lá em cima deve ser utilizado.

- Agora podemos criar o teste do **validator** utilizando a classe **Validatable** que criamos como **subject** do nosso teste.

  ```ruby
  #spec/validators/future_date_validator_spec.rb
  describe FutureDateValidator do
  	subject { Validatable.new }
  end
  ```

  Desta forma o subject do **RSpec** vai ser **a instância da classe** que nós criamos lá na parte de cima.

- Para este validator, nós precisamos contemplar três cenários que penso ser interessante: caso onde <span style="color:red">a data é anterior à data atual,</span> onde a <span style="color:red">data é igual à atual</span> e onde a <span style="color:red">data é posterior a data atual.</span> 

  ```ruby
  # spec/validators/future_date_validator_spec.rb
  
  describe FutureDateValidator do
      subject { Validatable.new }
      context "When date is before current date" do
          before { subject.date = 1.day.ago }
         	
          it "should be invalid" do
              expect(subject.valid?).to be_falsey
          end
          
          it "adds an error on model" do
          	subject.valid?
              expect(subject.errors.keys).to include(:date) # nesse caso temos de adicionar o erro ainda
          end
      end
      ...
  ```

  Estamos considerando a **data anterior** à atual e testando duas coisas neste cenário. Se o **model é inválido** e se o **model possui o campo _date_ nos erros**.
  
- Para o segundo caso, onde a data é igual **à data atual** , teremos o mesmo cenário: o model precisa ser inválido e o model possui campo _date_ de erros.

  ```ruby
  # spec/validators/future_date_validator_spec.rb
  
  describe FutureDateValidator do
      context "When date is before current date" do
      end
      
      context "When date is equal current date" do
      	before { subject.date = Time.zone.now }
          
          it "should be invalid" do
          	expect(subject.valid?).to be_falsey
          end
          it "adds an error on model" do
          	subject.valid?
              expect(subject.errors.keys).to include(:date)
          end
      end
  end
      
  ```

- Agora vamos ao último caso, onde a data é **posterior a atual** , nosso model tem que ser válido.

  ```ruby
  #spec/validators/future_date_validator_spec.rb
  
  describe FutureDateValidator do
      context "When date is before current date" do
      end
      context "When date is equal current date" do
      end
      context "When date is greater than current date" do
     		before { subject.date = Time.zone.now + 1.day }
          
          it "should be valid" do
          	expect(subject.valid?).to be_truthy
          end
      end
  end
  ```

- Vamos executar os testes

  ```shell
  bundle exec rspec
  
  NotImplementedError:
      Subclasses must implement a validate_each(record, attribute, value) method
  ```

  Deu erro não ? ... vamos olhar mais de perto.

- Agora vamos preencher o **FutureDateValidator** para que possa de fato validar o model.

  Toda vez que o validator é chamado como vimos, **herdamos da class ActiveModel::EachValidator**, ele precisa implementar um método chamado _validate_each_.

  ```ruby
  # app/validators/future_date_validator.rb
  class FutureDateValidator < ActiveModel::EachValidator
  	def validate_each(record, attribute, value)
      	
      end
  end
  ```

  Perceba que este método precisa de 3 parâmetros: **o record** que recebe o objeto do model, o **attribute** que é o nome do atributo que está sendo validado e o **value** que é o valor do atributo no model.

  Então, por exemplo a class **Validatable** utilizando este validator no campo _date_ com o <span style="color:red">valor da data de ontem</span>: o **_record_ vai receber o objeto** de **Validatable**, o **_attribute_ vai receber _:date_** e o **_value_ vai receber a data de ontem**.

- E vamos preencher este método para validar de fato:

  ```ruby
  # app/validators/future_date_validator.rb
  
  class FutureDateValidator < ActiveModel::EachValidator
  	def valdiate_each(record, attribute, value)
      	if value.present? && value <= Time.zone.now
              message = options[:message] || :future_date
              record.errors.add(attribute, message)
          end
      end
  end
  ```

  Estamos verificando se foi passado um valor utilizando o parâmetro _value_ e verificando se ele é menor ou igual **a data atual**. Caso seja, um erro é adicionado ao model utilizando o record.

  Repare que nós também temos uma linha onde atribuímos a mensagem de erro que queremos enviar. Esta variável _options_ recebe as opções que enviamos para o validator. No caso, se tivéssemos declarado no model( Acho que seria action ) <span style="color:red">future_date: { message: "Test"}</span> , esta variável **options** teria uma chave **:message** com o valor "Test".

  Para a mensagem de erro, **caso a opção _message_ não seja passada para o validator**, vamos configurar o symbol future_date.

- Nós criamos um symbol future_date porque quando adicionamos um erro a um model contendo um symbol como mensagem, a mensagem em si é buscada no **locales** do **Rails**. Então para isso, vamos adicionar a mensagem de erro para _future_date_.

  Nossa abordagem será criar um arquivo específico para nossas validações customizadas:

  ```ruby
  touch config/locales/pt-BR/custom_validations.yml
  ```

- Com o arquivo criado, podemos adicionar a mensagem de erro de forma que o **ActiveRecord** consiga pegar

  ```yml
  pt-BR:
  	activerecord:
  		errors:
  			messages:
  				future_date: "deve ser uma data futura"
  ```

- Bom então testemos:

  ```shell
  bundle exec rspec
  ...........................................
  Finished in 0.50211 seconds (files took 0.86407 seconds to load)
  43 examples, 0 failures
  ```

## Criando uma validação customizada Parte 2

​	:smile: a mesma coisa que o parte 1

## Utilização do Custom Validator no model Coupon

- Agora iremos implementor o **custom validator** em nosso model Coupo para evitar a inserção de data incorretas.

- Para adicionar este validator no model **Coupon**, vamos escrever alguns testes antes para certificarmos que vai se comportar como esperado. Como sabemos que queremos adicionar esta validação para o campo **due_date**, vamos escrever alguns testes para certificar que ele é incluído como **erro** quando é inválido e que não é incluído quando é válido.

  ```ruby
  # spec/models/coupon_spec.rb
  
  it { is_expected.to validate_presence_of :due_date }
  
  it "can't have past due_date" do
  	subject.due_date = 1.day.ago
      subject.valid?
      expect(subject.errors.keys).to include :due_date
  end
  
  it "is invalid with current due_date" do
  	subject.due_date = Time.zone.now
      subject.valid?
      expect(subject.errors.keys).to include :due_date
  end
  
  it "is valid with future date" do
  	subject.due_date = Time.zone.now + 1.hour
      subject.valid?
      expect(subject.errors.keys).to_not include :due_date
  end
  ```

  Estamos validando se o **due_date** está vindo no erro do model quando ele possui uma data presente ou anterior à data atual. ( E no caso em a data é posterior )

- Vamos executar 

  ```shell
  bundle exec rspec
  ```

  Perceba que, o  último teste já passa, já que ele é válido de qualquer forma.

- Agora vamos executar nossa validação customizada no model **Coupon**.

  ```ruby
  # app/models/coupon.rb
  validates :due_date, presence: true
  ```

  [Para]

  ```ruby
  # app/models/coupon.rb
  validates :due_date, presence: true, future_date: true
  ```

- Vamos executar os testes

  ```shell
  bundle exec rspec
  ..............................................
  Finished in 0.61886 seconds (files took 0.85168 seconds to load)
  46 examples, 0 failures
  ```

- Tudo ocorrendo bem ... 

   

## Instalação do Active Storage

​	Como Active Storage podemos trabalhar com upload de imagens.

- Vamos instalar o active storage, executando o seguinte comando rails

  ```shell
  rails active_storage:install
  
  Copied migration 20201121193425_create_active_storage_tables.active_storage.rb from active_storage
  ```

  Este comando gerou uma migration para criar as tabelas que o ActiveStorage vai utilizar para gerenciar os uploads.

- Vamos executar o migration

  ```shell
  rails db:migrate
  
  == 20201121193425 CreateActiveStorageTables: migrating ========================
  -- create_table(:active_storage_blobs, {})
     -> 0.1899s
  -- create_table(:active_storage_attachments, {})
     -> 0.2145s
  == 20201121193425 CreateActiveStorageTables: migrated (0.4049s) ===============
  ```

- Ele possui um arquivo de configuração que nos informa services de upload que queremos utilizar. Vamos dar uma olhada nele:

  ```ruby
  # config/storage.yml
  test:
  	service: Disk
  	root: <%= Rails.root.join("tmp/storage") %>
  local:
  	service: Disk
  	root: <%= Rails.root.join("storage") %>
  ```

  Olha que interessante, temos aqui duas configurações de upload, uma chamada test e outra local. Em ambas estamos utilizando um service chmado **Disk**. Este service nada mais é do que salvar os arquivos em disco e utiliza o atributo **root** para poder definir a raiz dos uploads. ( Pois poderimos setar para salvar na nuvem ).

- Vamos as configurações do ambiente que informamos qual configuração do **ActiveStorage** queremos utilizar. Vamos abrir o arquivo de configuração de desenvolvimento para dar uma olhada.

  ```ruby
  # config/environments/development.rb
  config.active_storage.service = :local
  ```

  É nesta linha que estamos informando que, para o ambiente de desenvolvimento nós queremos utilizar a configuração com nome **local** do Active Storage. Podemos perfeitamente definir um para cada ambiente, mas por hora **vamos deixar tudo em disco**.

## Adição de imagem para Produto

- Vamos criar incluir um campo para upload de imagens para o **model** Product.

  - Precisamos ter em mente que o nosso model **Product** vai ter um campo chamado _image_ que vai referenciar a imagem de upload do produto. O campo _image_ **não existe no banco mais é um recurso que podemos mapear** no model e informar que ele terá um campo que vai **refenciar um arquivo Active Storage**.

    ```ruby
    # app/models/product.rb
    has_one_attached :image
    ```

    _has_one_attached_ é um recurso do Active Storage que permite mapear um campo no model, para arquivo. Neste caso estamos adicionando um campo image ao model **Product** que haverá um arquivo indexado.

  - Além disso, precisamos validar que um produto sempre terá uma imagem, ou seja, que aquele campo _image_ não pode ser vazio. Vamos começar adicionando um teste para isso.

    ```ruby
    # spec/models/product.rb
    it { is_expected.to validate_presence_of(:image) }
    ```

  - Executaremos os testes

    ```shell
    bundle exec rspec
    
    Expected Product to validate that :image cannot be empty/falsy, but this
           could not be proved.
             After setting :image to ‹nil› -- which was read back as
             ‹#<ActiveStorage::Attached::One:0x000055faf3af3180 @name="image",
             @record=#<Product id: nil, name: "Product 9", description: "Mollitia
             ea et. Deserunt consequatur magnam. Conse...", price: 0.35991e3,
             productable_type: "Game", productable_id: 9, created_at: nil,
             updated_at: nil>>› -- the matcher expected the Product to be invalid,
             but it was valid instead.
         
             As indicated in the message above, :image seems to be changing certain
             values as they are set, and this could have something to do with why
             this test is failing. If you've overridden the writer method for this
             attribute, then you may need to change it to make this test pass, or
             do something else entirely.
    
    ```

  - Para podemos validar image devemos ter um valor presente para ele em model.

    ```ruby
    validates :image, presence: true
    ```

  - Execute novamente

    ```shell
    bundle exec rspec
    
    ...............................................
    
    Finished in 0.54385 seconds (files took 0.84749 seconds to load)
    47 examples, 0 failures
    ```

  - E agora vários teste não rodam pois o model **Product** informa que _image_ não pode ser nula. Isso está ocorrendo porque o **subject** do teste do model **Product**, nós substituimos por um objeto construido a partir da _factory_ dele.

  - Para corrgirmos o problema da _factory_, precisamos mapear o campo _image_ lá também. Como o campo é uma imagem, antes precisamos ter uma imagem de exemplo para usar na _factory_. 

    [Imagem de exemplo](https://drive.google.com/file/d/12fth3MkZMivGexMz8daXqnV5fymP1xuY/view?usp=sharing)

  - Vamos colocar a imagem dentro do arquivo support:

    ```shell
    mkdir spec/support/images
    ```

  - Vamos até o factory de Product para mapear este campo lá também:

    ```ruby
    # spec/factories/products.rb
    
    price { Faker::Commerce.price(range: 100.0..400.0) }
    image{ Rack::Test::UploadedFile.new(Rails.root.join("spec/support/images/product_image.png"))}
    ```

  - Estamos mapeando o campo image Product e para o valor estamos utilizando um recurso do Rack::Test::UploadedFile onde podemos instanciar um objeto passando como parâmetro o caminho do arquivo que queremos anexar neste campo.

  - Se formos executar os testes novamente agora:

    ```shell
    bundle exec rspec
    
    ...............................................
    Finished in 1.03 seconds (files took 0.94282 seconds to load)
    47 examples, 0 failures
    ```

     Fomos capazer de criar um campo de imagem dentro do model Product.

## Configuração dos testes de Request

- Vamos fazer as configurações de teste de **Request** para que possamos entrar na produção dos endpoints da aplicação. Nós vamos criar o módulo de suporte para auxiliar no retorno do HEADERS de autenticação que cada request precisa.

- Vamos começar criando um arquivo de suporte chamado **RequestAPI**:

  ```shell
  # spec/support/request_api.rb
  touch spec/support/request_api.rb
  ```

- Neste arquivo teremos dois métodos: um para fazer o _parse_ das respostas que chegam em **JSON** para hash e outro para retornar o cabeçalho de autenticação de um usuário. Então vamos começar acrescentando o método para o **parse**.

  ```ruby
  # spec/support/request_api.rb
  module RequestAPI 
  	def body_json( symbolize_keys: false )
  		json = JSON.parse(response.body)
  		symbolize_keys ? json.deep_symbolize_keys : json
  	rescue
  		return {}
  	end
  end
  ```

  O response são um objeto do RSpec que possui os dados de uma resposta de um request que foi feito. O que fazemos neste método é pegar o _body_ dessa resposta e converter para **Hash** e, caso dê algum erro, retornará um **hash vazio**.

- Vamos adicionar mais um método que é para retornar o cabeçalho de autenticação de um **request**.

  ```ruby
  # spec/support/request_api.rb
  module RequestAPI
  	def body_json( symbolize_keys: false)
  	end
      def auth_header(user = nil, merge_with: {} )
          user ||= create(:user)
          auth = user.create_new_auth_token # method devise token that to obtain credentials user. 
          header = auth.merge({'Content-Type' => 'application/json', "Accept" => 'application/json'}) 
          header.merge merge_with
      end
  end
  ```

  <blockquote>
      a ||= b is a conditional assignment operator. It means if a is undefined or falsey, then evaluate b and set a to the result. Equivalently, if a is defined and evaluates to truthy, then b is not evaluated, and no assignment takes place.
  </blockquote>

  Este método auth_header tem um parâmetro _user_ e um outro merge_with. O parametro **user** é para que ele possa receber um usuário específico para o retorno do cabeçalho, mas caso não receba, ele cria o usuário e retorna o cabeçalho. O **merge_with** é um parâmetro que esperamos receber **cabeçalhos adicionais** que queiramos enviar junto com o cabeçalho de autenticação.

- Com os métodos criados adicionaremos em uma única linha este módulo  no **RSpec**. 

  ```ruby
  # spec/support/request_api.rb
  module RequestAPI
  	...
  end
  RSpec.configure do |config|
  	config.include RequestAPI, type: :request
  end
  ```

- Estamos incluindo o modulo apenas para testes de request.

- Para testar nossa configuração vamos criar um teste para a rota /home, que nós já temos criado. Criaremos primeiro o diretório e o arquivo para o teste do tipo desta rota. ( <span style="color:red">Depois não esqueça de apagá-lo.</span>)

  ```shell
  mkdir -p spec/requests/admin/v1
  ```

  ```shell
  touch spec/requests/admin/v1/home_spec.rb
  ```

- Para os testes vamos começar criando a declaração no arquivo:

  ```ruby
  require "rails_helper"
  
  describe "Home", type: :request do
  end
  ```

  Agora iremos criar um usuário que utilizaremos para autenticar o endpoint.

  ```ruby
  require "rails_helper"
  
  describe "Home", type: :request do
  	let(:user){create(:user)}
  end
  ```

  Criando um usuário utilizando o método **create** do factoryBot para chamar a factory user. O let que circunda esse **create**, irá gerar um método interno a cada teste que poderemos acessar o usuário criado com a factory.

- Neste arquivo iremos testar duar coisas: **verificar se o body dará uma resposta** e o **status da requisição**.Então vamos para o teste do body.

  ```ruby
  # spec/requests/admin/v1/home_spec.rb
  require "rails_helper"
  
  describe "Home", type: :request do
  	let(:user){create(:user)}
      it "tests home" do 
          get '/admin/v1/home', headers: auth_header(user)
          expect(body_json).to eq( { 'message' => 'Uhu!' } )
      end
  end
  ```

  Estamos enviando uma requisição GET para /admin/v1/home, utilizando o método **auth_user** com o usuário que criamos no **let** e utilizando método **body_json** para retornar a **body** formatada da resposta e **validando** se é um que esperamos.

  E o próximo passo agora é testarmos o status da requisição. Então vamos testar no nosso código.

  ```ruby
  # spec/requests/admin/v1/home_spec.rb
  
  require 'rails_helper'
  
  describe "Home", type: :request do
      let(:user) { create(:user) }
      it "should return the response body" do
          get '/admin/v1/home', headers: auth_header(user)
          expect(body_json).to eq( {'message' => 'Uhu!' })
      end
  
      it "should to have status" do
      	get '/admin/v1/home', headers: auth_header(user)
          expect(response).to have_http_status(:ok) 
      end
  end
  ```

  Estamos fazendo a mesma requisição **GET** enviando a mesmo cabeçalho, porém dessa vez estamos verificando se o objeto **response** possui **status** :ok através do matcher **have_http_status**.

- Vamos testar os testes

  ```shell
  bundle exec rspec
  
  .................................................
  Finished in 0.68701 seconds (files took 0.87889 seconds to load)
  49 examples, 0 failures
  ```

## JBuilder e a primeira rota de Categoria

- Vamos instalar o JBuilder para renderizar JSON.

- Adicione a gem jbuilder no Gemfile

  ```ruby
  #Rendering 
  gem 'jbuilder', '~> 2.10.1'
  ```

- Realize a instalação 

  ```shell
  bundle install
  ```

- Agora podemos criar o **controller** de Categorias, onde iremos começar a trabahar em nosso endpoint.

  ```shell
  rails g controller admin/v1/categories
  
  Running via Spring preloader in process 972614
        create  app/controllers/admin/vi/categories_controller.rb
         route  namespace :admin do
    namespace :vi do
      get 'categories/index'
    end
  end
        invoke  rspec
        create    spec/requests/admin/vi/categories_request_spec.rb
  
  ```

  O comando gerou tanto o controller quanto o arquivo para testar os requests.

- Quando instalamos o controller por generate por padrão ele vem herdado por **ApplicationController**. Precisamos alterar isso, já que temos um controller **raiz** para nosso escopo **admin/v1**.

  ```ruby
  class CategoriesController < ApiController
  end
  ```

- A primeira rota que iremos criar será **GET /categories** para carregar as categorias. Primeiro precisamos criar o usuário que irá ser autenticado.

  ```ruby
  RSpec.describe " Admin V1 Categories", type: :request do
  	let(:user){ create(:user) }
  end
  ```

  Criaremos um contexto para ela e junto com isso vamos  criar os dados que queremos que sejam retornados quando chamarmos esse endpoint.

  ```ruby
  let(:user) { create(:user) }
  context "GET /categories" do
      let(:url){ "/admin/v1/categories" }
      let!(:categories) { create_list(:category, 5) }
  end
  ```

  Estamos definindo mais duas variáveis com o **let** e para categories utilizamos **let!**, com **bang**. Precisamos fazer isso pois **let** opera como **lazy loading**, ou seja, ele só carrega o conteúdo quando chamamos o método pela primeira vez. Precisamos utilizar o **let!** para _categories_ porque nós precisamos que seja executado antes de fazermos o request, caso contrário não teremos dados retornados pelo **endpoint**.

- Para **GET /categories**, existem dois cenários que são importantes testar: o primeiro é que todas as categorias foram retornadas e o segundo é se o status da resposta é 200. Então vamos começar pela verificação da categorias.

  ```ruby
  # spec/requests/admin/v1/categories_spec.rb
  let(:user){ create(:user) }
  context "GET /categories" do
      let(:url){ "/admin/v1/categories" }
      let!(:categories){ create_list(:category, 5) }
      it "return all the categories" do
      	get url, headers: auth_header(user) # lembrar de fazer um put body_json['categories']
          expect(body_json['categories']).to contain_exactly *categories.as_json(only: %i(id name) )
      end
  end
  ```

  Estamos chamando nosso endpoint **url** e passando o cabeçalho com o que retornado do **auth_header** que criamos     no modulo support **RequestAPI**. Utilizamos **body_json**, que também criamos no **RequestAPI**, e buscamos pelo chave **categories** e verificamos se ela contem os mesmos elementos que criamos pelo **let!** . Neste categories usamos o **as_json** que forma os valores num hash.

- Vamos verificar o status de resposta:

  ```ruby
  # spec/requests/admin/v1/categories_spec.rb
  let(:user){ create(:user) }
  context "GET /categories" do
      let(:url){ "/admin/v1/categories" }
      let!(:categories){ create_list(:category, 5) }
      it "return all the categories" do
      	get url, headers: auth_header(user) # lembrar de fazer um put body_json['categories']
          expect(body_json['categories']).to contain_exactly *categories.as_json(only: %i(id name) )
      end
  	it "return status 200" do
      	get url, headers: auth_header(user)
          expect(response).to have_http_status(:ok)
      end
  end
  
  ```

  Neste teste estamos utilizando o matcher **have_http_status** com o response para ver se o status retornado foi o **ok**. Estes status ok é o que **symbol** que o Rails utiliza para referenciar o status 200.

- Vamos executar os testes

  ```shell
  bundle exec rspec
  ```

- Vamos criar nossa rota de categoria

  ```ruby
  # config/routes.rb
  
  namespace :admin do
      namespace :v1 do
     		resources :categories
      end
  end
  
  ```

  Com isso estamos criando as rotas de /categories que precisamos.

- Apenas para verificarmos vamos executar o rspec mais uma vez.

  ```shell
  bundle exec rspec
  ```

- Vamos começar a preencher a action index do nosso controller categories. Mas antes, faremos um alteração na definição da classe. A nossa action index será bem simples, somente carregará todas as categorias.

  ```ruby
  # app/controllers/admin/v1/categories_controller.rb
  module Admin:V1
      class CategoriesController < ApplicationController
     		def index 
              @categories = Category.all
          end
      end
  end
  ```

- Com as categorias já carregadas, agora vamos entender como ele será **serializado** para **JSON**. Nós já sabemos que, quando trabalhamos com renderização no Rails, graças à convenção sob configuração, ele vai até o diretório **app/views** e busca por um arquivo com o mesmo nome da **action** seguindo o mesmo padrão de diretórios os controller. Como nossa **action** não tem **render**, ele irá buscar por um **view** em **app/views/admin/v1/categories/index**. O que vamos fazer aqui é criar esta view, porém em vez de ser HTML, ele será JSON.

  ```shell
  mkdir -p app/views/admin/v1/categories
  ```

  ```shell
  touch app/views/admin/v1/categories/index.json.jbuilder
  ```

- Não precisamos fazer nenhum tipo de configuração porque os arquivos de view já seguem um padrão de extensão que contém a ferramenta de **renderização**, no caso o **.jbuilder** e o formato de renderização, no caso **.json**. Ele funciona no mesmo raciocínio de quando é renderizado uma página HTML onde a extensão é **.html.erb**.

  Este arquivo do jbuilder é exatamente uma "view" **JSON**. Ou seja, a variável @categories que definimos na **action index ** será enviada para esta view.

  [ Repositorio JBuilder ](https://github.com/rails/jbuilder)

- Vamos preencher a view para que o JSON seja construido.

  ```ruby
  # app/views/admin/v1/categories/index.json.jbuilder
  
  json.categories do
      json.array! @categories, :id, :name
  end
  ```

  Estamos utilizando o **json.categories** para criar uma chave de nome **categories** no JSON e o **json.array!**, que pega um objeto e monta uma array extraindo os atributos que passamos como parâmetro.

- Agora vamos executar os testes:

  ```shell
  bundle exec rspec
  ...................................................
  
  Finished in 0.79033 seconds (files took 0.87106 seconds to load)
  51 examples, 0 failures
  ```

## Cadastro de Categoria e Renderização de Erro 

- Vamos criar os testes de rota do cadastro de categoria. Antes de criarmos os casos de testes especificamente, temos de pensar no que é importante testar numa rota como essa:

  - Quando os parâmetros são válidos.
  - Quando os parâmetros não são válidos.

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  context "GET /categories" do
  end
  
  context "POST /categories" do
      let(:url){ "/admin/v1/categories"}
      context "with valid params" do
  	end
  	context "with invalid params" do
  	end
  end
  ```

  Adicionamos dois contexts internos, um para parâmetros válidos e outro para inválidos. E junto com isso, criamos a variável **url** com a URL que utilizaremos internamente.

- Para os parâmetros válidos vamos testar 3 blocos: que **uma nova Categoria foi adicionada**, que **ela é igual a última categoria criada no banco** e **que seu status de resposta é 200**.

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  
  context "with valid params" do
      let(:category_params){ {category:  attributes_for(:category) }.to_json }
      it "should create a new category" do
      	expect do
              post url, headers: auth_header(:user), params: category_params
          end.to change(Category,:count).by(1)
      end
  end
  ```

  [ Doc rspec: analisar change ](https://relishapp.com/rspec/rspec-expectations/v/2-0/docs/matchers/expect-change)

  Primeiro criamos uma variável **category_params** e chamamos o método **attributes_for** do **Factory_Bot**. Este método retorna um hash com os dados de uma **factory**, que armazenamos numa chave **category**. Chamamos o **to_json** para que ele transforme este **hash** em JSON de fato para enviarmos no **request**.

  Logo depois criamos os dados de autenticação, que irá fazer o **POST** na URL, enviando os **headers** de autenticação e como  parâmetro a variável **category_params** que criamos. Deste post, ele verifica que **Category** aumentou a quantidade em **1**. Desse jeito conseguimos verificar se ela foi realmente **persistida** no banco.

  Próximo:
  
  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  
  context "with valid params" do
      let(:category_params){ {category: attributes_for(:category) }.to_json }
      it "should create a new category" do
      end
      it "returns last added Category" do
      	post url, headers: auth_header(use), params: category_params
          expcted_category = Category.last.as_json( only: %i(id name) )
          expect(body_json['category']).to eq expected_category
      end
end
  ```
  
  Estamos fazendo o mesmo **POST**, porém agora estamos pegando a última categoria adicionada no banco, transformando ela em **hash** e verificando se ela é igual à que foi retornada pela API.

- E o último, verificar o **status** que a API retornou:

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  
  context "with valid params" do
      let(:category_params) { {category: attributes_for(:category)}.to_json }}
      it "shoud create a new category" do
      end
  	it "returns last added Category" do
      end
  	it "returns success status" do
          post url, headers: auth_header( user ), params: category_params
          expect(response).to have_http_status(:ok)
      end
  end
  ```

  Mesmo **POST**, agora utilizando o **matcher** _have_http_status para validar o status da resposta se é **:ok**.

- É importante termos em mente como retornaremos a nossa estrutura de erros. Quando definimos os contratos da **API**, também definimos isso e chegamos à seguinte estrutura.

  ```
  "errors": {
  	"fields":{
  		"<campo_de_erro_especificacao>":["validation erro:1", "erro:2", ... ]
  	},
  	"message": "general error"
  }
  ```

  Teremos uma chave principal que chamaremos de errors e para os erros de validação teremos um campo **fields** onde colocaremos erros de validação para cada campo e caso seja um erro de validação de entidade, ele virá na chave base.

  E entre os errors também teremos uma chave **message** para colocarmos algum erro geral que não esteja diretamente lidado à validação de dados.

- Com a estrutura de erros definido podemos ir para os parâmetros inválidos. Podemos testar 3 casos parecidos: que nenhuma Categoria foi adicionada, que renderiza uma mensagem de erro e que o **status** da resposta é 422 ( :unprocessble_entity do Rails )

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  
  context "with valid params" do
      let(:category_params) { {category: attributes_for(:category)}.to_json }}
      it "shoud create a new category" do
      end
  	it "returns last added Category" do
      end
  	it "returns success status" do
      end
  end
  
  context "with invalid params" do
  	let(:category_invalid_params) do
          {category: attributes_for(:category, name: nil) }.to_json
      end
      it "does not add Category" do
          expect do
              post url, headers: auth_header(user), params: category_invalid_params
          end.to_not change(Category, :count)
      end
  end
  ```

    Criamos uma variável **category_invalid_params** com a factory de categoria, porém dessa vez estamos colocando o nome como vazio, pois já sabemos que **ele é um atributo obrigatório.**

  O primeiro teste então, fazemos **POST** com estes parâmetros inválidos que estamos nos certificando que **Category** não possui nenhuma alteração na contagem.

  O segundo teste vai ser para certificar o retorno do erro num formato que vamos definir:

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  context "with valid params" do
  end
  context "with invalid params" do
      let(:category_invalid_params) do
          {category: attributes_for(:category, name: nil) }.to_json
      end
      it "does not add Category" do
      end
      it "returns error message" do
      	post url, headers: auth_header(user), params: category_invalid_params
          expect(body_json['errors']['fields']).to have_key('name')
      end
  end
  ```

  Fazendo o mesmo **POST** com os parâmetros inválidos, estamos verificando se a resposta possui a chave **name** dentro da estrutura de erro. Estamos testando exatamente com a estrutura que vimos acima.

  E por último, vamos testar se ele está retornando o status 422 quando ocorre o erro na validação.

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  context "with valid params" do
  end
  context "with invalid params" do
      let(:category_invalid_params) do
          {category: attributes_for(:category, name: nil) }.to_json
      end
      it "does not add Category" do
      end
      it "returns error message" do
      end
      it "returns unprocessable_entity status" do
      	post url, headers: auth_header(user), params: category_invalid_params
          expect(response).to have_http_status(:unprocessable_entity)
      end
  end
  ```

  Mesmo **POST**, agora utilizando a **matcher** _have_http_status_ para validar o status da resposta como **unprocessable_entity**(422)

- Com os testes prontos, podemos executar o **RSpec**.

  ```shell
  bundle exec rspec
  ```

- Vamos criar a action dentro do controller de categorias

  ```ruby
  # app/controllers/admin/v1/categories_controller.rb
  class CategoryController < ApiController 
      def index
      end
      def create
      end
  end
  ```

  E dentro dela, já podemos instanciar uma variável **@category** e atribuir os valores dos parâmetros.

  ```ruby
  # app/controllers/admin/v1/categories_controller.rb
  class CategoryController < ApiController 
      def index
      end
      def create
     		@category = Category.new
          @category.attributes = category_params
      end
  end
  ```

  Este método **category_params** é o que utilizamos para fazer o **permit**. Então vamos criá-lo encapsulando como **private**.

  ```ruby
  # app/controllers/admin/v1/categories_controller.rb
  class CategoryController < ApiController 
      def index
      end
      def create
     		@category = Category.new
          @category.attributes = category_params
      end
      
      private 
      
      def category_params
          return {} unless params.has_key?(:category)
      	params.require(:category).permit(:id, :name)
      end
  end
  ```

  Criamos o método e estamos verificando se params possui a chave :category senão terá um retorno vazio.

  E como a última ação da **action create**, vamos salvar e, em caso de erro, retornará uma mensagem de erro na estrutura que definimos.

  ```ruby
  # app/controllers/admin/v1/categories_controller.rb
      def create
     		@category = Category.new
          @category.attributes = category_params
          @category.save!
          render :show
      rescue
          render json: {errors: {fields: @category.errors.messages } }, status: :unprocessable_entity
      end
  ```

  Estamos salvando o model **@category** com **save!**, que retorna uma exceção caso ocorra um erro e renderizando a **view show**. Caso **@category** tenha algum erro, ele cairá no **rescue** e vai renderizar um **JSON** como erro.

- Com o create feito, precisamos criar a view show do **jbuilder** para renderizar a categoria no render :show que colocamos:

  ```shell
  touch app/views/admin/v1/categories/show.json.jbuilder
  ```

  E na view, vamos renderizar em JSON o model **@category** que foi salvo:

  ```ruby
  # app/views/admin/v1/categories/show.json.jbuilder
  
  json.category do
      json.(@category, :id, :name )
  end
  ```

  Estamos criando uma chave category e dentro dela os atributos **id e name** do model **@category** que foi salvo, exatamente como testamos. Lembrando que essa **view** só será renderizada se não houver nenhum erro.

- Com a view criada podemos realizar os testes:

  ```shell
  bundle exec rspec
  ```

  E todos os nossos testes passaram. Porém ainda podemos fazer algumas melhorias.

- Nós já definimos  como será a resposta de erro da nossa API, mas podemos melhorar criando um método específico para renderizarmos os erros. Para isso, vamos criar um método no **ApiController** do Admin, para podermos compartilhar com todos os controllers que herdarem dele:

  ```ruby
  # app/controllers/admin/v1/api_controller.rb
  include Authenticable
  
  def render_error(message: nil, fields: nil, status: :unprocessable_entity )
  	errors = {}
      errors['fields'] = fields if fields.present?
      errors['message'] = message if message.present?
      render json: { errors: errors }, status: status
  end
  ```

  Este método tem 3 parâmetros-chave opcionais: um chamado **message**, para adicionar uma mensagem geral, outro chamado **fields** para os campos inválidos e o terceiro para o status que queremos retornar com este erro, que por padrão **422** (:unprocessable_entity). E ele adiciona ambos **message** e **fields** dentro de uma chave raiz **errors** e renderiza isso como JSON. Exatamente como precisamos.

  Agora podemos pegar este método e usar no **create**.

  ```ruby
  render json: {errors: { fields: @category.errors.messages }}, status: :unprocessable_entity
  ```

  [Por]

  ```ruby
  render_error( fields: @category.errors.messages )
  ```

- Executaremos os testes novamente 

  ```shell
  bundle exec rspec
  ```

- Para melhorar um pouco nosso controller podemos fazer o seguinte, vamos criar um método para **salvar a categoria** e **limpar a action create**.

  ```ruby
  # app/controllers/admin/v1/categories_controller.rb
  
  def category_params 
  end
  def save_category!
  	@category.save!
      render :show
  rescue
      render_error(fields: @category.errors.messages)
  end
  ```

   Nós colocamos neste save_category! a mesma coisa que estava no create. Agora e substituir tudo aquilo que adicionamos neste método pela chamada a ele.

  ```ruby
  # app/controllers/admin/v1/categories_controller.rb
  
  def create
  	save_category!
  end
  ```

- Vamos executar os testes

  ```shell
  bundle exec rspec
  .........................................................
  
  Finished in 2.14 seconds (files took 8.41 seconds to load)
  57 examples, 0 failures
  ```

## Atualizando uma Categoria

​	Vamos criar uma rota para atualizar uma categoria.

- <span style="color:green">Vamos começar realizando a implementação dos teste e logo depois a action</span> . Acrescente um novo context no arquivo de testes para o **PATCH /categories/:id**. 

  ```ruby
  #spec/requests/admin/v1/categories_request_spec.rb
  
  context "POST /categories" do
  	...
  end
  conntext "PATCH /categories/:id" do
  	let!(:category){ create(:category) }
      let(:url) {"/admin/v1/categories/#{category.id}"}
  end
  ```

  Criamos o context para o teste e junto com isso já estamos criando uma categoria, para que possamos atualizá-la nos testes e uma variável para a **url**, com o ID dessa categoria que estamos criando.

- Os testes para atualizar não serão muito diferentes do que fizemos para criar. Precisamos tanto considerar a chamada com parâmetros válidos e inválidos. Acrescente mais dois context:

  ```ruby
  #spec/requests/admin/v1/categories_request_spec.rb
  let!(:category){ create(:category) }
  let(:url) {"/admin/v1/categories/#{categories.id}"}
  context "with valid params"
  	let(:new_name){"My new Category"}
  	let(:category_params) {{category: {name: new_name} }.to_json}
  end
  context "with invalid params"
  	let(:category_invalid_params) do
      	{category: attributes_for(:category, name: nil) }.to_json
      end
  end
  ```

   Para os dois context estamos criando as variáveis que utiizaremos internamente. No de parâmetros válidos, uma variável como o novo nome e os parâmetros incluindo o novo nome e no de parâmetros inválidos uma variável com os parâmetros inválidos com nome em branco.

- **Para o contexto de parâmetros válidos**, vamos testar **se a categoria teve o nome atualizado**, **se a categoria atualizada foi retornada** e **se os status foi de sucesso.**

  ```ruby
  context "PATCH /categories/:id" do
  	let!(:category){ create(:category) }
      let(:url) {"/admin/v1/categories/#{categories.id}"}
      context "with valid params" do
  		let(:new_name){ "Teste"}
  		let(:category_params){{category: {name: new_name} }.to_json }
  		it "updates Category" do
  			patch url, headers: auth_header(user), params: category_params
              category.reload
              expect(category.name).to eq new_name
  		end
  	end
  end
  ```

  Chamamos a rota **PATCH**, passando os headers de autenticação e os parâmetros. Após isso, faremos um reload com a category que criamos para que ela seja **recarregada do banco** e verificamos se o nome dela é igual ao novo nome que definimos nos parâmetros.

  O teste seguinte é verificar se a categoria retornada é igual à categoria atualizada.

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  context "PATCH /categories/:id" do
      let!(:category){ create(:category) }
      let(:url) {"/admin/v1/categories/#{categories.id}"}
  	context "with valid params" do
  		let(:new_name){ "Teste"}
  		let(:category_params){{category: {name: new_name} }.to_json }
  		it "updates Category" do
  			patch url, headers: auth_header(user), params: category_params
              category.reload
              expect(category.name).to eq new_name
  		end
          it "returns updated Category" do
              patch url, headers: auth_header(user), params: category_params
              category.reload
              expected_category = category.as_json(only: %i(id name))
              expect(body_json['category']).to eq expected_category
          end
  	end
  end
  ```

  Neste teste estamos fazendo novamente um **reload** de **category**  e transformando num hash. Em seguida é verificado se o que foi retornado no corpo da resposta é igual à category atualizada.

  E por último verificamos o estado da resposta:

  ```ruby
  # spec/requests/admin/v1/categories_requests_params.rb
  context "PATCH /categories/:id" do
      let!(:category){ create(:category) }
      let(:url) {"/admin/v1/categories/#{categories.id}"}
  	context "with valid params" do
  		let(:new_name){ "Teste"}
  		let(:category_params){{category: {name: new_name} }.to_json }
  		it "updates Category" do
  			patch url, headers: auth_header(user), params: category_params
              category.reload
              expect(category.name).to eq new_name
  		end
          it "returns updated Category" do
              patch url, headers: auth_header(user), params: category_params
              category.reload
              expected_category = category.as_json(only: %i(id name))
              expect(body_json['category']).to eq expected_category
          end
          it "returns success status" do
          	patch url, headers: auth_header(user), params: category_params
              expect(response).to have_http_status(:ok)
          end
  	end
  end
  ```

- Para os testes de parametros inválidos, vamos validar se a categoria permanece com o nome antigo, se o erro contém o campo **name** como chave e se os **status** é 422 (**:unprocessable_entity**).

- Para verificar se o nome permanede antigo, vamos:

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  context "PATCH /categories/:id" do
      let!(:category){ create(:category) }
      let(:url) {"/admin/v1/categories/#{categories.id}"}
  	context "with valid params" do
  		let(:new_name){ "Teste"}
  		let(:category_params){{category: {name: new_name} }.to_json }
  		it "updates Category" do
  			patch url, headers: auth_header(user), params: category_params
              category.reload
              expect(category.name).to eq new_name
  		end
          it "returns updated Category" do
              patch url, headers: auth_header(user), params: category_params
              category.reload
              expected_category = category.as_json(only: %i(id name))
              expect(body_json['category']).to eq expected_category
          end
          it "returns success status" do
          	patch url, headers: auth_header(user), params: category_params
              expect(response).to have_http_status(:ok)
          end
  	end
      context "with invalid params" do
      	let(:category_invalid_params) do
              {category: attributes_for(:category, name: nil )}.to_json
          end
          it "does not update Category" do
              old_name = category.name
              patch url,header: auth_header(user),params: category_invalid_params
              category.reload
              expect(category.name).to eq old_name 
          end
      end
  end
  ```

  Antes da requisição, guardamos o nome antigo numa variável **old_name**, então enviamos o request e fazendo um reload de **category** para forçar recarregar a partir do banco. Em seguida, validamos que a categoria recarregada ainda permanece com o nome antigo.

  Para verificarmos se retorna uma mensagem de erro, não faremos nada muito diferente do **POST**.

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  context "PATCH /categories/:id" do
      let!(:category){ create(:category) }
      let(:url) {"/admin/v1/categories/#{categories.id}"}
  	context "with valid params" do
  		let(:new_name){ "Teste"}
  		let(:category_params){{category: {name: new_name} }.to_json }
  		it "updates Category" do
  			patch url, headers: auth_header(user), params: category_params
              category.reload
              expect(category.name).to eq new_name
  		end
          it "returns updated Category" do
              patch url, headers: auth_header(user), params: category_params
              category.reload
              expected_category = category.as_json(only: %i(id name))
              expect(body_json['category']).to eq expected_category
          end
          it "returns success status" do
          	patch url, headers: auth_header(user), params: category_params
              expect(response).to have_http_status(:ok)
          end
  	end
      context "with invalid params" do
      	let(:category_invalid_params) do
              {category: attributes_for(:category, name: nil )}.to_json
          end
          it "does not update Category" do
              old_name = category.name
              patch url,header: auth_header(user),params: category_invalid_params
              category.reload
              expect(category.name).to eq old_name 
          end
          it "returns error message" do
          	patch url, header: auth_header(user), params: category_invalid_params
              expect(body_json['errors']['fields']).to have_key('name')
          end
      end
  end
  ```

   Enviamos o **PATCH** e então verificamos se a chave **name** está presente na nossa estrutura de retorno de erro. 

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  context "PATCH /categories/:id" do
      let!(:category){ create(:category) }
      let(:url) {"/admin/v1/categories/#{categories.id}"}
  	context "with valid params" do
  		let(:new_name){ "Teste"}
  		let(:category_params){{category: {name: new_name} }.to_json }
  		it "updates Category" do
  			patch url, headers: auth_header(user), params: category_params
              category.reload
              expect(category.name).to eq new_name
  		end
          it "returns updated Category" do
              patch url, headers: auth_header(user), params: category_params
              category.reload
              expected_category = category.as_json(only: %i(id name))
              expect(body_json['category']).to eq expected_category
          end
          it "returns success status" do
          	patch url, headers: auth_header(user), params: category_params
              expect(response).to have_http_status(:ok)
          end
  	end
      context "with invalid params" do
      	let(:category_invalid_params) do
              {category: attributes_for(:category, name: nil )}.to_json
          end
          it "does not update Category" do
              old_name = category.name
              patch url,header: auth_header(user),params: category_invalid_params
              category.reload
              expect(category.name).to eq old_name 
          end
          it "return error message" do
          	patch url, headers: auth_header(user), params: category_invalid_params
              expect(body_json['errors']['fields']).to have_key('name')
          end
          it 'returns unprocessable_entity status' do
          	patch url, headers: auth_header(user), params: category_invalid_params
              expect(response).to have_http_status(:unprocessable_entity)
          end
      end
  end
  ```

  Fazemos o mesmo usando o matcher **have_http_status**.

- Com os testes prontos, vamos executar o **RSpec**

  ```shell
  bundle exec rspec
  ```

- Para a rota **PATCH /categories/:id** será utilizado a **active update**. Então camos criá-la em nosso controller. 

  ```ruby
  # app/controllers/admin/v1/categories_controller.rb
  def create
  end
  def update
  end
  ```

- Para esta action, precisamos basicamente carregar  a categoria a partir do **ID** enviando na **URL**, atualizando os parâmentros e salvar.

  ```ruby
  # app/controllers/admin/v1/categories_controller.rb
  def create
  end
  def update
      @category = Category.find(params[:id])
      @category.attributes = category_params
      save_category!
  end	
  ```

  Estamos carregando a categoria com o método **find** e fazendo um reuso de código para atribuir os valores utilizando o método **category_params** e salvando com o **salve_category!**.

- Rodamos os testes 

  ```shell
  bundle exec rspec
  ...............................................................
  
  Finished in 1.22 seconds (files took 0.90191 seconds to load)
  63 examples, 0 failures
  ```

## Removendo uma categoria

​	Vamos criar um context para uma rota de remoção.

```ruby
# spec/requests/admin/v1/categories_request_spec.rb

context "PATCH /categories/:id"	do
end

context "DELETE /categories/:id" do
	let!(:category) { create(:category) }
    let(:url){ "/admin/v1/categories/#{category.id}" }
end
```

Criamos o context para o teste e junto com isso estamos criando uma categoria, para que possamos atualizá-la nos testes, uma variável para a **url**, com o ID dessa categoria que estamos criando.

- Para os testes de remoção, precisamos verificar alguns pontos: **se houve redução da quantidade de categorias**, **se o status foi retornado**, **se body retornado foi vazio**, se **todas as associações ProductCategories associadas a ela foram removidas** e **se todas não associadas permanecem intactas.**

  ```ruby
  context "DELETE /categories/:id" do
  	let!(:category){ create(:category) }
  	let(:url){ "/admin/v1/categories/#{category.id}"}
      it 'removes Category' do
      	expect do 
          	delete url, headers: auth_header(user)
          end.to change(Category, :count).by(-1)
      end
  end
  ```

  Validaremos se a quantidade de Categorias teve uma alteração de contagem em -1, ou seja, se há menos uma categoria após a chamada da rota.

  A próxima é a verificação do status:

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  context "DELETE /categories/:id" do
  	let!(:category){ create(:category) }
  	let(:url){ "/admin/v1/categories/#{category.id}"}
      it 'removes Category' do
      	expect do 
          	delete url, headers: auth_header(user)
          end.to change(Category, :count).by(-1)
      end
      it 'returns success status' do
      	delete url, headers: auth_header(user)
          expect(response).to have_http_status(:no_content)
      end
  end
  ```

  Da mesma forma como estávamos verificando as outras respostas, neste estamos verificando se retornou o status 204(**:no_content**)

  Agora verificaremos se a resposta realmente está sem conteúdo no **body**:

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  context "DELETE /categories/:id" do
  	let!(:category){ create(:category) }
  	let(:url){ "/admin/v1/categories/#{category.id}"}
      it 'removes Category' do
      	expect do 
          	delete url, headers: auth_header(user)
          end.to change(Category, :count).by(-1)
      end
      it 'returns success status' do
      	delete url, headers: auth_header(user)
          expect(response).to have_http_status(:no_content)
      end
      it 'does not return any body content' do
      	delete url, headers: auth_header(user)
          expect(body_json).to_not be_present
      end
  end
  ```

  Agora precisamos verificar se as entidades de **ProductCategory** foram removidas

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  
  context "DELETE /categories/:id" do
  	let!(:category){ create(:category) }
  	let(:url){ "/admin/v1/categories/#{category.id}"}
  	it "removes Category" do
  		expect do 
  			delete url, headers: auth_header(user)
  		end.to change(Category, :count).by(-1)
      end
      it 'returns success status' do
      	delete url, headers: auth_header(user)
          expect(response).to have_http_status(:no_content)
      end
      it 'does not return any body content' do
      	delete url, headers: auth_header(user)
          expect(body_json).to_not be_present
      end
      it 'removes all associated product categories' do
      	product_categories = create_list(:product_category, 3, category: category)
          delete url, headers: auth_header(user)
          expected_product_categories = ProductCategory.where(id: product_categories.map(&:id))
          expect(expected_product_categories.count).to eq 0 
      end
  end
  ```

  Para verificar vamos criar uma lista **ProductCategory** com o método **create_list** do **FactoryBot** <span style="color:red">atribuindo como categoria a que criamos lá no começo</span> do teste de **DELETE**. Após isso, chamamos a rota **DELETE** com o cabeçalho de autenticação, carregamos os **ProductCategory** que tenham o mesmo **id** dos que criamos e verificamos se a contagem deles é **igual a 0**.

  E por último temos de validar que as **ProductCategory** que não são associadas à category estarão intactas após o delete:	

    

  ```ruby
  # spec/requests/admin/v1/categories_request_spec.rb
  
  context "DELETE /categories/:id" do
  	let!(:category){ create(:category) }
  	let(:url){ "/admin/v1/categories/#{category.id}"}
  	it "removes Category" do
  		expect do 
  			delete url, headers: auth_header(user)
  		end.to change(Category, :count).by(-1)
      end
      it 'returns success status' do
      	delete url, headers: auth_header(user)
          expect(response).to have_http_status(:no_content)
      end
      it 'does not return any body content' do
      	delete url, headers: auth_header(user)
          expect(body_json).to_not be_present
      end
      it 'removes all associated product categories' do
      	product_categories = create_list(:product_category, 3, category: category)
          delete url, headers: auth_header(user)
          expected_product_categories = ProductCategory.where(id: product_categories.map(&:id))
          expect(expected_product_categories.count).to eq 0 
      end
      it 'does not remove unassociated product categories' do
      	product_categories = create_list(:product_category, 3)
          delete url, headers: auth_header(user)
          present_product_categories_ids = product_categories.map(&:id))
          expected_product_categories = ProductCategory.where(id:present_product_categories_ids)
      	expect(expected_product_categories.ids).to contain_exactly(*present_product_categories_ids)
      end
  end
  ```

  Dessa vez criamos o **ProductCategory** com **create_list** que não tenham **category** associado e chamamos a rota. Em seguida, extraímos os **ids** dos **ProductCategory** que criamos, carregamos do model e verificamos se eles continuam presentes.

- Com os testes feitos, podemos criar a **action destroy**.

  ```ruby
  # app/controllers/admin/v1/categories_controller.rb
  def update
  end
  def destroy
  end
  ```

- Na **action**, vamos carregar a categorie e remover:

  ```ruby
  def destroy
  	@category = Category.find(params[:id])
      @category.destroy!
  rescue
      render_error(fields: @category.errors.messages)
  end
  ```

  Então, estamos carregando, chamando o **destroy!** e caso ocorra algum erro, recuperamos com o **rescue** e rederizamos o erro.

- Execute os testes:

  ```ruby
  bundle exec rspec
  ```

- Por último vamos realizar uma pequena refatoração. Tanto a action **update** quanto a **destroy** precisam carregar  a categoria antes de atuar nela. Então vamos colocar o carregamento dela como um **before_action** para estas duas rotas.

  ```ruby
  # app/controllers/admin/v1/categories_controller.rb
  
  private
  
  def load_category
  	@category = Category.find(params[:id])
  end
  def category_params
  	...
  end
  ```

  Em seguida, vamos adicionar **before_action** chamando este método apenas para as **actions update e destroy**.

  ```ruby
  # app/controllers/admin/v1/categories_controller.rb
  class CategoriesController < ApiController
      before_action :load_category, only: [:update, :destroy]
  end
  ```

- Com o callback adicionado, podemos remover os itens que estão sendo carregados nas actions.

  ```ruby
  # app/controllers/admin/v1/categories_controller.rb
  def update
      #@category = Category.find(params[:id])
  end
  def destroy
      #@category = Category.find(params[:id])
  end
  ```

- Com as linhas removidas e o callback criado, vamos executar os testes novamente:

  ```ruby
  bundle exec rspec
  ....................................................................
  
  Finished in 1.58 seconds (files took 0.99645 seconds to load)
  68 examples, 0 failures
  ```

## Shared example para acesso restrito

​	Conseguimos contruir o cadastro de categorias. Porém, analisando o que fizemos, percebemos que são **endpoints** de **certa forma livres**, embora a gente verifique se o usuário está logado, qualquer perfil pode acessá-los.

O que faremos é restringir o acesso apenas ao perfil **Admin**.

- Antes de começarmos a restrição de acesso, precisamos analisar nossos testes: embora a gente esteja testando todos os endpoints, estamos fazendo isso somente com o perfil **Admin**. Para melhorar nossos testes, vamos escrever mais 2 tipos de testes, um para acesso proibido, **onde validaremos o perfil do usuário**, e outro para **acesso sem autenticação**, quando **não há um cabeçalho de autenticação válido**, e **vamos criá-los em arquivos separados.**

  Então vamos renomear os testes de categoria que criamos para que fique mais compreensivel o que eles fazem:

  ```shell
  mkdir spec/requests/admin/v1/categories
  ```

  E vamos mover e renomear os testes de categories que fizemos até agora:

  ```shell
  mv spec/requests/admin/v1/categories_request_spec.rb spec/requests/admin/v1/categories/admin_requests_spec.rb
  ```

   Por último vamos renomear o teste para deixa-lo compreensível que se trata de um teste específico para o perfil **admin**: 

  ```ruby
  RSpec.describe "Admin V1 Categories as :admin", type: :request do
  ```

- Arquivo movido, agora precisamos pensar o seguinte: os testes que faremos para **acesso proibido e acesso não autenticado,** serão bem parecido até para futuros **endpoints** que vamos testar. Então para esses dois acessos, nós criaremos um **shared example** do **RSpec** e chamaremos dentro dos arquivos de testes.

- Primeiro criaremos um diretório exclusivo para armazená-los:

  ```shell
  mkdir spec/shared_examples
  ```

- Normalmente este diretório fica dentro de spec/support, mas dependendo do porte da aplicação ele tende a crescer bastante  e prefiro trazê-lo para a raiz dos testes, como as **factories**.

  Agora precisamos configurar o **RSpec** para carregar este diretório e para isso vamos fazer o mesmo que fizemos para a pasta **support**:

  ```ruby
  # spec/rails_helper.rb
  Dir[Rails.root.join('spec', 'support', '**', '*.rb')].each { |f| require f }
  Dir[Rails.root.join('spec', 'shared_examples', '**', '*.rb')].each { |f| require f }
  ```

- Com a importação configurada, podemos criar o **shared_examples** e o **primeiro será acesso proibido**. E faremos um bem simples: apenas para verificar se estamos **retornando a mensagem de acesso proibido** e outra para **verificar se o status** é **403** (**:forbidden**)

  <span style="background-color:yellow;color:black">Vamos criar o arquivo para ele:</span>

  ```shell
  touch spec/shared_examples/forbidden_access_example.rb
  ```

  E podemos preencher o caso que queremos cobrir.

  ```ruby
  # spec/shared_examples/forbidden_access_example.rb
  
  shared_examples 'forbidden access' do
      it 'returns error message' do
          expect(body_json['errors']['message']).to eq 'Forbidden access'
      end
      
      it 'returns forbidden status' do
          expect(response).to have_http_status(:forbidden)
      end
  end
  ```

- Para usarmos este novo **shared example**, vamos criar um novo arquivo de teste para categorias de **acesso proibido**, ou seja, que não seja admin.

  ```shell
  touch spec/requests/admin/v1/categories/client_requests_spec.rb
  ```

  Então vamos para a declação do teste:

  ```ruby
  require 'rails_helper'
  
  RSpec.describe "Admin V1 Categories as :client", type: :request do
  	let(:user){ create(:user)}
  end
  ```

  Então criaremos um usuário que seja do tipo **:client** que é de quem pegaremos o cabeçalho de autenticação.

- Para estes testes, podemos definir como base de teste para cada **endpoint** os mesmos que temos para os **admins**.

  ```ruby
  # spec/requests/admin/v1/categories/client_requests_spec.rb
  let(:user) { create(:user, profile: :client) }
  
  context "GET /categories" do
    let(:url) { "/admin/v1/categories" }
    let!(:categories) { create_list(:category, 5) }
  end
  
  context "POST /categories" do
    let(:url) { "/admin/v1/categories" }
  end
  
  context "PATCH /categories/:id" do
    let!(:category) { create(:category) }
    let(:url) { "/admin/v1/categories/#{category.id}" }
  end
  
  context "DELETE /categories/:id" do
    let!(:category) { create(:category) }
    let(:url) { "/admin/v1/categories/#{category.id}" } 
  end
  ```

- Para que possamos utilizar o **shared examples** que criamos, consideremos que eles têm apenas o **expect**, vamos adicionar em cada rota em cada teste com before :each.

  ```ruby
  # spec/requests/admin/v1/client_requests_spec.rb
  context "GET /categories" do
      ...
  	before(:each) { get url, headers: auth_header(user) }
  end
  
  context "POST /categories" do
    ...
    
    before(:each) { post url, headers: auth_header(user) }
  end
  
  context "PATCH /categories/:id" do
    ...
    
    before(:each) { patch url, headers: auth_header(user) }
  end
  
  context "DELETE /categories/:id" do
    ...
    
    before(:each) { delete url, headers: auth_header(user) }
  end
  ```

  Então para cada **context** estamos adicionando as rotas de cada um dentro do **before (:each)**.

- Agora só chamar o **shared example** dentro de cada teste.

  ```ruby
  # spec/requests/admin/v1/client_requests_spec.rb
  
    include_examples "forbidden access"
  end
  
  context "POST /categories" do
    ...
    
    include_examples "forbidden access"
  end
  
  context "PATCH /categories/:id" do
    ...
    
    include_examples "forbidden access"
  end
  
  context "DELETE /categories/:id" do
    ...
    
    include_examples "forbidden access"
  end
  ```

- Com os testes feitos para o perfil **client**, onde esperamos o acesso proibido, podemos fazer o mesmo para acesso não autenticado. Primeiro, vamos criar o **shared example** para isso.

  ```shell
  touch spec/shared_examples/unauthenticated_access_example.rb
  ```

  E para os exemplos não-autenticados, vamos apenas validar se está retornando o status **401**(**:unauthorized**) 

  ```ruby
  # spec/shared_examples/unauthenticated_access_example.rb
  shared_examples 'unauthenticated access' do
  	it 'returns unauthenticated access' do
      	expect(response).to have_http_status(:unauthorized)
      end
  end
  ```

- Agora para configurar estes **shared examples** para não-autenticados nos endpoints de categories  será bem simples. O conteúdo do arquivo vai ficar muito parecido com o do acesso proibido.

  Podemos começar copiando o arquivo de testes e cliente.

  ```shell
  # spec/requests/admin/v1/categories/client_requests_spec.rb para spec/requests/admin/v1/categories/unauthenticated_requests_spec.rb
  
  cp spec/requests/admin/v1/categories/client_requests_spec.rb spec/requests/admin/v1/categories/unauthenticated_requests_spec.rb
  
  ```

- Com o arquivo criado vamos fazer algumas alterações. Primeiro vamos alterar o título do teste.

  ```ruby
  RSpec.describe "Admin V1 Cagories without authentication", type: :request do
  ```

- Após isso, vamos remover o usuário que criamos para criar o cabeçalho. Se é não-autenticado, não precisamos do usuário.

  ```ruby
  let(:user){ create(:user, profile: :client)}
  ```

  Agora podemos alterar todas as rotas que chamamos no **before :each** removendo os headers de autenticação, **GET**, **POST**, **PATCH** e **DELETE**.

  ```ruby
  before(:each) { get url }
  ```

- Agora é só **substituir os shared examples** por:

  ```ruby
  include_examples "unauthenticated access"
  ```

- Agora podemos executar os testes e ver o que temos:

  ```shell
  bundle exec rspec
  ```

  Os testes para os perfis de cliente falharam, afinal não tem nenhum tipo de restrição de acesso por perfil. Os de autenticação passaram porque já cuidamos disso com a autenticação do **Devise Token Auth**.

## Configuração de Restrição de Acesso

​	Os usuários de acesso autenticado já passaram graças ao **Devise Token Auth**, porém precisamos restringir o acesso para não-admins e reexecutar os testes.

- Para a restrição de acesso, como será algo que será compartilhado por todas as rotas de **/admin/v1**, vamos concentrar dentro de **ApiController**. Para começar, vamos criar uma classe interna de erro.

  ```ruby
  # app/controllers/admin/v1/api_controller.rb
  class ApiController < ApplicationController
      class ForbiddenAccess < StandardError; end
  ```

  Essa classe de erro chamaremos de **ForbiddenAccess** e é ela que vamos lançar quando o erro de aceso ocorrer.

- Então, toda vez que esta class de erro for lançado dentro de nosso código nós vamos retornar um erro. Para conseguirmos fazer isso, vamos adicionar um recurso do Rails chamado **rescue_from**.

  ```ruby
  include Authenticatable
  
  rescue_from ForbiddenAccess do
  	render_error(message: 'Forbidden access', status: :forbidden)
  end
  ```

- Com o erro sendo recuperado e considerando, novamente, que tudo está interno ao **/admin/v1** será restrito ao perfil **:admin**, vamos criar um callback que verifique se o usuário tem esse perfil e caso não, lance a exceção **ForbiddenAccess**. Primeiro vamos criar o método.

  ```ruby
  # app/controllers/admin/v1/api_controller.rb
  
  def render_error(message: nil, fields:nil, status: :unprocessable_entity)
      ...
  end
  
  private
  
  def restrict_access_for_admin!
      raise ForbiddenAccess unless current_user.admin?
  end
  
  ```

   Com o método criado, basta chamarmos ele como um **callback**.

  ```ruby
  # app/controllers/admin/v1/api_controller.rb
  
  rescue_from ForbiddenAccess do
      ...
  end
  
  before_action :restrict_access_for_admin!
  
  ...
  ```

  Perceba que estamos chamando para todas a actions e como está no **ApiController** ele será chamado para todas as actions de todos os controllers que herdem dele (que serão todos sob o escopo **/admin/v1**).

- Agora podemos executar nossos testes pra ver como ficou nossa solução

  ```shell
  bundle exec rspec
  
  ................................................................................
  
  Finished in 1.81 seconds (files took 0.89776 seconds to load)
  80 examples, 0 failures
  ```

  E todos os testes passaram! Ou seja, agora temos o acesso restrito apenas ao usuário **:admin**.

## <span style="color:black;background-color:yellow">Concern para renderização de Erros</span>

- Nós criamos uma renderização de erros dentro do ApiController do escopo /admin/v1. Porém, como falamos, no contrato de API foi estabelecido em time para podermos chegar a estrutura que temos hoje, então faz sentido isolarmos em um **Concern**. <span style="color:red"> Obscuro como faz sentido ?</span>

- Vamos começar criando um arquivo para o _concern_ e adicionando a estrutura base dele, extendendo de **ActiveSupport::Concern**. Nós chamaremos ele de **SimpleErrorRenderable**.

  ```shell
  touch app/controllers/concerns/simple_error_renderable.rb
  ```

    Agora podemos declarar e extender o módulo

  ```ruby
  class SimpleErrorRenderable 
  	extend ActiveSupport::Concern
  end
  ```

- A ideia é que esse Concern **renderize uma partial com o conteúdo de erro** e **para a classe incluí-lo**, também deixaremos disponível uma **variável para definir o caminho da partial que será renderizada.**

  ```ruby
  # app/controllers/concerns/simple_error_renderable.rb
  
  extend ActiveSupport::Concern
  
  included do 
      class_attribute :simple_error_partial
  end
  ```

  Desta forma, a classe que incluí-lo, terá também uma variável de classe chamada **simple_error_partial**.

  Agora vamos criar o **método de erro que queremos deixar disponível.** Para evitar impactar o que já existe, vamos criar o método com o mesmo **render_error**.

  ```ruby
  # app/controllers/concerns/simple_error_renderable.rb
  class SimpleErrorRenderable
      extend ActiveSupport::Concern
  	included do
          class_attribute :simple_error_partial
          
          def render_error(message:nil, fields: nil, status: :unprocessable_entity)
              render partial: self.class.simple_error_partial, locals: {message: message, fields: fields}, status: status
          end
      end
  end
  ```

  Aqui vem a parte interessante, estamos declarando o **render_error** com a mesma assinatura do que já existe **ApiController**, porém aqui ele vai **renderizar a partial** que está na variável **simple_error_partial** e enviando para ele as **variáveis locais** **fields** e **message**.

- Com o **concern** definido, podemos importá-lo no **ApiController** e remover o **render_error** que existe lá:

  ```ruby
  # app/controllers/admin/v1/api_controller.rb
  
  include	Authenticable
  include SimpleErrorRenderable
  self.simple_error_partial = "shared/simple_error"
  ```

  Estamos incluindo o **Concern** **SimpleErrorRenderable** e **atribuindo um valor para simple_error_partial com o caminho** da **partial** que queremos renderizar o erro. Podemos então **remover** o método **render_error** que está nesse controller.

  ```ruby
  # app/controllers/admin/v1/api_controller.rb
  
  def render_error(message: nil, fields: nil, status: :unprocessable_entity)
    errors = {}
    errors['fields'] = fields if fields.present?
    errors['message'] = message if message.present?
    render json: { errors: errors }, status: status
  end
  ```

- Vamos criar a partial de erro que definimos na variável **simple_error_partial**. Primeiro vamos criar o diretório **shared**.

  ```shell
  mkdir app/views/shared
  ```

- Dentro dele a partial definida para o erro

  ```shell
  touch app/views/shared/_simple_error.json.jbuilder
  ```

- E para a partial vamos criar o conteúdo para preencher o JSON na mesma estrutura que temos acordado:

  ```ruby
  # app/views/shared/_simple_error.json.jbuilder
  
  json.errors do
      json.fields fields if defined?(fields) && fields.present?
      json.message message if defined?(message) && message.present?
  end
  ```

  Exatamente como as outras renderizações de erro, e**stamos criando a chave errors** e dentro dela uma chave **fields**, que é criada apenas se a variável **fields** existir e tiver algum conteúdo, e uma **message**, que também só é criada se a variável message existir e tiver algum conteúdo.

- Com tudo feito, vamos ver se nossos testes continuam passando:

  ```shell
  bundle exec rspec
  ................................................................................
  
  Finished in 1.75 seconds (files took 1.14 seconds to load)
  80 examples, 0 failures
  ```

- E com isso passamos a entender a importância dos testes para um ciclo saudável de desenvolvimento.

## Adicionando busca por nome

- Para realizarmos a busca por nome, adicionaremos um **scope** aos models. Porém, como queremos tornar isso compartilhável entre os models, criaremos este **scope ** dentro de um concern que será importado em cada model. Junto com este concern, criamos também um **shared example** para este **_concern_** para que ambos sejam importados no teste do model e o model em si. Nosso **concern** se chamará  **NameSearchable **. Então, vamos começar criando o **shared example** para ele.

  ```shell
  touch spec/shared_examples/name_searchable_concern_example.rb
  ```

- Então vamos fazer a declaração de nosso testes:

  ```ruby
  # spec/shared_examples/name_searchable_concern_example.rb
  shared_examples "name searchable concern" do |factory_name|
  end
  ```

  Este **shared example** vai receber o nome da **factory** do model para que internamente possamos criar os dados e validar.

- Para os testes de busca por nome, vamos validar duas coisas: **que os dados de busca que queremos  foram retornados** e os **que não queremos não foram retornados**. E precisamos ter **estes dois tipos de dados**

  ```ruby
  # spec/shared_examples/name_searchable_concern_example.rb
  	
  shared_examples "name searchable concern" do |factory_name|
  	let!(:search_param){"Example"}
      let(:records_to_find) do
      	(0..3).to_a.map { |index| create(factory_name, name: "Example #{index}") }
      end
      let!(:records_to_ignore){ create_list(factory_name, 3) }
  end
  ```

  Criamos uma **variável para o parâmetro de busca**, uma **lista de registros para encontrar** e **uma lista que queremos ignorar.** No let de **records_to_find** não podemos utilizar o **create_list** do FactoryBot porque queremos ter controle pelo nome de cada um.

- Agora podemos adicionar os testes que gostariamos de validar, começando pelo registros encontrados.

  ```ruby
  # spec/shared_examples/name_searchable_concern_spec.rb
  shared_examples "name searchable concern" do |factory_name|
  	let!(:search_param){"Example"}
      let!	(:records_to_find) do
      	(0..3).to_a.map { |index| create(factory_name, name: "Example #{index}") }
      end
      let!(:records_to_ignore){ create_list(factory_name, 3) }
      
      it "found records with expression in :name" do
      	found_records = described_class.search_by_name(search_param)
          expect(found_records.to_a).to contain_exactly(*records_to_find)
      end
  end
  ```

  Utilizamos a **described_class** **para chamar a classe que está sendo testada** e esperamos que ela tenha o método **search_by_name**, que será adicionado pelo concern que criaremos. E em seguida **verificamos se o que foi retornado** tem o mesmo conteúdo do que definimos em **records_to_find**.

- O segundo teste é validar que ele não retorna o que não queremos.

  ```ruby
  # spec/shared_exampples/name_searchable_concern_spec.rb
  
  it "found records with expression in :name" do
     ... 
  end
  
  it "ignores records without expression in :name" do
  	found_records = described_class.search_by_name(search_param)
      expect(found_records.to_a).to_not include(*records_to_ignore)
  end
  ```

  E neste utilizamos a mesma chamada mas dessa vez para certificar que o que foi retornado não possui nenhum elemento do que está em **records_to_ignore**.

- Antes de começarmos a construir o concern, vamos incluir o **shared example** no model **Category**. 

  ```ruby
  # spec/models/category_spec.rb
  RSpec.describe Category, type: :model do
  	it_behaves_like 'name searchable concern', :category
  end
  ```

  Estamos então chamando o **concern** e passando como parâmetro o nome da **factory**.

- Se executarmos os testes, veremos que haverá falhas nos testes de Category.

  ```shell
  bundle exec rspec
  ```

- Agora podemos criar o arquivo **Concern**:

  ```shell
  touch app/models/concerns/name_searchable.rb
  ```

- Vamos declarar o concern:

  ```ruby
  module NameSearchable
  	extend ActiveSupport::Concern
  end
  ```

- E nele, queremos que, ao ser incluído, fique disponível um **scope** do model chamado **search_by_name**.

  ```ruby
  # app/models/concerns/name_searchable.rb
  extend ActiveSupport::Concern
  
  included do
      scope :search_by_name, -> (value) do
      	self.where('name ILIKE?', "%#{value}%")
      end
  end
  ```

  Neste scope estamos utilizando o where para fazer uma **query** utilizando o **ILIKE** do Postgres.

- Agora basta incluirmos este concern no model **Category**.

  ```ruby
  class Category < ApplicationRecord
  	include NameSearchable
      ....
  ```

- Com o concern incluido, podemos executar os testes novamente.

  ```shell
  bundle exec rspec
  ..................................................................................
  
  Finished in 1.75 seconds (files took 0.92288 seconds to load)
  82 examples, 0 failures
  ```



Fazendo um adendo 

[Rails guide - ActiveRecord ](https://guides.rubyonrails.org/active_record_querying.html#scopes)

[Rails Guide Secury](https://guides.rubyonrails.org/security.html#sql-injection)

[Rails unscoped](https://prathamesh.tech/2019/07/15/how-to-not-use-unscope-in-rails/)

[ORM - OneBitCode ](https://onebitcode.com/otimizando-a-velocidade-das-queries-no-ruby-on-rails/)

### Pure String Conditions

Building your own conditions as pure strings can leave you vulnerable to SQL injection exploits. For example, `Client.where("first_name LIKE '%#{params[:first_name]}%'")` is not safe. See the next section for the preferred way to handle conditions using an array.

Putting the variable directly into the conditions string will pass the variable to the database as-is. This means that it will be an unescaped variable directly from a user who may have malicious intent. If you do this, you put your entire database at risk because once a user finds out they can exploit your database they can do just about anything to it. Never ever put your arguments directly inside the conditions string.

2 -In most database systems, on selecting fields with **distinct** from a result set using methods like **select, pluck and ids;** the **order** method will raise an **ActiveRecord::StatementInvalid** exception unless the field(s) used in order clause are included in the select list. 

3 - To apply `LIMIT` to the SQL fired by the `Model.find`, you can specify the `LIMIT` using **limit**` and `**offset** methods on the relation. You can use **limit** to specify the number of records to be retrieved, and use **offset** to specify the number of records to skip before starting to return the records. 

```ruby
Client.limit(5).offset(30)
```

Will return instead a maximum of 5 clients beginning with the 31st.

4 - Pessimistic locking uses a locking mechanism provided by the underlying database. Using `lock` when building a relation obtains an exclusive lock on the selected rows. Relations using `lock` are usually wrapped inside a transaction for preventing deadlock conditions.

```ruby
Item.transaction do
  i = Item.lock.first
  i.name = 'Jones'
  i.save!
end
```

5 - You can also pass raw SQL to the `lock` method for allowing different types of locks. For example, MySQL has an expression called `LOCK IN SHARE MODE` where you can lock a record but still allow other queries to read it. To specify this expression just pass it in as the lock option:

```ruby
Item.transaction do
  i = Item.lock("LOCK IN SHARE MODE").find(1)
  i.increment!(:views)
end
```

6 - If you already have an instance of your model, you can start a transaction and acquire the lock in one go using the following code:

```ruby
item = Item.first
item.with_lock do
  # This block is called within a transaction,
  # item is already locked.
  item.increment!(:views)
end
```

7 - Scoping allows you to specify commonly-used queries which can be referenced as method calls on the association objects or models. With these scopes, you can use every method previously covered such as `where`, `joins` and `includes`. All scope bodies should return an `ActiveRecord::Relation` or `nil` to allow for further methods (such as other scopes) to be called on it.

To define a simple scope, we use the scope method inside the class, passing the query that we'd like to run when this scope is called:

```ruby
class Article < ApplicationRecord
  scope :published, -> { where(published: true) }
end
```

Scopes are also chainable within scopes:

```ruby
class Article < ApplicationRecord
  scope :published,               -> { where(published: true) }
  scope :published_and_commented, -> { published.where("comments_count > 0") }
end
```

To call this `published` scope we can call it on either the class:

```ruby
Article.published
```

Or on an association consisting of `Article` objects:

```ruby
category = Category.first
category.articles.published # => [published articles belonging to this category
```

Using Conditions

Your scope can utilize conditionals:

```ruby
class Article < ApplicationRecord
  scope :created_before, ->(time) { where("created_at < ?", time) if time.present? }
end
```

Like the other examples, this will behave similarly to a class method.

```ruby
class Article < ApplicationRecord
  def self.created_before(time)
    where("created_at < ?", time) if time.present?
  end
end
```

However, there is one important caveat: A scope will **always return an ActiveRecord::Relation object**, even if the conditional **evaluates to false**, **whereas a class method, will return nil**. This can cause **NoMethodError** when chaining class methods with conditionals, **if any of the conditionals return false**.

## Paginação nos Models

- Criaremos um concern voltado para adicionar um **método de paginação** no model. Lembrando que só estamos criando como concern porque **pretendemos compartilhar entre mais models futuramente**.

  E como já vimos como funciona um teste de **concern**, pegaremos o **shared example** pronto a partir da nossa aplicação já construída.

- O nosso **concern** vai se chamar **Paginatable** e nele implementaremos um método chamado **paginate**, que vai ter dois parâmetros: um **page** para o número de páginas e outro **length** para a quantidade de registros que queremos. E vamos começar criando o **shared example** para ele.

  ```shell
  touch spec/shared_examples/paginatable_concern_example.rb
  ```

- Para o conteúdo do **shared example**:

  ```ruby
  shared_examples "paginatable concern" do |factory_name|
    context "when records fits page size" do
      let!(:records) { create_list(factory_name, 20) }
  
      context "when :page and :length are empty" do
        it "returns default 10 records" do
          paginated_records = described_class.paginate(nil, nil)
          expect(paginated_records.count).to eq 10
        end
  
        it "matches first 10 records" do
          paginated_records = described_class.paginate(nil, nil)
          expected_records = described_class.all[0..9]
          expect(paginated_records).to eq expected_records
        end
      end
  
      context "when :page is fulfilled and :length is empty" do
        let(:page) { 2 }
        
        it "returns default 10 records" do
          paginated_records = described_class.paginate(page, nil)
          expect(paginated_records.count).to eq 10
        end
  
        it "returns 10 records from right page" do
          paginated_records = described_class.paginate(page, nil)
          first_record_index = 10
          last_record_index = 19
          expected_records = described_class.all[first_record_index..last_record_index]
          expect(paginated_records).to eq expected_records
        end
      end
  
      context "when :page and :length are fulfilled and fits records size" do
        let(:page) { 2 }
        let(:length) { 5 }
        
        it "returns right quantity of records" do
          paginated_records = described_class.paginate(page, length)
          expect(paginated_records.count).to eq length
        end
  
        it "returns records from right page" do
          paginated_records = described_class.paginate(page, length)
          first_record_index = 5
          last_record_index = 9
          expected_records = described_class.all[first_record_index..last_record_index]
          expect(paginated_records).to eq expected_records
        end
      end
  
      context "when :page and :length are fulfilled and does not fit records size" do
        let(:page) { 2 }
        let(:length) { 30 }
        
        it "does not return any records" do
          paginated_records = described_class.paginate(page, length)
          expect(paginated_records.count).to eq 0
        end
  
        it "returns empty result" do
          paginated_records = described_class.paginate(page, length)
          expect(paginated_records).to_not be_present
        end
      end
    end
  
    context "when records does not fit page size" do
      let!(:records) { create_list(factory_name, 7) }
  
      context "when :page and :length are empty" do
        it "returns 7 records" do
          paginated_records = described_class.paginate(nil, nil)
          expect(paginated_records.count).to eq 7
        end
  
        it "matches first 7 records" do
          paginated_records = described_class.paginate(nil, nil)
          expected_records = described_class.all[0..6]
          expect(paginated_records).to eq expected_records
        end
      end
  
      context "when :page is fulfilled and :length is empty" do
        let(:page) { 2 }
        
        it "does not return any records" do
          paginated_records = described_class.paginate(page, nil)
          expect(paginated_records.count).to eq 0
        end
  
        it "returns empty result" do
          paginated_records = described_class.paginate(page, nil)
          expect(paginated_records).to_not be_present
        end
      end
  
      context "when :page and :length are fulfilled" do
        let(:page) { 2 }
        let(:length) { 5 }
        
        it "returns right quantity of records" do
          paginated_records = described_class.paginate(page, length)
          expect(paginated_records.count).to eq 2
        end
  
        it "returns records from right page" do
          paginated_records = described_class.paginate(page, length)
          first_record_index = 5
          last_record_index = 6
          expected_records = described_class.all[first_record_index..last_record_index]
          expect(paginated_records).to eq expected_records
        end
      end
    end
  end
  ```

  Neste **shared example**, também guardamos o nome da Factory onde ele está incluido e estamos fazendo diversas validações que estão incluidas dentro de dois context principais: **quando a quantidade a de registros cabem em uma página** e **quando não cabem.**

  E dentro de cada um destes dois contextos principais, temos sub-contextos para teste que envolvem testar:	 quando page e length são vazios, quando page é prenchido mas lenght é vazio e quando ambos são preenchidos.

- Com o **shared example** definido, podemos adicionar aos testes de Categoria.

  ```ruby
  # spec/models/category_spec.rb
  it_behaves_like "name searchable concern", :category
  it_behaves_like "paginatable concern", :category
  ```

- Com o shared example sendo chamado, podemos executar os testes:

  ```shell
  bundle exec rspec
  ```

- Com os testes falhando, iremos começar criando o arquivo concern:

  ```shell
  touch app/models/concerns/paginatable.rb
  ```

- Arquivo criado, podemos adicionar a assinatura do concern:

  ```ruby
  module Paginatable
  	extend ActiveSupport::Concern
  end
  ```

- Neste concern vamos adicionar  constantes, uma para o máximo de items por página e outra para página padrão:

  ```ruby
  # app/models/concerns/paginatable.rb
  module Paginatable
      extend ActiveSupport::Concern
      
      MAX_PER_PAGE = 10
      DEFAULT_PAGE = 1
  end
  ```

- Através dele um novo **scope** **paginate** será adicionado:

  ```ruby
  # app/models/concerns/paginatable.rb
  
  module Paginatable
      extend ActiveSupport::Concern
      
      MAX_PER_PAGE = 10
      DEFAULT_PAGE = 1
      
      included do
          scope :paginate, -> (page, length) do
              page = page.present?  && page > 0 ? page : DEFAULT_PAGE
              length = length.present? && length > 0 ? length : MAX_PER_PAGE
              starts_at = (page-1)*length
              limit(length).offset(starts_at)
          end
      end
  end
  ```

  Neste **paginate**, estamos verificando se **page** está presente e se é maior que 0 e caso não, utilizamos o DEFAULT_PAGE. O mesmo com length, caso ela não esteja preenchida ou seja menor ou igual a 0, utilizaremos MAX_PER_PAGE. E com teste dois valores, utilizaremos o **limit  offset.**

- Com ele construído podemos adicionar ao model de categoria.

  ```ruby
  # app/models/category.rb
  
  include Paginatable
  ```

- Agora podemos reexecutar os testes e ver o resultado:

  ```shell
  bundle exec rspec
  ................................................................................................
  
  Finished in 3.22 seconds (files took 1.11 seconds to load)
  96 examples, 0 failures
  ```

## Aplicando os Concerns aos outros Models 

- Simplesmente iremos adicionar os **concerns** de **paginação** e **busca por nome que criamos.**

- Primeiro vamos adicionar os **shared examples** do dois concerns nos testes de cada **model**.

  ```ruby
  # spec/models/coupon_spec.rb
  it "is valid with future date" do
  	...
  end
  
  it_behaves_like "paginatable concern", :coupon
  ```

  ```ruby
  # spec/models/product_spec.rb
  it { is_expected.to validate_presence_of(:image) }
  
  it_behaves_like "name searchable concern", :product
  it_behaves_like "paginatable concern", :product
  ```

  ```ruby
  # spec/models/system_requirement.spec
  it { is_expected.to have_many(:games).dependent(:restrict_with_error) }
  
  it_behaves_like "name searchable concern", :system_requirement
  it_behaves_like "paginatable concern", :system_requirement
  ```

  ```ruby
  # spec/models/user_spec.rb
  
  it { is_expected.to define_enum_for(:profile).with_values({ admin: 0, client: 1 }) }
  
  it_behaves_like "name searchable concern", :user
  it_behaves_like "paginatable concern", :user
  ```

- Agora podemos adicionar os concerns:

  ```ruby
  # app/moldels/coupon.rb
  class Coupon < ApplicationRecord
  	include Paginatable
  ```

  ```ruby
  # app/models/product.rb 
  
  class Product < ApplicationRecord
    include NameSearchable
    include Paginatable
  ```

  ```ruby
  # app/models/system_requirement.rb
  class SystemRequirement < ApplicationRecord
    include NameSearchable
    include Paginatable
  ```

  ```ruby
  # app/models/user.rb
  
  class User < ActiveRecord::Base
    include NameSearchable
    include Paginatable
  ```

  :happy: :happy: :happy: :happy:

  ```shell
  bundle exec rspec
  ..............................................................................................................................................................
  
  Finished in 13.68 seconds (files took 1.12 seconds to load)
  158 examples, 0 failures
  ```
  
  

- ## Criar o CRUD de System Requirements :white_check_mark:

- ## Criar o CRUD de Coupons :white_check_mark:

- ## Criar o CRUD de User :white_check_mark:

```shell
...............................

Finished in 14.61 seconds (files took 0.92835 seconds to load)
245 examples, 0 failures
```

##   

## <span style="color:gold">Spin-off OneBitExchange</span> :euro: :dollar: :brazil:

### 	Gerando e Dockerizando o Projeto

- Vamos criar o projeto:

  ```shell
  docker run --rm --user "$(id -u):$(id -g)" -v $(pwd):/usr/src -w /usr/src -ti ruby:2.6.5 bash
  
  gem install rails -v 6.0.2.2
  
  rails new onebit_exchange --database=postgresql --skip-bundle
  
  exit
  
  cd onebit_exchange
  ```

  - **--user "$(id -u):$(id -g)"** - Seta o usuário com a permissão necessária para editar o projeto.
  - **-v**: volume - espaços compartilhados

- Vamos agora configurar o docker na raiz do projeto faça a criação do arquivo Dockfile.

  ```dockerfile
  FROM ruby:2.6.5 # Selecionamos a máquina que desejamos alterar
  
  # add nodejs and yarn dependencies for the frontend
  RUN curl -sL https://deb.nodesource.com/setup_12.x | bash - && \
  curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
  echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
  
  # Instala nossas dependencias
  RUN apt-get update && apt-get install -qq -y --no-install-recommends \
  nodejs yarn build-essential libpq-dev imagemagick git-all nano
  
  # Instalar bundler
  RUN gem install bundler
  
  # Seta nosso path
  ENV INSTALL_PATH /onebitexchange
  
  # Cria nosso diretório
  RUN mkdir -p $INSTALL_PATH
  
  # Seta o nosso path como o diretório principal
  WORKDIR $INSTALL_PATH
  
  # Copia o nosso Gemfile para dentro do container
  COPY Gemfile ./
  
  # Seta o path para as Gems
  ENV BUNDLE_PATH /gems
  
  # Copia nosso código para dentro do container
  COPY . .
  ```

- Iremos agora criar o arquivo **docker-compose.yml**

  ```yml
  version: "3.8"
  
  services:
    db:
      image: "postgres:12.2"
      environment: 
        - POSTGRES_PASSWORD=postgres # variavel de ambiente
      volumes:
        - postgres:/var/lib/postgresql/data
  
    app:
      build: .
      command: bash start.sh
      environment:
        - DB_PASSWORD=postgres     
      volumes:
        - .:/onebitexchange
        - gems:/gems
      ports:
        - "3001:3000"
      depends_on:
        - db
  
  volumes:
    postgres:
    gems:
  ```

- Crie um arquivo chamado **start.sh**:

  ```sh
  # Instalar as GEMS 
  bundle check || bundle install
  
  # Roda nosso servidor 
  bundle exec puma -C config/puma.rb
  ```

- Vamos construir a imagem do ambiente

  ```shell
  docker-compose build
  ```

- Caso de linux:

  ```shell
  sudo groupadd docker
  sudo usermod -aG docker $USER
  sudo service docker restart
  ```

### Setup Initial 

- Adicione o rspec em seu Gemfile :

  ```ruby
  gem 'rspec-rails', '~>4.0'
  ```

- Instale as gems:

  ```shell
  docker-compose run --rm web bundle install
  ```

- Para configurar o RSpec utilize o seguinte comando:

  ```shell
  docker-compose run --rm web bundle exec rails g rspec:install
  ```

- Como ele criar a pasta spec, podemos apagar a pasta de testes:

  ```shell
  rm -r test
  ```

- Altere seu arquivo config/database.yml

  ```yml
  adapter: postgresql
  encoding: unicode
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  host: db
  user: postgres
  password: <%= ENV['DB_PASSWORD']%>
  
  
  development:
  <<: *default
  database: onebit_exchange_development
  
  
  test:
  <<: *default
  database: onebit_exchange_test
  
  
  production:
  <<: *default
  database: onebit_exchange_production
  username: onebit_exchange
  ```

- Vamos criar nosso database:

  ```shell
  docker-compose run --rm web bundle exec rails db:create db:migrate
  ```

- Instale o webpacker rodando: ( Super importante )

  ```shell
  docker-compose run --rm web bundle exec rails webpacker:install
  ```

- Vamos testar

  ```shell
  docker-compose up
  ```

### Token Current data feed

​	[API - currency data feed ](https://currencydatafeed.com/panel/api)

```
iwl8twoz5k103e233s30
```

### Credentials

- Vamos abrir o editor para podermos editar nossas credenciais:

  ```shell
  docker-compose run --rm -e EDITOR=nano app bundle exec rails credencials:edit
  ```

  ```txt
  O Rails possui o um arquivo config/credentials.yml.enc que é um arquivo criptografado utilizado para armazenar dados sensíveis como chaves de APIs
  Quando executamos este comando, o Rails pega a chave de criptogradia que está no arquivo config/master.key , descriptografa este arquivo e abre no editor que enviamos no "EDITOR". Quando fechamos, ele recriptografa.
  Por isso, podemos sempre comitar o arquivo config/credentials.yml.key . Mas NÃO podemos fazer o mesmo como config/master.key
  ```

- Vamos editá-lo:

  ```
  secret_key_base: <sua_secret_key>
  
  currency_api_key: <sua_api_key>
  currency_api_url: https://currencydatafeed.com/api/data.php
  ```

  ```txt
  Quando vc abre o arquivo, ele já vem com o secret_key_base . Deixe como está.
  Onde temos o <sua_api_key> , coloque a chave do Currency Data Feed que está na sua conta que pegamos na aula anterior.
  Nós poderíamos também ter feito uma separação por ambiente, mas como utilizaremos a mesma chave de API para os 3 ambientes de desenvolvimento, teste e produção, deixaremos desta forma
  ```

- Agora basta fechar o arquivo que será recriptografado.

### Instalando as dependências 

- Vamos fazer a instalação do **bootstrap** em nosso projeto

  ```shell
  docker-compose run --rm app bundle exec yarnadd bootsrap
  ```

- Crie dentro da pasta **javascript** , raiz do projeto, a pasta **stylesheets** e dentro da mesma o arquivo **application.rb**.

  ```ruby
  @import '~bootstrap/scss/bootstrap'
  ```

- No arquivo app/javascript/packs/application.js coloque:

  ```ruby
  import "../stylesheets/application"
  ```

### Exchange Service teste

- Vamos adicionar a gem rest-client

  [Repositório da gem](https://github.com/rest-client/rest-client)

  ```shell
  gem 'rest-client'
  ```

- Instale com o comando:

  ```shell
  docker-compose run --rm app bundle install 
  ```

- Agora crie uma pasta chamada spec/services, porém você não terá permissões de editá-la sendo assim devemos configurar a permissão.

  ```shell
  sudo chown -R $USER:$USER
  
  mkdir spec/services
  touch spec/services/exchange_service_spec.rb
  ```

- Adicione o seguinte conteúdo em spec/services/exchange_service_spec.rb 

  > ```ruby
  > require 'rails_helper'
  > require 'json'
  > require './app/services/exchange_service.rb'
  > 
  > describe ExchangeService do
  >   let(:source_currency){ 'BRL' }
  >   let(:target_currency){ 'USD'}
  >   let(:exchange_value){ 3.56 }
  >   let(:api_return) do
  >   	{
  >     	currency: [
  >          {
  >          	currency: "#{source_currency}/#{target_currency}",
  >             value: exchange_value,
  >             date: Time.now,
  >             type: 'Original' 
  >           }
  >         ] 	   
  >     }
  >   end
  >    
  >   before do
  >   	allow(RestClient).to receive(:get) { OpenStruct.new(body: api_return.to_json)}
  >   end
  >     
  >   it '#call' do
  >   	amount = rand(1..9999)
  >     service_exchange = ExchangeService.new('USD','BRL', amount).call
  >     expect_exchange = amount * exchange_value
  >     expect(service_exchange).to eq expect_exchange
  >   end
  > end
  > ```

- Rode o teste e irá falhar

  ```shell
  docker-compose run --rm app bundle exec rspec spec/services/exchange_service_spec.rb 
  ```

### Exchange Service

- Dentro da sua pasta app/services crie o arquivo exchange_service.rb

  ```shell
  mkdir app/services
  touch app/services/exchange_service.rb
  ```

- Vamos adicionar o seguinte conteúdo:

  ```ruby
  require 'rest-client'
  require 'json'
  
  class ExchangeService
  	def initialize(source_currency, target_currency, amount)
      	@source_currency = source_currency
          @target_currency = target_currency
          @amount = amount.to_f
      end
      
      def call 
          begin 
          	value = get_exchange
              value * @amount
          rescue RestClient::ExceptionWithResponse => e
          	e.response
          end
      end
      
      private
      
      def get_exchange 
      	exchange_api_url = Rails.application.credentials[:currency_api_url]
          exchange_api_key = Rails.application.credentials[:currency_api_key]
          url = "#{exchange_api_url}?token=#{exchange_api_key}&currency=#{@source_currency}/#{@target_currency}"
          res = RestClient.get url
          JSON.parse(res.body)['currency'][0]['value'].to_f
      end
  end
  ```

- Vamos entrar no console para testar:

  ```shell
  docker-compose run --rm app bundle exec rails c
  ```

- Vamos testar a conversão de 100 reais em euro 

  ```shell
  ExchangeService.new('BRL', 'EUR', 100)
  ```

- Por fim, rode novamente o nosso teste de service:

  ```shell
  docker-compose run --rm app bundle exec rspec spec/services/exchange_service_spec.rb
  ```

   