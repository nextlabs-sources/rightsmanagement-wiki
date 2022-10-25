# Setup Mac OSX Development Environment

## Overview
We heavily use command line tools in our development. This document provide a standardized development environment and process to ensure all developers in sync.

## Install Required tools

- **Homebrew**: The package manager for Mac OSX

    We recommend use Homebrew, you can choose Macports on your own risk.

		ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

- **git**

		$ brew install git

- **ant**

		$ brew install ant

- **maven**

		$ brew install maven

- **dart-sdk**

		$ brew tap dart-lang/dar
		$ brew install dart --with-content-shell --with-dartium

- **tomcat**

    The easiest way is download tomcat8 binary zip file from [http://tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi), and manually unzip it to `/Applications/tomcat` folder.

- **findbugs 3.0.1**

    We use findbugs as one of the static code analysis tools. Please download findbugs: [http://findbugs.sourceforge.net/downloads.html](http://findbugs.sourceforge.net/downloads.html)

- **PostgreSQL server**

    Download Postgres.app from [http://postgresapp.com](http://postgresapp.com).

- **JCE Unlimited Strength Jurisdiction Policy**

	Our solution key length requires JCE Unlimited Strength Jurisdiction Policy. Download it from [http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html](http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html). Unzip files and copy them into 
`/Library/Java/Home/lib/security/` folder.

## IDE
Developers can choose any IDE that they prefer. However we strongly recommended **IntelliJ**, you can download community version from:  [https://www.jetbrains.com/idea/download/#](https://www.jetbrains.com/idea/download/#). In current source tree, IntelliJ and Oracle JDeveloper project files are checked into git repo.

## Configure your bash
You can setup your bash environment by editing .profile or .bash_profile:
```
PS1='[\w]>'

export FINDBUGS_HOME="/Applications/findbugs-3.0.1"
export CATALINA_HOME="/Applications/tomcat"
export JPDA_ADDRESS=8000
export JPDA_TRANSPORT=dt_socket
export PGUSER=rms
export PGHOST=localhost

PATH=.:$PATH:$CATALINA_HOME/bin

export DEBUG PATH JAVA_HOME

alias cds="cd ~/source"
alias cdrms="cd ~/source/nextlabs/rms/server"
alias cdrouter="cd ~/source/nextlabs/router/server"
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

## Configure PostgreSQL password file
You can createa file `~/.pgpass` to avoid type password when you run psql command.
```
vi ~/.pgpass
```

Add following line:
```
localhost:5432:rms:rms:123next!
localhost:5432:router:router:123next!
```
You need change file permission to 600
```
chmod ~/.pgpass 600
```

## Configure tomcat for remote debug
You need edit `/Applications/tomcat/bin/startup.sh` file
```
$ vi $CATALINA_HOME/bin/startup.sh
```
Change last line to:
```
exec "$PRGDIR"/"$EXECUTABLE" jpda start "$@"
```

## Configure git
See wiki page: [Configure git](Setup Git Development Environment)