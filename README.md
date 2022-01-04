# 作業：網頁版計算機

Github page 連結： https://jchans.github.io/calc-example/

## 關於這個專案

![PC畫面中的拖拉 demo ](demo_pc_draggable.gif)

1. 這是一個用 React 17 寫的 [網頁版計算機](https://jchans.github.io/calc-example/) ，可以在電腦或手機上使用，在電腦上可以把計算機拖拉到想要的位置。
2. 可以用數字鍵輸入數字，按下運算符號（加、減、乘、除）的按鍵之後，輸入的數字與運算符號會被更新到算式的欄位。
3. 「 C 」鍵會清除目前的輸入的數字或計算後的結果。
4. 「 AC 」鍵會同時清除算式欄與輸入（或結果）的欄位。
5. 可以用退位鍵「 ← 」修改目前輸入的數字或者算式。
6. 按「 = 」鍵會計算目前顯示的算式與輸入數字運算的結果，並且產生本次使用的算式的紀錄。結果大於 2^32 會顯示超過數字上限。
7. 點選計算的紀錄條可以讓算式顯示改為該紀錄的算式，並繼續計算。
8. 可以點選「 x 」刪除個別紀錄條。
9. Refresh 頁面的時候，會清空計算紀錄區，但是在算式會保留最後一次計算的算式。第二次 Refresh 不會保留算式。

![PC畫面中的編輯紀錄 demo ](demo_pc_edit.gif)

### 詳細需求 Spec

1. 請製作一個計算機功能的網頁，包含計算機裡的功能 +, -, \*, /, =, clear(C)
2. 畫面上會有兩個 readonly input box , 一個可以及時顯示「算式」，另一個按下「 = 」功能鍵後可以顯示「結果」
3. 數字計算上限為 2^32 ，超過請顯示超過數字上限
4. 請使用 React 16+
5. 手機版畫面要求：寬度為畫面 100%，高度最大為畫面 50%，若高度超過顯示範圍則可 scroll
6. 電腦版畫面要求：計算機可以被拖拉至畫面任一位置，是否可放到超出螢幕範圍自行決定
7. 設計風格自行決定，可以使用 bootstrap ，tailwind 等框架
8. 請撰寫 README.md 描述 project 及如何使用

### Spec 解讀與製作構想

- 是一個網頁版的計算機，基本功能要有加減乘除，還有清除鍵。
- 兩個顯示欄，一個顯示算式，另一個顯示結果。
  - 不確定的部分，例如：
    - 如果是在普通的計算機，按入「 3 」，「 + 」，「 3 」，「 / 」，「 2 」，結果會是「 3 」，算式會是「 (3 + 3) / 2 」。
    - 如果是在工程計算機，按入「 3 」，「 + 」，「 3 」，「 / 」，「 2 」，會顯示的算式是「 3 + 3 / 2 」，結果會是「 4.5 」。
- 心得：我本來覺得有些描述不是很清楚，但後來覺得以一個實作題目來說，這樣的模糊也蠻有趣的。做普通計算機的使用感的東西與工程計算機使用感的東西我的寫法方向可能會不太一樣。

#### 製作構想

- 參考工程計算機的算式處理的形式製作，使用者列出算式後，按下等於才運算。
- 算式的部分用陣列去儲存使用者輸入的字串（數字或者運算符號），然後再用 JS 內建的方法去把算式轉換回可以運算的 code 。
  - 用字串處理數字是個直覺上有問題的作法。但是目前的 spec 如果想做出顯示算式，還有可能對算式的處理或儲存，可能字串是個還算可行的處理方式。目前的做法要處理小數不精確的問題，可能整個寫法要以 numerical 的方式來寫才行。
- 在沒有設計稿的情況下，參考手機內建的計算機規劃畫面，用 tailwindcss 做。
- 「電腦版畫面要求：計算機可以被拖拉至畫面任一位置。」目前我自己寫可能寫不出這個功能， Google 查詢了一下，找到 react-draggable 這個套件，拖拉的部分就先用這個套件完成。在計算機頁面開啟的時候確認畫面寬度小於 768px 的時候不可拖拉。
- 把網頁版計算機 build 到 gh-page ，用手機與電腦測試，可能找幾個朋友幫忙測試看看，再調整一些使用上不直覺的部分。

#### 一些待處理的問題

- 小數計算與數字顯示問題：
  - 目前的作法：使用 mathjs 的套件，把字串運算後，有效位數為小數點以下 14 位。在 e10 到 e-10 之間直接顯示數字而不使用科學記號。
  - （已取消這個做法，這個 workaround 會產生許多不必要的行為，測試幾次後決定先用 mathjs 處理計算）小數的處理目前不使用其他套件的 workaround 是：找出陣列裡最長的小數位數，最後取值的時候用 toFixed() 取最長的小數的兩倍的位數。最後再用一個函示把結果的字串小數多餘的零處理掉。
- 拖拉與視窗大小改變的調整：
  - 電腦版拖拉的控制可能可以微調得更細緻，讓 iPad 等平板的畫面中也可以正常拖拉與計算，目前在平板上可以拖拉，但是無法計算。
  - 電腦版在調整視窗大小加上拖拉的時候的切版。待研究。偵測視窗大小的設定與反應的方式。
- 實作 Refresh 後保留先前資料的部分，一開始嘗試用有效期限去管理 Local Storage 儲存的資料，因為 useEffect 除理 new Date() 的部分的過多 render 次數，待研究處理方式。目前設定為第一次

## ToDo

- [x] 計算機手機版型外觀，用 tailwindcss 做
- [x] 計算機電腦版拖拉功能，用 [react-draggable](https://www.npmjs.com/package/react-draggable) 套件做
- [x] 基本計算機功能
  - [x] 加、減、乘、除、Clear(C)
- [x] 數字計算結果大於 2^32 顯示「超過數字上限」。
- [x] 算式的更新
- [x] 計算結果的更新
- [x] build 到 gh-page
- [x] 實作 Refresh 網頁之後，會顯示上一次計算的算式。
- [x] README 撰寫說明
