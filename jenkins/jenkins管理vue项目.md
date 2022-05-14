# jenkins管理vue项目

## 1. 创建任务

创建自由风格的软件项目：

![image-20220514212726218](images/image-20220514212726218.png)

## 2. 配置

#### 2.1 Git

![image-20220514213003165](images/image-20220514213003165.png)

#### 2.2 构建

选择执行shell命令(编译vue任务）：

![image-20220514222614201](images/image-20220514222614201.png)

```linux
node -v
npm install 
rm -rf ./dist/*  
npm run build  
rm -rf /usr/local/nginx/webapps/dist/*  
cp -rf ./dist/*  /usr/local/nginx/webapps/dist/
```

## 3.构建

shell执行需要node环境，如果没有node环境，需要配置，参考：

![image-20220514213438749](images/image-20220514213438749.png)

Success则说明成功了

![image-20220514214637615](images/image-20220514214637615.png)