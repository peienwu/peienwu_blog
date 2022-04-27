---
title: '糟糕，找不到此頁 404 not found！'
date: 2020-09-12 23:01:35
comments: false
permalink: /404.html
---

<!-- markdownlint-disable MD039 MD033 -->

## 這是一個不存在的頁面

目前這個連結已經失效，可以[點這裡回首頁](https://peienwu.com/)尋找一下這篇文章！

![](https://i.imgur.com/FLdzRXc.png)

預計將在約 <span id="timeout">5</span> 秒後返回首頁。

如果你很急著想看文章，你可以 **[點這裡](https://peienwu.com/)** 返回首頁。

<script>
let countTime = 5;

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
