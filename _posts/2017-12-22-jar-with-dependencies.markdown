---
layout: post
title: "MAVEN将本地jar包添加为依赖库并进行打包"
date: 2017-12-22 22:41:00 +0800
categories: java
tags:
- java
- maven
---
+ 添加本地jar包为依赖包

{% highlight xml %}
	<dependency>
        <groupId>org.rxtx</groupId>
        <artifactId>rxtxcomm</artifactId>
        <version>2.2.0</version>
        <scope>system</scope>
        <systemPath>${basedir}/lib/RXTXcomm.jar</systemPath>
    </dependency>
{% endhighlight %}

+ 使用addjars-maven-plugin插件将本地jar包进行package

{% highlight xml %}
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
{% endhighlight %}

​+ maven打成一个jar包时合并资源文件

使用maven-assembly-plugin插件进行package时会有一个问题，就是META-INF/services下的同名文件会被覆盖，因此需要使用另一个插件(maven-shade-plugin)进行package

{% highlight xml %}
	<build>
        <plugins>
            <plugin>
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
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.1.0</version>
                <configuration>
                    <createDependencyReducedPom>false</createDependencyReducedPom>
                    <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <manifestEntries>
                                <Main-Class>com.github.Application</Main-Class>
                            </manifestEntries>
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
                    </transformers>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
{% endhighlight %}

   