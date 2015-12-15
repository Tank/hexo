title: SSH-Agent-Forwarding
date: 2015-12-14 14:55:22
updated: 2015-12-14 14:55:22
tags:
- tips
categories:
- tips
---

# Using SSH agent forwarding

SSH agent forwarding allows you to use your local SSH keys instead of copy and paste your keys on your server. Means that  you don't have to worry about leaking you SSH Keys.
Take what I recently used for example, I was going to access the Server2 through Server1 from Local, but I don't want to leave my SSH Key on Server1.
```
Local ---(SSH)---> Server1 ---(SSH)---> Server2
```

# Setting up SSH agent forwarding
Let's set up SSH to allow agent forwarding to your server.
1. > Using your favorite text editor to open the file at `~/.ssh/config`. If it doesn't exist, you can enter `touch ~/.ssh/config` in the terminal to create this file.
2. > Enter the following text into the file, replacing `Server1` with your server's domain name or IP :
```
Host Server1
  ForwardAgent yes
```
Then you're good to go!

---

# SSH agent forwarding 的應用

SSH agent forwarding可以讓本地的SSH Key在遠端Server上轉送，就是說當你需要透過一台遠端Server連到另一台遠端Server的時候，你不需要將你的Public Key跟Private Key複製到遠端Server上，就可以省掉手動複製的麻煩，也不用擔心SSH key會外流。
<!-- more -->
舉個我自己最近用到的例子，因為我要SSH進Server1，然後透過Server1去連到Server2，但是因為Server1上沒有我的SSH Key，我就沒辦法連到，這時候就可以用SSH agent forwarding了，就會在登入Server2的時候自動使用你本地端的SSH Key去連。
```
Local ---(SSH)---> Server1 ---(SSH)---> Server2
```

# 設定 SSH agent forwarding
那SSH agent forwarding到底要怎麼設定呢？
1. > 首先用你常用的文字編輯器打開`~/.ssh/config`.如果你的`~/.ssh`裡面沒有config這個檔案的話，你可以用`touch~/.ssh/config`這個指令來新增檔案。
2. > 把下面的文字複製到config裡，把Server1改成你Server1的domain name或IP：
```
Host Server1
  ForwardAgent yes
```
這樣你就可以直接透過Server1去連Server2啦！

# Reference
* [Using SSH agent forwarding](https://developer.github.com/guides/using-ssh-agent-forwarding/)
* [SSH agent forwarding 的應用](https://ihower.tw/blog/archives/7837)