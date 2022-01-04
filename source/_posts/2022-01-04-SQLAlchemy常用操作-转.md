---
title: SQLAlchemy常用操作(转)
excerpt: ORM全称ObjectRelationalMapping,即对象关系映射。简洁的说，ORM将数据库中的表与面向对象语言表达中的类创建了一类对应关系。那样，我们要操作数据库，数据库中的表或是表中的一条记录就可以直接根据操作类或是类案例来完成。
toc: true
date: 2022-01-04 10:15:27
updated: 2022-01-04 10:15:27
tags:
categories:
---

原文: <https://juejin.cn/post/7029596479492718623>

ORM全称 `ObjectRelationalMapping` ,即对象关系映射。简洁的说，ORM将数据库中的表与面向对象语言表达中的类创建了一类对应关系。那样，我们要操作数据库，数据库中的表或是表中的一条记录就可以直接根据操作类或是类案例来完成。

SQLAlchemy是Python社区最知名的ORM工具之一，为高效和性能的数据库访问设计方案，完成了完整的企业级持久模型。

SQLAlchemy优点：

1. **简洁易读**：将数据表抽象为对象（数据模型），更形象化易读。
2. **可移植**：封装了多种数据库引擎，应对多个数据库，实际操作基本相同，代码易维护。
3. **更安全**：有效避免SQL注入。

本文通过介绍Sqlite数据库的常见实际操作，来介绍一下SQLAlchemy的使用方法。SQLAlchemy具体的建立方式是将数据库表变换为Python类，其中数据列作为属性，数据库操作作为方法。

## SQLAlchem安装

Sqlite3是Python3标准库不需要另外安装，只需要安装SQLAlchemy即可。

```bash
pip install sqlalchemy
```

## ORM 创建数据库连接

Sqlite3 创建数据库连接就是创建数据库，而其他MySQL等数据库，需要数据库已存在，才能创建数据库连接。

### SQLite

以相对路径形式，在当前目录下创建数据库格式如下：

```python
from sqlalchemy import create_engine

engine = create_engine('sqlite:///AiTestOps.db')
```

以绝对路径形式创建数据库，格式如下：

```python
from sqlalchemy import create_engine

engine = create_engine('sqlite:///G:\python_sql\AiTestOps.db')
```

我们以在当前目录下创建SQLite数据库为例，后续各步同使用此数据库。我们在`create_engine`方法中补充了两个参数。如下：

```python
from sqlalchemy import create_engine

engine = create_engine('sqlite:///AiTestOps.db?check_same_thread=False', echo=True)
```

* **echo** ：`echo`默认为`False`，表示不打印执行的SQL语句等较详细的执行信息，改为`Ture`表示让其打印。
* **check_same_thread**：`check_same_thread`默认为 `False`，`sqlite`默认建立的对象只能让建立该对象的线程使用，而`sqlalchemy`是多线程的，所以我们需要指定`check_same_thread=False`来让建立的对象任意线程都可使用。

### 其它常用数据库

SQLAlchemy用一个字符串表示连接信息：

```text
'数据库类型+数据库驱动名称://用户名:密码@IP地址:端口号/数据库名'
```

#### PostgreSQL

```python
from sqlalchemy import create_engine

# default, 连接串格式为 "数据库类型+数据库驱动://数据库用户名:数据库密码@IP地址:端口/数据库"
engine = create_engine('postgresql://username:password@127.0.0.1:9527/AiTestOps')
# psycopg2
engine = create_engine('postgresql+psycopg2://username:password@127.0.0.1:9527/AiTestOps')
# pg8000
engine = create_engine('postgresql+pg8000://username:password@127.0.0.1:9527/AiTestOps')
```

#### MySQL

```python
from sqlalchemy import create_engine

# default,连接串格式为 "数据库类型+数据库驱动://数据库用户名:数据库密码@IP地址:端口/数据库"
engine = create_engine('mysql://username:password@127.0.0.1:9527/AiTestOps')
# mysql-python
engine = create_engine('mysql+mysqldb://username:password@127.0.0.1:9527/AiTestOps')
# MySQL-connector-python
engine = create_engine('mysql+mysqlconnector://username:password@127.0.0.1:9527/AiTestOps')
```

