# RSpes

## Method Stubs

### Stub a chain of methods

Encadear métodos é considerado a uma má prática, mas não é todalmente verdade.

Quando um método encadeia-se isso é um problema, gera uma dependencia pois à cada método é retornado um objeto diferente.

`foo.get_bar.get_baz`

"Law of Demeter" - [Wiki](https://en.wikipedia.org/wiki/Law_of_Demeter) 

- Basicamente um objeto deve chamar um método que o pertença.

  ```ruby
  # Errado
  product.provider.name  
  provider.address.city
  company.building.city
  
  # Certo, nesse caso um objeto não é chamado diretamente
  # em outro.
  product.provider_name
  provider.address_city #or provider.city 
  company.city
  ```

#### stub a chain of methods

```ruby
describe 'stubbing a chain of methods' do
   subject { Object.new }
    
    context 'given symbols representing methods' do
        it 'returns the correct value' do
          subject.stub_chain(:one, :two, :three).and_return(:four)
          subject.one.two.three.should eq(:four)
        end
    end
    
    context 'given a hash at the end' do
    	it 'returns the correct value' do
          subject.stub_chain(:one, :two, :three => :four)
          subject.one.two.three.should eq(:four)
        end
    end
    
    context 'given a string of methods separated by dots' do
      it 'returns the correct value' do
        subject.stub_chain('one.two.three').and_return(:four)
        subject.one.two.three.should eq(:four)
      end
    end
end
```



### Stub on any instance of a class

#### any_instance stub with a single return value

```ruby
describe 'any_instance.stub' do
	it 'returns the specified value on any instance of the class'
       Object.any_instance.stub(:foo).and_return(:return_value) 
       
       o = Object.new 
       o.foo.should eq(:return_value )
    end
end
```

#### any_instance stub with a hash

```
describe 'any_instance.stub' do
  context 'with a hash' do
    it 'returns the hash values on any instance of class' do
      Object.any_instance.stub(:foo => 'foo', :bar => 'bar')
      
      o = Object.new
      o.foo.should eq('foo')
      o.bar.should eq('bar')
    end
  end
end
```

#### any-instance stub with specific arguments matchers

```ruby
describe 'any_instance.stub' do
  context 'with arguments' do
  	it 'returns the stubbed value when arguments match' do
  		Object.any_instance.stub(:foo).with(:param_one, :param_two).and_return(:result_one)
  		Object.any_instance.stub(:foo).with(:param_three,, :param_four).and_return(:result_two)
  		
  		o = Object.new
  		o.foo(:param_one, :param_two).should eq(:result_one)
  		o.foo(:param_three, :param_four).should eq(:result_two)
  	end
  end
end
```

#### any_instance unstub

```ruby
describe 'any_instance.unstub' do
  it 'unstubs a stubbed method' do
    class Object
      def foo
        :foo
      end
    end
      
    Object.any_instance.stub(:foo)
    Object.any_instance.unstub(:foo)
    Object.new.foo.should eq(:foo)
  end
end
```

#### any_instance unstub 

```ruby
describe 'any_instance.unstub' do
  context 'with both an expectation and a stub already set' do
    it 'only removes the stub' do
      class Object 
        def foo
          :foo
        end
      end
        
      Object.any_instance.should_receive(:foo).and_return(:bar)
      Object.any_instance.stub(:foo)
      Object.any_instance.unstub(:foo)
      
      Object.new.foo.should eq(:bar)
    end
  end
end
```

#### stub a chain of methods an  any instance 

```ruby
describe 'stubbing a chain of methods' do
  context 'given symbold representing methods' do
    it 'returns the correct value' do
      Object.any_instance.stub_chain(:one, :two, :three).and_return(:four)
      Object.new.one.two.three.should eq(:four)
    end
  end
    
  context 'given a hash at the end' do
    it 'returns the correct value' do
      Object.any_instance.stub_chain(:one, :two, :three => :four)
      Object.new.one.two.three.should eq(:four)
    end
  end
    
  context 'given a string of methods separated by dots' do
    it 'returns the correct value' do
      Object.any_instance.stub_chain('one.two.three').and_return(:four)
      Object.new.one.two.three.should eq(:four)
        
    end
  end
end
```



### As_null_object

O método `as_null_object` serve para ignorar qualquer mensagem que não seja explícita.

EXCEPTION: `to_ary` levanta um erro de `NoMethodError` se não for explicitado o stubbed com suporte a flatten em um array que contém um `double`.

#### double acting as_null_object

```ruby
describe 'a double with as_null_object called' do
  let(:null_object) { double('null object').as_null_object }
  
  it 'responds to any method that is not defined' do
    null_object.should respond_to(:an_undefined_method)
  end
    
  it 'allows explicit stubs using expect syntax' do
    allow(null_object).to receive(:foo) { 'bar' }
    expect(null_object.foo).to eq('bar')
  end
    
  it 'allows explicit stubs using should syntax' do
    null_object.stub(:foo) { 'bar' }
    null_object.foo.should eq( 'bar' )
  end
    
  it 'allows explicit expectations' do
    null_object.should_receive(:something)
    null_object.something
  end
    
  it 'supports Array#flatten' do
    [null_object].flatten.should eq([null_object])
  end
end
```

Basicamente o `as_null_object`  é um ser que pode fazer qualquer coisa não vejo uma aplicação real dele, a não ser que seja suporte mesmo, caso contrário é um teste que não condiz com a realidade.

### Double handling to_ary

O ruby implicitamente envia `to_ary`para todo objeto Array quando é recebido `flatten`.

```ruby
[obj].flatten # => obj.to_ary
```

#### double receiving to_ary

```ruby
describe '#to_ary' do
    shared_examples 'to_ary' do
      it 'can be overridden with a stub' do
        obj.stub(:to_ary) { :non_nil_value }
        obj.to_ary.should be( :non_nil_value )  
      end
      
      it 'supports Array#flatten' do
        obj = double('foo')
        [obj].flatten.should eq 
      end
    end
end
```

  