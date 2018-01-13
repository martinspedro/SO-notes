---
title: "Interprocess Communication"
---

# Conceitos Introdutórios
Num ambiente multiprogramado, os processos podem ser:

- Independentes:
	- Nunca interagem desde a sua criação à sua destruição
	- Só possuem uma interação implícita: **competir por recursos do sistema**
		- e.g.: jobs num sistema bacth, processos de diferentes utilizadores
	- É da responsabilidade do sistema operativo garantir que a atribuição de recursos é feita de forma controlada
		- É preciso garantir que não ocorre perda de informação
		- Só **um processo pode usar um recurso num intervalo de tempo** - _**Mutual Exclusive Access**_
- Cooperativos:
	- **Partilham Informação** e/ou **Comunicam** entre si
	- Para **partilharem** informação precisam de ter acesso a um **espaço de endereçamento comum**
	- A comunicação entre processos pode ser feita através de:
		- Endereço de memória comum
		- Canal de comunicação que liga os processos
	- É da **responsabilidade do processo** garantir que o acesso à zona de memória partilhada ou ao canal de comunicação é feito de forma controlada para não ocorrerem perdas de informação
		- Só **um processo pode usar um recurso num intervalo de tempo** - _**Mutual Exclusive Access**_
		- Tipicamente, o canal de comunicação é um recurso do sistema, pelo quais os **processos competem**

O acesso a um recurso/área partilhada é efetuada através de código. Para evitar a perda de informação, o código de acesso (também denominado zona crítica) deve evitar incorrer em **race conditions**.

## Exclusão Mútua
Ao forçar a ocorrência de exclusão mútua no acesso a um recurso/área partilhada, podemos originar:

- **deadlock:**
	- Vários processos estão em espera **eternamente** pelas condições/eventos que lhe permitem aceder à sua respetiva **zona crítica**
		- Pode ser provado que estas condições/eventos **nunca se irão verificar**
	- Causa o bloqueio da execução das operações
- **starvation:**
	- Na competição por acesso a uma zona crítica por vários processos, verificam-se um conjunto de circunstâncias na qual novos processos, com maior prioridade no acesso às suas zonas críticas, continuam a aparecer e **tomar posse dos recursos partilhados**
	- O acesso dos processos mais antigos à sua zona crítica é sucessivamente adiado

# Acesso a um Recurso
No acesso a um recurso é preciso garantir que não ocorrem **race conditions**.
Para isso, **antes** do acesso ao recurso propriamente dito é preciso **desativar o acesso** a esse recurso pelos **outros processos** (reclamar _ownership_) e após o acesso é preciso restaurar as condições iniciais, ou seja, **libertar o acesso** ao recurso.


```c
/* processes competing for a resource - p = 0, 1, ..., N-1 */
void main (unsigned int p)
{
	forever
	{	
		do_something();
		access_resource(p);
		do_something_else();
	}	
}

void acess_resource(unsigned int p)
{
	enter_critical_section(p);
	use_resource();		//	critical section
	leave_critical_section(p);
}
```

# Acesso a Memória Partilhada
O acesso à memória partilhada é muito semelhante ao aceso a um recurso (podemos ver a memória partilhada como um recurso partilhado entre vários processos).

Assim, à semelhança do acesso a um recurso, é preciso **bloquear o acesso de outros processos à memória partilhada** antes de aceder ao recurso e após aceder, **reativar o acesso a memória partilhada** pelos outros processos.

```c
/* shared data structure */
shared DATA d;

/* processes sharing data - p = 0, 1, ..., N-1 */
void main (unsigned int p)
{
	forever
	{
		do_something();
		access_shared_area(p);
		do_something_else();
	}
}

void access_shared_area(unsigned int p)
{
	enter_critical_section(p);
	manipulate_shared_area(); // critical section
	leave_critical_section(p);
}
```

## Relação Produtor-Consumidor
O acesso a um recurso/memória partilhada pode ser visto como um problema Produtor-Consumidor:

- Um processo acede para **armazenar dados**, **escrevendo** na memória partilhada _(Produtor)_
- Outro processo acede para **obter dados**, **lendo** da memória partilhada _(Consumidor)_

### Produtor
O produtor "produz informação" que quer guardar na FIFO e enquanto não puder efetuar a sua escrita, aguarda até puder **bloquear** e **tomar posse** do zona de memória partilhada

```c
/* communicating data structure: FIFO of fixed size */
shared FIFO fifo;

/* producer processes - p = 0, 1, ..., N-1 */
void main (unsigned int p)
{
	DATA val;
	bool done;


	forever
	{
		produce_data(&val);
		done = false;
		do
		{
			// Beginning of Critical Section
			enter_critical_section(p);
			if (fifo.notFull())
			{
				fifo.insert(val);
				done = true;
			}
			leave_critical_section(p);
			// End of Critical Section
		} while (!done);
		do_something_else();
	}
}
```

### Consumidor
O consumidor quer ler informação que precisa de obter da FIFO e enquanto não puder efetuar a sua leitura, aguarda até puder **bloquear** e **tomar posse** do zona de memória partilhada

```c
/* communicating data structure: FIFO of fixed size */
shared FIFO fifo;

/* consumer processes - p = 0, 1, ..., M-1 */
void main (unsigned int p)
{
DATA val;
bool done;
	forever
	{
		done = false;
		do
		{
			// Beginning of Critical Section
			enter_critical_section(p);
			if (fifo.notEmpty())
			{
				fifo.retrieve(&val);
				done = true;
			}
			leave_critical_section(p);
			// End of Critical Section
		} while (!done);
		consume_data(val);
		do_something_else();
	}
}
```

# Acesso a uma Zona Crítica
Ao aceder a uma zona crítica devem ser verificados as seguintes condições:

- **Efective Mutual Exclusion:** O **acesso** a uma **zona crítica** associada com o mesmo recurso/memória partilhada só pode ser **permitida a um processo de cada vez** entre **todos os processos** a competir pelo acesso a esse mesmo recurso/memória partilhada
- **Independência** do número de processos intervenientes e na sua velocidade relativa de execução
- Um processo fora da sua zona crítica não pode impedir outro processo de entrar na sua zona crítica
- Um processo **não deve ter de esperar indefinidamente** após pedir acesso ao recurso/memória partilhada para que possa aceder à sua zona crítica
- O período de tempo que um processo está na sua **zona crítica** deve ser **finito**

## Tipos de Soluções
Para controlar o acesso às zonas críticas normalmente é usado um endereço de memória. A gestão pode ser efetuada por:

- **Software:**
	- A solução é baseada nas instruções típicas de acesso à memória
	- Leitura e Escrita são independentes e correspondem a instruções diferentes
- **Hardware:**
	- A solução é baseada num conjunto de instruções especiais de acesso à memória
	- Estas instruções permitem ler e de seguida escrever na memória, de forma **atómica**

## Alternância Estrita _(Strict Alternation)_
**Não é uma solução válida**

- Depende da velocidade relativa de execução dos processos intervenientes
- O processo com menos acessos impõe o ritmo de acessos aos restantes processos
- Um processo fora da zona crítica não pode prevenir outro processo de entrar na sua zona crítica
- Se não for o seu turno, um processo é obrigado a esperar, mesmo que não exista mais nenhum processo a pedir acesso ao recurso/memória partilhada

