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

## 程式碼(HTML)


### HTML 基本結構

!doctype html：聲明文件類型為 HTML5。

html lang="zh-Hant"：設定網頁語言為繁體中文。

meta charset="utf-8"：設定字符編碼為 UTF-8，支援中文。

meta name="viewport"...：確保在手機或平板上自適應縮放。

title：瀏覽器標籤顯示的標題。

link rel="stylesheet" href="style.css"：連結外部 CSS 樣式表，用來控制頁面外觀。

    <!doctype html>
    <html lang="zh-Hant">
    <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <title>Sudoku - 可變數出題</title>
    <link rel="stylesheet" href="style.css" />
    </head>




### 頁面主容器

body：網頁的主要內容。

.wrap：包住整個網頁內容的容器，方便統一排版或置中。

    <body>
    <div class="wrap">




### 標題與說明區
h1：主標題，顯示「數獨 Sudoku（可調整題目變數）」。

div class="small"：附加說明文字，提示使用者如何操作「難度」「保留格數」與生成題目。

    <div class="top">
      <div>
        <h1>數獨 Sudoku（可調整題目變數）</h1>
        <div class="small">可選難度或直接設定「保留格數」。按 Generate 隨機出題（保證唯一解）。</div>
      </div>





### 操作控制區（Controls）
難度選擇：

提供 Easy / Medium / Hard 選項。

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




### 盤面區（Sudoku Board）
id="board"：給 JavaScript 取得 DOM 元素，動態生成 9×9 格子。

class="board"：套用 CSS 排版（格子大小、間距、邊框）。

aria-label="Sudoku board"：無障礙設計，讓輔助工具知道這是數獨棋盤。

    <div id="board" class="board" aria-label="Sudoku board"></div>






### 狀態與計時器

time：顯示遊戲計時，會由 JS 每秒更新。

message：顯示提示或訊息，例如「已完成」「有錯誤」

    <div class="status">
      <div class="timer">時間：<span id="time">00:00</span></div>
      <div class="small" id="message"></div>
    </div>





### 連結 JavaScript

將 script.js 加入網頁。

這個 JS 檔案負責：

動態生成棋盤格子

玩家輸入數字

即時檢查衝突

實作 Check / Hint / Solve / Reset

計時器功能

數獨解題（backtracking）

    <footer>Generate = 產生新題（唯一解）。Hint = 填一格正確數字。Solve = 顯示完整解答。Reset = 還原當前題目並重置時間。</footer>
    </div>

    <script src="script.js"></script>
    </body>
    </html>

## 程式碼(CSS)

### :root — 全局變數
定義全局 CSS 變數：

cell-size：每個格子的大小

gap：格子間距

board-bg：棋盤背景色

fixed-bg：固定題目格背景色

input-bg：可填格背景色

accent：主要強調色（按鈕、標題）

conflict：衝突格顏色（紅色）

    :root{
      --cell-size:44px;
      --gap:6px;
      --board-bg:#f7f7f7;
    --fixed-bg:#e9e9e9;
    --input-bg:#fff;
    --accent:#2b7cff;
    --conflict:#ff6b6b;
    }
### body 與文字樣式

設定字型、背景色、文字顏色、頁面外邊距

標題 <h1> 使用強調色，字重加粗

    body{
    font-family: "Noto Sans", "Segoe UI", Roboto, Arial;
    background:#f0f4f8;
    margin:24px;
    color:#1b2330;
    }
    h1{margin:0 0 12px;font-weight:700;color:var(--accent)}

### 容器與布局

.wrap：主要內容容器，置中、白底、圓角、陰影

.top：標題與控制區水平排列

.controls：按鈕與選單排列，標籤文字與輸入對齊

    .wrap{
    max-width:980px;
    margin:0 auto;
    background:white;
    padding:18px;
    border-radius:12px;
    box-shadow:0 6px 20px rgba(20,30,60,0.08);
    }
    .top{display:flex;align-items:center;gap:12px;flex-wrap:wrap}
    .controls{margin-left:auto;display:flex;gap:8px;align-items:center}
    .controls label{display:flex;align-items:center;gap:6px}

### 按鈕樣式

主按鈕：藍色、白字、圓角、游標變手型

secondary：灰色次要按鈕

