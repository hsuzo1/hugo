---
title: "Sqlalchemy中Query的方法汇总"
date: 2021-01-14T09:53:16+08:00
draft: false
categories: ["tech"]
Tags : ["flask", "python", "sqlalchemy"]
keywords : "flask sqlalchemy query "
---


**SQLAlchemy** 是**flask**中必备的库，能操作各种关系数据库。数据库映射的方法在[廖雪峰](https://www.liaoxuefeng.com/wiki/897692888725344/955081460091040)的官方网站上有具体例子。本文主要是记录一下SQLAlchemy通过 session 对象执行查询的各种 **query** 方法。

### 遍历
```python
for user in session.query(User).order_by(User.id):
    print(user.name, user.fullname)

执行结果：
ed Ed Jones
wendy Wendy Williams
mary Mary Contrary
fred Fred Flinstone
```

或者：
```python
for name, fullname in session.query(User.name, User.fullname):
    print(name, fullname)

# 执行结果：
ed Ed Jones
wendy Wendy Williams
mary Mary Contrary
fred Fred Flinstone
```

再看一个例子：
```python
for name in session.query(User.name):
    print(name)

# 执行结果：
('ed',)
('wendy',)
('mary',)
('fred',)
```

这时，返回结果为元组。

--------------------------------------------------------------------------------

### 设置别名

给返回的结果起一个别名（label）：
```python
for row in session.query(User.name.label('name_label')).all():
    print(row.name_label)

# 执行结果：
ed
wendy
mary
fred
```

给数据表对象设置别名（aliased）：
```python
from sqlalchemy.orm import aliased

# 把 User 别名设置为：user_alias
user_alias = aliased(User, name='user_alias')

for row in session.query(user_alias):
    print(row)

# 执行结果：
ed
wendy
mary
fred
```

--------------------------------------------------------------------------------

### 切片
```python
for u in session.query(User).order_by(User.id)[1:3]:
    print(u)

# 执行结果：
wendy
mary
```

--------------------------------------------------------------------------------

### 查询

查询，也就是 SQL 的 where 子句。在 SQLAlchemy 中，query 的查询可以通过 filter_by 或 filter 来实现。

1.使用 filter_by：
```python
for name, in session.query(User.name).\
    filter_by(fullname='Ed Jones'):
    print(name)

# 执行结果：
ed
```

2.使用 filter：
```python
for name, in session.query(User.name).\
    filter(User.fullname=='Ed Jones'):
    print(name)

# 执行结果：
ed
```

--------------------------------------------------------------------------------

### 运算符

1. 等于：
```python
query.filter(User.name == 'ed')
```

2. 不等于
```python
query.filter(User.name != 'ed')
```

3. LIKE 运算符：
```python
# 查询所有 "e" 开头的 name
for user in session.query(User).\
    filter(User.name.like("e%")):
    print(user)

# 执行结果：
ed
```

4. IN 运算符：
```python
for user in session.query(User).\
    filter(User.name.in_(['ed', 'wendy', 'jack'])):
    print(user)

# 执行结果：
ed
wendy
```

5. NOT IN 运算符：
```python
for user in session.query(User).\
    filter(~User.name.in_(['ed', 'wendy', 'jack'])):
    print(user)

# 执行结果：
mary
fred
```

6. IS NULL 运算符：
```python
query.filter(User.name == None)

query.filter(User.name.is_(None))
```

7. IS NOT NULL 运算符：
```python
query.filter(User.name != None)

query.filter(User.name.isnot(None))
```

8. AND 运算符：

方法1：
```python
from sqlalchemy import and_

for user in session.query(User).\
    filter(and_(User.name == 'ed', User.fullname == 'Ed Jones')):
    print(user)

# 执行结果：
ed
```

方法2：
```python
for user in session.query(User).\
    filter(User.name == 'ed', User.fullname == 'Ed Jones'):
    print(user)
```

方法3：
```python
for user in session.query(User).\
    filter(User.name == 'ed').\
    filter(User.fullname == 'Ed Jones'):
    print(user)
```

9. OR 运算符：
```python
from sqlalchemy import or_

for user in session.query(User).\
    filter(or_(User.name == 'ed', User.name == 'wendy')):
    print(user)

# 执行结果：
ed
wendy
```

--------------------------------------------------------------------------------

### query 包含的方法

1. all() ：

返回一个包含所有对象的 list
```python
u = session.query(User).all()
print(u)

# 执行结果：
[<__main__.User object at 0x033DA410>, 
<__main__.User object at 0x033DA470>, 
<__main__.User object at 0x033DA4B0>, 
<__main__.User object at 0x033DA510>]
```

2. first() ：
```python
返回结果的第一个对象

u = session.query(User).first()
print(u)

# 执行结果：
ed
```

3. one() ：

返回结果的实例对象
```python
u = session.query(User).filter(User.id == 1).one()
print(u)
print(u.fullname)

# 执行结果：
ed
Ed Jones
```

如果有多个结果返回，使用 one() 将会报错：
```python
u = session.query(User).one()

# 错误信息
Traceback (most recent call last):
...
MultipleResultsFound: Multiple rows were found for one()
```

如果查找的对象不存在，也会报错：
```python
u = session.query(User).filter(User.id == 99).one()

# 错误信息
Traceback (most recent call last):
...
NoResultFound: No row was found for one()
```

我们可以用 one_or_none() 来处理对象未必存在的情况：
```python
u = session.query(User).filter(User.id == 99).one_or_none()
print(u)

# 执行结果：
None
```

使用 scalar() 方法，返回结果的第一列的数值：
```python
u = session.query(User.id).filter(User.name == 'wendy').scalar()
print(u)

# 执行结果：
2
```

--------------------------------------------------------------------------------

### 文本化 SQL

使用 text() 方法可以用文本化的方式执行查询，使得语法更灵活，多数方法都支持这种文本化的方式，比如：filter() 和 order_by()。
```python
from sqlalchemy import text

# 筛选 id<3 再根据 id 排序
for user in session.query(User).\
    filter(text("id<3")).\
    order_by(text("id")).all():
    print(user.name)

# 执行结果：
ed
wendy
```

需要用冒号传递参数时，可以用 params() 方法：
```python
from sqlalchemy import text

# 筛选出 id<5 和 name='fred'
u = session.query(User).\
    filter(text("id<:value and name=:name")).\
    params(value=5, name='fred').order_by(User.id).one()

print(u)

# 执行结果：
fred
```

使用 from_statement() 方法，支持直接写原生 SQL 语句：
```python
from sqlalchemy import text

u = session.query(User).\
    from_statement(text(" SELECT * FROM users where name='ed' ")).one()

print(u)

# 执行结果：
ed
```

--------------------------------------------------------------------------------

### 计数

返回查询结果的对象的数目。
```python
u = session.query(User).\
    filter(User.name.like('%ed')).count()

print(u)

# 执行结果：
2
```

需要单独计算某一类对象的数量，可以用 func() 和 group_by() 配合查询：
```python
from sqlalchemy import func

u = session.query(User.name, func.count(User.name)).group_by(User.name).all()
print(u)

# 执行结果：
[('ed', 1), ('fred', 1), ('mary', 1), ('wendy', 1)]
```

返回查询得到的的数值，我们使用 scalar() 方法：
```python
u = session.query(func.count(User.id)).scalar()
print(u)

# 执行结果：
4
```


## 其他例子汇总

```python
0.like:模糊查询
result0 = db.session.query(Protocols.protocolName).filter(Protocols.protocolName.like("%" + "cp" + "%")).all()
1.notlike：模糊查询，不在查询范围内
result1 = db.session.query(Protocols.protocolName).filter(Protocols.protocolName.notlike("%" + "cp" + "%")).all()
2.in_:在某个范围内，参数为元组或者列表类型的数据
result2 = db.session.query(Protocols.protocolName).filter(Protocols.id.in_((1, 2))).all()
3.notin_：和in_相反
result3 = db.session.query(Protocols.protocolName).filter(Protocols.id.notin_((1, 2))).all()
4.is_:是否为null的比较
result4 = db.session.query(Protocols.protocolName).filter(Protocols.protocolName.is_(None)).all()
5.isnot:不为null
result5 = db.session.query(Protocols.protocolName).filter(Protocols.protocolName.isnot(None)).all()
6.startswith：以某个数据开头
result6 = db.session.query(Protocols.protocolName).filter(Protocols.protocolName.startswith("t")).all()
7.endswith：以某数据结尾
result7 = db.session.query(Protocols.protocolName).filter(Protocols.protocolName.endswith("t")).all()
8.contains：数据中包含，和like功能差不多
result8 = db.session.query(Protocols.protocolName).filter(Protocols.protocolName.contains("cp")).all()
9.desc：对查询出来的数据进行降序排序
result9 = db.session.query(Protocols.protocolName).order_by(Protocols.id.desc()).all()
10.asc：对查询出来的数据进行升序排序
result10 = db.session.query(Protocols.protocolName).order_by(Protocols.id.asc()).all()
11.between：某个字段的参数在某个范围内
result11 = db.session.query(Protocols.protocolName).filter(Protocols.id.between(1, 3)).all()
12.distinct：对查询出来的数据进行去重
result12 = db.session.query(Protocols.parent_protocol).distinct().all()
```