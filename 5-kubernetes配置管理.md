# 一、Secret

加密数据并存放Etcd中，让Pod的容器以挂载Volume方式访问。

应用场景：凭据

官方文档：https://kubernetes.io/docs/concepts/configuration/secret/

```
# Create files needed for rest of example.
echo -n 'admin' > ./username.txt
echo -n '1f2d1e2e67df' > ./password.txt
```

> ```shell
> kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt
> secret "db-user-pass" created
> ```
>
> 

> ```shell
> kubectl get secrets
> NAME                  TYPE                                  DATA      AGE
> db-user-pass          Opaque                                2         51s
> ```
>
> 

> ```shell
> kubectl describe secrets/db-user-pass
> Name:            db-user-pass
> Namespace:       default
> Labels:          <none>
> Annotations:     <none>
> 
> Type:            Opaque
> 
> Data
> ====
> password.txt:    12 bytes
> username.txt:    5 bytes
> ```



#### 1.手动创建加密凭据

```
echo -n 'admin' | base64
YWRtaW4=
echo -n '1f2d1e2e67df' | base64
MWYyZDFlMmU2N2Rm
```

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
```

> ```shell
> kubectl apply -f ./secret.yaml
> ```



#### 2.Secret 作为环境变量

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: nginx
    image: nginx
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: password

#容器中查看变量 echo $SECRET_USERNAME
#容器中查看变量 echo $SECRET_PASSWORD



```

#### 3.secret作为文件挂载

```
---
##保存Secret到/etc/foo 目录中
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret

# cat /etc/foo/username
# cat /etc/foo/password
```





# 二、Configmap

与Secret类似，区别在于ConfigMap保存的是不需要加密配置信息。

应用场景：应用配置

https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/



```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myconfig
  namespace: default
data:
  special.level: info
  special.type: hello

---

apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: busybox
      image: busybox
      command: [ "/bin/sh", "-c", "echo $(LEVEL) $(TYPE)" ]
      env:
        - name: LEVEL
          valueFrom:
            configMapKeyRef:
              name: myconfig
              key: special.level
        - name: TYPE
          valueFrom:
            configMapKeyRef:
              name: myconfig
              key: special.type
  restartPolicy: Never


---

apiVersion: v1
kind: ConfigMap
metadata:
  name: redis-config
data:
  redis.properties: |
    redis.host=127.0.0.1
    redis.port=6379
    redis.password=123456

---

apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: busybox
      image: busybox
      command: [ "/bin/sh","-c","cat /etc/config/redis.properties" ]
      volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: redis-config
  restartPolicy: Never

```

