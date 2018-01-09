# Processor Scheduling
A execução de um processo é uma sequência alternada de períodos de:

- `CPU burst`, causado pela execuçao de intruções do CPU 
- `I/O burst`, causados pela espera do resultado de pedidos a dispositivos de I/O


O processo pode então ser classificado como:

- `I/O bound` se possuir muitos e curtos `CPU bursts`
- `CPU bound` se possuir poucos e longos `CPU bursts`


O objetivo da **multiprogramação** é obter vantagem dos períodos de `I/O burst` para permitir outros processos terem acesso ao processador. A componente do sistema responsável por esta gestão é o `scheduler`.

A funcionalidade principal do `scheduler` é decidir da `poll` de processos prontos para serem executados que coexistem no sistema:

- quais é que devem ser executados?
- quando?
- por quanto tempo?
- porque ordem?


## Scheduler
Revisitando o diagrama de estados do processador, identificamos três `schedulers`

![Identificação dos diferentes tipos de schedulers no diagrama de estados dos processos](../Pictures/scheduling.png)

### Long-Term Scheduling
Determina que **programas são admitidos para serem processados**:

- Controla o **grau de multiprogramação** do sistema 
- Se um programa do utilizador ou `job` for aceite, torna-se um **processo** e é adicionado à **_queue_ de processos `ready` em fila de espera**
	- Em princípio é adicionado à `queue` do `short-term scheduler`
	- mas também é possível que seja adicionada à `queue` do `medium-term scheduler`
- Pode colocar processos em `suspended ready`, libertando quer a memória quer a fila de processos

### Medium Term Scheduling
Gere a `swapping area`

- As decisões de `swap-in` são **controladas pelo grau de multiprogramação**
- As decisões de `swap-in` são **condicionadas pela gestão de memória**


### Short-Term Scheduling
Decide qual o **próximo processo a executar**

- É invocado quando existe um evento que:
	- **bloqueia o processo atual**
	- **permite que este seja `preempted`**
- Eventos possíveis são:
	- interrupção de relógio
	- interrupção de I/O
	- `system calls`
	- signal (e.g. através de semáforos)

## Critérios de Scheduling 

### User oriented

**Turnaround Time:**

- Intervalo de de tempo entre a submissão de um processo até à sua conclusão
- Inclui:
	- Tempo de execução enquanto o processo tem a posse do CPU
	- Tempo dispendido à espera pelos recursos que precisa (inclui o processador)
- Deve ser minimizado em sistemas `batch`
- É a medida apropriada para um `batch job`


**Waiting Time:**

- Soma de todos os períodos de tempo em que o processo esteve à espera de ser colocado no estado `ready`
- Deve ser minimizado

**Response Time:**

- Intervalo de tempo que decorre desde a submissão de um pedido até a resposta começa a ser produzida
- Medida apropriada para sistemas/processos interativo
- Deve ser minimizada para este tipo de sistemas/processos
- O número de processos interativos deve ser máximizado desde que seja garantido um tempo de resposta aceitável


**Deadlines:**

- Tempo necessário para um processo terminar a sua execução
- Usado em sistemas de tempo real
- A percentagem de `deadlines` atingidas deve ser maximizada, mesmo que isso implique subordinar/reduzir a importância de outras objetivos/parâmetros do sistema

**Predictability:**

- Quantiza o impacto da carga (de processos) no tempo de resposta dos sistema
- Idealmente, um `job` deve correr no **mesmo intervalo de tempo** e gastar os **mesmos recursos de sistema** independentemente da carga que o sistema possui


### System oriented

**Fairness:**

- Igualdade de tratamento entre todos os processos
- Na ausência de diretivas que condicionem os processos a atender, deve ser efetuada um gestão e partilha justa dos recursos, onde todos os processos são tratados de forma equitativa
- Nenhum processo pode sofrer de `starvation`


**Throughput:**

- Medida do número de processos completados por unidade de tempo ("taxa de transferência" de processos)
- Mede a quantidade de trabalho a ser executada pelos processos
- Deve ser máximizado
- Depende do tamanho dos processos e da **política de escalonamento**


**Processor Utilization:**

- Mede a percentagem que o processador está ocupado
- Deve ser maximizada, especialmente em sistemas onde predomina a partilha do processador


**Enforcing Priorities:**

- Os processos de **maior prioridade** devem ser sempre favorecidos em detrimento de processos menos prioritários

\begin{center}
{\large É \textbf{impossível favorecer todos os critérios em simultâneo}}

Os \textbf{critérios a favorecer} dependem da \textbf{aplicação específica}
\end{center}


