# 簡易 Rime (鼠鬚管 Squirrel) 個人設定

這份文件記錄 `~/Library/Rime/` 目錄下常用自訂檔案的用途，以及它們如何互相配合，讓 `bopomofo_tw` (注音·台灣正體) 變得更順手。

```text
~/Library/Rime/
            ├── build/
            │   ├── *.bin
            │   └── *.schema.yaml
            ├── terra_pinyin.userdb/
            │   ├── XXX.ldb
            │   ├── XXX.log
            │   ├── CURRENT
            │   ├── LOCK
            │   ├── LOG
            │   ├── LOG.old
            │   └── MANIFEST-XXX
            │
            ├── ＊bopomofo_tw.custom.yaml
            ├── ＊custom_phrase.txt
            ├── ＊terra_pinyin.mine.dict.yaml
            ├── ＊default.custom.yaml
            ├── ＊installation.yaml
            ├── user.yaml
            └── README.md
```

## 📁 資料夾

### 1. `terra_pinyin.userdb`

- **用途**：這是 Rime 的 **「動態記憶大腦」**（用戶二進位資料庫）。
- **說明**：
  - 裡面存放的是你「每天打過的字詞」和「選字頻率權重 (d 值)」。
  - 因為 `bopomofo_tw` 底層共用 `terra_pinyin` (地球拼音) 的字典，所以你的注音打字習慣都會自動存進這個拼音的資料夾裡 。
  - **維護方式**：這是系統自動維護的二進位檔，**絕對不要手動去改裡面的內容**。如果發生死鎖 (卡住不更新)，可以進去把裡面的 `LOCK` 檔刪除，或是整個資料夾刪掉讓它重置。

## 📄 核心設定檔 (.yaml 與 .txt)

### 2. `bopomofo_tw.custom.yaml`

- **用途**：**注音方案的專屬設定檔 (Patch)**。
- **說明**：
  - Rime 預設的注音方案 (`bopomofo_tw.schema.yaml`) 是不能改的。我們透過這個 `.custom.yaml` 去「覆蓋」或「外掛」新功能上去 。
  - **目前掛載的功能**：
    1. 啟用了 `custom_phrase.txt` 功能，讓注音可以支援自訂快捷短語。
    2. 把預設的系統字典，替換成了我們自己寫的擴充字典 `terra_pinyin.mine.dict.yaml`。

### 3. `custom_phrase.txt`

- **用途**：**自訂快捷鍵與文字巨集 (文字快播)**。
- **說明**：
  - 用來設定「打幾個特定按鍵，就直接噴出一長串字或特殊符號」。
  - 例如：設定 `a4` 變成 `/A4GMQVR`，或是設定 `gmail` 變成 `XXX@gmail.com`。
  - 不參與造句，它是一個死板的捷徑，如果夾在長句子裡連打，系統無法根據上下文自動選出這個詞。
  - **注意事項**：
    - 這裡的字**不參與智慧造句**。
    - 格式必須嚴格遵守：`文字 [Tab] 編碼 [Tab] 權重`。**中間絕對不能用空格，必須是真實的 Tab 鍵**。
    - vscode 右下角 `Spaces:4` 是錯的，點擊後改成 **Ident Using Tabs**，⌘+⇧+P 搜尋 `Convert Indentation to Tabs`，這樣才是正確的格式。

模板：

```text
# Rime 自定義短語（繁體中文）
# coding: utf-8
# 路徑：~/Library/Rime/custom_phrase.txt
# 欄位順序：文字 + Tab + 編碼 + Tab + 權重（可選）

# 常用個資
XX@gmail.com	gmail

# 人名

# 帶權重的常用詞（權重越大越靠前）

# 常見符號／特殊字
⌘	cmd
⇧	shift
⌥	option
⌃	ctrl
↩︎	enter

# 其他常用字組（可依你工作習慣調整）

```

### 4. `terra_pinyin.mine.dict.yaml`

- **用途**：**真正的個人專屬擴充字典**。
- **說明**：
  - 用來教輸入法**認識新字、新詞或新發音**。例如：教系統把「丼」唸作 `dong4` (ㄉㄨㄥˋ)。
  - 它會在開頭 `import_tables: - terra_pinyin` 把系統內建的幾萬字大字典繼承過來，然後在下方加上我們自訂的發音。
  - 完全參與造句，系統會把它當作正常的中文詞彙，學會它的詞性，在長句連打時能聰明地自動選出它。
  - **注意事項**：
    - 加進這裡的字**會參與智慧造句**，是系統語言模型的一部分。
    - 格式必須是：`文字 [Tab] 拼音代碼 [Tab] 權重`。多字詞的拼音之間要加半形空格（如：`pu3 pian4`）。

### 5. `default.custom.yaml`

- **用途**：**全域快捷鍵與方案選單設定**。
- **說明**：
  - 用來控制鼠鬚管「整體的行為」，例如：
    - 按 `Ctrl + \` 或 `F4` 叫出方案選單。
    - 設定選單裡要顯示哪些輸入法 (例如只留下 `bopomofo_tw`，把用不到的明月拼音、倉頡等藏起來) 。
    - 控制每頁候選字的數量 `"menu/page_size"` (例如預設 5 個，改成 10 個)。

### 6. `installation.yaml`

- **用途**：**本機安裝識別與同步設定**。
- **說明**：
  - 記錄了這台電腦的專屬 ID (`distribution_code_name` / `installation_id`) 。
  - **最重要功能**：裡面設定了 `sync_dir: "你的 Google Drive 路徑（e.g. path/to/SquirrelBackup）"`，負責把上面提到的這些設定檔 (.yaml 和 .txt) 和打字記憶 (`userdb`) 定期備份到雲端，達成多台電腦間的打字習慣同步。

### 7. `user.yaml`

- **用途**：**用戶目前的狀態記憶**。
- **說明**：
  - Rime 自動生成的狀態檔。記錄你「最後一次選了哪個輸入方案」或是一些 UI 狀態 。
  - **維護方式**：系統自動產生，通常不需要手動編輯。

## 🛠️ 日常使用與維護指南

### 情境 A：我想加一個「地址快捷鍵」或「顏文字」

1. 打開 `custom_phrase.txt`。
2. 加上 `台北市信義區... [Tab] address`。
3. 點擊重新佈署 (Deploy)。

### 情境 B：我遇到一個「Google 查得到注音，但鼠鬚管打不出來」的字

1. 去萌典查那個字的**漢語拼音** (例如 `piàn`)，轉成數字聲調 (`pian4`)。
2. 打開 `terra_pinyin.mine.dict.yaml`。
3. 在最下面加上 `你要的字 [Tab] pian4`。
4. 點擊重新佈署 (Deploy)，等它編譯完成。

### 情境 C：我換了新電腦，怎麼還原？

1. 在新電腦安裝鼠鬚管。
2. 把舊電腦的 `installation.yaml` (確保裡面的 `sync_dir` 路徑是對的) 複製過去。
3. 點擊「同步用戶資料 (Sync)」。
4. 所有的 `.custom.yaml`、自訂字典和 `userdb` 打字習慣就會自動從 Google Drive 載回來並生效。
