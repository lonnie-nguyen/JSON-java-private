COLLABORATORS: LONNIE NGUYEN & TAHA ZIA

Two overloaded methods have been added as asked with the signatures:
1) static JSONObject toJSONObject(Reader reader, JSONPointer path) 
2) static JSONObject toJSONObject(Reader reader, JSONPointer path, JSONObject replacement)
These can be found in src/main/java/XML.java

To test both methods, four JUnit tests have been added in XMLTest
Two to check a valid output for a valid input
Two additional ones for added to check if an empty or null path has been added by user
We could have further added one to check for a wrong path, but after discussion with the TA, decided to assume that a 
correct path will be given.