## Preemption & Non-Preemption

**Non-preemptive scheduling:**

- O processo mantêm o processador até este ser bloqueado ou terminar
- As **transições são sempre por `time-out`**
- Não existe `preempt`
- Típico de sistemas `batch`
	- Não existem deadlines nem limitações temporais restritas a cumprir

**Preemptive Scheduling:**

- O processo pode **perder o processador devido a eventos externos**
	- esgotou o seu `time-quantum`
	- um processo **mais prioritário** está `ready`
	- Tipico de **sistemas interativos**
		- É preciso garantir que a resposta ocorre em intervalos de tempo limitados
		- É preciso "simular" a ideia de paralelismo no `macro-tempo`
	- Sistemas em tempo real são `preemptive` porque existem `deadlines` **restritas** que precisam de ser cumpridas
	- Nestas situações é importante que um **evento externo** tenha capacidade de libertar o processador


## Scheduling

### Favouring Fearness

![Espaço de endereçamento de um processo em Linux](../Pictures/favouring_fearness.png)

Todos os processos **são iguais** e são atendidos por **ordem de chegada**

- É implementado usando **FIFOs**
- Pode existir mais do que um processo à espera de eventos externos
- Existe uma fila de espera para cada evento
- Fácil de implementar
- Favorece processos `CPU-bound` em detrimento de processos `I/O-bound`
	- Só necessitam de acesso ao processador, não de recursos externos
	- Se for a vez de um processo `I/O-bound` ser atendido e não possuir os recursos de I/O que precisa tem de voltar para a fila
- Em **sistemas interativos**, o `time-quantum` deve ser escolhido cuidadosamente para obter um bom compromisso entre `fairness` e `response time`

Em função do scheduling pode ser definido como:

- `non-preemptive scheduling` $\implies$ `first come, first-served` (FCFS)
- `preemptive scheduling` $\implies$ `round robin`

### Priorities
![Espaço de endereçamento de um processo em Linux](../Pictures/scheduling_priorities.png)

Segue o princípio de que atribuir a mesma importância a todos os processos pode ser uma solução errada. Um sistema injusto _per se_ não é necessariamente mau.

- A **minimização do tempo de resposta** (`response time`) exigue que os processos `I/O-bound` sejam **priveligiados**
- Em **sistemas de tempo real**, os processos associados a **eventos/alarmes** e **ações do sistema operativo** sofrem de várias **limitações e exigências temporais**

	 
Para resolver este problema os processos são **agrupados** em grupos de **diferentes prioridades**

- Processos de maior prioridade são executados primeiros
- Processos de menor prioridade podem sofrer `starvation`

#### Prioridades Estáticas
As prio ridades a atribuir a cada processo são determinadas _a priori_ de forma **determinística**

- Os processos são **agrupados em classes de prioridade fixa**, de acordo com a sua importância relativa
- Existe risco de os processos menos prioritários sofrerem `starvation`
	- Mas se um **processo de baixa prioridade não é executado** é porque o sistema foi **mal dimensionado**
- É o sistema de `scheduling` mais injusto
- É usado em sistemas de tempo real, para garantir que os processos que são críticos são sempre executados


Alternativamente, pode se fazer:

1. Quando um processo é criado, é lhe **atribuído um dado nível de prioridade**
2. Em `time-out` a prioridade do processo é **decrementada**
3. Na ocorrência de um `wait event` a prioridade é **incrementada**
4. Quando o valor de **prioridade atinge um mínimo**, o valor da prioridade sofre um `reset`
	- É colocada no valor inicial, garantindo que o processo é executado
	
Previnem-se as situações de `starvation` impedindo que o processo não acaba por ficar com uma prioridade tão baixa que nunca mais consegue ganhar acesso

#### Prioridades Dinãmicas
- As classes de prioridades estão definidas de forma funcional _a priori_
- A mudança de um processo de classe é efetuada com base na utilização última janela de execução temporal que foi atribuída ao processo


Por exemplo:

- **Prioridade 1:** `terminais`
	- Um processo entra nesta categoria quando se efetua a transição `event occurs` (evento de escrita/leitura de um periférico) quando estava à espera de dados do `standard input device`
- **Prioridade 2:** `generic I/O`
	- Um processo entra nesta categoria quando efetua a transição `event occurs` se estava à espera de dados de **outro tipo de input device** que não o `stdin`
- **Prioridade 3:** `small time quantum`
	- Um processo entra nesta classe quando ocorre um `time-out`
