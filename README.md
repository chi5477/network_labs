# network_labs

## Session 記錄
```
# 可記錄常用指令或 session ID
```

## 目的
本專案用於 DNS 與 HTTPS 相關實驗，透過可重現的步驟理解網域解析、TLS 憑證、憑證信任鏈與 HTTPS 連線建立流程。

## 實驗總覽
- `01-dns-basics`：使用 DuckDNS 建立子網域，設定 A record 並驗證透過網域存取 HTTP 服務（Lab 01）

## 共通前置條件
- 可操作的 Ubuntu on GCE 執行環境
- 具備對外連線的公網 IP
- 可開放對外連線的防火牆規則（HTTP 80）
- 本機可使用 `dig`/`nslookup`/`curl` 等工具

## 注意事項
- DNS 設定可能需要時間生效（取決於 TTL 與快取）。
- 實驗請限制在工作目錄與測試資源範圍內，避免影響正式環境。
- 不要提交或分享任何 API key 或憑證。
