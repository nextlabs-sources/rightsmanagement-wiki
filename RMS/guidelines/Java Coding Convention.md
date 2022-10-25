# Java Coding Convention
It will assume a baseline of the [Sun coding conventions](http://www.oracle.com/technetwork/java/javase/documentation/codeconvtoc-136057.html) and [Google Java Style](https://google.github.io/styleguide/javaguide.html). We only discusses deltas or divergences from those standards.

## Line ending
All java files should use \n as line ending. Configure your text editor to use UNIX file format by default. You can use `dos2unix` command to convert the line ending before submit. `ant formatjava` can also fix your files line ending.
  

## Indentation
- Please use space characters for the indentation, not tabs.
- Please remove trailing white spaces.
- Line limit is 120.
- You can use `ant formatjava` to automatically format java source file.

## Javadoc and Comments
- Please javadoc your public functions properly, and keep them consistent.
- Make sure put comments for anything that is not obvious.
- Please use TODO and FIXME. Don't introduce new ones unless there's a really good reason

## Declarations
It’s better put declarations just before the first use, which is opposite to sun’s recommendation.

## Statements
- Never omit the braces around a single statement controlled by **if** or **else**. (And the same goes for **for** and **while**.)
- I don't believe that parentheses around return expressions are ever necessary (sun java coding convention, section 7.3), and I'd rather they were never used.

## Naming
- Enum should be treated the same as class (Note: use singular nouns: Color, not Colors.) 
- We do NOT use prefix for member/static variables, though Android uses prefix **m** for member.

## Unused code
- If the code is not used any more, remove them from source, not just comment it out. You can always find them back in Version Control System.
- Remove `// TODO Auto-generated catch block` unless you really want to implement it in future.

## Logging
- Never log confidential/privacy data, at least they should be masked in logs.
- Never flood log output.
- Avoid logging in a loop. If it's really necessary, try to use a rate-limiting mechanism or only compiled into local build.
- Avoid repeatedly logging the same information.
- Avoid print log to System.out or System.err
- Use proper log levels
    - Error level log should really be error case which indicate there is a bug in system.
    - Verbose level should only be compiled in local debug build.
    - Debug level should be turned off in release build by default and should be able to turned on at running time.
    - Logging that reports successes should never be used at levels higher than DEBUG.
- Avoid unnecessary string concatenate
    - Use vararg API if possible.
    - Test if log level is enabled before preparing log data

## Exception handling
- Avoid capturing RuntimeException and Throwable.
- Avoid throws **Exception** or **Throwable** in method declaration.
- Preserve stacktrace.
- Don’t use exception as error code or return value.
- Don’t declare in method if the Exception is not actually thrown.

## Constants only interface
Constants only interface is discouraged in many books. But it actually provide many advantage:
- Constants is actually compiled into each classes.
- Constants only interface will not be loaded at runtime.

## Inner class
- Reduce anonymous class if possible
    - Anonymous class should only be used to improve readability.
    - Consider to merge similar anonymous class into one.
- Try to use static inner class if possible
- Consider to make the base class implement interface to avoid creating new inner class.
- Don’t extends collection class just for initialization.

## Thread
- Consider to use thread pool when need periodically create thread.
- Uncaught exception may lost when using ExecutorService.submit(), make sure call Future.get() to handle the exception properly.

## Covariant override
- Covariant returns is handy to more accurately convey the type of object returned by a method. However, the feature should be used judiciously. In General, the benefit it provided is not worth the confusion it created.
- Avoid overriding method from Object.
- Only consider it if the class is final.

## toString()
- Object.toString() is designed for display only. The result may be different in different jvm. Avoid using default Object.toString() as part of logic (except for some class like StringBuilder, ByteArrayOutputStream, which override  toString())
- Don’t name your method as toString(…), it's hard to be refactored and search source code.