```c
/* control data structure */
#define R 		/* process id = 0, 1, ..., R-1 */

shared unsigned int access_turn = 0;
void enter_critical_section(unsigned int own_pid)
{
	while (own_pid != access_turn);
}

void leave_critical_section(unsigned int own_pid)
{
	if (own_pid == access_turn)
		access_turn = (access_turn + 1) % R;
}
```

## Eliminar a Alternância Estrita

```c
/* control data structure */
#define R 2 	/* process id = 0, 1 */

shared bool is_in[R] = {false, false};

void enter_critical_section(unsigned int own_pid)
{
	unsigned int other_pid_ = 1 - own_pid;

	while (is_in[other_pid]);
	is_in[own_pid] = true;
}

void leave_critical_section(unsigned int own_pid)
{
	is_in[own_pid] = false;
}
```

Esta solução não é válida porque não garante **exclusão mútua**. 

Assume que:

- $P_0$ entra na função `enters_critical_section` e testa `is_in[1]`, que retorna  Falso
- $P_1$ entra na função `enter_critical_section` e testa `is_in[0]`, que retorna Falso
- $P_1$ altera `is_in[0]` para _true_ e entra na zona crítica
- $P_0$ altera `is_in[1]` para _true_ e entra na zona crítica

Assim, ambos os processos entra na sua zona crítica **no mesmo intervalo de tempo**.

O principal problema desta implementação advém de **testar primeiro** a variável de controlo do **outro processo** e só **depois** alterar a **sua variável** de controlo.

## Garantir a exclusão mútua

```c
/* control data structure */
#define R 2 	/* process id = 0, 1 */

shared bool want_enter[R] = {false, false};

void enter_critical_section(unsigned int own_pid)
{
	unsigned int other_pid_ = 1 - own_pid;

	want_enter[own_pid] = true;
	while (want_enter[other_pid]);
}

void leave_critical_section(unsigned int own_pid)
{
	want_enter[own_pid] = false;
}
```

Esta solução, apesar de **resolver a exclusão mútua**, **não é válida** porque podem ocorrer situações de **deadlock**. 

Assume que:

- $P_0$ entra na função `enter_critical_section` e efetua o _set_ de `want_enter[0]`
- $P_1$ entra na função `enter_critical_section` e efetua o _set_ de `want_enter[1]`
- $P_1$ testa `want_enter[0]` e, como é _true_, **fica em espera** para entrar na zona crítica
- $P_0$ testa `want_enter[1]` e, como é _true_, **fica em espera** para entrar na zona crítica

Com **ambos os processos em espera** para entrar na zona crítica e **nenhum processo na zona crítica** entramos numa situação de **deadlock**.

Para resolver a situação de deadlock, **pelo menos um dos processos** tem recuar na intenção de aceder à zona crítica.

## Garantir que não ocorre deadlock

```c
/* control data structure */
#define R 2 	/* process id = 0, 1 */

shared bool want_enter[R] = {false, false};

void enter_critical_section(unsigned int own_pid)
{
	unsigned int other_pid_ = 1 - own_pid;

	want_enter[own_pid] = true;
	while (want_enter[other_pid])
	{
		want_enter[own_pid] = false;	// go back
		random_dealy();
		want_enter[own_pid] = true;		// attempt a to go to the critical section
	}
}

void leave_critical_section(unsigned int own_pid)
{
	want_enter[own_pid] = false;
}
```

A solução é quase válida. Mesmo um dos processos a recuar ainda é possível ocorrerem situações de **deadlock** e **starvation**:

- Se ambos os processos **recuarem ao "mesmo tempo"** (devido ao `random_delay()` ser igual), entramos numa situação de **starvation**
- Se ambos os processos **avançarem ao "mesmo tempo"** (devido ao `random_delay()` ser igual), entramos numa situação de **deadlock**

A solução para **mediar os acessos** tem de ser **determinística** e não aleatória.

## Mediar os acessos de forma determinística: _Dekker agorithm_

```c
/* control data structure */
#define R 2 	/* process id = 0, 1 */

shared bool want_enter[R] = {false, false};
shared uint p_w_priority = 0;

void enter_critical_section(unsigned int own_pid)
{
	unsigned int other_pid_ = 1 - own_pid;

	want_enter[own_pid] = true;
	while (want_enter[other_pid])
	{
		if (own_pid != p_w_priority)			// If the process is not the priority process
		{
			want_enter[own_pid] = false;		// go back
			while (own_pid != p_w_priority);	// waits to access to his critical section while
												//  its is not the priority process 
			want_enter[own_pid] = true;			// attempt to go to his critical section
		}
	}
}

void leave_critical_section(unsigned int own_pid)
{
	unsigned int other_pid_ = 1 - own_pid;
	p_w_priority = other_pid;					// when leaving the its critical section, assign the
												// priority to the other process
	want_enter[own_pid] = false;				
}
```

É uma **solução válida**:

- Garante exclusão mútua no acesso à zona crítica através de um mecanismo de alternância para resolver o conflito de acessos
- **deadlock** e **starvation** **não estão presentes**
- Não são feitas suposições relativas ao tempo de execução dos processos, i.e., o algoritmo é **independente** do tempo de execução dos processos

No entanto, **não pode ser generalizado**  para mais do que 2 processos e garantir que continuam a ser satisfeitas as condições de **exclusão mútua** e a ausência de **deadlock** e **starvation**


## Dijkstra algorithm (1966)

```c
/* control data structure */
#define R 2 	/* process id = 0, 1 */

shared uint want_enter[R] = {NO, NO, ..., NO};
shared uint p_w_priority = 0;

void enter_critical_section(uint own_pid)
{
	uint n;
	do
	{
		want_enter[own_pid] = WANT;					// attempt to access to the critical section
		while (own_pid != p_w_priority)				// While the process is not the priority process
		{
			if (want_enter[p_w_priority] == NO)		// Wait for the priority process to leave its critical section
				p_w_priority = own_pid;
		}

		want_enter[own_pid] = DECIDED;				// Mark as the next process to access to its critical section

		for (n = 0; n < R; n++)				// Search if another process is already entering its critical section
		{
			if (n != own_pid && want_enter[n] == DECIDED)	// If so, abort attempt to ensure mutual exclusion
				break;
		}
	} while(n < R);
}

void leave_critical_section(unsigned int own_pid)
{
	p_w_priority = (own_pid + 1) % R;			// when leaving the its critical section, assign the
												// priority to the next process
	want_enter[own_pid] = false;				
}
```

Pode sofrer de **starvation** se quando um processo iniciar a saída da zona crítica e alterar `p_w_priority`, atribuindo a prioridade a outro processo, outro processo tentar aceder à zona crítica, sendo a sua execução interrompida no for. Em situações "especiais", este fenómeno pode ocorrer sempre para o mesmo processo, o que faz com que ele nunca entre na sua zona crítica


## Peterson Algorithm (1981)

```c
/* control data structure */
#define R 2 	/* process id = 0, 1 */

shared bool want_enter[R] = {false, false};
shared uint last;

void enter_critical_section(uint own_pid)
{
	unsigned int other_pid_ = 1 - own_pid;

	want_enter[own_pid] = true;
	last = own_pid;
	while ( (want_enter[other_pid]) && (last == own_pid) );		// Only enters the critical section when no other
																// process wants to enter and the last request
																// to enter is made by the current process
}

void leave_critical_section(unsigned int own_pid)
{
	want_enter[own_pid] = false;
}
```

O algoritmo de _Peterson_ usa a **ordem de chegada** de pedidos para resolver conflitos:

- Cada processo tem de **escrever o seu ID numa variável partilhada** (_last_), que indica qual foi o último processo a pedir para entrar na zona crítica
- A **leitura seguinte** é que vai determinar qual é o processo que foi o último a escrever e portanto qual o processo que deve entrar na zona crítica



\begin{table}[H]
\centering
\renewcommand{\arraystretch}{1.2}
\begin{tabular}{lccccc}
\multicolumn{1}{c}{ } & \multicolumn{2}{c}{$P_0$ quer entrar} & & \multicolumn{2}{c}{$P_1$ quer entrar} \\ \cmidrule{2-3} \cmidrule{5-6}
\multicolumn{1}{c}{\multirow{-2}{*}} & \multicolumn{1}{c}{$P_1$ não quer entrar} & \multicolumn{1}{c}{$P_1$ quer entrar} & & $P_0$ não quer entrar & $P_0$ quer entrar \\ \toprule
\multicolumn{1}{l}{last = $P_0$} & \multicolumn{1}{c}{$P_0$ entra} & \multicolumn{1}{c}{$P_1$ entra} & & - & $P_1$ entra \\
last = $P_1$ & - & $P_0$ entra & & $P_1$ entra & $P_0$ entra \\ \bottomrule
\end{tabular}
\end{table}


É uma solução válida que:

- Garante exclusão mútua
- Previne deadlock e starvation
- É independente da velocidade relativa dos processos 
- Pode ser generalizada para mais do que dois processos (variável partilhada -> fila de espera)

## Generalized Peterson Algorithm (1981)

```c
/* control data structure */
#define R ... 	/* process id = 0, 1, ..., R-1 */

shared bool want_enter[R] = {-1, -1, ..., -1};
shared uint last[R-1];

void enter_critical_section(uint own_pid)
{
	for (uint i = 0; i < R -1; i++)				
	{
		want_enter[own_pid] = i;		
		
		last[i] = own_pid;

		do
		{
			test = false;
			for (uint j = 0; j < R; j++)
			{
				if (j != own_pid)
					test = test || (want_enter[j] >= i)
			}
		} while ( test && (last[i] == own_pid) );		// Only enters the critical section when no other
																// process wants to enter and the last request
																// to enter is made by the current process
	}
}

void leave_critical_section(unsigned int own_pid)
{
	want_enter[own_pid] = -1;
}
```

> _needs clarification_

# Soluções de Hardware

## Desativar as interrupções

Num ambiente computacional com **um único processador:**

- A alternância entre processos, num ambiente **multiprogramado**, é sempre causada por um evento/dispositivo externo
	- **real time clock (RTC):** origina a transição de time-out em sistemas _preemptive_
	- **device controller:** pode causar transições _preemptive_ no caso de um fenómeno de _wake up_ de um **processo mais prioritário**
	- Em qualquer dos casos, o **processador é interrompido** e a execução do processo atual parada
- A garantia de acesso em **exclusão mútua** pode ser feita desativando as interrupções
- No entanto, só pode ser efetuada em **modo kernel**
	- Senão código malicioso ou com _bugs_ poderia bloquear completamente o sistema

Num ambiente computacional **multiprocessador**, desativar as interrupções num único processador não tem qualquer efeito.

Todos os outro processadores (ou _cores_) continuam a responder às interrupções.

## Instruções Especiais em Hardware

### Test and Set (TAS primitive)
A função de hardware, `test_and_set` se for implementada atomicamente (i.e., sem interrupções) pode ser utilizada para construir a primitiva  **lock**, que permite a entrada na zona crítica

Usando esta primitiva, é possível criar a função _lock_, que permite entrar na zona crítica


```c
shared bool flag = false;

bool test_and_set(bool * flag)
{
	bool prev = *flag;
	*flag = true;
	return prev;
}

void lock(bool * flag)
{
	while (test_and_set(flag);	// Stays locked until and unlock operation is used
}

void unlock(bool * flag)
{
	*flag = false;
}
```

### Compare and Swap
Se implementada de forma atómica, a função `compare_and_set` pode ser usada para implementar a primitiva lock, que permite a entrada na zona crítica

O comportamento esperado é que coloque a variável a 1 sabendo que estava a 0 quando a função foi chamada e vice-versa.

```c
shared int value = 0;

int compare_and_swap(int * value, int expected, int new_value)
{
	int v = *value;
	if (*value == expected)
		*value = new_value;
	return v;
}

void lock(int * flag)
{
	while (compare_and_swap(&flag, 0, 1) != 0);
}

void unlock(bool * flag)
{
	*flag = 0;
}
```

## Busy Waiting
Ambas as funções anteriores são suportadas nos _Instruction Sets_ de alguns processadores, implementadas de forma atómica

No entanto, ambas as soluções anteriores sofrem de **busy waiting**. A primitiva lock está no seu **estado ON** (usando o CPU) **enquanto espera** que se verifique a condição de acesso à zona crítica. Este tipo de soluções são conhecidas como **spinlocks**, porque o processo oscila em torno da variável enquanto espera pelo acesso

Em sistemas **uniprocessor**, o **busy_waiting** é **indesejado** porque causa:

- **Perda de eficiência:** O **time quantum** de um processo está a ser desperdiçado porque não está a ser usado para nada
- ** Risco de deadlock**: Se um **processo mais prioritário** tenciona efetuar um **lock** enquanto um processo menos prioritário está na sua zona crítica, **nenhum deles pode prosseguir**.
	- O processo menos prioritário tenta executar um unlock, mas não consegue ganhar acesso a um _time qantum_ do CPU devido ao processo mais prioritário
	- O processo mais prioritário não consegue entrar na sua zona crítica porque o processo menos prioritário ainda não saiu da sua zona crítica


Em sistemas **multiprocessador** com **memória partilhada**, situações de busy waiting podem ser menos críticas, uma vez que a troca de processos _(preempt)_ tem custos temporais associados. É preciso:

- guardar o estado do processo atual
	- variáveis
	- stack
	- $PC
- copiar para memória o código do novo processo


## Block and wake-up
Em **sistemas uniprocessor** (e em geral nos restantes sistemas), existe a o requerimento de **bloquear um processo** enquanto este está à espera para entrar na sua zona crítica

A implementação das funções `enter_critical_section` e `leave_critical_section` continua a precisar de operações atómicas.



```c
#define  R ... /* process id = 0, 1, ..., R-1 */

shared unsigned int access = 1;		// Note that access is an integer, not a boolean

void enter_critical_section(unsigned int own_pid)
{
	// Beginning of atomic operation
	if (access == 0) 
		block(own_pid);
	
	else access -= 1;
	// Ending of atomic operation
}

void leave_critical_section(unsigned int own_pid)
{
	// Beginning of atomic operation
	if (there_are_blocked_processes)
		wake_up_one();
	 else access += 1;
	// Ending of atomic operation
}
```

```c
/* producers - p = 0, 1, ..., N-1 */
void producer(unsigned int p)
{
	DATA data;
	forever
	{
		produce_data(&data);
		bool done = false;
		do
		{
			lock(p);
			if (fifo.notFull())
			{
				fifo.insert(data);
				done = true;
			}
		unlock(p);
	} while (!done);
	do_something_else();
	}
}
```

```c
/* consumers - c = 0, 1, ..., M-1 */
void consumer(unsigned int c)
{
	DATA data;
	forever
	{
		bool done = false;
		do
		{
			lock(c);
			if (fifo.notEmpty())
			{
				fifo.retrieve(&data);
				done = true;
			}
			unlock(c);
		} while (!done);
		consume_data(data);
		do_something_else();
	}
}
```

