Cinderella
===
自動化帳單分析，利用 BeanCount 實現全自動記帳

## Introduction

[Beancount](https://github.com/beancount/beancount) 是一個純文字檔[複式記帳](https://zh.wikipedia.org/wiki/复式簿记)工具。網路上有關複式記帳的特性及好處文章眾多，在此不再贅述。和 Beancount 有關的簡介和使用方式可以參考他的 [文件](https://beancount.github.io/docs/) 和 [CheatSheet](https://beancount.github.io/docs/beancount_cheat_sheet.html)，選用 BeanCount 除了他是開源複式記帳工具，也因為他是純文字檔記錄，單純且容易以程式自動化，且能以 [Fava](https://github.com/beancount/fava) 來視覺化。

**Cinderella** 能夠自動分析銀行的帳戶、信用卡對帳單，和電子發票記錄，**並依照使用者定義的關鍵字分類開銷歸戶**。最後產生 BeanCount 格式檔案。可以先參考 [Fava Demo](https://fava.pythonanywhere.com)。

## 支援的對帳單

目前支援自動分析下列單位對帳單，未來會繼續增加。歡迎直接 PR 或 Feature Request (如果我有空的話)。

| 金融單位         | 帳戶對帳單  | 信用卡     | **Cinderella 識別字**  | 備註 |
| -----------     | ----------- | ----------- | ----------- | ----------- |
| 財政部電子發票    | ✅ (csv)  |➖         | `receipt`         | 每週/月寄的匯整通知  |
| 中華郵政         | ✅ (csv)  |❌         | `post`            | 網銀下載           |   
| 台新銀行(Richart)| ✅ (excel)|✅ (excel) | `taishin`         | APP 申請，需分開 worksheet 到不同檔案 |
| 永豐銀行         | ✅ (csv)* |✅ (csv)   | `sinopac`         | 網銀下載   |
| 中國信託         | 🚀        |🚀         |                   |                   |
| 玉山銀行         | 🚀        |🚀         |                   |                   |
| 國泰世華         | 🚀        |🚀         |                   |                   |

> 🚀: 已規劃支援

> *：需手動複製網銀表格後貼上

> Cinderella 識別字: Cinderella 用於識別一個金融機構


## 使用

### 系統需求

+ `Python`  >= 3.9
+ 目前只在 `macOS Big Sur` 上測試

### 安裝

+ 下載專案
```
git clone https://github.com/daniel0076/Cinderella
```

+ 安裝 Python packages (推薦使用 [pipenv](https://pipenv.pypa.io/en/latest/))
+ 在專案目錄下
```
pipenv install
pipenv shell
```
或 (使用 [virtualenv](https://virtualenv.pypa.io/en/latest/) 等)
```
pip install -r requirements.txt
```

### 準備對帳單

1. 建立 **對帳單資料夾** (例 `statements`，可用任意名稱)
2. 在 **對帳單資料夾** 中按建立類別子資料夾
    + 帳戶對帳單：`bank`
    + 信用卡帳單：`card`
    + 電子發票記錄：`receipt`
3. 下載各金融機構對帳單，按照類別放入上一步建的子資料夾
4. 依對帳單的金融機構，將 **Cinderella 識別字** 加入對帳單的檔名，只要識別字有在檔名中即，例如
    + 發票記錄：`receipt-202108.csv`
    + 中華郵政對帳單：`2021-post.csv`
5. 範例對帳單資料夾
```
statements
|-- card
|   |-- taishin-2021.excel
|
|-- bank
    |-- sinopac-202108.csv
```

6. 往後只要把新的對帳單放入相應資料夾，Cinderella 會批次處理
7. 建立 BeanCount 資料夾
 + 比如 `beans`
 + 可以修改 `main.bean` 去讀取不同資料夾中的 `bean` 檔案
   
### 執行

```
python3 cinderella/main.py PATH_TO_STATEMENTS PATH_TO_BEANCOUNT
fava main.bean  # 啟動 GUI，在 localhost:5000
```
+ 範例


```
python3 cinderella/main.py ./statements ./beans
fava main.bean  # 啟動 GUI，在 localhost:5000
```

### 自訂消費項目分類(歸戶)

Cinderella 能夠依照使用者提供的關鍵字在對帳單項目中比對，將某筆收支歸戶到自定義的類別下

Cinderella 的 [`configs/mappings`](https://github.com/daniel0076/Cinderella/tree/main/Cinderella/configs/mappings) 資料夾下存放了自訂的關鍵字和對應的類別

+ 通用歸類 ([`general.json`](https://github.com/daniel0076/Cinderella/tree/main/Cinderella/configs/mappings/general.json))：收錄了常用的歸類，可照需求自行修改
+ 特用歸類：其它按照 **Cinderella 識別字** 命名的檔案，提供了針對不同金融機構的歸類關鍵字，只用該金融機構對帳單。

**Cinderella 將優先使用特用的歸類項目**

## 後記

Cinderella?

小時候看灰姑娘一直記得 Cinderella 有挑豆子的橋段。不過問了身邊好多朋友，都沒有人對這段有印象。[但 Cinderella 真的有挑豆子的?](https://sites.pitt.edu/~dash/grimm021.html)不論如何，就讓 Cinderella 來幫我們做 bean counting 吧
