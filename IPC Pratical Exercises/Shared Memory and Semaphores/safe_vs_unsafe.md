# Race conditions no acesso a uma estrutura de dados partilhada
```bash
# concurrent programming with unsafe increment
./incrementer_unsafe
Launching 5 processes, each one performing 1000 increments
Process 19982 launched
Process 19983 launched
Process 19984 launched
Process 19985 launched
Process 19986 launched
Waiting for processes to return
Process 19982 returned
Process 19983 returned
Process 19984 returned
Process 19985 returned
Process 19986 returned
final values = (4999, 4988)

# safe increment
./incrementer_safe
Launching 5 processes, each one performing 1000 increments
Process 20006 launched
Process 20007 launched
Process 20008 launched
Process 20009 launched
Process 20010 launched
Waiting for processes to return
Process 20006 returned
Process 20007 returned
Process 20008 returned
Process 20009 returned
Process 20010 returned
final values = (5000, 5000)
```

## Ferramentas de monitorização da memória partilhada
A memória partilhada fica na localização

```bash
/dev/shm/
```

```bash
# Lista os segmentos de memória partilhada
ipcs -m

# Listar os processos a usar memória partilhada
ipcs -pm

# Apagar shared memory
ipcrm
```

## Diferenças entre o incremento safe vs unsafe
 
### Includes
O inclusão de `sem.h` permite 

```c
#include <sys/shm.h>                                            #include <sys/shm.h>
#include <sys/sem.h>                                          <
```

### Tempo de Incremento
```c
#define INC_TIME 500                                          | #define INC_TIME 1000
```

### Internal Data Strucuture
A estrutura de memória partilhada passa a ter um semáforo implementado, usando a variável `semid` para controlo.

```c
/* Internal data structure */                                   /* Internal data structure */
typedef struct                                                  typedef struct
{                                                               {
    int var1, var2;                                                 int var1, var2;
    int semid;                                                <
} SharedDataType;                                               } SharedDataType;
```
 
### Lock e Unlock

A versão **safe** possui as primitivas `lock` e `unlock`, para garantir que o acesso à memória partilhada é feito em condições de exclusão mútua.

```c
static void lock()                                            <
{                                                             <
    struct sembuf down = {0, -1, 0};                          <
    if (semop(p->semid, &down, 1) == -1)                      <
    {                                                         <
        perror("lock");                                       <
        exit(EXIT_FAILURE);                                   <
    }                                                         <
}                                                             <
                                                              <
static void unlock()                                          <
{                                                             <
    struct sembuf up = {0, 1, 0};                             <
    if (semop(p->semid, &up, 1) == -1)                        <
    {                                                         <
        perror("unlock");                                     <
        exit(EXIT_FAILURE);                                   <
    }                                                         <
}                                                             <
```

### Criação, Conexão à memória Partilhada e Destruição dos semáforos

```c
void v_create()                                                 void v_create()
{                                                               {
    /* create the shared memory */                                  /* create the shared memory */
    shmid = shmget(key, sizeof(SharedDataType), 0600 | IPC_CR       shmid = shmget(key, sizeof(SharedDataType), 0600 | IPC_CR
    if (shmid == -1)                                                if (shmid == -1)
    {                                                               {
        perror("Fail creating shared data");                            perror("Fail creating shared data");
        exit(EXIT_FAILURE);                                             exit(EXIT_FAILURE);
    }                                                               }
                                                              <
    /* attach shared memory to process addressing space */    <  // no modo unsafe, a memória não é adicionada ao addressing space
    p = shmat(shmid, NULL, 0);                                <  // de um processo porque o processo não possui a opção de efetuar
    if (p == (void*)-1)                                       <  // um lock à memória enquanto a está a utlizar. 
    {                                                         <  // Assim a sequência attach memory, create access locker, unlock e
        perror("Fail connecting to shared data");             <  // detach não são necessárias
        exit(EXIT_FAILURE);                                   <
    }                                                         <
                                                              <
    /* create access locker */                                <
    p->semid = semget(key, 1, 0600 | IPC_CREAT | IPC_EXCL);   <
    if (p->semid == -1)                                       <
    {                                                         <
        perror("Fail creating locker semaphore");             <
        exit(EXIT_FAILURE);                                   <
    }                                                         <
                                                              <
    /* unlock shared data structure */                        <
    unlock();                                                 <
                                                              <
    /* detach shared memory from process addressing space */  <
    shmdt(p);                                                 <
    p = NULL;                                                 <
}                                                               }
```

A função `v_connect` não sofre alterações do modo safe para o modo unsafe porque a conexão (atribuição de memória partilhada ao espaço de endereçamento de um processo) é igual quer o processo consiga ou não efetuar o lock/unlock desta

