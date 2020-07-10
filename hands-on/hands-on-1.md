# NOTES

`<resource>`: 表示該指令為通用指令，請替換 `<resource>` 為當下練習的 resource name

# Namespace

## 建立專屬 namespace

```bash
$ kubectl create namespace <ns>
```

## 更換現在 namespace

* 預設為 `default`，為避免衝突請切換至剛建立的 ns

```bash
$ kubectl config set-context --current --namespace=<ns>
```

## 確認是否已經切換成功

```bash
$ kubectl config view --minify | grep namespace:
```

## 上標籤

用 CLI 給 namespace 上標籤

```bash
$ kubectl label <resource> <ns> <key>=<value>
```

用 editor 給 namespace 上標籤

```bash
$ kubectl edit <resource> <ns>
```

## 確認標籤

```bash
$ kubectl get <resource> --show-labels
```

## 移除標籤

* 最後有個 `-` 代表移除

```bash
$ kubectl label <resource> <ns> <key>-
```

# Pod

## 用 CLI 創建

* image 使用 `nginx:latest`

```bash
$ kubectl run <pod> --generator=run-pod/v1 --image=<image>

# (不用實作，僅為範例) 假設今天要跑一個除錯用的容器可以加上 `--rm --it`
$ kubectl run <pod> --generator=run-pod/v1 --rm -it --image=<image>｀ - <binary>

# 範例
$ kubectl run foobar --generator=run-pod/v1 --rm -it --image=busybox - sh
```

## 用 `get` 查看 pod 資訊

```bash
$ kubectl get <resource> <name>

# -o wide 看更多資訊
$ kubectl get <resource> <name> -o wide
```

## 用 `describe` 查看 pod 細節 

```bash
$ kubectl describe pod <pod>
```

## 測試 `port-forward` 是否成功

* port-forward 是透過 kubectl 在本地端建立與遠端 pod 的連線

```bash
# nginx 跑在 80 port
$ kubectl port-forward pod/<pod> <local-port>:<container-port>
```

* 利用 browser-preview 打開瀏覽器，連線至 `127.0.0.1:<local-port>` 查看是否成功

因為我們現在的 `IDE 環境是在叢集內`，所以也可以用 `<pod-ip>:<container-port>` 看到網頁

## `exec` 進去 pod 內操作 

* binary: `bash`
* 平常可以利用 exec 進到 pod 內除錯

```bash
$ kubectl exec -it <pod> <binary>

> echo 'Hello World' > /usr/share/nginx/html/index.html
```

更新瀏覽器，應該得到不同結果 

## 查看 pod log

```bash
$ kubectl logs -f <pod> -c <container>
```

## 將 Pod 輸出成 YAML

```bash
$ kubectl get <resource> <pod> -o yaml

# 將輸出變成檔案
$ kubectl get <resource> <pod> -o yaml > <file>.yaml
```

## Apply YAML 檔

* 修改 YAML 加上 Label 與 Annotations
* `apply` 剛修改好的網頁

```bash
$ kubectl apply -f <file>.yaml
```

* 確認標籤是否正確

## Delete pod

```bash
# 用檔案刪除
$ kubectl delete -f <file>.yaml

# 用物件名刪除
$ kubectl delete <resource> <name>
```

# Volume

* 參考投影片使用 `emptyDir` ，建立 2 個各含有 2 個容器的 pod (寫成 `pods.yaml`，並 apply)

* container images:
  * `nginx`: 掛載到 /usr/share/nginx/html
  * `busybox`: 掛載到 /tmp

* `exec` 進去 busybox 容器內，建立一個 `index.html` (可參考上面 `echo` 方式建立))
* 分別針對兩個 pod 做 `port-forward`，並且能夠在網頁上以 `http://127.0.0.1:<local-port>` 看到剛剛建立內容
* 兩個 pod 的網頁內容要不同，後面會用到

# Service

* 給剛建立的 2 個 pod 上標籤
* 參考投影片建立一個 service (寫成 `service.yaml`，並 apply)
* 利用 `get` 查看剛建立的 service 相關資訊
* 利用 `port-forward` 建立本地到遠端 service 的連線，應該要能得到來自 2 個 pod 的網頁結果

```bash
$ kubectl port-forward svc/<svc> <local-port>:<service-port>
```

因為我們現在的 `IDE 環境是在叢集內`，所以也可以用 `<service>:<service-port>` 看到網頁

* 現在修改 service YAML 的 selector 讓他只能連到其中一個 pod
* 和你的同伴測試是否能連到對方的 Service 

應該要能夠透過 `<service>.<ns>:<service-port>` 來連線到對方服務 (cross namespace access)