---
title: "Flask动态切换选项并通过Fetch查询显示"
date: 2021-01-30T08:21:07+08:00
draft: false
categories: ["tech"]
Tags : ["flask", "python", "jquery"]
keywords : "flask wtform query "
---


[Flask-WTF](https://pypi.org/project/Flask-WTF/)提供了WTForms的功能集成，使用该模块能快捷创建表单页面，包含有 Flask 和 WTForms， 集成了 CSRF， file upload 以及 reCAPTCHA.。目前版本为0.14.3，使用说明文档在[https://flask-wtf.readthedocs.io/en/stable/](https://flask-wtf.readthedocs.io/en/stable/)。
  
**具体功能如下:** 
- 集成 wtforms。
- 带有 csrf 令牌的安全表单。
- 全局的 csrf 保护。
- 支持验证码（Recaptcha）。
- 与 Flask-Uploads 一起支持文件上传。
- 国际化集成。

### 安装方法
```python
pip install Flask-WTF
```

### 创建表单
```python
from flask_wtf import Form
from wtforms import StringField
from wtforms.validators import DataRequired

class MyForm(Form):
    name = StringField('name', validators=[DataRequired()])
```

另外，隐藏的 CSRF 令牌会被自动地创建。你可以在模板这样地渲染它:
```python
<form method="POST" action="/">
    {{ form.csrf_token }}
    {{ form.name.label }} {{ form.name(size=20) }}
    <input type="submit" value="Go">
</form>
```
尽管如此，为了创建有效的 XHTML/HTML， Form 类有一个 hidden_tag 方法， 它在一个隐藏的 DIV 标签中渲染任何隐藏的字段，包括 CSRF 字段:
```python
<form method="POST" action="/">
    {{ form.hidden_tag() }}
    {{ form.name.label }} {{ form.name(size=20) }}
    <input type="submit" value="Go">
</form>
```  

### 验证表单
在你的视图处理程序中验证请求:
```python
@app.route('/submit', methods=('GET', 'POST'))
def submit():
    form = MyForm()
    if form.validate_on_submit():
        return redirect('/success')
    return render_template('submit.html', form=form)
```
注意你不需要把 request.form 传给 Flask-WTF；Flask-WTF 会自动加载。便捷的方法 validate_on_submit 将会检查是否是一个 POST 请求并且请求是否有效。
  


## 一个使用fetch查询结果的WTF实例  
- 前端页面 **index.html**：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <form method="POST">
    {{ form.csrf_token }}
    {{ form.state }}
    {{ form.city }}
    <input type="submit">
    </form>
    <script>
        var state_select = document.getElementById("state");
        var city_select = document.getElementById("city");

        state_select.onchange = function()  {
             
            state = state_select.value;
            
            fetch('/city/' + state).then(function(response) {
                response.json().then(function(data) {
                    var optionHTML = '';
                    for (var city of data.cities) {
                        optionHTML += '<option value="' + city.id + '">' + city.name + '</option>';
                    }
                    city_select.innerHTML = optionHTML;
                })
            });
        }
    </script>
</body>
</html>
```  

- 服务器后台视图 **app.py**:
```python
from flask import Flask, render_template, request, jsonify
from flask_sqlalchemy import SQLAlchemy 
from flask_wtf import FlaskForm 
from wtforms import SelectField

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///test.db'
app.config['SECRET_KEY'] = 'secret'

db = SQLAlchemy(app)

class City(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    state = db.Column(db.String(2))
    name = db.Column(db.String(50))

class Form(FlaskForm):
    state = SelectField('state', choices=[('CA', 'California'), ('NV', 'Nevada')]) 
    city = SelectField('city', choices=[])

@app.route('/', methods=['GET', 'POST'])
def index():
    form = Form()
    form.city.choices = [(city.id, city.name) for city in City.query.filter_by(state='CA').all()]

    if request.method == 'POST':
        city = City.query.filter_by(id=form.city.data).first()
        return '<h1>State: {}, City: {}</h1>'.format(form.state.data, city.name)

    return render_template('index.html', form=form)

@app.route('/city/<state>')
def city(state):
    cities = City.query.filter_by(state=state).all()

    cityArray = []

    for city in cities:
        cityObj = {}
        cityObj['id'] = city.id
        cityObj['name'] = city.name
        cityArray.append(cityObj)
    return jsonify({'cities' : cityArray})

if __name__ == '__main__':
    app.run(debug=True,host="0.0.0.0")
```