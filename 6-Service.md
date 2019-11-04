# 什么是service?

- #### 防止Pod失联

- #### 定义一组Pod的访问策略

- #### 支持ClusterIP，NodePort以及LoadBalancer三种类型

- #### Service的底层实现主要有iptables和ipvs二种网络模式





# 一、Pod与Service的关系



- 通过label-selector相关联
- 通过Service实现Pod的负载均衡（TCP/UDP 4层）

![1567996116729](\assets\1567996116729.png)



# 二、Service类型

![1567996138489](\assets\1567996138489.png)

```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: A
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

![1567996201649](\assets\1567996201649.png)





```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: A
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
      nodePort: 30001
  type: NodePort
```











# 三、Service代理模式



![1567996324341](\assets\1567996324341.png)

- ### **Iptables**

    - **灵活，功能强大**
    - **规则遍历匹配和更新，呈线性时延**
    - **可扩展性**

- ### **IPVS**
  
    - **工作在内核态，有更好的性能**
    - **调度算法丰富：rr，wrr，lc，wlc，ip hash...**





# 四、DNS

DNS服务监视Kubernetes API，为每一个Service创建DNS记录用于域名解析。

ClusterIP A记录格式：`<service-name>.<namespace-name>.svc.cluster.local  `

示例：`my-svc.my-namespace.svc.cluster.local`