---
title: Semáforos
---

# Semáforos
No ficheiro `IPC.md` são indicadas as condições e informação base para:

- Sincronizar a entrada na zona crítica
- Para serem usadas em programação concorrente
- Criar zonas que garantam a exclusão mútua

Semáforos são **mecanismos** que permitem por implementar estas condições e **sincronizar a atividade** de **entidades concorrentes em ambiente multiprogramado**

Não são nada mais do que **mecanismos de sincronização**.


## Implementação
Um semáforo é implementado através de:

- Um tipo/estrutura de dados
- Duas operações **atómicas**:
	- down (ou wait)
	- up (ou signal/post)

```c
typedef struct
{
	unsigned int val;	/* can not be negative */
	PROCESS *queue;		/* queue of waiting blocked processes */
} SEMAPHORE;
```

### Operações
As únicas operações permitidas são o **incremento**, up, ou **decremento**, down, da variável de controlo. A variável de controlo, `val`, **só pode ser manipulada através destas operações!**

Não existe uma função de leitura nem de escrita para `val`.

- `down`
	- **bloqueia** o processo se `val == 0`
	- **decrementa** `val` se `val != 0`
- `up`
	- Se a `queue` não estiver vazia, **acorda** um dos processos
	- O processo a ser acordado depende da **política implementada**
	- **Incrementa** `val` se a `queue` estiver vazia

### Solução típica de sistemas _uniprocessor_

```c
/* array of semaphores defined in kernel */
#define R /* semid = 0, 1, ..., R-1 */

static SEMAPHORE sem[R];

void sem_down(unsigned int semid)
{
	disable_interruptions;
	if (sem[semid].val == 0)
		block_on_sem(getpid(), semid);
	else
		sem[semid].val -= 1;
	enable_interruptions;
}

void sem_up(unsigned int semid)
{
	disable_interruptions;
	if (sem[sem_id].queue != NULL)
		wake_up_one_on_sem(semid);
	else
		sem[semid].val += 1;
	enable_interruptions;
}
```

A solução apresentada é típica de um sistema _uniprocessor_ porque recorre à diretivas **disable_interruptions** e **enable_interruptions** para garantir a exclusão mútua no acesso à zona crítica.

Só é possível garantir a exclusão mútua nestas condições se o sistema só possuir um único processador, porque as diretivas irão impedir a interrupção do processo que está na posse do processador devido a eventos externos. Esta solução não funciona para um sistema multiprocessador porque ao  executar a diretiva **disable_interruptions**, só estamos a **desativar as interrupções para um único processador**. Nada impede que noutro processador esteja a correr um processo que vá aceder à mesma zona de memória partilhada, não sendo garantida a exclusão mútua para sistemas multiprocessador.

Uma solução alternativa seria a extensão do **disable_interruptions** a todos os processadores. No entanto, iriamos estar a impedir a troca de processos noutros processadores do sistema que poderiam nem sequer tentar aceder às variáveis de memória partilhada.

## Bounded Buffer Problem

```c
shared FIFO fifo; /* fixed-size FIFO memory */

/* producers - p = 0, 1, ..., N-1 */
void producer(unsigned int p)
{
	DATA data;
	forever
	{
		produce_data(&data);
		bool done = false;
		do
		{
			lock(p);
			if (fifo.notFull())
			{
				fifo.insert(data);
				done = true;
			}
			unlock(p);
		} while (!done);
	do_something_else();
	}
}

/* consumers - c = 0, 1, ..., M-1 */
void consumer(unsigned int c)
{
	DATA data;
	forever
	{
		bool done = false;
		do
		{
			lock(c);
			if (fifo.notEmpty())
			{
				fifo.retrieve(&data);
				done = true;
			}
			unlock(c);
		} while (!done);
		consume_data(data);
		do_something_else();
	}
}
```

### Como Implementar usando semáforos?
A solução para o _Bounded-buffer Problem_ usando semáforos tem de:

- Garantir **exclusão mútua**
- Ausência de busy waiting

```c
shared FIFO fifo;	/*fixed-size FIFO memory */
shared sem access;	/*semaphore to control mutual exclusion */
shared sem nslots;	/*semaphore to control number of available slots*/
shared sem nitems;	/*semaphore to control number of available items */


/* producers - p = 0, 1, ..., N-1 */
void producer(unsigned int p)
{
	DATA val;

	forever
	{
		produce_data(&val);
		sem_down(nslots);
		sem_down(access);
		fifo.insert(val);
		sem_up(access);
		sem_up(nitems);
		do_something_else();
	}
}

/* consumers - c = 0, 1, ..., M-1 */
void consumer(unsigned int c)
{
	DATA val;

	forever
	{
		sem_down(nitems);
		sem_down(access);
		fifo.retrieve(&val);
		sem_up(access);
		sem_up(nslots);
		consume_data(val);
		do_something_else();
	}
}
```

Não são necessárias as funções `fifo.empty()` e `fifo.full()` porque são implementadas indiretamente pelas variáveis:

- **nitens:** Número de "produtos" prontos a serem "consumidos"
	- Acaba por implementar, indiretamente, a funcionalidade de verificar se a FIFO está empty
- **nslots:** Número de slots livres no semáforo. Indica quantos mais "produtos" podem ser produzidos pelo "consumidor"
	- Acaba por implementar, indiretamente, a funcionalidade de verificar se a FIFO está full


Uma alternativa **ERRADA** a uma implementação com semáforos é apresentada abaixo:

```c
shared FIFO fifo;	/*fixed-size FIFO memory */
shared sem access;	/*semaphore to control mutual exclusion */
shared sem nslots;	/*semaphore to control number of available slots*/
shared sem nitems;	/*semaphore to control number of available items */


/* producers - p = 0, 1, ..., N-1 */
void producer(unsigned int p)
{
	DATA val;

	forever
	{
		produce_data(&val);
		sem_down(access);		// WRONG SOLUTION! The order of this
		sem_down(nslots);		// two lines are changed 
		fifo.insert(val);
		sem_up(access);
		sem_up(nitems);
		do_something_else();
	}
}

/* consumers - c = 0, 1, ..., M-1 */
void consumer(unsigned int c)
{
	DATA val;

	forever
	{
		sem_down(nitems);
		sem_down(access);
		fifo.retrieve(&val);
		sem_up(access);
		sem_up(nslots);
		consume_data(val);
		do_something_else();
	}
}
```

A diferença entre esta solução e a anterior está na troca de ordem de instruções  `sem_down(access)` e `sem_down(nslots)`. A função `sem_down`, ao contrário das funções anteriores, **decrementa** a variável, não tenta decrementar. 

Assim, o produtor tenta aceder à sua zona crítica sem primeiro decrementar o número de slots livres para ele guardar os resultados da sua produção _(needs_clarification)_

## Análise de Semáforos

### Vantagens
- **Operam ao nível do sistema operativo:**
	- As operações dos semáforos são implementadas no _kernel_
	- São disponibilizadas aos utilizadores através de _system_calls_
- São **genéricos** e **modulares**
	- por serem implementações de baixo nível, ganham **versatilidade**
	- Podem ser usados em qualquer tipo de situação de programação concorrente


### Desvantagens
- Usam **primitivas de baixo nível**, o que implica que o programador necessita de conhecer os **princípios da programação concorrente**, uma vez que são aplicadas numa filosofia _bottom-up_
		- Facilmente ocorrem **race conditions**
		- Facilmente se geram situações de **deadlock**, uma vez que **a ordem das operações atómicas são relevantes**