```c
void v_destroy()                                                void v_destroy()
{                                                               {
    /* destroy the locker semaphore */                        <		// antes de destruir o semáforo é necessário destruir a 
    semctl(p->semid, 0, IPC_RMID, NULL);                      <		// capacidade do semáforo dar lock à zona de memória partilhada
                                                              <
    /* detach shared memory from process addressing space */        /* detach shared memory from process addressing space */
    shmdt(p);                                                       shmdt(p);
    p = NULL;                                                       p = NULL;

    /* ask OS to destroy the shared memory */                       /* ask OS to destroy the shared memory */
    shmctl(shmid, IPC_RMID, NULL);                                  shmctl(shmid, IPC_RMID, NULL);
    shmid = -1;                                                     shmid = -1;
}                                                               }
```

### Funções de acessos aos semáforos
A única diferença entre a versão `safe`e a versão `unsafe` é a presença do `lock` **antes de aceder à memória partilhada** e após o acesso o `unlock` da memória partilhada 

```c
/* set shared data with new values */                           /* set shared data with new values */
void v_set(int value1, int value2)                              void v_set(int value1, int value2)
{                                                               {
    lock();                                                   <
        p->var1 = value1;                                               p->var1 = value1;
    bwDelay(SET_TIME);                                              bwDelay(SET_TIME);
        p->var2 = value2;                                               p->var2 = value2;
    unlock();                                                 <
}                                                               }

/* get current values of shared data */                         /* get current values of shared data */
void v_get(int * value1p, int * value2p)                        void v_get(int * value1p, int * value2p)
{                                                               {
    lock();                                                   <
        *value1p = p->var1;                                             *value1p = p->var1;
    bwDelay(GET_TIME);                                              bwDelay(GET_TIME);
        *value2p = p->var2;                                             *value2p = p->var2;
    unlock();                                                 <
}                                                               }

/* increment both variables of the shared data */               /* increment both variables of the shared data */
void v_inc(void)                                                void v_inc(void)
{                                                               {
    lock();                                                   <
    p->var1 = p->var1 + 1;                                          p->var1 = p->var1 + 1;
    bwDelay(INC_TIME);                                              bwDelay(INC_TIME);
    p->var2 = p->var2 + 1;                                          p->var2 = p->var2 + 1;
    unlock();                                                 <
}                                                               }
```

## Perguntas

### If N processes increment both variables M times each, why are the final values different from N × M ?
Devido à ocorrência de **race conditions**, o acesso à estrutura de dados partilhados não é efetuada com mecanismos de exclusão mútua, ou seja, as tarefas/ações de um processo no acesso à estrutura **podem sobrepor-se às de outro**.

É possível ocorrerem situações do tipo:

1. $P_0$ lê o valor da variável `valuep1`
2. $P_1$ lê o valor da variável `valuep1`
3. $P_0$ incrementar o valor da variável `valuep1` e escreve na memória partilhada
4. $P_1$ incrementar o valor da variável `valuep1` e escreve na memória partilhada
	- Como a cópia da variável que o processo $P_1$ possui não foi incrementada por $P_0$, a nova variável em memória no conjunto destas instruções é `valuep1 + M` em vez de `valuep1 + 2M`

É devido á ocorrência destas situações, **race conditions no acesso memória partilhada**, que o resultado final das duas variáveis **não é** $N \times M$

###  Why can the two variables have different values?
- As duas variáveis, `valuep1` e `valuep2` possuem valores diferentes porque não é garantida exclusão mútua no acesso à memória partilhada. 
- Entre as operações de leitura e incremento de `valuep1` e `valuep2` existe um delay. 
	- Neste intervalo de tempo outros processos acedem à memória partilhada
	- Quando o delay terminal, a cópia local da variável pode não corresponder ao valor que se encontra em memória partilhada, à semelhança do explicado acima
	- O problema é que a introdução do delay potencia a ocorrência de fenómenos de race conditions, daí que o valor das variáveis seja diferente e que `valuep2` se afaste mais do valor expectável, $N \times M$
	


### Change TIME MACROS VALUE

> Macros SET TIME,GET TIME, INC TIME and OTHER TIME represent the times taken bythe manipulating operations and by other work. Change their values and understand what happens. Why? 

- **SET_TIME**
	- É usado para efetuar a operação de `set`
	- Como esta operação é efetuada **antes de ser lançado qualquer processo**, para inicializar a estrutura de dados, o valor da MACRO não altera os resultados da simulação
- **GET_TIME**
	- É usado para a operação de `get`
	- Como esta operação é efetuada **após todos os processos terminarem**, para ler os resulatdos da estrutura de dados, o valor da MACRO não altera os resultados da simulação
- **INC_TIME**
	- É o intervalo de tempo que decorre entre a leitura mais incremento da `valuep1` e da `valuep2`
	- Quanto maior este intervalo de tempo, mais provável é existirem race conditions, o que faz com que os valores das variáveis sejam mais diferentes
	- Inversamente, quanto menor, mais próximos serão os valores das variáveis, porque o intervalo de tempo que decorre entre e a leitura de uma e de outra é menor, existindo menor probabilidade de ter sido lida por outro processo nesse intervalo de tempo

