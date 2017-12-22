---
layout: post
title: "jar with dependencies and include system scope dependency in maven-assembly"
date: 2017-12-22 22:41:00 +0800
categories: maven
---

`
	<build>
		<plugins>
			<plugin>
				<!--Include system scope dependency in maven-assembly-->
                <groupId>com.googlecode.addjars-maven-plugin</groupId>
                <artifactId>addjars-maven-plugin</artifactId>
                <version>1.0.2</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>add-jars</goal>
                        </goals>
                        <configuration>
                            <resources>
                                <resource>
                                    <directory>${basedir}/lib</directory>
                                </resource>
                            </resources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
			<plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-assembly-plugin</artifactId>
                <version>3.1.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>com.github.Application</mainClass>
                        </manifest>
                    </archive>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                </configuration>
                <executions>
                    <execution>
                        <id>make-assembly</id>
                        <phase>package</phase>
                        <goals>
                            <goal>single</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
`


