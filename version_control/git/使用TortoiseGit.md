# 使用TortoiseGit管理
a.安装完成TortoiseGit之后，尝试更新，发现以下问题。  
![](pic/TortoiseGit/error1.png)

![](pic/TortoiseGit/error2.png)

b.看帮助文档发现，是因为我们初始化git使用的git bash，生成的.ssh/config不被TortoiseGit使用的默认SSH Client（TortoiseGitPlink）识别。  
![](pic/TortoiseGit/solve1.png)

![](pic/TortoiseGit/solve2.png)

c. 解决方式：

打开TortoiseGit的setting，  
![](pic/TortoiseGit/solve3.png)

选择General，把Git.exe path改到我们安装git的地址：  
![](pic/TortoiseGit/solve4.png)

选择Network，把SSH client改为ssh.exe或者ssh.exe全路径（C:\Program Files\Git\usr\bin\ssh.exe）  
![](pic/TortoiseGit/solve5.png)

d. 尝试commit文件并push到github

①commit  
![](pic/TortoiseGit/commit.png)

② push到github  
![](pic/TortoiseGit/push1.png)

![](pic/TortoiseGit/push2.png)



### Success

