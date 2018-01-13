# Processes and Threads

## Arquitectura típica de um computador
![Arquitectura típica de um computador](../Pictures/computer_architecture.png)

## Programa vs Processo
- **programa**: conjunto de instruções que definem como uma tarefa é executada num computador
	- É apenas um **conjunto de instruções** (código máquina), nada mais
	- Para realizar essas **funções/instruções/tarefas** o código (ou a versão compilada dele) tem de ser executado(a)
- **processo:** Entidade que representa a **execução de um programa**
	- Representa a sua atividade
	- Tem associado a si:
		- código que ao contrário do programa está armazenado num endereço de memória (addressing space)
		- **dados** (valores das diferentes variáveis) da execução corrente
		- valores atuais dos registos internos do processador
		- dados dos I/Os, ou seja, dados que estão a ser transferidos entre dispositivos de input e output
		- Estado da execução do programa, ou seja, qual a próxima execução a ser executada (registo PC)
	- Podem existir diferentes processos do mesmo programa
		- Ambiente **multiprogramado** - mais processos que processadores
	 

## Execução num ambiente multiprogramado
O sistema assume que o processo que está na posse do processador irá **ser interrompido**, pudendo assim executar outro processo e dar a "sensação" em **macro tempo** de **simultaneidade**. Nestas situações, o OS é responsável por:

- tratar da **mudança do contexto de execução**, guardando
	- o valor dos registos internos
	- o valor das variáveis
	- o endereço da próxima instrução a ser executada
- chamar o novo processo que vai ocupar agora o CPU e:
	- Esperar que o novo processo termine a realização das suas operações **ou**
	- Interromper o processo,  **parando a sua execução no** processador quando este esgotar o seu `time quantum`
 
![Exemplo de execução num ambiente multiprogramado](../Pictures/execution_multiprogramming_environment.png)



## Modelo de Processos	
Num ambiente **multiprogramado**, devido à constante **troca de processos**, é difícil expressar uma modelo para o processador. Devido ao elevado numero de processo e ao multiprogramming, torna-se difícil de saber qual o processo que está a ser executado e qual a fila de processos as ser executada.

É mais fácil assumir que o ambiente multiprogramado pode ser representado por um **conjunto de processadores virtuais**, estando um processo atribuído a cada um.

O processador virtual está:
	- **ON:** se o processo que lhe está atribuído está a ser executado
	- **OFF:** se o processo que lhe está atribuído não está a ser executado

Para este modelo temos ainda de assumir que:
- Só um dos processadores é que pode estar ativo num dado período de tempo
- O número de **processadores virtuais ativos é menor** (ou igual, se for um ambiente `single processor`) ao número de **processadores reais**
- A execução de um processo **não é afetada** pelo instante temporal nem a localização no código em que o processo é interrompido e é efetuado o switching
- Não existem restrições do número de vezes que qualquer processo pode ser interrompido, quer seja executado total ou parcialmente	

A operação de **switching entre processos** e consequentemente entre processadores virtuais ocorre de forma não **controlada** pelo programa a correr no CPU

Uma **operação de switching** é equivalente a efetuar o `Turning Off` de um processo virtual e o `Turning On` de outro processo virtual.

- `Turning Off` implica **guardar** todo o **contexto de execução**
- `Turning On` implica carregar todo o contexto de execução, **restaurando o estado do programa** quando foi interrompido


## Diagrama de Estados de um Processo
Durante a sua existência, um processo pode assumir diferentes estados, dependendo  das condições em que se encontra:

- **run:** O processo está em execução, tendo a posse do processador
- **blocked:** O processo está bloqueado à **espera de um evento externo** para estar em condições retomar a sua execução. Esse evento externo pode ser:
	- Acesso a um recurso da máquina
	- Fim de uma operação de I/O
	- ...
- **ready:** O processo está pronto a ser executado, mas está à espera que o processador lhe dê a ordem de `start/resume` para puder retomar a sua execução.

As **transições entre estados** normalmente resultam de **intervenções externas ao processo**, mas podem depender de situações em que o processo força uma transição:
	- termina a sua execução antes de terminar o seu `time quantum`
	- Leitura/Escrita em I/O (scanf/printf)