- São tanto usados para implementar **exclusão mútua** como para **sincronizar processos**

### Problemas do uso de semáforos
Como tanto usados para implementar **exclusão mútua** como para **sincronizar processos**, se as condições de acesso não forem satisfeitas, os processos são bloqueados **antes** de entrarem nas suas regiões críticas.

- Solução sujeita a erros, especialmente em situações complexas
	- pode existir **mais do que um ponto de sincronismos** ao longo do programa
	
## Semáforos em Unix/Linux

**POSIX:**

- Suportam as operações de `down` e `up`
	- `sem_wait`
	- `sem_trywait`
	- `sem_timedwait`
	- `sem_post`
- Dois tipos de semáforos:
	- **named semaphores:**
		- São criados num sistema de ficheiros virtual (e.g. /dev/sem)
		- Suportam as operações:
			- `sem_open`
			- `sem_close`
			- `sem_unlink`
	- **unnamed semaphores:**
		- São _memory based_
		- Suportam as operações
			- `sem_init`
			- `sem_destroy`

**System V:** 

- Suporta as operações:
	- `semget` : criação
	- `semop` : as diretivas `up` e `down`
	- `semctl` : outras operações



# Monitores
Mecanismo de sincronização de alto nível para resolver os problemas de sincronização entre processos, numa perspetiva __top-down__. Propostos independentemente por Hoare e Brinch Hansen

Seguindo esta filosofia, a **exclusão mútua** e **sincronização** são tratadas **separadamente**, devendo os processos:

1. Entrar na sua zona crítica
2. Bloquear caso não possuam condições para continuar


Os monitores são uma solução que suporta nativamente a exclusão mútua, onde uma aplicação é vista como um conjunto de _threads_ que competem para terem acesso a uma estrutura de dados partilhada, sendo que esta estrutura só pode ser acedida pelos métodos do monitor.

Um monitor assume que todos os seus métodos **têm de ser executados em exclusão mútua**:

- Se uma _thread_ chama um **método de acesso** enquanto outra _thread_ está a executar outro método de acesso, a sua **execução é bloqueada** até a outra terminar a execução do método

A sincronização entre threads é obtida usando **variáveis condicionais**:

- `wait`: A _thread_ é bloqueada e colocada fora do monitor
- `signal`: Se existirem outras _threads_ bloqueadas, uma é escolhida para ser "acordada"


## Implementação
```cpp
	monitor example
	{
	/* internal shared data structure */
	DATA data;
	
	condition c; /* condition variable */
	
	/* access methods */
	method_1 (...)
	{
		...
	}
	method_2 (...)
	{
		...
	}
	
	...

	/* initialization code */
	...
```

## Tipos de Monitores

### Hoare Monitor
![Diagrama da estrutura interna de um Monitor de Hoare](Pictures/hoare_monitor.png)

- Monitor de aplicação geral
- Precisa de uma stack para os processos que efetuaram um `wait` e são colocados em espera
- Dentro do monitor só se encontra a _thread_ a ser executada por ele
- Quando existe um `signal`, uma _thread_ é **acordada** e posta em execução


### Brinch Hansen Monitor
![Diagrama da estrutura interna de um Monitor de Brinch Hansen](Pictures/brinch_hansen_monitor.png)

- A última instrução dos métodos do monitor é `signal`
	- Após o `signal` a  _thread_ sai do monitor
- **Fácil de implementar:** não requer nenhuma estrutura externa ao monitor
- **Restritiva:** **Obriga** a que cada método só possa possuir uma instrução de `signal`


### Lampson/Redell Monitors
![Diagrama da estrutura interna de um Monitor de Lampson/Redell](Pictures/lampson_redell_monitor.png)

- A _thread_ que faz o `signal` é a que continua a sua execução (entrando no monitor)
- A _thread_ que é acordada devido ao `signal` fica fora do monitor, **competindo pelo acesso** ao monitor
- Pode causar **starvation**.
	- Não existem garantias que a __thread__ que foi acordada e fica em competição por acesso vá ter acesso
	- Pode ser **acordada** e voltar a **bloquear**
	- Enquanto está em `ready` nada garante que outra _thread_ não dê um `signal` e passe para o estado `ready`
	- A _thread_ que tinha sido acordada volta a ser **bloqueada**


## Bounded-Buffer Problem usando Monitores
```c
shared FIFO fifo;			 /* fixed-size FIFO memory */
shared mutex access;		 /* mutex to control mutual exclusion */
shared cond nslots;		 /* condition variable to control availability of slots*/
shared cond nitems;		 /* condition variable to control availability of items */

/* producers - p = 0, 1, ..., N-1 */
void producer(unsigned int p)
{
	DATA data;
	forever
	{
		produce_data(&data);
		lock(access);
		if/while (fifo.isFull())
		{
			wait(nslots, access);
		}
		fifo.insert(data);
		unlock(access);
		signal(nitems);
		do_something_else();
	}
}

/* consumers - c = 0, 1, ..., M-1 */
void consumer(unsigned int c)
{
	DATA data;
	forever
	{
		lock(access);
		if/while (fifo.isEmpty())
		{
			wait(nitems, access);
		}
		fifo.retrieve(&data);
		unlock(access);
		signal(nslots);
		consume_data(data);
		do_something_else();
	}
}
```

O uso de `if/while` deve-se às diferentes implementações de monitores:

- `if`: **Brinch Hansen** 
	- quando a _thread_ efetua o `signal` sai imediatamente do monitor, podendo entrar logo outra _thread_
- `while`: **Lamson Redell**
	- A _thread_ acordada fica à espera que a _thread_ que deu o `signal` termine para que possa **disputar** o acesso
	

- O `wait` internamente vai **largar a exclusão mútua**
	- Se não larga a exclusão mútua, mais nenhum processo consegue entrar
	- Um wait na verdade é um  `lock(..)` seguido de `unlock(...)`
- Depois de efetuar uma **inserção**, é preciso efetuar um `signal` do nitems
- Depois de efetuar um **retrieval** é preciso fazer um `signal` do nslots
	- Em comparação, num semáforo quando faço o up é sempre incrementado o seu valor
- Quando uma _thread_ emite um `signal` relativo a uma variável de transmissão, ela só **emite** quando alguém está à escuta
	- O `wait` só pode ser feito se a FIFO estiver cheia
	- O `signal` pode ser sempre feito
	
É necessário existir a `fifo.empty()` e a `fifo.full()` porque as variáveis de controlo não são semáforos binários.

O valor inicial do **mutex** é 0.


## POSIX support for monitors
A criação e sincronização de _threads_ usa o _Standard POSIX, IEEE 1003.1c_.

O _standard_ define uma API para a **criação** e **sincronização** de _threads_, implementada em Unix pela biblioteca _pthread_

O conceito de monitor **não existe**, mas a biblioteca permite ser usada para criar monitores _Lampsom/Redell_ em C/C++, usando:

- `mutexes`
- `variáveis de condição`

	
As funções disponíveis são:

- `ptread_create`: **cria** uma nova _thread_ (similar ao _fork_)
- `ptread_exit`: equivalente à `exit`
- `ptread_join`: equivalente à `waitpid`
- `ptread_self`: equivalente à `getpid`
- `pthread_mutex_*`: manipulação de **mutexes**
- `ptread_cond_*`: manipulação de **variáveis condicionais**
- `ptread_once`: inicialização

