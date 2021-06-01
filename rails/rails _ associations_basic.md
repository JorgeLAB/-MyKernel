# Rails - Associations Basics

## The Types of Associations

Rails suporta 6 tipos de associações:

* *belongs_to*
* *has_one* 
* *has_many*
* *has_many :through* 
* *has_one :through*
* *has_and_belongs_to_many*

### belongs_to 

​	Adiciona uma relação com outro model, cada instância do model declarada se conecta a uma outra instância.

```ruby
class Book < ApplicationRecord 
	belongs_to :author
end
```

 Ou seja um livro uni-se a um único **author**.

I - Repare um detalhe o *:author* está no singular por um motivo, o Rails irá inferir automaticamente o nome declarado ao model associado caso esteja *:authors* o Rails não irá reconhecer. Ex: `Book.create(authors: @author)`

Sua migration correspondente é da seguinte forma:

```ruby
class CreateBooks < ActiveRecord::Migration[6.0]
	def change
	  create_table :authors do |t|
        t.string :name
		t.timestamps
	  end 
	end
	
	create_table :books dos |t|
	  t.belongs_to :author
	  t.datetime :published_at
	  t.timestamps
	end
  end
end
```

> When used alone, `belongs_to` produces a one-directional one-to-one connection. Therefore each book in the above example "knows" its author, but the authors don't know about their books. To setup a [bi-directional association](https://guides.rubyonrails.org/association_basics.html#bi-directional-associations) - use `belongs_to` in combination with a `has_one` or `has_many` on the other model.
>
> `belongs_to` does not ensure reference consistency, so depending on the use case, you might also need to add a database-level foreign key constraint on the reference column, like this:

 ```ruby
create_table :books do |t|
  t.belongs_to :author, foreign_key: true
  #...
end
 ```

### has_one 

Indica que um outro model tem uma referência com este model. Esse model pode rebecer informações por meio dessa associação.

```ruby
class Supplier < ApplicationRecord 
	has_one :account
end
```

Isto quer dizer que o id estrangeiro dessa associação ficará em *:account* .

|   suppliers    | has_one |       accounts        |
| :------------: | :-----: | :-------------------: |
| Model Supplier |         |     Model Account     |
|    id: int     |         |        id:int         |
|  name:string   |         |    supplier_id:int    |
|                |         | account_number:string |

A migration correspondente seria assim: 

```ruby
class CreateSuppliers < ActiveRecord::Migration[6.0]
  def change 
  	create_table :suppliers do |t|
    	t.string :name 
        t.timestamps
    end
      
    create_table :accounts do |t|
    	t.belongs_to :suppliers
        t.string :account_number
        t.timestamps
    end
  end
end
```

Uma observação de onde se colacar *has_one* e *belongs_to*, apenas reforçando a distinção dos dois é a presença do chave estrangeira. Mas *has_one* semanticamente é possuir ... veja o exemplo:

```ruby
class Supplier < ApplicationRecord
  has_one :account 
end

class Account < ApplicationRecord
  belongs :supplier
end
```

Ou seja, **supplier** possui um **account**  é semanticamente correto, é ao contrário também corresponde **account** pertence a um **supplier**.

A migration correspondente é:

```ruby
class CreateSuppliers < ActiveRecord::Migration[6.0]
  def change 
    create_table :suppier do |f|
      f.string :name 
      f.timestamps
    end
      
    create_table :account do |f|
      f.bigint :supplier_id
      f.string :account_number 
      f.timestamps
    end
      
    add_index :accounts, :supplier_id 
  end
end
```

> Using `t.bigint :supplier_id` makes the foreign key naming obvious and explicit. In current versions of Rails, you can abstract away this implementation detail by using `t.references :supplier` instead.

### has_many 

Indica uma associação um-para-muitos com outro model.

```ruby 
class Author < ApplicationRecord 
  has_many :books 
end
```

:alien: Veja que o nome do model é no plural **:books** 

A migration correspondente é: 

```ruby
class CreateAuthor < ApplicationRecord
  def change 
  	create_table :authors do |t|
      t.string :name
      t.timestamps
    end
   	
    create_table :books do |t|
      t.belongs_to :author
      t.datatime :published_at
      t.timestamps
    end
  end 
end
```

### has_many :through 

Geralmente empregado em uma uma associação muitos para muitos.

