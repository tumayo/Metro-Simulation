# Metro Simulation
In our implementation, we created 5 threads. We have a Train class, which is the trains in the metro simulation, so Train class has some fields such as Id of train,starting point and destination point of train, start time and departure time of train. We have 4 train queues which are trainsA, trainsB, trainsE and trainsF each are storing trains coming from specific points, A, B, E, F, to tunnel.
We have ACLine, BCLine, EDLine, FDLine threads, each are whether creating or not according to some random numbers created each 1 second. If probability is satisfied, we are determining the length and end point according to new random numbers.\
\
When a train is created, it is added to its line's trains queue and goes into pthread_sleep for the next second.\
\
We also have controlcenter thread. Controlcenter is first checking counter which is global variable, which is the total the number of trains in all system, we changed this variable, by countmutex to avoid race condition between threads sharing it.Counter is increased while adding the train to queues and decreased after passing the tunnel. Decreasing counter is done in controlcenter, increasing is done in other threads. So, when counter>=10, we made the system go into system overload state by using condition variables and not allowing other threads to put trains in the system until counter==0.
This is done by there is another global bool flag, when sytstem is overloaded it is assigned to true so threads cannot create trains until flag turned into false. Flag is turned into false when busy waiting of threads finish. Until counter==0, threads are in busy waiting with pthread_cond_wait. When counter=0, condition is broadcasted to all other treads and all other threads are allowed again to add trains by turning flag to false.
While controlcenter allowing which train to pass the tunnel, it is first checking the sizes of queues and if one is greater, this queue's front train is given priority to pass the tunnel, if there is ties we checked the sizes of queues of each and gave priority accordingly.
While passing the tunnel, we also included some random number to see whether or not there is a breakdown. If yes, we made the pthread_sleep additionally wait 4 more seconds.
For controlcenter_log and train_log, we created txt file added all events happening in controlcenter while trains are passing, such as start time and end time, passing train's id and finally waiting trains.
For, train_log we again created a txt file, for keeping the information about trains after they passed the tunnel, we used map data structure to keep train id as key of map and train. When the simulation time ends, we also wrote the train informations  to the file. 
