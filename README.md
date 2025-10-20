Shared Memory Task Queue (Producer–Consumer IPC System)

# Overview
This project implements a Task Queue system using the Shared Memory model of Inter-Process Communication (IPC).
It demonstrates how multiple processes can communicate and synchronize through a shared memory segment — without corrupting data or blocking each other.

At its core, this is a Producer–Consumer problem implemented with:
- Shared Memory → for fast communication.
- Semaphores → for synchronization and mutual exclusion.

# Architecture
````
           ┌──────────────────────┐
           │   Manager Process    │
           │ (Producer of tasks)  │
           └─────────┬────────────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │ Shared Memory (Task Queue) │
        │  + Circular Buffer         │
        │  + Counters (front/rear)   │
        └────────────────────────────┘
                     │
     ┌───────────────┼────────────────┐
     ▼               ▼                ▼
 Worker 1       Worker 2         Worker 3
 (Consumer)     (Consumer)       (Consumer)
````

- The manager process continuously produces new tasks and stores them in the shared queue,
  while multiple worker processes concurrently fetch and execute them.
  
------

# Components
## Component	Description : 
- SharedQueue	Circular queue structure stored in shared memory.
- shm_open, mmap	Used to create and map shared memory into each process.
- sem_open, sem_wait, sem_post	POSIX named semaphores for synchronization.
- mutex	Ensures only one process modifies the queue at a time.
- full, empty	Control queue fill and empty conditions (Producer–Consumer control).
  
------

# File Structure
```bash
task_queue/
├── manager.c       # Producer process code
├── worker.c        # Consumer process code
├── shared.h        # Shared data structures & constants
├── build.sh        # Build script
├── README.md       # Documentation
````
------

# System Calls & Libraries Used
- shm_open() : Create or open a POSIX shared memory object
- ftruncate() : Set the size of shared memory
- mmap()	Map : shared memory to process address space
- sem_open() : Create or open named semaphores
- sem_wait() : sem_post()	Wait/signal semaphores for synchronization
- fork() : Create multiple worker processes
- munmap() , shm_unlink()	: Cleanup shared memory resources

-------

🚀 How to Run
1. Build
```chmod +x build.sh```
```./build.sh```

2. Run the Manager (Producer)
```bash 
./manager
```

3. Run Workers (Consumers)
- In separate terminals:
```bash
./worker 1
./worker 2
./worker 3
```

You’ll see tasks being produced and consumed concurrently.

4. Cleanup (Important)

- After running all processes:
```bash
ipcs -m      # (to view shared memory segments)
ipcrm -m <id>  # (to manually remove if needed)
```
- or run cleanup in manager:
```./manager --cleanup```

- This ensures shared memory and semaphores are removed cleanly.
  
--------

# Concepts Demonstrated
- Concept	Implementation
- Inter-process communication	POSIX Shared Memory (shm_open, mmap)
- Synchronization	POSIX Semaphores
- Concurrency Control	Mutual Exclusion + Bounded Buffer
- Process Coordination	Producer–Consumer Pattern
- Resource Cleanup	shm_unlink, sem_unlink
  
--------

# Key Learnings
- How data consistency is maintained across processes using synchronization.
- Why shared memory is the fastest IPC (zero kernel overhead per transfer).
- How semaphores enforce order and safety in concurrent environments.
- The real-world relevance of producer-consumer models (task schedulers, pipelines, message brokers, etc.).
  
------

# Extension Ideas
Once this base system works, you can extend it:
- Add priority-based task scheduling (use multiple queues).
- Implement task acknowledgment (workers send back completion status).
- Add logging for queue status and performance.
- Convert to Message Passing version using POSIX message queues for comparison.

