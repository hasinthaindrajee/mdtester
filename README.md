
This provides a basic API for logging which helps to support maintaining,  tracing, diagnosing and servicing software for service engineers, system administrators and developers. 

Below are the key terms and functionalities exposed from this package.

### Loggers 

Loggers are defined over packages. Ie there are dedicated loggers for each package and package will be printed with logs so that it is self contained with rest of the information.  In the below sample **[foo]** is the package where the ballerina logging logic (test.bal) resides.

 Ex - 
 ```2018-04-09 11:33:21,300 ERROR [foo] - This is an error log.```


### Log Outputs 

All logs are written to **stderr** by default which is more container friendly than writing the logs to a file. Depending on requirements logs can be redirected to a log file using output redirection. Furthermore, different logging output levels can be defined for different packages. Refer **Log Level** section for more information about controlling log output levels.

Ex - Suppose you have a logging sample as described in [Ballerina by Example: Log API](https://ballerinalang.org/docs/by-example/log-api). If the given sample is run with below command the logs will be printed in the console . (test.bal contains   the Ballerina code for logging).

	```ballerina run test.bal```

Instead you can redirect stderr stream to a log file using below command. 

	```ballerina run test.bal 2> test.log```


### Log Level

 This includes a set of standard levels of logs which are used commonly such as **INFO, DEBUG, TRACE, WARN, ERROR**. By default the level of logging in Ballerina programs is **“INFO”**. In addition to these log levels, there are two additionals levels: OFF and ALL. OFF turns off logging and ALL allows all log levels. 


#### * Control log level from CLI

Output log levels can be controlled over CLI arguments as well as through configurations. To control output log level over CLI use the below command.

```ballerina run test.bal -e ballerina.log.level=<LOG_LEVEL>```

Ex - ballerina run test.bal -e ballerina.log.level=ERROR -e foo.log.level=ERROR

ballerina.log.level=ERROR defines the overall logging level whereas foo.log.level=ERROR controls the logging level of package foo

#### * Controlling log level from configuration file. 

Create a file ballerina.conf at source root level if you do not have this config file already created. Add the following content and save it.
```
[ballerina.log]
level="ERROR"

[foo]
level="ERROR"
```

### Sample : 

In this sample term <project_home> will be referred to the root directory where the ballerina project resides.

Create a directory with name foo.
Add a file named test.bal and add the following content to the file.
```
package foo;
import ballerina/log;

function main(string[] args) {
  error err = {message: "error occurred"};
  log:printDebug("debug log");
  log:printError("error log");
  log:printErrorCause("error log with cause", err);
  log:printInfo("info log");
  log:printTrace("trace log");
  log:printWarn("warn log");		
}
```
  Save it.

Stay in <project_home> directory and run the ballerina file.
  
``` ballerina run foo/ ```

To write logs to test.log file execute the below command.

```ballerina run foo/ 2> test.log```

To enable only debug logs, run the ballerina program with below arguments.
  
``` ballerina run foo/ -e ballerina.log.level=ERROR -e foo.log.level=ERROR ```

		
[1] https://ballerinalang.org/docs/by-example/log-api