> This association indicates that the declaring model can be matched with zero or more instances of another model by proceeding *through* a third model. 

```ruby 
class Physician < ApplicationRecord 
  has_many :appointments
  has_many :patients, through: :appointments 
end

class Appointment < ApplicationRecord
  belongs_to :patient
  belongs_to :physician
end

class Patient < ApplicationRecord
  has_many :appointments
  has_many :physician, through: :appointments
end
```



|  patients   |       appointments        |  physician  |
| :---------: | :-----------------------: | :---------: |
|   id: int   |          id:int           |   id:int    |
| name:string |      patient_id:int       | name:string |
|             |     physician_id:int      |             |
|             | appointment_date:datetime |             |

A migration correspondente seria:

```ruby
class CreateAppointments < ActiveRecord::Migration[6.0]
  def change 
    create_table :physicians do |t|
      t.string :name
      t.timestamps
    end
    create_table :appointments do |t|
      t.belongs_to :physician
      t.belongs_to :patient
      t.datetime appointment_date
      t.timestamps
    end
    create_table :physician do |t|
      t.string :name
      t.timestamps
    end
  end
end
```

> The `has_many :through` association is also useful for setting up "shortcuts" through nested `has_many` associations. For example, if a document has many sections, and a section has many paragraphs, you may sometimes want to get a simple collection of all paragraphs in the document. You could set that up this way:

```ruby
class Document < ApplicationRecord 
  has_many :sections
  has_many :paragraphs, through: :sections
end

class Section < ApplicationRecord
  belongs_to :document
  has_many :paragraphs
end

class Paragraph < ApplicationRecord 
  belongs_to :section
end
```

O uso **through** lá em cima possibilita o seguinte: `@document.paragraphs`

### has_one :through 

