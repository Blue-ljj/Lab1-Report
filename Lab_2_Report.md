# Lab 2 Report

> Title: The ORM Magic
>
> Author: 周智豪、顾晓楠、费宇馨、蓝嘉婕
>
> Date: 2021/5/31

## Introduction

We learn the object-relational mapper (ORM) provided by SQLAlchemy. With ORM, we can map a class to a database table, and map an object of that class to a row in the databasetable. With SQLAlchemy’s ORM, we can avoid directly using any raw SQL statements.

## Materials and Methods

We manipulate database with SQLAlchemy's ORM. 

ORM can map the objects represented by the object model to the SQL-based relational model database structure. In this way, we no longer need to use complex SQL statements when we specifically manipulate the entity object, but simply manipulate the attributes and methods of the entity object. ORM technology provides a bridge between objects and relationships, and can transform object data and relational data in the database through this bridge.



Software version:

> Operating system - Ubuntu 18.04.5 LTS
>
> Python version - 3.6.9 
>
> SQLAlchemy version - 1.4.14



## Results

- **model.py**

```python
# Software Architecture and Design Patterns -- Lab 2 starter code
# Copyright (C) 2021 Hui Lan

from dataclasses import dataclass


@dataclass
class Article:
    article_id:int
    text:str
    source:str
    date:str
    level:int
    question:str
    

class NewWord:
    def __init__(self, username, word='', date='yyyy-mm-dd'):
        self.username = username
        self.word = word
        self.date = date

        
        
#####################Additional part##########################
class User:
    def __init__(self, username, password='12345', start_date='2021-05-19', expiry_date='2031-05-19'):
        self.username = username
        self.password = password
        self.start_date = start_date
        self.expiry_date = expiry_date
        self._read = []
        self._newwords = []

    def read_article(self, article):
        self._read.append(Reading(self.username, article.article_id))
        
    @property
    def newwords(self):
        return self._newwords
        
        
class Reading:
	def __init__(self,username, article_id):
		self.username = username
		self.article_id = article_id
#####################Additional part##########################
```



- **orm.py**

```python
# Software Architecture and Design Patterns -- Lab 2 starter code
# Copyright (C) 2021 Hui Lan

from sqlalchemy import Table, MetaData, Column, Integer, String, Date, ForeignKey
from sqlalchemy.orm import mapper, relationship

import model

metadata = MetaData()

articles = Table(
    'articles',
    metadata,
    Column('article_id', Integer, primary_key=True, autoincrement=True),
    Column('text', String(10000)),
    Column('source', String(100)),
    Column('date', String(10)),
    Column('level', Integer, nullable=False),
    Column('question', String(1000)),    
    )

users = Table(
    'users',
    metadata,
    Column('username', String(100), primary_key=True),
    Column('password', String(64)),
    Column('start_date', String(10), nullable=False),
    Column('expiry_date', String(10), nullable=False),  
    )

newwords = Table(
    'newwords',
    metadata,
    Column('word_id', Integer, primary_key=True, autoincrement=True),
    Column('username', String(100), ForeignKey('users.username')),
    Column('word', String(20)),
    Column('date', String(10)),
    )

readings = Table(
    'readings',
    metadata,
    Column('id', Integer, primary_key=True, autoincrement=True),
    Column('username', String(100), ForeignKey('users.username')),
    Column('article_id', Integer, ForeignKey('articles.article_id')),
);



#####################Additional part##########################
def start_mappers():
    mapper(model.Article,articles)
    words_mapper = mapper(model.NewWord,newwords)
    reading_mapper = mapper(model.Reading,readings)
    mapper(model.User, users,
    	properties={
    		"_newwords":relationship(
    			words_mapper, collection_class = list,
    		),
            "_read":relationship(
                reading_mapper, collection_class = list,
            )
    	},
    )
#####################Additional part##########################
```



- **app.py**

