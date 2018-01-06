# Considerações sobre semáforos

- SO pode fazer o down fora da exclusão mutua senão o se semáforo entra em deadlock
- Entre o up e o down podem entrar outros processo
	- Se fosse uma soluçºao com threads não funcionava
	- Funciona porque os semáforos são persistentes
	- As soluções com semáforos e monitores 
- Se faço com a intenção de acordar alguém, tenho de verificar que ele já está bloqueado quando mando  signal
- A função de _drop_forks_ tem de verificar se ao dar drop ao garfo os restantes tem a capacidade de comer
	- O problema pode causar stravation
		- Se dois processo tiverem a correr indifeinidamente, podemos ter o cas de o processo entrre dois processos nunca comer
- O delay do filósofo serve para eliminar o busy wainting
	- Demora mais tempo que as outtas soluções
	- Deixa sde haver um get_left e get_right e passa a exisitir um get_lower e um get_higer, snedo os garfos obtidos usando o ID
- **Não façam duas ou três ou 4 execuções** 
- **FAÇAM CENTENAS!!** script 



# Notas para o 2º Trabalho Prático
- Enquanto a cadeira não estiver feita não passa à segunte
- PAra estudar a cadeira é obigado a rquesitr todas os livros necessários pela cadeira
- Os livros são partilhados entre todos os alinos e possivelmente entre cadeiras
- Entidades ativas
	- Aluno (>= 1)
		- a simulaçao preve a possibilidade de ser mais do que 1 aluno
	- Librarean (1)
	- Logger(1)
		- Toda a simulação tem de funionar de forma segura
		- Temos de observar em tempo real a simulação à medida que flui 
		- Sempre qe quero fazer um registo no logger recebo um ID
			- nº de colunas, nº de linhas
			- diz logo onde vair esverecer na zona do ecrã
			- recebe uma mensagem a indicar o quadrado
			- temnho uma comnicação por mensagens entre as entidades ativas e o logger
			- a unica entidade que vai escrever no terminal é o oogger
			- estratégia semelhnate à  biblioteca gráfica em java
			- usamos comunicação por mensagen
			- é sequencial
			- race conditions (n percebi pq foi dito)
			- a versão do logger é apenas gráfica
			- de momento só gráfico
	- Recursos partilhados
		- Biblioteca:
			- não é ma entidade atova
			- é na bilbioteca onde os alunos são obrigados a aestudar
			- deixa os livros na mesa
			- o bibliotcário tem a responsabilidade de ir byscar os livros e dar aos alunos
				- tem também que ir buscar os livros às mesas deixados pelos alunos
			- **undhandled**: Número de pedidos não trataods
			- **Requests:**
- em cima são as estantes dos livros			
- os livros são recusos partilhados
- as cadeiras sºao recursos partilhados
- as mesas são recursos partilhados
	- Podem não estar disponíveis
- Bc, Bb - livros que pediu e que requisitou
- Enquanto o bibliotecário não recolher os livros de cima da mesa
			
- Vai ser fornecido código sequencial que vai lançar exceções
- versão funcional em threads com binário na próxima semana

## Ciclo de vida
### Alunos
1. Fazer as cadeiras todas
	- 1 de cada vez
2. Para cada cadeira, enquanto a cadeira não estiver feita
	2.1 Comer
	2.2 Estudar
	2.3 Comer
	2.4 Estudar
	2.5 COmer
	2.6 Fun
	2.7 Dormir

### Bibliotecário
1. Eat()
2. handleRequests()
3. CollectBookFromTables()
4. Eat()
5  handleRequests()
6. CollectBookFromTables()
7, Eat()
8. Fun()
9. Sleep()

# IPC
- O logger aperece com a encessidasde de geiri os printfs
	- é preciso porque existe atividade concorrente
	.- todos eles enviam pedidos de atualização do estado para o logger
	- 
- Do lado do cliente só existem 3 funções a usar
	- regist_logger
	- é preciso definir o trabalho de comunicação e sincornização entre as entidades correntes
	- é um pouco transformar as entidades em monitores

- Garantir:
	- race conditions
		- Qualquer send log que um faz não vai colidir com o outro
		- Basicamente: evitar exclusão mútua
			- lock no incicio
			- unlock no fim
			- threads: mutex
			- semáforo:  
	- Ausência de busy waiting
		- Só existe nos estudantes e não no logger
	- Não pode ter deadlock
		- Onde é que pdoe ocorrer deadlock
			- verificar as 4 condições de deadlock
				- Requisitar os livros com base no seu ID
				- Garantir que os livros são requesitados apenas 1 vez
			- Façam as coisas por ordem

