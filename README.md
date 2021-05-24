# Code Coverage Analysis using JaCoCo & Sonar
A basic maven multi-module project codebase to help understand the following blog post - <Insert blog post>
## Basic Example
Run the below command -
```sh
mvn clean verify
```
This will genereate the JaCoCo Report for coverage in module-a and module-b as both of them have their own test cases.

Once the reports have generated you can run -
```sh
mvn sonar:sonar -Dsonar.login=<sonar-token>
```
This will publish the analysis to sonar running locally.

## Exception Example
In this case module-b has a dependency on module-a and test cases covering code in both modules are written under the test folder of module-b. Due to this if we use the configuration in the basic example, JaCoCo report for module-a will not be generated and JaCoCo report in module-b will only have coverage for classes under module-b.
To solve the problem we use the maven-dependency-plugin in the pom.xml file of module-b to unpack the classes of module-a when running verify for module-b.
```sh
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <version>2.8</version>
    <executions>
        <execution>
            <id>unpack-classes</id>
            <phase>generate-resources</phase>
            <configuration>
                <includeArtifactIds>
                    module-a
                </includeArtifactIds>
                <outputDirectory>${project.build.directory}/classes
                </outputDirectory>
            </configuration>
            <goals>
                <goal>unpack-dependencies</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```
We also need to tell sonar that while analysing module-a the coverage report should be picked up from the target folder of module-b. For this we add the following property to pom.xml of module-a.
```sh
<sonar.coverage.jacoco.xmlReportPaths>
    ${project.basedir}/../module-b/target/site/jacoco/jacoco.xml
</sonar.coverage.jacoco.xmlReportPaths>
```
Running ```mvn clean verify``` now will generate the JaCoCo report for classes of both modules under the target/site/jacoco directory of module-b.
Also, ```mvn sonar:sonar -Dsonar.login=<sonar-token>``` should now publish the code to Sonar with coverage for both modules.

## Analysing Bitbucket PRs
You can find more information about this in our blog post - <Insert link with sublink to this subsection>
You can also look at the Jenkinsfile provided in the codebase - <Insert link to Jenkinsfile in code>