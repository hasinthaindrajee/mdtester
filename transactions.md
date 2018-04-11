Transaction is a sequence of operations performed as a single logical unit of work. In simple terms operations in a transaction either must be completed successfully or fully fail as there are no intermediate states. 

A transaction should exhibit ACID properties. Ie 

1. Atomicity - Either all operations of a transactions should be completed successfully or all operations should fail. There shouldn’t be any intermediate states.
2. Consistency - A transaction should never leave data in an inconsistent state. Any data structure including the data in database should transform from one valid state to another valid state following a transaction.
3. Integrity - Makes sure that no unexpected consequences are present as a result of concurrent executions.
4. Durability - A transactions effects are permanently stored in the system. The modifications persist even in the event of a system failure.

## Terminology

### Transacted functions

In simple terms transacted functions are the functions which can be subjected for transactions. A failure within a transaction will cause to rollback the operations done using transacted functions. 

Ex - Database operations. JMS operations.
Database insert/update operations in ballerina are transacted functions.


### Define a transaction 

A definition of a transaction has few main elements and keywords.

Example definition :

'''
   transaction with retries = 4, oncommit = onSuccess, onabort = onFailure {

    } onretry {

   }
'''


Retries
Number of retries allowed when a transaction failure takes place. In the given example retry count is specified as 4 where a failure of an execution with transaction block will execute the logic inside on retry block on each failure upto four failures.
Oncommit

Onretry
Onretry block is executed on failures within the transaction. This block will be executed multiple times which can be defined with “retries” value.

Oncommit 
Oncommit is a function pointer where it points to an existing function with a string argument. The string argument will be the ID of the transaction.  This function will be executed upon successful execution of transaction without any failures.

Eg- 

function onSuccess(string id) {
   io:println("Transaction completed successfully. Transaction id: " + id);
}

Onabort
Onabort also is a function pointer which points towards a developper developed function with a string argument which is the transaction ID. This function will be executed upon aborting the transaction. 

function onFailure(string id) {
   io:println("Transaction aborted after retrying 4 times. Transaction id: " + id);
}


Fail
This is a keyword which is used to fail a transaction explicitly. This fails the transaction and skip rest of the logic in the transaction but will try to retry. Will be elaborated more in later sections.

Abort
Abort the transaction without retrying even if retrying count and action is specified. Upon abort the function which is pointed from Onabort will be executed.
Sample 

This ballerina sample comprises of database transactions. This sample use MySQL DB and before running the sample copy the MySQL JDBC driver to the BALLERINA_HOME/bre/lib folder.

import ballerina/sql;
import ballerina/io;

endpoint sql:Client testDB {
   database:sql:DB_MYSQL,
   host:"localhost",
   port:3306,
   name:"testDB",
   username:"root",
   password:"root",
   options:{maximumPoolSize:1}
};

function main(string[] args) {

   var updatedRows = testDB -> update("CREATE TABLE IF NOT EXISTS CUSTOMER (ID INT, NAME VARCHAR(30))", null);
   updatedRows = testDB -> update("CREATE TABLE IF NOT EXISTS SALARY (ID INT, MON_SALARY FLOAT)", null);

   transaction with retries = 4, oncommit = onSuccess, onabort = onFailure {
       var c = testDB -> update("INSERT INTO CUSTOMER(ID,NAME) VALUES (1, 'Anne')", null);
       c = testDB -> update("INSERT INTO SALARY (ID, MON_SALARY) VALUES (1, 2500)", null);
   } onretry {
       io:println("Not retrying eventhough 4 retrying attempts are specified.");
   }
   _ = testDB -> close();
}

function onSuccess(string id) {
   io:println("Transaction completed successfully. Transaction id: " + id);
}

function onFailure(string id) {
   io:println("Transaction aborted after retrying 4 times. Transaction id: " + id);
}

Save the content in .bal file. Make sure to change connection properties as per your connection and run the ballerina sample.

This sample

Creates two tables if not exists.
Enters two records to two tables in as a transaction.

Manipulate database queries inside transaction block and it will cause failures of database updates. A single execution failure in this block will fail all operations which are done within the block.

What are transaction failures ?

Any unhandled exception which occurs inside a transaction block will be considered as a transaction failure. Calling “fail” explicitly will cause a transaction failure as well. 

Apart from them, errors which are returned from transacted functions are also considered as transaction failures. 

The second type of failures (ie failures which are returned from transacted functions) behave in a special way. Even though an error is returned from a transacted function, it will not halt, fail or abort the transaction immediately. Instead it will continue the reset of the logic within the transaction and will fail the transaction if any of the transacted functions have returned an error at the end and will rollback operations which took place in transacted functions. 

Onretry will be executed a given number of times upon these transaction failures. 

Fail and abort explicitly within transactions

As described in the introduction, transactions can be failed or aborted explicitly without continuing the rest of the logic in the transaction block. To fail a transaction “fail” keyword is used where as “abort” is used to abort transactions.

Eg 

 transaction with retries = 4, oncommit = onSuccess, onabort = onFailure {
       var c = testDB -> update("INSERT INTO CUSTOMER_UNAVAILABLE(ID,NAME) VALUES (1, 'Anne')", null);

match c {
   int y => {
       insertedCount = y;
   }
   sql:SQLConnectorError err1 => {
       fail;
   }
}

       c = testDB -> update("INSERT INTO SALARY (ID, MON_SALARY) VALUES (1, 2500)", null);
   } onretry {
       io:println("Not retrying eventhough 4 retrying attempts are specified.");
   }


In this sample transacted function update fails since it executes an sql statement on non-existing table. Since an error is returned from update, “fail” is used to explicitly avoid executing the next insert query which is has correct sql statement. Upon failing onretry will be executed.

Likewise “abort” can also be called to abort the execution of the rest of the logic inside transaction. Difference between abort and fail is, upon abort there will be no retries. 
