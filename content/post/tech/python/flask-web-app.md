---
title: "用Flask建立一个简单的Web App"
date: 2020-12-29T13:07:19+08:00
draft: false
categories: ["tech"]
Tags : ["python", "flask", "mssql"]
keywords : "张旭州 flask web mssql jinja2 python "
---


SQLAlchemy is the Python SQL toolkit and Object Relational Mapper that gives application developers the full power and flexibility of SQL.It provides a full suite of well known enterprise-level persistence patterns, designed for efficient and high-performing database access, adapted into a simple and Pythonic domain language.
网上的很多教程和视频都是基于本地**SQLite**数据库进行的操作，因为我个人的项目是在MSSQL上的，所以这里对**Flask**使用**MS SQL Server**的方法进行记录。
>目标：
>1. MSSQL数据库存储数据
>2. 有增删改功能
>3. 使用网页模板  

{{< image src="http://blogstatic.cn-gd.ufileos.com/tech2.gif"  position="center" style="border-radius: 8px;" alt="flask-with-mssql" caption="Flask" >}}

### 1. app.py
```python
# -*- coding: utf-8 -*-
from flask import Flask, render_template, url_for, request, redirect
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.dialects.mssql import \
    BIGINT, BINARY, BIT, CHAR, DATE, DATETIME, DATETIME2, \
    DATETIMEOFFSET, DECIMAL, FLOAT, IMAGE, INTEGER, MONEY, \
    NCHAR, NTEXT, NUMERIC, NVARCHAR, REAL, SMALLDATETIME, \
    SMALLINT, SMALLMONEY, SQL_VARIANT, TEXT, TIME, \
    TIMESTAMP, TINYINT, UNIQUEIDENTIFIER, VARBINARY, VARCHAR
from sqlalchemy import Table, MetaData, Column, Integer, String, ForeignKey
from sqlalchemy.orm import mapper, sessionmaker


from datetime import datetime
import pymssql

app = Flask(__name__)

DBUser = 'flask'
DBPassword = '123'
DBHost = '192.168.100.5'
DBName = 'test'
engine = create_engine(f'mssql+pymssql://{DBUser}:{DBPassword}@{DBHost}/{DBName}', echo=True)

# 映射基类
Base = declarative_base()

# 具体映射类
class Flaskdemo(Base):
    # 指定映射表名
    __tablename__ = 'flaskdemo'

    # id 设置为主键
    id = Column('id', Integer, primary_key=True, autoincrement=True)
    # 指定 name 映射到 name 字段
    content = Column('content', String(200))
    date_created = Column('date_created', DATETIME, default=datetime.utcnow()) # datetime.today().strftime("%Y-%m-%d")

# 创建表
# Base.metadata.create_all(engine)
# 建立会话
Session = sessionmaker(bind=engine)
# 创建 Session 类实例
session = Session()


@app.route('/', methods=['POST', 'GET'])
def index():
    if request.method == 'POST':
        task_content = request.form['content']
        new_task = Flaskdemo(content=task_content)
        try:
            session.add(new_task)
            session.commit()
            return redirect('/')
        except:
            return 'There was an issue adding your task'
    else:
        tasks = session.query(Flaskdemo).all()
        return render_template('index.html', tasks=tasks)


@app.route('/delete/<int:id>')
def delete(id):
    task_to_delete = session.query(Flaskdemo).filter(Flaskdemo.id==id).one()
    try:
        session.delete(task_to_delete)
        session.commit()
        return redirect('/')
    except:
        return 'There was a problem deleting that task'

@app.route('/update/<int:id>', methods=['GET', 'POST'])
def update(id):
    task = session.query(Flaskdemo).filter(Flaskdemo.id==id).one()
    if request.method == 'POST':
        task.content = request.form['content']
        try:
            session.commit()
            return redirect('/')
        except:
            return 'There was an issue updating your task'

    else:
        return render_template('update.html', task=task)


if __name__ == "__main__":
    app.run(debug=True)
    # 关闭
    session.close()
```

### 2. index.html
```html
{% extends 'base.html' %}

{% block head %}
<title>Task Master</title>
{% endblock %}

{% block body %}
<div class="content">
    <h1 style="text-align: center">Task Master</h1>
    {% if tasks|length < 1 %}
    <h4 style="text-align: center">There are no tasks. Create one below!</h4>
    {% else %}
    <table>
        <tr>
            <th>Task</th>
            <th>Added</th>
            <th>Actions</th>
        </tr>
        {% for task in tasks %}
            <tr>
                <td>{{ task.content }}</td>
                <td>{{ task.date_created }}</td>
                <td>
                    <a href="/delete/{{task.id}}">Delete</a>
                    <br>
                    <a href="/update/{{task.id}}">Update</a>
                </td>
            </tr>
        {% endfor %}
    </table>
    {% endif %}

    <div class="form">
        <form action="/" method="POST">
            <input type="text" name="content" id="content">
            <input type="submit" value="Add Task">
        </form>
    </div>
</div>
{% endblock %}
```

### 3. update.html
```html
{% extends 'base.html' %}

{% block head %}
<title>Task Master</title>
{% endblock %}

{% block body %}
<div class="content">
    <h1 style="text-align: center">Update Task</h1>

    <div class="form">
        <form action="/update/{{task.id}}" method="POST">
            <input type="text" name="content" id="content" value="{{task.content}}">
            <input type="submit" value="Update">
        </form>
    </div>
</div>
{% endblock %}
```

### 4. base.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <link rel="stylesheet" href="{{ url_for('static', filename='css/main.css') }}">
    {% block head %}{% endblock %}
</head>
<body>
    {% block body %}{% endblock %}
</body>
</html>
```

### 5. main.css
```css
body, html {
    margin: 0;
    font-family: sans-serif;
}

.content {
    margin: 0 auto;
    width: 400px;
}

table, td, th {
    border: 1px solid #aaa;
}

table {
    border-collapse: collapse;
    width: 100%;
}

th {
    height: 30px;
}

td {
    text-align: center;
    padding: 5px;
}

.form {
    margin-top: 20px;
}

#content {
    width: 70%;
}
```