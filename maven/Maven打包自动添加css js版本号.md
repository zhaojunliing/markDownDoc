# Maven打包插件：自动添加css js版本号
> jcv-maven-plugin、maven-replacer-plugin这两款maven打包插件均可以实现在打出的包自动添加js/css版本号。jcv-maven-plugin是专门为压缩js/css文件和添加js/css版本号设计的一款插件，使用的人很少，使用说明文档较为模糊。maven-replacer-plugin则可以在打包时匹配指定文件中的内容并替换，这里介绍用来自动添加js/css版本号。
>

## jcv-maven-plugin

### 介绍

项目主页：https://github.com/byzy/jcv-maven-plugin jdk版本等注意事项、参数均在主页中有说明。

### 配置

```xml
<!--将下列配置放在pom.xml的中-->
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.1.0</version>
            <configuration>
                <warSourceExcludes>**/*.html,**/*.js,**/*.css</warSourceExcludes>
            </configuration>
        </plugin>
        <plugin>
            <groupId>com.iqarr.maven.plugin</groupId>
            <artifactId>jcv-maven-plugin</artifactId>
            <version>1.0.0</version>
            <executions>
                <execution>
                    <phase>prepare-package</phase>
                    <goals>
                        <goal>process</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <jsConstantName>test</jsConstantName>
                <cssConstantName>test</cssConstantName>
                <!--需要处理的文件后缀-->
                <suffixs>
                    <param>html</param>
                </suffixs>
                <!--清理页面注释-->
                <clearPageComment>true</clearPageComment>
                <!--压缩-->
                <compressionCss>true</compressionCss>
                <compressionJs>true</compressionJs>
                <globaJsMethod>MD5_METHOD</globaJsMethod>
                <globaCssMethod>MD5_METHOD</globaCssMethod>
                <versionLable>v</versionLable>
            </configuration>
        </plugin>
    </plugins>
</build>
```

参数说明很模糊，在使用过程中看了参数说明文档仍不清楚有些关键参数的含义，最终打包也没达到预期的效果。不得不去查看插件源代码才搞清楚jsConstantName, cssConstantName这两个配置使用。

### 注意事项

1. prepare-package表示在真正打包之前，为准备打包执行任何必要的操作。这牵涉到maven build声明周期相关的配置，这里不做阐述，可[查阅此处](http://wiki.jikexueyuan.com/project/maven/build-life-cycle.html)。
```xml
<executions>
    <execution>
        <phase>prepare-package</phase>
        <goals>
            <goal>process</goal>
        </goals>
    </execution>
</executions>
```

2. 如果在页面引入js/css时把自定义的根目录写到了路径，例如：js文件路径为js/main.js(这路径是相对于工程目录webapp的)，根目录为/test，在页面中的路径被写为`<script src="/test/js/main.js"></script>`，必须设置jsConstantName, cssConstantName的值为**test**，**WARN：不要带斜杠/**。

3. `<warSourceExcludes>**/*.html,**/*.js,**/*.css</warSourceExcludes>`需要在maven-war-plugin中排除掉jcv-maven-plugin要处理的文件类型。

4. globaJsMethod, globaCssMethod推荐使用MD5_METHOD。



### 效果





## maven-replacer-plugin

### 介绍

项目主页：https://code.google.com/archive/p/maven-replacer-plugin/

### 配置

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.1.0</version>
            <configuration>
                <useCache>true</useCache>
            </configuration>
            <executions>
                <execution>
                    <id>prepare-war</id>
                    <phase>prepare-package</phase>
                    <goals>
                        <goal>exploded</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        <!--此插件用于获取当前时区当前时间-->
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>build-helper-maven-plugin</artifactId>
            <version>3.0.0</version>
            <executions>
                <execution>
                    <id>timestamp-property</id>
                    <goals>
                        <goal>timestamp-property</goal>
                    </goals>
                    <configuration>
                        <name>build.time</name>
                        <pattern>MdHHmmssSSS</pattern>
                        <timeZone>GMT+8</timeZone>
                    </configuration>
                </execution>
            </executions>
        </plugin>
        <plugin>
            <groupId>com.google.code.maven-replacer-plugin</groupId>
            <artifactId>replacer</artifactId>
            <version>1.5.3</version>
            <executions>
                <execution>
                    <phase>prepare-package</phase>
                    <goals>
                        <goal>replace</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <basedir>${build.directory}</basedir>
                <includes>
                    <include>${build.finalName}/views/*.html</include>
                </includes>
                <replacements>
                    <replacement>
                        <token>\.css\"</token>
                        <value>.css?v=${build.time}\"</value>
                    </replacement>
                    <replacement>
                        <token>\.css\'</token>
                        <value>.css?v=${build.time}\'</value>
                    </replacement>
                    <replacement>
                        <token>\.js\"</token>
                        <value>.js?v=${build.time}\"</value>
                    </replacement>
                    <replacement>
                        <token>\.js\'</token>
                        <value>.js?v=${build.time}\'</value>
                    </replacement>
                </replacements>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### 注意事项

1. `<useCache>true</useCache>`，参见[Stack Overflow](https://stackoverflow.com/questions/39105557/maven-replacer-plugin-to-replace-tokens-in-build-and-not-source)
2. 多文件引入使用`<basedir>${build.directory}</basedir>`，参见[Stack Overflow](https://stackoverflow.com/questions/42248547/maven-replacer-plugin-with-multiple-files)
3. 通过变量${maven.build.timestamp}获取的时间不是当前时区，故引入插件build-helper-maven-plugin