Mesmo que um processo **não abandone o processador por sua iniciativa**, o `scheduler` é responsável por **planear o uso do processador pelos diferentes processos**.

O `(Process) Scheduler` é um módulo do kernel que **monitoriza e gere as transições entre processos**. Assim, um `while(1)` não é executado _ad eternum_. Um processador `multiprocess` só permite que o ciclo infinito seja executado quando é atribuído `CPU time` ao processo.

Existem diferentes políticas que permitem controlar a execução destas transições

![Diagrama de Estados do Processador - Básico](../Pictures/basic_processor_cicle.png)


`Triggers` das transições entre estados:

- **dispatch:** 
	- O processo que estava em modo `run` perdeu o acesso ao processador.
	- Do conjunto de processos prontos a serem executados, tem de ser escolhido **um** para ser executado, sendo lhe atribuído o processador.
	- A escolha feita pelo `dispatcher` pode basear-se em:
		- um sistema de prioridades
		- requisitos temporais
		- aleatoriedade
		- divisão igual do CPU
- **event wait:** 
	- O processo que estava a ser executado sai do estado `run`, não estando em execução no processador. 
		- Ou porque é impedido de continuar pelo scheduler
		- Ou por iniciativa do próprio processo.
			- _scanf_ 
			- _printf_ 
	- O CPU guarda o estado de execução do processo
	- O processo fica em estado `blocked` à **espera da ocorrência de um evento externo**, `event occurs`
- **event occurs:** 
	- Ocorreu o evento que o processo estava à espera
	- O processo transita do estado `blocked` para o estado `ready`, ficando em fila de espera para que lhe seja atribuído o processador
- **time_out:** 
	- O processo esgotou a sua janela temporal, `time quantum`
	- Através de uma interrupção em _hardware_, o sistema operativo vai forçar a saída do processo do processador
	- Transita para o estado _ready_ até lhe ser atribuído um novo `time-quantum` do CPU
	- A transição por time out ocorre em qualquer momento do código. 
	- Os sistemas podem ter `time quantum` diferentes e os `time slots` alocados não têm de ser necessariamente iguais entre dois sistemas.
- **preempt**: 
	- O processo que possui a posse do processador tem uma prioridade mais baixa do que um processo que acordou e está pronto a correr (estado `ready`) 
	- O processo que está a correr no processador é **removido** e transita para o estado `ready`
	- Passa a ser **executado** o processo de **maior prioridade**

### Swap Area
O diagram de estados apresentado não leva em consideração que a **memória principal** (RAM) é **finita**. Isto implica que o número de **processos coexistentes em memória é limitado**.

É necessário usar a **memória secundária** (Disco Rígido) para **extender a memória principal** e aumentar a capacidade de armazenamento dos estados dos processos.

A **memória `swap`** pode ser:

- uma partição de um disco
- um ficheiro

Qualquer processo que **não esteja a correr** por ser `swapped out`, libertando memória principal para outros processos

Qualquer processo `swapped out` pode ser `swapped in`, **quando existir memória principal disponível**

Ao diagrama de estados tem de ser adicionados:
- dois novos estados:
	- **suspended-ready:** Um processo no estado `ready` foi `swapped-out`
	- **suspended-blocked:** O processo no estado `blocked` foi `swapped-out`
- dois novos tipos de transições:
	- **suspend:** O processo é `swapped out`
	- **activate:** O processo é `swapped in`

![Diagrama de Estados do Processador - Com Memória de Swap](../Pictures/swap_processor_cicle.png)

### Temporalidade na vida dos processos
O diagrama assume que os processos são **intemporais**. Excluindo alguns processos de sistema, todos os processos são **temporais**, i.e.:

1. Nascem/São criados
2. Existem (por algum tempo)
3. Morrem/Terminam

Para introduzi a temporalidade no diagrama de estados, são necessários dois novos estados:
- **new:** 
	- O processo foi criado
	- Ainda não foi atribuído à `pool` de processos a serem executados
	- A estrutura de dados associado ao processo é inicializada
- **terminated:** 
	- O processo foi descartado da fila de processos executáveis
	- Antes de ser descartado, existem ações que tem de tomar _(needs clarification)_

