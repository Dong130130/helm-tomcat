# 项目总结 - Helm Tomcat Chart

## 1. 项目目标
根据面试题要求，实现一个基于 Helm 模板的 Tomcat 部署，要求如下：
1. 使用 `tomcat:latest` 镜像，并使用默认参数和端口。
2. 在 `my-namespace` 命名空间部署。
3. 使用 Deployment 部署，副本数设置为 3。
4. 配置 Ingress（nginx）暴露服务，域名为 `my-tomcat.test.com`，支持最大上传 2G 文件，代理超时时间 120 秒。
5. Helm 模板化，关键部分添加注释。


## 2. 项目实现步骤

### 2.1 Helm Chart 创建
- 使用 `helm create tomcat-chart` 创建基础模板。
- 删除不需要的模板文件：HPA、ServiceAccount、HttpRoute、tests。
- 保留 Deployment、Service、Ingress 以及 `_helpers.tpl`（用于模板函数和生成资源名称）。

### 2.2 Deployment 模板
- 使用 `my-namespace` 作为部署命名空间（如果不存在，需要先创建）。
- 设置副本数 `replicaCount: 3`，从 `values.yaml` 读取。
- 镜像由 `values.yaml` 配置，支持动态修改。
- 容器端口保持 Tomcat 默认 8080。

### 2.3 Service 模板
- 类型为 `ClusterIP`，暴露 8080 端口。
- Service 名称与 Release 名称一致，方便 Ingress 关联。
- 注释说明 Service 类型和端口含义。

### 2.4 Ingress 模板
- IngressClass 使用 nginx。

- 域名 my-tomcat.test.com 和路径 / 通过 values.yaml 配置，模板中动态渲染。

- 关键注解（允许上传 2G 文件、代理超时时间 120 秒）同样由 values.yaml 控制：

- nginx.ingress.kubernetes.io/proxy-body-size: "2g"

- nginx.ingress.kubernetes.io/proxy-read-timeout: "120"


### 2.5 values.yaml
- 修改关键可配置参数：
  - `replicaCount`
  - `image.repository` / `image.tag`
  - `service.port`
  - `ingress.hosts` / `ingress.annotations`
- 说明了 Helm 模板化和可配置化优势。

### 2.6 外部访问方法
- 使用 nginx IngressClass，将服务暴露给集群外部访问。
- Deployment 对应的 Service 使用 ClusterIP 类型，Ingress 将外部请求路由到该 Service。
- 本地访问方法：
  - 将 `my-tomcat.test.com` 映射到 Ingress 控制器的外部 IP（或 minikube / kind 的节点 IP）  
    示例：在 `/etc/hosts` 文件中添加  
    ```
    127.0.0.1  my-tomcat.test.com
    ```
  - 使用浏览器或 curl 访问 `http://my-tomcat.test.com/`