- **Prioridade 4:** `large time quantum`
	- Um processo entra nesta classe após um sucessivo número de `time-outs`
 	- São claramente processos `CPU-bound` e o objetivo é atribuir-lhes janelas de execução com grande `time quantum`, mas menos vezes


##### Shortest job first (SJF) / Shortest process next (SPN)
Em sistemas `batch`, o `turnaround time` deve ser minimizado.

Se forem conhecidas **estimativas do tempo de execução** _a priori_, é possível estabelecer uma **ordem de execução** dos processos que **minimizam o tempo de `turnaround` médio** para um dado grupo de processos

Assumindo que temos N `jobs` e que o tempo de execução de cada um deles é $te_n$, com $n = 1, 2, ..., N$. O `average turnaround time` é:
$$t_m = te_1 + \frac{N-1}{N} \cdot te_2 + ... + \frac{1}{N} \cdot te_N$$

onde $t_m$ é o `turnaround time` mínimo se os `jobs` forem sorteados por ordem ascendente de tempo de execução (estimado)


Para **sistemas interativos**, podemos usar um sistema semelhante:

- Estimamos a taxa de ocupação da próxima janela de execução baseada na taxa de ocupação das janelas temporais passadas
- Atribuímos o processador ao processo cuja estimativa for a **mais baixa**


Considerando $fe_1$ como sendo a **estimativa da taxa de ocupação** da primeira janela temporal atribuída a um processo e $f_1$ a fração de tempo efetivamente ocupada:

- A estimativa da segunda fração de tempo necessária é 
  $$fe_2 = a \cdot fe_1 + (1-a) \cdot f_1$$
- A estimativa da e-nésima fração de tempo necessária é:
$$ fe_N = a \cdot fe_{N-1} + (1-a) \cdot f_{N-1}$$
  Ou alternativamente:
$$a^{N-1} \cdot fe_1 + a^{N-2} \cdot(1-a) \cdot fe_2 + a \cdot (1-a) \cdot fe_{N-2} + (1-a) \cdot fe_{N-1}$$ 


Com $a \in [0, 1]$, onde $a$ é um coeficiente que representa o peso que a história passada de execução do processo influencia a estimativa do presente

Esta alternativa levanta o problema que que processos `CPU-bound` podem sofrer de `starvation`. Este problema pode ser resolvido contabilizando o tempo que um processo está em espera (`aging`) enquanto está na fila de processos `ready`

Normalizando esse tempo em função do período de execução e denominando-o $R$, a **prioridade** de um processo pode ser dada por:
$$p = \frac{1+b \cdot R}{fe_N}$$
onde $b$ é o coeficiente que **controla o peso do aging** na fila de espera dos processos `ready`


## Scheduling Policies

### First Come, First Serve (FCFS)
Também conhecido como `First In First Out` (FIFO). O processo mais antigo na fila de espera dos processos `ready` é o primeiro a ser selecionado.

- `Non-preemptive` (em sentido estrito), podendo ser combinado com um esquema de prioridades baixo
- Favorece processos `CPU-bound` em detrimento de processos `I/O-bound`
- Pode resultar num **mau uso** do processador e dos dispositivos de I/O
- Pode ser utilizado com **low priority schemas**

![Problema de Scheduling](../Pictures/processor_scheduling_problem.pdf)

Usando uma política de `first come first serve`, o resultado do scheduling do processador é:

![Política FCFS](../Pictures/processor_scheduling_solution_FCFS.pdf)

- O P1 começa a usar o CPU. 
- Como é um sistema FCFS, o processo 1 só larga o CPU passado 3 ciclos. 
- O processo P2 é o processo seguinte na fila `ready`, e ocupa o CPU durante 10 ciclos.
- Quando P2 termina, P1 é o processo que está à mais tempo à espera, sendo ele que é executado
- Quando P2 abandona voluntariamente o CPU, o processo P1 corre os seus primeiros dois ciclos
- Quando P3 liberta o CPU, o processo P1 termina os últimos 3 ciclos que precisa
- Quando P3 liberta o CPU, o processo P1 como é `I/O-bound` e precisa de 5 ciclos para o dispositivo estar pronto fica mais dois ciclos à espera para puder terminar executando o seu último ciclo

### Round-Robin
- `Preemptive`
	- O `scheduler` efetua a gestão baseado num `clock`
	- A cada processo é atribuído um `time-quantum` máximo antes de ser `preempted`
- O processo **mais antigo** em `ready` é o **primeiro a ser selecionado** 
	- não são consideradas prioridades
