## istio canary deployment demo
### 1、两种灰度策略（灰度发布有时也称金丝雀发布）
策略类型分为“基于请求内容发布”和“基于流量比例发布”，通过页签选择确定。  
灰度发布是将原有生产环境的默认版本的流量引流一部分到新版本中，以便进行测试版本是否有什么问题。
#### 1.1 基于请求内容发布：
对当前版本配置相应的请求内容规则，服务流量在满足此规则的情况下，会走此版本。例如http请求，请求头cookie必须满足“访问条件”走版本A。
#### 1.2 基于流量比例发布：
对当前版本配置相应的流量权重，服务流量将会按照权重比率以对应的概率分发当前版本。例如10%的流量走版本A，90%的流量走版本B。

### 2、本例子演示的金丝雀发布（基于请求内容，http请求头必须包含qa:canary-test）实现是同通过在请求头中添加额外的信息从而达到不同的路由
refers to https://kublr.com/blog/hands-on-canary-deployments-with-istio-and-kubernetes/  

### 3、官方 istio canary 实现 （基于流量比例）
refers to https://istio.io/blog/2017/0.1-canary/  
#### 官方例子是通过设置每个路由目的地的权重达到流量分发的目的
```
cat <<EOF | kubectl apply -f -
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: helloworld
spec:
  hosts:
    - helloworld
  http:
  - route:
    - destination:
        host: helloworld
        subset: v1
      weight: 90
    - destination:
        host: helloworld
        subset: v2
      weight: 10
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: helloworld
spec:
  host: helloworld
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
EOF
```
