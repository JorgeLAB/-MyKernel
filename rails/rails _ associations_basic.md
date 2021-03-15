# Rails - Associations Basics

## Self Joins

​	Podemos criar um model que poderá se relacionar o sigo mesmo. Um exemplo seria entre gerentes e subordinados, todos são empregados. Trataremos nosso modelo da seguinte forma: 

```ruby
class Employee < ApplicationRecord
	has_many :subordinates, class_name: "Employee", foreign_key: "manager_id"
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

Cria-se uma tabela manifests que podemos tratar de forma independente das demais tabelas. Como assim ? em um único lugar você poderá mapear todas as relações de assembly e part.

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

Pelos nomes declarados automaticamente o ActiveRecord reconhece a associação pelos nomes, sem precisar acrescentar mais nada. Ele irá trabalhar apenas com um cópia de dado em todo o processo de consulta: 

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

Com a alteração do atributo `first_name` não houve um mapeamento automático do ActiveRecord para reconhecer a mudança e propagá-la as outra variáveis. Mas há uma opção chamada `:inverse_of ` que realiza esse processo:

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

Em termos de banco de dados, belongs_to representa uma coluna na tabela do model que é uma referencia a outra tabela. Tanto poderá ser uma associação de um-para-muitos ou um-para-um, no caso de um-para-um a declação no outro model será `has_one`. Sendo assim exitem 6 métodos de associações `belongs_to` que podemos utilizar:

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

### Scopes for belongs_to

[Documentação](https://guides.rubyonrails.org/association_basics.html#detailed-association-reference)

Podemos customizar estes hooks da seguinte forma usando query padronizadas:

* where
* includes
* readonly
* select

**Where**

Deve confirmar associação quando a condição de busca for atendida:

```ruby
class Book < ApplicatioRecord
	belongs_to :author, -> { where active: true}
end
```

Só será realizada o retorno da associações em que o author tiver active: true.

 

