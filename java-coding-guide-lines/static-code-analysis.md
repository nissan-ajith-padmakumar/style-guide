# Static Code Analysis Tools

### PMD
PMD scans Java source code and looks for potential problems
Problems range from breaking naming conventions and unused code or varibales to performance and complexity of code, not forgetting lots of possible bugs
The PMD project also supports JavaScript, PLSQL, Apache Velocity, XML and XSL. It also ships with a CPD, a tool to detect duplicated code in several languages.

![PMD](//style-guide/assets/images/pmd.png)

PMD integrates with several tools and editors, including Eclipse, NetBeans, IntelliJ IDEA, TextPad, Maven, Ant and Emacs.
You can suppress warnings (in a variety of ways) and you can also write your own rules in either Java or XPath.

https://pmd.sourceforge.io/pmd-5.4.1/pmd-java/rules/index.html

### Checkstyle
As the name implies, Checkstyle checks that your code adheres to a coding standard.
The tool is configurable, which makes it able to support different code style conventions. Two examples are the Sun Code Conventions and Google Java Style (although the one from Sun hasn’t been maintained since 1999). 
![Checkstyle](/style-guide/assets/images/checkstyle.png)

You can find a configuration file for Google’s Java Style on the checkstyle repository.
https://github.com/checkstyle/checkstyle/blob/3e4367941c3e9680703e8ea8400abbd5dc78e1d9/src/main/resources/google_checks.xml


### FindBugs

FindBugs looks for bugs in Java Code, and this means over 400 different bugs.
http://findbugs.sourceforge.net/bugDescriptions.html
Patterns are separated into several categories: bad practice, correctness, malicious code vulnerability, multithreaded correctness, performance, security and dodgy code (two additional categories exist, with just a couple of patterns each: experimental and internationalization).

![FindBugs](/style-guide/assets/images/findbugs.png)

### SonarCube

SonarQube® is an automatic code review tool to detect bugs, vulnerabilities and code smells in your code.
 It can integrate with your existing workflow to enable continuous code inspection across your project branches and pull requests.

SonarQube can perform analysis on up to 27 different languages depending on your edition. The outcome of this analysis will be quality measures and issues.

SonarQube collects and analyzes source code, measuring quality and providing reports for your projects.


Note: SonarQube is in no way competing with any of the above static analysis tools, but rather it complements and works very well with these tools. 

![Sonarcube](/style-guide/assets/images/sonarcube1.png)
![Sonarcube](/style-guide/assets/images/sonarcube2.png)
