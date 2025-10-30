# 軟體工程期中報告-對於零基礎的人，如何利用chatGPT開發自己的APP？
# 11224118林宗翰 11224125吳泰安 11224127錢俊宇

## 智慧數獨 — 提升你的邏輯與專注力
---

### 設計理念
- 挑戰邏輯思維
- 放鬆心情
- 結合紙本數獨與數位便利

---

### 核心功能
- 9×9 盤面顯示

  固定題目格（題目提供數字，無法修改）

  可填格（玩家輸入數字）

  格線明顯區分 3×3 宮格

- 即時輸入與衝突檢查

  只能輸入數字 1–9

  自動檢查行、列、宮格衝突

  衝突格高亮顯示（紅色）

- Check 功能

  檢查玩家填入的數字是否正確

  高亮錯誤格

  提示是否完成整盤數獨

- Hint 功能

  自動填入一格正確數字

  利用內建解題器計算，不依賴外部 API

- Solve 功能

  顯示完整正確解答

  保留原始題目備份，可回復

- Reset 功能

  回復到原始題目

  同時重置計時器

- 計時器

  顯示遊戲已用時間

---


### 總結
- 遊戲與訓練兼具
- 適合放鬆或挑戰思維

## 程式碼

    <!doctype html>
    <html lang="zh-Hant">
    <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <title>Sudoku - 可變數出題</title>
    <link rel="stylesheet" href="style.css" />
    </head>
### HTML 基本結構

!doctype html：聲明文件類型為 HTML5。

html lang="zh-Hant"：設定網頁語言為繁體中文。

meta charset="utf-8"：設定字符編碼為 UTF-8，支援中文。

meta name="viewport"...：確保在手機或平板上自適應縮放。

title：瀏覽器標籤顯示的標題。

link rel="stylesheet" href="style.css"：連結外部 CSS 樣式表，用來控制頁面外觀。


    <body>
    <div class="wrap">
### 頁面主容器

body：網頁的主要內容。

.wrap：包住整個網頁內容的容器，方便統一排版或置中。




    <div class="top">
      <div>
        <h1>數獨 Sudoku（可調整題目變數）</h1>
        <div class="small">可選難度或直接設定「保留格數」。按 Generate 隨機出題（保證唯一解）。</div>
      </div>

### 標題與說明區
1：主標題，顯示「數獨 Sudoku（可調整題目變數）」。

div class="small"：附加說明文字，提示使用者如何操作「難度」「保留格數」與生成題目。

      <div class="controls">
        <label class="small">難度：
          <select id="difficulty">
            <option value="easy">Easy</option>
            <option value="medium" selected>Medium</option>
            <option value="hard">Hard</option>
          </select>
        </label>

        <label class="small">保留格數：
          <input id="givensRange" type="range" min="17" max="60" value="36" />
          <span id="givensLabel">36</span>
        </label>

        <button id="generateBtn" class="secondary">Generate</button>
        <button id="checkBtn">Check</button>
        <button id="hintBtn" class="secondary">Hint</button>
        <button id="solveBtn" class="secondary">Solve</button>
        <button id="resetBtn" class="warn">Reset</button>
      </div>
    </div>

### 操作控制區（Controls）
難度選擇：

<select> 提供 Easy / Medium / Hard 選項。

id="difficulty"：給 JS 取得值。

selected：預設為 Medium。

保留格數：

input type="range"：滑桿，可設定保留題目格數（17~60）。

span id="givensLabel"：顯示目前滑桿的值。

操作按鈕：

Generate：生成新題目。

Check：檢查玩家填入數字是否正確。

Hint：填入一格正確數字。

Solve：顯示完整解答。

Reset：回復到原始題目並重置計時器。



    <div id="board" class="board" aria-label="Sudoku board"></div>

    <div class="status">
      <div class="timer">時間：<span id="time">00:00</span></div>
      <div class="small" id="message"></div>
    </div>

    <footer>Generate = 產生新題（唯一解）。Hint = 填一格正確數字。Solve = 顯示完整解答。Reset = 還原當前題目並重置時間。</footer>
    </div>

    <script src="script.js"></script>
    </body>
    </html>

