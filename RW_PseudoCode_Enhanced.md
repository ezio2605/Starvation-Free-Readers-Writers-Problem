writing=1; //Semaphore for Writers
reading=0; //Semaphore for Readers
mutex=1; //Semaphore for Mutual Exclusion
reader=0; //Semaphore for indicating Reader in Critical Section
waiting=0; //Semaphore for Writers waiting for Critical Section
linebreaker=0; //Semaphore for preventing Writer/Reader Starvation


Reader_Process()
{
    wait(writing);
    signal(reading);
    if((writing||waiting)&&reading){
        signal(linebreaker);
        wait(writing);
    }
    wait(mutex);
    signal(reader);



    *******// CRITICAL SECTION //*******


    
    wait(reader);
    wait(reading);
    if(!reader){
        signal(mutex);
        signal(writing);
    }
}


Writer_Process()
{
    if(reader){
        signal(waiting);
    }
    else if(linebreaker){
        signal(writing);
    }
    else if(reading){
        wait(linebreaker);
    }
    

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

    if(signal(writing)){
        signal(reading);
        signal(mutex);
    }
}