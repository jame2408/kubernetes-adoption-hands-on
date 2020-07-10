# Pod + Services + Volumes

* 選擇任意節點，並為其增加一個標籤

* 建立名稱為 `pod-a` 的 pod，並且有以下幾個條件
  * 只有一個容器，image 為 `nginx:latest`
  * 參考投影片增加環境變數 `NGINX_PORT: 12345` (這讓 nginx listen 的 port 變成 `12345`)
  * 參考投影片增加資源限制 cpu (requests: 100m limits: 150m) 與 memory (requests: 150Mi limits: 150Mi)
  * 參考投影片增加 nodeSelector，將 pod 部署到剛剛選擇的節點上

* 建立名稱為 `pod-b` 的 pod，並且有以下幾個條件
  * 只有一個容器，image 為 `nginx:latest`
  * 參考投影片增加 `liveness` probe 且利用 `httpGet` 方式檢測 `80 port` 的 `/` 路徑
  * 參考投影片增加 `readiness` probe 且利用 `httpGet` 方式檢測 `80 port` 的 `/` 路徑
    
探針的其他設定參數

```yaml
    initialDelaySeconds: 5
    periodSeconds: 5
    successThreshold: 1
    failureThreshold: 3
```

* 建立 4 種 Serive，並且可以連到剛剛建立的兩個 pod
  * ClusterIP
  * LoadBalancer
  * NodePort (完成後告知講師，會給予 Node IP 進行測試)
  * 無頭服務 (反解 DNS 會得到 pod IP)

* 修改 `pod-b` 的 `readiness` 探針的 httpGet 偵測路徑 (需刪掉舊 pod 再重建)
  * 修改 `readiness` 的 `httpGet` 檢測路徑為 `/foobar`
    * readiness 檢測會失敗，此為故意用來模擬服務無法接收新流量的狀況

* 此時上面 4 種服務應該只能接收到來自 `pod-a` 的回應

* 修改 `pod-b` 的 `liveness` 探針的 httpGet 偵測路徑 (需刪掉舊 pod 再重建)
  * 修改 `liveness` 的 `httpGet` 檢測路徑為 `/foobar`
    * readiness 檢測會失敗，此為故意用來模擬服務無法接收新流量的狀況

* 此時 `kubectl` 應該會看到 pod-b 出現 `CrashLoopBackoff` 狀態

# Secret + Volume

* 參考投影片撰寫 Secret YAML 與創建它

Secret 包含一個設定
```yaml
password: hello-world
```

* 參考投影片撰寫 Configmap YAML 與創建它

Configmap 包含兩個設定
```yaml
color.good=purple
color.bad=yellow
```

* 建立一個 Pod，並滿足以下條件
  * image: `nginx:latest`
  * 將 Secret 以`環境變數`方式掛載進去，環境變數名稱為 `MY_PASSWORD`
  * 將 Configmap 以`檔案`方式掛載到 `/tmp/config/color.cfg`
  * 利用 DownwardAPI 將 Pod IP 以`檔案`方式掛載到 `/tmp/config/pod-ip`
