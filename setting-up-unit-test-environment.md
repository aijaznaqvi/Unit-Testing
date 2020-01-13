# Setting Up Unit Test Environment on a Mac
## How to Install Node.js and NPM on a Mac
### Installation
Installing Node.js and NPM is pretty straightforward using Homebrew. Homebrew handles downloading, unpacking and installing Node and NPM on your system. The whole process (after you have XCode and Homebrew installed) should only take you a few minutes.

1. **Open the Terminal app** and type `brew install node`.
2. Homebrew downloads some files and installs them. And that’s it.
To make sure you have Node and NPM installed, run two simple commands to see what version of each is installed:

* To see if Node is installed, type `node -v` in Terminal. This should print the version number so you’ll see something like this `v0.10.31`.
* To see if NPM is installed, type `npm -v` in Terminal. This should print the version number so you’ll see something like this `1.4.27`

## NPM module to run SonarQube/SonarCloud analyses
### Installation
This package is available on npm as: sonarqube-scanner

To add code analysis to your build files, simply add the package to your project dev dependencies:

```
npm install -D sonarqube-scanner
```
To install the scanner globally and be able to run analyses on the command line:
```
npm install -g sonarqube-scanner
```

### Usage: run analyses on the command line
If you want to run an analysis without having to configure anything in the first place, simply run the `sonar-scanner` command. The following example assumes that you have installed SonarQube locally:
```
cd my-project
sonar-scanner
```

## setting up xDebug
This will allow you to have proper debugging for your php apps. In the long run this will save you hours of time.

First check if you have xdebug module in your machine:
```
php -i | grep xdebug
```
then check if it's enabled:
```
php -m | grep xdebug
```
If you don't have the xdebug module, you'll need to install it.

* copy `/etc/php.ini.default` to `/etc/php.ini` if `/etc/php.ini` doesn't exist.
* open `/Applications/XAMPP/xamppfiles/etc/php.ini` or `/etc/php.ini`
* scroll to the very end
* make sure the xDebug lines are not commented out, add them if they arent there

```
[Zend]
zend_extension = /usr/lib/php/extensions/no-debug-non-zts-20180731/xdebug.so
xdebug.remote_enable = 1
xdebug.remote_port = 9000
xdebug.remote_host = localhost
```

* restart apache
## setting up test database
### Change your MySQL server max_allowed_packet
1) Edit your server my.cnf file using below command
`sudo vi /Applications/XAMPP/xamppfiles/etc/my.cnf`
2) Add the line under the [MYSQLD] section. :
```
max_allowed_packet=1024M
wait_timeout = 100
```

3) Then restart your mysql server
4) You can verify by going into PHPMyAdmin or opening a SQL command window and executing:
```
SHOW VARIABLES LIKE 'max_allowed_packet'
```

### Create test database
1) Create empty test database schema by opening a SQL command window and executing:
```
create schema ci_pos_test;
```
2) open your terminal window
3) please write following command in your terminal
```
./yii test-database --create --verbose
```
This command populates the test database with structure and test data.

## Setting up SonarLint
SonarLint is an IDE extension that helps you detect and fix quality issues as you write code. Like a spell checker, SonarLint squiggles flaws so they can be fixed before committing code. You can get it directly from the PhpStorm Plugin Repository, and it will then detect new bugs and quality issues as you code.

If your project is analyzed on SonarQube or on SonarCloud, SonarLint can connect to the server to retrieve the appropriate quality profiles and settings for that project.

Prerequisite: You must already have PhpStorm installed and have a project open.

1. Launch PhpStorm and open a project.
1. In PhpStorm, open Settings (called Preferences on Mac), then go to Plug-ins and search "SonarLint" and click "Install".
1. You may need to re-launch PhpStorm for the new plug-in to become active.

## References
1. [How to Install Node.js and NPM on a Mac](https://blog.teamtreehouse.com/install-node-js-npm-mac)
1. [NPM module to run SonarQube/SonarCloud analyses](https://www.npmjs.com/package/sonarqube-scanner)
1. [ERROR No code coverage driver available](https://github.com/Codeception/Codeception/issues/1207)
1. [setting up xDebug](https://jonathansblog.co.uk/setting-up-xmapp-on-a-mac-with-xdebug)
1. [Error while sending QUERY packet](https://stackoverflow.com/questions/30753674/error-while-sending-query-packet)
1. [SonarLint](https://plugins.jetbrains.com/plugin/7973-sonarlint)

## Bibliography
1. [node-sonar-scanner](https://www.npmjs.com/package/sonar-scanner)
1. [SonarQube and SonarLint User Guide](https://openlmis.atlassian.net/wiki/spaces/OP/pages/96469059/SonarQube+and+SonarLint+User+Guide)