warn：紅色警示按鈕（Reset 或衝突）

    button{background:var(--accent);color:white;border:none;padding:8px 12px;border-radius:8px;cursor:pointer}
    button.secondary{background:#6b7280}
    button.warn{background:var(--conflict)}

### 棋盤與格子

.board：9×9 棋盤，用 CSS Grid 排版

.cell：單格容器，水平垂直置中

.cell input：可輸入格，白底，圓角

.cell.fixed input：固定題目格，加粗、灰底、不可編輯

.cell.conflict input：衝突格紅色背景

    .board{
    margin-top:18px;
    display:inline-grid;
    grid-template-columns:repeat(9, var(--cell-size));
    gap:var(--gap);
    background:var(--board-bg);
    padding:10px;
    border-radius:8px;
    }
    .cell{
    width:var(--cell-size);
    height:var(--cell-size);
    display:flex;
    align-items:center;
    justify-content:center;
    font-size:18px;
    position:relative;
    box-sizing:border-box;
    }
    .cell input{
    width:100%;
    height:100%;
    text-align:center;
    border-radius:6px;
    border:1px solid #d1d5db;
    font-size:18px;
    background:var(--input-bg);
    outline:none;
    }


    .cell.fixed input{
    background:var(--fixed-bg);
    font-weight:700;
    border-color:#bbb;
    cursor:default;
    }
    .cell.conflict input{
    background:var(--conflict);
    color:white;
    border-color:darkred;
    }

### 加粗格線（區分 3×3 宮）

每三列或三欄加粗邊線，清楚分隔 3×3 宮格

    .cell.r3,.cell.r6{border-bottom:2px solid #cbd5e1}
    .cell.c3,.cell.c6{border-right:2px solid #cbd5e1}

### 狀態欄與其他文字

.status：計時器與訊息提示欄

.timer：加粗顯示時間

.small：小字體提示

footer：頁腳說明文字

    .status{margin-top:12px;display:flex;gap:12px;align-items:center;flex-wrap:wrap}
    .timer{font-weight:700}
    .small{font-size:13px;color:#475569}
    footer{margin-top:12px;font-size:13px;color:#64748b}

### 響應式

當螢幕寬度 < 860px：

減小棋盤格尺寸

控制區改為垂直排列

內距縮小，更適合手機或平板

    @media (max-width:860px){
    .wrap{padding:12px}
    :root{--cell-size:36px}
    .controls{flex-direction:column;align-items:flex-end;gap:6px}
    }
## 程式碼(JS)

### 工具函式（Utilities）

deepCopy()：二維陣列（例如 9×9 棋盤）深拷貝，避免修改到原資料。

shuffle()：隨機打亂陣列順序（用 Fisher–Yates 洗牌算法），確保生成的數獨是隨機的。

    function deepCopy(mat){ return mat.map(row => row.slice()); }
    function shuffle(arr){ for(let i=arr.length-1;i>0;i--){ ... } return arr; }

### DOM 元件綁定

boardDiv：顯示 9×9 的數獨格子。

timeSpan：顯示計時。

messageDiv：顯示提示文字。

generateBtn、checkBtn 等：綁定按鈕事件。

    const boardDiv = document.getElementById('board');
    const timeSpan = document.getElementById('time');
    const messageDiv = document.getElementById('message');
    ...

### 全域變數

儲存目前的遊戲狀態。

    let fullSolution = null; // 完整解
    let board = null;        // 使用者當前盤面
    let startBoard = null;   // 題目（不可改）
    let timerInterval = null;
    let seconds = 0;

### 初始題目（Fallback）

若生成題目失敗（例如演算法耗時太久），會使用這組經典的題目作為「預設備案」。

    const fallbackPreset = [
      [5,3,0,0,7,0,0,0,0],
    ...
    ];

### UI 初始化與事件處理

監聽滑桿或下拉選單變化，改變「難度」或「保留格數」。

各按鈕的功能：

Generate → 產生新題目

Check → 檢查錯誤格

Hint → 填一格正確提示

Solve → 顯示完整解答

Reset → 重置題目

    givensRange.addEventListener('input', ()=>{ ... });
    generateBtn.addEventListener('click', ()=>{ generateNewPuzzle(); });
    ...

### 盤面繪製與輸入事件

動態生成 HTML <input> 格子。

固定題目格（startBoard[r][c] != 0）禁用編輯。

可輸入格子監聽：

onInput()：輸入數字更新 board。

onKeyDown()：處理刪除鍵。

根據位置加上 .r3、.c3 樣式 → 畫出粗線區塊分隔。

    function renderBoard(){
    boardDiv.innerHTML = '';
    for(let r=0;r<9;r++){
    for(let c=0;c<9;c++){
      ...
      const input = document.createElement('input');
      ...
    }
    }
    }

### 驗證衝突（行、列、九宮格）

findConflicts()：檢查同列、同行、同宮格有無重複數字。

markConflicts()：在畫面上把有錯格標成紅色（.conflict）。

clearConflicts()：清除紅格與訊息。

    function findConflicts(bd){ ... }
    function clearConflicts(){ ... }
    function markConflicts(){ ... }

### 數獨解題器（Backtracking）

用遞迴回溯法嘗試填 1~9。

若找到解則回傳 true，並填滿整個盤面。

    function solveSudoku(bd){ ... }


### 檢查唯一解（生成時用）

計算盤面有幾個解，超過 limit（通常 2）就停止。

用於確保生成題目時「唯一解」。

    function countSolutions(bd, limit=2){ ... }

### 題目生成（generatePuzzle）

生成完整盤 solution。

打亂所有座標。

隨機挖洞（設 0），每挖一格就檢查「是否仍唯一解」。

若失去唯一性就回復該格。

回傳 {puzzle, solution}。

    function generateFullSolution(){ ... }
    function generatePuzzle(givensTarget, maxAttempts=5000){ ... }

### 高階功能：操作按鈕

generateNewPuzzle()

依難度選擇保留格數（givens），生成題目並顯示。

giveHint()

找出第一個空格，填入正確答案。

solveCurrent()

直接顯示完整解答。

resetToStart()

重置回原題目狀態。

    // === Higher-level actions: generateNewPuzzle, hint, solve, reset ===

    // 🧮 產生新題目
    function generateNewPuzzle(){
    const diff = difficultySel.value;
    let givens = parseInt(givensRange.value,10);

    // 根據選擇難度，決定保留格數（givens）
    if(diff !== 'custom'){
    if(diff==='easy') givens = getRandomInt(36,49);
    if(diff==='medium') givens = getRandomInt(32,35);
    if(diff==='hard') givens = getRandomInt(28,31);
    givensRange.value = givens;
    givensLabel.textContent = givens;
    }

    showMessage('正在產生題目，請稍候（可能需要幾秒）...', false);

    // 用 setTimeout 防止 UI 卡住（生成可能耗時）
    setTimeout(()=>{
    const res = generatePuzzle(givens, 10000); // 挖洞+檢查唯一解
    if(!res){
      // 若生成失敗 → 使用 fallback 題
      showMessage('產生失敗，使用內建題目（fallback）', true);
      startBoard = deepCopy(fallbackPreset);
      fullSolution = deepCopy(fallbackPreset);
      solveSudoku(fullSolution); // 求出解
      board = deepCopy(startBoard);
      renderBoard(); 
      resetTimer(); 
      startTimer(); 
      return;
    }

    // 成功生成題目
    startBoard = res.puzzle;
    fullSolution = res.solution;
    board = deepCopy(startBoard);
    renderBoard();
    resetTimer(); 
    startTimer();
    showMessage(`產生完成（保留 ${givens} 格）。若生成較慢可嘗試降低保留數或改難度。`, false);
    }, 50);
    }

    // 💡 提示功能：自動填一格正確答案
    function giveHint(){
    if(!fullSolution) { showMessage('尚無題目，請先按 Generate', true); return; }
    const copy = deepCopy(board);
    // 找第一個空格填入解答
    for(let r=0;r<9;r++){
    for(let c=0;c<9;c++){
      if(board[r][c]===0){
        board[r][c]=fullSolution[r][c];
        renderBoard();
        showMessage('已填入一格提示（正確）', false);
        return;
      }
    }
    }
    showMessage('盤面已滿或無需提示', false);
    }

    // 🧩 顯示完整解答
    function solveCurrent(){
    if(!fullSolution){ showMessage('尚無題目，請先按 Generate', true); return; }
    board = deepCopy(fullSolution);
    renderBoard();
    showMessage('已顯示完整解答', false); 
    stopTimer();
    }

    // 🔁 重置回原始題目狀態
    function resetToStart(){
    if(!startBoard){ showMessage('尚無題目，請先按 Generate', true); return; }
    board = deepCopy(startBoard);
    renderBoard();
    resetTimer(); 
    startTimer();
    showMessage('已重置到原題', false);
    }

### 完成檢查

是否填滿。

是否無衝突。

    function isComplete(){
    for(... if(board[r][c]===0) return false; ...)
    return findConflicts(board).size===0;
    }

### 計時器功能

每秒累加 seconds。

顯示格式為 mm:ss。

生成新題、重置時重新啟動計時。

    function startTimer(){ ... }
    function stopTimer(){ ... }
    function resetTimer(){ ... }

### 輔助顯示與隨機工具

顯示訊息（紅色表示錯誤）。

生成指定範圍內的亂數。

    function showMessage(txt, err=false){ ... }
    function getRandomInt(a,b){ ... }
### 初始化（最後一段）

預設載入 fallback 題。

自動求解完整解（用於提示與檢查）。

顯示棋盤並啟動計時。

    givensLabel.textContent = givensRange.value;
    startBoard = deepCopy(fallbackPreset);
    fullSolution = deepCopy(fallbackPreset);
    solveSudoku(fullSolution);
    board = deepCopy(startBoard);
    renderBoard();
    resetTimer();
    startTimer();
    showMessage('載入完成，或按 Generate 產生新題。', false);

### 整體架構概觀

    UI事件 → 動態生成/重置盤面
       ↓
    生成完整數獨 → 挖洞 → 檢查唯一解 → 輸出 puzzle + solution
       ↓
    玩家輸入 → 檢查錯誤（findConflicts）
       ↓
    操作按鈕（Hint / Check / Solve / Reset）
       ↓
    計時器 + 訊息提示
![01](
