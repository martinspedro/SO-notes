---
title: "Deadlock"
author: Pedro Martins
---

# Deadlock
- **recurso:** algo que um processo precisa para proseeguir com a sua execução. Podem ser:
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
	- Os recursos em causa são non-preemptive, o que implica que só o processo na posse do recurso o pode libertar
4. **espera circular:**
	- é necessário um conjunto de processos em espera tais que cada um deles precise de um recurso que está na posse de outro processo nesse conjunto

Se **existir deadlock**, todas estas condições se verificam. _(A => B)_

Se **uma delas não se verifica**, não há deadlock. _(~B => ~A)_

 
## Jantar dos Filósofos
- 5 filósofos sentados à volta de uma mesa, com comida à sua frente
	- Para comer, cada filósofo precisa de 2 garfos, um à sua esquerda e outro à sua direita
	- Cada filósofo alterna entre períodos de tempo em que medita ou come
- Cada **filósofo** é um **processo/thread** diferente
- Os **garfos** são os **recursos**

Uma possível solução para o problema é:

![Ciclo de Vida de um filósofo](../Pictures/philosopher_life.png)

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
	- Os garfos são recursos non-preemptive. Só o filósofo é que pode libertar os seus garfos após obter a sua posse e no fim de comer
4. **espera circular:**
	- Os garfos são partilhados por todos os filósofos de forma circular
		- O garfo à esquerda de um filósofo, `left_fork` é o garfo à direita do outro, `right_fork`

Se todos os filósofos estiverem a pensar e decidirem comer, pegando todos no garfo à sua esquerda ao mesmo tempo, entramos numa situação de **deadlock**.

## Prevenção de Deadlock
Se uma das condições necessárias para a ocorrência de deadlock não se verificar, não ocorre deadlock.

As **políticas de prevenção de deadlock** são bastantes **restritas**, **pouco efetivas** e **difíceis de aplicar** em várias situações.

- **Negar a exclusão mútua** só pode ser aplicada a **recursos partilhados**
- **Negar _hold-and-wait_** requer **conhecimento _a priori_ dos recursos necessários** e considera sempre o pior caso, no qual os recursos são todos necessários em simultâneo (o que pode não ser verdade)
- **Negar _no preemption_**, impondo a libertação (e posterior re-aquisição) de recursos adquiridos por processos que não têm condições (aka, todos os recursos que precisam) para continuar a execução pode originar grandes atrasos na execução da tarefa
- **Negar a _circular wait_** pode resultar numa má gestão de recursos

### Negar a exclusão mútua
- Só é possível se os recursos puderem ser partilhados, senão podemos incorrer em **race conditions**
- Não é possível no jantar dos filśofos, porque os garfos não podem ser partilhados entre os filósofos
- Não é a condição mais vulgar a negar para prevenir _deadlock_

### Negar _hold-and-wait_
- É possível fazê-lo se um processo é obrigado a pedir todos os recursos que vai precisar antes de iniciar, em vez de ir obtendo os recursos à medida que precisa deles
- Pode ocurrer **starvation**, porque um processo pode nunca ter condições para obter nenhum recurso
	- É comum usar _aging mechanisms_ to para resolver este problema
- No jantar dos filósofos, quando um filósofo quer comer, passa a adquirir os dois garfos ao mesmo tempo
	- Se estes não tiverem disponíveis, o filósofo espera no `hungry state`, podendo ocorrer **starvation**

![Negar _hold-and-wait_](../Pictures/hold_and_wait_denial.png)

Solução equivalente à proposta por Dijkstra.

### Negar _no preemption_
- A condição de os recursos serem _non-preemptive_ pode ser implementada fazendo um processo libertar o(s) recurso(s) que possui se não conseguir adquirir o próximo recurso que precisa para continuar em execução
- Posteriormente o processo tenta novamente adquirir esses recursos
- Pode ocorrer **starvation** and **busy waiting**
	- podem ser usados _aging mechanisms_ para resolver a starvation
	- para evitar busy waiting, o processo pode ser bloqueado e acordado quando o recurso for libertado
- No janta dos filósofos, o filósofo tenta adquirir o `left_fork`
	- Se conseguir, tenta adquirir o `right_fork`
		- Se conseguir, come
		- Se não conseguir, liberta o `left_fork` e volta ao estado `hungry`


![Negar a condição de  _no preemption_ dos recursos](../Pictures/no_preempt_denial.png)


### Negar a espera circular
- Através do uso de IDs atribuídos a cada recurso e impondo uma ordem de acesso (ascendente ou descendente) é possível evitar sempre a espera em círculo
- Pode ocorrer **starvation**
- No jantar dos filósofos, isto implica que nalgumas situações, um dos filśofos vai precisar de adquirir primeiro o `right_fork` e de seguida o `left_fork`
	- A cada filósofo é atribuído um número entre 0 e N
	- A cada garfo é atribuído um ID (e.g., igual ao ID do filósofo à sua direita ou esquerda)
	- Cada filśofo adquire primeiro o garfo com o menro ID
	- obriga a que os filósofos 0 a N-2 adquiram primeiro o `left_fork` enquanto o filósofo N-1 adquir primeiro o `right_fork`

![Negar a condição de espera circular no acesso aos recursos](../Pictures/circular_wait_denial.png)

 
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

O sistema pode impedir um novo processo, `M`, de ser exectuado se a sua terminação não pode ser garantida. Para que existam certezas que um novo processo pode ser terminado após ser lançado, tem de se verificar:

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

![Algoritmo dos banqueiros aplicado ao Jantar dos filósofos](../Pictures/bankers_philosophers.png)


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




	


-