#### Oracle

```python
from sqlalchemy import create_engine

# default,连接串格式为 "数据库类型+数据库驱动://数据库用户名:数据库密码@IP地址:端口/数据库"
engine = create_engine('oracle://username:password@127.0.0.1:9527/AiTestOps')
# cx_oracle
engine = create_engine('oracle+cx_oracle://username:password@127.0.0.1:9527/AiTestOps')
```

## 定义映射

首先，我们建立基本映射类，后边具体的映射类（表）需要继承它。

```python
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base()
```

然后，创建具体的映射类，我们这里以`Person`映射类为例，我们把`Person类`映射到`Person表`。

```python
from sqlalchemy import Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

# 定义映射类Person，并继承 Base
class Person(Base):
    # 指定本类映射到 Person 表
    __tablename__ = 'Person'
    # 若有多个类指向同一张表，那么在后边的类需要把 extend_existing设为True，表示在已有列基础上进行扩展
    # 或者换句话说，sqlalchemy 允许类是表的字集，如下：
    # __table_args__ = {'extend_existing': True}
    # 若表在同一个数据库服务（datebase）的不同数据库中（schema），可使用schema参数进一步指定数据库
    # __table_args__ = {'schema': 'AiTestOps_database'}

    # sqlalchemy 强制要求必须要有主键字段不然会报错，sqlalchemy在接收到查询结果后还会自己根据主键进行一次去重，因此不要随便设置非主键字段设为primary_key
    # 各变量名一定要与表的各字段名一样，因为相同的名字是他们之间的唯一关联关系，指定 person_id 映射到 person_id 字段; person_id 字段为整型，为主键，自动增长（其实整型主键默认就自动增长）
    person_id = Column(Integer, primary_key=True, autoincrement=True)
    # 指定 username 映射到 username 字段; username 字段为字符串类形，
    # 指定 username 映射到 username 字段; username 字段为字符串类形，
    username = Column(String(20), nullable=False, index=True)
    password = Column(String(32))
    desc = Column(String(32))

    # __repr__方法用于输出该类的对象被print()时输出的字符串
    def __repr__(self):
        return "<User(username='%s', password='%s', desc='%s')>" % (
            self.username, self.password, self.desc)
```

首先要明确下，ORM中一般情况下表是不需要先存在的，我们看到，在 `Person` 类中，用 `__tablename__` 指定在 `SQLite` 中表的名字。

我们在`Person`中创建了三个字段，类中的每一个 `Column` 代表数据库中的一列（字段），在 `Colunm`中，指定该列的一些属性。第一个字段代表数据类型，上面我们使用 `String`, `Integer` 两个最常用的类型，其他常用的包括：`Text`、`Boolean`、`SmallInteger`、`DateTime`。

`nullable=False` 代表这一列不可以为空，`index=True` 表示在该列创建索引。另外，定义 `repr` 是为了方便调试。

在上面的`Person类`映射定义中，`__tablename__`属性是静态的，但有时我们可能想通过外部动态的给类传递表名，此时可以通过定义内部类进行传参的方式来实现，如下：

```python
from sqlalchemy import Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base

def table_name_model_class(table_name, Base=declarative_base()):
    # 定义一个内部类
    class User_Model(Base):
        # 给表名赋值
        __tablename__ = table_name
        __table_args__ = {'extend_existing': True}

        person_id = Column(Integer, primary_key=True, autoincrement=True)
        # 指定 username 映射到 username 字段; username 字段为字符串类形，
        username = Column(String(20), nullable=False, index=True)
        password = Column(String(32))
        desc = Column(String(32))
        
        def __repr__(self):
            return "<User(username='%s', password='%s', desc='%s')>" % (
                self.username, self.password, self.desc)
    # 把动态设置表名的类返回去
    return User_Model

if __name__ == '__main__':
    TestModel = table_name_model_class("Person_Info")
    print(TestModel.__table__)
```

## 创建数据表

**查看映射对应的表**

```python
Person.__table__
```

**创建所有继承于Base的类对应的表**

```python
Base.metadata.create_all(engine, checkfirst=True)
```

