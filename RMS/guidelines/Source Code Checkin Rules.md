## Code format and static code analysis tools

To ensure highest code quality, code format and static java code analysis is enforced:

- ### Java compiler warnings
    All java compiler warnings are treated as error. We should address the warnings in source code. Use **@SuppressWarnings()** in source code to avoid compiling failure. **@SuppressWarnings()** should be justified case by case.

- ### Java code formatter
    Our java coding style is defined in libraries/tools/formatter/conventions.xml

    All the projects ant build contains a `ant formatjava` target. Always use it to format your java source code before commit.

- ### XML formatter
    All the projects ant build contains a `ant formatxml` target. Always use it to format your XML source file before commit.

- ### Checkstyle
    We use Checkstyle to help us write Java code that adheres to a coding standard. For more information about Checkstyle see: [http://checkstyle.sourceforge.net/](http://checkstyle.sourceforge.net/)

    All the projects ant build contains a `ant checkstyle` target. Warnings will fail the build, error will be saved in build/checkstyle.html file. Make sure address all issues Checkstyle reported before commit.

- ### PMD
    We use PMD to help us find common programming flaws. For more information about PMD see: [http://pmd.github.io](http://pmd.github.io)

    All the projects ant build contains a `ant pmd` target. Warnings will fail the build, error will be saved in build/pmd.html file. Make sure address all issues PMD reported before commit.

- ### Findbugs
    We use Findbugs to help us look for bugs in Java code. For more information about Findbugs see: [http://findbugs.sourceforge.net](http://findbugs.sourceforge.net)

    All the projects ant build contains a `ant findbugs` target. Warnings will fail the build, error will be saved in build/findbugs.html file. Make sure address all issues Findbugs reported before commit.

## False positive
checkstyle,PMD and findbugs may generate false positive warning. You can suppress those errors in that cases. Suppressing those warnings is justified case by case.
## Code validation

In each project there is ant build target `ant validate` to help check if code is compliance with coding standard. It use all above tools.

There is also a top level build target `ant validateall` to validate all projects. This target will be executed on Jenkins for continuous build. Validation failure will fail the build.
 
## Other general guidelines:
* Do not make unnecessary changes
- Rename file only when necessary. Renaming a file make it hard to diff/track back in history 
* File name must be meaningful and self explain.
* Comment out code is not recommended. If code is not in use, just remove them from source code.
* If you want to do some cosmetic changes (reformatting, refactor), keep such changes in a separate commit. 
* Make each change unit as small as possible. Big changes is hard to review. It's also hard to track back if there is bug in it.
* Provide meaningful title for each commit. When rebasing changes, keep proper title for the final changes.
* Review your changes before submit. Use `git status`, `git diff` and `git show --name-status` to review your changes, and see if all of them are necessary. Developer often forget adding new code into commit, above commend will help you identify many silly mistakes.
* Review your own changes on after submit to gerrit on gerrit. Many times you will find issues by yourself.

## Jenkins build

After checking in code, a continuous build will be scheduled in 5 minutes. Please resolve the issue immediately if the build is broken and email to other developer that you are working on it. If you can't resolve the issue in a timely fashion, please revert your commit.

You can view build result on:
http://nxt-build24.nextlabs.com:9031/job/RMSNG-master-ci/