# Message-passing

Os processos podem comunicar entre si usando **mensagens**. 

- Não existe a necessidade de possuírem memória partilhada
- Mecanismos válidos quer para sistemas **uniprocessor** quer para sistemas **multiprocessador**

	
A **comunicação** é efetuada através de **duas operações**:

- `send`
- `receive`

Requer a existência de um **canal de comunicação**. Existem 3 implementações possíveis:

1. **Endereçamento direto/indireto**
2. Comunicação **síncrona/assíncrona**
	- Só o `sender` é que indica o **destinatário**
	- O destinatário **não indica** o `sender`
	- Quando existem **caixas partilhadas**, normalmente usam-se mecanismos com políticas de **round-robin**
		1. Lê o processo $N$
		2. Lê o processo $N+1$
		3. etc...
	- No entanto, outros métodos podem ser usados
3. **Automatic or explicit buffering**

## Direct vs Indirect

### Symmetric direct communication
O processo que pretende comunicar deve **explicitar o nome do destinatário/remetente:**

- Quando o `sender` envia uma mensagem tem de indicar o **destinatário**
	- `send(P, message`
- O destinatário tem de indicar de quem **quer receber** (`sender`)
	- `receive(P, message)`


A comunicação entre os **dois processos** envolvidos é **peer-to-peer**, e é estabelecida automaticamente entre entre um conjunto de processos comunicantes, só existindo **um canal de comunicação**

## Asymmetric direct communications
Só o `sender` tem de explicitar o destinatário:

- `send(P, message`: 
- `receive(id, message)`: receive mensagens de qualquer processo

## Comunicação Indireta
As mensagens são enviadas para uma **mailbox** (caixa de mensagens) ou **ports**, e o `receiver` vai buscar as mensagens a uma `poll`

- `send(M, message`
- `receive(M, message)`


O canal de comunicação possui as seguintes propriedades:

- Só é estabelecido se o **par de processos** comunicantes possui uma **`mailbox` partilhada**
- Pode estar associado a **mais do que dois processos**
- Entre um par de processos pode existir **mais do que um link** (uma mailbox por cada processo)


Questões que se levantam. Se **mais do que um processo** tentar **receber uma mensagem da mesma `mailbox`**...

- ... é permitido?
	- Se sim. qual dos processos deve ser bem sucedido em ler a mensagem?


## Implementação
Existem várias opções para implementar o **send** e **receive**, que podem ser combinadas entre si:

- **blocking send:** o `sender` **envia** a mensagem e fica **bloqueado** até a mensagem ser entregue ao processo ou mailbox destinatária
- **nonblocking send:** o `sender` após **enviar** a mensagem, **continua** a sua execução
- **blocking receive:** o `receiver` bloqueia-se até estar disponível uma mensagem para si
- **nonblocking receiver:** o `receiver` devolve a uma mensagem válida quando tiver ou uma indicação de que não existe uma mensagem válida quando não tiver

## Buffering
O link pode usar várias políticas de implementação:

- **Zero Capacity:** 
	- Não existe uma `queue`
	- O `sender` só pode enviar uma mensagem de cada vez. e o envio é **bloqueante**
	- O `receiver` lê uma mensagem de cada vez, podendo ser bloqueante ou não
- **Bounded Capacity:**
	- A `queue` possui uma capacidade finita
	- Quando está cheia, o `sender` bloqueia o envio até possuir espaço disponível
- **Unbounded Capacity:**
	- A `queue` possui uma capacidade (potencialmente) infinita
	- Tanto o `sender` como o `receiver` podem ser **não bloqueantes**
 
## Bound-Buffer Problem usando mensagens
```c
shared FIFO fifo;			 /* fixed-size FIFO memory */
shared mutex access;		 /* mutex to control mutual exclusion */
shared cond nslots;		 /* condition variable to control availability of slots*/
shared cond nitems;		 /* condition variable to control availability of items */

/* producers - p = 0, 1, ..., N-1 */
void producer(unsigned int p)
{
	DATA data;
	MESSAGE msg;

	forever
	{
		produce_data(&val);
		make_message(msg, data);
		send(msg);
		do_something_else();
	}
}

/* consumers - c = 0, 1, ..., M-1 */
void consumer(unsigned int c)
{
	DATA data;
	MESSAGE msg;

	forever
	{
		receive(msg);
		extract_data(data, msg);
		consume_data(data);
		do_something_else();
	}
}
```

## Message Passing in Unix/Linux
**System V:**

- Existe uma fila de mensagens de **diferentes tipos**, representados por um inteiro
- `send` **bloqueante** se **não existir espaço disponível**
- A recepção possui um argumento para especificar o **tipo de mensagem a receber**:
	- Um tipo específico
	- Qualquer tipo
	- Um conjunto de tipos
- Qualquer que seja a política de recepção de mensagens:
	- É sempre **obtida** a mensagem **mais antiga** de uma dado tipo(s)
	- A implementação do `receive` pode ser **blocking** ou **nonblocking**
- System calls:
	- `msgget`
	- `msgsnd`
	- `msgrcv`
	- `msgctl`

**POSIX**

- Existe uma **priority `queue`**
- `send` **bloqueante** se **não existir espaço disponível**
- `receive` obtêm a mensagem **mais antiga** com a **maior prioridade**
	- Pode ser blocking ou nonblocking
- Funções:
	- `mq_open`
	- `mq_send`
	- `mq_receive`

---
title: Shared Memory

author: Pedro Martins
---

# Shared Memory in Unix/Linux
- É um recurso gerido pelo sistema operativo

Os espaços de endereçamento são **independentes** de processo para processo, mas o **espaço de endereçamento** é virtual, podendo a mesma **região de memória física**(memória real) estar mapeada em mais do que uma **memórias virtuais**

## POSIX Shared Memory
- Criação:
	- `shm_open`
	- `ftruncate`
- Mapeamento:
	- `mmap`
	- `munmap`
- Outras operações:
	- `close`
	- `shm_unlink`
	- `fchmod`
	- ...

## System V Shared Memory
- Criação:
	- `shmget`
- Mapeamento:
	- `shmat`
	- `shmdt`
- Outras operações:
	- `shmctl`

---
title: "Deadlock"
author: Pedro Martins
---

# Deadlock
- **recurso:** algo que um processo precisa para prosseguir com a sua execução. Podem ser:
	- **componentes físicos** do sistema computacional, como:
		- processador
		- memória
		- dispositivos de I/O
		- ...
	- **estruturas de dados partilhadas**. Podem estar definidas 
		- Ao nível do sistema operativo
			- PCT
			- Canais de Comunicação
		- Entre vários processos de uma aplicação

Os recursos podem ser:

- **preemptable:** podem ser retirados aos processos que estão na sua posse por entidades externas
	- processador
	- regiões de memória usadas no espaço de endereçamento de um processo
- **non-preemptable:** os recursos só podem ser libertados pelos processos que estão na sua posse
	- impressoras
	- regiões de memória partilhada que requerem acesso por exclusão mútua

	
O **deadlock** só é importante nos recursos **non-preemptable**.

O caso mais simples de deadlock ocorre quando:

1. O processo $P_0$ pede a posse do recurso $A$
	- É lhe dada a posse do recurso $A$, e o processo $P_0$ passa a possuir o recurso $A$ em sua posse
2. O processo $P_1$ pede a posse do recurso $B$
	- É lhe dada a posse do recurso $B$, e o processo $P_1$ passa a possuir o recurso $B$ em sua posse
