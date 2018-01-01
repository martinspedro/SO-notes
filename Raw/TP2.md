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