Em consequência dos novos estados, passam a existir três novas transições:
	- **admit:** O processo é admitido pelo OS para a `pool` de processos executáveis
	- **exit:** O processo informa o SO que terminou a sua execução
	- **abort:** Um processo é forçado a terminar.
		- Ocorreu um `fatal error`
		- Um processo autorizado abortou a sua execução


![Diagrama de Estados do Processador - Com Processos Temporalmente Finitos](../Pictures/full_processor_cicle.png)

## State Diagram of a Unix Process
![Diagrama de Estados do Processador - Com Memória de Swap](../Pictures/unix_processor_cicle.png)

As três diferenças entre o diagrama de estados de um processo e o diagrama de estados do sistema Unix são

1. Existem **dois estados run**
	1. **kernel running**
	2. **user running**
	- Diferem **no modo** como o processador **executa o código máquina**, existindo **mais instruções e diretivas disponíveis** no modo supervisor (`root`)
2. O estado **ready** é dividido em dois estados:
	1. **ready to run in memory:** O processo está pronto para ser executado/continuar a execução, estando guardado o seu estado em memória
	2. **preempted:** O processo que estava a ser executado foi **forçado a sair do processador** porque **existe um processo mais prioritário para ser executado**
	- Estes **estados são equivalentes** porque:
		- estão ambos **armazenado na memória principal**
		- quando um processo é `preempted` continua pronto a ser executado (não precisando de nenhuma informação de I/O)
		- Partilham a mesma fila (`queue`) de processos, logo são tratados de forma idêntica pelo OS
	- Quando um **processo do utilizador abandona o modo de supervisor** (corre com permissões `root`), **pode ser preempted**
3. A transição de `time-out` que existe no diagrama dos estados de um processo em UNIX é coberta pela transição `preempted`

## Supervisor preempting
Tradicionalmente, a **execução** de um processo **em modo supervisor** (`root`) implicava que a execução do processo **não pudesse ser** interrompida, ou seja, o processo não pode ser **`preempted`**. Ou seja, o UNIX não permitia **real-time processing**

Nas novas versões o código está dividido em **regiões atómicas**, onde a **execução não pode ser interrompida** para  garantir a **preservação de informação das estruturas de dados a manipular**. Fora das regiões atómicas é seguro interromper a execução do código

Cria-se assim uma nova transição, `return to kernel` entre os estados `preempted` e `kernel running`.

## Unix – traditional login
![Diagrama do Login em Linux](../Pictures/unix_traditional_login.png)

## Criação de Processos

![Criação de Processos](../Pictures/process_creation.png)

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main(void)
{
	printf("Before the fork:\n");
	printf(" PID = %d, PPID = %d.\n",
	getpid(), getppid());

	fork();

	printf("After the fork:\n");
	printf(" PID = %d, PPID = %d. Who am I?\n", getpid(), getppid());

	return EXIT_SUCCESS;
}
```

- **fork**: **clona** o processo existente, criando uma **réplica**
	- O estado de execução é igual, incluindo o PC _(Program Counter)_
	- O **mesmo programa** é executado em **processos diferentes**
	- Não existem garantias que o pai execute primeiro que o filho
		- Tudo depende do `time quantum` que o processo pai ocupa antes do `fork`
		- É um ambiente multiprogramado: os dois programas correm em **simultâneo no micro tempo**
- O **espaço de endereçamento** dos dois processos é **igual**
	- É seguida uma filosofia **copy-on-write**. Só é efetuada a cópia da página de memória se o **processo filho modificar** os conteúdos das variáveis 

		 
Existem variáveis diferentes: 

- **PPID:** Parent Process ID
- **PID:** Process ID


```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main(void)
{
	printf("Before the fork:\n");
	printf(" PID = %d, PPID = %d.\n",
	getpid(), getppid());

	int ret = fork();

	printf("After the fork:\n");
	printf(" PID = %d, PPID = %d, ret = %d\n", getpid(), getppid(), ret);

	return EXIT_SUCCESS;
}
```

O retorno da instrução `fork` é diferente entre o processo pai e filho:

- pai: `PID` do filho
- filho: `0`

O retorno do `fork` pode ser usado como variável booleana, de modo a **distinguir o código a correr no filho e no pai**


```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main(void)
{
	printf("Before the fork:\n");
	printf(" PID = %d, PPID = %d.\n", getpid(), getppid());

	int ret = fork();

	if (ret == 0)
	{
		printf("I'm the child:\n");
		printf(" PID = %d, PPID = %d\n", getpid(), getppid());
	}
	else
	{
		printf("I'm the parent:\n");
		printf(" PID = %d, PPID = %d\n", getpid(), getppid());
	}

	printf("After the fork:\n");
	printf(" PID = %d, PPID = %d, ret = %d\n", getpid(), getppid(), ret);

	return EXIT_SUCCESS;
}
```

O `fork` por si só não possui grande interesse. O interesse é puder executar um programa diferente no filho.

- **`exec` system call:** Executar um programa diferente no processo filho
- **`wait` system call:** O pai esperar pela conclusão do programa a correr nos filhos

 

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main(void)
{
	/* check arguments */
	if (argc != 2)
	{
		fprintf(stderr, "spawn <path to file>\n");
		exit(EXIT_FAILURE);
	}
	char *aplic = argv[1];

	printf("===============================\n");

	/* clone phase */
	int pid;
	if ((pid = fork()) < 0)
	{
		perror("Fail cloning process");
		exit(EXIT_FAILURE);
	}

	/* exec and wait phases */
	if (pid != 0) // only runs in parent process
	{
		int status;
		while (wait(&status) == -1);
		printf("===============================\n");
		printf("Process %d (child of %d)”
		“ ends with status %d\n",
		pid, getpid(), WEXITSTATUS(status));
	}
	else // this only runs in the child process
	{
		execl(aplic, aplic, NULL);
		perror("Fail launching program");
		exit(EXIT_FAILURE);
	}
}
```

