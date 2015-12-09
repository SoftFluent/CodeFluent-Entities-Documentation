# Source control and generated files

Should I checkin generated files? It depends! This question is not only related to CodeFluent Entities, but to all code generators. There
is a lot of resources on the web about this. For example: 

http://stackoverflow.com/questions/893913/should-i-store-generated-code-in-source-control

Here’s are main advantages of each solution:

## Advantages of storing generated files in the source control

- Developers without CodeFluent Entities can build the code
- You don’t need CodeFluent Entities on your build server
- When you label a version you know all files needed to rebuild the project are there
- When you have large model, you avoid long time code generation
- You can compare different version of files (for instance when a new version of CodeFluent
Entities is released)

## Advantages of excluding generated files in the source control

- Checkin contains less files, so you have a clearer change history
- You avoid potential merge conflicts on generated files
- You can generate your model without checkout files even if you use exclusive checkout policy