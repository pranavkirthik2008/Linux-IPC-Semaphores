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

```

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/sem.h>
#include <sys/wait.h>

#define NUM_LOOPS 10

union semun {
    int val;
    struct semid_ds *buf;
    unsigned short *array;
};

void wait_semaphore(int sem_id) {
    struct sembuf op = {0, -1, 0};
    semop(sem_id, &op, 1);
}

void signal_semaphore(int sem_id) {
    struct sembuf op = {0, 1, 0};
    semop(sem_id, &op, 1);
}

int main() {
    int sem_id = semget(IPC_PRIVATE, 1, 0600);
    union semun u;
    u.val = 0;
    semctl(sem_id, 0, SETVAL, u);

    if (fork() == 0) {
        for (int i = 0; i < NUM_LOOPS; i++) {
            wait_semaphore(sem_id);
            printf("consumer: '%d'\n", i);
            fflush(stdout);
        }
        exit(0);
    } else {
        for (int i = 0; i < NUM_LOOPS; i++) {
            printf("producer: '%d'\n", i);
            fflush(stdout);
            signal_semaphore(sem_id);
            usleep(500000);
        }
        wait(NULL);
        semctl(sem_id, 0, IPC_RMID, u);
        printf("Semaphore removed.\n");
    }
    return 0;
}

```



## OUTPUT
$ ./sem.o 

![alt text](<WhatsApp Image 2026-02-26 at 8.29.01 AM.jpeg>)


$ ipcs

![alt text](<WhatsApp Image 2026-02-26 at 8.29.16 AM.jpeg>)



# RESULT:
The program is executed successfully.
