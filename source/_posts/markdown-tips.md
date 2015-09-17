title: Markdown tips
date: 2015-09-16 15:38:58
tags:
- Markdown
- tips
categories:
- tips
---

# Markdown tips
---

半形空白(Halfwidth space) = `&ensp;` 或 `&#8194;`
全形空白(Fullwidth space) = `&emsp;` 或 `&#8195;`
不斷行的空白格(non-breaking space) = `&nbsp;` 或 `&#160;`

## 半形空白(Halfwidth space)
```Markdown
哈囉！這邊&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;有五個半形空白。
Hello! We have&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5 halfwidth spaces here.
```
執行結果如下：
> 哈囉！這邊&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;有五個半形空白。
> Hello! We have&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5 halfwidth spaces here.

<!--more-->

## 全形空白(Fullwidth space)
```Markdown
哈囉！這邊&emsp;&emsp;&emsp;&emsp;&emsp;有五個全形空白。
Hello! We have&emsp;&emsp;&emsp;&emsp;&emsp;5 fullwidth spaces here.
```
執行結果如下：
> 哈囉！這邊&emsp;&emsp;&emsp;&emsp;&emsp;有五個全形空白。
> Hello! We have&emsp;&emsp;&emsp;&emsp;&emsp;5 fullwidth spaces here.

## 不斷行的空白格(non-breaking space)
```Markdown
哈囉！這邊&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;有五個不斷行的空白格。
Hello! We have&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5 non-breaking spaces here.
```
執行結果如下：
> 哈囉！這邊&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;有五個不斷行的空白格。
> Hello! We have&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5 non-breaking spaces here.
