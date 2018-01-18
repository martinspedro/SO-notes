# Guião Prático - Threads and Monitors
## Incrementer
### bwdelay.c

```bash
# Correr um comando só no core 0
taskset -c 0

# medir o tempo de execução de um comando
time
```

Ilustrar _race conditions_ no acesso a memória partilhada por _threads_ concurrentes:

1. A thread acede ao valor em memória
2. Copia esse valor localmente
3. Incrementa esse valor M vezes
4. Espera algum tempo
5. Escreve o novo valor em memória

## increment_unsafe
  Nº Threads        Final Value     Real Time           USER TIME           SYSTEM TIME
---------------  ---------------- ----------------- -------------------  -----------------
     0                  1000         0m0.003s         0m0.004s              0m0.000s
	 1                  1000         0m0.389s         0m0.384s				0m0.000s
	 2				    2000	     0m0.354s		  0m0.688s				0m0.000s
	 3					1671		 0m0.360s		  0m1.064s			    0m0.000s
	 4  				3105 		 0m0.391s		  0m1.472s			    0m0.000s
	 5					3409		 0m0.395s		  0m1.876s			    0m0.000s
     6					3768		 0m0.393s		  0m2.236s				0m0.000s
	 7					3633		 0m0.397s		  0m2.648s				0m0.004s
	 8					4265		 0m0.422s		  0m3.048s				0m0.000s
	 9 					4953		 0m0.555s	      0m3.408s			    0m0.000s
	 10					4696	     0m0.563s		  0m3.788s			    0m0.000s
	 11					5444		 0m0.618s		  0m4.168s			    0m0.000s
	 12					5215		 0m0.664s		  0m4.796s			    0m0.000s
	 13					6483	     0m0.680s	      0m4.976s			    0m0.000s
	 14					7256	     0m0.721s		  0m5.396s			    0m0.000s
	 15 				6856	     0m0.801s	      0m5.840s			    0m0.000s
	 16 				6699		 0m0.921s		  0m6.788s			    0m0.000s
	 17 				7554		 0m0.946s	      0m6.912s			    0m0.000s
     18					7624	     0m0.968s	 	  0m7.324s			    0m0.000s
	 19					7475		 0m1.039s		  0m7.748s			    0m0.000s
	 20					7250		 0m2.001s		  0m15.284s			    0m0.000s
	 21        			7272  		 0m2.076s		  0m16.032s				0m0.000s
	 22					9797	     0m1.296s         0m9.968s              0m0.000s
     23                 9854         0m3.928s         0m31.144s             0m0.000s

Cada thread incrementou o valor M vezes, onde M=1000:

- **P:** If N threads increment the variable M times each, why is the final value different from $N \times M$
- **R:** O valor final é diferente de $N \times M$ porque não são garantidas as _race conditions_. Assim não é possível

