# Vim 模式

Zed 內建一個稱為「Vim 模式」的 Vim 模擬層。在此頁面中，您將學習如何開啟或關閉 Zed 的 Vim 模式、Zed 提供了哪些工具和指令來幫助您導覽和編輯程式碼，以及如何在 Zed 中充分利用 Vim 模式。

您將學會如何：

-   理解 Zed 的 Vim 模式與傳統 Vim 之間的核心差異
-   啟用或停用 Vim 模式
-   在 Vim 模式中充分利用 Zed 的專屬功能
-   自訂 Vim 模式的按鍵綁定
-   設定 Vim 模式的組態

無論您是 Vim 模式的新手，還是希望最佳化 Zed 體驗的資深 Vim 使用者，本指南都將幫助您在 Zed 中駕馭模式編輯的全部威力。

## Zed 的 Vim 模式設計

Vim 模式試圖為 Vim 使用者提供熟悉的體驗：在合理的情況下，它會精確地複製移動 (motion) 和指令 (command) 的行為，並利用 Zed 的專屬功能提供一種「開箱即用」的編輯體驗，無需您進行額外設定。

這包括對語意導覽、多重游標，或其他通常由外掛（如環繞文字）提供的功能的支援。

因此，Zed 的 Vim 模式並非一對一地複製 Vim，而是將 Vim 的模式設計與 Zed 的現代功能相結合，以提供更流暢的體驗。它也是可設定的，因此您可以加入自己的按鍵綁定或覆寫預設值。

### 核心差異

Vim 模式中有四種類型的功能利用了 Zed 的核心功能，這導致了一些行為上的差異：

