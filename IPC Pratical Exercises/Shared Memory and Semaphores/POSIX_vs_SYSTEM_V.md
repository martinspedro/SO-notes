# POSIX vs SYSTEM V semaphores
 
[LINK](https://stackoverflow.com/questions/368322/differences-between-system-v-and-posix-semaphores)

- One marked difference between the System V and POSIX semaphore implementations is that in System V you can control how much the semaphore count can be increased or decreased; whereas in POSIX, the semaphore count is increased and decreased by 1.
- POSIX semaphores do not allow manipulation of semaphore permissions, whereas System V semaphores allow you to change the permissions of semaphores to a subset of the original permission.
- Initialization and creation of semaphores is atomic (from the user's perspective) in POSIX semaphores.
- From a usage perspective, System V semaphores are clumsy, while POSIX semaphores are straight-forward
- The scalability of POSIX semaphores (using unnamed semaphores) is much higher than System V semaphores. In a user/client scenario, where each user creates her own instances of a server, it would be better to use POSIX semaphores.
- System V semaphores, when creating a semaphore object, creates an array of semaphores whereas POSIX semaphores create just one. Because of this feature, semaphore creation (memory footprint-wise) is costlier in System V semaphores when compared to POSIX semaphores.
- It has been said that POSIX semaphore performance is better than System V-based semaphores.
- POSIX semaphores provide a mechanism for process-wide semaphores rather than system-wide semaphores. So, if a developer forgets to close the semaphore, on process exit the semaphore is cleaned up. In simple terms, POSIX semaphores provide a mechanism for non-persistent semaphores.


Two major problems with POSIX shared/named semaphores used in separate processes (not threads): POSIX semaphores provide no mechanism to wake a waiting process when a different process dies while holding a semaphore lock. This lack of cleanup can lead to zombie semaphores which will cause any other or subsequent process that tries to use them to deadlock. There is also no POSIX way of listing the semaphores in the OS to attempt to identify and clean them up. The POSIX section on SysV IPC does specify the ipcs and ipcrm tools to list and manipulate global SysV IPC resources. No such tools or even mechanisms are specified for POSIX IPC, though on Linux these resources can often be found under /shm. This means that a KILL signal to the wrong process at the wrong time can deadlock an entire system of interacting processes until reboot.

Another disadvantage is the use of file semantics for POSIX semaphores. The implication is that there can be more than one shared semaphore with the same name, but in different states. For example a process calls sem_open, then sem_unlink before sem_close. This process can still use the semaphore just like unlinking an open file before closing it. Process 2 calls sem_open on the same semaphore between the sem_unlink and sem_close calls of process 1, and (according to documentation) gets a brand new semaphore with the same name, but in a different state than process 1. Two shared semaphores with the same name operating independently defeats the purpose of shared semaphores.

Limitation one above makes POSIX shared semaphores unusable in a real-world system without a guarantee that uncatchable signals can never be sent. Limitation two can be mitigated by careful coding, assuming control over all code that will use a given semaphore. Frankly, its more than a bit surprising they made it into the standard as they are.-
