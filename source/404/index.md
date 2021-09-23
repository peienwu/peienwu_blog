---
title: '404 - 真巧，竟然在這裡遇到你！'
date: 2020-09-12 23:01:35
comments: false
permalink: /404.html
---

<!-- markdownlint-disable MD039 MD033 -->

## 這是一個不存在的頁面

很抱歉，你目前存取的頁面並不存在。

![](https://i.imgur.com/FLdzRXc.png)

好爛，怎麼會這樣，要告訴我這個壞消息嗎QAQ

預計將在約 <span id="timeout">5</span> 秒後返回首頁。

如果你很急著想看文章，你可以 **[點這裡](https://hsiangfeng.github.io/)** 返回首頁。

<script>
let countTime = 5;

function count() {
  
  document.getElementById('timeout').textContent = countTime;
  countTime -= 1;
  if(countTime === 0){
    location.href = 'https://hsiangfeng.github.io/'; // 記得改成自己網址 Url
  }
  setTimeout(() => {
    count();
  }, 1000);
}

count();
</script>