`checkfirst`默认为`True`，表示创建表前先检查该表是否存在，若同名表已存在，则不再创建。

**创建指定表**

```python
Base.metadata.create_all(engine, tables=[Base.metadata.tables['Person']], checkfirst=True)
# 或者是
Person.__table__.create(engine, checkfirst=True)
```

## 建立会话

```python
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine
# 创建链接 
engine = create_engine(r'sqlite:///AiTestOps.db?check_same_thread=False', echo=True)
# 创建Session类对象
Session = sessionmaker(bind=engine)
# 创建Session类实例
session = Session()
```

## 插入数据

```python
from sqlalchemy import Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine

def table_name_model_class(table_name, Base = declarative_base()):
    # 定义一个内部类
    class User_Model(Base):
        # 给表名赋值
        __tablename__ = table_name
        __table_args__ = {'extend_existing': True}

        person_id = Column(Integer, primary_key=True, autoincrement=True)
        # 指定 username 映射到 username 字段; username 字段为字符串类形，
        username = Column(String(20))
        password = Column(String(32))
        desc = Column(String(32))
        
        def __repr__(self):
            return "<User(username='%s', password='%s', desc='%s')>" % (
                self.username, self.password, self.desc)
    # 把动态设置表名的类返回去
    return User_Model

if __name__ == '__main__':

    Person = table_name_model_class("Person")
    # 创建链接
    engine = create_engine(r'sqlite:///AiTestOps.db?check_same_thread=False', echo=True)
    # 创建 Person 表
    Person.__table__.create(engine, checkfirst=True)
    # 创建Session类对象
    Session = sessionmaker(bind=engine)
    # 创建Session类实例
    session = Session()
    # 创建User类实例
    jon_info = Person(username='Jon', password='123456', desc='活泼')

    # 将该实例插入到 Person 表
    session.add(jon_info)

    # 一次插入多条记录形式
    session.add_all(
        [
        Person(username='Mark', password='123456', desc='活泼'),
        Person(username='Tony', password='123456', desc='活泼')
        ]
    )

    # 当前更改只是在session中，需要使用commit确认更改才会写入数据库
    session.commit()
```

## 查询数据

```python
from sqlalchemy import Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine

def table_name_model_class(table_name, Base = declarative_base()):
    # 定义一个内部类
    class User_Model(Base):
        # 给表名赋值
        __tablename__ = table_name
        __table_args__ = {'extend_existing': True}

        person_id = Column(Integer, primary_key=True, autoincrement=True)
        # 指定 username 映射到 username 字段; username 字段为字符串类形，
        username = Column(String(20))
        password = Column(String(32))
        desc = Column(String(32))
        
        def __repr__(self):
            return "<User(username='%s', password='%s', desc='%s')>" % (
                self.username, self.password, self.desc)
    # 把动态设置表名的类返回去
    return User_Model

if __name__ == '__main__':

    Person = table_name_model_class("Person")
    # 创建链接
    engine = create_engine(r'sqlite:///AiTestOps.db?check_same_thread=False', echo=True)
    # 创建 Person 表
    Person.__table__.create(engine, checkfirst=True)
    # 创建Session类对象
    Session = sessionmaker(bind=engine)
    # 创建Session类实例
    session = Session()
    # 一次插入多条记录形式
    session.add_all(
        [
        Person(username='Mark', password='123456', desc='活泼'),
        Person(username='Tony', password='123456', desc='活泼')
        ]
    )
    # 当前更改只是在session中，需要使用commit确认更改才会写入数据库
    session.commit()
    
    # 查询 username='Mark' 的所有结果，返回结果对象
    mark = session.query(Person).filter_by(username='Mark').all()
    print(mark)

    # 如果只获取部分字段，那么返回的就是元组而不是对象了
    mark_desc = session.query(Person.desc).filter_by(username='Mark').all()
    print(mark_desc)
```

为了更好的理解 `SQL` 与 `SQLalchemy` 的写法区别，可以参照以下内容：

