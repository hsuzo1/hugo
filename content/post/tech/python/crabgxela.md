---
title: "Python爬取广西干部网络学院的训练题库"
date: 2020-11-30T19:24:55+08:00
draft: false
categories: ["tech"]
Tags : ["scrap", "python"]
keywords : "爬虫 题库 BeautifulSoup python"
---

 
即将面临职业测试，但是备考资料实在太多，光知识点就**447**页，真不愧是绝世武功的目录啊。不过，广西干部网络学院给大家提供了一个很好的模拟训练的平台，而且正式测试的题目也会从中间抽取。由于模拟平台的设计有点不友好，比如要练习单选题，你要做完全部的**960**道题，才能提交批改🙀。这个时间长度是个让人疯狂的状态。看了一下网页源码，居然直接嵌入了正确答案和解析。为了更加灵活的学习，干脆写了一个爬虫程序，将题库爬取下来，方便复习🌝   
使用了BeautifulSoup4。
>Beautiful Soup is a Python package for parsing HTML and XML documents. It creates a parse tree for parsed pages that can be used to extract data from HTML, which is useful for web scraping.   

## 1.准备素材
登录网站，然后进入训练模块，查看网页源代码，并保存'**.html**'到本地项目目录下。大概结构如下：
```html
<!DOCTYPE html>
<html>
 <head>
  <meta charset="utf-8" />
  <base href="http://202.103.252.140:80/portal/" />
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
  <meta charset="utf-8" />
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <title>ExamStack</title>
 </head>
 <body data-genuitec-lp-enabled="false" data-genuitec-file-id="wc2-12" data-genuitec-path="/ExamStackPortal/WebRoot/WEB-INF/views/practice-improve-qh.jsp">
  <header data-genuitec-lp-enabled="false" data-genuitec-file-id="wc2-12" data-genuitec-path="/ExamStackPortal/WebRoot/WEB-INF/views/practice-improve-qh.jsp">
  </header>
  <div class="content" style="margin-bottom: 100px;">
   <div class="container">
    <div class="row">
     <div class="col-xs-9" style="padding-right: 0px;min-height:800px;">
      <div class="def-bk" id="bk-exampaper">
       <div class="expand-bk-content" id="bk-conent-exampaper">
        <ul id="exampaper-body">
         <li class="question qt-multiplechoice">
          <form class="question-body">
           <p class="question-body-text">根据《行政处罚法》的规定，当事人对行政机关所给予的行政处罚享有哪些权利？（ ）</p>
           <ul class="question-opt-list">
            <li class="question-list-item"><input type="checkbox" value="A" name="question-radio1" class="question-input" /><span class="question-li-text">A: 陈述权</span></li>
            <li class="question-list-item"><input type="checkbox" value="B" name="question-radio1" class="question-input" /><span class="question-li-text">B: 申辩权</span></li>
            <li class="question-list-item"><input type="checkbox" value="C" name="question-radio1" class="question-input" /><span class="question-li-text">C: 申请行政复议</span></li>
            <li class="question-list-item"><input type="checkbox" value="D" name="question-radio1" class="question-input" /><span class="question-li-text">D: 提起行政诉讼</span></li>
           </ul>
          </form>
          <div class="answer-desc">
           <div class="answer-desc-summary">
            <span>正确答案：</span>
            <span class="answer_value">ABCD</span>
            <br />
           </div>
           <div class="answer-desc-detail">
            <label class="label label-info"><i class="fa fa-paw"></i><span> 来源</span></label>
            <div class="answer-desc-content">
             <p></p>
            </div>
           </div>
           <div class="answer-desc-detail">
            <label class="label label-warning"><i class="fa fa-flag"></i><span> 解析</span></label>
            <div class="answer-desc-content">
             <p>《行政处罚法》第六条 公民、法人或者其他组织对行政机关所给予的行政处罚，享有陈述权、申辩权；对行政处罚不服的，有权依法申请行政复议或者提起行政诉讼。 公民、法人或者其他组织因行政机关违法给予行政处罚受到损害的，有权依法提出赔偿要求。</p>
            </div>
           </div>
           <div class="answer-desc-detail">
            <label class="label label-success"><i class="fa fa-bookmark"></i><span> 考点</span></label>
            <div class="answer-desc-content">
             <p>行政执法练习库 &gt; 多项选择题 &gt; 知识关键点：</p>
            </div>
           </div>
          </div></li>
         <li class="question qt-multiplechoice">
          <div class="question-title">
           <div class="question-title-icon"></div>
           <span class="question-no"></span>
           <span class="question-type" style="display: none;">multiplechoice</span>
           <span class="knowledge-point-id" style="display: none;">17</span>
           <span class="question-type-id" style="display: none;">2</span>
           <span>[多选题]</span>
           <span class="question-point-content"><span>(</span><span class="question-point">0</span><span>分)</span></span>
           <span class="question-id" style="display:none;">17944</span>
          </div>
          <form class="question-body">
           <p class="question-body-text">张某是甲县农贸市场的个体工商户，县卫生行政主管部门多次通知张某办理健康证和卫生许可证，但张某一直未办理。县卫生行政主管部门遂依法对其作出行政处罚。根据《行政处罚法》的规定，张某对县卫生行政主管部门的处罚决定享有的权利包括哪些？（ ）</p>
           <ul class="question-opt-list">
            <li class="question-list-item"><input type="checkbox" value="A" name="question-radio1" class="question-input" /><span class="question-li-text">A: 陈述权</span></li>
            <li class="question-list-item"><input type="checkbox" value="B" name="question-radio1" class="question-input" /><span class="question-li-text">B: 申辩权</span></li>
            <li class="question-list-item"><input type="checkbox" value="C" name="question-radio1" class="question-input" /><span class="question-li-text">C: 张某不服执法机关的处罚决定，可依法申请行政复议</span></li>
            <li class="question-list-item"><input type="checkbox" value="D" name="question-radio1" class="question-input" /><span class="question-li-text">D: 张某不服执法机关的处罚，可以拒绝执行行政处罚决定</span></li>
           </ul>
          </form>
          <div class="answer-desc">
           <div class="answer-desc-summary">
            <span>正确答案：</span>
            <span class="answer_value">ABC</span>
            <br />
           </div>
           <div class="answer-desc-detail">
            <label class="label label-info"><i class="fa fa-paw"></i><span> 来源</span></label>
            <div class="answer-desc-content">
             <p></p>
            </div>
           </div>
           <div class="answer-desc-detail">
            <label class="label label-warning"><i class="fa fa-flag"></i><span> 解析</span></label>
            <div class="answer-desc-content">
             <p>《行政处罚法》第六条 公民、法人或者其他组织对行政机关所给予的行政处罚，享有陈述权、申辩权；对行政处罚不服的，有权依法申请行政复议或者提起行政诉讼。 公民、法人或者其他组织因行政机关违法给予行政处罚受到损害的，有权依法提出赔偿要求。</p>
            </div>
           </div>
           <div class="answer-desc-detail">
            <label class="label label-success"><i class="fa fa-bookmark"></i><span> 考点</span></label>
            <div class="answer-desc-content">
             <p>行政执法练习库 &gt; 多项选择题 &gt; 知识关键点：</p>
            </div>
           </div>
          </div></li>

    <!-- 其他略去 -->

 </body>
</html>
```
  


## 2.编写爬虫代码  
  
以下为多选题的代码，其他单选和判断题的方法差不多。
```python
from bs4 import BeautifulSoup
import requests

# 方法1.直接在线爬取
htmlfile = requests.get('http://202.103.252.140/portal/student/practice-improve/6/17/2').content
# 方法2.从保存的本地文件爬取
# htmlfile = open('多选题.html', 'r', encoding='utf-8')
soup = BeautifulSoup(htmlfile, 'lxml', from_encoding='utf-8')
tags = soup.find_all('li', class_="qt-multiplechoice")

for tag in tags:
    print(tag.select('.question-body-text')[0].get_text())
    tagsa = tag.select(".question-li-text")
    for tagc in tagsa:
        print(tagc.get_text())
```  
  

至此，大功告成。总题目数**2000**，复习去了。


> 知乎上Dwzb的[BeautifulSoup全面总结](https://zhuanlan.zhihu.com/p/35354532)作为笔记收藏一下