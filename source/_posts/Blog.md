---
title: 使用Hexo搭配GitHub架設個人部落格
date: 2021-8-27
tags: 
    - 個人部落格
    - 網站架設
categories: 其他
mathjax: true
---
本網頁就是利用github page架設的部落格，使用Hexo搭配Next主題。
暑假常常寫題，都幾乎紀錄在Hackmd上，如果能有屬於自己的部落格、用自己的網址，感覺超酷！來自己架設一個試試看！

## 網域種類

架設網站有好多種方法，有時候是看題解看到了很多的網站XD，上網查了資料以及很多人的部落格，發現寫coding部落格的人的網站大多分成以下四類：

1. [Blogger](https://www.blogger.com/)
2. [Hexo](https://hexo.io/)
3. [GitHub Pages](https://pages.github.com/)
4. [WordPress.org](https://wordpress.org/)

<!--more-->

每一種部落格都有優勢與劣勢，就看使用者覺得哪一個平台使用起來比較方便！在經過多方嘗試之下，最後我是選擇了使用 Hexo 搭配 Github 來架設我的網站，以下來分析一下我個人在架設的過程中，以一個新手的角度看各個平台使用上的優缺點！

| 網站比較    | 優點 | 缺點 |
| ----------- | ---- | ---- |
| **Blogger**|  1. 介面清楚，操作方便<br>2. 一鍵預覽及發布，快速查看文章成果<br>3. 免費！|1. 不支援MarkDown語法<br>2. 對數學式、程式碼支援不便利    |
| **Hexo**   | 1. 支援MarkDown語法<br>2. Mac終端機操作方便<br>3. 免費！|1. 純程式碼呈現，較不直覺<br>2. 修改過程中如果程式碼出錯，造成整個網站掛掉|
|**GitHub Pages** |1. 支援MarkDown語法<br>2. 進行版本控制<br>3. 免費！|1. 單純使用的話操作、修改不方便<br>2. Git操作有一定難度|
|**WordPress com** |1. 跟Blogger相近，操作簡單易上手<br>2. 快速查看文章長相   |1. 不支援Markdown<br>2. 免費版限制多、模板不漂亮   |
|**WordPress org** | 太貴不考慮 | 上網查必須要先付費才能開始架設<br>費用好像一年3000以上|

經過多方嘗試（花了我好多天），出產了一堆失敗品，最後還是用 Hexo 搭配 GitHub 來架設一個算滿意的網站（至少看起來不錯XD）。以下是我的失敗品網站：

1. [Blogger](https://peienwu1216.blogspot.com/?zx=60407725c2514b18)
2. [WorldPress](https://peienwu.wordpress.com/)
3. Github的我已經刪掉了

## Hexo 安裝

之前遇上很多軟體對Mac都不友善，不過Hexo卻是對Mac的用戶比較友善的工具。只要在終端機輸入以下命令就可以安裝Hexo：

```
npm install -g hexo-cli
```

詳細的安裝步驟可以參考[官方網站](https://hexo.io/docs/)，或是網路上的[教學影片](https://youtu.be/jOJI9ekTzK8)就可以一步一步的把網站建立起來！

在這過程中，我發現影片上有使用到VS Code，於是我就把它也載下來了。用起來感覺VS Code 對專案製作很有幫助，可以同步的在各個檔案之間進行修改，蠻實用的一個IDE！
![](https://i.imgur.com/eIiT0Pb.png)

## GitHub同步

將 Hexo 裡面的檔案推送到 Git Hub 裡面的一個Repository中，利用它來將網頁的內容進行發布。在每一次本地修改完成之後，如果本地測試可行，就可以將檔案推送到Github中，在Github將網站進行發布。我使用來架設部落格的[Repository在此](https://github.com/peienwu/peienwu.github.io)。

```
git add .                //加入
git commit -m "xxx"      //提交
git push                 //推入
git push -f              //強制推入（聽說很危險）
```

在提交的後面的字串表示當前版本的一個提示，例如我做了什麼修改就可以顯示在上面，讓別人知道這份檔案被更改的情況。下圖就是在每一次推入時附加的修改資訊，讓我自己可以看到檔案被更改以及提交的狀況!

![](https://i.imgur.com/kBpX3gp.png)

Git真是博大精深，版本控制真的有難度，我只會簡單的修改以及推入（之後找時間可以學一下）。

## 外掛安裝

做到這一個步驟已經花了我快三小時，出現的是類似以下的畫面：

![](https://i.imgur.com/a1zD8Qz.png)

這是 Hexo 預設的樣板，感覺還不錯，不過我的目標是使用另外外掛的模板，看起來比較漂亮XD。於是我就找了[Next主題](https://github.com/theme-next/hexo-theme-next)的外掛來安裝，這個模板就感覺舒服很多。這個模板使用的大陸部落格很多，也有一些其他的競程部落格使用了這個主題！

![](https://i.imgur.com/aP8mwrQ.png)

### Hexo NexT 美化

上網查關鍵字其實有很多美化的資訊可以參考，不論是字體或版面配置都有很多種選項可以選擇。最一開始是設計版面的部分，將網站總共分五個子類別，分別是首頁、檔案、類別、標籤以及關於，這五個經典的配置。接下來就是個人資訊的設定，加入自己的圖片以及說明：
![](https://i.imgur.com/TXykwfE.png)

以及在網頁瀏覽標籤上方設定icon：
![](https://i.imgur.com/Y2lE6Qp.png)

當然最重要的就是文章內容，因為是使用Markdown語法，剛好有幾篇筆記就丟上去看看。當然還有很多的優化以及美化，如果有什麼需要改進的地方要跟我說喔！

### Disqus 留言板功能

在每一篇文章下方加入留言板功能，感覺還蠻酷的！

![](https://i.imgur.com/DAUAz3R.png)

### Google Analytics 追蹤分析

這是酷東西，使用google 帳號生成一組代碼之後，把一些它提供的程式碼埋到網站中，它可以即時的統計網站的瀏覽狀況，以及觸及、感興趣的主題等等。**假設**未來瀏覽的人次比較多的時候（可能根本沒人會看XDD），還可以用它來分析哪一種文章最受人歡迎、哪一種文章最冷門之類的！

![](https://i.imgur.com/3lPv0uW.png)

### 購買個人網址

昨天終於買到了自己名字的網址了！新的網址名稱是：peienwu.com！在**Godaddy** 上用NT570元買了一年這個網址的使用權限，買完之後到**CloudFlare**設定好Dns之類的，讓這個網址可以指向一個IP位址（這其中的原理有點複雜）。

設定完之後，發現他一直出現錯誤訊息，點開網址也是不可使用的，差點昏倒ww。晚上處理不了了，隔天一早起床就開始著手處理中這個問題，把檔案刪刪改改，終於成功讓GitHub跳出以下訊息：

![](https://i.imgur.com/Zoe8Aes.png)

很討厭的，他沒有https，也就是鎖頭，在這種情況下，瀏覽器就會自動設置為<font color="#f00">不安全</font>的網站，接下來早上的時間都拿去處理這個問題。在設定一些SSH以及https的安全性設定之後，終於，在訪問網站時拿到了鎖頭的認證！

![](https://i.imgur.com/jguOHwH.png)

### 留言板更新

最近重新登入後發現文章底部都是一堆Disqus的廣告，真的有夠討厭，如果要消除廣告的方法只剩下付費，好討厭！於是我決定換一個評論系統，叫做[Valine](https://valine.js.org/) 的系統，他的優點是支援Emoji 😉、MarkDown 全语法支持，而且**沒有廣告**！大概的留言就像以下這樣：
![](https://i.imgur.com/xgmM0EX.png)

支援Markdown的留言真的很酷！

至於留言管理的部分，則是用[LeanCloud](https://leancloud.app/) 來實現，可以刪留言之類的XD
![](https://i.imgur.com/Zmtl0u8.png)

### 抱怨一下

今天在用Icloud 的時候，不小心把電腦上所有的檔案全部刪掉了，哭啊，這真是嚴重的事情ＱＱＱ，用到快掛了！當然，我電腦上的所有東西都不見了，也包括這一個部落格的本地程式碼，我用了 google drive 的備份，可惜那很難下載，所以我就用了github原本的備份的東西。

這真是不幸中的大幸，還好github的備份直接下載就可以直接用，不用特別再設定一些奇怪的東西，但所有的 hexo 指令都不見了，git的指令也都不見了，必須要重新設定。

這邊有一個小東西，就是下面這一行警告，就是要你把push設定成你要push的地方，不然只打一個push他並不會知道要push去哪裏，所以設定一下repo的位置就好了。

> git push --set-upstream origin master

當然還有一堆bug ，只好慢慢爬文嘍，當你看到這一段文字，也就代表我成功找到解決辦法了！

### 我又來更新了

這幾天網站一直404 not found 超煩，於是我叫出之前的教學影片，重新看了之後，發現我的分支名稱弄錯了，不是用master而是用gh-pages，搞了好多天QQ

## 架設心得

部落格以後會常常更新，最近也會陸續把之前的文章放到部落格上面。整個網站從誕生到現在的結果已經花了15個小時，其中在Debug的時間應該佔了大多數！

遇到了幾個麻煩的問題，首先碰到的是本地的資料無法上傳到Github，終端機冒出來的是：<font color="#f00">Please make sure you have the correct access rights and the repository exists.</font>。這個錯誤超級煩，爬了一堆文結果他要在GitHub上面設定ssh金鑰之類的東西，才能獲取從本地更新的權限。結果網路上的文章說我要開啟一個名叫".ssh"的資料夾，可是我翻遍了我的電腦也找不到這一個資料夾！後來我是利用終端機，輸入路徑位置硬是把那一個資料夾打開，它是一個透明的資料夾，還是第一次遇到！

接著是終端機跑出一堆error，那時候真的差點崩潰，花了超久架設的東西，竟然跑出這麼多的錯誤！難道全部的東西都付之一炬了嗎？翻了每一個被變更過的資料夾，才發現到在user_name的地方踩了雷，把他變回原本的設定就好了，真是不幸中的大幸，幸好我還能把東西救回來！

最後，終於能用https的前綴連到我的網站：<https://peienwu.com> 。如果要打www開頭也是可以連得上，直接打 peienwu.com 也可以順利連上！
