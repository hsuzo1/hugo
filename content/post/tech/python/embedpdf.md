---
title: "使用Streamlit在Web中嵌入显示PDF文件"
date: 2020-12-23T20:16:15+08:00
draft: false
categories: ["tech"]
Tags : ["PDF", "python", "streamlit"]
---

本文主要记录了使用streamlit在webapp中显示PDF文件的方法。  
Streamlit是一个如此出色的框架，可让您构建多个出色的数据科学应用程序。在本教程中，我们将了解如何将PDF文件嵌入我们的流式应用程序。
为了做到这一点，我们只需要步：
- st.markdown（）：这将使我们能够在应用程序中使用相同格式的HTML嵌入文件。
- base64编码：为了能够通过我们发送pdf文件，我们需要在打开文件后使用base64编码对其进行编码。没有这个，您将无法看到显示的pdf –您只会收到404错误或其他错误。

```python
import streamlit as st
import base64

# 显示PDF文件的函数
def st_display_pdf(pdf_file):
    with open(pdf_file, "rb") as f:
        base64_pdf = base64.b64encode(f.read()).decode('utf-8')
    pdf_display = f'<embed src="data:application/pdf;base64,{base64_pdf}" width="800" height="1000" type="application/pdf">'
    st.markdown(pdf_display, unsafe_allow_html=True)

def main():
    st.title("在Streamlit中嵌入PDF文件")
    st.subheader("Learn Streamlit")

    st_display_pdf("PDFembed.pdf")

if __name__ == '__main__':
    main()

```

