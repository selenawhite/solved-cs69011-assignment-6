Download Link: https://assignmentchef.com/product/solved-cs69011-assignment-6
<br>
Access to the reservation database of Indian Railways is a typical example of a critical section. There may be several concurrent processes that wish to access the database for checking the reservation status and for booking reservations. We may broadly classify these processes into two types, namely:

<ul>

 <li><em>Readers: </em>These processes simply read the status of the reservations</li>

 <li><em>Writers: </em>These processes change the reservation status by making a reservation</li>

</ul>

For each train, we may have a <em>read lock </em>and a <em>write lock</em>. A reader must acquire a <em>read lock </em>in order to study the reservation status, while a writer must acquire a <em>write lock</em> to write the database. When a process does not get its desired lock, it must wait for it. We are given the following constraints on the acquisition of locks:

<ol>

 <li>Write locks are exclusive — a process can obtain a write lock only when no process has a read or write lock.</li>

 <li>Read locks are shared — one or more processes can obtain read locks when no process has a write lock.</li>

 <li>Writers have higher priority — new readers are not granted as long as there are waiting writers.</li>

 <li>Locks are non-preemptive</li>

</ol>

We will assume that each process holds at most one lock at a time. Suppose the reservation database has the following structure:

struct train{

int train-id;

int AC2, AC3, SC;  // No. of available berths                                 struct reservation *rlist;

}




struct reservation{

int pnr; char passenger-name[20], age[3], sex; char class[4];  // AC2, AC3, or SC flag status; // waitlisted or confirmed

}




struct train *rail-data;

Each train has 3 classes, namely 2AC, 3AC, and SC (sleeper class). <em>rlist</em> is a dynamically allocated array of reservations. <em>rail-data</em> is a dynamically allocated array of train records. We assume that all this is for a given date only. The possible transactions are as follows:

<ul>

 <li>Check availability, given the train-id and class</li>

 <li>Make a reservation — if not available, then it goes into waiting list. The position on the waiting list is given by the position in rlist.</li>

 <li>Cancel a reservation</li>

</ul>




<strong>Task-1: </strong>

<strong> </strong>

Write the following functions:




<ul>

 <li>get-read-lock( train-id ) //<em> The calling process blocks until it gets a read lock on the given train</em></li>

 <li>get-write-lock( train-id ) // <em>The calling process blocks until it gets a write lock on the given train</em></li>

 <li>release-read-lock( train-id )</li>

 <li>release-write-lock( train-id )</li>

</ul>




Create a system of 4 child processes, P1, P2, P3, P4, which respectively reads input files in1, in2, in3, in4. Each input file contains a list of instructions of the form:




reserve ánameñ áageñ ásexñ átrainidñ áclassñ       cancel ápnrñ




The reservation database is assumed to be in shared memory. Each reservation request must first check the availability using a read-lock and then obtain a write-lock for making the reservation, if available. If no reservation is available, then the passenger will be waitlisted. If a passenger cancels a reservation, the first waitlisted passenger will be confirmed. The processes sleep for 1 second after processing each reservation / cancellation request. The processes print the outcome of their reservation / cancellation after processing each request.




For simplicity assume that there are 3 trains, and 10 berths of each class in each train.




<strong> </strong>

<strong>Task-2: </strong>




In continuation with Task 1, we look at the Railways database as a 3-tier hierarchical database.




<ul>

 <li>At the highest tier, we have the whole database, containing the reservation status of all trains.</li>

 <li>At the middle tier, we have individual trains.</li>

 <li>At the lowest tier, we have the reservation classes (such as 1AC, 2AC, CC, SC, etc) in a given train.</li>

</ul>




Objects of the higher tiers are collections of objects of the lower tier (for example, the reservation status of a train in a given date is the collection of the reservation status of the different classes in the train).




The object hierarchy may therefore be represented by a tree with the root representing the whole database, nodes at depth 1 representing trains, and nodes at depth 2 representing reservation classes in a train. Processes can acquire the following types of locks on the nodes of the tree:




<ul>

 <li><em>Shared locks </em>(S): An S-lock at node n is equivalent to read locks on all nodes in the sub-tree rooted at n.</li>

 <li><em>Exclusive locks </em>(X): An X-lock at node n is equivalent to write locks on all nodes in the sub-tree rooted at n.</li>

 <li><em>Intention shared </em>(IS): A process acquires an IS-lock at node n if it requires a S-lock on one or more nodes in the sub-tree rooted at n.</li>

 <li><em>Intention exclusive </em>(IX): A process acquires an IX-lock at node n if it requires a X-lock on one or more nodes in the sub-tree rooted at n.</li>

 <li><em>Shared intention exclusive </em>(SIX): An SIX-lock at node n is equivalent to a S-lock at n plus a IX-lock at n.</li>

</ul>




In order to lock a node, a process must acquire the appropriate locks on all nodes in the path from the root to that node. The lock compatibility matrix is as follows.




<table width="290">

 <tbody>

  <tr>

   <td width="47"><strong>  </strong></td>

   <td width="47"><strong> IS </strong></td>

   <td width="49"><strong> IX </strong></td>

   <td width="49"><strong> S </strong></td>

   <td width="50"><strong> SIX </strong></td>

   <td width="49"><strong> X </strong></td>

  </tr>

  <tr>

   <td width="47"><strong> IS </strong></td>

   <td width="47"> true</td>

   <td width="49"> true</td>

   <td width="49"> true</td>

   <td width="50"> true</td>

   <td width="49"> false</td>

  </tr>

  <tr>

   <td width="47"><strong> IX </strong></td>

   <td width="47"> true</td>

   <td width="49"> true</td>

   <td width="49"> false</td>

   <td width="50"> false</td>

   <td width="49"> false</td>

  </tr>

  <tr>

   <td width="47"><strong> S </strong></td>

   <td width="47"> true</td>

   <td width="49"> false</td>

   <td width="49"> true</td>

   <td width="50"> false</td>

   <td width="49"> false</td>

  </tr>

  <tr>

   <td width="47"><strong> SIX </strong></td>

   <td width="47"> true</td>

   <td width="49"> false</td>

   <td width="49"> false</td>

   <td width="50"> false</td>

   <td width="49"> false</td>

  </tr>

  <tr>

   <td width="47"><strong> X </strong></td>

   <td width="47"> false</td>

   <td width="49"> false</td>

   <td width="49"> false</td>

   <td width="50"> false</td>

   <td width="49"> false</td>

  </tr>

 </tbody>

</table>




Incompatible locks on the same object is not permitted at any given point of time.




The priority among locks is as follows: X &gt;  SIX &gt; S &gt;  IX &gt; IS.  // <em>X has highest priority, IS has lowest priority</em>




<ol>

 <li>Reconstruct your data structure for the Railways database to accommodate the three levels of granularity.</li>

 <li>Write functions for get-&lt;lock-type&gt;-lock( train-id ) and release-&lt;lock-type&gt;-lock( train-id ), where &lt;locktype&gt; can be S, X, IS, IX, SIX.</li>

 <li>Write functions for the following types of transactions:</li>

</ol>




<ul>

 <li>Making a reservation in a given train and class</li>

 <li>Checking availability in one or more classes in a given train and booking if available</li>

 <li>Checking the total availability in a given class among one or more trains with a given source and destination</li>

 <li>Making reservations in two connecting trains only when both connections have availability</li>

 <li>Adding an extra coach in a train (amounts to adding to the availability of berths)</li>

 <li>Adding an extra train</li>

</ul>




Create a suitable interface to demonstrate the working of the reservation system in accordance with the above system.


