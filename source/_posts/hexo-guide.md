title: 使用Hexo在GitHub Pages上架設自己的Blog
date: 2015-09-11 22:33:02
categories:
- Hexo
tags: 
- Hexo
- GitHub
---
## 什麼是 Hexo
簡單介紹一下 Hexo ，引用 [Hexo](https://hexo.io/zh-tw) 官方網站上面的一句話
> Hexo is a fast, simple and powerful blog framework. 
> Hexo 是一個快速、簡單且強大的網誌框架。

剩下的大家可以自己點進去 [Hexo](https://hexo.io/zh-tw) 看。
作者是台灣人喔，感覺是個肥宅XD
有興趣可以看一下作者[對 hexo 的介紹](http://zespia.tw/blog/2012/10/11/hexo-debut/)。

## 什麼是 GitHub Pages
也是引用 [GitHub Pages](https://pages.github.com/) 官方網站上面的一句話
> Hosted directly from your GitHub repository. Just edit, push, and your changes are live.
> 直接把你的網頁放到你的 GitHub repository ，這樣全世界就都看得到你的網頁啦。

[GitHub Pages](https://pages.github.com/) 不止讓可以你放網站，同時還給你一組專屬的URL  http<span></span>://username.github.io，省去了不少申請DNS的麻煩呢，當然如果有自己的DNS也是可以拿來使用的。
設定的方式 [GitHub Pages](https://pages.github.com/) 上寫得圖文並茂、淺顯易懂，就請各位自己點進去看啦。


好，終於進入正題，現在開始一步一步來架我們的Blog吧。
## 安裝 Hexo
安裝需求
在安裝前您必須先檢查下列您的電腦是否已經安裝下列軟體：<!--more-->
- [Node.js](https://nodejs.org/en/)
- [Git](http://git-scm.com/)

如果你的電腦已經安裝上述的必備軟體，那麼只需要透過 npm 可以完成 Hexo 的安裝。若還沒安裝好請先從上面連結點進去下載吧。
```
    $ npm install -g hexo-cli
```
一旦 Hexo 安裝完成後，隨便切換到自己想要放blog的資料夾，執行下列指令，Hexo 就會在指定資料夾中建立所有搭建 blog 需要的檔案。
```
    $ hexo init <folder> # 建立一個新的網站。如果沒有設定 folder 的話，Hexo 會在目前的資料夾建立網站。這裡我們把 folder 取名為 Blog
    $ cd <folder>
    $ npm install
```
建立完成後，專案資料夾會有下列檔案：
```
    .
    ├── _config.yml
    ├── package.json
    ├── scaffolds
    ├── scripts
    ├── source
    |   ├── _drafts
    |   └── _posts
    └── themes
```
每個資料夾的詳細說明再請自己點進去官方網站的[設定](https://hexo.io/docs/setup.html)裡面看囉。
```
    $ hexo g # hexo generate 產生靜態檔案。
    $ hexo s # hexo server 啟動伺服器。
    # Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
    # 啟動伺服器以後可以按Ctrl + C來停止。
```
現在我們已經建好本機端的 blog 了，用瀏覽器輸入 [http://localhost:4000](http://localhost:4000) 或是 [http://127.0.0.1:4000](http://127.0.0.1:4000) 就可以看到你的 blog 了。
## 把 Blog 放到 GitHub 上
請先用下面指令安裝 [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git) .
```
    $ npm install hexo-deployer-git --save
```
再來先在 GitHub 上開一個 repository ，取名叫 Blog 後，將該 repository 的 HTTPS clone URL 複製後，打開本地端 Blog 中的 _config.yml ， 在  deploy: 下面，type 輸入 git，repository 就把剛剛複製的HTTPS clone URL專案路徑貼在這裡，ssh 或https都可以， branch 填 master  最後會長成像以下這個樣子
```
    deploy:
        type: git
        repository: git@github.com:username/yourRepo.git
        branch: master
```
username : 你的 GitHub 使用者名稱
yourRepo : 你剛剛個取的 repo 的名字

關於更詳細的設定請參考 [deploy](https://hexo.io/docs/deployment.html#Git) 。

接下來只要再回到 console 輸入
```
    $ hexo d # hexo deploy 部署網站(到GitHub上)。
```
或是把部署跟產生頁面合成一條指令，下面兩個指令作用是相同的。
```
    $ hexo d -g # hexo deploy --generate
    $ hexo g -d # hexo generate --deploy
```
這樣就可以在你的 GitHub Pages 上看到剛剛建好的 Blog 了。
官方給定的網址是 http://username.github.io/
