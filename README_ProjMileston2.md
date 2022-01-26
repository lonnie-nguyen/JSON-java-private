# SWE262P Project
**COLLABORATORS: LONNIE NGUYEN & TAHA ZIA**
## Milestone 2
### Summary:
Two overloaded methods have been added as asked with the signatures:
> 1) static JSONObject toJSONObject(Reader reader, JSONPointer path) 
> 2) static JSONObject toJSONObject(Reader reader, JSONPointer path, JSONObject replacement)

These can be found in src/main/java/XML.java

To test both methods, four JUnit tests have been added in XMLTest
Two to check a valid output for a valid input
Two additional ones for added to check if an empty or null path has been added by user
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
original JSONObject, the replacement JSONObject, and then parsing the orignal to replace it with the replacement.
- An additional helper method was added to facilitate processing: 
  > toJSONObject(Reader, XMLParserConfiguration, String, JSONObject)
- The implementations are not designed to handle instances with arrays.
- To handle the overloaded methods, a separate parse method was implemented with the signature:
  > parse1(XMLTokener, JSONObject, String, XMLParserConfiguration, String, JSONObject)
  - Code diffs from original parse method are marked by single line comments // Task 1 and // Task 2.