```python
# Software Architecture and Design Patterns -- Lab 2 starter code
# Copyright (C) 2021 Hui Lan

from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

import model
import orm


orm.start_mappers()
engine = create_engine(r'sqlite:///C:\Users\mrlan\Downloads\Teaching\SoftArch\Labs\Lab2\starter_code\EnglishPalDatabase.db')
orm.metadata.drop_all(engine)
orm.metadata.create_all(engine)
get_session = sessionmaker(bind=engine)




# add two users

session = get_session()

try:
    session.add(model.User(username='mrlan', password='12345', start_date='2021-05-14'))
    session.add(model.User(username='lanhui', password='Hard2Guess!', start_date='2021-05-15'))
    session.commit()
except:
    print('Duplicate insertions.')
    
print(session.query(model.User).count())

for u in session.query(model.User).all():
    print(u.username)

session.close()




# add a few new words

session = get_session()
session.add(model.NewWord(username='lanhui', word='starbucks', date='2021-05-15'))
session.add(model.NewWord(username='lanhui', word='luckin', date='2021-05-15'))
session.add(model.NewWord(username='lanhui', word='secondcup', date='2021-05-15'))
session.add(model.NewWord(username='mrlan',  word='costa', date='2021-05-15'))
session.add(model.NewWord(username='mrlan',  word='timhortons', date='2021-05-15'))
session.commit()
session.close()




# add a few articles

session = get_session()
article = model.Article(article_id=1, text='THE ORIGIN OF SPECIES BY MEANS OF NATURAL SELECTION, OR THE PRESERVATION OF FAVOURED RACES IN THE STRUGGLE FOR LIFE', source='CHARLES DARWIN, M.A.', date='1859-01-01', level=5, question='Are humans descended from monkeys?')
session.add(article)
session.commit()
session.close()




# query user and let him read something

session = get_session()



#####################Additional part##########################
article = model.Article(article_id=1, text='THE ORIGIN OF SPECIES BY MEANS OF NATURAL SELECTION, OR THE PRESERVATION OF FAVOURED RACES IN THE STRUGGLE FOR LIFE', source='CHARLES DARWIN, M.A.', date='1859-01-01', level=5, question='Are humans descended from monkeys?')
#####################Additional part##########################



user = session.query(model.User).filter_by(username='lanhui').one()

for item in list(user.newwords):
    print(item.word)

user.read_article(article) # this method call will add a row to table readings

print('-----')

user = session.query(model.User).filter_by(username='mrlan').one()
for item in list(user.newwords):
    print(item.word)

user.read_article(article) # this method call will add a row to table readings

session.commit()
session.close()

```



## Discussions

- **Problem:**

  While doing this lab, we encountered a DetachedInstanceError as follows:

```python
# Part of app.py
session = get_session()
user = session.query(model.User).filter_by(username='lanhui').one()

for item in list(user.newwords):
    print(item.word)

print(article)
user.read_article(article)
```

​		If we insert a `print` statement before calling `read_article()`, so that the program will raise DetachedInstanceError.

```python
sqlalchemy.orm.exc.DetachedInstanceError: Instance <Article at 0x7fe2e8e27f28> is not bound to a Session; 
```



- **Solution**

​		The session bound at the place where this article was defined has been closed, so executing `user.read_article(article) ` statement in another session will cause DetachedInstanceError.

​		It can be avoided by adding an assignment statement before calling `read_article()`, therefore we have to modify one line.



- **Modification Explanation** 

  - model.py

    In this file, we use the **descriptors** to have a more comprehensive way to generate attributes. 

    Name the attribute with an underscore, and use **property** in python to create it.

  - orm.py

    In this file, we implement the ***start_mappers()*** function by defining explicit mappers for converting between the schema and our domain model. The ORM imports the domain model,  we call the mapper function, SQLAlchemy does its magic to bind our domain model classes to the various tables we’ve defined.

  - app.py

    In this file we add an assignment of **article** in **let him reading session** before reading executed, wihch shouldn't need to be modified. However we are unable to solve the bug we encountered, such that we have no idea but to bind the article to current session.
    
    

## Read the Docs

[Check this Link](https://lab1-report.readthedocs.io/en/latest/Lab_2_Report.html)



## References

[1] [How to Write a Computer Science Lab Report](https://thehackpost.com/a-brief-guide-how-to-write-a-computer-science-lab-report.html)

[2] [Repository Pattern](https://www.cosmicpython.com/book/chapter_02_repository.html)

[3] [SQLAlchemy](https://pypi.org/project/SQLAlchemy/)

[4] [SQLAlchemy-ORM](https://pypi.org/project/sqlalchemy-orm/)