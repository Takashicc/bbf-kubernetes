```bash
# podの状態を確認
kubectl get pods --namespace default

# ymlをもとにpodの作成
kubectl apply --filename chapter-04

# リソースの情報を取得することができる
# kubectl get <resource_name>でリソースの情報を取得することができる
❯ kubectl get pod --namespace default
NAME     READY   STATUS    RESTARTS        AGE
myapp    1/1     Running   3 (7m47s ago)   4h23m
myapp2   1/1     Running   3 (7m47s ago)   4h9m
```

Namespaceとは単一のクラスタ内のリソース群を分離するために使うリソース。
通情はNamespaceリソースを作成する必要があるが、default namespaceはクラスタ作成時に自動で作成される。
`kubectl get pod <pod_name> --namespace default`で特定のリソースの情報を取得することができる。

`--output wide`でIPアドレスやNode情報が取得できる。

```bash
❯ kubectl get pod --namespace default --output wide
NAME     READY   STATUS    RESTARTS       AGE     IP           NODE                 NOMINATED NODE   READINESS GATES
myapp    1/1     Running   3 (7m6s ago)   4h22m   10.244.0.5   kind-control-plane   <none>           <none>
myapp2   1/1     Running   3 (7m6s ago)   4h8m    10.244.0.6   kind-control-plane   <none>           <none>
```

`--output yaml`でYAMLファイル形式でリソースの情報を取得する。

Kubernetesクラスタが認識しているKubernetesオブジェクトの内容を参照することで、自分がapplyしたマニフェストとの差分を確認できる。
`kubectl run`だとこれができないが、`kubectl apply`を利用するとこの差分検知が使える。

```bash
# 現在のpodの情報をファイルに出力
kubectl get pod <pod_name> --output yaml --namespace default > pod.yaml

# diffを確認する
diff pod.yaml chapter-04/myapp.yaml
```

`jsonpath`でフィールドを指定してgetする

```bash
❯ kubectl get pod myapp --output jsonpath='{.spec.containers[].image}'
blux2/hello-server:1.0
```

`--output json`でJSON出力することもできる。`jq`と組み合わせることもできる。

`--v`で`kubectl`の出力結果のログレベルを変更する。

```bash
❯ kubectl get pod myapp --v=7 --namespace default
I0205 02:04:29.613317    4084 loader.go:395] Config loaded from file:  C:\Users\takas\.kube\config
I0205 02:04:29.618764    4084 round_trippers.go:463] GET https://127.0.0.1:55017/api/v1/namespaces/default/pods/myapp
I0205 02:04:29.618764    4084 round_trippers.go:469] Request Headers:
I0205 02:04:29.618764    4084 round_trippers.go:473]     Accept: application/json;as=Table;v=v1;g=meta.k8s.io,application/json;as=Table;v=v1beta1;g=meta.k8s.io,application/json
I0205 02:04:29.618764    4084 round_trippers.go:473]     User-Agent: kubectl.exe/v1.31.4 (windows/amd64) kubernetes/a78aa47
I0205 02:04:29.627916    4084 round_trippers.go:574] Response Status: 200 OK in 9 milliseconds
NAME    READY   STATUS    RESTARTS      AGE
myapp   1/1     Running   3 (19m ago)   4h34m
```

`kubectl describe`でリソースの詳細を取得する。Eventの内容が役立つ(一定時間で消える)

`kubectl logs`でコンテナのログを取得する。

```bash
❯ kubectl logs myapp --namespace default
2025/02/04 16:44:57 Starting server on port 8080

# コンテナが複数Pod内にある場合は --container オプションを利用する
❯ kubectl logs myapp --container hello-server --namespace default
2025/02/04 16:44:57 Starting server on port 8080
```

p.88

- status
  - Pending
  - Running
  - Completed
  - Unknown
  - ErrImagePull
  - Error
  - OOMKilled
    - Out Of Memory Error
  - Terminating
