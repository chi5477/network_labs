# Repository Guidelines

## Project Structure & Module Organization
- 根目錄提供總覽與導覽：`README.md`
- 每個實驗子目錄以 `README.md` 說明步驟與驗證方式
- 實驗資料夾命名以遞增數字前綴（例如：`01-dns-basics`）

## README 格式規範（根目錄與子目錄）
### 根目錄 README.md（總覽用）
- 目的：提供整體導覽、快速理解 repo 結構與各實驗入口。
- 固定段落（順序如下）：
  1. `# <Repository Name>`：單一 H1 標題
  2. `## Session 記錄`：可包含常用指令或 session ID（以 code block 表示）
  3. `## 目的`：條列或短文，說明此 repo 的學習/實驗目標
  4. `## 實驗總覽`：逐項列出子目錄與對應說明（例如：`01-dns-basics`）
  5. `## 共通前置條件`：適用於多數實驗的共通需求
  6. `## 注意事項`：安全或操作注意事項
- 書寫規則：
  - 以 Markdown 標題層級清晰呈現（不跳級）
  - 段落精簡、避免冗長敘述
  - 使用相對路徑連結子目錄（例如：`./01-dns-basics/README.md`）

### 子目錄 README.md（實驗/模組用）
- 目的：提供可重現的實驗流程與驗證方式。
- 固定段落（順序如下；可依實驗數量重複「實驗」區塊）：
  1. `# <實驗名稱>`：單一 H1 標題
  2. `## 範圍說明`：本文件涵蓋哪些實驗/版本
  3. `## 語言/版本`：必填，記錄此實驗使用語言與版本（例如：Python 3.12）
  4. `## 實驗 X.Y：<標題>`：每個實驗一個區塊（可重複多個）
     - X 為大實驗編號（對應子目錄，例如 `01-dns-basics` → X=1）
     - Y 為小實驗編號（同一子目錄內遞增，例如 1.1、1.2、1.3）
     - `### 目的`
     - `### 前置需求`
     - `### 步驟`
     - `### 成功判斷`
  5. `## 失敗排查`（選填）：常見問題與排除方式
  6. `## 參考連結`（選填）
- 書寫規則：
  - 指令需以 code block 呈現，並避免含有機密資訊
  - 若有多平台差異，請分段標示（例如：Windows / macOS / Linux）
  - 內容以可重現為優先，避免含糊描述（例如：「自行測試」）

## Build, Test, and Development Commands
- 本 repo 以文件與實驗流程為主，目前沒有統一的 build/test 指令。
- 若要執行範例，請依各子目錄 `README.md` 的步驟進行（例如：`01-dns-basics/README.md`）。

## Coding Style & Naming Conventions
- 文件使用 Markdown，標題層級清楚、段落精簡。
- 行尾格式使用 LF（`\n`）。
- 實驗編碼依子目錄規則（例如：`1.x`、`2.x`、`3.x`）。
- 檔名與路徑保持可讀性，避免使用空白與特殊符號。

## Testing Guidelines
- 目前沒有全域測試框架與覆蓋率要求。
- 若新增測試，請在對應子目錄說明如何執行與驗證。

## Commit & Pull Request Guidelines
- Commit message 採 Conventional Commits：`<type>(optional scope): <description>`
  - 例：`docs: update experiment instructions`
- 建議 PR 內容包含：變更摘要、測試方式、注意事項（若有）。

## Security & Configuration Tips
- 不要提交或分享任何 API key（例如 `OPENAI_API_KEY`）。
- 需要金鑰時以 `.env` 本地設定，避免進入版本控制。
- 實驗操作應限制在工作目錄內，避免影響系統環境。
- 實驗完成後應關閉/停止所有測試服務（例如 Nginx、臨時 HTTP server），避免留下不必要的對外服務。
