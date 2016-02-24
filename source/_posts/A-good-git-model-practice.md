title: Git 分支模組開發流程 A good Git model practice and some tips
date: 2016-02-23 16:49:33
updated: 2016-02-23 16:49:33
tags:
- git
- tips
categories:
- git
- tips
---

commit tree of a real project      
![Crazy git tree](/uploads/git_branch/crazy_tree.png "Crazy tree")
有時候如果沒有用適合的流程來使用Git，commit tree就會變成像上圖那樣讓人不易閱讀。

# Git 分支模組開發流程

今天來跟大家介紹一下許多人在使用的Git branch開發最佳使用慣例，我現在工作上也是使用這種模式。下面的圖及內容都是參考 [A successful Git branching model](http://http://nvie.com/posts/a-successful-git-branching-model/ "A successful Git branching model") 這篇文章。
簡單來說，他將 branch 分成兩個主要分支(master, develop)，三種支援性分支(Feature branches, Release branches, Hotfix branches):   
[![git-model](/uploads/git_branch/git-model@2x.png "git-model")](http://http://nvie.com/posts/a-successful-git-branching-model/)

* 主要分支 (The main branches)
	* master: 主程式分支, 永遠處在 production-ready 狀態
	* develop: 開發分支, 下次發布之前的開發最新進度
* 支援性分支 (Supporting branches)
	* Feature branches: 開發新功能的分支, 從 develop 切出來, 功能開發完成再 merge 回 develop 
	* Release branches: 準備要 release 的版本, 在這上面只修 bugs, 從 develop 切出來, merge 回 develop 跟 master
	* Hotfix branches: 有重大 bug 必須馬上修, 不能等到下次 release 版本才修時用的, 會從 master 分支出來，完成後 merge 回 master 和 develop

<!-- more -->

## 主要分支 The main Branches

當專案開始執行時，我們將程式碼分成兩個分支：   

 * master
 * develop
 
master 主要是 Release 專用的，沒事不會在上面開發，假如要繼續開發新功能，或者是修正 Bug issue 就利用 develop 這分支來開發，等開發完成，要 Release 下一版產品時再將 develop merge 到 origin/master 分支，避免有人把 origin/master 改爛，底下這張圖就說明只有兩個主要分支時的開發模式:   
![main-branches](/uploads/git_branch/main-branches@2x.png "main branches")

## 支援性分支 Supporting Branches

支援性分支包含了:

* Feature branches
* Release branches
* Hotfix branches

其中 Hotfixes 用來修正最重大的 bug，所以從 origin/master 直接分支出來，修正之後再 merge 回 master 跟 develop。

Feature 跟 Release 都是從 develop 分支出來，最後都 merge 回 develop branch，develop 再 merge 回主分支 master 加上版本號的 tag 這樣一個 release 就完成了。

### 新功能分支 Feature branches
![feature branches](/uploads/git_branch/fb@2x.png)
顧名思義就是當你要開發新功能的時候，從 develop 分支切出一個 Feature branch ，在上面開發新功能，開發完成以後再 merge 回 develop，不過 merge 回 develop 時請大家記得加上參數`git merge --no-ff`，我們利用下面這張圖來說明有加`--no-ff`跟沒有加的差異：   
![merge-without-ff](/uploads/git_branch/merge-without-ff@2x.png)   
我們可以很清楚地看到，右邊是正常的 merge，會將原本的 commit log 合併成一條，然而如果加上 `-–no-ff` 參數的話，commit log 會紀錄您是開分支出去的，清楚紀錄您的分支操作步驟，建議大家多使用此方法，這樣開發新功能時的紀錄就會被分開在另外一條，不會跟原本 develop 的紀錄混在一起，比較容易閱讀。

### 發佈分支 Release branches
Release branch 主要是幫助你在出 Production release 之前的準備。
可以修改一寫小 bug 或是調整一些 Metadata (版本號、建置時間等等) 透過 release branch 的幫忙，你的 develop branch 就可以清楚的切割功能與版本，為開發下一次 release 的新功能做準備。

#### 建立 Release branch 的時機
Release branch 是從 develop 分支分出來的。舉例來說，現在我們的 production 版本是1.1.5，我們現在預計要出一個新的版本，當 develop 分支的新功能都已經開發完而且準備可以出 "next release" 的時候，我們決定新版的版號是 1.2 (不叫 1.1.6 也不叫 2.0)，這時我們從 develop 分支切出一支 release branch 並且根據我們的版號把這個 branch 命名為release-1.2:   
```bash
$ git checkout -b release-1.2 develop
切到新 branch "release-1.2"
$ ./bump-version.sh 1.2
把版本改成 1.2
$ git commit -a -m "Bumped version number to 1.2"
[release-1.2 74d9424] Bumped version number to 1.2
1 files changed, 1 insertions(+), 1 deletions(-)
```
建好新 release branch 並切過去之後，我們用一個假的 bump-version.sh 來把我們的版本號改成 1.2 。
這個 release branch 會存在一段時間直到真正 merge 到 master 並且 release ，這段期間內我們可以在這個 branch 修一些 bug (而不是在 develop )，但是嚴禁在 release branch 增加任何新功能，最後，當 merge 到 master 並 release 之後，一定要把這些改動 merge 回 develop 以便繼續開發。

#### 完成 Release branch 
當 release branch 已經準備好真正的 release 時，有幾個步驟需要完成：   
   1. 把release branch merge 到 master 並加上版本 tag
	```bash
   $ git checkout master
   切到 master branch
   $ git merge --no-ff release-1.2
   merge release branch 到 master
   $ git tag -a 1.2
   加上 tag
	```
	這樣 release 就完成了，也加上了 tag 方便追蹤。
   2. 為了把在 release branch 做的改動保留下來，我們要把release branch merge 回 develop。
	```bash
   $ git checkout develop
   切到 develop branch
   $ git merge --no-ff release-1.2
   merge release branch 到 master
	```
在 merge 回 develop 的時候可能會遇到 conflict (非常有可能，因為我們改了版號...)，如果遇到 conflict 就修改一下再commit。

現在我們所有跟 release 有關的步驟都完成了， release branch 也可以功成身退，畢竟我們再也不需要它了:   
```bash
$ git branch -d release-1.2
Deleted branch release-1.2 (was ff452fe).
```

### 重大 issue 分支 Hotfix branches 

當目前 Production 版本發現重大的 bug 需要儘快解決的時候， 就需要從 master branch 有 tag 目前 Production 版本的地方切一支 Hotfix branch 出來，目的是為了能讓工程師在修 bug 的同時，不會影響到另外原本 develop 分支的開發。   
![hotfix-branch](/uploads/git_branch/hotfix-branches@2x.png)   
從上圖可以看到，當需要 Hotfix 的時候我們就從 master 切出一個 Hotfix branch 出來，解決掉 bug 之後再把它 merge 回 master 直接出一版新的 1.2.1，最後把它 merge 回 develop 。完成之後就可以把這支 hotfix branch 刪掉了。

# Summary
1. Master branch 是用來出穩定版本，並且記錄整個產品開發的歷程。
2. 幫每個 release 都出一個 release branch (e.g. 1.0, 1.1, 1.2 ...) 。
	```bash
	$ git checkout -b <project_release_branch> // from master branch
	```
3. 幫每個 release 都出一個 develop branch (e.g. 1.0-devel)。
	```bash
	$ git checkout -b <project_develop_branch> // from <project_release_branch>
	```
4. 當要開發的新功能需要比較長的時間的時候，開一個	feature branch 來做。
	```bash
	$ git checkout -b <long_task_branch> // from <project_develop_branch>
	```
5. 當 long task 做完的時候用 rebase + fast-forward merge 回 develop branch。
	```bash
	$ git checkout <long_task_branch>
	$ git rebase <project_develop_branch>
	$ git checkout <project_develop_branch>
	$ git merge <long_task_branch> --ff-only
	$ git push origin <project_develop_branch>
	$ git branch -D <long_task_branch>        // remove local long task branch
	$ git push origin :<long_task_branch>     // remove remote long task branch
	```
6. 用 merge 把 develop branch merge 回 release branch。   
	```bash
	$ git checkout <project_release_branch>
	$ git merge <project_develop_branch>
	$ git push origin <project_release_branch>
	```
7. 把 release branch 用 rebase + non-fast-forward merge 到 master 並加上 tag。
	```bash
	$ git checkout <project_release_branch>
	$ git rebase <master>
	$ git checkout <master>
	$ git merge <project_release_branch> --no-ff
	$ git tag -a <tag>
	$ git branch -D <project_release_branch> // remove local project release branch, because it may not consist with remote branch
	```
8. 出完 release 之後把 local 跟 remote 的 develop branch 都刪掉。
	```bash
	$ git branch -D <project_develop_branch>           // remove local project develop branch
	$ git push origin :<project_develop_branch>      // remove remote project develop branch
	```

# Reference
[http://nvie.com/posts/a-successful-git-branching-model/#summary](http://nvie.com/posts/a-successful-git-branching-model/#summary)

[https://ihower.tw/blog/archives/3843](https://ihower.tw/blog/archives/3843)

[https://ihower.tw/blog/archives/5140](https://ihower.tw/blog/archives/5140)

[https://blog.wu-boy.com/2011/03/git-%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6-branch-model-%E5%88%86%E6%94%AF%E6%A8%A1%E7%B5%84%E5%9F%BA%E6%9C%AC%E4%BB%8B%E7%B4%B9/](https://blog.wu-boy.com/2011/03/git-%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6-branch-model-%E5%88%86%E6%94%AF%E6%A8%A1%E7%B5%84%E5%9F%BA%E6%9C%AC%E4%BB%8B%E7%B4%B9/)
