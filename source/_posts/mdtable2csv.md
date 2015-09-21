title: mdtable2csv
date: 2015-09-21 10:42:37
updated: 2015-09-21 10:42:37
tags:
- Markdown
- Tools
- tomroy
categories:
- Tools
- tomroy
---
# mdtable2csv
Recently I was trying to find an easy way to convert the tables in md to excel, instead of just copy paste.
After some research, I found that there are so many converters on the internet, but none of them can convert markdown file to excel. So I guess I have to make one on my own. And I was just happen to be learning Python, so I decided to write a Python! I also put it in my [GitHub repo](https://github.com/tomroy/mdtable2csv).

After lots of search on the internet, I found that GitHub provides a [Markdown API](https://developer.github.com/v3/markdown/#markdown) that can convert your markdown to html. So I can send my markdown file using json format to github, and it'll return the html format to me.

After I got the html, I first find all the contents in the `<table>...</table>` tags, extract the rows in the HTML table from `<tr>` tag. Then get the Header cells `<th>` which contains the header information and Standard cells `<td>` which contains data
separate headers and data by delimiter `,`.

最近由於工作上為了一些文件，有可能需要將markdown上面的表格貼到Excel上，但是總覺得用人工不是很可靠，所以想先找找看網路上有沒有人寫好工具可以用。
但是找了半天都只有轉成markdown的，或是把markdown轉成html之類的，並沒有轉成excel可以開的檔案。
剛好最近對Python有點興趣，就想說用Python來做一個吧！我也把我寫好的程式放在我的[GitHub repo](https://github.com/tomroy/mdtable2csv)裡面了。

由於並沒有直接把.md轉成.csv的工具，但是在找的過程中發現GitHub有提供一個 [Markdown API](https://developer.github.com/v3/markdown/#markdown) 可以讓你把markdown的內容包在json裡面丟上去，他就會把它轉成html的形式，我發現滿適合的就拿來用了。
當拿到html之後，剩下的就是從裡面把 `<table>...</table>` 標籤裡面的東西找出來，把每一行 `<tr>` 的標題 `<th>` 跟內容 `<td>` 然後用 `,` 把每個內容分開，輸出成.csv的格式就完成了！

Here are some sample results of my work : 
<!-- more -->
## Examples

### table.md
```sh
# table.md : 
First Header  | Second Header
------------- | -------------
Content Cell  | Content Cell
Content Cell  | Content Cell
```
`> mdtable2csv table.md`
```sh
# table.csv : 
First Header,Second Header
Content Cell,Content Cell
Content Cell,Content Cell
```
---
### abc.md
```sh
# abc.md :
|   |  a | b  | c  |
|---|----|----|----|
| 1 | a1 | b1 | c1 |
| 2 | a2 | b2 | c2 |
| 3 | a3 | b3 | c3 |
```
`> mdtable2csv abc.md`
```sh
# abc.csv :
 ,a,b,c
1,a1,b1,c1
2,a2,b2,c2
3,a3,b3,c3
```

Below are my implementation in Python : 
```Python
#!/usr/bin/python
#coding:utf-8
# Created by tom_th_lin on 2015/9/18.
import sys
import io
import requests
from flask import abort, json
from bs4 import BeautifulSoup

###
# define functions
###

def _read_file_or_404(filename, read_as_text=True):
    """
    Reads the contents of the specified file, or raise 404.
    """
    mode = 'rt' if read_as_text else 'rb'
    encoding = 'utf-8' if read_as_text else None
    try:
        with io.open(filename, mode, encoding=encoding) as f:
            return f.read()
    except IOError as ex:
        if ex.errno != errno.ENOENT:
            raise
        abort(404)

def render_content(text, api_url, gfm=False, context=None,
                   username=None, password=None):
    """
    Renders the specified markup using the GitHub API.
    """
    if gfm:
        url = '{}/markdown'.format(api_url)
        data = {'text': text, 'mode': 'gfm'}
        if context:
            data['context'] = context
        data = json.dumps(data, ensure_ascii=False).encode('utf-8')
        headers = {'content-type': 'application/json; charset=UTF-8'}
    else:
        url = '{}/markdown/raw'.format(api_url)
        data = text.encode('utf-8')
        headers = {'content-type': 'text/x-markdown; charset=UTF-8'}

    auth = (username, password) if username or password else None
    r = requests.post(url, headers=headers, data=data, auth=auth)

    # Relay HTTP errors
    if r.status_code != 200:
        try:
            message = r.json()['message']
        except Exception:
            message = r.text
        abort(r.status_code, message)

    return r.text

##
# Start program.
###
if len(sys.argv) >= 2:
	filename = sys.argv[1]
else:
	print "please put sys.argv[1] as filename !!"
	sys.exit()
if not (filename[-2:] == 'md'):
    print "the file format should be *.md"
    sys.exit()
render_text = _read_file_or_404(filename)
api_url = 'https://api.github.com'
html_table = render_content(render_text, api_url, True, None, None, None)

soup = BeautifulSoup(html_table, "html.parser") # parse html
table = soup.table # get <table>...</table>
# print table

f = open(filename[0:filename.find('.')] +'.csv', 'w') # open file.csv

rows = table.find_all('tr') # get all content <tr>...</tr>

ths = rows[0].find_all('th') # get <th>...</th> in first <tr>

write_th_to_file = ''
for th in ths:
    if th.string is not None:
        write_th_to_file += (th.string + ',')
    else:
        write_th_to_file += (' ' + ',')
write_th_to_file = write_th_to_file[:-1]
# print write_th_to_file
f.write(write_th_to_file)
f.write('\n')

for row in rows[1:]:
	write_td_to_file = ''
	tds = row.find_all('td')
	for td in tds:
		write_td_to_file += (td.string+',')
	write_td_to_file = write_td_to_file[:-1]
	# print write_td_to_file
	f.write(write_td_to_file)
	f.write('\n')

f.close()
print "convertion successfully done"
```

my GitHub page : https://github.com/tomroy