* **query** ：对应 SELECT xxx FROM xxx
* **filter/filter_by** ：对应 `WHERE` ，**fillter** 可以进行比较运算(`==`, `>`, `<` ...)来对条件进行灵活的运用，不同的条件用逗号分割，`fillter_by` 只能指定参数传参来获取查询结果。
* **limit** ：对应 `limit()`
* **order by** ：对应 `order_by()`
* **group by** ：对应 `group_by()`

### like查询

```python
# like 
data_like = session.query(Person).filter(Person.desc.like("活%")).all()
# not like
data_like = session.query(Person).filter(Person.desc.notlike("活%")).all()
```

### is查询

```python
# is_ 相当于 ==
result = session.query(Person).filter(Person.username.is_(None)).all()
result = session.query(Person).filter(Person.username == None).all()
# isnot 相当于 !=
result = session.query(Person).filter(Person.username.isnot(None)).all()
result = session.query(Person).filter(Person.username != None).all()
```

### 正则查询

```python
data_regexp = session.query(Person).filter(Person.password.op("regexp")(r"^[\u4e00-\u9fa5]+")).all()
```

### 统计数量

```python
data_like_count = session.query(Person).filter(Person.desc.like("活%")).count()
```

### IN 查询

```python
more_person = session.query(Person).filter(Person.username.in_(['Mark', 'Tony'])).all()
```

### NOT IN 查询

```python
# ~代表取反,转换成sql就是关键字not
more_person = session.query(Person).filter(~Person.username.in_(['Mark', 'Tony'])).all()
# 或 notin_
more_person = session.query(Person).filter(Person.username.notin_(['Mark', 'Tony'])).all()
```

### AND 查询

```python
from sqlalchemy import and_

more_person = session.query(Person).filter(and_(Person.password=='123456',Person.desc=="可爱'")).all()
```

### OR 查询

```python
from sqlalchemy import or_

more_person = session.query(Person).filter(or_(Person.password=='123456',Person.desc=="活泼'")).all()
```

### 分组查询

```python
std_group_by = session.query(Person).group_by(Person.desc).all()
# 或是
from sqlalchemy.sql import func

res = session.query(Person.desc,
                    func.count(Person.desc),
                   ).group_by(Person.desc).all()

# 遍历查看，已无ed用户记录
for person in res:
    print(person)
```

### 排序查询

```python
std_order_by = session.query(Person).order_by(Person.username.desc()).all()
```

### limit 查询

```python
# limit 限制数量查询, limit里传入一个整型来约束查看的数量, 当limit里面的参数大于实例表中的数量时，会返回所有的查询结果
data_limit = session.query(Person).filter(Person.desc.notlike("活%")).limit(1).all()
```

### 偏移量查询

```python
# offset 偏移量查询,offset中传入一个整型，从表中的该位置开始查询，offset可以和limit混用来进行限制
data_like = session.query(Person).filter(Person.desc.like("活%")).offset(1).all()
result = session.query(Person).offset(1).limit(6).all()
```

### 聚合函数

```python
from sqlalchemy import func, extract
# count
result = session.query(Person.password, func.count(Person.id)).group_by(Person.password).all()
# sum
result = session.query(Person.password, func.sum(Person.id)).group_by(Person.password).all()
# max
result = session.query(Person.password, func.max(Person.id)).group_by(Person.password).all()
# min
result = session.query(Person.password, func.min(Person.id)).group_by(Person.password).all()
# having
result = session.query(Person.password, func.count(Person.id)).group_by(Person.password).having(func.count(Person.id) > 1).all()
```

### 关于返回结果数量

```python
all()
- 会触发SQL应用到数据库
- 查询所有
- 返回一个列表对象

first()
- 会触发SQL应用到数据库
- 查询第一个符合条件的对象
- 返回一个对象
```

### 关于传参

```python
filter = (Person.username=='Mark')

our_user = session.query(Person).filter(filter).first()
print(our_user)
```

## 更新