> A [`has_one :through`](https://api.rubyonrails.org/v6.1.3.1/classes/ActiveRecord/Associations/ClassMethods.html#method-i-has_one) association sets up a one-to-one connection with another model. This association indicates that the declaring model can be matched with one instance of another model by proceeding *through* a third model. For example, if each supplier has one account, and each account is associated with one account history, then the supplier model could look like this:

``` ruby
class Supplier < ApplicationRecord
  has_one :account
  has_one :account_history, through: :account
end
class Account < ApplicationRecord 
  belongs_to :supplier
  has_one :account_history
end
class AccountHistory < ApplicationRecord
  belongs_to :account
end
```

A migration correspondente seria assim:

```ruby
class CreateAccountHistories < ActiveRecord::Migration[6.0]
  def change 
  	create_table :suppliers do |t|
      t.string name
      t.timestamps
    end
      
    create_table :accounts do |t|
      t.belongs_to :supplier 
      t.string :account_number 
      t.timestamps
    end
      
    create_table :account_histories do |t|
      t.belongs_to :account
      t.integer :credit_rating
      t.timestamps
    end
  end
end
```

### has_and_belongs_to_many

Cria uma conexão direta com dois models, `a direct many-to-many connection ` . Essa associação não tem interferência de um terceiro model, indicado diretamente que uma está associada a outra.

```ruby
class Assembly < ApplicationRecord 
  has_and_belongs_to_many :parts
end

class Part < ApplicationRecord
  has_and_belongs_to_many :assemblies
end
```

A migration correspondente é da seguinte forma:

```ruby
class CreateAssembliesAndParts < ActiveRecord::Migration[6.0]
  def change
    create_table :assemblies do |t|
      t.string :name
      t.timestamps 
    end
      
    create_table :parts do |t|
      t.string :part_number
      t.timestamps
    end
    
    create_table :assemblies_parts, id: false do |t|
      t.belongs_to :assembly
      t.belongs_to :parts 
    end
  end
end
```



## Polymorphic Associations 

> A slightly more advanced twist on associations is the *polymorphic association*. With polymorphic associations, a model can belong to more than one other model, on a single association. For example, you might have a picture model that belongs to either an employee model or a product model. Here's how this could be declared:

```ruby 
class Picture < ApplicationRecord 
  belongs_to :imageable, polymorphic: true
end

class Employee < ApplicationRecord 
  has_many :pictures, as: :imageable
end

class Product < ApplicationRecord
  has_many :pictures, as: :imageable
end
```

:stopwatch: Stop here



## Self Joins

​	Podemos criar um model que poderá se relacionar com sigo mesmo. Um exemplo seria entre gerentes e subordinados, todos são empregados. Trataremos nosso modelo da seguinte forma: 

```ruby
class Employee < ApplicationRecord
	has_many :subordinates, class_name: "Employee", 
                            foreign_key: "manager_id"
	belongs_to :manager, class_name: "Employee", optional: true
end
```

Nos estamos colocando os empregados em uma única classe. Podemos então escrever `@employee.subordinates` e `@employee.manager`.

Como será a migration:

```ruby
class CreateEmployees < ActiveRecord::Migration[6.0]
  def change
    create_table :employees do |t|
	t.references :manager, foreign_key: { to_table: :employees }
	t.timestamps
  end
end
```

## Escolhendo entre uma `has_many :through` e  `has_and_belongs_to_many` 

O Rails oferece dois modos de realizar uma relação `many_to_many` .  Vamos analisar a primeira `has_and_belongs_to_many` que é uma ligação direta entre os models : 

```ruby
class Assembly < ApplicationRecord
	has_and_belongs_to_many :parts
end

class Part < ApplicationRecord
	has_and_belongs_to_many :assemblies
end
```

O segundo modo é utilizar de um `join table` empregando o hook `has_many :through` :

```ruby
class Assembly < ApplicationRecord
	has_many :manifests
	has_many :parts, through: :manifest
end

class Manifest < ApplicationRecord
	belongs_to :assembly
	belongs_to :part
end

class Part < ApplicationRecord
	has_many :manifests
	has_many :assemblies, through: :manifest
end
```

Cria-se uma tabela manifests que podemos tratar de forma independente das demais tabelas. Como assim ? em um único lugar você poderá mapear todas as relações de assembly e part. Algo que empregando `has_and_belongs_to_many` não tem como.

>  You should use has_many :through if you need validations, callbacks, or extra attributes on the join model.

## Associações bi-direcionais

Esse tipo de associação requer uma declaração nos dois models:

```ruby
class Author < ApplicationRecord
	has_many :books
end

class Book < ApplicationRecord
	belongs_to :author
end
```

Pelos nomes declarados automaticamente o ActiveRecord reconhece a associação pelos nomes, sem precisar acrescentar mais nada. Ele irá trabalhar apenas com uma cópia de dado em todo o processo de consulta: 

```ruby
irb> a = Author.first
irb> b = a.books.first
irb> a.first_name == b.author.first_name 
true
irb> a.first_name = "Nome_de_Author"
irb> a.first_name == b.author.firstt_name 
true
```

Por meio desse tipo de estrutura de associação podemos realizar consultas de maneira mais inteligente sem depender memória. 

O ActiveRecord não identifica associações bi-direcionais que possuam scope ou  outras opções.

```ruby
class Author < ApplicationRecord 
	has_many :books
end

class Book < ApplicationRecord
	belongs_to :writer, class_name: 'Author', foreign_key: 'author_id'
end
```

O ActiveRecord não irá fazer uma criação de cópia automáticamente:

```ruby
irb> a = Author.first
irb> b = a.books.first
irb> a.first_name == b.author.first_name 
true
irb> a.first_name = "Nome_de_Author"
irb> a.first_name == b.author.firstt_name 
false
```

Com a alteração do atributo `first_name` não houve um mapeamento automático do ActiveRecord para reconhecer a mudança e propagá-la as outra variáveis. Mas há uma opção chamada `:inverse_of ` que realiza esse processo: ( ou durante o processo no console você poderá realizar um load) :arrow_down:  Lá embaixo há detalhes sobre [Controlling Caching](controlling_caching).

``` ruby
class Author < ApplicationRecord
	has_many :books, inverse_of: 'writer'
end

class Book < ApplicationRecord
	belongs_to :writer, class_name: 'Author', foreign_key: 'author_id'
end
```

```ruby
irb> a = Author.first
irb> b = a.books.first
irb> a.first_name == b.author.first_name 
true
irb> a.first_name = "Nome_de_Author"
irb> a.first_name == b.author.firstt_name 
true
```

## Mais detalhes de Associações com References

### belongs_to

[ Documentação](https://guides.rubyonrails.org/association_basics.html#detailed-association-reference)

Em termos de banco de dados, belongs_to representa uma coluna na tabela do model que é uma referência à outra tabela. Tanto poderá ser uma associação de um-para-muitos ou um-para-um, no caso de um-para-um a declação no outro model será `has_one`. Sendo assim exitem 6 métodos de associações `belongs_to` que podemos utilizar:

* association
* association=(associate)
* build_association(attibutes = { } )
* create_association(attributes = { } )
* create_association!(attributes = { } )
* reload_association

Basta apenas trocar `association` pela correspondente ao model como exemplo temos: 

```ruby
class Book < ApplicationRecord
	belongs_to :author
end
```

Temos todos esses métodos para book:

```ruby
author 
author=
build_author
create_author
create_author!
reload_author
```

> When initializing a new `has_one` or `belongs_to` association you must use the `build_` prefix to build the association, rather than the `association.build` method that would be used for `has_many` or `has_and_belongs_to_many` associations. To create one, use the `create_` prefix.

### Opções do belongs_to

- `:autosave`
- `:class_name`
- `:counter_cache`
- `:dependent`
- `:foreign_key`
- `:primary_key`
- `:inverse_of`
- `:polymorphic`
- `:touch`
- `:validate`
- `:optional`

#### autosave

> If you set the `:autosave` option to `true`, Rails will save any loaded association members and destroy members that are marked for destruction whenever you save the parent object. Setting `:autosave` to `false` is not the same as not setting the `:autosave` option. If the `:autosave` option is not present, then new associated objects will be saved, but updated associated objects will not be saved.

:interrobang:  Não entendi a última confirmação. Acho! quando eu atualizo um objeto associado, se não estivermos com autosave setado temos que quando chamarmos este objeto ele ainda será o original ( não atualizado ).

`@test.association={novos_valores}` não será salvo.

#### class_name

> If the name of the other model cannot be derived from the association name, you can use the `:class_name` option to supply the model name. For example, if a book belongs to an author, but the actual name of the model containing authors is `Patron`, you'd set things up this way:

```ruby
class Book < ApplicationRecord 
  belongs_to :author, class_name: 'Patron'
end
```

#### counter_cache

  Uma forma eficiente de buscar uma quantidade de objetos associados de forma eficiente. Exemplo:

```ruby
class Book < ApplicationRecord 
  belongs_to :author
end

class Author < ApplicationRecord
  hes_many :books
end
```

> With these declarations, asking for the value of `@author.books.size` requires making a call to the database to perform a `COUNT(*)` query. To avoid this call, you can add a counter cache to the *belonging* model:

```ruby
class Book < ApplicationRecord 
  belongs_to :author, counter_cache: true
end

class Author < ApplicationRecord
  has_many :books
end
```

Com essa declaração a chamada do tamanho de associação `@author.books.size` não consultará diretamente no banco e sim por *counter cache* .

> Although the `:counter_cache` option is specified on the model that includes the `belongs_to` declaration, the actual column must be added to the *associated* (`has_many`) model. In the case above, you would need to add a column named `books_count` to the `Author` model.
>
> You can override the default column name by specifying a custom column name in the `counter_cache` declaration instead of `true`. For example, to use `count_of_books` instead of `books_count`:

```ruby
class Book < ApplicationRecord 
  belongs_to :author, counter_cache: :count_of_books  
end

class Author < ApplicationRecord 
  has_many :book
end
```

> Counter cache columns are added to the containing model's list of read-only attributes through `attr_readonly`.

#### dependent

Ao **dependent** podemos setar como *destroy* ou *delete*:

* :destroy => quando um objeto é destroído chamará seus objetos associados. 
* :delete => quando se usa essa cláusula todos os objetos associados serão removidos do banco. Diretamente.

> You should not specify this option on a **`belongs_to`** association that is connected with a `has_many` association on the other class. Doing so can lead to orphaned records in your database.

#### foreign_key

#### primary_key

#### inverse_of

#### polymorphic

#### touch 

#### validate

#### optional

### Scopes for belongs_to

[Documentação](https://guides.rubyonrails.org/association_basics.html#detailed-association-reference)

Podemos customizar estes hooks da seguinte forma usando query padronizadas:

* where
* includes
* readonly
* select

#### where

Deve confirmar associação quando a condição de busca for atendida:

```ruby
class Book < ApplicationRecord
	belongs_to :author, -> { where active: true}
end
```

Só será realizada o retorno da associações em que o author tiver active: true.

#### includes

#### readonly

#### select

## Tips e Tricks

### Controlling Caching

