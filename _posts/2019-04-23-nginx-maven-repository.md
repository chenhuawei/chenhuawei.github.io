---
layout: post
title:  "Nginx搭建Maven Repository"
date:   2019-04-23 14:00:52 +0800
categories: maven
tags:
- maven
- nginx
---


# nginx创建一个maven仓库站点

{% highlight text %}
server {
    listen 80;
    server_name nginx-maven-repository.域名;
    client_max_body_size 200m;
    
    # 如果仓库不想开放给所有人可以简单配置个认证
    auth_basic           "Administrator's Area";
    auth_basic_user_file /etc/nginx/.htpasswd;
    
    # maven站点路径
    location / {
	    root maven站点路径;
    }
}
{% endhighlight %}

`.htpasswd`可以参考[Nginx的配置文档](https://docs.nginx.com/nginx/admin-guide/security-controls/configuring-http-basic-authentication)


# 在要发布jar包的机器上找到settings.xml
{% highlight shell %}
mvn -v
{% endhighlight %}

会输出类似以下内容：
```
Apache Maven 3.0.5 
Maven home: /usr/share/maven
```

编辑`/usr/share/maven/conf/settings.xml`，在`<servers>`标签里加入`nginx-maven-repository`的配置
{% highlight xml %}
    <server>
        <id>nginx-maven-repository</id>
        <username>ssh用户名</username>
        <password>ssh密码</password>
    </server>
    <!--如果前面配置了需要认证再加一个引用仓库的配置-->
    <server>
        <id>ref-nginx-maven-repository</id>
        <username>http认证用户名</username>
        <password>http认证密码</password>
    </server>

{% endhighlight %}

也可以编辑`~/.m2/settings.xml`

# 修改要发布到maven仓库的pom.xml

{% highlight xml %}

    <!--deploy的时候会先从仓库获取，所以需要加上ref-nginx-maven-repository-->
    <repositories>
        <repository>
            <id>ref-nginx-maven-repository</id>
            <name>ref-nginx-maven-repository</name>
            <url>http://nginx-maven-repository.域名</url>
        </repository>
    </repositories>
    
    <!--配置要发布到的仓库，id要和前面settings.xml中配置的一致-->
    <distributionManagement>
        <repository>
            <id>nginx-maven-repository</id>
            <url>scp://nginx服务器地址/maven站点路径</url>
        </repository>
    </distributionManagement>
   
    <!--增加scp插件-->
    <build>
            <extensions>
                <extension>
                    <groupId>org.apache.maven.wagon</groupId>
                    <artifactId>wagon-ssh</artifactId>
                    <version>2.10</version>
                </extension>
            </extensions>
    </build>
    
{% endhighlight %}

# 发布到仓库

{% highlight shell %}
mvn deploy
{% endhighlight %}

# 其它需要引用仓库的pom.xml

{% highlight xml %}
    <repositories>
        <repository>
            <id>ref-nginx-maven-repository</id>
            <name>ref-nginx-maven-repository</name>
            <url>http://nginx-maven-repository.域名</url>
        </repository>
    </repositories>
    <dependencies>
        <dependency>
            <groupId>发布到仓库的groupId</groupId>
            <artifactId>发布到仓库的artifactId</artifactId>
            <version>发布到仓库的版本号</version>
        </dependency>
    </dependencies>
{% endhighlight %}

大功告成！