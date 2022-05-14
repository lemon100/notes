# jenkins管理java项目（使用git代码管理）

### 1. 创建任务

![image-20220514180708493](images/image-20220514180708493.png)

创建maven项目

![image-20220514182216585](images/image-20220514182216585.png)

### 2. Git配置

![image-20220514181029668](images/image-20220514181029668.png)

### 3. Pre Steps

![image-20220514182624555](images/image-20220514182624555.png)

### 4. Post Steps

![image-20220514183128714](images/image-20220514183128714.png)

选择build succeeds，shell命令根据实际项目调整

```linux
#输入Maven打包后的项目名称
app=inventory-0.0.1-SNAPSHOT
#项目移动的目的地址
path=/home/projects/inventory
echo this is app : $app

#若项目已启动，杀死旧进程
api_pid=`ps -ef | grep "$app.jar" | grep -v grep | awk '{print $2}'`
echo api_pid = $api_pid

if [ "$api_pid" != "" ]; then
        echo kill api
        kill -9 $api_pid

        echo sleep 3s
        sleep 1
        echo sleep 2s
        sleep 1
        echo sleep 1s
        sleep 1
fi

#将jar包从jenkins工作空间中移动到指定路径下
mv /root/.jenkins/workspace/inventory_java/target/$app.jar $path
cd $path

#防止进程被杀死
BUILD_ID=dontKillMe

#后台进程形式启动项目
nohup java -jar -Dspring.profiles.active=dev2 $app.jar &
echo $app start success
exit 0
```

### 5. 运行任务

![image-20220514185106317](images/image-20220514185106317.png)

查看控制台输出：

![image-20220514185125166](images/image-20220514185125166.png)

Success则说明成功

![image-20220514185148764](images/image-20220514185148764.png)

服务器上面查看进程：

![image-20220514185213989](images/image-20220514185213989.png)