```python
from sqlalchemy import Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine

def table_name_model_class(table_name, Base = declarative_base()):
    # 定义一个内部类
    class User_Model(Base):
        # 给表名赋值
        __tablename__ = table_name
        __table_args__ = {'extend_existing': True}

        person_id = Column(Integer, primary_key=True, autoincrement=True)
        # 指定 username 映射到 username 字段; username 字段为字符串类形，
        username = Column(String(20))
        password = Column(String(32))
        desc = Column(String(32))
        
        def __repr__(self):
            return "<User(username='%s', password='%s', desc='%s')>" % (
                self.username, self.password, self.desc)
    # 把动态设置表名的类返回去
    return User_Model

if __name__ == '__main__':

    Person = table_name_model_class("Person")
    # 创建链接
    engine = create_engine(r'sqlite:///AiTestOps.db?check_same_thread=False', echo=True)
    # 创建 Person 表
    Person.__table__.create(engine, checkfirst=True)
    # 创建Session类对象
    Session = sessionmaker(bind=engine)
    # 创建Session类实例
    session = Session()
    # 一次插入多条记录形式
    session.add_all(
        [
            Person(username='Mark', password='123456', desc='活泼'),
            Person(username='Tony', password='123456', desc='活泼')
        ]
    )
    # 当前更改只是在session中，需要使用commit确认更改才会写入数据库
    session.commit()
    
    # 要修改需要先将记录查出来
    person = session.query(Person).filter_by(username='Mark').first()
    # 将 Mark 用户的密码修改为 654321
    person.password = '654321'
    # 确认修改
    session.commit()
    
    our_user = session.query(Person.password).filter_by(username='Mark').all()
    print(our_user)
```

上边的操作，先进行查询再修改，相当于执行了两条语句，我们可直接使用如下方法

```python
session.query(Person).filter_by(username='Mark').update({Person.password: '6543210'})
session.commit()
```

以同schema的一张表更新另一张表的写法，在跨表的`update/delete`等函数中要注明`synchronize_session=False`，否则报错：

```python
session.query(Person).filter_by(Person.username=Person1.username).update({Person.password: Person1.password}, synchronize_session=False)
session.commit()
```

以一个schema的表更新另一个schema的表的写法，写法与同一schema的一样，只是定义model时需要使用`table_args = {'schema': 'test_Person'}`等形式指定表对应的schema。

## 删除

```python
from sqlalchemy import Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine

def table_name_model_class(table_name, Base = declarative_base()):
    # 定义一个内部类
    class User_Model(Base):
        # 给表名赋值
        __tablename__ = table_name
        __table_args__ = {'extend_existing': True}

        person_id = Column(Integer, primary_key=True, autoincrement=True)
        # 指定 username 映射到 username 字段; username 字段为字符串类形，
        username = Column(String(20))
        password = Column(String(32))
        desc = Column(String(32))
        
        def __repr__(self):
            return "<User(username='%s', password='%s', desc='%s')>" % (
                self.username, self.password, self.desc)
    # 把动态设置表名的类返回去
    return User_Model


if __name__ == '__main__':
    Person = table_name_model_class("Person_Info")

    # 创建链接
    engine = create_engine(r'sqlite:///AiTestOps.db?check_same_thread=False', echo=True)
    # 创建 Person 表
    Person.__table__.create(engine, checkfirst=True)
    # 创建Session类对象
    Session = sessionmaker(bind=engine)
    # 创建Session类实例
    session = Session()
    # 一次插入多条记录形式
    session.add_all(
        [
            Person(username='Mark', password='123456', desc='活泼'),
            Person(username='Tony', password='123456', desc='活泼')
        ]
    )
    # 当前更改只是在session中，需要使用commit确认更改才会写入数据库
    session.commit()

    mark = session.query(Person).filter_by(username='Mark').first()

    # 将 mark 用户记录删除
    session.delete(mark)

    # 确认删除
    session.commit()

    # 遍历查看，已无 Mark 数据 
    for person in session.query(Person):
        print(person.username)
```

或者，直接一步到位 ，不需要像上面那样，先查询出来，再执行删除操作。

```python
session.query(Person).filter(Person.username == "Mark").delete()

session.commit()
# 删除 in 操作查询出来的记录,需要传synchronize_session=False，否则会抛出 qlalchemy.exc.InvalidRequestError
session.query(Person).filter(Person.desc.in_(['可爱', '活泼'])).delete(synchronize_session=False) 
```
