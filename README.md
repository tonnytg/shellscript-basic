

- Variaveis
- PATH
- SHELL
- Sub-Shell
- Permissão de Execução
- If
- Case
For
While
Functions
Args e Argv
Stdin, Stdout, Sterr
Jobs
Background
Foreground

### Variáveis
São locais que guardam informações, que podem ser alterados a qualquer momento, com isso o termo Variaveis. Para declarar uma váriavel no Shell, você deve colocar `NOME="STRING"` perceba que o `NOME` é colado no `=`

Existem diversas variaveis no seu Linux, que faciltam as coisas, por exemplo, quando você executa o comando `cd ~` você na verdade está  digitando `cd $HOME` 

`$HOME` a variável armazena a home do usuário que foi extraído do /etc/passwd.

você quer saber onde você está? Basta digitar `echo $PWD` com isso você terá o mesmo resultado que digitar o comando `pwd`

Normalmente utilizamos variáveis para reaproveitamento das informações.

Exercícios:
- Crie duas variaveis, uma com seu nome e outra com sua idade.
- Imprima "Olá eu sou $NAME e tenho $IDADE anos"


### PATH

Nós utilizamos até aqui alguns comandos para conseguir realizar a primeira tarefa, por exemplo o comando `echo` que imprime informações na sua tela. Esse comando é um binário que foi executado, e esse binário fica em algum lugar, para saber o lugar use outro comando `which echo` você vai ter um retorno com o caminho completo de onde ele está guardado, `/bin/echo`

Agora que já sabemos sobre variáveis, vamos imprimir a variável PATH que armazena o caminho de todos os binários habilitados para serem executados de qualquer lugar do seu sistema.

`echo $PATH` rodando esse comando você deve ter esse retorno:
`/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/go/bin:/Library/Apple/usr/bin`

Perceba que cada caminho que armazena diversos binários é separado por `:` então podemos falar que o delimitador é os `:` e com isso você pode usar ele para separar as coisas por exemplo `echo $PATH | cut -d":" -f1`


### Shell

Agora que sabemos um pouquinho, é importante entender que grande parte dos comandos do terminal estão sendo interpretadas pelo nosso `SHELL`

O SHELL pode ter alguns interpretadores, `sh`, `bash` e `zsh`
para saber qual shell você está usando basta imprimir a variável `echo $SHELL` provavelmente você vai ter um retonro de: `/bin/bash`

O Shell trabalha com níveis, nossa primeira sessão é o level 1, vamos checar: `echo $SHLVL`
Chame um novo Shell, digite `bash` e rode novamente `echo $SHLVL`

### Sub-Shell

Quando precisamos rodar um comando e armazenar a informação em algum lugar, nós chamos um Shell temporario que tem o objetivo de executar aquela ação e depois morrer.

```
AGORA=$(date)
echo $AGORA
Mon Jul 19 19:07:12 -03 2021
```

Perceba que o comando `date` foi executado e armazenado dentro da variável chamada `AGORA` com isso você consegue realizar reaproveitamento de código e facilitar muito a sua vida na hora de criar um script. Você pode colocar dentro do seu script várias vezes uma variável e alimentar ela somente uma vez no inicio do seu script e consequentemente ele vai refletir em todo seu script.


### Permissão de execução

Todos os comando do Linux, executados no terminal tem um propósito muito especifico e o tempo de vida é muito rápido, ele nasce e morre, guspindo na tela o que você queria.

Mas quando a gente precisa lapidar as informações e ter algo mais estruturado, a gente recorre a scripts, que são lidos linha por linha, mas para que isso aconteça o ideal é que ele tenha permissão de execução.

você pode executar forçando o `bash` para interpretar linha por linha daquele arquivo, ou você pode dar permissão de execução: `chmod +x nomeScript.sh`

Importante entender que dar permissão de execução, não resolve todos os problemas, o script na primeira linha tem que ter a referencia de qual será o interpretador.

Por exemplo, se você fosse criar um arquivo com comandos em Python, você teria que colocar a primeira linha do script como `#!/bin/python`
mas infelizmente o python pode ficar em qualquer lugar no seu Linux, podendo dar erro por não encontrar nada lá dentro de /bin com nome de python... para resolver isso, recomendo usar o `env`
`#!/bin/env python`

Lembra do PATH? O `/bin/env` vai procurar pelo python e interpretar as linhas com ele. Mas não estamos falando de Python, vamos voltar para o `bash` que é nosso interpretador de comandos.

##### Tarefa
- Descubra onde o Bash está: `which bash`
- Crie um arquivo script.sh
- Deste arquivo crie duas linhas, sendo a primeira `#!/bin/bash` e a segunda com `date`
- Salve o arquivo, saia dele
- De permissão de execução `chmod +x script.sh`
- Execute ele `./script.sh`


##### Tarefa2

Sabendo que o `env` encontra o binário no seu $PATH será que ele consegue achar também o bash? Faça o teste, coloque na primeira linha do seu script e veja o resultado.