3. O processo $P_0$ pede agora a posse do recurso $B$ 
	- Como o recurso $B$ está na posse do processo $P_1$, é lhe negado
	- O processo $P_0$ fica em espera que o recurso $B$ seja libertado para puder continuar a sua execução
	- No entanto, o processo $P_0$ não liberta o recurso $A$
4. O processo $P_1$ necessita do recurso $A$
	- Como o recurso $A$ está na posse do processo $P_0$, é lhe negado
	- O processo $P_1$ fica em espera que o recurso $A$ seja libertado para puder continuar a sua execução
	- No entanto, o processo $P_1$ não liberta o recurso $B$
5. Estamos numa situação de **deadlock**. Nenhum dos processos vai libertar o recurso que está na sua posse mas cada um deles precisa do recurso que está na posse do outro

## Condições necessárias para a ocorrência de deadlock
Existem 4 condições necessárias para a ocorrência de **deadlock:**

1. **exclusão mútua:**
	- Pelo menos um dos recursos fica em posse de um processo de forma não partilhável
	- Obriga a que outro processo que precise do recurso espere que este seja libertado
2. **hold and wait:**
	- Um processo mantêm em posse pelo menos um recurso enquanto espera por outro recurso que está na posse de outro processo
3. **no preemption:**
	- Os recursos em causa são non preemptive, o que implica que só o processo na posse do recurso o pode libertar
4. **espera circular:**
	- é necessário um conjunto de processos em espera tais que cada um deles precise de um recurso que está na posse de outro processo nesse conjunto

Se **existir deadlock**, todas estas condições se verificam. _(A => B)_

Se **uma delas não se verifica**, não há deadlock. _(~B => ~A)_

### O Problema da Exclusão Mútua 
Dijkstra em 1965 enunciou um conjunto de regras para garantir o acesso **em exclusão mútua** por processo em competição por recursos de memória partilhados entre eles.[^1]

1. **Exclusão Mútua:** Dois processos não podem entrar nas suas zonas críticas ao mesmo tempo
2. **Livre de Deadlock:** Se um process está a tentar entrar na sua zona crítica, eventualmente algum processo (não necessariamente o que está a tentar entrar), mas entra na sua zona crítica
3. **Livre de Starvation:** Se um processo está atentar entrar na sua zona crítica, então eventualmente esse processo entra na sua zona crítica
4. **First In First Out:** Nenhum processo a iniciar pode entrar na sua zona crítica antes de um processo que já está à espera do seu turno para entrar na sua zona crítica



[^1]: _"Concurrent Programming, Mutual Exclusion (1965; Dijkstra)"._ Gadi Taubenfeld, The Interdisciplinary Center, Herzliya, Israel

## Jantar dos Filósofos
- 5 filósofos sentados à volta de uma mesa, com comida à sua frente
	- Para comer, cada filósofo precisa de 2 garfos, um à sua esquerda e outro à sua direita
	- Cada filósofo alterna entre períodos de tempo em que medita ou come
- Cada **filósofo** é um **processo/thread** diferente
- Os **garfos** são os **recursos**

Uma possível solução para o problema é:

![Ciclo de Vida de um filósofo](Pictures/philosopher_life.png)

```c
enum {MEDITATING, HUNGRY, HOLDING, EATING};

typedef struct TablePlace
{
	int state;
} TablePlace;

typedef struct Table
{
	Int semid;
	int nplaces;
	TablePlace place[0];
} Table;

int set_table(unsigned int n, FILE *logp);
int get_hungry(unsigned int f);
int get_left_fork(unsigned int f);
int get_right_fork(unsigned int f);
int drop_forks(unsigned int f);
```

Quando um filósofo fica _hungry_:

1. Obtém o garfo à sua esquerda
2. Obtém o garfo à sua direita

A solução **pode sofrer de deadlock:**

1. **exclusão mútua:**
	- Os garfos são partilháveis
2. **hold and wait:**
	- Se conseguir adquirir o `left_fork`, o filósofo fica no estado `holding_left_fork` até conseguir obter o `right_fork` e não liberta o `left_fork`
3. **no preemption:**
	- Os garfos são recursos non preemptive. Só o filósofo é que pode libertar os seus garfos após obter a sua posse e no fim de comer
4. **espera circular:**
	- Os garfos são partilhados por todos os filósofos de forma circular
		- O garfo à esquerda de um filósofo, `left_fork` é o garfo à direita do outro, `right_fork`

Se todos os filósofos estiverem a pensar e decidirem comer, pegando todos no garfo à sua esquerda ao mesmo tempo, entramos numa situação de **deadlock**.

## Prevenção de Deadlock
Se uma das condições necessárias para a ocorrência de deadlock não se verificar, não ocorre deadlock.

As **políticas de prevenção de deadlock** são bastantes **restritas**, **pouco efetivas** e **difíceis de aplicar** em várias situações.

- **Negar a exclusão mútua** só pode ser aplicada a **recursos partilhados**
- **Negar _hold and wait_** requer **conhecimento _a priori_ dos recursos necessários** e considera sempre o pior caso, no qual os recursos são todos necessários em simultâneo (o que pode não ser verdade)
- **Negar _no preemption_**, impondo a libertação (e posterior reaquisição) de recursos adquiridos por processos que não têm condições (aka, todos os recursos que precisam) para continuar a execução pode originar grandes atrasos na execução da tarefa
- **Negar a _circular wait_** pode resultar numa má gestão de recursos

### Negar a exclusão mútua
- Só é possível se os recursos puderem ser partilhados, senão podemos incorrer em **race conditions**
- Não é possível no jantar dos filósofos, porque os garfos não podem ser partilhados entre os filósofos
- Não é a condição mais vulgar a negar para prevenir _deadlock_

### Negar _hold and wait_
- É possível fazê-lo se um processo é obrigado a pedir todos os recursos que vai precisar antes de iniciar, em vez de ir obtendo os recursos à medida que precisa deles
- Pode ocorrer **starvation**, porque um processo pode nunca ter condições para obter nenhum recurso
	- É comum usar _aging mechanisms_ to para resolver este problema
- No jantar dos filósofos, quando um filósofo quer comer, passa a adquirir os dois garfos ao mesmo tempo
	- Se estes não tiverem disponíveis, o filósofo espera no `hungry state`, podendo ocorrer **starvation**

![Negar _hold and wait_](Pictures/hold_and_wait_denial.png)

Solução equivalente à proposta por Dijkstra.

### Negar _no preemption_
- A condição de os recursos serem _non preemptive_ pode ser implementada fazendo um processo libertar o(s) recurso(s) que possui se não conseguir adquirir o próximo recurso que precisa para continuar em execução
- Posteriormente o processo tenta novamente adquirir esses recursos
- Pode ocorrer **starvation** and **busy waiting**
	- podem ser usados _aging mechanisms_ para resolver a starvation
	- para evitar busy waiting, o processo pode ser bloqueado e acordado quando o recurso for libertado
- No janta dos filósofos, o filósofo tenta adquirir o `left_fork`
	- Se conseguir, tenta adquirir o `right_fork`
		- Se conseguir, come
		- Se não conseguir, liberta o `left_fork` e volta ao estado `hungry`


![Negar a condição de  _no preemption_ dos recursos](Pictures/no_preempt_denial.png)


