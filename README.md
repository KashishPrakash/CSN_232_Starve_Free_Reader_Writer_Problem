
# Starve Free Reader Writer Problem
The Reader-Writer problem is a well-known issue in computer science that arises when multiple processes concurrently access some storage area. In this scenario, it is crucial to ensure that the data is not corrupted due to simultaneous access by multiple processes. Therefore, a critical section is defined, which can only be accessed by one writer at a time, while multiple readers can access it concurrently.

To address this problem, semaphores are used to control the access to the critical section. A semaphore is a synchronization object that allows multiple processes to access shared resources in a controlled manner. It consists of a counter that is used to manage access to the critical section. The counter is incremented by readers when they access the shared resource and decremented when they finish accessing it. Similarly, the counter is incremented by writers when they access the shared resource and decremented when they finish accessing it.

However, while implementing semaphores, it is essential to consider the priorities of readers and writers. For instance, if readers have a higher priority than writers, readers may be given access to the critical section more frequently than writers. Conversely, if writers have a higher priority, then writers may be given access more frequently than readers. This can lead to starvation of either the readers or the writers, depending on their priorities.


## Process Control BLock Implementation 
```cpp
class PCB{
    public:
    int process_id;
    PCB* next_process=NULL;
    bool process_state;
}
```
The process_state variable represents whether the process is blocked or active. If it is true then process is active and if it is false then it is in inactive state.
The next_process variable stores the next process
Other variables can also bhi defined but only necessary variables are mentioned


## Waiting Queue Implementation
```cpp
class Waiting_Queue{
    public:
    int size = 0 ;
    PCB* front, back;
    void Push(PCB* process){
        process->process_state = false;
        if(size==0){
            front = process;
            back = process;
            size++;
        }
        else {
            size++;
            back->next_process = process;
            back = process;
        }
    }
    process* Pop(){
        PCB* temp = front;
        front = front->next_process;
        size--;
        return temp;
    }
}
```
The size variable represents the size of the queue. front and back stores the first and last process of the queue.
Whenever a process enters the queue, its process_state is turned false as it is in a blocked state.


## Semaphore Implementation
```cpp
class semaphore {
    public:
    int sem = 1;
    semaphore(int n ){
        sem = n;
    }
    waiting_queue wait_queueu = new waiting_queue;
}
void wait(PCB process, semaphore *s){
    s->sem--;
    if(s->sem<0){
        s->wait_queue.Push(process);
    }
}
void wake(PCB* process){
    process->process_state = true;
}
void signal(semaphore s){
    s->sem++;
    if(s->sem<=0){
        PCB* temp = s.waitqueue.Pop();
        wake(temp);
    }
}
```
The semaphore class contains the waiting queue. The wait function decrements the value of the semaphore and put the process in the waiting queue if necessary. The signal function increments the semaphore value and wake up the process from the queue if necessary.

##  Global Variables
```cpp
semaphore* sem_writer = new semaphore(1);
semaphore* sem_reader = new semaphore(1);
semaphore* sem_enter = new semaphore(1);
int reader_count = 0;
```
The semaphores are initialised with value 1 because only one writer process can be in the critical section at a time.

## Reader Code Immplementation
```cpp
while(true){
    wait(process,sem_enter);
    wait(process,sem_reader);
    reader_count++;
    if(reader_count==1){
        wait(process, sem_writer);
    }
    signal(sem_reader);
    signal(sem_enter);
    // Critical Section
    wait(process,sem_reader);
    reader_count--;
    if(reader_count==0){
        signal(sem_writer);
    }
    signal(sem_reader);
}
``` 


## Writer Code Implementation
```cpp
while(true){
    wait(process, sem_enter);
    wait(process, sem_writer);
    signal(sem_enter);
    // Critical Section
    signal(sem_writer);
}
```
## Correctness of the Solution

The starve free solution should satisfy the three criterias: Mutual Exclusion, Progress and Bounded Waiting.

### Mutual Exclusion

To ensure mutual exclusion in the Reader-Writer problem, either one writer or one or more readers can access the critical section at a given time. To achieve this, the reader semaphore is used, which calls the wait operation and acquires the writer semaphore until there is at least one reader in the critical section. This ensures that if a writer is already in the critical section, then no other writer can enter it. Similarly, if a writer is waiting to enter the critical section, then the reader semaphore ensures that no readers can enter the critical section until the writer has finished.

On the other hand, if a writer is currently in the critical section, then the writer semaphore is required for a reader to access the critical section. This ensures that if a writer is already in the critical section, then no reader can enter it until the writer has finished. Therefore, the use of semaphores allows for proper synchronization and prevents conflicts between readers and writers, ensuring that the critical section is accessed safely and efficiently.

### Progress

The code structure has been designed to prevent the occurrence of deadlock in the Reader-Writer problem. This means that there is no situation in which the system gets stuck in a state where no process can proceed. Furthermore, the readers and writers take a finite amount of time to execute, and the use of semaphores ensures that they are released after completing their critical section execution. This allows other processes to access the critical section and prevents any delays in the overall execution.

### Bounded Waiting

Previously, if readers arrived consecutively, writers could starve in the Reader-Writer problem. However, with the introduction of the entry semaphore, all processes are queued up and released one after the other, regardless of whether they are readers or writers. As a result, readers and writers can access the critical section fairly, without any starvation of either process.
