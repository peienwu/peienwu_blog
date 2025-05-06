# Welcome to Peienwu Blog

**🔗 我的部落格：[peienwu.com](https://peienwu.com)**

用 Hexo + NexT 主題構建，部署在 GitHub Pages，主要紀錄數學、物理、程式設計筆記，以及一些個人心得文章。

---

## 🚀 專案概覽

* **框架**：Hexo 7.x
* **主題**：NexT-Pisces（客製化樣式）
* **部署**：GitHub Pages + 自訂網域（peienwu.com）
* **語法支援**：MathJax (LaTeX)、Highlight.js
* **嵌入**：YouTube 影片用 `<iframe>` 直接嵌入
* **文章格式**：Markdown（Front-matter 支援 `tags`、`categories`）

---

## 📂 專案結構

```
peienwu_blog/
├── source/
│   ├── _posts/          # 所有文章內容
│   ├── images/          # 圖片資料夾
│   └── CNAME            # 自訂網域
├── themes/              # NexT 主題
├── _config.yml          # Hexo 設定
├── package.json         # 相依套件與 script
└── README.md            # 本說明文件
```

---

## ⚙️ 本機開發指南

1️⃣ **環境需求**

* Node.js (推薦 18.x LTS)
* Hexo CLI

```
npm install -g hexo-cli
```

2️⃣ **安裝**

```
git clone git@github.com:peienwu/peienwu_blog.git
cd peienwu_blog
npm install
```

3️⃣ **啟動本機服務**

```
hexo clean
hexo g
hexo s
```

瀏覽器開啟 [http://localhost:4000](http://localhost:4000) 查看網站預覽。

---

## ✍️ 新增文章

```
hexo new "文章標題"
```

新增後編輯 `source/_posts/` 裡的 Markdown 檔案。

---

## 🎥 影片嵌入教學

請**避免**舊的 `{% video %}` 或 `{% youtube %}` 樣式，直接用原生 HTML：

```
<iframe width="560" height="315"
        src="https://www.youtube.com/embed/你的影片ID"
        frameborder="0"
        allowfullscreen>
</iframe>
```

範例文章：

* [橢圓軌道上行星到太陽平均距離](https://peienwu.com/Gravity2/)
* [伯特蘭悖論（Bertrand Paradox）](https://peienwu.com/bertrand/)

---

## 🚀 部署指南

1️⃣ 確認 `source/CNAME` 設定正確（內容為 `peienwu.com`）

2️⃣ 執行部署指令：

```
hexo clean && hexo g && hexo deploy
```

這會 push 到 `gh-pages` 分支並自動更新 GitHub Pages。

---

## ❓ FAQ

| 問題             | 解決方式                                    |
| -------------- | --------------------------------------- |
| 影片無法顯示         | 確認使用 `<iframe>` 而非 Hexo 插件格式            |
| 更新後沒變化         | 執行 `hexo clean` 再重建                     |
| 推不上 `gh-pages` | 檢查 SSH 金鑰 & `deploy.repo` 是否正確設為 SSH 路徑 |
| 網域解析失敗         | 檢查 `source/CNAME` & DNS 記錄設定            |

---

## 🚧 未來規劃

目前已開始評估轉換為：

* **Next.js 14 + MDX**
* 部署至 Vercel
* 加入自動化 SEO、搜尋功能
* Tailwind CSS + Shadcn UI 重構

目標是讓部落格更現代化、更易維護！

---

## 🛡 授權

* **程式碼**：MIT License
* **內容**：CC BY-NC-SA 4.0

請在轉載或引用時附上出處 🙌。

---

## 🙋‍♂️ 作者

Peien Wu
部落格：[peienwu.com](https://peienwu.com)
GitHub：[peienwu](https://github.com/peienwu)

> *「紀錄每一段學習的軌跡，累積一點一滴的成長。」 ✨*
