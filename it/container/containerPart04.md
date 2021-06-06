---
layout: default
title: Docker、k8sまとめ[第4回] Docker Desktopでk8s
description: Docker、k8sまとめ。第4回はDocker Desktopでk8sを利用します。
category: IT
---

[第3回](/it/container/containerPart02.html)はDocker Desktopを利用して  
Dockerfileについて紹介しました。  
今回はDocker Desktopでk8sを紹介しようと思います。

## 目次

- [Docker Desktopでk8sの利用設定](#anchor1)  
- [k8sでnginxのクラスタを起動](#anchor2)  
- [nginxのバージョンアップ](#anchor3)  
- [レプリカ数の変更](#anchor4)  
- [まとめ](#anchor5)

<a id="anchor1"></a>

## Docker Desktopでk8sの利用設定

Docker Desktopの設定画面を開いて、  
[Kubernetes]→[Enable Kubernetes]にチェックを入れる→[Apply & Restart]  
とすることで利用可能になります。

![Kubernetes設定](/images/it/container/settingKubernetes.png)

コマンドプロンプトで、`kubectl version`と実行すると、以下のように出れば成功です。

```
# kubectl version
Client Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.7", GitCommit:"1dd5338295409edcfff11505e7bb246f0d325d15", GitTreeState:"clean", BuildDate:"2021-01-13T13:23:52Z", GoVersion:"go1.15.5", Compiler:"gc", Platform:"windows/amd64"}
Server Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.7", GitCommit:"1dd5338295409edcfff11505e7bb246f0d325d15", GitTreeState:"clean", BuildDate:"2021-01-13T13:15:20Z", GoVersion:"go1.15.5", Compiler:"gc", Platform:"linux/amd64"}
```

`kubectl`がk8sのクラスタをコントロールするコマンドです。
デプロイしたり、状態を見たり、削除できます。

<a id="anchor2"></a>

## k8sでnginxのクラスタを起動

とにもかくにも動かしてみましょう。

kubectlではyamlファイルに設定をまとめて、
それを読み込むことができます。

ですので、`nginx.yaml`というファイルを作って
以下のように記載します。

```Yaml
# deploymentの設定。何のイメージをいくつ起動するかなどを設定
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # 起動するコンテナの数
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.12
        ports:
        - containerPort: 80

---
# LoadBalancerの設定。外からのリクエストを受けて作成したコンテナに振り分ける設定
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
      port: 8080  # 外部からアクセスるぽ0と
      targetPort: 80 # コンテナ内のポート　containerPort: 80と一致
```

これで、`nginx.yaml`のあるフォルダで以下を実行してみましょう。

```
kubectl apply -f ./nginx.yaml
```

すると、`http://localhost:8080/`でnginxの画面が出ると思います。


では中身を見ましょう。

k8sの最小単位がPodという単位です。  
クジラの群れという意味があるようです。  
Dockerのロゴマークがクジラだからかな？

それを確認するコマンドが以下で、こんな感じで2つ動いているのが確認できます。
```
# kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-5d47ff8589-4vdxl   1/1     Running   0          8s
nginx-deployment-5d47ff8589-j6r9r   1/1     Running   0          6s
```

`replicas`に設定している値の分だけ実行されます。
この値は監視されていて、以下のコマンドで削除すると、すぐに復活するかと思います。

```
kubectl delete pods ${POD_ID}
```

AGEなどを見ると新しく作られていることが確認できます。
このように2つ起動することを見て、  
起動数が足りないと勝手に起動してくれます。

また、以下のコマンドで全体も見えます。
```
# kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2/2     2            2           11m
```

削除したいときは`service`と`deployment`を削除すればよいです。
```
# kubectl delete service nginx
# kubectl delete deployment nginx-deployment
```

`service`と`deployment`については、どこかでまた書きたいと思います。

<a id="anchor3"></a>

## nginxのバージョンアップ

では、次にnginxのバージョンを1.12から1.13.12に上げてみましょう。

まず、`nginx-deployment.yaml`を作って以下のように記載します。

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
        image: nginx:1.13.12 # バージョンを1.12→1.13.12に変更
        ports:
        - containerPort: 80
```

その後、以下のコマンドを実行します。
```
kubectl apply -f ./nginx-deployment.yaml
```

そして`kubectl get pods`を見ると以下のように、podsが変わっていきます。  
このようにk8sに任せると、停止せずに少しずつバージョンを変えてくれます。  

これを利用して無停止でデプロイとかができるわけですね。すごい！！！

```
nginx-deployment-5d47ff8589-4vdxl   0/1     ContainerCreating   0          1s
nginx-deployment-f77774fc5-2t9jw    1/1     Running             0          25s
nginx-deployment-f77774fc5-7q4h5    1/1     Running             0          23s
↓
nginx-deployment-5d47ff8589-4vdxl   1/1     Running       0          5s
nginx-deployment-5d47ff8589-j6r9r   1/1     Running       0          3s
nginx-deployment-f77774fc5-2t9jw    1/1     Terminating   0          29s
↓
nginx-deployment-5d47ff8589-4vdxl   1/1     Running   0          8s
nginx-deployment-5d47ff8589-j6r9r   1/1     Running   0          6s
```

実際に1.13.12に上がっているかは、画面を開いて開発者ツールのNetworkで  
Response Headerを確認すると`Server: nginx/1.13.12`に変わっていることが確認できます。

<a id="anchor4"></a>

## レプリカ数の変更

次に、replicasを3に変更してみましょう。

```Yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 3 # 2→3に変更
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
```


`kubectl apply -f ./nginx-deployment.yaml`で  
Containerの数が2から3に増えることがわかります。
```
# kubectl apply -f ./nginx-deployment.yaml
# kubectl get pods
nginx-deployment-5d47ff8589-7tbsj   0/1     ContainerCreating   0          3s
nginx-deployment-5d47ff8589-bdzlt   1/1     Running             0          13m
nginx-deployment-5d47ff8589-j6r9r   1/1     Running             0          24m
# kubectl get pods
nginx-deployment-5d47ff8589-7tbsj   1/1     Running   0          5s
nginx-deployment-5d47ff8589-bdzlt   1/1     Running   0          13m
nginx-deployment-5d47ff8589-j6r9r   1/1     Running   0          25m
```

<a id="anchor5"></a>

## まとめ

Docker Desktopを使ってkubectlの動作を勉強することができます。  
yamlファイルに設定をまとめ、読み込ませることで、  
バージョンやレプリカ数などを変更できます。

他にも、オートスケールなどもできるようなので、  
また勉強して紹介しようと思います。