### If

O ShellScript não é considerado uma linguagem de programação, mas se você explorar muito bem todos os recurso, estrutura, organização do código, funções, com certeza você estará muito perto disso.

Para termos decisões dentro do ShellScript, vamos precisar utilizar estruturas de controle, mas antes precisamos entender um comando chamado `test` e uma variável `$?`

Esses dois são as peças fundamentais para nos ajudar nesse desafio para criar um ShellScript que faça o que precisamos.

Perceba que todos os comandos que executamos até aqui, ou deu certo, ou deu errado. E quando deu errado ele falou alguma mensagem de erro, nos afirmando que aconteceu algum erro.

Por exemplo, digite `ls /meu/nome/pasta` 
Obvio que o retorno desse comando será um erro, pois não existe essa pasta, com isso você pode checar o que está guardado dentro do `$?`

`echo $?`

Provavelmente é um valor diferente de `0`

Zero representa que está tudo ok, quer ver? Digite `ls /bin/ls`
Você vai listar um executável que existe, nesse caso consulte o `echo $?`

```
1     = Catchall for general errors
2     = Misuse of shell builtins
126   = Command invoked cannot execute
127   = "command not found"
128   = "Invalid Exit"
128+n = "Fatal error signal "n"
130   = Script terminated by Control+c
255*  = Exit status out of range
```
Se tiver curiosidade depois de uma olhada nesses códigos, mas por hora só precisamos saber que diferente de 0 tem algo errado :)


Agora vamos usar o comando `test`
O comando test serve para comparação, retornando `0` ou `1`
Ele pode comparar Inteiros(Números) e Strings(Texto) porém existe uma diferença na hora de comparar eles...

Para Número a gente tem que usar algumas siglas:

```
1 -eq 1 -> representa igual a, nesse caso retorna verdadeiro
1 -gt 2 -> representa maior que, nesse caso retorna falso
1 -lt 2 -> representa menor que, nesse caso retorna verdadeiro
1 -ne 2 -> representa não é igual, nesse caso retorna verdadeiro
...
```

Para texto é somente ` "teste" == "teste"` nesse caso retorna verdadeiro.

Vamos fazer um teste,

```
IDADE=30
test $IDADE -gt 30
```

Execute esse comando e veja o que acontece!
...
Eita, algo de errado não está certo... O comando test, não tem retorno, e esse é um problema para nós humanos, mas não para a maquina. Ela usa o `$?` para saber o que fazer, se nós quisermos fazer algo precisamos estruturar melhor essas condições...

Então vamos lá, para trabalhar com `test`, a gente não usa ele explicito, a gente abstraí ele com o `[` isso mesmo `[` representa o comando test... estranho, mas logo você acostuma

```
IDADE=30
test $IDADE -gt 30
if [ $? -eq 0 ]
then
    echo "Jovem com dinheiro"
else
    echo "Ainda lutando na vida"
fi
```
O `if` fecha com `fi` é ele mesmo ao contrário.

Olha que interessante, a gente chamou o `test` duas vezes, uma para ele alimentar a variável $? e depois mesclamos ele com o comando `if [ CONDIÇÃO ]`

Em maioria dos casos você não precisa usar o `test` igual eu fiz, fiz dessa forma, apenas para ficar claro que o sucesso ou erro sempre fica dentro da `$?` e o `test` não mostra nada, apenas alimentar a `$?`

Vamos fazer a mesma coisa só que sem o `test` 

```
IDADE=30
if [ $IDADE -gt 30 ]
then
    echo "Invista 30% em FII e o resto em ações"
else
    echo "Invista só em ações, ta novo vc recupera depois!"
fi
```

#### Tarefa

Faça a mesma comparação, só que dessa vez com seu nome, lembre-se que seu nome é texto(string)


OBS:. Cuidado em colocar as coisas coladas `if [$IDADE -gt 30]` não vai funcionar porque é como se estivesse fazendo `if test$IDADE -gt 30` deixe com espaço `if [ $IDADE -gt 30 ]`


### Case

O case é uma forma mais elegante de deixar seu ShellScript menor, sem aquele atomontado de Ifs...

```
case $NOME in
	"Jose")
		echo "Seu nome é Jose"
	;;
	"Maria")
		echo "Seu nome é Maria"
	;;
	*)
		echo "Não sei seu nome"
	;;
esac
```

O Case tem essa forma de abrir com `case` e fechar com `esac` que é o case ao contrário. Quando você estiver comparando uma grande variaedade de resultado o código ficará mais limpo com ele.

Perceba que cada bloco de comando é separado por `;;`
Somente com isso ele sabe quando terminou um bloco e começa outro.

Quer ter alguma ideia do que da para fazer?
Da uma olhada nessa pergunta sobre usar o case com maior e menor
https://unix.stackexchange.com/questions/65310/case-how-to-implement-equal-or-less-or-greater-in-case-syntax


### For
