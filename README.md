# Linux-IPC-Semaphores
Ex05-Linux IPC-Semaphores

# AIM:
To Write a C program that implements a producer-consumer system with two processes using Semaphores.

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux Process API - Sempahores

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## Write a C program that implements a producer-consumer system with two processes using Semaphores.

```c
#include <stdio.h>      
#include <stdlib.h>     
#include <unistd.h>     
#include <sys/types.h>  
#include <sys/ipc.h>    
#include <sys/sem.h>    
#include <sys/wait.h>   
#include <time.h>      

#define NUM_LOOPS 10 
union semun {
    int val;               
    struct semid_ds *buf;  
    unsigned short int *array; 
    struct seminfo *__buf;
};
void wait_semaphore(int sem_set_id) {
    struct sembuf sem_op;
    sem_op.sem_num = 0;
    sem_op.sem_op = -1;
    sem_op.sem_flg = 0;
    semop(sem_set_id, &sem_op, 1);
}


void signal_semaphore(int sem_set_id) {
    struct sembuf sem_op;
    sem_op.sem_num = 0;
    sem_op.sem_op = 1;
    sem_op.sem_flg = 0;
    semop(sem_set_id, &sem_op, 1);
}

int main() {
    int sem_set_id;
    union semun sem_val;
    int child_pid;


    sem_set_id = semget(IPC_PRIVATE, 1, 0600);
    if (sem_set_id == -1) {
        perror("semget");
        exit(1);
    }

    printf("semaphore set created, semaphore set id '%d'.\n", sem_set_id);


    sem_val.val = 0;
    if (semctl(sem_set_id, 0, SETVAL, sem_val) == -1) {
        perror("semctl");
        exit(1);
    }


    child_pid = fork();

    if (child_pid < 0) {
        perror("fork");
        exit(1);
    }

    if (child_pid == 0) {  
        for (int i = 0; i < NUM_LOOPS; i++) {
            wait_semaphore(sem_set_id); 
            printf("consumer: '%d'\n", i);
            fflush(stdout);
        }
        exit(0);
    } else {  
        for (int i = 0; i < NUM_LOOPS; i++) {
            printf("producer: '%d'\n", i);
            fflush(stdout);
            signal_semaphore(sem_set_id);
            usleep(500000);
        }

        wait(NULL);

        semctl(sem_set_id, 0, IPC_RMID, sem_val);
        printf("Semaphore removed.\n");
    }
    return 0;
}
```


## OUTPUT
$ ./sem.o 

<img width="560" height="529" alt="Screenshot 2025-09-29 112628" src="https://github.com/user-attachments/assets/1bb71a2e-bdc1-40ec-98e8-5f6122460253" />

$ ipcs

<img width="910" height="256" alt="Screenshot 2025-09-29 112657" src="https://github.com/user-attachments/assets/af935085-b671-4379-9054-f4da5f295e8d" />




# RESULT:
The program is executed successfully.
