# SWE262P Project
COLLABORATORS: LONNIE NGUYEN & TAHA ZIA
## Milestone 2
### Summary:
Two overloaded methods have been added to src/main/java/XML with the method declarations:
```java
static JSONObject toJSONObject(Reader reader, JSONPointer path)
``` 
```java
static JSONObject toJSONObject(Reader reader, JSONPointer path, JSONObject replacement)
```

To test both methods, four JUnit tests have been added in XMLTest:

- Two to check a valid output for a valid input

- Two additional ones for added to check if an empty or null path has been added by user

We could have further added one to check for a wrong path, but after discussion with the TA, decided to ***assume*** that a 
correct path will be given.

To run the tests, we used the IDE [IntelliJ](https://www.jetbrains.com/help/idea/performing-tests.html) 
Test methods that have been added have the signature:
> shouldReturnCorrectSubObject()
> 
> shouldReplaceCorrectSubObject()
> 
> shouldThrowExceptionOnEmptyPathOverloadedOne()
> 
> shouldThrowExceptionOnEmptyPathOverloadedTwo()

### Notes Concerning Milestone 2:
- Both overloaded methods successfully process XML files up to 1.46GB in size.
- By implementing method two in the library, there is a slight performance gain. We are creating the one 
JSONObject that contains the replacement in one go. Otherwise, outside the library we are creating the
original JSONObject, the replacement JSONObject, and then parsing the original to replace it with the replacement.
- An additional helper method, with the following signature, was added to facilitate processing: 
  > toJSONObject(Reader, XMLParserConfiguration, String, JSONObject)
- The implementations are not designed to handle instances with arrays.
- To handle the overloaded methods, a separate parse method was implemented with the signature:
  > parse1(XMLTokener, JSONObject, String, XMLParserConfiguration, String, JSONObject)
  - Code diffs from original parse method are marked by single line comments // Task 1 and // Task 2.

## Milestone 3
### Summary:
One overloaded method has been added to src/main/java/XML with the method declaration:
```java
static JSONObject toJSONObject(Reader reader, Function<String, String> keyTransformer) 
```

To test the method, three JUnit tests have been added in XMLTest with the signatures:
> checkKeyReplacement()
> 
> checkKeyReplacementReverseTransformation()
> 
> checkKeyReplacementUpperCaseTransformation()

The unit tests compares the actual JSONObject output against the expected JSONObject and passes if both objects are equal.
Each unit test checks a different type of string transformation: adding a prefix to keys, reversing keys, and changing
the case of keys to uppercase.

### Notes Concerning Milestone 3:
- The overloaded method successfully processes XML files up to 1.46BG in size.
- The key transformation is occurring during the parsing of the XML file.
- A third parse method was implemented with the method declaration:
```java
private static boolean parse2(XMLTokener x, JSONObject context, String name, XMLParserConfiguration config, 
        Function<String, String> keyTransformer) throws JSONException
```
- Regarding performance:
  - Implementing key transformation inside the library allows for performance gains when concerning resources, time, and
  effort. The XML file is parsed once and the desired JSONObject is created.
  - The performance of key transformation outside the library is poor due to resource limit (memory) and required
  implementation effort (user implemented recursion, multiple events of parsing the information, multiple 
  JSONObject creations).
- This implementation works with JSONArrays.
- For large XML files, key transformation is not successful on "content" keys added to the JSONObject by the library.
  (Refer to XML.java lines 903 - 904.)
  - This occurs when the original XML has an element with one or more attribute names and corresponding
  attribute values. 
  - The library handles this situation by treating each element and attribute as a key in the final JSONObject.
  - The side effect is the content between the element tags is left without its own key, so here the library keeps the assigned
  key of "content." 
  - The library uses JSONObject jsonObject as a building block. 
    - The CDATA Tag Name of "content" is given to the jsonObject, which is usually overwritten when context calls accumulate
    to join the correct key and value pair within the JSONObject.
    >Example:
    > 
    >```<Element attribute_name1="attribute value 1"...>Content</Element>```
    > 
    >```{"Element":{"attribute_name1":"attribute value 1",...,"content":"Content"}}```
  - In line 903, an attempt was made to replace the string "content" returned from ```config.getcDataTagName()``` with its 
  transformed string. For small files this succeeded. Larger files of 1.46GB resulted in OutOfMemoryError.
    - The code in lines 903 - 904 are original to the code and have not been altered by the contributors for submission of 
    Milestone 3 in order to maintain support for large files.
    - All original keys of the XML are transformed.

## Milestone 4
### Summary:

One method has been added to src/main/java/JSONObject with the method declaration:
```java
public Stream<JSONObject> toStream()
```
This method uses the spliterator to traverse and stream JSONObject nodes as they are encountered.
- A **spliterator** method was added, which returns a JSONObjectSpliterator of the passed in JSONObject.
- The inner class **JSONObjectSpliterator**, which implements Spliterator<JSONObject>, was also added.
- The method **tryAdvance** under the class **JSONObjectSpliterator** contains the logic to traverse the
JSONObject's elements.

To test the methods, three JUnit tests have been added to JSONObjectTest:

>testToStreamFilterMapCollect()
>
>testToStreamEmptyJsonObject()
>
>testNullJsonObjectToStream()

The test checks the capabilities of the json stream by using filter, map, and collect methods provided by
the stream library in java.

### Notes Concerning Milestone 4:
- Large XML/JSON files can be streamed when filtering, but will result in an OutOfMemoryError if attempts are
made to stream the entire file as output to console or a new file.
- For this milestone, a "node" is considered to be a JSONObject. This is including any JSONObjects nested within the original
JSONObject (think nesting dolls). Defining a node in this way simplifies the streaming process and prevents unnecessary creation of 
JSONObjects that would be used only for streaming to the client output.
  >Example:
  > 
  > The following JSONObject has 2 nodes.
  > 
  >```{"Element":{"attribute_name":"attribute value","content":"Content"}}``` 
  > 
  >```{"attribute_name":"attribute value","content":"Content"}```
- It is assumed that users of the toStream method will be familiar with the package 
[java.util.stream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html) and have experience 
using methods of the [stream interface](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html).
- A usage example for this method would be to retrieve **only** the driving directions from a JSON file (ex. route.json) 
  obtained through MapQuest.
    - Driving directions are identified by the key "narrative" where the value of "narrative" is a String. The String represents
  a direction sentence. 
    - Manually retrieving the first direction is cumbersome as it requires traversal along the 
  following path: /route/legs/0/maneuvers/0/narrative. Getting all the directions requires this to be repeated for the 
  entire JSONArray maneuvers. The toStream() method can do all of this by chaining methods of the stream API into one 
  line of code. Example shown below is broken down to show the different chained methods:
  ```java
  route.toStream()
        .filter(node -> node.has("narrative"))
        .forEach(node -> System.out.println(node.get("narrative")));
  ```
## Milestone 5
### Summary:

Two asynchronous methods have been added to src/main/java/XML with the method declarations:
```java
public static void toJSONObject(Reader reader, Consumer<JSONObject> func, Consumer<Exception> exception)
```
```java
public static CompletableFuture<JSONObject> toFutureJSONObject(Reader reader)
```
The first method toJSONObject:
- Uses an explicitly created thread to execute its tasks.
  - It first parses XML to JSONObject. 
  - The same thread will process the JSONObject according to the Consumer function func passed in through the client. 
    - The function func takes a JSONObject as a parameter and returns nothing. 
  - If something goes wrong during this process, an exception will be handled according to the Consumer function
  exception passed in as the last argument. 
    - The function exception takes an Exception as a parameter and returns nothing.

The second method toFutureJSONObject:
- Uses a thread from the global ForkJoinPool.commonPool().
  - Parses XML to JSONObject.
  - Returns a CompletableFuture of type JSONObject, which can be retrieved on the client side with get() and used
  accordingly.
  
To test the methods, three JUnit tests have been added to XMLTest

>testAsynchronousToJSONObject()
> 
>testAsynchronousToJSONObjectWithSleep()
> 
>testAsynchronousToJSONObjectException()
> 
>testFuturesToJSONObject()

These tests make sure the correct JSONObject is returned from the given XML. The first method uses the normal overloaded asynchronous method, whereas, the fourth test uses Futures to process the XML. In the second method, Thread.sleep has been added to test the asynchronous functionality.
Finally, the third method tests to check whether an exception is thrown when a null XML and an invalid XMl is given.

### Notes Concerning Milestone 5:
- Both methods are capable of asynchronously processing files up to 1.46GB in size.
- When writing large JSONObjects to file, using the method write(Reader r) will throw an OutOfMemoryError. To avoid this,
use the write() method which allows for pretty print.
- When comparing performance, both methods are on par with each other. The toFutureJSONObject finishes its task faster 
because it is only parsing XML to JSONObject. While toJSONObject is creating a JSONObject and writing it to file.
  - It can be argued that using CompletableFuture is better because it does not explicitly create a thread for its task,
  making it less computationally expensive.