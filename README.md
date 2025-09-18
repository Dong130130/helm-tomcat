# Tomcat Helm Chart

这是一个使用 Helm 模板部署 Tomcat 服务的 Chart，满足面试题要求。  
Chart 包含 Deployment、Service、Ingress 模板，并在关键部分进行了注释。

## 特性

- 部署 Tomcat:latest 镜像
- 副本数默认 3 个
- 命名空间固定为 `my-namespace`
- 使用 Ingress（nginx）暴露服务
  - 域名：`my-tomcat.test.com`
  - 支持最大上传 2G 文件
  - 代理超时时间 120 秒
- Helm 模板支持可配置化，关键参数可通过 `values.yaml` 修改


## 目录结构
```
helm-tomcat/
├── Chart.yaml
├── values.yaml
├── templates/
│ ├── deployment.yaml
│ ├── service.yaml
│ ├── ingress.yaml
│ └── _helpers.tpl
└── README.md
```

## 安装说明

1. 创建命名空间（如果不存在）：
```
kubectl create namespace my-namespace
```

安装 Helm Chart：
```
helm install my-tomcat ./helm-tomcat -n my-namespace
```

检查资源是否创建：
```
kubectl get deploy -n my-namespace
kubectl get svc -n my-namespace
kubectl get ingress -n my-namespace
```

卸载 Chart
```
helm uninstall my-tomcat -n my-namespace
```


项目的总结文档请见 [SUMMARY.md](./SUMMARY.md)




























