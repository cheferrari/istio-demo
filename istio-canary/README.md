## istio canary deployment demo
refers to https://kublr.com/blog/hands-on-canary-deployments-with-istio-and-kubernetes/  
#### 本例子演示的金丝雀发布实现是同通过在请求头中添加额外的信息从而达到不同的路由


## 官方 istio canary 实现
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
