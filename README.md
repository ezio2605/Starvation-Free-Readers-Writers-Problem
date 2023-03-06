# Starvation-Free-Readers-Writers-Problem

## [Introduction:](https://en.wikipedia.org/wiki/Readers%E2%80%93writers_problem)


__The Starvation-free Reader-Writer problem is a classic synchronization problem where multiple processes (readers and writers) access a shared resource (a data structure) in a mutually exclusive way. I have presented the solution using binary semaphores. The solution ensures that no reader or writer thread is starved and also deadlock free.__

__The solution uses the following variables:__

+ writing;  //Semaphore for Writers
+ reading;  //Semaphore for Readers
+ mutex;  //Semaphore for Mutual Exclusion
+ reader;  //Semaphore for indicating Reader in Critical Section
+ waiting;  //Semaphore for Writers waiting for Critical Section
+ linebreaker;  //Semaphore for preventing Writer/Reader Starvation


__Variables Initialization:__

+ writing=1; 
+ reading=0; 
+ mutex=1; 
+ reader=0; 
+ waiting=0; 
+ linebreaker=0; 

__We consider 4 basic cases upon which all the cases would be handled:__

1. First Reader, Second Reader
2. First Writer, Second Writer
3. First Reader, First Writer, Second Reader
4. First Writer, First Reader, Second Writer


### First Reader, Second Reader

+ As a Reader accesses the Reader_Process function, it first puts writing to wait and signals reading.
+ Then it checks the condition and eventually it comes out false ( because waiting=0, writing=0, reading=1 ).
+ Then it aquires mutex and signals reader to indicate that a reader is in Critical Section.

``` js
wait(writing);
    signal(reading);
    if((writing||waiting)&&reading){
        signal(linebreaker);
        wait(writing);
    }
    wait(mutex);
    signal(reader);

    *******// CRITICAL SECTION //*******

```


