# 一、准备工作与注意事项

## 1.部署项目情况

1.业务架构及服务:  dubbo、spring cloud

2.第三方服务：例如：mysql\redis\zookeeepr\eruka,mq

3,服务之间怎么通信

4.资源消耗，硬件资源，带宽

### 2.部署项目时用到的K8S资源

1.使用namespes命名空间进项项目隔离（dev,test,prod）

2.无状态应用（deployment），有状态应用（statefuleset,pv,pvc）

3.暴露外部访问(service,ingress)

4.secret,configmap

### 3.项目基础镜像

```
docker search lizhenliang
```

 https://github.com/lizhenliang/php-demo 

 https://github.com/lizhenliang/tomcat-java-demo 

### 4.编排部署

1.项目构建（java）,ci/cd环境自动完成（ 代码拉取--代码编译构建---镜像打包---推送仓库---部署）

2.编写yaml文件，使用仓库镜像进行部署

- deploment部署web
- statefulset 部署Mysql
- 镜像认证拉取secret
- service & Ingress 暴露



### 5.工作流程

kubectl ----- > yaml ----> 仓库拉去镜像 ---->  service(集群内部访问) Ingress(暴露给外部用户)





# 二、准备基础镜像并推送到镜像仓库



```
#安装java编译环境
yum install -y java maven

#拉去代码
git clone https://github.com/lizhenliang/tomcat-java-demo.git


#编译java包
mvn clean package

#完成后
[root@master-a tomcat-java-demo]# ll target/ -h
total 18M
drwxr-xr-x 5 root root  95 Nov 11 21:15 classes
drwxr-xr-x 3 root root  25 Nov 11 21:15 generated-sources
drwxr-xr-x 4 root root  37 Nov 11 21:15 ly-simple-tomcat-0.0.1-SNAPSHOT
-rw-r--r-- 1 root root 18M Nov 11 21:15 ly-simple-tomcat-0.0.1-SNAPSHOT.war
drwxr-xr-x 2 root root  28 Nov 11 21:15 maven-archiver
drwxr-xr-x 3 root root  35 Nov 11 21:15 maven-status



#打包镜像
docker build -t registry.cn-hangzhou.aliyuncs.com/qinzc-k8s/tomacat-java-demo .

#推送至仓库
docker push registry.cn-hangzhou.aliyuncs.com/qinzc-k8s/tomacat-java-demo
```





# 三、在K8S中部署Java项目

- 命名空间（环境）

```
apiVersion: v1
kind: Namespace
  name: test
```



- deploment部署web

```

```



- statefulset 部署Mysql

```

```



- 镜像认证拉取secret

```

```



- service & Ingress 暴露

```

```











# 四、在K8S中部署PHP项目