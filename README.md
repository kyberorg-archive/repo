# Virtalab Maven Repository

## Instructions

### How to get dependencies from repository
 
 Just add following section to your pom.xml:
 
        <repositories>
            <repository>
                <id>virtalab-maven-repo</id>
                <url>https://raw.github.com/virtalab/repo/mvn-repo/</url>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
         
        
### How to release to repository
* Add following property to your pom.xml:
* 
        <properties>
            <github.global.server>github</github.global.server>
        </properties>

* Add distributionManagement section to your pom.xml:
    
        <distributionManagement>
            <repository>
                <id>internal</id>
                <name>Temporary Staging Repository</name>
                <url>file://${project.build.directory}/mvn-repo</url>
            </repository>
        </distributionManagement>
        
* Add maven deploy plugin configuration (should be located under build->plugins):

        <plugin>
            <artifactId>maven-deploy-plugin</artifactId>
                <version>2.8.1</version>
                <configuration>
                    <altDeploymentRepository>internal::default::file://${project.build.directory}/mvn-repo</altDeploymentRepository>
                </configuration>
        </plugin>
                
* And finally add github-site-plugin configuration (should be located under build->plugins):

        <plugin>
            <groupId>com.github.github</groupId>
            <artifactId>site-maven-plugin</artifactId>
            <version>0.9</version>
            <configuration>
                <!-- It's recommended to leave message as is -->
                <message>Group: ${project.groupId} Project: ${project.artifactId} Ver: ${project.version}</message>
                <noJekyll>true</noJekyll>
                <!-- Add instead of overwrite (MUST be true) -->
                <merge>true</merge>
                
                <outputDirectory>${project.build.directory}/mvn-repo</outputDirectory>
                <branch>refs/heads/mvn-repo</branch>
                <includes><include>**/*</include></includes>
                <!-- This is our maven repo -->
                <repositoryName>repo</repositoryName>
                <repositoryOwner>virtalab</repositoryOwner>
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <goal>site</goal>
                    </goals>
                    <phase>deploy</phase>
                </execution>
            </executions>
        </plugin>
            
* Add credentials to your settings.xml (Maven settings):
 
        <server>
            <id>github</id><!-- Should be same as property github.global.server in pom.xml -->
            <username>login</username>
            <password>pass</password>
        </server>

* Performing release

        mvn -P release clean release:prepare release:perform
