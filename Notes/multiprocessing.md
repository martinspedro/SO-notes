# 6 Oct 2017
**Multiprocessing**

## Program vs process
- Todos os programas tem acesso à memoria 0.
- A memória é virtual: todos os processos pensam que têm acesso à memoria completa
	- Cabe ao OS alocar as necessidades de memoria de um processo para a memoria física

## Programa vs Processo
| Programa | Processo |
|:--------:|:--------:|
| conjunto de isntruções que descrevem como uma tarefa deve ser executada por um computador | uma entidade que representa um programa a ser executado|


## Execution in a multiprogrammed environment
- Quando o sistema assume que o processo qe está na posse do processador irá ser interrompido por uma interrupção para puder executar outro processo e dar a ideia em macro tempo de simultanedade
	- O OS deve guardar o contexto de execução do processo:
		- valor dos registos internos
		- valor das variáveis
		- insrução a ser executada
	- In multiprogramming, the activity of the processor, because it is switching back and forth from process to process is hard to perceive

Num dado momento, o processador que está on é desligado e é considerado que é ligado outro processador para seguir com o novo processo
	- Turn off implica guardar tudo (contexto de execução)
	- Turn on implica carregar novamente todo o contexto de execução quando o programa foi interrompido

### Process Model
To be viable, this process model requires that: \
- the execution of any process is not affected by th instant in time or the location in the code where the switching takes place
- no restricitions are imposed on the total or partial execution times of any process

## State Diagram of a process
Um processo pode estar em diferentes situações (_states_): \
- **run** : o processo está na posse do processador
- **blocked**: o processo está à espera que ocorra um evento externo:
	- acesso a um recurso
	- termino de um operação de input/output
- **ready**: o processo está pronto a correr, mas está à espera que o processador lhe dê a ordem de _start/resume_ para retomar a sua exceução

As transições entre estados normalmente resultam de intervenção externa, mas em alguns casos podem ser iniciadas pelo processo:
	- printf
	- scanf


- mesmo que um **processo não abandone o processador**
	- um while(1) não corre _ad eternum_ 
	- um processador multiprocess só permite que este programa corre quando for permitido pelo CPU

## State Diagram of a process
```c
// Inserir imagem do state diagram (slides)
```

- **dispatch**: O processo que estava em modo _run_ deixou de o estar. É preciso dar o CPU a processos disponiveis. Compete à função de dispatch escolher. Pode ser feita por:
		- um sistema de prioridades
		- requesitos temporais
		- aletoriedade
		- divisao igaul do CPU
- **event wait**: O processo sai do estado _run_ por iniciativa do proprio processo. _scanf_, _printf_ são exemplos válidos. O CPU vai guardar o estado da execução
- **event occurs**: O evento que o processo esperava aconteceu. O processo passa para a fila de espera àte ser novamente atendido
- **time-out**: foi atribuida uma janela temporal ao processo (_time quantum_). Há uma interrupção gerada por hardware. O sistema operativo vai forçar a saida do processo. A transição por time-out ocorre em qualquer momento do mue codigo. O sistema pode ter tempos diferentes. O tim quantum pode nao ser igual em dois sistemas.
- **preempt**: O processo que está a correr tem uma prioridade mais baixa do que um processo que acordou e está pronto a correr. Não faz sentido continaur a correr um processo de baixa prioridade, pelo que este é interrompdo e é executado o processo de maior prioridade

Deve ser considerao que:
- A memória é finita
	- Limita o numero de processos coexistentes

- **Swap out**: a imagem do processo é passada para partição de swap. Se o processo estiver na memória swap há muito tempo pode ser swaped in, e substituido por um
- **swap in**: a imagem do processo é carreagda da memória principal para 



Novas transições devido à memória _swap_: \
- **suspend**: O processo está _swapped out_ \
- **activate**: O processo está _swapped in_ \


Dois novos estados para representar a criação e destruição de um processo: \
- **new**: o processo foi criado mas não foi admitido para a _pool_ de processo executáveis (the process data strucuture is been initialized) \
- **terminated**: o processo foi removido da _pool_ do processo executável (existem algumas que devem ser tomadas entre \




-----

# 10 Oct

- quando se dá o fork o processo é o clonado e é executado o mesmo programa em dois processos diferentes
- O program counter mantêm, logo ambos os processos executam o mesmo código
- Não existem garantias que o pai execute primeiro que o filho. 
- Depende do time quantum que o processo pai ocupa antes do fork
- Ambiente multiprogramado - os dois programas correm em simultaneo no microtempo

PID - ID processo  corrente
PPID - ID pai processo corrente

- Podem ocorrer fork failure
- 





