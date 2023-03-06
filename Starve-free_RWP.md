# **Starve-free Readers-Writers Problem in Operating Systems**

It deals with the issue of allowing multiple readers to access a shared resource simultaneously, while ensuring that only one writer can access the resource at any 
given time. The problem is to ensure that no reader is kept waiting indefinitely for access to the resource and no writer is starved of access to the resource.
This program provides a solution that ensures neither readers nor writers starve for access to the shared resource. By providing equal priority to both readers and 
writers, the program ensures fairness in accessing the shared resource.

## **Solution Overview**
The solution to the readers-writers problem involves providing mutual exclusion to the shared resource and prioritizing the access to the resource based on the type
of process. In the starve-free solution, readers and writers are given equal priority.

The implementation uses the following variables and semaphores:

- **rd_started** - a counter that keeps track of the number of readers that have started accessing the resource.
- **rd_finished** - a counter that keeps track of the number of readers that have finished accessing the resource.
- **writer_wait** - a flag that indicates whether a writer is waiting to access the resource.
- **wrt** - a semaphore that provides mutual exclusion to the shared resource for writers.
- **Sem_R** - a semaphore that ensures that only one reader is accessing the resource at a time.
- **Sem_W** - a semaphore that ensures that writers are given priority over readers.

The solution ensures that:

- Readers are given priority if no writer is waiting for access to the resource
- If a writer is waiting for access, no new readers are allowed to access the resource until the writer has finished
- Writers are given priority if there are no readers currently accessing the resource

## **Pseudocode**
### **Global Variables**
```
int rd_started = 0 ;
int rd_finished = 0 ;
bool writer_wait = false ;
```
### **Semaphores Initaialization**
```
wrt = 0 ;
Sem_R = 1 ;
Sem_W = 1 ;
```
### **Writer Process**
```
wait(Sem_W);
wait(Sem_R);

if(rd_finished < rd_started)
{
    writer_wait = true;
    signal(Sem_R);
    wait(wrt);
    writer_wait = false;
}
else signal(Sem_R);

//Critical Section
     ....WRITING.....
     
signal(Sem_W);
```
#### Explaination: 
- The writer process first waits for the Sem_W semaphore, which ensures that writers are given priority over readers. 
- Then it waits for the Sem_R semaphore, which ensures that only one reader is accessing the resource at a time. 
- If there are readers currently accessing the resource, it sets the writer_wait flag to true and signals the Sem_R semaphore to let readers finish then waits on the wrt semaphore, which indicates that a writer is waiting otherwise, it just signals the Sem_R semaphore to allow other processes to access the resource. Then it enters the critical section. 
- Finally, it signals the Sem_W semaphore to allow other processes to access the resource. 
- The semaphore wrt is initialized to 0 as in both the processes, one executes only wait() 
and other executes only signal().


### **Reader Process**
```
wait(Sem_W);
rd_started++;
signal(Sem_W);

//Critical Section
     ....READING.....

wait(Sem_R);
rd_finished++;

if(writer_wait && rd_finished==rd_started) signal(wrt);

signal(Sem_R);
```
#### Explaination: 
- The reader process first waits for the Sem_W semaphore, which ensures that writers are given priority over readers. It then increments the rd_started 
counter to indicate that a new reader has started accessing the resource. After that, it signals the Sem_W semaphore to release the resources and enters the critical 
section to read from the resource. 
- After that, it waits on the Sem_R semaphore to ensure that other readers or writers are not accessing the resource. It then increments rd_finished counter to indicate that a reader has finished accessing the resource. If there is a writer waiting and all readers have finished, it signals 
the wrt semaphore to allow the writer to access the resource. 
- Finally, it signals the Sem_R semaphore to allow other processes to access the resource.
