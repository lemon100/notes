# linux部署jenkins

### 1. 安装jenkins

我们直接使用war模式运行jenkins，首先下载jenkins的war包：https://www.jenkins.io/download/

下载完成后，放到我们运行的目录，使用java命令跑起来即可：

![image-20220514170918543](images/image-20220514170918543.png)

```linux
java -jar jenkins.war
```

出现下面提示则说明启动成功：

![image-20220514171025405](images/image-20220514171025405.png)

### 2. 配置jenkins

2.1 在浏览器访问jenkins启动的url，进入出现下图则进入配置流程：

![image-20220514170022799](images/image-20220514170022799.png)

默认密码复制粘贴过来，默认密码位置如图：

![image-20220514171209204](images/image-20220514171209204.png)



2.2 插件使用默认推荐安装，直接下一步：

![](images/image-20220514170102793.png)

2.3 插件安装完成后进入用户创建页面，创建一个自己的管理员账号，保存好账号密码下一步：

![image-20220514170611711](images/image-20220514170611711.png)

2.4 用户创建之后，实例配置，我们这里默认8080端口：

![image-20220514170655444](images/image-20220514170655444.png)

2.5 配置完成后，进入首页，jenkins配置完成：

![image-20220514171400246](images/image-20220514171400246.png)

### 3. 编写jenkins启动/停止脚本

3.1 启动脚本

在jenkins启动目录，创建脚本文件：run.sh

```
touch run.sh
vim run.sh
```

添加启动命令：

```
nohup java -jar jenkins.war --httpPort=8080 &
```

授权：

```
chmod +x run.sh
```

启动：

```
./run.sh
```

![image-20220514172039459](images/image-20220514172039459.png)

3.2 停止脚本

在jenkins启动目录，创建脚本文件：stop.sh， 编辑停止命令

```
touch stop.sh
chmod +x stop.sh
vim stop.sh
```

```
echo "<-------------------------------------->"
# 查看该jar进程
pid=`ps -ef | grep jenkins | grep -v 'grep' | awk '{print $2}'`
echo "jenkins pid="${pid}
# 存在则kill,不存在打印一下吧
if [ "${pid}" ]; then
	kill -9 ${pid}
    # 检查kill是否成功
    if [ "$?" -eq 0 ]; then
        echo "jenkins stop success"
    else
        echo "jenkins stop fail"
    fi
else
	echo "进程不存在"
fi
```

停止：

```
./stop.sh
```

![image-20220514172940078](images/image-20220514172940078.png)

3.2.2 浏览器关闭jenkins方式：访问：http://localhost:8888/exit （url改成自己服务器url）

![image-20220514173130787](images/image-20220514173130787.png)

### 3. 全局工具配置

3.1 Maven，直接使用默认配置（前提是linux服务器上面，maven已配置好）

![image-20220514175254350](images/image-20220514175254350.png)

3.2 JDK配置：

![image-20220514175503260](images/image-20220514175503260.png)

3.3 git

![image-20220514175814596](images/image-20220514175814596.png)

3.4 maven home

![image-20220514182426776](images/image-20220514182426776.png)



保存即可！

### 4. 全局凭据配置

4.1 添加码云账号

![image-20220514180430561](images/image-20220514180430561.png)

### 5. 插件配置

5.1 maven构建工具插件

![image-20220514182114548](images/image-20220514182114548.png)

