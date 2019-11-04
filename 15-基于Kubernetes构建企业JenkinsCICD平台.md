# 一、发布流程设计



![1568280837735](\assets\1568280837735.png)



**软件环境：**Jenkins + Kubernetes + Git + Maven + Harbor

**工作流程：**手动/自动构建-> Jenkins 调度K8S API-＞动态生成Jenkins Slave pod -＞Slave pod 拉取Git 代码／编译／打包镜像-＞推送到镜像仓库Harbor -＞Slave 工作完成，Pod 自动销毁-＞部署到测试或生产Kubernetes平台。





# 二、部署Harbor镜像仓库

# 三、部署Git仓库

# 四、构建Jenkins-Slave镜像

![1568280895846](\assets\1568280895846.png)

参考：https://github.com/jenkinsci/docker-jnlp-slave

# 五、在Kubernetes中部署Jenkins

参考：https://github.com/jenkinsci/kubernetes-plugin/tree/fc40c869edfd9e3904a9a56b0f80c5a25e988fa1/src/main/kubernetes

# 六、Jenkins与Kubernetes集成



### 插件：

- KubernetesContinuousDeploy

- Kubernetes

### 凭据：

- 在Kubernetes部署应用（kubeconfig）

# 七、流水线发布Java项目



**插件：**ExtendedChoiceParameter
**凭据：**访问Git仓库



# 八、回滚

#### 查看发布历史
```
kubectl rollout history deployment/nginx
```

#### 查看指定版本对应的Deployment的API对象细节
```
kubectl rollout history deployment/nginx --revision=3
```

#### 回滚到上一个版本
```
kubectl rollout undo deployment/nginx
```

#### 回滚到指定版本号
```
kubectl rollout undo deployment/nginx --revision=3
```

#### 查看部署状态
```
kubectl rollout status deployment/nginx
```

# 小结

