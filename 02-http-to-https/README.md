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

2. 啟動並設定 Nginx：

```bash
sudo systemctl enable --now nginx
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
echo | openssl s_client -servername chi-networklabs.duckdns.org -connect chi-networklabs.duckdns.org:443 \
  | openssl x509 -noout -issuer -subject -dates
```

請將 `chi-networklabs` 替換成你自己的子網域。

### 成功判斷
- `curl` 回傳 200 或 3xx（視 Nginx 設定）。
- openssl 可看到 Let’s Encrypt 的簽發資訊。

## 實驗 2.4：將 HTTP 強制轉向 HTTPS
### 目的
確保所有 HTTP 請求導向 HTTPS。

### 前置需求
- HTTPS 已可正常連線

### 步驟
1. 如果在 certbot 流程中已選擇「Redirect」，可直接跳到驗證。
2. 若未選擇，請在 Nginx HTTP server block 中加入轉向設定，並重新載入：

```bash
sudo nginx -t
sudo systemctl reload nginx
```

3. 驗證轉向：

```bash
curl -I http://chi-networklabs.duckdns.org
```

### 成功判斷
- `curl` 顯示 301 或 308，並含有 `Location: https://...`。

## 失敗排查
- ACME 驗證失敗：確認 80 對外可達、DNS 解析正確。
- HTTPS 連線失敗：確認 443 防火牆、Nginx 是否載入憑證設定。
- 轉向未生效：確認 Nginx 設定是否已 reload。
