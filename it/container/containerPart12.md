---
layout: default
title: Docker、k8sまとめ[第12回] オートスケール
description: Docker、k8sまとめ。k8sでオートスケールする方法について記載します。
category: IT
---

[第11回](/it/container/containerPart11.html)ではスケジューリング対象から除外やノード、Podを停止する方法について記載ました。

今回はオートスケールについてに記載します。

## 目次

- [Metric Serverの構築](#anchor1)  
- [Horizontal Pod Autoscaling](#anchor2)
- [まとめ](#anchor4)

<a id="anchor1"></a>

## Metric Serverの構築

Docker Desktopに入れるには少し工夫がいるようで、以下をすればよいようです。

1. [Metric Server](https://github.com/kubernetes-sigs/metrics-server/releases)から、`components.yaml
`をダウンロード
2. Yamlの`args`に以下を追記
`- --kubelet-insecure-tls`
3. `kubectl apply -f components.yaml`で適用

普通に、`kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.5.0/components.yaml`としても、動作しない・・・

metrics-serverが起動していればOK
以下で確認できます。

```Shell
$ kubectl -n kube-system get pod
NAME                                     READY   STATUS    RESTARTS   AGE
・・・
metrics-server-6d84b55499-df8h2          1/1     Running   0          26m
・・・
```

## Horizontal Pod Autoscaling

以下のYamlを適用します。

```Yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.13.12
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: "1"
          requests:
            cpu: "0.5"

---
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
    - name: http
      port: 8080
      targetPort: 80
      
---
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: hpa-nginx
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 1
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      targetAverageUtilization: 50
```

deploymentでCPUの制限を加えないと動作しません。
```Yaml
        resources:
          limits:
            cpu: "1"
          requests:
            cpu: "0.5"
```

以下を実行すると、どれくらい負荷がかかっていると認識しているかを見ることができます。

```
$ kubectl get HorizontalPodAutoscaler hpa-nginx
NAME        REFERENCE                     TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
hpa-nginx   Deployment/nginx-deployment   0%/50%    1         5         2          15m
```

今は負荷をかけてないので、最初は<unkonw>とでるとおもいますが、そのうち0%になります。

ならなかったら、metrics-serverが動いてるか確認してください。

podを確認すると1つになると思います。負荷がかかってないですからね~。
```Shell
$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7fcff5dcc6-dlqwp   1/1     Running   0          30m
```

そこで負荷をかけてみましょう。
ログインして、ddコマンドを使って負荷をかけます。

```Shell
$ kubectl exec --stdin --tty nginx-deployment-7fcff5dcc6-dlqwp -- /bin/bash
root@nginx-deployment-7fcff5dcc6-dlqwp:/# dd if=/dev/zero of=/dev/null
```

しばらくすると以下のようになって4つほどPodができるます。


```Shell
$ kubectl get pod
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7fcff5dcc6-2s5s6   1/1     Running   0          53s
nginx-deployment-7fcff5dcc6-8srgt   1/1     Running   0          53s
nginx-deployment-7fcff5dcc6-d42sj   1/1     Running   0          53s
nginx-deployment-7fcff5dcc6-dlqwp   1/1     Running   0          35m

$ kubectl get HorizontalPodAutoscaler
NAME        REFERENCE                     TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
hpa-nginx   Deployment/nginx-deployment   49%/50%   1         5         4          23m
```

タイムラグがあるので、途中で見ると、以下のようになります。200パーセント(#^^#)

```Shell
kubectl get HorizontalPodAutoscaler
NAME        REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
hpa-nginx   Deployment/nginx-deployment   202%/50%   1         5         1          33m
```

また、次のコマンドで、どう状態が変わったを見ることができます。

```Shell
$ kubectl describe HorizontalPodAutoscaler hpa-nginx
Name:                                                  hpa-nginx
Namespace:                                             default
Labels:                                                <none>
Annotations:                                           <none>
CreationTimestamp:                                     Wed, 14 Jul 2021 01:12:07 +0900
Reference:                                             Deployment/nginx-deployment
Metrics:                                               ( current / target )
  resource cpu on pods  (as a percentage of request):  8% (43m) / 50%
Min replicas:                                          1
Max replicas:                                          5
Deployment pods:                                       4 current / 4 desired
Conditions:
  Type            Status  Reason               Message
  ----            ------  ------               -------
  AbleToScale     True    ScaleDownStabilized  recent recommendations were higher than current one, applying the highest recent recommendation
  ScalingActive   True    ValidMetricFound     the HPA was able to successfully calculate a replica count from cpu resource utilization (percentage of request)
  ScalingLimited  False   DesiredWithinRange   the desired count is within the acceptable range
Events:
  Type    Reason             Age                 From                       Message
  ----    ------             ----                ----                       -------
  Normal  SuccessfulRescale  39m                 horizontal-pod-autoscaler  New size: 3; reason: cpu resource utilization (percentage of request) above target
  Normal  SuccessfulRescale  12m (x3 over 41m)   horizontal-pod-autoscaler  New size: 1; reason: All metrics below target
  Normal  SuccessfulRescale  8m1s (x4 over 35m)  horizontal-pod-autoscaler  New size: 4; reason: cpu resource utilization (percentage of request) above target
  Normal  SuccessfulRescale  7m1s                horizontal-pod-autoscaler  New size: 5; reason:
  Normal  SuccessfulRescale  2m59s               horizontal-pod-autoscaler  New size: 4; reason: All metrics below target
```

3→1→4→5→4とPodが変わっていることがわかりますね。

<a id="anchor3"></a>

## まとめ

HorizontalPodAutoscalerを利用することで、Pod数を負荷によって自動で変えることができます。
Docker Desktopで試すには、metric-serverを立てるときに少し変更が必要です。

今回は、HorizontalPodAutoscalerでしたが、VerticalPodAutoscalerというのもあるらしい。
リソース（CPU、メモリ）の使用量を増やせたりするみたい。

