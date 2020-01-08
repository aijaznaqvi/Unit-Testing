# Setting Up Unit Test Environment
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

* open /Applications/XAMPP/xamppfiles/etc/php.ini
* scroll to the very end
* make sure the xDebug lines are not commented out, add them if they arent there

```
[Zend] zend_extension = /full/path/to/xdebug.so xdebug.remote_enable = 1
xdebug.remote_port = 9000 xdebug.remote_host = localhost
```

* restart apache

## References
1. [How to Install Node.js and NPM on a Mac](https://blog.teamtreehouse.com/install-node-js-npm-mac)
1. [NPM module to run SonarQube/SonarCloud analyses](https://www.npmjs.com/package/sonarqube-scanner)
1. [setting up xDebug](https://jonathansblog.co.uk/setting-up-xmapp-on-a-mac-with-xdebug)

## Bibliography
1. [node-sonar-scanner](https://www.npmjs.com/package/sonar-scanner)
1. [ERROR No code coverage driver available](https://github.com/Codeception/Codeception/issues/1207)
