# 一、收集哪些日志

- K8S系统的组件日志
- K8S Cluster里面部署的应用程序日志

# 二、日志方案

![1568274177811](\assets\1568274177811.png)



# 三、容器中的日志怎么收集

### 方案一

**Node上部署一个日志收集程序**

- DaemonSet方式部署日志收集程序
- 对本节点/var/log和/var/lib/docker/containers/两个目录下的日志进行采集

![1568274304505](\assets\1568274304505.png)



### 方案二

**Pod中附加专用日志收集的容器**

- 每个运行应用程序的Pod中增加一个日志收集容器，使用emtyDir共享日志目录让日志收集程序读取到。



![1568274313578](\assets\1568274313578.png)

### 方案三

**应用程序直接推送日志**

- 超出Kubernetes范围

![1568274322862](\assets\1568274322862.png)



| 方式                                | 优点                                                       | 缺点                                                         |
| ----------------------------------- | ---------------------------------------------------------- | ------------------------------------------------------------ |
| 方案一：Node上部署一个日志收集程序  | 每个Node仅需部署一个日志收集程序，资源消耗少，对应用无侵入 | 应用程序日志需要写到标准输出和标准错误输出，不支持多行日志   |
| 方案二：Pod中附加专用日志收集的容器 | 低耦合                                                     | 每个Pod启动一个日志收集代理，增加资源消耗，并增加运维维护成本 |
| 方案三：应用程序直接推送日志        | 无需额外收集工具                                           | 浸入应用，增加应用复杂度                                     |





### 方案二例子Pod中附加专用日志收集的容器

```
containers:
  - name: web
    image: reg.example.com/project/web:1.1
    ports:
    - containerPort: 8080
    volumeMounts:
    - name: tomcat-catalina
      mountPath: /usr/local/tomcat/logs
  - name: filebeat
    image: reg.example.com/ops/filebeat:6.4.1
    args: [
      "-c", "/etc/filebeat.yml",
      "-e",
    ]
    volumeMounts:
    - name: filebeat-config
      mountPath: /etc/filebeat.yml
      subPath: filebeat.yml
    - name: tomcat-catalina
      mountPath: /usr/local/tomcat/logs
volumes:
  - name: tomcat-catalina
    emptyDir: {}
  - name: filebeat-config
    configMap:
      name: filebeat-config
```





```
apiVersion: v1
kind: ConfigMap
metadata:
  name: filebeat-config
data:
  filebeat.yml: |-
    filebeat.prospectors:
      - type: log
        paths:
        - /usr/local/tomcat/logs/catalina*.log
      fields:
        app: tomcat
        type: project-catalina
      fields_under_root: true
      multiline:
        pattern: '^\['
        negate: true
        match: after
  output.redis:
    hosts: ["10.213.94.202"]
    password: "elk"
    key: "filebeat"
    db: 0
    datatype: list
```

