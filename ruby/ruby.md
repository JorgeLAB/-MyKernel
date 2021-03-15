# Compatilhando comportamentos

## Herança, Módulos e Mixins

>  O princípio DRY (Don’t Repeat Yourself) criado por Andy Hunt e Dave Thomas, no excelente livro “The Pragmatic Programmer”, propõe que cada pequena quantidade de código deve possuir somente uma representação em todo o sistema. 

>  Todo o objeto Ruby possui um conjunto de variáveis de instância, e estas variáveis não são definidas na classe, mas sim quando invocamos algum método que as cria, que na maioria dos casos acaba sendo o próprio método initialize, que é um simples método como outro qualquer.

> Pelo motivo de não serem definidas na classe, variáveis de instância não são herdadas por subclasses. Quando invocamos o método initialize da superclasse (através do método super) **uma variável de instância é criada no escopo onde foi chamado**.

> O primeiro ponto que devemos ressaltar é que Ruby não possui construtores. Existe um método initialize que é executado quando criamos um objeto ao invocar o método new em uma constante definida. Como Ruby possui apenas métodos, quando desejamos invocar o método initialize da superclasse, devemos invocá-lo através da palavra chave super. Porém existe uma pequena armadilha. Quando invocamos o initialize da superclasse apenas com **super** sem os parenteses, o interpretador Ruby **tentará invocá-lo passando os mesmos parâmetros recebidos pelo método initialize da subclasse:**

> Utilizamos herança geralmente para modelar classes nas quais desejamos **compartilhar comportamentos**, mas o que muita gente não sabe é que muitas vezes acabamos compartilhando implementação. Entre outras coisas, isso significa que não importa de quantas classes você herde, todos os métodos e o estado do seu objeto são definidos em um único namespace.
>

> O principal problema aqui, é que em Ruby **não existe o conceito de encapsulamento entre os objetos envolvidos no uso da herança.** O mesmo problema acontece com variáveis de instância (que por padrão são sempre privadas), que podem ser alteradas nas superclasses, e afetar algum outro comportamento das subclasses que utilizam as variáveis alteradas.
>
> 