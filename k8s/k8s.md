<!--
 * @Author: kekexili1230 457738564@qq.com
 * @Date: 2023-12-20 15:19:48
 * @LastEditors: kekexili1230 457738564@qq.com
 * @LastEditTime: 2023-12-27 15:51:22
 * @FilePath: /mynote/k8s.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
# k8s 笔记

## k8s简介

1. Kubernetes（通常简称为K8s）是一个开源的容器编排和管理平台，旨在自动化应用程序的部署、扩展和运维。

容器编排是一种自动化和管理容器化应用程序的过程，包括调度、部署、伸缩和联网等方面。

- 调度：选择合适的节点运行

- 部署：通过定义应用程序的拓扑结构、服务依赖和配置信息，能够自动化部署应用程序

- 伸缩：允许根据负载自动调整容器的数量

- 升级和回滚

- 配置管理：管理应用程序的配置信息

2. k8s的优点

- 简化部署

- 更好的利用硬件

- 健康检查和自我修复

- 自动扩容

## pod

1. pod是一组并置的容器，代表k8s中的基本构建模块。

- 总是工作在一个节点上

- 处于一个linux命名空间中，像是一个独立的逻辑机器，拥有自己的主机名和IP地址。

2. 尽可能在一个容器中运行一个进程（任务），涉及日志混乱和重启策略等因素。经pod视为独立的机器，每一个机器只托管一个应用。

3. 同一个pod中容器之间的部分隔离

- 共享的资源

    - 网络命名空间：所有的容器共享的网络命名空间，可以使用localhost通信，共享相同的端口信息

    - 存储卷：所有的容器可以访问同一个存储卷

- 相互隔离的资源

    - 文件系统视图：每一个文件都有自己独立的文件系统视图（文件系统和镜像有关） 

    - 环境变量：每一个容器有自己的环境变量

4. 通过yaml文件创建pod

## yaml文件

- API版本

- k8s资源类型

    - Pod

    - ReplicateSet

    - Deployment

    - Service

    - Namespace

    - ConfigMap

    - pv/pvc

    - Job

- metadata

    包括名称、命名空间、标签

- spec

    docker、卷、资源和其他数据

- status

    pod所处的条件、每个容器的描述和状态，以及内部IP


## kubectl常用命令

1. 根据yaml文件常见pod

kubectl create -f xxx.yaml

2. 根据运行中的pod查看yaml文件

kubectl get pod -o yaml/json

3. 查看日志，多容器pod的日志

kubectl logs pod_name -c <container name>

4. 端口转发：使用场景，不通过service和pod进行通信，将主机端口host_port映射到pod端口pod_port

kubectl port-forward pod_name host_port:pod_pod

5. 显示所有标签

kubectl get pod --show-labels

kubectl get pod -L lable_name1, label_name2

6. 添加标签

kubectl label pod pod_name lable_name

7. 修改标签

kubect label pod pod_name label_key=label_value --overwrite

## 标签和标签选择器

1. 标签是一组键值对，附加到资源上，通过标签选择器选择标签。

2. 通过标签组织k8s资源，包括pod，service

    - 标签的唯一性：同一个资源对象中的标签必须是唯一的。例如两个pod中，可以存在相同的标签"app"

3. 标签选择器语法



## ReplicationController(副本控制)

1. RC通过标签选择器匹配pod，控制pod的数量，当pod数量不匹配时，RC增加或者删除pod

2. RS和RC的区别：

    - RS的pod选择器的表达能力更强

    - ReplicaSet 支持滚动更新策略，允许逐步替换旧的 Pod

3. DaemonSet: 

    - 在集群的每一个节点上部署pod

    - 使用节点选择器可以将pod部署到指定节点

4. Job资源

    - 运行完成后就终止的pod

    - 任务正常结束，不重启容器

    - 任务异常退出，重启容器

    - 节点异常，调度到其他节点

    - cronJob


## 服务

1. 为什么需要服务？

    - pod是短暂的，会随时关闭或者删除

    - k8s在pod启动前，给pod分配IP地址，因此外部服务无法提前知道pod的IP地址

    - 扩容意味着多个pod会提供相同的服务，每一个pod都有自己的IP地址

2. k8s的服务是一种为一组功能相同的pod提供单一不变接入点的资源。

    - 服务的IP地址和端口不变

    - 客户端通过服务IP地址和端口号建立连接，这些连接被路由到提供服务的pod集群

