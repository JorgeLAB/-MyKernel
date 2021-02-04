# Resumo Guia Foca

## Diretórios 

​	Primeiro detalhe é que nos sistemas Linux/Unix são especificados por um `/` e não uma `\` como é feito no **DOS**. 	 Podemos criar diretórios com **mkdir**.

## Diretório raiz

​	O diretório raiz é representado por `/` , usando-se o comando `cd \` você irá cair direto neste diretório.

Nele estão localizados outros diretórios :

- /
- /bin
- /sbin
- /usr
- /usr/local
- /tmp
- /var
- /home

A estrutura de diretórios também é chamada de **Árvore de Diretórios** porque é parecida com uma árvore de cabeça para baixo. Cada diretório do sistema tem seus respectivos arquivos que são armazenados conforme regras definidas pela **FHS (FileSystem Hierarchy Standard - Hierarquia Padrão do Sistema de Arquivos)** versão 2.0, definindo que tipo de arquivo deve ser armazenado em cada diretório.

### Diretório atual

​	É o diretório em que nos encontramos no momento. Você pode digitar `pwd` (veja [“pwd”](ch06s03.html)) para verificar qual é seu diretório atual.

​	O diretório atual também é identificado por um "." (ponto). O comando comando `ls .` pode ser usado para listar seus arquivos (é claro que isto é desnecessário porque se não digitar nenhum diretório, o comando **ls** listará o conteúdo do diretório atual).

### DIretório home 

Podemos chamar de diretórios de usuários que inclui o usuário `root`, onde cada usuário terá um diretórios onde armazenar seu arquivos. Podemos localiza em ` /home/nome_do_usuário` ou no caso do root `/root` . O diretório home também é identificado por `~` . 

Dependendo de sua configuração e do número de usuários em seu sistema, o diretório de usuário pode ter a seguinte forma: `/home/[1letra_do_nome]/[login]`, neste caso se o seu login for "joao" o seu diretório home será `/home/j/joao`.

### Diretório superior

Podemos indetificá-lo por ` ..`, isso é tudo pessoal. ( Upper Directory ).

### Nomeando arquivos e Diretórios

Os programas executáveis do **GNU/Linux**, ao contrário dos programas de **DOS** e **Windows**, não são executados a partir de extensões `.exe, .com` ou `.bat`. O **GNU/Linux** (como todos os sistemas POSIX) usa a *permissão de execução* de arquivo para identificar se um arquivo pode ou não ser executado.

### Comando Internos

São comandos que estão localizados dentro do interpretador de comandos e não no disco. Eles são carregados na memória RAM do computador junto com o interpretador de comandos. Quando se executa um comando o interpretador identidica primeiro se é um comando interno e caso não seja é verificado se é um comando externo.

Exemplos de comandos internos são: cd, exit, echo, bg, fg, source, help

### Path

Path é o caminho de procura dos arquivos/comandos executáveis. O path (caminho) é armazenado na variável de ambiente `PATH`. Você pode ver o conteúdo desta variável com o comando `echo $PATH`.

Por exemplo, o caminho `/usr/local/bin:/usr/bin:/bin:/usr/bin/X11` significa que se você digitar o comando `ls`, o interpretador de comandos iniciará a procura do programa **ls** no diretório `/usr/local/bin`, caso não encontre o arquivo no diretório `/usr/local/bin` ele inicia a procura em `/usr/bin`, até que encontre o arquivo procurado.

Caso o interpretador de comandos chegue até o último diretório do path e não encontre o arquivo/comando digitado, é mostrada a seguinte mensagem:

`bash: ls: command not found` (comando não encontrado).

O caminho de diretórios vem configurado na instalação do Linux, mas pode ser alterado no arquivo **`/etc/profile`**. Caso deseje alterar o caminho para todos os usuários, este arquivo é o melhor lugar, pois ele é lido por todos os usuários no momento do login.

Caso um arquivo/comando não esteja localizado em nenhum dos diretórios do path, você deve executa-lo usando um `./` na frente do comando.

Se deseja alterar o `path` para um único usuário, modifique o arquivo `.bash_profile` em seu diretório de usuário (home).

**OBSERVAÇÃO:** Por motivos de segurança, não inclua o diretório atual **$PWD** no `path`.

## Comandos de Prompt

## Aviso de comando (Prompt)

Aviso de comando (ou Prompt), é a linha mostrada na tela para *digitação de comandos* que serão passados ao `interpretador de comandos` para sua execução.

A posição onde o comando será digitado é marcado um "traço" piscante na tela chamado de *cursor*. Tanto em shells texto como em gráficos é necessário o uso do cursor para sabermos onde iniciar a digitação de textos e nos orientarmos quanto a posição na tela.

O aviso de comando do usuário `root` é identificado por uma "#" (tralha), e o aviso de comando de usuários é identificado pelo símbolo "$". Isto é padrão em sistemas **UNIX**.

Você pode retornar comandos já digitados pressionando as teclas `Seta para cima` / `Seta para baixo`.

A tela pode ser rolada para baixo ou para cima segurando a tecla `SHIFT` e pressionando `PGUP` ou `PGDOWN`. Isto é útil para ver textos que rolaram rapidamente para cima.

Abaixo algumas dicas sobre a edição da linha de comandos (não é necessário se preocupar em decora-los):

- **Pressione a tecla `Back Space` ("<--") para apagar um caracter à esquerda do cursor.**
- **Pressione a tecla `Del` para apagar o caracter acima do cursor.**
- **Pressione `CTRL`+`A` para mover o cursor para o inicio da linha de comandos.**
- **Pressione `CTRL`+`E` para mover o cursor para o fim da linha de comandos.**
- **Pressione `CTRL`+`U` para apagar o que estiver à esquerda do cursor. O conteúdo apagado é copiado para uso com `CTRL`+`y`.**
- **Pressione `CTRL`+`K` para apagar o que estiver à direita do cursor. O conteúdo apagado é copiado para uso com `CTRL`+`y`.**
- **Pressione `CTRL`+`L` para limpar a tela e manter o texto que estiver sendo digitado na linha de comando (parecido com o comando clear).**
- **Pressione `CTRL`+`Y` para colocar o texto que foi apagado na posição atual do cursor.**

## Interpretador de comando 

Também conhecido como "shell". É o programa responsável em **interpretar as instruções enviadas pelo usuário** e seus programas ao sistema operacional (o kernel). Ele que executa comandos lidos do dispositivo de entrada padrão (teclado) ou de um arquivo executável. É a principal ligação entre o usuário, os programas e o kernel. O **GNU/Linux** possui diversos tipos de interpretadores de comandos, entre eles posso destacar o **bash, ash, csh, tcsh, sh,** etc. Entre eles o mais usado é o **bash**. O interpretador de comandos do DOS, por exemplo, é o `command.com`.

Os comandos podem ser enviados de duas maneiras para o interpretador: `interativa` e `não-interativa`:

- `Interativa`

  Os comandos são digitados no aviso de comando e passados ao interpretador de comandos um a um. Neste modo, o computador depende do usuário para executar uma tarefa, ou próximo comando.

- `Não-interativa`

  São usados arquivos de comandos criados pelo usuário (scripts) para o computador executar os comandos na ordem encontrada no arquivo. Neste modo, o computador executa os comandos do arquivo um por um e dependendo do término do comando, o script pode checar qual será o próximo comando que será executado e dar continuidade ao processamento.Este sistema é útil quando temos que digitar por várias vezes seguidas um mesmo comando ou para compilar algum programa complexo.

## Coringas

Coringas (ou referência global) é um recurso usado para especificar um ou mais arquivos ou diretórios do sistema de uma só vez. Este é um recurso permite que você faça a filtragem do que será listado, copiado, apagado, etc. São usados 4 tipos de coringas no **GNU/Linux**:

- "*" - Faz referência a um nome completo/restante de um arquivo/diretório.

- "?" - Faz referência a uma letra naquela posição.

- `[padrão]` - Faz referência a uma faixa de caracteres de um arquivo/diretório. Padrão pode ser:

  - `[a-z][0-9]` - Faz referência a caracteres de `a` até `z` seguido de um caracter de `0` até `9`.
  - `[a,z][1,0]` - Faz a referência aos caracteres `a` e `z` seguido de um caracter `1` ou `0` naquela posição.
  - `[a-z,1,0]` - Faz referência a intervalo de caracteres de `a` até `z` ou `1` ou `0` naquela posição.

  A procura de caracteres é "Case Sensitive" assim se você deseja que sejam localizados todos os caracteres alfabéticos você deve usar `[a-zA-Z]`.

  Caso a expressão seja precedida por um `^`, faz referência a qualquer caracter exceto o da expressão. Por exemplo `[^abc]` faz referência a qualquer caracter exceto `a`, `b` e `c`.

- `{padrões}` - Expande e gera strings para pesquisa de padrões de um arquivo/diretório.

  - `X{ab,01}` - Faz referência a seqüencia de caracteres `Xab` ou `X01`
  - `X{a-z,10}` Faz referencia a seqüencia de caracteres X`a-z` e `X10`.

O que diferencia este método de expansão dos demais é que a existência do arquivo/diretório é opcional para geração do resultado. Isto é útil para a criação de diretórios. Lembrando que os 4 tipos de coringas ("*", "?", "[]", "{}") podem ser usados juntos.

### Exemplo de coringas

Para entender melhor vamos a prática:

Vamos dizer que tenha 5 arquivo no diretório `/usr/teste`: `teste1.txt, teste2.txt, teste3.txt, teste4.new, teste5.new`.

Caso deseje listar **todos** os arquivos do diretório `/usr/teste` você pode usar o coringa "*" para especificar todos os arquivos do diretório:

`cd /usr/teste` e `ls *` ou `ls /usr/teste/*`.

Não tem muito sentido usar o comando **ls** com "*" porque todos os arquivos serão listados se o **ls** for usado sem nenhum Coringa.

Agora para listar todos os arquivos `teste1.txt, teste2.txt, teste3.txt` com excessão de `teste4.new`, `teste5.new`, podemos usar inicialmente 3 métodos:

1. Usando o comando `ls *.txt` que pega todos os arquivos que começam com qualquer nome e terminam com `.txt`.
2. Usando o comando `ls teste?.txt`, que pega todos os arquivos que começam com o nome `teste`, tenham qualquer caracter no lugar do coringa `?` e terminem com `.txt`. Com o exemplo acima `teste*.txt` também faria a mesma coisa, mas se também tivéssemos um arquivo chamado `teste10.txt` este também seria listado.
3. Usando o comando `ls teste[1-3].txt`, que pega todos os arquivos que começam com o nome `teste`, tenham qualquer caracter entre o número 1-3 no lugar da 6a letra e terminem com `.txt`. Neste caso se obtém uma filtragem mais exata, pois o coringa *?* especifica qualquer caracter naquela posição e [] especifica números, letras ou intervalo que será usado.

Agora para listar somente `teste4.new` e `teste5.new` podemos usar os seguintes métodos:

1. `ls *.new` que lista todos os arquivos que terminam com `.new`
2. `ls teste?.new` que lista todos os arquivos que começam com `teste`, contenham qualquer caracter na posição do coringa *?* e terminem com `.new`.
3. `ls teste[4,5].*` que lista todos os arquivos que começam com `teste` contenham números de 4 e 5 naquela posição e terminem com qualquer extensão.

Existem muitas outras formas de se fazer a mesma coisa, isto depende do gosto de cada um. O que pretendi fazer aqui foi mostrar como especificar mais de um arquivo de uma só vez. O uso de coringas será útil ao copiar arquivos, apagar, mover, renomear, e nas mais diversas partes do sistema. Alias esta é uma característica do **GNU/Linux**: permitir que a mesma coisa possa ser feita com liberdade de várias maneiras diferentes.

### Tipos de Permissões de acesso

Quanto aos tipos de permissões que se aplicam ao *dono*, *grupo* e *outros usuários*, temos 3 permissões básicas:

- `r` - Permissão de leitura para arquivos. Caso for um diretório, permite listar seu conteúdo (através do comando **ls**, por exemplo).

- `w` - Permissão de gravação para arquivos. Caso for um diretório, permite a gravação de arquivos ou outros diretórios dentro dele.

  Para que um arquivo/diretório possa ser apagado, é necessário o acesso a gravação.

- `x` - Permite executar um arquivo (caso seja um programa executável). Caso seja um diretório, permite que seja acessado através do comando **cd** (veja [“cd”](ch06s02.html) para detalhes).

As permissões de acesso a um arquivo/diretório podem ser visualizadas com o uso do comando `ls -la`. Para maiores detalhes veja [“ls”](ch06.html#comando-ls). As 3 letras (rwx) são agrupadas da seguinte forma:

```
-rwxr-xr--   gleydson   users  teste
```

Virou uma bagunça não? Vou explicar cada parte para entender o que quer dizer as 10 letras acima (da esquerda para a direita):

- A primeira letra diz qual é o tipo do arquivo. Caso tiver um "d" é um diretório, um "l" um link a um arquivo no sistema (veja [“ln”](ch08s04.html) para detalhes) , um "-" quer dizer que é um arquivo comum, etc.
- Da segunda a quarta letra (rwx) dizem qual é a permissão de acesso ao *dono* do arquivo. Neste caso *gleydson* ele tem a permissão de ler (r - read), gravar (w - write) e executar (x - execute) o arquivo `teste`.
- Da quinta a sétima letra (r-x) diz qual é a permissão de acesso ao *grupo* do arquivo. Neste caso todos os usuários que pertencem ao grupo *users* tem a permissão de ler (r), e também executar (x) o arquivo `teste`.
- Da oitava a décima letra (r--) diz qual é a permissão de acesso para os *outros usuários*. Neste caso todos os usuários que não são donos do arquivo `teste` tem a permissão somente para ler o programa.

### Chmod

Muda a permissão de acesso a um arquivo ou diretório. Com este comando você pode escolher se usuário ou grupo terá permissões para ler, gravar, executar um arquivo ou arquivos. Sempre que um arquivo é criado, seu dono é o usuário que o criou e seu grupo é o grupo do usuário (exceto para diretórios configurados com a permissão de grupo `"s"`, será visto adiante).

**chmod** [*opções*] [*permissões*] [*diretório/arquivo*]

Onde:

- *diretório/arquivo*

  Diretório ou arquivo que terá sua permissão mudada.

- *opções*, -v, --verbose

  Mostra todos os arquivos que estão sendo processados.

- -f, --silent

  Não mostra a maior parte das mensagens de erro.

- -c, --change

  Semelhante a opção -v, mas só mostra os arquivos que tiveram as permissões alteradas.

- -R, --recursive

  Muda permissões de acesso do *diretório/arquivo* no diretório atual e sub-diretórios.

- ugoa+-=rwxXst

  *ugoa* - Controla que nível de acesso será mudado. Especificam, em ordem, usuário (u), grupo (g), outros (o), todos (a).*+-=* - *+* coloca a permissão, *-* retira a permissão do arquivo e *=* define a permissão exatamente como especificado.rwx - *r* permissão de leitura do arquivo. *w* permissão de gravação. *x* permissão de execução (ou acesso a diretórios).

**chmod** não muda permissões de links simbólicos, as permissões devem ser mudadas no arquivo alvo do link. Também podem ser usados códigos numéricos octais para a mudança das permissões de acesso a arquivos/diretórios. Modo de permissão octal.

DICA: É possível copiar permissões de acesso do arquivo/diretório, por exemplo, se o arquivo `teste.txt` tiver a permissão de acesso `r-xr-----` e você digitar `chmod o=u`, as permissões de acesso dos outros usuários (o) serão idênticas ao do dono (u). Então a nova permissão de acesso do arquivo `teste.txt` será `r-xr--r-x`

Exemplos de permissões de acesso:

- `chmod g+r *`

  Permite que todos os usuários que pertençam ao grupo dos arquivos (g) tenham (+) permissões de leitura (r) em todos os arquivos do diretório atual.

- `chmod o-r teste.txt`

  Retira (-) a permissão de leitura (r) do arquivo `teste.txt` para os outros usuários (usuários que não são donos e não pertencem ao grupo do arquivo `teste.txt`).

- `chmod uo+x teste.txt`

  Inclui (+) a permissão de execução do arquivo `teste.txt` para o dono e outros usuários do arquivo.

- `chmod a+x teste.txt`

  Inclui (+) a permissão de execução do arquivo `teste.txt` para o dono, grupo e outros usuários.

- `chmod a=rw teste.txt`

  Define a permissão de todos os usuários exatamente (=) para leitura e gravação do arquivo `teste.txt`.

### Tipo de execução de comandos

Um programa pode ser executado de duas formas:

1. `Primeiro Plano` - Também chamado de *foreground*. Quando você deve esperar o término da execução de um programa para executar um novo comando. Somente é mostrado o aviso de comando após o término de execução do comando/programa.

2. `Segundo Plano` - Também chamado de *background*. Quando você não precisa esperar o término da execução de um programa para executar um novo comando. Após iniciar um programa em *background*, é mostrado um número PID (identificação do Processo) e o aviso de comando é novamente mostrado, permitindo o uso normal do sistema.

   O programa executado em background continua sendo executado internamente. Após ser concluído, o sistema retorna uma mensagem de pronto acompanhado do número PID do processo que terminou.

Para iniciar um programa em `primeiro plano`, basta digitar seu nome normalmente. Para iniciar um programa em `segundo plano`, acrescente o caracter `"&"` após o final do comando.

OBS: Mesmo que um usuário execute um programa em segundo plano e saia do sistema, o programa continuará sendo executado até que seja concluído ou finalizado pelo usuário que iniciou a execução (ou pelo usuário root).

Exemplo: `find / -name boot.b &`

O comando será executado em segundo plano e deixará o sistema livre para outras tarefas. Após o comando **find** terminar, será mostrada uma mensagem.

Os comandos podem ser executados em seqüência (um após o término do outro) se os separarmos com ";". Por exemplo: `echo primeiro;echo segundo;echo terceiro`. "Eu usava o pipe para isto. Parece ter mesma função que `&&`"

### Pipe

Envia a saída de um comando para a entrada do próximo comando para continuidade do processamento. Os dados enviados são processados pelo próximo comando que mostrará o resultado do processamento.

Por exemplo: `ls -la | more`, este comando faz a listagem longa de arquivos que é enviado ao comando **more** (que tem a função de efetuar uma pausa a cada 25 linhas do arquivo).

Outro exemplo é o comando `locate find | grep "bin/"`, neste comando todos os caminhos/arquivos que contém *find* na listagem serão mostrados (inclusive man pages, bibliotecas, etc.), então enviamos a saída deste comando para `grep "bin/"` para mostrar somente os diretórios que contém binários. Mesmo assim a listagem ocupe mais de uma tela, podemos acrescentar o **more**: `locate find | grep "bin/" | more`.

Podem ser usados mais de um comando de redirecionamento (<, >, |) em um mesmo comando.

### ps

Algumas vezes é útil ver quais processos estão sendo executados no computador. O comando **ps** faz isto, e também nos mostra qual usuário executou o programa, hora que o processo foi iniciado, etc.

### top

Mostra os programas em execução ativos, parados, tempo usado na CPU, detalhes sobre o uso da memória RAM, Swap, disponibilidade para execução de programas no sistema, etc.

**top** é um programa que continua em execução mostrando continuamente os processos que estão rodando em seu computador e os recursos utilizados por eles. Para sair do **top**, pressione a tecla `q`.

**top** [*opções*]

Onde:

- -d [tempo]

  Atualiza a tela após o [tempo] (em segundos).

- -s

  Diz ao **top** para ser executado em modo seguro.

- -i

  Inicia o **top** ignorando o tempo de processos zumbis.

- -c

  Mostra a linha de comando ao invés do nome do programa.

A ajuda sobre o **top** pode ser obtida dentro do programa pressionando a tecla `h` ou pela página de manual (`man top`).

Abaixo algumas teclas úteis:

- `espaço` - Atualiza imediatamente a tela.
- `CTRL`+`L` - Apaga e atualiza a tela.
- `h` - Mostra a tela de ajuda do programa. É mostrado todas as teclas que podem ser usadas com o **top**.
- `i` - Ignora o tempo ocioso de processos zumbis.
- `q` - Sai do programa.
- `k` - Finaliza um processo - semelhante ao comando **kill**. Você será perguntado pelo número de identificação do processo (PID). Este comando não estará disponível caso esteja usando o **top** com a opção `-s`.
- `n` - Muda o número de linhas mostradas na tela. Se 0 for especificado, será usada toda a tela para listagem de processos.

### Fechando um programa quando não se sabe como sair

Muitas vezes quando se esta iniciando no **GNU/Linux** você pode executar um programa e talvez não saiba como fecha-lo. 

Isto pode também ocorrer com programadores que estão construindo seus programas e por algum motivo não implementam uma opção de saída, ou ela não funciona!

Em nosso exemplo vou supor que executamos um programa em desenvolvimento com o nome **contagem** que conta o tempo em segundos a partir do momento que é executado, mas que o programador esqueceu de colocar uma opção de saída. Siga estas dicas para finaliza-lo:

1. Normalmente todos os programas **UNIX** (o **GNU/Linux** também é um Sistema Operacional baseado no **UNIX**) podem ser interrompidos com o pressionamento das teclas `<CTRL>` e `<C>`. Tente isto primeiro para finalizar um programa. Isto provavelmente não vai funcionar se estiver usando um Editor de Texto (ele vai entender como um comando de menu). Isto normalmente funciona para comandos que são executados e terminados sem a intervenção do usuário.

   Caso isto não der certo, vamos partir para a força! ;-)

2. Mude para um novo console (pressionando `<ALT>` e `<F2>`), e faça o *login* como usuário **root**.

3. Localize o PID (número de identificação do processo) usando o comando: `ps ax`, aparecerão várias linhas cada uma com o número do processo na primeira coluna, e a linha de comando do programa na última coluna. Caso aparecerem vários processos você pode usar `ps ax|grep contagem`, neste caso o **grep** fará uma filtragem da saída do comando `ps ax` mostrando somente as linhas que tem a palavra "contagem". Para maiores detalhes, veja o comando [“grep”](ch08s08.html).

4. Feche o processo usando o comando **kill** *PID*, lembre-se de substituir PID pelo número encontrado pelo comando **ps ax** acima.

   O comando acima envia um sinal de término de execução para o processo (neste caso o programa **contagem**). O sinal de término mantém a chance do programa salvar seus dados ou apagar os arquivos temporários que criou e então ser finalizado, isto depende do programa.

5. Alterne para o console onde estava executando o programa **contagem** e verifique se ele ainda está em execução. Se ele estiver parado mas o aviso de comando não está disponível, pressione a tecla <ENTER>. Freqüentemente acontece isto com o comando **kill**, você finaliza um programa mas o aviso de comando não é mostrado até que se pressione <ENTER>.

6. Caso o programa ainda não foi finalizado, repita o comando **kill** usando a opção -9: `kill -9 PID`. Este comando envia um sinal de DESTRUIÇÃO do processo, fazendo ele terminar "na marra"!

Uma última dica: todos os programas estáveis (todos que acompanham as boas distribuições **GNU/Linux**) tem sua opção de saída. Lembre-se que quando finaliza um processo todos os dados do programa em execução podem ser perdidos (principalmente se estiver em um editor de textos), mesmo usando o **kill** sem o parâmetro `-9`.

### cat

Mostra o conteúdo de um arquivo binário ou texto.

**cat** [opções] [*diretório/arquivo*] [*diretório1/arquivo1*]

- *diretório/arquivo*

  Localização do arquivo que deseja visualizar o conteúdo.

- ***opções*, -n, --number** ( destaque a este que eu não conhecia )

  Mostra o número das linhas enquanto o conteúdo do arquivo é mostrado.

- -s, --squeeze-blank

  Não mostra mais que uma linha em branco entre um parágrafo e outro.

- -

  Lê a entrada padrão.

O comando **cat** trabalha com arquivos texto. Use o comando **zcat** para ver diretamente arquivos compactados com **gzip**.

Exemplo: `cat /usr/doc/copyright/GPL`

### tac

Mostra o conteúdo de um arquivo binário ou texto (como o **cat**) só que em ordem inversa. Literalmente

### date
Permite ver/modificar a Data e Hora do Sistema. Você precisa estar como usuário root para modificar a data e hora. Muitos programas do sistema, arquivos de registro (log) e tarefas agendadas funcionam com base na data e hora fornecidas pelo sistema, assim esteja consciente das modificações que a data/hora pode trazer a estes programas (principalmente em se tratando de uma rede com muitos usuários).

date MesDiaHoraMinuto[AnoSegundos]

Onde:

MesDiaHoraMinuto[AnoSegundos]
São respectivamente os números do mês, dia, hora e minutos sem espaços. Opcionalmente você pode especificar o Ano (com 2 ou 4 dígitos) e os Segundos.

+[FORMATO]
Define o formato da listagem que será usada pelo comando date. Os seguintes formatos são os mais usados:

%d - Dia do Mês (00-31).

%m - Mês do Ano (00-12).

%y - Ano (dois dígitos).

%Y - Ano (quatro dígitos).

%H - Hora (00-24).

%I - Hora (00-12).

%M - Minuto (00-59).

%j - Dia do ano (1-366).

%p - AM/PM (útil se utilizado com %d).

%r - Formato de 12 horas completo (hh:mm:ss AM/PM).

%T - Formato de 24 horas completo (hh:mm:ss).

%w - Dia da semana (0-6).

Outros formatos podem ser obtidos através da página de manual do date.

Para maiores detalhes, veja a página de manual do comando date.

Para ver a data atual digite: date

Se quiser mudar a Data para 25/12 e a hora para 08:15 digite: date 12250815

Para mostrar somente a data no formato dia/mês/ano: date +%d/%m/%Y

### ln

Cria links para arquivos e diretórios no sistema. O link é um mecanismo que faz referência a outro arquivo ou diretório em outra localização. O link em sistemas **GNU/Linux** faz referência reais ao arquivo/diretório podendo ser feita cópia do link (será copiado o arquivo alvo), entrar no diretório (caso o link faça referência a um diretório), etc.

**ln** [*opções*] [*origem*] [*link*]

Onde:

- *origem*

  Diretório ou arquivo de onde será feito o link.

- *link*

  Nome do link que será criado.

- *opções*, -s

  Cria um link simbólico. Usado para criar ligações com o arquivo/diretório de destino.

- -v

  Mostra o nome de cada arquivo antes de fazer o link.

- -d

  Cria um hard link para diretórios. Somente o root pode usar esta opção.

Existem 2 tipos de links: *simbólicos* e *hardlinks*.

- O *link simbólico* cria um arquivo especial no disco (do tipo link) que tem como conteúdo o caminho para chegar até o arquivo alvo (isto pode ser verificado pelo tamanho do arquivo do link). Use a opção `-s` para criar links simbólicos.

- O *hardlink* faz referência ao mesmo inodo do arquivo original, desta forma ele será perfeitamente idêntico, inclusive nas permissões de acesso, ao arquivo original.

  Ao contrário dos links simbólicos, não é possível fazer um hardlink para um diretório ou fazer referência a arquivos que estejam em partições diferentes.

Observações:

- Se for usado o comando **rm** com um link, somente o link será removido.
- Se for usado o comando **cp** com um link, o arquivo original será copiado ao invés do link.
- Se for usado o comando **mv** com um link, a modificação será feita no link.
- Se for usado um comando de visualização (como o **cat**), o arquivo original será visualizado.

Exemplos:

- `ln -s /dev/ttyS1 /dev/modem` - Cria o link `/dev/modem` para o arquivo `/dev/ttyS1`.
- `ln -s /tmp ~/tmp` - Cria um link `~/tmp` para o diretório `/tmp`.

### free

Mostra detalhes sobre a utilização da memória RAM do sistema.

**free** [*opções*]

Onde:

- *opções*, -b

  Mostra o resultado em bytes.

- -k

  Mostra o resultado em Kbytes.

- -m

  Mostra o resultado em Mbytes.

- -o

  Oculta a linha de buffers.

- -t

  Mostra uma linha contendo o total.

- -s [num]

  Mostra a utilização da memória a cada [num] segundos.

O **free** é uma interface ao arquivo `/proc/meminfo`.

### grep

Procura por um texto dentro de um arquivo(s) ou no dispositivo de entrada padrão.

**grep** [*expressão*] [*arquivo*] [*opções*]

Onde:

- *expressão*

  palavra ou frase que será procurada no texto. Se tiver mais de 2 palavras você deve identifica-la com aspas "" caso contrário o **grep** assumirá que a segunda palavra é o arquivo!

- *arquivo*

  Arquivo onde será feita a procura.

- *opções*, -A [número]

  Mostra o [número] de linhas após a linha encontrada pelo **grep**.

- -B [número]

  Mostra o [número] de linhas antes da linha encontrada pelo **grep**.

- -f [arquivo]

  Especifica que o texto que será localizado, esta no arquivo [arquivo].

- -h, --no-filename

  Não mostra os nomes dos arquivos durante a procura.

- -i, --ignore-case

  Ignora diferença entre maiúsculas e minúsculas no texto procurado e arquivo.

- -n, --line-number

  Mostra o nome de cada linha encontrada pelo **grep**.

- -E

  Ativa o uso de expressões regulares.

- -U, --binary

  Trata o arquivo que será procurado como binário.

Se não for especificado o nome de um arquivo ou se for usado um hífen "-", **grep** procurará a string no dispositivo de entrada padrão. O **grep** faz sua pesquisa em arquivos texto. Use o comando **zgrep** para pesquisar diretamente em arquivos compactados com **gzip**, os comandos e opções são as mesmas.

Exemplos: `grep "capitulo" texto.txt`, `ps ax|grep inetd`, `grep "capitulo" texto.txt -A 2 -B 2`.

### time

Mede o tempo gasto para executar um processo (programa).

**time** [*comando*]

Onde: *comando* é o comando/programa que deseja medir o tempo gasto para ser concluído.

Exemplo: `time ls`, `time find / -name crontab`.

### uptime

Mostra o tempo de execução do sistema desde que o computador foi ligado. Esse é bem bacana.

### su
Permite o usuário mudar sua identidade para outro usuário sem fazer o logout. Útil para executar um programa ou comando como root sem ter que abandonar a seção atual.

su [usuário] [-c comando]

Onde: usuário é o nome do usuário que deseja usar para acessar o sistema. Se não digitado, é assumido o usuário root. Caso seja especificado -c comando, executa o comando sob o usuário especificado.

Será pedida a senha do superusuário para autenticação. Digite exit quando desejar retornar a identificação de usuário anterior.

## wc

Conta o número de palavras, bytes e linhas em um arquivo ou entrada padrão. Se as opções forem omitidas, o **wc** mostra a quantidade de linhas, palavras, e bytes.

**wc** [*opções*] [*arquivo*]

Onde:

- *arquivo*

  Arquivo que será verificado pelo comando **wc**.

- *opções*, -c, --bytes

  Mostra os bytes do arquivo.

- -w, --words

  Mostra a quantidade de palavras do arquivo.

- -l, --lines

  Mostra a quantidade de linhas do arquivo.

A ordem da listagem dos parâmetros é única, e modificando a posição das opções não modifica a ordem que os parâmetros são listados.

Exemplo:

- `wc /etc/passwd` - Mostra a quantidade de linhas, palavras e letras (bytes) no arquivo `/etc/passwd`.
- `wc -w /etc/passwd` - Mostra a quantidade de palavras.
- `wc -l /etc/passwd` - Mostra a quantidade de linhas.
- `wc -l -w /etc/passwd` - Mostra a quantidade de linhas e palavras no arquivo `/etc/passwd`.



### wc

Conta o número de palavras, bytes e linhas em um arquivo ou entrada padrão. Se as opções forem omitidas, o wc mostra a quantidade de linhas, palavras, e bytes.

wc [opções] [arquivo]

Onde:

arquivo
Arquivo que será verificado pelo comando wc.

opções, -c, --bytes
Mostra os bytes do arquivo.

-w, --words
Mostra a quantidade de palavras do arquivo.

-l, --lines
Mostra a quantidade de linhas do arquivo.

A ordem da listagem dos parâmetros é única, e modificando a posição das opções não modifica a ordem que os parâmetros são listados.

Exemplo:

wc /etc/passwd - Mostra a quantidade de linhas, palavras e letras (bytes) no arquivo /etc/passwd.

wc -w /etc/passwd - Mostra a quantidade de palavras.

wc -l /etc/passwd - Mostra a quantidade de linhas.

wc -l -w /etc/passwd - Mostra a quantidade de linhas e palavras no arquivo /etc/passwd.

### seq

Imprime uma seqüência de números começando em [primeiro] e terminando em [último], utilizando [incremento] para avançar.

**seq** [*opções*] [*primeiro*] [*incremento*] [*último*]

Onde:

- *primeiro*

  Número inicial da seqüência.

- *incremento*

  Número utilizado para avançar na seqüência.

- *último*

  Número final da seqüência.

- *opções*, -f, --format=[formato]

  Formato de saída dos números da seqüência. Utilize o estilo do printf para ponto flutuante (valor padrão: %g).

- -s, --separator=[string]

  Usa [string] para separar a seqüência de números (valor padrão: \n).

- -w, --equal-width

  Insere zeros na frente dos números mantendo a seqüência alinhada.

Observações:

- Se [primeiro] ou [incremento] forem omitidos, o valor padrão 1 será utilizado.
- Os números recebidos são interpretados como números em ponto flutuante.
- [incremento] deve ser positivo se [primeiro] for menor do que o último, e negativo caso contrário.
- Quando utilizarmos a opção --format, o argumento deve ser exatamente %e, %f ou %g.

Exemplos: `seq 0 2 10`, `seq -w 0 10`, `seq -f%f 0 10`, `seq -s", " 0 10`

### cmp

Compara dois arquivos de qualquer tipo (binário ou texto). Os dois arquivos especificados serão comparado e caso exista diferença entre eles, é mostrado o número da linha e byte onde ocorreu a primeira diferença na saída padrão (tela) e o programa retorna o código de saída 1.

**cmp** [*arquivo1*] [*arquivo2*] [*opções*]

Opções:

- *arquivo1/arquivo2*

  Arquivos que serão comparados.

- *opções*, -l

  Mostra o número do byte (hexadecimal) e valores diferentes de bytes (octal) para cada diferença.

- -s

  Não mostra nenhuma diferença, só retorna o código de saída do programa.

Use o comando **zcmp** para comparar diretamente arquivos binários/texto compactados com **gzip**.

Exemplo: `cmp teste.txt teste1.txt`.

### diff

Compara dois arquivos e mostra as diferenças entre eles. O comando **diff** é usado somente para a comparação de arquivos em formato texto. As diferenças encontradas podem ser redirecionadas para um arquivo que poderá ser usado pelo comando **patch** para aplicar as alterações em um arquivo que não contém as diferenças. Isto é útil para grandes textos porque é possível copiar somente as modificações (geradas através do diff, que são muito pequenas) e aplicar no arquivo para atualiza-lo (através do **patch**) ao invés de copiar a nova versão. Este é um sistema de atualização muito usado na atualização dos código fonte do kernel do **Linux**.

**diff** [*diretório1/arquivo1*] [*diretório2/arquivo2*] [*opções*]

### who

Mostra quem está atualmente conectado no computador. Este comando lista os nomes de usuários que estão conectados em seu computador, o terminal e data da conexão.

**who** [*opções*]

onde:

- *opções*, -H, --heading

  Mostra o cabeçalho das colunas.

- -b, --boot

  Mostra o horário do último boot do sistema.

- -d, --dead

  Mostra processos mortos no sistema.

- -i, -u, --idle

  Mostra o tempo que o usuário está parado em Horas:Minutos.

- -m, i am

  Mostra o nome do computador e usuário associado ao nome. É equivalente a digitar `who i am` ou `who am i`.

- -q, --count

  Mostra o total de usuários conectados aos terminais.

- -r, --runlevel

  Mostra o nível de execução atual do sistema e desde quando ele está ativo.

- -T, -w, --mesg

  Mostra se o usuário pode receber mensagens via **talk** (conversação).+ O usuário recebe mensagens via talk- O usuário não recebe mensagens via talk.? Não foi possível determinar o dispositivo de terminal onde o usuário está conectado.

## telnet

Permite acesso a um computador remoto. É mostrada uma tela de acesso correspondente ao computador local onde deve ser feita a autenticação do usuário para entrar no sistema. Muito útil, mas deve ser tomado cuidados ao disponibilizar este serviço para evitar riscos de segurança e usado o **ssh** sempre que possível por ser um protocolo criptografado e com recursos avançados de segurança.

**telnet** [*opções*] [*ip/dns*] [*porta*]

onde:

- *ip/dns*

  Endereço IP do computador de destino ou nome DNS.

- *porta*

  Porta onde será feita a conexão. Por padrão, a conexão é feita na porta *23*.

- *opções*

  -8Requisita uma operação binária de 8 bits. Isto força a operação em modo binário para envio e recebimento. Por padrão, **telnet** não usa 8 bits.-aTenta um login automático, enviando o nome do usuário lido da variável de ambiente `USER`.-dAtiva o modo de debug.-rAtiva a emulação de rlogin.-l [usuário]Faz a conexão usando [usuário] como nome de usuário.

Exemplo: `telnet 192.168.1.1`, `telnet 192.168.1.1 23`.

### dnsdomainname

Mostra o nome do domínio de seu sistema.