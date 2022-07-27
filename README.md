# maven shade plugin
##  命令说明
shade:help 显示帮助信息

mvn shade:help -Ddetail=true -Dgoal= 显示参数详情

shade:shade 执行着色委托给 Shader 组件的 Mojo。

## 举例说明
###Selecting Contents for Uber JAR

将该工程依赖的部分 Jar 包 include/exclude 掉。
如果想将整个第三方jar包都过滤掉，可以使用exclude，也是指定groupId:artifactId的标识。


```shell
<build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.4.3</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <artifactSet>
                <excludes>
                  <exclude>classworlds:classworlds</exclude>
                  <exclude>junit:junit</exclude>
                  <exclude>jmock:*</exclude>
                  <exclude>*:xml-apis</exclude>
                  <exclude>org.apache.maven:lib:tests</exclude>
                  <exclude>log4j:log4j:jar:</exclude>
                </excludes>
              </artifactSet>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
</build>
```
### filters
将依赖的某个 Jar 包内部的类或者资源 include/exclude 掉。
Filter操作在打包时将jar包中的内容排除。
它是以groupId:artifactId为标识，在filter内部可以使用/更细致地控制，既可以移除代码文件，也可以移除配置文件。
```shell
<build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.4.3</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <filters>
                <filter>
                  <artifact>junit:junit</artifact>
                  <includes>
                    <include>junit/framework/**</include>
                    <include>org/junit/**</include>
                  </includes>
                  <excludes>
                    <exclude>org/junit/experimental/**</exclude>
                    <exclude>org/junit/runners/**</exclude>
                  </excludes>
                </filter>
                <filter>
                  <artifact>*:*</artifact>
                  <excludes>
                    <exclude>META-INF/*.SF</exclude>
                    <exclude>META-INF/*.DSA</exclude>
                    <exclude>META-INF/*.RSA</exclude>
                  </excludes>
                </filter>
              </filters>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
</build>
```
### minimizeJar
maven-shade-plugin 自动将所有不使用的类全部排除掉，将 uber-jar 最小化。

```shell
 <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.4.3</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <minimizeJar>true</minimizeJar>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```
默认会生成一个Jar包和一个以 "-shaded"为结尾的uber-jar包，可以通过配置来指定uber-jar的后缀名。

```shell
<build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.4.3</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <shadedArtifactAttached>true</shadedArtifactAttached>
              <shadedClassifierName>jackofall</shadedClassifierName> <!-- Any name that makes sense -->
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```
### Executable JAR

通过设置 MainClass 创建一个可执行 Jar 包。

```shell
<build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.4.3</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                  <mainClass>org.sonatype.haven.HavenCli</mainClass>
                </transformer>
              </transformers>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```
更多的Transformer见http://maven.apache.org/plugins/maven-shade-plugin/examples/resource-transformers.html



### Relocating Classes

Java 工程经常会遇到第三方 Jar 包冲突，使用 maven shade plugin 解决 jar 或类的多版本冲突。 maven-shade-plugin 在打包时，可以将项目中依赖的 jar 包中的一些类文件打包到项目构建生成的 jar 包中，在打包的时候把类重命名。下面的配置将 org.codehaus.plexus.util jar 包重命名为 org.shaded.plexus.util。
```shell
 <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.4.3</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>org.codehaus.plexus.util</pattern>
                  <shadedPattern>org.shaded.plexus.util</shadedPattern>
                  <excludes>
                    <exclude>org.codehaus.plexus.util.xml.Xpp3Dom</exclude>
                    <exclude>org.codehaus.plexus.util.xml.pull.*</exclude>
                  </excludes>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

## 场景说明
