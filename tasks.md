## Overview

This package comprises of time tasks timers and task appointments.


### Tasks timers

Timers are used to execute periodical tasks. The timer starts ticking upon task registration. A timer may have an initial delay and an interval specified. The tasks will be triggered periodically following the given interval. The delay specifies the time between registering the task and its first execution. Thereafter, the timer will go off at regular intervals which causes the trigger function to be triggered. If a failure occurs during the execution of the trigger function, it will return an error. That error will be passed to the ```onErrorFunction ```.

```onTriggerFunction ```

The function which will be called upon timer goes off. 

```onErrorFunction ```
 
This function will be called if an error occurred while executing onTriggerFunction. 

```interval ```

The time interval which the clock should go down. Once the peoriod of time denoted by ```intevel``` passes, the timer goes off and ```ballerina onTriggerFunction ``` is executed. The value should be specified in milliseconds.

```delay```

The delay which needs to be added executing the task upon for the very first time. This is specified in milliseconds.

#### Define a task timer
```ballerina

(function() returns error?) onTriggerFunction = doTask;
    (function(error)) onErrorFunction = onError;
    timer = new task:Timer(onTriggerFunction, onErrorFunction, interval, delay = delay);
    _ = timer.start();

```
Function doTask will do the timer task whereas function onError is the function which is responsible for handling errors which takes place while doing the specified task. 


### Task appointments

An arrangement of a task to be taken place on clock time. If we take a real world appointment as an  analogy for an appointment task, an appointment takes place on a given time, in a given pattern. (may be periodically). Cron syntax is used to define the time, and in
case of recurring appointments, the frequency of the appointment.

#### onTriggerFunction

 A trigger function is called when an appointment is due. 

#### onErrorFunction

If a failure occurs during the execution of the trigger function, it will return an error. That error will be passed to the onError function.

#### Define a task appointment

```ballerina
 (function() returns error?) onTriggerFunction = onTrigger;
    (function (error)) onErrorFunction = cleanupError;
    app = new task:Appointment(onTriggerFunction, onErrorFunction, cronExpression);
    _ = app.schedule();
```

Funcion onTrigger is a user defined function which does the actual appointment task where as the function cleanupError acts as the function for handling if an error is returned from the task.


###Samples

#### Tasks timer

```ballerina
import ballerina/task;
import ballerina/io;
import ballerina/runtime;

int count;
task:Timer? timer;

function main (string[] args) {
        io:println("tasks sample is running");
        scheduleTimer(1000,1000);
runtime:sleepCurrentWorker(100*1000);
}

function scheduleTimer(int delay, int interval) {
    (function() returns error?) onTriggerFunction = onTrigger;
    (function (error)) onErrorFunction = onError;
    timer = new task:Timer(onTriggerFunction, onErrorFunction, interval, delay = delay);
    _ = timer.start();
}

function getCount() returns (int) {
    return count;
}

function onError(error e) {
    io:print("[ERROR] failed to execute timed task");
    io:println(e);
}

function onTrigger() returns error? {
    count = count + 1;
    if(count == 10) {
        error e = {message:"Task cannot be performed when the count is 10"};
        return e;
    }

    if(count == 20) {
        _ = stopTask();
    }
    io:println("on trigger : count value is: " + count);
    return ();
}

function stopTask() returns error? {
    io:println("Stopping task");
    _ = timer.stop();
    count = -1;
    return ();
}

```

In this sample

A task is registered with a delay of 1000 milliseconds and with an intervals of 1000 milliseconds.
Function ```onTrigger ``` is triggered when the clock goes off.
```onError``` function will be executed if an error is returned from the ```onTrigger``` function.
In this sample the count will be incremented by the periodic task and if the count is equal to 10, an error occurs. If the count is equal to 20 the task will be stopped.



#### Tasks appointment

```ballerina
import ballerina/task;
import ballerina/io;
import ballerina/runtime;

int count;
task:Appointment? app;

function main (string[] args) {
        io:println("tasks sample is running");
        scheduleAppointment("0/05 * * * * ?");
        runtime:sleepCurrentWorker(100*1000);
}
function scheduleAppointment(string cronExpression) {
    (function() returns error?) onTriggerFunction = onTrigger;
    (function (error)) onErrorFunction = onError;
    app = new task:Appointment(onTriggerFunction, onErrorFunction, cronExpression);
    _ = app.schedule();
}

function getCount() returns (int) {
    return count;
}

function onTrigger() returns error? {
    io:println("tasks is triggered and the value of count is : " + count);
    count = count + 1;

    if(count == 10) {
        error e = {message:"Task appointment cannot be executed when the count is 10"};
        return e;
    }

    if(count == 20) {
        cancelAppointment();
        io:println("appointment cancelled");
    }
    return ();
}

function onError(error e) {
    io:print("[ERROR] failed to execute timed task");
    io:println(e);
}

function cancelAppointment() {
    _ = app.cancel();
    count = -1;
}

```

In this sample

An appointment is registered with cron expression to run after each 5 seconds.
 Function ```onTrigger ``` is triggered in periods of 5 seconds..
```onError``` function will be executed if an error is returned from the ```onTrigger``` function.
In this sample the count will be incremented by the periodic task and if the count is equal to 10, an error occurs. If the count is equal to 20 the task will be stopped.