O `fork` pode **não ser bem sucedido**, ocorrendo um `fork failure`.


## Execução de um programa em C/C++

![Execução de um programa em C/C++](../Pictures/c_cpp_exit.png)

- Em C/C++ o nome de uma função é um ponteiro para a sua função.
- Em C/C++ um include não inclui a biblioteca
	- Indica ao programa que vou usar uma função que tem esta assinatura
- **atexit:** Regista uma função para ser chamada no fim da execução normal do programa
	- São chamadas em ordem inversa ao seu registo

## Argumentos passados pela linha de comandos e variáveis de ambiente
- **argv:** array de strings que representa um conjunto de parâmetros passados ao programa
	- **argv[0]** é a referência do programa
- **env** é um array de strings onde cada string representa uma variável do tipo: `name-value`
- **getenv** devolve o valor de uma variável definida no array **env**

## Espaço de Endereçamento de um Processo em Linux

![Espaço de endereçamento de um processo em Linux](../Pictures/address_space_unix_process.png)

- As variáveis que existem no processo pai também existem no processo filho (clonadas)
- As variáveis globais são comuns aos dois processos 
- Os endereços das variáveis são todos iguais porque o espaço de endereçamento é igual (memória virtual)
- Cada processo tem as suas variáveis, residindo numa página de memória física diferente
- Quando o processo é clonado, o espaço de dados só é clonado quando um processo escreve numa variável, ou seja, após a modificação é que são efetuadas as cópias dos dados

- O programa acede a um endereço de memória virtual e depois existe hardware que trata de alocar esse endereço de memória de virtual num endereço de memória física
- Posso ter dois processos com memórias virtuais distintas mas fisicamente estarem ligados *ao mesmo endereço de memória*
- Quando faço um `fork` não posso assumir que existem variáveis partilhadas entre os processos 


### Process Control Table
É um array de `process control block`, uma estrutura de dados mantida pelo sistema operativo para guardar a informação relativa todos os processos.

O `process controlo block` é usado para guardar a informação relativa a apenas um processo, possuindo os campos:

- `identification`: id do processo, processo-pai, utilizador e grupo de utilizadores a que pertence
- `address space`: endereço de memória/swap onde se encontra:
	- código
	- dados
	- stack
- `processo state`: valor dos registos internos (incluindo o PC e o `stack pointer`) quando ocorre o switching entre processos
- `I/O context`: canais de comunicação e respetivos buffers que o processo tem associados a si
- `state`: estado de execução, prioridade e eventos
- `statistic`: _start time_, _CPU time_

![Process Control Table](../Pictures/process_control_table.png)





