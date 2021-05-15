# windows10安装配置maven

### 1.配置Maven的环境变量
a. 新建系统变量，变量名为“MAVEN_HOME”, 变量值为maven的解压目录。
![](pic\1.png)

b. 在系统变量PATH中引入你配置的变量名:  MAVAEN_HOME
输入：%MAVEN_HOME%\bin
![](pic\2.png)

c. 保存后打开cmd，输入mvn -v， 显示如下图则说明配置成功。
![](pic\3.png)

### 2.配置setting.xml

路径在Maven解压路径下conf
![](pic\4.png)
a. 修改本地仓库地址
添加下列代码到settings文件中，路径需要修改为你自己的
`  <localRepository>E:\program dev\apache\maven\maven_repository</localRepository>`

![](pic\5.png)
b. 添加国内镜像源，加快jar包下载速度，我们添加阿里的。
添加下列代码到mirrors标签块中间

```
<!-- alibaba --> 
<mirror>
	<id>alimaven</id>
	<mirrorOf>central</mirrorOf>
	<name>aliyun maven</name>
	<url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
</mirror>
```

![](pic\6.png)

### 3.在IDEA配置Maven
在File->Settings->Build,Execution-Deployment->Build Tools->Maven，配置到自己Maven版本及setting
![](pic\7.png)