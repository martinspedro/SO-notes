# Taxonomia de Sistemas Operativos

## Classificação com base no tipo de processamento
- Processamento em série
- Batch Processing
	- Single
	- Multiprogrammed batch
- Time-sharing System
- Real-time system
- Network system
- Distributed System



### Multiprogrammed batch
- **Propósito:** Otmizar a utilização do processador
- **Método de Otimização:** Enquanto um programa está à espera pela conclusão de uma operação de I/O, outro programa usa o processador

	
![Multiprogrammed batch](../Pictures/multiprogrammed_batch.png)


### Interactive System (Time-Sharing)
-  **Propósito:** 
	- Proporcionar uma interface `user-friendly` 
	- Minimizar o tempo de resposta a pedidos externos
- **Método:**
	- Vários utilizadors mas cada um no seu terminal
	- Todos os terminais têm comunicação direta e em simultâneo com o sistema
	- Usando multiprogramação, o uso do processador é multiplexado no tempo, sendo atribuído um time-quantuma cada utilizador
	- No `macrotempo` é criada a ilusão ao utilizador que possui o sistema só para si


![Interactive system (Time-Sharing)](../Pictures/time_sharing_system.png)

### Real Time System
-  **Propósito:** Monitorizar e (re)agir processo físicos 
- **Método:** Variante do Sistema Interativo que permite import limites máximos aos tempos de resposta para diferentes classes de eventos externos


![Real Time System](../Pictures/real_time_system.png)

### Network Operaing System
-  **Propósito:** Obter vantagem com as interconexões de `hardware` existentes de sistemas computacionais para estabelecer um conjunto de serviços comuns a uma comunidade.

A máquina é mantêm a sua individualidade mas está dotada de um conjunto de primitivas que permite a comunicação com outras máquinas da mesma rede:

- partilha de ficheiros (ftp)
- acesso a sistemas de ficheiros remotos (NFS)
- Partilha de recursos (e.g. impressoras)
- Acesso a sistemas computacionais remotos:
	- telnet
	- remote login
	- ssh
- servidores de email
- Acesso à internet e/ou Intranet


![Networking Operating System](../Pictures/network_operating_system.png)


### Distributed Operating System
-  **Propósito:** Criar uma rede de computadores para explorar as vantagens de usar **sistemas multiprocessador**, estabelecendo uma cada de abstração onde o utilizador vê a computação paralela distribuída por todos os computadores da rede como uma única entidade
- **Metodologia:** Tem de garantir uma completa **transparência ao utilizador** no acesso ao processador e outros recursos partilhados (e.g. memória, dados) e permitir:
	- distribuição da carga de `jobs` (programas a executar) de forma dinâmica e estática
	- automaticamente aumentar a sua capacidade de processamento de forma dinâmica se
		- um novo computador se ligar à rede
		- forem incorporados novos processadores/computadores na rede
	- a paralelização de operações
	- implementação de mecanismos tolerantes a falhas

## Classificação com base no propósito
- Mainframe
- Servidor
- Multiprocessador
- Computador Pessoal
- Real time
- Handhled
- Sistemas Embutidos
- Nós de sensores
- Smart Card

 
# Multiprocessing vs Multiprogramming
 
## Paralelismo

- Habilidade de um computador **executar simultaneamente** um ou mais programas
- Necessita de possuir uma estrutura multicore
	- Ou processadores com mais que um core
	- Ou múltiplos processadores por máquina
	- Ou uma estrutura distribuída
	- Ou uma combinação das anteriores


Se um sistema suporta este tipo de arquitectura, suporta **multiprocessamento**

O **multiprocessamento** pode ser feito com diferentes arquitecturas:

- **SMTP** - symetric processing (SMP)
	- Computadores de uso pessoal
	- Vários processadores
	- A memória principal é partilhada por todos os processadores
	- Cada core possui cache própria
	- Tem de exisitir **mecanismos de exclusão mútua** para o hardware de suporte ao multiprocessamento
	- Cada processador vê toda a memória (como memória virtual) apesar de ter o acesso limitado
- **Planar Mesh** 
	- Cada processador liga a 4 memória adjacentes

 
## Concurrência 
-  Ilusão criada por um sistema computacional de "aparentemente" ser capaz de executar mais programas em simultâneo do que o seu número de processadores
- Os processador(es) devem ser atribuídos a diferentes programas de forma multiplexada no tempo


Se um sistema suporta este tipo de arquitectura suporta **multiprogramação**

![Exemplo de multiplexing temporal: Os programas A e B estão a ser executados de forma concurrente num sistema single processor](../Pictures/multiprogramming_time_diagram.png)


 
