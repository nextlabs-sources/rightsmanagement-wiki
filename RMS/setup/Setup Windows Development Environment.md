# Setup Windows Development Environment

## Overview
We heavily use command line tools in our development. This document provide a standardized development environment and process to ensure all developers in sync.

## Required tools on Windows

All following installation command requires you run **cmd.exe as administrator**. You can also use **git bash**.

- **Chocolatey**: The package manager for Windows

    We use Chocolatey to install required tools on Windows. For more information about Chocolatey, see: [https://chocolatey.org](https://chocolatey.org). Run following command in cmd.exe:

		C:\> @powershell -NoProfile -ExecutionPolicy unrestricted -Command "(iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))) >$null 2>&1" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin

- **git**

	MinGW will be installed together with git command line tool. You need add bash path to Windows PATH environment variables: `C:\Program Files\Git\bin`

		C:\> choco install git

    Clone the repositories: rmsng and rmsng-binaries

- **ant**

	Download the apache-ant-1.9.7-bin.zip archive from: [https://ant.apache.org/bindownload.cgi](https://ant.apache.org/bindownload.cgi). Update the PATH variable. For example: C:\Users\rchoudhury\Documents\apache-ant-1.9.7\bin

- **maven**
    
     Follow the steps on how to setup maven from this site: [https://www.mkyong.com/maven/how-to-install-maven-in-windows/](https://www.mkyong.com/maven/how-to-install-maven-in-windows/)

- **npm**

	Download from the site: [https://nodejs.org/en/download/](https://nodejs.org/en/download/)
    Update the PATH variable, for example: C:\Program Files\nodejs 

- **grunt**

		C:\> npm install -g grunt-cli

- **angularJS**

        C:\> npm install angular@1.4.7

- **tomcat**

	Using Windows installer is not recommended. The easiest way is download tomcat8 binary zip file from [http://tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi), and manually unzip it under user user's Programs folder, for example: `C:\Users\Myname\Programs\apache-tomcat-8.0.32`

    Create a system variable CATALINA_HOME and set it to for example: C:\Users\..\..\apache-tomcat-8.0.36\
    Update your PATH variable as %CATALINA_HOME%\bin

- **findbugs 3.0.1**

	We use findbugs as one of the static code analysis tools. Please download findbugs: [http://findbugs.sourceforge.net/downloads.html](http://findbugs.sourceforge.net/downloads.html)

    Create a system variable FINDBUGS_HOME and set it to for example: C:\Users\...\...\findbugs-3.0.1
    Update your PATH variable as %FINDBUGS_HOME%\bin


- **PostgreSQL server**

	Download PostgreSQL server installer from [https://www.postgresql.org/download/windows](https://www.postgresql.org/download/windows)

- **JCE Unlimited Strength Jurisdiction Policy**

	Our solution key length requires JCE Unlimited Strength Jurisdiction Policy. Download it from [http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html](http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html). Unzip files and copy them into 
`C:\Program Files\Java\jdk1.8.0_92\jre\lib\security` folder.

## IDE
Developers can choose any IDE that they prefer. However we strongly recommended **Eclipse**, you can download Eclipse from:  [http://www.eclipse.org/](http://www.eclipse.org/). IntelliJ Idea is an alternative. 

## Configure your bash
You can setup your bash environment by adding .bashrc and .profile
Create .bashrc file:
```
C:\> bash
$ vi ~/.bashrc
```
Add following lines in .bashrc file:
```
source ~/.profile
```

Create .profile file with content, modify path to match your local environment
```
PS1='[\w]>'

export FINDBUGS_HOME="/C/Program Files/findbugs-3.0.1"
export CATALINA_HOME="/C/Users/XXXX/Programs/apache-tomcat-8.0.32"
export JPDA_ADDRESS=8000
export JPDA_TRANSPORT=dt_socket
export PGUSER=rms
export PGHOST=localhost

PATH=.:$PATH:$CATALINA_HOME/bin

export DEBUG PATH JAVA_HOME

alias edit="/C/Program\ Files/Editplus/editplus.exe"
alias chrome="/C/Program\ Files\ \(x86\)/Google/Chrome/Application/chrome.exe"
alias pub="pub.bat"

alias cds="cd /j/source"
alias cdrms="cd /j/source/nextlabs/rms/server"
alias cdrouter="cd /j/source/nextlabs/router/server"
alias cdl='cd "$CATALINA_HOME/logs"'
alias cdf='cd "$CATALINA_HOME/conf"'
alias cdt='cd "$CATALINA_HOME"'

function open {
    if [[ "$1" =~ ^.*\.(md)|(html)|(htm)$ ]]; then
        chrome "$1"
    else
        explorer $1
    fi
}

function gitp {
    remote=`git rev-parse --abbrev-ref --symbolic-full-name @{u} | cut -d \/ -f 1`
    url=`git remote get-url $remote`
    git push $url HEAD:refs/for/$1
}

function gitn {
    remote=`git rev-parse --abbrev-ref --symbolic-full-name @{u} | cut -d \/ -f 1`
    url=`git remote get-url $remote`
    git push $url HEAD:refs/changes/$1
}

function newbranch {
    cb=`git branch | awk '/[*]/ { print $2 }'`
    tracking=`git rev-parse --abbrev-ref --symbolic-full-name @{u}`
    git checkout -b tt --track $tracking
    if [ "$1" == "-D" ]; then
        git branch -D $cb
        git branch -m $cb
    fi
}
```

**Note:** The value of the ```$CATALINA_HOME``` environment variable required by the bash is likely *different* than the value stored in the system ```CATALINA_HOME```, for example:

Windows: ```CATALINA_HOME=C:\Program Files\Apache\Tomcat```

Bash: ```$CATALINA_HOME="/C/Program Files/Apache/Tomcat"```

Using the system version of ```CATALINA_HOME``` won't work in the bash as is, because of a problem with spaces.  So if encountering any problems along these lines, consider defining ```CATALINA_HOME``` in a script to run each time the bash is opened.

## Configure PostgreSQL password file
You can createa file `C:\Users\XXXX\AppData\Roaming\postgresql\pgpass.conf` to avoid type password when you run psql command.
```
vi ~/AppData/Roaming/postgresql/pgpass.conf
```

Add following line:
```
localhost:5432:rms:rms:123next!
localhost:5432:router:router:123next!
```
## Configure tomcat for remote debug
You need edit `C:\Users\XXXX\Programs\apache-tomcat-8.0.32\bin\startup.sh` file
```
$ vi ~/Programs\apache-tomcat-8.0.32\bin\startup.sh
```
Change last line to:
```
exec "$PRGDIR"/"$EXECUTABLE" jpda start "$@"
```

Maven should be set to include debugging information by default.  If, for some reason, your IDE cannot hook into Tomcat running in ```jdpa``` mode, you can manually add the following ```<plugin>``` tag to your Maven POM

```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.5.1</version>
    <configuration>
        <source>1.8</source>
        <target>1.8</target>
        <debug>true</debug>
        <debuglevel>lines,vars,source</debuglevel> 
    </configuration>
</plugin>
```

## Configure git
See wiki page: [Configure git](Setup Git Development Environment)

## Configure log
See wiki page: [Configure log](Setup Log4j)