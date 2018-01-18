## Includes
O inclusão de `sem.h` permite 

```c
#include <sys/shm.h>                                            #include <sys/shm.h>
#include <sys/sem.h>                                          <
```

## Tempo de Incremento
```c
#define INC_TIME 500                                          | #define INC_TIME 1000
```

## Internal Data Strucuture
A estrutura de memória partilhada passa a ter um semáforo implementado, usando a variável `semid` para controlo.

```c
/* Internal data structure */                                   /* Internal data structure */
typedef struct                                                  typedef struct
{                                                               {
    int var1, var2;                                                 int var1, var2;
    int semid;                                                <
} SharedDataType;                                               } SharedDataType;
```

## Lock e Unlock

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

## Criação, Conexão à memória Partilhada e Destruição dos semáforos

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

## Funções de acessos aos semáforos
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

