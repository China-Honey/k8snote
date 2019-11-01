# 一、kubectl管理命令概要



## 1.常用命令

#### 1.1、创建

```
kubectl run nginx --replicas=3 --image=nginx:1.14	--port=80  
kubectl get deploy,pods
```

#### 1.2、发布

```
kubectl expose deployment nginx --port=80 --type=NodePort --target-port=80 --name=nginx-service

kubectl get service
```

#### 1.3、更新

```
kubectl set image deployment/nginx nginx=nginx:1.13 --record=true
```

#### 1.4、回滚

```
kubectl rollout history deployment/nginx  
kubectl rollout undo deployment/nginx
```

#### 1.5、删除

```
kubectl delete deploy/nginx  kubectl delete svc/nginx-service
```



> 命令补全
>
> yum -y install  bash-completion 
>
> source /usr/share/bash-completion/bash_completion
> source  <(kubectl  completion  bash)



## 2.基础命令

| 基础命令 | 描述                                                   |
| -------- | ------------------------------------------------------ |
| create   | 通过文件名或标准输入创建资源                           |
| expose   | 将一个资源公开为一个新的Service                        |
| run      | 在集群中运行一个特定的镜像                             |
| set      | 在对象上设置特定的功能                                 |
| get      | 显示一个或多个资源                                     |
| explain  | 文档参考资料                                           |
| edit     | 使用默认的编辑器编辑一个资源。                         |
| delete   | 通过文件名、标准输入、资源名称或标签选择器来删除资源。 |

## 3.部署命令

| 部署命令       | 描述                                               |
| -------------- | -------------------------------------------------- |
| rollout        | 管理资源的发布                                     |
| rolling-update | 对给定的复制控制器滚动更新                         |
| scale          | 扩容或缩容Pod数量，Deployment、ReplicaSet、RC或Job |
| autoscale      | 创建一个自动选择扩容或缩容并设置Pod数量            |

## 4.集群管理命令

| 集群管理命令 | 描述                                                 |
| ------------ | ---------------------------------------------------- |
| certificate  | 修改证书资源                                         |
| cluster-info | 显示集群信息                                         |
| top          | 显示资源（CPU/Memory/Storage）使用。需要Heapster运行 |
| cordon       | 标记节点不可调度                                     |
| uncordon     | 标记节点可调度                                       |
| drain        | 驱逐节点上的应用，准备下线维护                       |
| taint        | 修改节点taint标记                                    |

## 5.故障诊断和调试 命令

| 故障诊断和调试 命令 | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| describe            | 显示特定资源或资源组的详细信息                               |
| logs                | 在一个Pod中打印一个容器日志。如果Pod只有一个容器，容器名称是 可选的 |
| attach              | 附加到一个运行的容器                                         |
| exec                | 执行命令到容器                                               |
| port-forward        | 转发一个或多个本地端口到一个pod                              |
| proxy               | 运行一个proxy到Kubernetes API server                         |
| cp                  | 拷贝文件或目录到容器中                                       |
| auth                | 检查授权                                                     |

## 6.高级命令

| 高级命令 | 描述                               |
| -------- | ---------------------------------- |
| apply    | 通过文件名或标准输入对资源应用配置 |
| patch    | 使用补丁修改、更新资源的字段       |
| replace  | 通过文件名或标准输入替换一个资源   |
| convert  | 不同的API版本之间转换配置文件      |



## 7.设置命令

| 设置命令   | 描述                        |
| ---------- | --------------------------- |
| label      | 更新资源上的标签            |
| annotate   | 更新资源上的注释            |
| completion | 用于实现kubectl工具自动补全 |



## 8.其他命令

| 其他命令     | 描述                                                |
| ------------ | --------------------------------------------------- |
| api-versions | 打印受支持的API版本                                 |
| config       | 修改kubeconfig文件（用于访问API，比如配置认证信息） |
| help         | 所有命令帮助                                        |
| plugin       | 运行一个命令行插件                                  |
| version      | 打印客户端和服务版本信息                            |







# 二、YAML配置文件管理资源

### YAML 是一种简洁的非标记语言

**语法格式：**

- 缩进表示层级关系
- 不支持制表符“tab”缩进，使用空格缩进
- 通常开头缩进 2 个空格
- 字符后缩进 1 个空格，如冒号、逗号等
- “---” 表示YAML格式，一个文件的开始
- “#”注释

### 用run命令生成

```
kubectl run --image=nginx my-deploy -o yaml --dry-run > my-deploy.yaml
```



### 用get命令导出

```
kubectl get my-deploy/nginx -o=yaml --export
```



### Pod容器的字段拼写忘记了

```
kubectl explain pods.spec.containers
```



