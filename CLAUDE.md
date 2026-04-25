# CLAUDE.md

此文件為 Claude Code（claude.ai/code）在本專案中工作時提供指引。

## 專案結構

實際原始碼位於 `claude_code_treasure_game-initial/`，以下所有指令皆應在該目錄下執行。

## 常用指令

```bash
npm install        # 安裝相依套件（clone 後執行一次）
npm run dev        # 啟動開發伺服器於 http://localhost:3000（自動開啟瀏覽器）
npm run build      # 正式環境建置 → 輸出至 build/
```

本專案未設定測試執行器。

## 架構說明

此遊戲為以 Vite 建置的單頁 React + TypeScript 應用程式，所有遊戲邏輯集中於一個檔案：

**`src/App.tsx`** — 完整遊戲邏輯，使用 `useState` 管理狀態：
- `boxes: Box[]` — 3 個寶箱的陣列，每個包含 `{ id, isOpen, hasTreasure }`
- `score: number` — 目前分數（找到寶藏 +100，骷髏 -50）
- `gameEnded: boolean` — 找到寶藏或所有寶箱開啟後設為 true

遊戲流程：`initializeGame()` 隨機分配寶藏至其中一個寶箱 → 玩家透過 `openBox(boxId)` 點擊寶箱 → 找到寶藏或所有寶箱開啟後遊戲結束。

**`src/components/ui/`** — shadcn/ui 元件庫（Radix UI 原始元件 + Tailwind）。這些為預先產生的檔案，優先使用現有元件而非直接修改。使用 `@/components/ui/` 路徑別名匯入。

**`src/assets/`** — 三張寶箱圖片：`treasure_closed.png`（關閉）、`treasure_opened.png`（寶藏）、`treasure_opened_skeleton.png`（骷髏）。另有 `key.png` 用於自訂滑鼠游標。

**`src/audios/`** — `chest_open.mp3`（一般開啟音效）與 `chest_open_with_evil_laugh.mp3`（骷髏開啟音效）。

## 重要慣例

- 動畫使用 `motion/react`（Motion 函式庫的 React 套件，非 `framer-motion`）。
- 所有樣式使用 Tailwind class；全域 CSS 重置位於 `src/styles/globals.css` 與 `src/index.css`。
- `@` 路徑別名對應 `src/`。
- 函式註解：每個新函式頂端須加上單行摘要註解，說明輸入與輸出參數。
