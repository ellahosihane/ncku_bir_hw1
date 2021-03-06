# BIR_Project 1 Full-text retrieval tool
生醫資訊作業
# Dependencies
* Python 3.7.11
* Pyqt5 5.15.4
# GUI
1. 使用Qt Designer 設計介面
2. 使用VScode 的 PYQT Integration extension對fulltext.ui進行編譯，生成對應的Ui_fulltext.py
 ![image](https://user-images.githubusercontent.com/91927076/136767797-d928d97d-e9fb-4665-b2d5-e974396aa6e3.png)
### Pyqt components
* `QTextBrowser` : 因其無編輯功能，選擇使用該元件作為顯示文章的欄位。
* `QLineEdit` : File name與Search欄位皆使用該元件，具有讀寫功能。
* `QListWidget` : 用以顯示文章列表，以及跳頁功能。
### Read File
* 讀取文件並依其格式進行處理、排版後，存成`Dictionary`(Key=`String`Title, Value=`String`Content)
* 以利於建立ListWdget與跳頁功能
* XML : 使用`xml.etree.ElementTree`讀取，取得文章的Title與Abstract，並仿照PubMed格式排版
* JSON : 讀取後取得Username、Fullname、Tweet text，仿照twitter格式排版

  ```
  #XML
  self.contentList[doc.find("ArticleTitle").text] = content
  #JSON
  self.contentList["twitter"] = content
  ```
* 因QTextBrowser支援HTML語法，因此部分符號需做修改
  ```
  content.replace("<", "&lt;")
  content.replace(">", "&gt;")
  ```
### Statistics
* Number of characters: `String`長度
* Number of words: 以空格將`String`分割所得到之`List`的長度
* Number of sentences: 以`.`將`String`分割所得到之`List`的長度
``` 
num_char = str(len(content))
num_word = str(len(content.split(" ")))
num_sentences = len(content.split('.'))
``` 
### Search & Highlight
* QTextBrowser支援HTML語法顯示，使用HTML實作Highlight功能
* 使用`re.finditer`找出`String`中符合關鍵字的字詞之索引
* 將內文轉為`List`，依照索引於插入HTML Tag:`<font style=\"background:yellow;\">` `</font>`
* 使用`str.join()`重組文章
* 因使用HTML語法，須將換行`\n`替換為`<br>`

  ``` 
  index_list.extend([_.start() for _ in re.finditer(keyword.lower(), curr_content.lower())])
  for num, index in enumerate(index_list):
      wordList.insert(index + num*2, "<font style=\"background:yellow;\">")  
      wordList.insert(index + num*2+ len(keyword)+1, "</font>")
  wordList = "".join(wordList)
  wordList = wordList.replace("\n","<br>")
  ``` 

# Demo
* 輸入檔案路徑或點選...選擇檔案(可選擇XML與JSON格式)
 ![image](https://user-images.githubusercontent.com/91927076/136768404-6c3d2310-1543-4c63-8a30-d4cf0a6c5eba.png)
* 點選Load讀取文件
  * 顯示格式仿照PubMed與Twitter，並於左下角顯示當前內容的字元數、字數與句數
  * xml格式預設顯示檔案中首篇文章，點選左方清單可選擇其他文章
  * json格式則是全數顯示
 
  ![image](https://user-images.githubusercontent.com/91927076/136770259-9a4e7959-a3c5-41d7-a8e7-cee76b357046.png)
  ![image](https://user-images.githubusercontent.com/91927076/136769722-55c9f991-16a2-4241-be2a-4533e4007e0d.png)

* Text Search

  ![image](https://user-images.githubusercontent.com/91927076/136770708-fded8631-d96f-41d8-af09-925003e1c66d.png)

# Reference
[VSCode + PYQT5 + QtDesigner 環境搭建和測試](https://www.itread01.com/content/1541809161.html)

[QListWidget Class](https://doc.qt.io/qt-5/qlistwidget.html)

[PyQt - QList Widget](https://www.tutorialspoint.com/pyqt/pyqt_qlistwidget.htm)

[Python3正則匹配re.split，re.finditer及re.findall函式用法詳解](https://www.itread01.com/article/1528700228.html)

[xml.etree.ElementTree — The ElementTree XML API](https://docs.python.org/3/library/xml.etree.elementtree.html)
