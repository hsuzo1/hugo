---
title: "Selenium4使用方法小结"
date: 2023-05-28T21:21:47+08:00
draft: false
categories: ["tech"]
Tags : ["python"]
description : Selenium是支持web浏览器自动化的一系列工具和库的综合项目，目前版本为selenium4.9.1。通过该工具您可以自动化操控chrome、firefox、edge等主流浏览器，效果相当于您亲手操作。
keywords : "张旭州 selenium 元素定位 浏览器自动化"
---

[Selenium](https://www.selenium.dev/zh-cn/)是支持 web 浏览器自动化的一系列工具和库的综合项目，目前版本为selenium***4.9.1***。通过该工具您可以自动化操控chrome、firefox、edge等主流浏览器，效果相当于您亲手操作。本文主要基于python环境进行总结。  
准备一：***安装selenium库***
```python
pip install selenium
```
准备二：***下载对应浏览器的driver程序***  
下载前需要确认电脑浏览器的***版本号***，再下载相对应的程序。
- 微软Edge浏览器：[Microsoft Edge WebDriver](https://developer.microsoft.com/zh-cn/microsoft-edge/tools/webdriver/)
- 谷歌浏览器：[chromedriver](https://googlechromelabs.github.io/chrome-for-testing/#stable)
- 火狐浏览器：[geckodriver](https://github.com/mozilla/geckodriver/releases)  

如果需要其他浏览器驱动程序，也可以在[淘宝镜像站](https://registry.npmmirror.com/binary.html)上查找。驱动程序下载后，可以保存在对应浏览器的程序文件夹中，也可以放在python脚本目录中，只要运行时指定好程序路径即可。
```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By


exePath = r'C:\Program Files (x86)\Google\Chrome\Application\chromedriver.exe'
s = Service(exePath)
op = webdriver.ChromeOptions()
browser = webdriver.Chrome(service=s, options=op)
browser.get('http://www.gxela.gov.cn')
browser.maximize_window()
browser.implicitly_wait(9)
write = browser.find_element(By.ID,"j_username")
write.send_keys("user123132")
write = browser.find_element(By.ID,"j_password1")
write.send_keys("pwd123456786")
browser.find_element(By.XPATH, '//*[@id="res-index"]/form[1]/div[4]').click()
```

### 官方给出的入门例子
```python
from selenium import webdriver
from selenium.webdriver.common.by import By


def test_eight_components():
    driver = webdriver.Chrome()

    driver.get("https://www.selenium.dev/selenium/web/web-form.html")

    title = driver.title
    assert title == "Web form"

    driver.implicitly_wait(0.5)

    text_box = driver.find_element(by=By.NAME, value="my-text")
    submit_button = driver.find_element(by=By.CSS_SELECTOR, value="button")

    text_box.send_keys("Selenium")
    submit_button.click()

    message = driver.find_element(by=By.ID, value="message")
    value = message.text
    assert value == "Received!"

    driver.quit()
```   
### 元素定位
主要有8种不同的内置元素定位策略：
|定位器Locator|描述|
| --- | ----------- |
|class name定位| class属性与搜索值匹配的元素（不允许使用复合类名）|
|css selector|定位CSS选择器匹配的元素|
|id| 定位id属性与搜索值匹配的元素|
|name	|定位name属性与搜索值匹配的元素|
|link text	|定位link text可视文本与搜索值完全匹配的锚元素|
|partial link text	|定位link text可视文本部分与搜索值部分匹配的锚点元素。如果匹配多个元素，则只选择第一个元素。|
|tag name	|定位标签名称与搜索值匹配的元素|
|xpath	|定位与XPath表达式匹配的元素|
```python
# find_element只返回第一个元素，find_elements则返回元素集
# class name定位，information为类名
driver.find_element(By.CLASS_NAME, "information")
# css selector定位，fname为id
driver.find_element(By.CSS_SELECTOR, "#fname")
# 通过id定位，lname为id
driver.find_element(By.ID, "lname")
# name属性定位
driver.find_element(By.NAME, "newsletter")
# link text定位，为超链接标签名
driver.find_element(By.LINK_TEXT, "Selenium Official Page")
# partial link text，可以理解为部分匹配
driver.find_element(By.PARTIAL_LINK_TEXT, "Official Page")
# tag name定位
driver.find_element(By.TAG_NAME, "a")
# xpath定位
driver.find_element(By.XPATH, "//input[@value='f']")

# 嵌套查询
fruits = driver.find_element(By.ID, "fruits")
fruit = fruits.find_element(By.CLASS_NAME,"tomatoes")
# 或优化为
fruit = driver.find_element(By.CSS_SELECTOR,"#fruits .tomatoes")

# 查询div中全部p元素的文本内容并输出
element = driver.find_element(By.TAG_NAME, 'div')
elements = element.find_elements(By.TAG_NAME, 'p')
for e in elements:
    print(e.text)

# 获取元素文本内容
text = driver.find_element(By.ID, "justanotherlink").text
# 页面元素是否显示
is_email_visible = driver.find_element(By.NAME, "email_input").is_displayed()
# 页面元素是否启用
is_email_visible = driver.find_element(By.NAME, "email_input").is_displayed()
# 元素是否被选中
value = driver.find_element(By.NAME, "checkbox_input").is_selected()
# 获取元素标签名
attr = driver.find_element(By.NAME, "email_input").tag_name
# 获取元素位置和大小，x、y、高度、宽度
res = driver.find_element(By.NAME, "range_input").rect
# 获取元素CSS值
cssValue = driver.find_element(By.ID, "namedColor").value_of_css_property('background-color')
# 获取特性或属性
email_txt = driver.find_element(By.NAME, "email_input")
value_info = email_txt.get_attribute("value")

```
### 文本输入和点击操作
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
driver = webdriver.Firefox()


driver.get("http://www.google.com")

    # 定位按钮并点击
submit_button = driver.find_element(by=By.CSS_SELECTOR, value="button").click()
    # 输入"webdriver"后按回车键
driver.find_element(By.NAME, "q").send_keys("webdriver" + Keys.ENTER)
    # 定位搜索框
SearchInput = driver.find_element(By.NAME, "q")
    # 搜索框中输入selenium
SearchInput.send_keys("selenium")
    # 清除输入的内容
SearchInput.clear()
```
### 警告框的处理
```python
# 点击按钮弹出警告框
driver.find_element(By.LINK_TEXT, "See a sample confirm").click()
# 等待弹出
wait.until(expected_conditions.alert_is_present())
# 存储到变量中
alert = driver.switch_to.alert
# 保存警告框的内容
text = alert.text
# 按下取消按钮
alert.dismiss()
  
# 按下按钮弹出提示框
driver.find_element(By.LINK_TEXT, "See a sample prompt").click()
# 等待显示
wait.until(expected_conditions.alert_is_present())
# 保存对象
alert = Alert(driver)
# 发送文字内容给提示框
alert.send_keys("Selenium")
# 按下确定按钮
alert.accept()
  
```
### frame页面的处理
```python
    # 方法一：定位frame对象
iframe = driver.find_element(By.CSS_SELECTOR, "#modal > iframe")
    # 切换到选择的 iframe
driver.switch_to.frame(iframe)
    # 单击frame中的按钮
driver.find_element(By.TAG_NAME, 'button').click()

    # 方法二：也可以通过 frame的id 切换到buttonframe框架
driver.switch_to.frame('buttonframe')
    
    # 方法三：基于索引切换到第 2 个 iframe
iframe = driver.find_elements(By.TAG_NAME,'iframe')[1]
    # 切换到选择的 iframe
driver.switch_to.frame(iframe)

    # 离开frame切回到默认内容
driver.switch_to.default_content()
```
  

关于浏览器窗口的操作比较多，可以自行到官方网址进行学习[https://www.selenium.dev/zh-cn/documentation/webdriver/interactions/windows/](https://www.selenium.dev/zh-cn/documentation/webdriver/interactions/windows/)。常用的操作有：  
```python
    # 窗口最大化
driver.maximize_window()
    # 窗口最小化
driver.minimize_window()
    # 全屏幕截图
driver.save_screenshot('./image.png')
    # 对某个元素截图
ele = driver.find_element(By.CSS_SELECTOR, 'h1')
ele.screenshot('./image.png')

    # 执行js脚本
header = driver.find_element(By.CSS_SELECTOR, "h1")
    # 执行脚本以获得header元素的文本内容
driver.execute_script('return arguments[0].innerText', header)
    # 其他js脚本例子：
lessonName = browser.execute_script("return document.getElementsByClassName('function-desc-text-desc'')[0].textContent")
    # 又如查询视频时长：
js = '''
        var vd = document.querySelector("video");
        return vd.duration;
    '''
duration = browser.execute_script(js)
    
```