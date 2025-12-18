# Iterator Actor

[![Image](https://www.vipm.io/package/ls_instruments_ag_lib_iterator_actor/badge.svg?metric=installs)](https://www.vipm.io/package/ls_instruments_ag_lib_iterator_actor/) [![Image](https://www.vipm.io/package/ls_instruments_ag_lib_iterator_actor/badge.svg?metric=stars)](https://www.vipm.io/package/ls_instruments_ag_lib_iterator_actor/)

## Authors
Andrea Vaccaro, Albert Adiyatullin  
LS Instruments AG

# Description

A library implementing a high-level Actor Framework-based Iterator. The caller Actor can configure the Iterator Actor to iteratively execute one of its methods by creating the corresponding Actor Framework messages.

## Main Features

* Fully Actor Framework Based.
* Iterate at a certain time period.
* Waits on iteration completion and reports an error to caller if the corresponding timeout is exceeded.
* Prepare and Conclude iterations definable.
* Multiple iterator end condition definable: number of iterations, time elapsed, iteration result.

## VIPM Package Installation

Install the **Iterator Actor** VIPM Package by means of the VI Package Manager. The VIPM package can be found  <a href="https://www.vipm.io/package/ls_instruments_ag_lib_iterator_actor/" target="_blank">here</a> 

## Library Walk-Through 

### Caller Actor Preparation
Implement the methods that are going to be executed as Prepare, Iterate, Conclude iteration steps. Create the corresponding AF messages.

### Configure and Launch the Iterator

* Place an "Itearator NR Actor.lvclass" constant on the block diagram.
* Place a "Iterator Settings.ctl" constant on the block diagram and type in the relevant desired settings. In this case, as shown in the picture, we wish to perform 100 iterations ("Active Stop Conditions.Number of Iterations" set to TRUE and "Number of Iterations" set to 100).
* Bundle to the resulting cluster the messages created before to the fields "Prepare Msg", "Iterate Msg", "Conclude Msg".
* Wire the "Itearator NR Actor.lvclass" constant to a property node and set the "Settings" property to the "Iterator Settings.ctl" cluster just prepared.
* Launch the just configured "Itearator NR Actor.lvclass" actor by means of the "Launch Nested Actor.vi" AF method and store the Iterator enqueuer.

![](media/Confugure%20and%20Launch.png)

#### Alternative "Active Stop Condition" Configurations
The "Active Stop Condition" cluster can be configured to trigger different stop conditions. The different conditions can be mixed. The other stop conditions are the following:
##### Time Elapsed
If we set the "Time Elapsed" Boolean flag to TRUE the Iterator will stop after the iteration time reaches a value specified by the "Time Before Stop (ms)" value in milliseconds.
##### Iteration Result
If we set the "Iteration Result" Boolean flag to TRUE we can stop the iteration internally by sending the Break message to the Iterator. If the flag is set to FALSE sending the Break message will have no effect. 

If the user wants to know about which condition triggered the Iterator stop, he has to subcalss the abstract message "Iterator Report Iterations End Msg.lvclass" that is sent to the caller at the end of the iteration. The concrete implementation should be stored in the active "Iteration Settings.ctl" cluster as illustrated in the previous screenshot. Such message bears the "Stop Reason" enum which clarifies which condition triggered the Iterator stop and the possible iterator errors occurred during the iterator operation.

The "Stop Reason" possible values are the following:
* Number of Iterations.
* Elapsed Time.
* Stop Internally: One of the iteration step execution sent the Break message to the Iterator and the "Iteration Result" stop condition was active.
* Stopped Externally: the caller actor has sent the "Stop Iterations" message to the iterator. This "Stop Reason" is not subject to any stop condition being enabled.

### Errors Reported
#### Errors Reported by the "Iterator Report Iterations End Msg.lvclass" Message
* **code 7900**: Input parameters are not valid.
* **code 7901**: Preparation call timed out.
* **code 7902**: Iteration call timed out.
* **code 7903**: Conclusion call timed out.

#### Errors Reported to the Caller's "Handle Error"
* **code 7904**: Cannot break Iterators configured with "Auto Iteration Notification" OFF.
* **code 7905**: Cannot break, Iterator not iterating.
* **code 7906**: Cannot stop iterations, Iterator not iterating.
* **code 7907**: Cannot start iterations, Iterator already iterating.

### Starting/Stopping Iterations
To start the iterations send the "Start Iterasions Msg.lvclass" to the iterator:

![](media/Start%20Iterations.png)

To stop the iterations send the "Stop Iterasions Msg.lvclass" to the iterator:

![](media/Stop%20Iterations.png)

### Changing Iterator's Setting During its Execution
To change the Iterator settings during its execution the user has to send the "Write Settings Msg.lvclass" to the iterator:

![](media/Change%20Settings.png)

Pay attenton that this will overwrite ***all*** the settings.
### Stopping the Iterator
Since the iterator is generally launched by means of the "Launch Nested Actor.vi" AF method with the Auto-Stop condition set to the default TRUE, the iterator will automatically stop when the caller actor stop. If for any particular reason one wants to explicitly stop the Iterator sending the stop message will do so.

## Contributing

Feel free to fork and submit pull requests. After cloning the repository, the best way to install the required dependencies is to install the VIPM pakcage.
 