- O código sequencial está feito
- O que falata é transofmramlas num monitor
- Numa ou noutra vai ser encessário garantir as variáveis de condição
- COmo cada entidade é um módulo, a interação com o logger é feita através de meoria partilahda
- A libraira também é uma zona +artilhada
- Quem está com processo existe um passo que é preciso
	- registar a memóri apartilhada
		- é preciso cirar e enviar para lá os dados
	- Em semáforos não é preciso
- Grupo como um todo pense na solução
- Depois façam a implementação com threads e processos
	- O de processos tem mais um passo
		- SOlução com threads
			- variável global é partilhada entre todas as threadas
		- Solução com processos
			- É preciso criar a memória partilhada
			- É preciso que o módulo copie de forma rápida as variáveis locais para as variáveis em memória
			- É preciso que o logger copie as mensagens para a sua memória interna

- A interface de comunicação tem de 
- 
- Criar váriáveis de
- O grau de paralelização da aplicação será avaliado

# Reunião
1 - logger
2 - entidades ativadas
3 - entidade biblioteca (estrutura de dados partilhada)
4 - Bibliotecário
5 - Estudante

53 funções para fazer
2/3 são triviais
Funções do bibliotecário não são triviais
Bibliotecário tem de ter duas listas
Bibliotecário tem de fazer gestão dos livros 
Se o estudante já estiver estudado uma cadeira pode mesmo assim 

# Aula Prática 24-11

```c
void *(*start_routine) (void *)
```

- EM C um ponteiro é sempre um ponteiro para qq coisa
- Se eu quiser um ponteiro genérico crio void* *p e posso passar o tipo de ponteiro que quiser
- Não posso incrementar u ponteiro void
	- O compilar não sabe a quantidade de endereços a incrementar
- Variável do tipo estrutursa -> & antes do nome
- Em C, o nomde de uma função é o seu endereço inicial
	- k é o endereço da função
		- Posso passar a outra função uma gunção que receb como endereço outra função
	- k() é a invocação de uma função

- AValiçaão de condições de locking é feita com a exclusão mútua

# Consumidor - proodutor
- Os dois digitos do id são os ultimos digitos do valor gerado
- Os monitores que o POSIX define são Lampson-Rodel
- Quando alguém é acordado é colocado na fila ready
- A condiçãoq eu levou a fazer o while


# pthread
- É criada a estrutura de dados em causa
- É feita a conversão do tipo de ponteiros void
- Depois é precisa a conversão de 
- manipulaçãpo de mutex
	- senão reconehcer
		- glibc-doc

# Guião de hoje
## Shared memory e semaphores
- O espaço de endereçamento dos város processos são independentes
	- O espaço de memória é virtual
- POss é criar uma variável em mem+ória real e mapea-la no espaço de endereçamento em memória real

```dot
# gráfico de memória partilhado com uma variável real mapeada nas duas memórias virtuais dos processo
a -> b -> c
c -> b -> c
```

- QUando ciro o recuso tenho de indicar quais as permisssões do recurso
pthread_cond_wait: unlock atomico da variável de condição 

shmget -> alocar uma memória parilhada de sistema V
shamt -> mapeamento de meória partilhada
semget : criação de semáforos
	- Usamos semáfoos de sistema 5
		- Posso criar e manipular um array de semaforos
semop : Maniuplação dos semáforos
	- +1 : ip
	- -1 : down
	- Mascara-se criando uma biblioteca de suporte mais amigável

A ideia é recriar o mapeador/manipulador de forma mapeada e que o mapeador crie uma das soluções
	- Monitores
	- Memória partilhada
	- Semáforos
	
Modelo Posix
- get para pere acesso/criar
- EM condisequência do attach em posso obter acesso à zona partilhada de memória
	- mapeada na heap memory
- 

- Quando crio recursos do tipo POSIX são mesmo criadas inode spara isso
- Os segmentos de memoria partilhados são escritos no disco
- /dev/shm/ : ZOna onde são guardados os devices de memoria partilhada

```bash
# Mostra os recursos ipc
# OU são 
# * semãfotos
* segmentos de moemria partilhada
# mensagens
ipcs
```

# apagar recursos criados num contexto de um programa
ipcrm
```
- Se entrar em deadlock 


- Devo ter a maior região critica o menor possível

```c
// Solução sem prevenção de deadlock
...
val = value;
val++;
value = val;
...

// Com proteção de deadlock
...
lock
val = value;
val++;
value = val;
unlock
...
```

