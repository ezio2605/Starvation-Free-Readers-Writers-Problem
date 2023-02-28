# Starvation-Free-Readers-Writers-Problem


Introduction:
The Starvation-free Reader-Writer problem is a classic synchronization problem where multiple processes (readers and writers) access a shared resource (a data structure) in a mutually exclusive way. I have presented the solution using semaphores. The solution ensures that no reader or writer thread is starved.


The solution uses the following variables:

readers: the number of reader processes accessing the resource

writers: the number of writer processes accessing the resource

writers_waiting: the number of writing processes waiting for resource

mutex: a semaphore that ensures mutual exclusion for readers and writers and control the access to shared resource (critical section)

rw_mutex: a semaphore that control access to the reader-writer variables readers, writers, and writers_waiting