- Efetivo em sistemas `time sharing` com objetivos globais e sistemas que processem transações
- **Favorece** `CPU-bound` em detrimento de processos `I/O-bound`
- Pode resultar num **mau uso de dispositivos I/O**


Na escolha/otimização do `time quantum` existe um **tradeoff:**

- **tempos muito curtos** favorecem a execução de **processos pequenos**
	- estes processos vão ser executados **rapidamente**
- **tempos muito curtos** obrigam a `processing overheads` devido ao `process switching` **intensivo**

Para os processos apresentados acima, o diagrama temporal de utilização do processador, para um `time-quantum` de 3 ciclos é:


![Política Round-Robin](../Pictures/processor_scheduling_solution_RR.pdf)

A história de processos em `ready` em fila de espera é: 2, 1, 3, 2, 1, 2, 3, 1


### Shortest Process Next (SPN) ou Shortest Job First (SJF)
- `Non-preemptive`
- O process com o `shortest CPU burst time` (menor tempo espectável de utilização do CPU) é o **próximo a ser selecionado**
	- Se vários processos tem o **mesmo tempo de execução** é usado FCFS para desempatar
- Existe um **risco de `starvation`** para grandes processos
	- o seu acesso ao CPU pode ser **sucessivamente adiado** se existir "forem existindo" processos com **tempo de execução menor**
- Normalmente é usado em escalonamento de logo prazo, `long-term scheduling` em sistemas `batch`, porque os utilizadores esperam estimar com precisão o tempo máximo que o processo necessita para ser executado


### Linux

No Linux existem 3 classes de prioridades:


1. **FIFO**, `SCHED_FIFO`
	- `real-time threads`, com politíca de prioridades
	- uma `thread` em execução é `preempted` apenas se um processo de **mais alta prioridade da mesma classe** transita para o estado `ready`
	- uma `thread` em execução pode **voluntariamente abandonar o processador**, executando a primitiva `sched_yeld`
	- dentro da mesma classe de prioridade a política escolhida é `First Come, First Serve` (FCFS)
	- Só o `root` é que pode lançar processos em modo FIFO
2. **Round-Robin real time threads**,  `SCHED_RR`
	- `threads` com prioridades com necessidades de execução em tempo real
	- Processos nesta classe de prioridades são `preempted` se o seu `time-quantum` termina
3. **Non real time threads**, `SCHED_OTHER`
	- Só são executadas se não existir nenhuma `thread` com necessidades de execução em tempo real
	- Está associada à processos do utilizador
	- A política de escalonamento tem mudado à medida que a são lançadas novas versões do _kernel_


A **escala de prioridades** varia

- 0 a 99 para `real-time threads`
- 100 a 139 para as restantes

Para lançar uma `thread` (sem necessidades de execução em tempo real) com diferentes prioridades, pode ser usado comando `nice`.

Por _default_, o comando lança uma `thread` com prioridade 120. O comando aceita um `offset` de [-20, +19] para obter a prioridade mínima ou máxima.

#### Algoritmo Tradicional
- Na classe `SCHED_OTHER` as prioridades são baseadas em **créditos**
- Os créditos do processo em execução são **decrementados** à medida que ocorre uma interrupção do `real time clock`
- O processo é `preempted` quando são atingidos zero créditos
- Quando todos os processos `ready` têm zero créditos, os créditos de **todos os processos** (incluindo os que estão bloqueados) são **recalculados** segundo a fórmula:
	$$CPU_j(i) = \frac{CPU_j(i-1)}{2} + PBase_j + nice_j$$
onde são tido em conta a **história passada de execução do processo** e as **prioridades**
- O `response time` de processos `I/O-bound` é minimizado
- A `starvation` de processos `CPU-bound` é evitada
- Solução **não adequada para múltiplos processadores** e é má se o número de processos é elevado


## Novo Algoritmo
- Os processos na classe `SCHED_OTHER` passam a usar um `completely fair scheduler (CFS)`
- O scheduling é baseado no `vruntime`, _virtual run time_, que mede durante quanto tempo uma `thread` esteve em execução
	- o `virtual run time` está relacionado quer com o **tempo de execução real** (`physical run time`) e a **prioridade** da `thread`
	- Quanto maior a prioridade de um processo, menor o `physical run time`
- O `scheduler` seleciona as `threads` com menor `virtual run time`
	- Uma `thread` com prioridade mais elevada que fique pronta a ser executada pode "forçar" um `preempt` uma `thread` com menor prioridade
		- Assim é possível que uma `thread I/O bound` "forçar" o processador a `preempt` um processo `CPU-bound`
- O algoritmo é implementado com base numa `red-black tree` do processador


