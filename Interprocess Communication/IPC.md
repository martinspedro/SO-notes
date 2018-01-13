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