3. 服务可以暴露多个端口

    - 服务端口接收请求，并将请求转发到pod端口上。

4. 服务选择集群pod的两种方式：

    - sessionAffinity: None, 随机选择后端pod中的一个，即使连接来自同一个客户端

    - sessionAffinity: ClientIP，将来自同一个client Ip 的所有请求转发至同一个pod

    - 不支持cookie，k8s处理请求工作在传输层，不关心应用层

5. 集群内部服务发现

    - 通过环境变量发现服务：启动pod后，pod中的环境变量会列出当前所有服务的IP地址和端口号。环境变量的命名规则：

      - <服务名>_SERVICE_HOST 和  <服务名>_SERVICE_PORT

      - 服务名用大写字母表示，服务名中的横线用下划线"_"替换
    
    - 通过DNS发现服务

        - kube-dns，该pod运行dns服务，集群中其他pod使用其作为dns服务器（k8s修改pod的/etc/resolv.conf文件实现）。

        - dns服务器知道系统中所有的服务

        - pod通过服务名称（FQDN）在dns服务器中查询服务的IP地址

            - FQDN（全限定域名）：<service_name>.<namespace>.svc.cluster.local

            - 如果服务在相同命名空间下，可以直接使用服务名称<service_name>访问
        
        - 服务的端口号

            - 标准端口

            - 环境变量

6. 集群外部服务

    - endpoint资源：endpoint是一种资源类型，用于将服务和后端的pod关联起来。当创建一个服务（Service）时，Kubernetes 会自动创建相应的 Endpoint 对象。Endpoint 对象描述了服务后端 Pod 的网络地址信息，包括每个 Pod 的 IP 地址和端口号。这使得服务能够将请求正确路由到其后端 Pod。

    - 服务中定义了pod选择器，选择器用来构建IP和端口列表，然后存储在Endpoint资源中。

    - 作为客户端，连接外部服务：创建ExternalName类型的服务，配置如下所示
      
        ```yaml
        apiVersion: v1
        kind: Service
        metadata:
            name: external-service
        spec:
            type: ExternalName
            externalName: external-service.example.com
        ```

    - 作为服务器，接受外部服务连接

        - 将服务的类型设置成nodePort：通过创建NodePort服务，可以在所有节点上保留端口，并将传入的连接转发给相关pod
        
            ```yaml
            apiVersion: v1
            kind: Service
            metadata:
            name: my-service
            spec:
            type: NodePort
            ports:
            -   port: 80  # Service 暴露的集群内部端口，集群内部服务可以通过<cluster_service_ip>:80访问服务
                targetPort: 8080  # Pod 上运行的应用程序的端口
                nodePort: 30123  # Node上端口，集群外部服务可以通过<node_ip>:30123访问服务
            selector:
                app: my-app  # 匹配包含标签 app=my-app 的 Pod
            ```

        - 将服务的类型设置成LoadBalance：负载均衡器拥有公开可访问的IP地址，将所有连接重定向到服务，有云服务商提供。
          
            ```yaml
            apiVersion: v1
            kind: Service
            metadata:
            name: my-service
            spec:
            type: LoadBalancer
            ports:
                - protocol: TCP
                port: 80  # 集群用户访问服务的端口
                targetPort: 8080  # 服务内部 Pod 的端口
                NodePort: 30123 
            selector:
                app: my-app  # 匹配包含标签 app=my-app 的 Pod
            ```

        - 将服务的类型设置成Ingress: 为多个服务提供外部连接请求
        
          - 集群中必须存在ingress控制器

          - 创建ingress资源

          - 修改DNS服务，将请求域名执行ingress ip地址

          - 通过ingress访问pod

        - ingress的工作原理

            ```yaml
            apiVersion: networking.k8s.io/v1
            kind: Ingress
            metadata:
            name: my-ingress
            spec:
            rules:
            - host: mydomain.com
                http:
                paths:
                - path: /app1  # 将www.mydomain.com/app1 映射到 service-app1服务
                    pathType: Prefix
                    backend:
                    service:
                        name: service-app1
                        port:
                        number: 80
                - path: /app2 # 将www.mydomain.com/app2 映射到 service-app2服务
                    pathType: Prefix
                    backend:
                    service:
                        name: service-app2
                        port:
                        number: 80
            ```

