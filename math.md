Math Package.

This provides functionalities for performing fixed-precision integer arithmetic and fixed-precision decimal arithmetic. 

For more information refer to [Math API Documentation](https://ballerina.io/learn/api-docs/math.html)

Sample

```ballerina
import ballerina/io;
import ballerina/math;
function main(string[] args) {

   io:println("Value of PI : " + math:PI);
   io:println("Value of E  : " + math:E);

   float absoluteFloatValue = math:absFloat(-152.2544);
   io:println("Absolute value of -152.2544 : " + absoluteFloatValue);

   int absoluteIntValue = math:absInt(-152);
   io:println("Absolute value of -152      : " + absoluteIntValue);


   float acosValue = math:acos(0.027415567780803774);
   io:println("Arc cosine of 0.027415567780803774  : " + acosValue);

   float arcSineValue = math:asin(0.027415567780803774);
   io:println("Arc sine of 0.027415567780803774    : " + arcSineValue);

   float arcTangent = math:atan(0.027415567780803774);
   io:println("Arc tangent of 0.027415567780803774 : " + arcTangent);

   float cubeRoot = math:cbrt(-27);
   io:println("Cube root of 0.027415567780803774   : " + cubeRoot);
}
```
Save ballerina file as math.bal and run using following command.

``` Ballerina run math.bal```
```
Value of PI : 3.141592653589793
Value of E  : 2.718281828459045
Absolute value of -152.2544 : 152.2544
Absolute value of -152      : 152
Arc cosine of 0.027415567780803774  : 1.5433773235341761
Arc sine of 0.027415567780803774    : 0.02741900326072046
Arc tangent of 0.027415567780803774 : 0.0274087022410345
Cube root of 0.027415567780803774   : -3.0
```’
