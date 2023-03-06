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


## First Reader, Second Reader

+ As a Reader accesses the Reader_Process function, it first puts writing to wait and signals reading.
+ Then it checks the condition and eventually it comes out false ( because waiting=0, writing=0, reading=1 ) and so it moves to code below.
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

__Suppose a Second Reader wants to access the Critical Section__

+ As the writing is already set as 0 by First Reader, it cannot further advance restricting the Second Reader and goes to waiting list.

__Suppose First Reader has finished reading__

+ It will set reader and reading to 0 indicating that there are no reader and reading process going on in Critical Section.
+ It will check the condition and set mutex and writing to 1, signaling the waiting Reader and allowing it to access.

``` js
wait(reader);
wait(reading);
if(!reader){
    signal(mutex);
    signal(writing);
```


## First Writer, Second Writer

+ As a Writer accesses the Writer_Process function, it first aquires writing.
+ It then checks the condition which comes out false ( because reader=0 and writing=0 ) and moves to code below.
+ It aquires mutex and further checks the condition.
+ As waiting=0, it advances inside the second condition, signals writing and accesses the Critical Section.

``` js
wait(writing);
if(reader||writing){
     signal(waiting);
     wait(mutex);
    }
wait(mutex);
if(waiting){
     wait(waiting);
     signal(writing);


        *******// CRITICAL SECTION //*******

    }
 else{
     signal(writing);


        *******// CRITICAL SECTION //*******

    }
```

__Suppose a Second Writer wants to access the Critical Section__

+ As the mutex is already set as 0 by First Writer, it cannot further advance restricting the Second Writer and goes to waiting list.

__Suppose First Writer has finished reading__

+ First Writer checks the condition and signals mutex and reading, indicating the waiting Writer to access the Critcal Section.

``` js
if(signal(writing)){
     signal(reading);
     signal(mutex);
    }
```

## First Reader, First Writer, Second Reader

+ As a Reader accesses the Reader_Process function, it first puts writing to wait and signals reading.
+ Then it checks the condition and eventually it comes out false ( because waiting=0, writing=0, reading=1 ) and so it moves to code below.
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

__Suppose a First Writer wants to access the Critical Section__

+ The Writer accesses the Writer_Process function, checks the condition and the first condition comes out true ( because reader=1 ).
+ It signals waiting and as writing is already set as 0 by Reader, the Writer goes to waiting list.

``` js
if(reader){
      signal(waiting);
    }
else if(linebreaker){
      signal(writing);
    }
else if(reading){
      wait(linebreaker);
    }
```

__Suppose a Second Reader wants to access the Critical Section__

+ As the writing is already set as 0 by First Reader, it cannot further advance, restricting the Second Reader and goes to waiting list.

__Suppose the First Reader has finished reading__

+ It will set reader and reading to 0 indicating that there are no reader and reading process going on in Critical Section.
+ It will check the condition and set mutex and writing to 1, signaling the waiting Processes and allowing it to access.

``` js
wait(reader);
wait(reading);
if(!reader){
    signal(mutex);
    signal(writing);
```

__Suppose Second Reader tries to access Critical Section before First Writer__

+ Second Reader accesses the Reader_Process function, sets writing=0 and reading=1.
+ It then checks the condition and eventually it comes out true ( because waiting=1 previously set by waiting Writer ).
+ It signals linebreaker and blocks the Second Reader.

``` js
if((writing||waiting)&&reading){
     signal(linebreaker);
     wait(writing);
    }
```

__Suppose First Writer tries to access Critical Section again__

+ First Writer accesses the Writer_Process function and checks the condition.
+ The second condition comes out true (because linebreaker=1 ) and signals writing.
+ It aquires writing.
+ It then checks the condition which comes out false ( because reader=0 and writing=0 ) and moves to code below.
+ It aquires mutex and further checks the condition.
+ As waiting=1, it advances inside the first condition, sets waiting=0, signals writing and accesses the Critical Section.
+ After finishing writing it signals the waiting Reader to access the Critical Section.


## First Writer, First Reader, Second Writer

+ As a Writer accesses the Writer_Process function, it first aquires writing.
+ It then checks the condition which comes out false ( because reader=0 and writing=0 ) and moves to code below.
+ It aquires mutex and further checks the condition.
+ As waiting=0, it advances inside the second condition, signals writing and accesses the Critical Section.

``` js
wait(writing);
if(reader||writing){
     signal(waiting);
     wait(mutex);
    }
wait(mutex);
if(waiting){
     wait(waiting);
     signal(writing);


        *******// CRITICAL SECTION //*******

    }
 else{
     signal(writing);


        *******// CRITICAL SECTION //*******

    }
```

__Suppose a First Reader tries to access the Critical Section__

+ As mutex is already set to 0 by First Writer, the Reader goes to waiting list.

__Suppose a Second Writer tries to access the Critical Section__

+ Second Writer accesses the Writer_Process and checks the condition.
+ The third comes out true ( because reading=1 set by First Reader ) and goes to waiting list.

``` js
if(reader){
      signal(waiting);
    }
else if(linebreaker){
      signal(writing);
    }
else if(reading){
      wait(linebreaker);
    }
```

__Suppose First Writer finishes writing__

+ First Writer checks the condition and signals mutex and reading, indicating the waiting Processes to access the Critcal Section.

``` js
if(signal(writing)){
     signal(reading);
     signal(mutex);
    }
```

__Suppose Second Writer tries to access the Critical Section before First Reader__

+ As the third condition is still true ( because reading=1 ), it gets blocked and goes to waiting list.

__Suppose First Reader tries to access the Critical Section again__

+ As a Reader accesses the Reader_Process function, it first puts writing to wait and signals reading.
+ Then it checks the condition and eventually it comes out false ( because waiting=0, writing=0, reading=1 ) and so it moves to code below.
+ Then it aquires mutex and signals reader=1 to indicate that a reader is in Critical Section.
+ After finishing reading, it signals the waiting Writer to access the Critical Section.