### Negar a espera circular
- Através do uso de IDs atribuídos a cada recurso e impondo uma ordem de acesso (ascendente ou descendente) é possível evitar sempre a espera em círculo
- Pode ocorrer **starvation**
- No jantar dos filósofos, isto implica que nalgumas situações, um dos filósofos vai precisar de adquirir primeiro o `right_fork` e de seguida o `left_fork`
	- A cada filósofo é atribuído um número entre 0 e N
	- A cada garfo é atribuído um ID (e.g., igual ao ID do filósofo à sua direita ou esquerda)
	- Cada filósofo adquire primeiro o garfo com o menor ID
	- obriga a que os filósofos 0 a N-2 adquiram primeiro o `left_fork` enquanto o filósofo N-1 adquirir primeiro o `right_fork`

![Negar a condição de espera circular no acesso aos recursos](Pictures/circular_wait_denial.png)

 
## Deadlock Avoidance
Forma menos restritiva para resolver situações de deadlock, em que **nenhuma das condições necessárias à ocorrência de deadlock é negada**. Em contrapartida, o sistema é **monitorizado continuamente** e um recurso **não é atribuído** se como consequência o sistema entrar num **estado inseguro/instável**

Um estado é considerado seguro se existe uma sequência de atribuição de recursos na qual todos os processos possa terminar a sua execução (não ocorrendo _deadlock_). 

Caso contrário, poderá ocorrer deadlock (pode não ocorrer, mas estamos a considerar o pior caso) e o estado é considerado inseguro.

Implica que:

- exista uma lista de todos os recursos do sistema
- os processos intervenientes têm de declarar _a priori_ todas as suas necessidades em termos de recursos


### Condições para lançar um novo processo
Considerando:

- $NTR_i$ - o número total de recursos do tipo `i` _(i = 0, 1, ..., N-1)_
- $R_{i, j}$: o número de recursos do tipo `i` requeridos pelo processo `j`, _(i=0, 1, ..., N-1 e j=0, 1, ..., M-1)_

O sistema pode impedir um novo processo, `M`, de ser executado se a sua terminação não pode ser garantida. Para que existam certezas que um novo processo pode ser terminado após ser lançado, tem de se verificar:

$$ NTR_i \geq R_{i, M} + \sum_{j=0}^{M-1} R_{i, j} $$

 
### Algoritmo dos Banqueiros
Considerando:

- $NTR_i$: o número total de recursos do tipo `i` _(i = 0, 1, ..., N-1)_
- $R_{i, j}$: o número de recursos do tipo `i` requeridos pelo processo `j`, _(i=0, 1, ..., N-1 e j=0, 1, ..., M-1)_
- $A_{i, j}$: o número de recursos do tipo `i` atribuídos/em posse do processo `j`, _(i=0, 1, ..., N-1 e j=0, 1, ..., M-1)_

Um novo recurso do tipo `i` só pode ser atribuído a um processo **se e só se** existe uma sequência $j'=f(i, j)$ tal que:

$$ R_{i, j'} - A_{i, j'}  < \sum_{k \geq ji'}^{M-1} A_{i, k} $$

\begin{table}[H]
\renewcommand{\arraystretch}{1.3}
\centering
\caption{Banker's  Algorithm Example} 
\begin{tabular}{cccccc}
						   &                            & A & B & C & D \\ \cline{3-6} 
						   & \multicolumn{1}{c}{total}  & 6 & 5 & 7 & 6 \\
						   & \multicolumn{1}{c}{free}   & 3 & 1 & 1 & 2 \\ \toprule
\multirow{3}{*}{maximum}   & p1                         & 3 & 3 & 2 & 2 \\
						   & p2                         & 1 & 2 & 3 & 4 \\
			 			   & p3                         & 1 & 3 & 5 & 0 \\ \midrule
\multirow{3}{*}{granted}   & p1                         & 1 & 2 & 2 & 1 \\
						   & p2                         & 1 & 0 & 3 & 3 \\
						   & p3                         & 1 & 2 & 1 & 0 \\ \midrule
\multirow{3}{*}{needed}    & p1                         & 2 & 1 & 0 & 1 \\
						   & p2                         & 0 & \cellcolor{blue!25}2 & 0 & 1 \\
						   & p3                         & 0 & 1 & \cellcolor{blue!25}4 & 0 \\ \midrule
\multirow{3}{*}{new Grant} & p1                         & 0 & 0 & 0 & 0 \\
						   & p2                         & 0 & 0 & 0 & 0 \\
						   & p3                         & 0 & 0 & 0 & 0 \\ \bottomrule
\end{tabular}
\end{table}

Para verificar se posso atribuir recursos a um processo, aos recursos `free` subtraio os recursos `needed`, ficando com os recursos que sobram. Em seguida simulo o que aconteceria se atribuisse o recurso ao processo, tendo em consideração que o processo pode usar o novo recurso que lhe foi atribuído sem libertar os que já possui em sua posse (estou a avaliar o pior caso, para garantir que não há deadlock)

Se o processo **p3** pedir 2 recursos do tipo C, o **pedido é negado**, porque **só existe 1 disponível**

Se o processo **p3** pedir 1 recurso do tipo B, o **pedido é negado**, porque apesar de existir 1 recurso desse tipo disponível, ao **longo da sua execução processo vai necessitar de 4** e só **existe 1 disponível**, podendo originar uma situação de **deadlock**, logo o **acesso ao recurso é negado**

#### Algoritmo dos banqueiros aplicado ao Jantar dos filósofos
- Cada filósofo primeiro obtém o `left_fork` e depois o `right_fork`
- No entanto, se um dos filósofos tentar obter um `left_fork` e o filósofo à sua esquerda já tem na sua posse um `left_fork`, o acesso do filósofo sem garfos ao `left_fork` é negado para não ocorrer **deadlock**

![Algoritmo dos banqueiros aplicado ao Jantar dos filósofos](Pictures/bankers_philosophers.png)


## Deadlock Detection
**Não são usados mecanismos nem para prevenir nem par evitar o deadlock**, podendo ocorrer situações de deadlock:

- O estado dos sistema deve ser examinado para determinar se ocorreu uma situação de deadlock
	- É preciso verificar se existe uma **dependência circular de recursos** entre os processos
	- Periodicamente é executado um algoritmo que verifica o estado do registo de recursos:
		- recursos `free` vs recursos `granted` vs recursos `needed`
	- Se tiver ocorrido uma situação de deadlock, o SO deve possuir uma **rotina de recuperação** de situações de deadlock e executá-la
- Alternativamente, de um ponto de vista "arrogante", o problema pode ser ignorado


Se **ocorrer uma situação de deadlock**, a rotina de recuperação deve ser posta em prática com o objetivo de interromper a dependência circular de processos e recursos.

Existem três métodos para recuperar de deadlock:

- **Libertar recursos de um processo**, se possível
	- É atividade de um processo é suspensa até se puder devolver o recurso que lhe foi retirado
	- Requer que o estado do processo seja guardado e em seguida recarregado
	- Método eficiente
- **Rollback**
	- O estado de execução dos diferentes processos é guardado periodicamente
	- Um dos processos envolvidos na situação de deadlock é _rolled back_ para o instante temporal em que o recurso lhe foi atribuído
	- A recurso é assim libertado do processo
- **Matar o processo**
	- Quando um processo entra em deadlock, é terminado
	- Método radical mas fácil de implementar

	
Alternativamente, existe sempre a opção de não fazer nada, entrando o processo em deadlock. Nestas situações, o utilizador é que é responsável por corrigir as situações de deadlock, por exemplo, terminando o programa com `CTRL + C`


