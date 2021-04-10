# Active Records Validations

## Validations Overview 

```ruby
class Person < ApplicationRecord
	validates :name, presence: true
end
```

```ruby
irb> Person.create(name: 'teste').valid?
=> true
irb> Person.create(name: nil).valid?
=> false
```

Na primeira execução temos a `person` sendo persistida, já que, seu valid retornou `true` contudo a segunda opção já não teve este retorno, já que, seu atributo `name` recebeu um valor inválido.

**Validations são usados para ter certeza de que um dado válido seja salvo no banco.** 