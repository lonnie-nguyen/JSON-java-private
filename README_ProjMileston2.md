# SWE262P Project
COLLABORATORS: LONNIE NGUYEN & TAHA ZIA
## Milestone 2
### Summary:
Two overloaded methods have been added with the signatures:
> 1) static JSONObject toJSONObject(Reader reader, JSONPointer path) 
> 2) static JSONObject toJSONObject(Reader reader, JSONPointer path, JSONObject replacement)

These can be found in src/main/java/XML.java

To test both methods, four JUnit tests have been added in XMLTest:

- Two to check a valid output for a valid input

- Two additional ones for added to check if an empty or null path has been added by user

We could have further added one to check for a wrong path, but after discussion with the TA, decided to ***assume*** that a 
correct path will be given.

To run the tests, we used the IDE [IntelliJ](https://www.jetbrains.com/help/idea/performing-tests.html) 
Test methods that have been added have the signature:
> shouldReturnCorrectSubObject
> 
> shouldReplaceCorrectSubObject
> 
> shouldThrowExceptionOnEmptyPathOverloadedOne
> 
> shouldThrowExceptionOnEmptyPathOverloadedTwo

### Notes Concerning Milestone 2:
- Both overloaded methods successfully process XML files up to 1.46GB in size.
- By implementing method two in the library, there is a slight performance gain. We are creating the one 
JSONObject that contains the replacement in one go. Otherwise, outside the library we are creating the
original JSONObject, the replacement JSONObject, and then parsing the original to replace it with the replacement.
- An additional helper method was added to facilitate processing: 
  > toJSONObject(Reader, XMLParserConfiguration, String, JSONObject)
- The implementations are not designed to handle instances with arrays.
- To handle the overloaded methods, a separate parse method was implemented with the signature:
  > parse1(XMLTokener, JSONObject, String, XMLParserConfiguration, String, JSONObject)
  - Code diffs from original parse method are marked by single line comments // Task 1 and // Task 2.

## Milestone 3
### Summary:
One overloaded method has been added with the signature:
> static JSONObject toJSONObject(Reader reader, Function<String, String> keyTransformer) 

The method can be found in src/main/java/XML.java

To test the method, three JUnit tests have been added in XMLTest with the signatures:
> checkKeyReplacement
> 
> checkKeyReplacementReverseTransformation
> 
> checkKeyReplacementUpperCaseTransformation

The unit tests compares the actual JSONObject output against the expected JSONObject and passes if both objects are equal.
Each unit test checks a different type of string transformation: adding a prefix to keys, reversing keys, and changing
the case of keys to uppercase.

### Notes Concerning Milestone 3:
- The overloaded method successfully processes XML files up to 1.46BG in size.
- The key transformation is occurring during the parsing of the XML file.
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
    >```{"Element": {"attribute_name1": "attribute value 1", ..., "content": "Content"}}```
  - In line 903, an attempt was made to replace the string "content" returned from ```config.getcDataTagName()``` with its 
  transformed string. For small files this succeeded. Larger files of 1.46GB resulted in OutOfMemoryError.
    - The code in lines 903 - 904 are original to the code and have not been altered by the contributors for submission of 
    Milestone 3 in order to maintain support for large files.
    - All original keys of the XML are transformed.

  