7. 就绪探针

就绪探针表示容器是否准备好接收请求。就绪探针和存活探针一致包括三种类型：

- exec探针

- http get探针

- tcp socket 探针

就绪探针未通过检查不会删除pod。


## 卷

1. 为什么引入卷？

    - 每一个容器都有自己独立的文件系统，容器重启后，写入文件系统的数据丢失


    - 希望在容器启动后，能够利用之前写入文件系统的数据


    - 一个pod中的所有容器都可以使用卷


2. emptyDir卷

- 卷的生命周期和pod一致，当pod删除时，emptyDir卷也被删除，内容丢失

- 用途：同一个pod中不同容器之间共享数据，典型使用场景：共享临时数据，存储应用程序运行时数据
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
    name: multi-container-pod
    spec:
    containers:
    - name: container-1
        image: nginx
        volumeMounts:
        - name: shared-volume
        mountPath: /path-for-container-1
    
    - name: container-2
        image: busybox
        command: ["/bin/sh", "-c", "while true; do cat /path-for-container-2/data.txt; sleep 10; done"]
        volumeMounts:
        - name: shared-volume
        mountPath: /path-for-container-2
    
    volumes:
    - name: shared-volume
        emptyDir: {}
    ```
    
    container-1在目录/path-for-container-1下写的文件data.txt，可以被container-2在/path-for-container-2目录下看到

3. hostPath卷

- 使用场景：系统级别的pod需要读取节点的文件或使用节点文件系统访问节点设备。在同一个节点上运行并在hostPath卷中使用相同路径的pod可以看到相同的文件。

    - 访问主机配置文件

    - 在主机上生成日志文件

    - 在pod间共享主机文件

4. pv和pvc

- pv、pvc和物理存储：

    - 物理存储（Physical Storage）:实际的底层存储资源，物理存储是实际存储数据的地方。由集群管理员创建
    
    - PV 是 Kubernetes 中对物理存储进行抽象的对象，它代表集群中的一个存储卷。由集群管理员创建

    - PVC 是由应用程序或用户创建的对象，它请求 Kubernetes 分配一个满足特定需求的 PV。

    - pod或者应用使用pvc进行数据存储





## ConfigMap和Secret

1. 容器化应用的配置方式

- 配置嵌入应用本身

- 命令行参数的形式配置应用

- 借助环境变量

- 使用configMap

2. 向容器传递命令行参数：容器中运行的完整指令由两部分组成：命令（EntryPoint）和参数（CMD）

- EntryPoint定义容器启动时调用的可执行程序

- CMD指定传给EntryPoint的参数

3. 在configMap文件中配置环境变量，程序通过环境变量获取配置信息

```shell
    kubectl create configmap my-configmap --from-file=key=example.txt
```

执行上述命令后创建一个configmap，configmap内容：

```yaml
apiVersion: v1
data:
    key: |
    key1=value1
    key2=value2
    key3=value3
kind: ConfigMap
metadata:
  creationTimestamp: "2023-01-01T00:00:00Z"
  name: my-configmap
  namespace: default
  resourceVersion: "123456"
  uid: abcdefgh-ijkl-mnop-qrst-uvwxyz123456

```

## StatefulSet

1. 为什么需要引入StatefulSet?

- RS管理的Pod使用相同的卷，每一个pod实例无法保持自己的持久化状态

- 每一个Pod无法提供稳定的标识，在分布式应用中该需求很普遍

2. StatefulSet

- 通过StatefulSet创建的pod挂掉后，实例有相同的名称、网络标识和状态

    - pod名称

        - 每一个pod都有一个从零开始的顺序索引
        
        - pod名称由StatefulSet加上该实例的顺序索引值组成
    
    - pod IP 

        - 通过创建一个headlessService服务，每一个pod都独立的DNS记录

        - 通过DNS服务，使用headlessService服务可以查询到所有的pod名称

        - 通过<podName>.<serviceName>.<namespace>.svc.cluster.local可以访问 podName
    
- 每一个pod实例拥有独立的数据卷

3. StatefulSet的扩容和缩容

- 扩容：新的pod实例索引值增加1

- 缩容：最先删除索引值最高的pod实例


## k8s资源限制 






















​            





















