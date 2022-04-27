---
title: '糟糕，找不到此頁 404 not found！'
date: 2020-09-12 23:01:35
comments: false
permalink: /404.html
---

<!-- markdownlint-disable MD039 MD033 -->

## 這是一個不存在的頁面

目前這個連結已經失效，可以去首頁尋找一下這篇文章！
好爛，怎麼會這樣，要告訴我這個壞消息嗎QAQ

可以透過 **[FB](https://www.facebook.com/peien.wu.58/)** 傳訊息給我，感激不盡！

![](https://i.imgur.com/FLdzRXc.png)

預計將在約 <span id="timeout">10</span> 秒後返回首頁。

如果你很急著想看文章，你可以 **[點這裡](https://peienwu.com/)** 返回首頁。

<script>
let countTime = 10;

function count() {
  
  document.getElementById('timeout').textContent = countTime;
  countTime -= 1;
  if(countTime === 0){
    location.href = 'http://peienwu.com/';
  }
  setTimeout(() => {
    count();
  }, 1000);
}
count();
</script>
