需要下载两个文件：

1. eclipse的Linux版本：[https://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/neon3](https://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/neon3)
2. eclips的插件源码：[https://github.com/winghc/hadoop2x-eclipse-plugin](https://github.com/winghc/hadoop2x-eclipse-plugin)

### 修改配置文件

1. src/contrib/eclipse-plugin/build.xml文件

   ```
    <target name="compile" depends="init, ivy-retrieve-common" unless="skip.contrib">
    修改为
    <target name="compile" depends="init" unless="skip.contrib">

    注释掉下面这行：
    <copy file="${hadoop.home}/share/hadoop/common/lib/htrace-core-${htrace.version}.jar"  todir="${build.dir}/lib" verbose="true"/>
   ```

2. ivy/libraries.properties文件

   ```
   hadoop.version=2.6.0 修改为 hadoop.version=2.2.0
   commons-lang.version=2.6 修改为 commons-lang.version=2.5
   jackson.version=1.9.13 修改为 jackson.version=1.8.8
   htrace.version=3.0.4 注释掉
   ```

3. 运行

   ```
   cd /home/h1/hadoop2x-eclipse-plugin-master/src/contrib/eclipse-plugin/
   /home/h1/apache-ant-1.9.2/bin/ant jar -Dversion=2.2.0 -Declipse.home=/home/h1/eclipse -Dhadoop.home=/home/h1/hadoop-2.2.0
   ```

4. 找到jar包

   ```
   cd /home/h1/hadoop2x-eclipse-plugin-master/build/contrib/eclipse-plugin
   hadoop-eclipse-plugin-2.2.0.jar包
   ```

**拷贝Jar包到Windows的Eclipse目录下的plugins目录**

### Eclipse选项配置

1. Window -&gt; Preferences -&gt;Hadoop Map/Reduce 选择Hadoop的目录
2. Window -&gt; Show View -&gt; MapReduce Tools 选择Map/Reduce Locations
3. Map/Reduce Locations -&gt; New Hadoop Locations

   * Map/Reduce Master 端口号50020不需修改

   * DFS Master 对应 core-site.xml

### 配置好Maven环境

* 下载插件
* 配置好环境  
  **.classpath**

  ```
  <?xml version="1.0" encoding="UTF-8"?>
  <classpath>
      <classpathentry kind="src" output="target/classes" path="src/main/java">
          <attributes>
              <attribute name="optional" value="true"/>
              <attribute name="maven.pomderived" value="true"/>
          </attributes>
      </classpathentry>
      <classpathentry excluding="**" kind="src" output="target/classes" path="src/main/resources">
          <attributes>
              <attribute name="maven.pomderived" value="true"/>
          </attributes>
      </classpathentry>
      <classpathentry kind="src" output="target/test-classes" path="src/test/java">
          <attributes>
              <attribute name="optional" value="true"/>
              <attribute name="maven.pomderived" value="true"/>
          </attributes>
      </classpathentry>
      <classpathentry excluding="**" kind="src" output="target/test-classes" path="src/test/resources">
          <attributes>
              <attribute name="maven.pomderived" value="true"/>
          </attributes>
      </classpathentry>
      <classpathentry kind="con" path="org.eclipse.jdt.launching.JRE_CONTAINER/org.eclipse.jdt.internal.debug.ui.launcher.StandardVMType/J2SE-1.5">
          <attributes>
              <attribute name="maven.pomderived" value="true"/>
          </attributes>
      </classpathentry>

      <classpathentry kind="con" path="org.eclipse.m2e.MAVEN2_CLASSPATH_CONTAINER">
          <attributes>
              <attribute name="maven.pomderived" value="true"/>
          </attributes>
      </classpathentry>
      <classpathentry kind="output" path="target/classes"/>
  </classpath>
  ```



