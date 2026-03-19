Databases can crash 
It could be that DBMS crashed. It couble be that the computer running the DBMS crashed 

We don't know which statement would execute first and what second, in a multi user database. 

Transactions are sequences of database operations that are executed in a consistency-preserving and reliable way and provide the following properties, known as ACID properties. 

### Atomicity 

All or nothing execution. If any part of a transaction fials, the whole transaction fails and the DBMS will rollback all changes made by the transaction. 

### Consitency

Transactions do not leave the database in an inconsitent way 

### Isolation 

Concurrent transactions are executed as if no transaction is running, essentially emulating serialised execution of the transactions. 

### Durability 

The results of a commited transaction are not lost. The DBMS provides mechanisms to recover from system crashes. 

## Raid 

Redundant Array of Independent/ Inexpensive Disks 

Multi disk setup that provides extra availability and performance by dividing and replicating data across mulitplle disks. There are many RAID schemes. RAID 5 for example allows database server to continue running with reduced performance until the failed disk is replaced. 

## Isolation

A system that is used by the database to run queries in parallel. 
It uses a concurreny control system. 

Isolation are only important when transactions write. If they are read only you dont need to use isolation. 

The DBMS could allow the transactions to run concurrently for each of the following caszes: 
1. both transactions are read only
2. The tranasactions perform modifications, but on different relations. 
3. The transactions perfrom modificatiosn on the same relations but on differrent tuples.

We only use isolation when on e transaction modfies a tuple and another wants to access it. 
### Isolation Levels 

1. Read uncmmited 
	1. Uncommited data changed by other concurrenrt transactions can be read 
	2. Supports Dirty read, Non repeatable read and Phantom read. 
2. Read commited 
	1. Only data committed by other concurrent transactions can be read..
	2. Supports Dirty read and Non Repeatable read 
3. Repeatable read 
	1. Guarantees that all tuples returned by a query will be included if the query is repeated.
	2. Supports Phantom read 
4. Serializable 
	1. Guarantees isolation, but might not be concurrent. 
	2. Prevents all reads 

Different types of reads 

Dirty Read 
Non repeatable read 
Phantom read 

Difference between non repeatable read and phantom read is that the non repeatable read would return modified attributes 2nd time, while phantom read would return a different size of tuples.  

*So Non repeatable -> same size 
Phantom read          -> different size of tuples returned 
