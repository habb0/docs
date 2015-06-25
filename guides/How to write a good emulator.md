# How to write a good emulator
> by [martinmine](https://twitter.com/martinmine) on [ragezone](https://forum.ragezone.com/f331/write-emulator-991142/)

Writing a good emulator isn't a simple approach. Many people here on the forum (including myself) are self-learned. When you learn to code on your own there are many elements which you may miss out on unless you go to school and learn coding there. In this post I will discuss some important heuristics in order to have an emulator which is actually good. I do know Butterfly does not follow most of these, but the goal is to increase the level of quality in this section, as it is pretty poor at the moment and I do not work on any emulator any more. These heuristics does not only apply to Habbo emulators, but most software in general.

**It is all about good quality code**

 First of all, a good emulator is not about the features. You can have all the features in the world, and there can be bugs. Hundreds. You fix one bug and ten new bugs appear. Suddenly you realize that half your code is wrong and you have to start all over again. I have seen many people trying to extend butterfly with new features (Habbo latino, bcstorm, etc.) and have litterary broken much of the emulator as they have tried to work on it. The main reason is that they added code that is garbage and code which went against (and in some cases broke) the design of the emulator. I won't say the code quality in Butterfly is good (because it is a mess), but honestly, the code I have seen from bcstorm and hlat is not much better.

**Write maintainable code**

 This is easier said than done. In a large system like an emulator you end up with large classes with thousands of lines with code. It is easy to loose track of all the functions, and the objects quickly gets large in memory. It is better to up a class into different classes where each class has its own specific purpose. One example was when I split up the Room class in Bfly/Uber from a huge Room class to several other classes (RoomUserManager, RoomItemManager, etc.). This makes not only the object size smaller, but also increases the modularity of the code. Having a code base with modules (which consists of several classes) that has weak links to other modules is good modulatiry. This let's us easily take out one module and replace it with another one without changing code of half our emulator. As only a few classes know about the modules existence, it is then easy to replace one module with another. A good example is the pathfinder of the database connector in Butterfly. 

**Design patterns are important**

 Design patterns are important when it comes to maintainabillity. It gives you a set of rules that makes your code elegant, maintainable and neat - as long as you follow the pattern. When it comes to modularity, model view controller (MVC) is a popular pattern often used in UI and web-applications but is also much used elsewhere in the software industry. Following the MVC pattern increases the modularity of the code and in the end maintainabillity of the code. 

**Code formatting matters. A lot.**

 Each language has its own unwritten rules when it comes to formatting of both code and class, variable and function names. In C# we write all functions like AFunction(Foo parameter), while some other languages have aFunction(Foo parameter). Following these unwritten rules are important. Mixed up formatting styles looks horrible. But code formatting is not only limited to this. Where to put the statements and which one to group together is a large part. Sure, you can put ten statements at one line to save space in your code, but it is not readable at all. Prioritize a few extra newlines instead of having 100 lines of code in one function with no space between any of the statements. A general rules is to group together functions that belongs together. I personally prefer having a space before an if/while/for/do and at the end of the bracket. Some languages have unwritten rules (C# and Java differs here), and it is important to stick with the one that fits with the language and not be the annoying guy that wants to go against everything "just because he knows better". If there are no "rules", use what you like best, and it is then important to follow this style everywhere and not have ten different coding formatting in one project.

**Poor documentation is the same as no documentation**

 If you are the good-guy coder and actually decide to comment your code, do it well. Don't write comments which barely makes sense to a new dev guy - write short, well-describing comments. It does take time to get good at commenting, but once you get it, you get it. Writing short meaningless comments have no purpose and are therefore a waste of space and time, [this](http://abstrusegoose.com/strips/you_down_wit_OPC-yeah_you_know_me.png) sums it up pretty well. And again, follow standards for the code, PHP (shruggle) has its own standard, and so does C# and Java. Many languages also provides you to generate documentation in e.g. HTML out from the comments in your code. Its pretty cool and useful. 

**Write simple code, not code that just "looks nice"**

 Code which looks nice can be incredibly bad. The code in the Uber source was well formatted, documented, but was horrible. Formatting your code is one thing, but having each function as a clear specific purpose is another. The style and look of the code has a lot to do with the maintainabillity of the code. 

**Understand what happens under the hood**

 If you want to write effective code, you must understand what happens under the hood of the code you are using. What is the difference between HashTable, List<T> and Dictionary<T,V>? What's the difference between asynchronous and blocking calls? How does a socket work? What's the OSI-stack? Understanding when not to use parallelization is also a key point. Understanding how each tool works is crucial to how to use it right. I recently commented on a guy using .NET's parallel for in a pathfinder algorithm. Paralellization sounds cool (is a bitch to type) and is super helpful, but there are many ways it can be used against it purpose and even cause worse performance. Understanding the tools you are using also does not help you unless you understand how the code in your own project works. And again, maintainabillity makes it easier for you to keep track of what is what and what is where.

**Get a nice set of tools and learn how to use them**

 Get good at some frameworks, understand how to use them and specialize yourself in how they work. Once you dig deep into a framework/language, there will always be key points you will identify when you look into other frameworks. Web-languages is a good example where many use the DOM API (but nobody cares about standardizing CSS).

**Find a simple solution to a hard problem**

 The main goal when designing algorithms is to make a neat simple solution to a hard and complex problem. In order to do this you must understand your problem very well. Sometimes the simple solution is not always the best solution, but it is simple to implement, and when something is simple to implement, the chance of bugs decreases a lot and it is a lot easier to debug than for example a recursive function on hundreds of lines. When I designed the DreamPathfinder class used for pathfinding in Butterfly, I thought that the user should make most of the calculation for where to move the user and not let the server do the job. Although users are annoying and dumb, I decided they should do the job and rather make the server "move the user to the square closest to the goal if that square is open". That way, there would be no more whining about "pathfinder is eating the CPU", because it cannot by design. 

**Profiling, profiling, profiling**

 They secret to make Butterfly so fast was profiling and testing. You profile, profile, profile. Once you are done profiling, you profile some more. You thought you was done profiling? Wrong, do some more profiling. Once you are done profiling, you do more profiling. Profiling is important specially in managed languages like C# and Java to understand how your code affects performance. Writing stress tests for profiling is a good idea, but profiling during production may be a bad idea unless you take memory dumps during high CPU-peaks then check the call stack for each thread which lets me get to my next point:

**Learn to use debuggers**

 If you are writing a C# application, learn how to use WinDBG, there are [some good tutorials](https://www.google.no/search?q=windows+dbg+managed&oq=windows+dbg+managed&aqs=chrome..69i57.3056j0j7&sourceid=chrome&espv=210&es_sm=93&ie=UTF-8#q=WinDBG+managed+site%3Achannel9.msdn.com) on Channel9 on how to debug managed applications in it, but learn how to analyze memory dumps. This lets you analyze the memory dumps from high CPU peaks or when your application ran out of memory and you are clueless on what happened. 

**Learn to use an IDE that does not get in your way**

Use your favorite IDE and find tools that makes it productive to write code. Auto-completion is important. Why would anyone want to use an editor which only allows you to enter text? I don't know. A good IDE lets you effectively write code and does not get in your way when you want to test code. It also lets you customize the view so you don't get that bright light when you write your code. (Programmers hate light) A good example of an IDE that does not follow these rules is Eclipse. 

**Monitoring**

 I once had this Brazilian guy which had a script which erased the logs my server created every minute. When his server crashed, I had no clue what happened as a 10 megabyte large logfile had such a critical impact on his disk performance and so did shortcuts on his desktop. Logfiles lets you see what goes wrong for example once an exception is thrown. We do not want exceptions to be thrown all the time as exceptions are expensive to handle. Once an exception occur, we want to log it and be notified of what the exception was and what happened so we can investigate and fix it. Monitoring is not only limited to logs. I created a tool called Butterfly Heatbeat tool along with Butterfly which let me monitor user count, room count, and the overall latency of the network and I could easily see if there was a lag spike. Once there was a lag spike, the issue was to find out if it was a ddos attack, hardware issue, CPU hog or whatever it was. I actually spent weeks looking over code because Dario thought my server caused the lag when he was getting ddossed 24/7.

**Object orientation**

 Keep data members for a class private and make getters/setters for these when accesssed outside the class. This has to do with maintainabillity. Object orientation is about encapsulating data and is also highly related with which kind of software design you have. Restrict which functions other classes should see by using interfaces if this is an issue. 

I hope that some of you kids learn something after reading this. You can learn how to C# in a few days, but you cannot learn how to properly program in a few days. These are just some rules to follow when writing a good emulator but are some of what I believe is the most important ones.
