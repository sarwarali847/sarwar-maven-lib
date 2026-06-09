Maven Artifact Publishing Demo
Step 1: Created Producer Maven Project

We first created a Maven project:

groupId    = com.sarwar
artifactId = hello-maven
version    = 1.0-SNAPSHOT

Purpose:

This project acts as a library/package producer.
Maven generates a JAR file from this project.
Step 2: Created GitHub Repository

Created:

sarwar-maven-lib

on GitHub.

Purpose:

GitHub Packages acts as a remote Maven repository.
Similar to Maven Central, but hosted on GitHub.
Step 3: Updated settings.xml

File:

C:\Users\user\.m2\settings.xml

Added:

<servers>
    <server>
        <id>github</id>
        <username>sarwarali847</username>
        <password>GitHub_PAT</password>
    </server>
</servers>

Why?

GitHub Packages requires authentication.
When Maven uploads artifacts, it reads credentials from settings.xml.
We don't keep passwords/tokens inside pom.xml.
Step 4: Updated pom.xml

Added:

<distributionManagement>
    <repository>
        <id>github</id>
        <name>GitHub Packages</name>
        <url>
            https://maven.pkg.github.com/sarwarali847/sarwar-maven-lib
        </url>
    </repository>
</distributionManagement>

Why?

Maven needs to know where to deploy the artifact.
distributionManagement tells Maven the destination repository.

Without this:

mvn deploy

would not know where to upload the JAR.

Step 5: Executed
mvn clean package

What happened?

clean

Deletes:

target/

to start with a fresh build.

package
Compiles code
Runs tests
Creates JAR

Output:

target/hello-maven-1.0-SNAPSHOT.jar
Step 6: Executed
mvn deploy

What happened internally?

Maven first performs
compile
test
package
install
deploy
install

Copies artifact to local repository:

C:\Users\user\.m2\repository
deploy

Uploads artifact to:

GitHub Packages

using:

URL from distributionManagement
Credentials from settings.xml
Step 7: Package Created in GitHub

After successful deployment:

GitHub automatically created:

Packages
    |
    --> com.sarwar.hello-maven

This package contains:

JAR
POM
Metadata
Versions

Now the artifact is available remotely.

Consumer Project
Step 8: Created Consumer Maven Project

Created another project:

consumer-app

Purpose:

Simulates another developer's machine.
Consumes our published artifact.
Step 9: Updated Consumer pom.xml

Added repository:

<repositories>
    <repository>
        <id>github</id>
        <url>
            https://maven.pkg.github.com/sarwarali847/sarwar-maven-lib
        </url>
    </repository>
</repositories>

Why?

By default Maven searches Maven Central.
Our package is in GitHub Packages.
So we must tell Maven where to look.
Step 10: Added Dependency
<dependency>
    <groupId>com.sarwar</groupId>
    <artifactId>hello-maven</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>

Why?

This tells Maven:

Download the artifact named hello-maven belonging to com.sarwar.

Step 11: Executed
mvn clean compile

What happened?

Maven:

Reads dependency
Checks local .m2
If not found, checks GitHub Packages
Downloads JAR
Stores in local repository
C:\Users\user\.m2\repository\com\sarwar\hello-maven
End Result

Flow:

Producer Project
      |
      | mvn deploy
      V
GitHub Packages
      |
      | dependency
      V
Consumer Project
      |
      | mvn compile
      V
Downloads JAR
