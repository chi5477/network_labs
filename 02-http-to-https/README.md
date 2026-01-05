# Lab 02｜HTTP → HTTPS

## 範圍說明
本文件涵蓋 Lab 02 的 HTTP 到 HTTPS 實驗，包含 Nginx 架設 HTTP、使用 Let’s Encrypt（ACME）申請憑證、啟用 HTTPS 與強制轉向。

## 語言/版本
- 環境：Ubuntu on GCE
- 工具：Nginx、certbot（python3-certbot-nginx）

## 實驗 2.1：架設 HTTP 服務（Nginx）
### 目的
啟動可被網域存取的 HTTP 服務，作為 ACME 驗證基礎。

### 前置需求
- DuckDNS 子網域已指向 GCE 公網 IP
- GCE 防火牆允許 TCP 80

### 步驟
1. 安裝 Nginx 與 certbot 套件：

```bash
sudo apt update
sudo apt install -y nginx certbot python3-certbot-nginx
```

2. 啟動 Nginx（本實驗不設定開機自動啟動）：

```bash
sudo systemctl start nginx
```

3. 建立簡單測試頁：

```bash
echo "ok" | sudo tee /var/www/html/index.html
```

4. 從本機驗證 HTTP：

```bash
curl -I http://chi-networklabs.duckdns.org
```

請將 `chi-networklabs` 替換成你自己的子網域。

### 成功判斷
- `curl` 回傳 HTTP 狀態碼（例如 200）。
- 範例回應：

```
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Date: Mon, 05 Jan 2026 09:07:35 GMT
Content-Type: text/html
Content-Length: 3
Last-Modified: Mon, 05 Jan 2026 09:06:57 GMT
Connection: keep-alive
ETag: "695b7f31-3"
Accept-Ranges: bytes
```

## 實驗 2.2：使用 ACME（Let’s Encrypt）申請憑證
### 目的
透過 ACME 驗證取得 HTTPS 憑證。

### 前置需求
- HTTP 80 對外可達（ACME HTTP-01 驗證）
- 網域已正確解析至 VM

### 步驟
1. 申請憑證（使用 Nginx 插件）：

```bash
sudo certbot --nginx -d chi-networklabs.duckdns.org
```

請將 `chi-networklabs` 替換成你自己的子網域。

### 指令說明
- `--nginx`：使用 Nginx 外掛，讓 certbot 直接修改 Nginx 設定並完成部署。
- `-d`：指定要申請憑證的網域名稱（可重複使用多次）。
- 轉向行為：`--redirect` 預設為啟用（install/run 時會自動加入 HTTP → HTTPS 轉向），可用 `--no-redirect` 關閉。

### 成功判斷
- certbot 顯示憑證申請成功，並提示已更新 Nginx 設定。

## 實驗 2.3：啟用 HTTPS 並驗證
### 目的
確認 HTTPS 可正常連線並使用有效憑證。

### 前置需求
- GCE 防火牆允許 TCP 443

### 步驟
1. 以 HTTPS 測試：

```bash
curl -I https://chi-networklabs.duckdns.org
```

2. 檢查憑證資訊（選用）：

```bash
echo | openssl s_client -servername chi-networklabs.duckdns.org -connect chi-networklabs.duckdns.org:443 | openssl x509 -noout -issuer -subject -dates
```

請將 `chi-networklabs` 替換成你自己的子網域。

### 成功判斷
- `curl` 回傳 200 或 3xx（視 Nginx 設定）。
- 範例回應：

```
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Date: Mon, 05 Jan 2026 09:15:22 GMT
Content-Type: text/html
Content-Length: 3
Last-Modified: Mon, 05 Jan 2026 09:06:57 GMT
Connection: keep-alive
ETag: "695b7f31-3"
Accept-Ranges: bytes
```

- openssl 可看到 Let’s Encrypt 的簽發資訊。
- 範例回應：

```
depth=2 C = US, O = Internet Security Research Group, CN = ISRG Root X1
verify return:1
depth=1 C = US, O = Let's Encrypt, CN = E7
verify return:1
depth=0 CN = chi-networklabs.duckdns.org
verify return:1
DONE
issuer=C = US, O = Let's Encrypt, CN = E7
subject=CN = chi-networklabs.duckdns.org
notBefore=Jan  5 08:15:42 2026 GMT
notAfter=Apr  5 08:15:41 2026 GMT
```

### 轉向驗證（HTTP → HTTPS）
本實驗使用 `sudo certbot --nginx -d <your-subdomain>.duckdns.org` 時，已自動寫入轉向設定；以下只需驗證是否成功。

1. 驗證 HTTP 是否已轉向：

```bash
curl -I http://chi-networklabs.duckdns.org
```

請將 `chi-networklabs` 替換成你自己的子網域。

### 轉向成功判斷
- 回應為 301 或 308，且包含 `Location: https://...`。
- 範例回應：

```
HTTP/1.1 301 Moved Permanently
Server: nginx/1.24.0 (Ubuntu)
Date: Mon, 05 Jan 2026 09:27:33 GMT
Content-Type: text/html
Content-Length: 178
Connection: keep-alive
Location: https://chi-networklabs.duckdns.org/
```

## 失敗排查
- ACME 驗證失敗：確認 80 對外可達、DNS 解析正確。
- HTTPS 連線失敗：確認 443 防火牆、Nginx 是否載入憑證設定。
- 轉向未生效：確認是否已有轉向設定（HTTP 回應應為 301/308 並包含 `Location: https://...`）。
