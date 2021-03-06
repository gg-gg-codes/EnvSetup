mac -> export M2_HOME=/Users/userame/software/apache-maven-3.3.9
      export PATH=@M2_HOME/bin:$PATH
      
windows-> path env variable-><Mavenhome>/bin folder  

__________________________________________________________
Notes:

1. to skip the test -> mvn install -DskipTests

2. maven life cycle phases
![](mavenphasesndgoals.png)

3. maven scopes

Maven dependency scope – compile

This is maven default scope. Dependencies with compile scope are needed to build, test, and run the project.

Scope compile is to be required in most of the cases to resolve the import statements into your java classes sourcecode.
```
<dependencies>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.14</version>
        <!-- You can ommit this because it is default -->
        <scope>compile</scope>
    </dependency>
</dependencies>
```
Maven dependency scope – provided

Maven dependency scope provided is used during build and test the project. They are also required to run, but should not exported, because the dependency will be provided by the runtime, for instance, by servlet container or application server.
```
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>3.0.1</version>
    <scope>provided</scope>
</dependency>
```
Maven dependency scope – runtime

Dependencies with maven dependency scope runtime are not needed to build, but are part of the classpath to test and run the project.
```
<dependency>
    <groupId>com.thoughtworks.xstream</groupId>
    <artifactId>xstream</artifactId>
    <version>1.4.4</version>
    <scope>runtime</scope>
 </dependency>
 ```
Maven dependency scope – test

Dependencies with maven dependency scope test are not needed to build and run the project. They are needed to compile and run the unit tests.
```
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```
Maven dependency scope – system

Dependencies with system are similar to ones with scope provided. The only difference is system dependencies are not retrieved from remote repository. They are present under project’s subdirectory and are referred from there. See external dependency for more detail.
```
<dependency>
  <groupId>extDependency</groupId>
  <artifactId>extDependency</artifactId>
  <scope>system</scope>
  <version>1.0</version>
  <systemPath>${basedir}\war\WEB-INF\lib\extDependency.jar</systemPath>
</dependency>
```
Maven dependency scope – import

import scope is only supported on a dependency of type pom in the dependencyManagement section. It indicates the dependency to be replaced with the effective list of dependencies in the specified POM’s dependencyManagement section.
```
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>other.pom.group.id</groupId>
            <artifactId>other-pom-artifact-id</artifactId>
            <version>SNAPSHOT</version>
            <scope>import</scope>
            <type>pom</type>
        </dependency>   
    </dependencies>
</dependencyManagement>
```
Maven dependency transitivity resolution

When you include a maven dependency and it has it’s own other dependencies (i.e. transitive dependencies) then you may want to be clear about the scope of these transitive dependencies as well.

Let’s understand about maven transitive dependencies with a simple table. In this table, if a dependency is set to the scope in the left column, transitive dependencies at top row will result in a dependency with the scope listed at their intersection.
```

Dependency	compile	provided	runtime	test
compile	compile	–	runtime	–
provided	provided	–	provided	–
runtime	runtime	–	runtime	–
test	test	–	test	–

```

MAVEN COMMANDS:

1. create exucutable jar -> mvn clean package

2. creating jar with dependencies
```
        <!-- Method 1 -->
        <!-- Copy dependency libraries jar files to a separated LIB folder -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-dependency-plugin</artifactId>
            <configuration>
                <outputDirectory>${project.build.directory}/lib</outputDirectory>
                <excludeTransitive>false</excludeTransitive> 
                <stripVersion>false</stripVersion>
            </configuration>
            <executions>
                <execution>
                    <id>copy-dependencies</id>
                    <phase>package</phase>
                    <goals>
                        <goal>copy-dependencies</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        <!-- Add LIB folder to classPath -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jar-plugin</artifactId>
            <version>2.4</version>
            <configuration>
                <archive>
                    <manifest>
                        <addClasspath>true</addClasspath>
                        <classpathPrefix>lib/</classpathPrefix>
                    </manifest>
                </archive>
            </configuration>
        </plugin>


        <!-- Method 2 -->
        <!-- Package all libraries classes into one runnable jar -->
        <plugin>
            <artifactId>maven-assembly-plugin</artifactId>
            <executions>
              <execution>
                <phase>package</phase>
                <goals>
                  <goal>single</goal>
                </goals>
              </execution>
            </executions>
            <configuration>
              <descriptorRefs>
                <descriptorRef>jar-with-dependencies</descriptorRef>
              </descriptorRefs>
            </configuration>
        </plugin>  
        
  ```
