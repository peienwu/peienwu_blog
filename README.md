Welcome to Peienwu Blog

我的個人部落格原始碼，部署於 peienwu.com。

📚 文章分類

分類	說明
數學筆記	含解析幾何、機率論、數論、競賽題解等文章。
物理筆記	研究力學、電磁、天體物理筆記與模擬。
程式專案 / 教學	使用 Python、C++、JS 的小專案與教學紀錄。
工具與雜談	學習筆記、工作流程、軟體心得、生活隨筆。

目前累積 280+ 篇文章，完整目錄請見部落格側邊欄「Categories」。

⸻

🚀 如何在本機查看全部文章

# 安裝依賴
npm install

# 生成並啟動本地伺服器
hexo clean && hexo g && hexo s
# → http://localhost:4000

所有文章位於 source/_posts/，Markdown 格式，Front-matter 例：

---
 title: 伯特蘭悖論（Bertrand Paradox）
 date: 2022-02-07
 categories: [數學筆記]
 tags: [數學, 機率]
 mathjax: true
---

內文可直接插入 LaTeX 與 YouTube <iframe>。

⸻

🔎 文章精選
	•	橢圓軌道上行星到太陽平均距離 — 數學推導 + 程式積分模擬
https://peienwu.com/Gravity2/
	•	伯特蘭悖論 — 三種隨機弦模型的幾何機率對比
https://peienwu.com/bertrand/
	•	倒立擺模擬 — Python + Matplotlib 動畫示例
https://peienwu.com/inverted-pendulum/

更多請直接瀏覽部落格主頁！

⸻

🗄 專案結構

peienwu_blog/
├─ source/_posts/   # 所有 Markdown 文章
├─ themes/          # NexT 主題
├─ _config.yml      # Hexo 設定
└─ package.json



⸻

🛠 技術&部署
	•	Hexo 7 / NexT-Pisces 主題
	•	MathJax / Highlight.js / 原生 YouTube <iframe>
	•	GitHub Pages + 自訂網域 peienwu.com

部署：hexo clean && hexo g && hexo d

⸻

🙋‍♂️ 關於作者

Peien Wu — 喜歡用數學與程式探索世界，持續書寫、持續分享。