1.  **移動 (Motions)**：Vim 模式使用 Zed 的語意解析，針對不同語言微調移動的行為。例如，在 Rust 中，使用 `%` 跳至對應的括號時，也適用於管道符號 `|`。在 JavaScript 中，`w` 會將 `$` 視為單字字元。
2.  **可視區塊選取**：Vim 模式使用 Zed 的多重游標來模擬可視區塊選取，使得區塊選取更加靈活。例如，您在區塊選取後插入的任何內容都會在每一行上即時更新，並且您可以隨時新增或移除游標。
3.  **巨集 (Macros)**：Vim 模式使用 Zed 的錄製系統來實現 Vim 巨集。因此，您可以擷取並重播更複雜的動作，例如自動完成。
4.  **搜尋與取代**：Vim 模式使用 Zed 的搜尋系統，因此，正規表示式的語法與 Vim 略有不同。詳情請參閱 [正規表示式差異](#regex-differences) 章節。

> **注意：** Zed 的 Vim 模式基礎應該已經涵蓋了許多使用情境，我們也一直在尋求改進。如果您在工作流程中發現缺少了您所依賴的功能，請[在 GitHub 上提出 issue](https://github.com/zed-industries/zed/issues)。

## 啟用與停用 Vim 模式

當您第一次開啟 Zed 時，您會在歡迎畫面上看到一個核取方塊，讓您可以啟用 Vim 模式。

如果您錯過了，您可以隨時透過開啟指令面板並使用工作區指令 `toggle vim mode` 來開啟或關閉 Vim 模式。

> **注意**：此指令會切換您使用者設定中的以下屬性：
>
> ```json
> {
>   "vim_mode": true
> }
> ```

## Zed 專屬功能

Zed 建立在一個現代化的基礎架構之上，它（其中包括）使用 Tree-sitter 和語言伺服器來理解您正在編輯的檔案內容，並原生支援多重游標。

Vim 模式有幾個「核心 Zed」按鍵綁定，可幫助您充分利用 Zed 的專屬功能集。

### 語言伺服器

以下指令使用語言伺服器來幫助您導覽和重構您的程式碼。

| 指令 | 預設快捷鍵 |
| ---------------------------------------- | ---------------- |
| 跳至定義 | `g d` |
| 跳至宣告 | `g D` |
| 跳至型別定義 | `g y` |
| 跳至實作 | `g I` |
| 重新命名 (變更定義) | `c d` |
| 跳至目前單字的所有參照 | `g A` |
| 在目前檔案中尋找符號 | `g s` |
| 在整個專案中尋找符號 | `g S` |
| 跳至下一個診斷訊息 | `g ]` 或 `] d` |
| 跳至上一個診斷訊息 | `g [` 或 `[ d` |
| 顯示行內錯誤 (懸停) | `g h` |
| 開啟程式碼動作選單 | `g .` |

### Git

| 指令 | 預設快捷鍵 |
| ------------------------------- | ---------------- |
| 跳至下一個 Git 變更 | `] c` |
| 跳至上一個 Git 變更 | `[ c` |
| 展開差異區塊 (diff hunk) | `d o` |
| 切換是否已暫存 | `d O` |
| 暫存並跳至下一個 (在 diff 檢視中) | `d u` |
| 取消暫存並跳至下一個 (在 diff 檢視中) | `d U` |
| 還原變更 | `d p` |

### Treesitter

Treesitter 是一個強大的工具，Zed 用它來理解您的程式碼結構。Zed 提供了改變目前游標位置的移動指令 (motions)，以及可作為動作目標的文字物件 (text objects)。

| 指令 | 預設快捷鍵 |
| ------------------------------- | --------------------------- |
| 跳至下一個/上一個方法 | `] m` / `[ m` |
| 跳至下一個/上一個方法的結尾 | `] M` / `[ M` |
| 跳至下一個/上一個區段 | `] ]` / `[ [` |
| 跳至下一個/上一個區段的結尾 | `] [` / `[ ]` |
| 跳至下一個/上一個註解 | `] /`, `] *` / `[ /`, `[ *` |
| 選取更大範圍的語法節點 | `[ x` |
| 選取更小範圍的語法節點 | `] x` |

| 文字物件 | 預設快捷鍵 |
| ---------------------------------------------------------- | ---------------- |
| 環繞一個類別、定義等 | `a c` |
| 在一個類別、定義等之內 | `i c` |
| 環繞一個函式、方法等 | `a f` |
| 在一個函式、方法等之內 | `i f` |
| 一個註解 | `g c` |
| 一個參數、列表項目等 | `i a` |
| 一個參數、列表項目等 (包含結尾的逗號) | `a a` |
| 環繞一個類似 HTML 的標籤 | `a t` |
| 在一個類似 HTML 的標籤之內 | `i t` |
| 目前縮排層級，以及前後各一行 | `a I` |
| 目前縮排層級，以及前一行 | `a i` |
| 目前縮排層級 | `i i` |

請注意，`[m` 系列移動指令的目標定義與 `af` 定義的邊界相同。`[[` 的目標與 `ac` 定義的目標相同，但如果沒有類別，則也會使用函式。同樣地，`gc` 用於尋找 `[ /`。

函式、類別和註解的定義取決於語言，可以透過在擴充功能中加入 [`textobjects.scm`] 檔案來增加支援。參數和標籤的定義在 Tree-sitter 層級運作，但在解析樹中尋找特定模式，目前無法針對每種語言進行設定。

### 多重游標

這些指令幫助您在 Zed 中管理多重游標。

| 指令 | 預設快捷鍵 |
| ------------------------------------------------------------ | ---------------- |
| 新增一個游標並選取下一個相同的單字 | `g l` |
| 新增一個游標並選取上一個相同的單字 | `g L` |
| 跳過最近選取的單字，並新增下一個 | `g >` |
| 跳過最近選取的單字，並新增上一個 | `g <` |
| 為目前單字的所有出現處新增可視選取 | `g a` |

### 窗格管理

這些指令會開啟新窗格或跳至特定窗格。

| 指令 | 預設快捷鍵 |
| ------------------------------------------ | ------------------ |
| 開啟專案範圍的搜尋 | `g /` |
| 開啟目前的搜尋結果摘錄 | `g <space>` |
| 在分割視窗中開啟目前的搜尋結果摘錄 | `<ctrl-w> <space>` |
| 在分割視窗中跳至定義 | `<ctrl-w> g d` |
| 在分割視窗中跳至型別定義 | `<ctrl-w> g D` |

### 在插入模式中

以下指令可幫助您在不離開插入模式的情況下，叫出 Zed 的自動完成選單、向 GitHub Copilot 請求建議，或開啟行內 AI 助理。

| 指令 | 預設快捷鍵 |
| ---------------------------------------------------------------------------- | ---------------- |
| 開啟自動完成選單 | `ctrl-x ctrl-o` |
| 請求 GitHub Copilot 建議 (需已設定 GitHub Copilot) | `ctrl-x ctrl-c` |
| 開啟行內 AI 助理 (需已設定助理) | `ctrl-x ctrl-a` |
| 開啟程式碼動作選單 | `ctrl-x ctrl-l` |
| 隱藏所有建議 | `ctrl-x ctrl-z` |

### 支援的外掛

Zed 的 Vim 模式包含一些通常由 Vim 生態系中非常熱門的外掛所提供的功能：

-   您可以用 `ys` 環繞文字物件 (yank surround)、用 `cs` 變更環繞字元、用 `ds` 刪除環繞字元。
-   您可以在可視模式下用 `gc` 註解和取消註解選取範圍，在普通模式下用 `gcc` 註解和取消註解。
-   專案面板支援許多模仿 Vim 外掛 `netrw` 的快捷鍵：用 `hjkl` 導覽、用 `o` 開啟檔案、用 `t` 在新分頁中開啟檔案等。
-   您可以在您的按鍵綁定檔中加入按鍵綁定，以在「駝峰式 (camelCase)」命名中導覽。請參閱下方的 [可選的按鍵綁定](#optional-key-bindings) 章節以了解如何操作。
-   您可以使用 `gR` 來執行 [ReplaceWithRegister](https://github.com/vim-scripts/ReplaceWithRegister) 的功能。
-   您可以使用 `cx` 來執行 [vim-exchange](https://github.com/tommcdo/vim-exchange) 的功能。請注意，它在可視模式下沒有預設綁定，但您可以在您的按鍵綁定檔中新增一個（請參閱 [可選的按鍵綁定](#optional-key-bindings) 章節）。
-   您可以使用 [indent wise](https://github.com/jeetsukumaran/vim-indentwise) 外掛的 `[-`, `]-`, `[+`, `]+`, `[=`, `]=`，根據游標位置導覽至相對的縮排深度。
-   您可以使用 AnyQuotes 和 AnyBrackets 文字物件選取被引號或括號包圍的文字。Zed 也提供了 MiniQuotes 和 MiniBrackets，它們基於 [mini.ai](https://github.com/echasnovski/mini.nvim/blob/main/readmes/mini-ai.md) Neovim 外掛提供了另一種選取行為。詳情請參閱下方的 [引號與括號文字物件](#quote-and-bracket-text-objects) 章節。
-   您可以設定 AnyQuotes、AnyBrackets、MiniQuotes 和 MiniBrackets 文字物件，以使用不同的選取策略來選取被引號和括號包圍的文字。詳情請參閱下方的 [Any Bracket 功能](#any-bracket-functionality) 章節。

### Any Bracket 功能

Zed 提供了兩種不同的策略來選取被任何引號或任何括號包圍的文字。這些文字物件**預設並未啟用**，必須在您的按鍵綁定檔中進行設定才能使用。

#### 包含的字元

每種類型的文字物件都適用於特定的字元：

| 文字物件 | 字元 |
| ------------------------ | -------------------------------------------------------------------------------------- |
| AnyQuotes/MiniQuotes | 單引號 (`'`)、雙引號 (`"`)、反引號 (`` ` ``) |
| AnyBrackets/MiniBrackets | 小括號 (`()`)、方括號 (`[]`)、大括號 (`{}`)、角括號 (`<>`) |

「Any」和「Mini」兩種變體都適用於相同的字元集，但它們的選取策略不同。

#### AnyQuotes 和 AnyBrackets (傳統 Vim 行為)

這些文字物件實作了傳統的 Vim 行為：

-   **選取優先順序**：優先尋找最內層（最接近）的引號或括號
-   **備用機制**：若找不到，則退回到目前行
-   **基於字元的匹配**：僅專注於開頭和結尾字元，不考慮語法
-   **與原生 Vim 的相似性**：AnyBrackets 的行為與原生 Vim 中的 `ci<`、`ci(` 等指令相符，包括潛在的邊界情況（例如將 `=>` 中的 `>` 視為結尾分隔符）

#### MiniQuotes 和 MiniBrackets (mini.ai 行為)

這些文字物件實作了 [mini.ai](https://github.com/echasnovski/mini.nvim/blob/main/readmes/mini-ai.md) Neovim 外掛的行為：

-   **選取優先順序**：先搜尋目前行，然後再向外擴展
-   **Tree-sitter 整合**：使用 Tree-sitter 查詢以進行更具語境感知能力的選取
-   **語法感知匹配**：能夠區分真實的括號和在其他語境中相似的字元（例如 `=>` 中的 `>`）

#### 如何選擇

-   如果您符合以下情況，請使用 **AnyQuotes/AnyBrackets**：
    -   偏好傳統的 Vim 行為
    -   希望有基於字元的一致性選取，並優先選取最內層的分隔符
    -   需要與原生 Vim 文字物件高度相符的行為

-   如果您符合以下情況，請使用 **MiniQuotes/MiniBrackets**：
    -   偏好 mini.ai 外掛的行為
    -   希望使用 Tree-sitter 進行更具語境感知能力的選取
    -   偏好在搜尋時以目前行為優先

#### 設定範例

要使用這些文字物件，您需要在您的按鍵綁定檔中新增綁定。以下是一個範例設定，讓您在使用文字物件運算子（`i` 和 `a`）或變更環繞字元（`cs`）時可以使用它們：

```json
{
  "context": "vim_operator == a || vim_operator == i || vim_operator == cs",
  "bindings": {
    // 傳統 Vim 行為
    "q": "vim::AnyQuotes",
    "b": "vim::AnyBrackets",

    // mini.ai 外掛行為
    "Q": "vim::MiniQuotes",
    "B": "vim::MiniBrackets"
  }
}
```

透過此設定，您可以使用如下指令：

-   `cib` - 使用 AnyBrackets 行為變更括號內的內容
-   `ciB` - 使用 MiniBrackets 行為變更括號內的內容
-   `ciq` - 使用 AnyQuotes 行為變更引號內的內容
-   `ciQ` - 使用 MiniQuotes 行為變更引號內的內容

## 指令面板

在 Vim 模式下，您可以用 `:` 開啟 Zed 的指令面板。然後您可以輸入文字來存取任何一般的 Zed 指令。此外，Vim 模式為熱門的 Vim 指令提供了別名，以確保您的肌肉記憶能轉移到 Zed。例如，您可以輸入 `:w` 或 `:write` 來儲存檔案。

下方表格列出了您可以在指令面板中使用的指令。我們將可選字元放在方括號中，表示您可以省略它們。

> **注意**：我們尚未完全模擬 Vim 命令列的全部功能。特別是，指令目前不支援參數。當您發現指令面板中缺少某些功能時，請[在 GitHub 上提出 issue](https://github.com/zed-industries/zed)。

### 檔案與視窗管理

此表格顯示了管理視窗、分頁和窗格的指令。由於指令目前不支援參數，您在儲存或建立新檔案時無法指定檔名。

| 指令 | 描述 |
| -------------- | ---------------------------------------------------- |
| `:w[rite][!]` | 儲存目前檔案 |
| `:wq[!]` | 儲存檔案並關閉緩衝區 |
| `:q[uit][!]` | 關閉緩衝區 |
| `:wa[ll][!]` | 儲存所有開啟的檔案 |
| `:wqa[ll][!]` | 儲存所有開啟的檔案並關閉所有緩衝區 |
| `:qa[ll][!]` | 關閉所有緩衝區 |
| `:[e]x[it][!]` | 關閉緩衝區 |
| `:up[date]` | 儲存目前檔案 |
| `:cq` | 完全退出 (關閉所有執行中的 Zed 實體) |
| `:vs[plit]` | 垂直分割窗格 |
| `:sp[lit]` | 水平分割窗格 |
| `:new` | 在水平分割視窗中建立新檔案 |
| `:vne[w]` | 在垂直分割視窗中建立新檔案 |
| `:tabedit` | 在新分頁中建立新檔案 |
| `:tabnew` | 在新分頁中建立新檔案 |
| `:tabn[ext]` | 前往下一個分頁 |
| `:tabp[rev]` | 前往上一個分頁 |
| `:tabc[lose]` | 關閉目前分頁 |
| `:ls` | 顯示所有緩衝區 |

> **注意：** `!` 字元用於強制執行指令，而不會在覆寫檔案前儲存變更或提示。

### Ex 指令

這些 ex 指令會開啟 Zed 的各種面板和視窗。

| 指令 | 描述 |
| ---------------------------- | ---------------- |
| `:E[xplore]` | 開啟專案面板 |
| `:C[ollab]` | 開啟協作面板 |
| `:Ch[at]` | 開啟聊天面板 |
| `:A[I]` | 開啟 AI 面板 |
| `:G[it]` | 開啟 Git 面板 |
| `:D[ebug]` | 開啟除錯面板 |
| `:No[tif]` | 開啟通知面板 |
| `:fe[edback]` | 開啟回饋視窗 |
| `:cl[ist]` | 開啟診斷視窗 |
| `:te[rm]` | 開啟終端機 |
| `:Ext[ensions]` | 開啟擴充功能視窗 |

### 導覽診斷訊息

這些指令用於導覽診斷訊息。

| 指令 | 描述 |
| ------------------------ | ------------------------------ |
| `:cn[ext]` 或 `:ln[ext]` | 前往下一個診斷訊息 |
| `:cp[rev]` 或 `:lp[rev]` | 前往上一個診斷訊息 |
| `:cc` 或 `:ll` | 開啟錯誤頁面 |

### Git

這些指令與版本控制系統 Git 互動。

| 指令 | 描述 |
| --------------- | ------------------------------------------------------- |
| `:dif[fupdate]` | 檢視游標下的差異 (普通模式下的 `d o`) |
| `:rev[ert]` | 還原游標下的差異 (普通模式下的 `d p`) |

### 跳轉

這些指令會跳轉到檔案中的特定位置。

| 指令 | 描述 |
| ------------------- | ----------------------------------- |
| `:<數字>` | 跳至指定行號 |
| `:$` | 跳至檔案結尾 |
| `:/foo` 和 `:?foo` | 跳至下一個/上一個匹配 foo 的行 |

### 取代

此指令用於取代文字。它模擬了 Vim 中的 substitute 指令。substitute 指令使用正規表示式，而 Zed 的語法與 Vim 略有不同。您可以在下方的 [正規表示式差異](#regex-differences) 章節中了解更多關於 Zed 的語法。Zed 只會取代目前行中搜尋樣式的第一次出現。若要取代所有匹配項，請加上 `g` 旗標。

| 指令 | 描述 |
| ----------------------- | --------------------------------- |
| `:[range]s/foo/bar/[g]` | 將 foo 的實例取代為 bar |

### 編輯

這些指令幫助您編輯文字。

| 指令 | 描述 |
| ----------------- | ------------------------------------------------------- |
| `:j[oin]` | 合併目前行 |
| `:d[elete][l][p]` | 刪除目前行 |
| `:s[ort] [i]` | 排序目前選取範圍 (加上 i 則不分大小寫) |
| `:y[ank]` | 抽出 (複製) 目前選取範圍或整行 |

### Set

這些指令會對目前緩衝區進行本機設定修改。

| 指令 | 描述 |
| ------------------------------- | --------------------------------------------------------------------------------------------- |
| `:se[t] [no]wrap` | 比視窗寬度更長的行將會換行，並在下一行繼續顯示 |
| `:se[t] [no]nu[mber]` | 在每行前面印出行號 |
| `:se[t] [no]r[elative]nu[mber]` | 將顯示的行號變更為相對於游標的位置 |

### 指令助記符

由於任何 Zed 指令都可用，您可能會發現記住執行正確指令的助記符很有幫助。例如：

-   `:diffs` 代表 "toggle all hunk diffs" (切換所有 hunk 差異)
-   `:cpp` 代表 "copy path to file" (複製檔案路徑)
-   `:crp` 代表 "copy relative path" (複製相對路徑)
-   `:reveal` 代表 "reveal in finder" (在 Finder 中顯示)
-   `:zlog` 代表 "open zed log" (開啟 Zed 日誌)
-   `:clank` 代表 "cancel language server work" (取消語言伺服器工作)

## 自訂按鍵綁定

在本節中，我們將學習如何自訂 Zed 的 Vim 模式的按鍵綁定。您將學會：

-   如何為您的新按鍵綁定選擇正確的情境 (context)。
-   適用於 Vim 模式按鍵綁定的實用情境。
-   可自訂以提高生產力的常見按鍵綁定。

### 選擇正確的情境

只有當 `"context"` 屬性與您在編輯器中的位置匹配時，Zed 的按鍵綁定才會被評估。例如，如果您將按鍵綁定加入到 `"Editor"` 情境中，它們只會在您編輯檔案時生效。如果您將按鍵綁定加入到 `"Workspace"` 情境中，它們將在 Zed 的任何地方都有效。以下是一個在編輯檔案時儲存的按鍵綁定範例：

```json
{
  "context": "Editor",
  "bindings": {
    "ctrl-s": "file::Save"
  }
}
```

情境是巢狀的，所以當您編輯檔案時，情境是 `"Editor"`，它位於 `"Pane"` 情境之內，而 `"Pane"` 又位於 `"Workspace"` 情境之內。這就是為什麼您加入到 `"Workspace"` 情境的任何按鍵綁定在您編輯檔案時都會有效。範例如下：

```json
// 這個按鍵綁定在您編輯檔案時會生效。它作為 workspace: save 指令內建於 Zed。
{
  "context": "Workspace",
  "bindings": {
    "ctrl-s": "file::Save"
  }
}
```

情境是表達式。它們支援布林運算子，如 `&&` (and) 和 `||` (or)。例如，您可以使用 `"Editor && vim_mode == normal"` 這個情境來建立只在您編輯檔案*且*處於 Vim 的普通模式時才有效的按鍵綁定。

Vim 模式在 `"Editor"` 情境中加入了幾個情境：

| 運算子 | 描述 |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| VimControl | 表示 Vim 按鍵綁定應生效。目前是 `vim_mode == normal \|\| vim_mode == visual \|\| vim_mode == operator` 的別名，但定義未來可能會改變 |
| vim_mode == normal | 普通模式 |
| vim_mode == visual | 可視模式 |
| vim_mode == insert | 插入模式 |
| vim_mode == replace | 取代模式 |
| vim_mode == waiting | 等待任意按鍵 (例如，在輸入 `f` 或 `t` 之後) |
| vim_mode == operator | 等待另一個綁定觸發 (例如，在輸入 `c` 或 `d` 之後) |
| vim_operator | 除非 `vim_mode == operator`，否則設定為 `none`。若為 operator 模式，則設定為目前運算子的預設按鍵綁定 (例如，輸入 `d` 後，`vim_operator == d`) |

> **注意**：情境一次只匹配一個層級。因此，可以使用 `"Editor && vim_mode == normal"` 表達式，但 `"Workspace && vim_mode == normal"` 永遠不會匹配，因為我們在 `"Editor"` 層級設定了 Vim 情境。

### 適用於 Vim 模式按鍵綁定的實用情境

這裡有一個包含實用 Vim 模式情境的範本，可幫助您自訂 Vim 模式的按鍵綁定。您可以複製它並整合到您的使用者按鍵綁定檔中。

```json
[
  {
    "context": "VimControl && !menu",
    "bindings": {
      // 如果您希望按鍵綁定在普通模式和可視模式下都有效，請放在此處。
    }
  },
  {
    "context": "vim_mode == normal && !menu",
    "bindings": {
      // "shift-y": ["workspace::SendKeystrokes", "y $"] // 使用 neovim 的 yank 行為：抽出至行尾。
    }
  },
  {
    "context": "vim_mode == insert",
    "bindings": {
      // "j k": "vim::NormalBefore" // 在插入模式中，讓 jk 逃脫至普通模式。
    }
  },
  {
    "context": "EmptyPane || SharedScreen",
    "bindings": {
      // 如果您希望按鍵綁定在沒有編輯器存在時也有效，
      // (除了上述情境之外) 請放在此處。
      // "space f": "file_finder::Toggle"
    }
  }
]
```

> **注意**：如果您想模擬 Vim 的 `map` 指令 (`nmap` 等)，您可以在正確的情境中使用 `workspace::SendKeystrokes` 動作。

### 可選的按鍵綁定

預設情況下，您可以使用像 `ctrl+w` 接著 `hjkl` 其中一個按鍵的快捷鍵，在編輯器中開啟的不同檔案之間分別向左、下、上、右移動。

但您不能使用相同的快捷鍵在所有編輯器停駐區（終端機、專案面板、助理面板...）之間移動。如果您想使用相同的快捷鍵導覽到停駐區，可以將以下按鍵綁定加入到您的使用者按鍵綁定檔中。

```json
{
  "context": "Dock",
  "bindings": {
    "ctrl-w h": "workspace::ActivatePaneLeft",
    "ctrl-w l": "workspace::ActivatePaneRight",
    "ctrl-w k": "workspace::ActivatePaneUp",
    "ctrl-w j": "workspace::ActivatePaneDown"
    // ... 或其他按鍵綁定
  }
}
```

子單字移動 (Subword motion) 功能，可讓您在 camelCase 或 snake_case 中導覽和選取個別單字，預設並未啟用。要啟用它，請將這些綁定加入到您的按鍵綁定檔中。

```json
{
  "context": "VimControl && !menu && vim_mode != operator",
  "bindings": {
    "w": "vim::NextSubwordStart",
    "b": "vim::PreviousSubwordStart",
    "e": "vim::NextSubwordEnd",
    "g e": "vim::PreviousSubwordEnd"
  }
}
```

Vim 模式內建了在普通模式下環繞選取範圍的快捷鍵 (`ys`)，但在可視模式下沒有新增環繞的快捷鍵。預設情況下，`shift-s` 會取代選取範圍（刪除文字並進入插入模式）。要在可視模式下使用 `shift-s` 來新增環繞，您可以將以下物件加入到您的按鍵綁定檔中。

```json
{
  "context": "vim_mode == visual",
  "bindings": {
    "shift-s": ["vim::PushAddSurrounds", {}]
  }
}
```

在非模式編輯器中，游標導覽在移動超過行尾時通常會換行。然而，Zed 預設情況下處理此行為的方式與 Vim 完全相同：游標在行邊界處停止。如果您偏好游標在行之間換行，請覆寫這些按鍵綁定：

```json
// 在 VimScript 中，這會像這樣：
// set whichwrap+=<,>,[,],h,l
{
  "context": "VimControl && !menu",
  "bindings": {
    "left": "vim::WrappingLeft",
    "right": "vim::WrappingRight",
    "h": "vim::WrappingLeft",
    "l": "vim::WrappingRight"
  }
}
```

[Sneak 移動](https://github.com/justinmk/vim-sneak) 功能可讓您快速導覽至文字中的任意兩個字元序列。您可以透過將以下按鍵綁定新增到您的按鍵綁定檔來啟用它。預設情況下，`s` 鍵對應到 `vim::Substitute`。新增這些綁定將會覆寫該行為，因此請確保此變更符合您的工作流程偏好。

```json
{
  "context": "vim_mode == normal || vim_mode == visual",
  "bindings": {
    "s": "vim::PushSneak",
    "shift-s": "vim::PushSneakBackward"
  }
}
```

[vim-exchange](https://github.com/tommcdo/vim-exchange) 功能在可視模式下沒有預設綁定，因為 `shift-x` 綁定與可視模式的預設 `shift-x` 綁定 (`vim::VisualDeleteLine`) 衝突。要指派預設的 vim-exchange 綁定，請將以下按鍵綁定新增到您的按鍵綁定檔中：

```json
{
  "context": "vim_mode == visual",
  "bindings": {
    "shift-x": "vim::Exchange"
  }
}
```

### 恢復常見的文字編輯按鍵綁定

如果您在 Linux 或 Windows 上使用 Vim 模式，您可能會發現它覆寫了您不可或缺的按鍵綁定：`ctrl+v` 貼上、`ctrl+f` 搜尋等。您可以透過將此資料複製到您的按鍵綁定檔中來恢復它們：

```json
{
  "context": "Editor && !menu",
  "bindings": {
    "ctrl-c": "editor::Copy",          // vim 預設：返回普通模式
    "ctrl-x": "editor::Cut",           // vim 預設：遞減
    "ctrl-v": "editor::Paste",         // vim 預設：可視區塊模式
    "ctrl-y": "editor::Undo",          // vim 預設：向上捲動一行
    "ctrl-f": "buffer_search::Deploy", // vim 預設：下一頁
    "ctrl-o": "workspace::Open",       // vim 預設：返回上一個位置
    "ctrl-s": "workspace::Save",       // vim 預設：顯示簽名
    "ctrl-a": "editor::SelectAll",     // vim 預設：遞增
  }
},
```

## 變更 Vim 模式設定

您可以變更以下設定來修改 Vim 模式的行為：

| 屬性 | 描述 | 預設值 |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| default_mode | 啟動時的預設模式。可選 "normal", "insert", "replace", "visual", "visual_line", "visual_block", "helix_normal"。 | "normal" |
| use_system_clipboard | 決定如何使用系統剪貼簿：<br><ul><li>"always": 用於所有操作</li><li>"never": 僅在明確指定時使用</li><li>"on_yank": 用於抽出 (yank) 操作</li></ul> | "always" |
| use_multiline_find | 已棄用 | |
| use_smartcase_find | 若為 `true`，當目標字母為小寫時，`f` 和 `t` 移動不分大小寫。 | false |
| toggle_relative_line_numbers | 若為 `true`，行號在普通模式下是相對的，在插入模式下是絕對的，讓您兩全其美。 | false |
| custom_digraphs | 一個物件，可讓您新增自訂的二合字母 (digraph)。範例請見下方。 | {} |
| highlight_on_yank_duration | 抽出 (yank) 時的醒目提示動畫持續時間（毫秒）。設為 `0` 可停用。 | 200 |

以下是一個為殭屍表情符號新增二合字母的範例。這讓您可以輸入 `ctrl-k f z` 來插入一個殭屍表情符號。您可以隨意新增任意數量的二合字母。

```json
{
  "vim": {
    "custom_digraphs": {
      "fz": "🧟‍♀️"
    }
  }
}
```

以下是這些設定變更後的範例：

```json
{
  "vim": {
    "default_mode": "insert",
    "use_system_clipboard": "never",
    "use_smartcase_find": true,
    "toggle_relative_line_numbers": true,
    "highlight_on_yank_duration": 50,
    "custom_digraphs": {
      "fz": "🧟‍♀️"
    }
  }
}
```

## 適用於 Vim 模式的實用核心 Zed 設定

這裡有一些通用的 Zed 設定，可以幫助您微調您的 Vim 體驗：

| 屬性 | 描述 | 預設值 |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------- |
| cursor_blink | 若為 `true`，游標會閃爍。 | `true` |
| relative_line_numbers | 若為 `true`，左側邊欄中的行號相對於游標。 | `false` |
| scrollbar | 控制捲軸顯示的物件。設定為 `{ "show": "never" }` 可隱藏捲軸。 | `{ "show": "auto" }` |
| scroll_beyond_last_line | 若設定為 `"one_page"`，允許捲動超過最後一行最多一頁。設定為 `"off"` 可防止此行為。 | `"one_page"` |
| vertical_scroll_margin | 捲動時在游標上方或下方保留的行數。設定為 `0` 可讓游標垂直移動到螢幕邊緣。 | `3` |
| gutter.line_numbers | 控制邊欄中行號的顯示。將 `"line_numbers"` 屬性設定為 `false` 可隱藏行號。 | `true` |
| command_aliases | 定義指令面板中指令別名的物件。您可以用它來為常用的指令定義快捷名稱。範例請見下方。 | `{}` |

以下是這些設定變更後的範例：

```json
{
  // 停用游標閃爍
  "cursor_blink": false,
  // 使用相對行號
  "relative_line_numbers": true,
  // 隱藏捲軸
  "scrollbar": { "show": "never" },
  // 防止緩衝區捲動超過最後一行
  "scroll_beyond_last_line": "off",
  // 允許游標到達螢幕邊緣
  "vertical_scroll_margin": 0,
  "gutter": {
    // 完全停用行號：
    "line_numbers": false
  },
  "command_aliases": {
    "W": "w",
    "Wq": "wq",
    "Q": "q"
  }
}
```

`command_aliases` 屬性是一個單一物件，將按鍵或按鍵序列對應到 Vim 模式指令。上面的範例定義了多個別名：`W` 對應 `w`，`Wq` 對應 `wq`，`Q` 對應 `q`。

## 正規表示式差異

Zed 使用與 Vim 不同的正規表示式引擎。這意味著在某些情況下，您必須使用不同的語法。以下是最常見的差異：

-   **捕獲群組 (Capture groups)**：Vim 使用 `\(` 和 `\)` 來表示捕獲群組，在 Zed 中則是 `(` 和 `)`。反過來說，在 Vim 中，`(` 和 `)` 代表字面上的括號，但在 Zed 中必須將它們逸出為 `\(` 和 `\)`。
-   **匹配項 (Matches)**：在取代時，Vim 使用反斜線字元後接數字來表示匹配的捕獲群組。例如，`\1`。Zed 則使用錢字號。所以，當您在 Vim 中使用 `\0` 表示整個匹配項時，在 Zed 中語法是 `$0`。對於有編號的捕獲群組也是一樣：Vim 中的 `\1` 在 Zed 中是 `$1`。
-   **全域選項 (Global option)**：在 Vim 中，正規表示式搜尋預設只匹配一行的第一次出現，您需要在查詢結尾加上 `/g` 來尋找所有匹配項。在 Zed 中，正規表示式搜尋預設是全域的。
-   **大小寫敏感度 (Case sensitivity)**：Vim 使用 `/i` 來表示不分大小寫的搜尋。在 Zed 中，您可以在樣式開頭寫 `(?i)`，或使用快捷鍵 {#kb search::ToggleCaseSensitive} 來切換大小寫敏感度。

> **注意**：為了幫助過渡，當您輸入 Vim 風格的 substitute 指令，如 `:%s//` 時，指令面板會自動為您修正括號和取代群組。因此，Zed 會將 `%s:/\(a\)(b)/\1/` 轉換為搜尋 `(a)\(b\)` 並取代為 `$1`。

有關 Zed 的正規表示式引擎支援的完整語法，請參閱 [regex crate 文件](https://docs.rs/regex/latest/regex/#syntax)。
