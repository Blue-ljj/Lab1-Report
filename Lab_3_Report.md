Lab 3 Report
======================================

> Title: The Service Layer
>
> Author: 周智豪、顾晓楠、费宇馨、蓝嘉婕
>
> Date: 2021/6/10

## Introduction

We implement a service layer in `services.py` for EnglishPal, which provides a core service called read. This service would choose a suitable article for a user to read.

## Materials and Methods

 The function `read(user, user repo, article repo, session)` takes as input the following four arguments and returns an article ID if the user has been successfully assigned with an article to read.

> user: a User object. The class User is defined in model.py.
>
> user repo: a UserRepository object. The class UserRepository is defined in repository.py.
>
> article repo: an ArticleRepository object. The class ArticleRepository is defined in repository.py.  session: an SQLAlchemy session object.



Software version:

> Operating system - Ubuntu 18.04.5 LTS
>
> Python version - 3.6.9 
>
> SQLAlchemy version - 1.4.14

## Results

- **services.py**

```python
# Software Architecture and Design Patterns -- Lab 3 starter code
# An implementation of the Service Layer
# Copyright (C) 2021 Hui Lan


# word and its difficulty level
WORD_DIFFICULTY_LEVEL = {'starbucks':5, 'luckin':4, 'secondcup':4, 'costa':3, 'timhortons':3, 'frappuccino':6}


class UnknownUser(Exception):
    pass


class NoArticleMatched(Exception):
    pass


##########################################Additional part###############################################
def read(user, user_repo, article_repo, session):
	user_in_repo = user_repo.get(user.username)
  # check user and its password
	if user_in_repo is None:
		raise UnknownUser('No User has name %s' % user.username )
	else:
		if user_in_repo.password != user.password:
			raise UnknownUser('Wrong password  %s' % user.password )
		else:
			user_word_difficulty_sum = 0
			user_word_len = len(user_in_repo.newwords)
			# check user level, if the number of new words > 3 then choose top 3 most difficulty words
			if user_word_len > 3:
				user_newwords_dict = {key: value for key, value in WORD_DIFFICULTY_LEVEL.items() if key in user_in_repo.newwords}
				sorted_word_level = sorted(user_newwords_dict.items(),key=lambda item:item[1],reverse=True)[:3]
				user_level = sum(word_tuple[1] for word_tuple in sorted_word_level) / 3
			else:
				for item in list(user_in_repo.newwords):
					user_word_difficulty_sum += WORD_DIFFICULTY_LEVEL[item.word]
				user_level = user_word_difficulty_sum / user_word_len
			# get article and traversal the list to get a one
			article_list = article_repo.list()
			available_article = {}
			for article in article_list:
				if article.level > user_level:
					available_article[article.article_id] = article.level
			# get the smallest level for user to read
			if available_article:
				min_level_article = article_repo.get(min(zip(available_article.values(), available_article.keys()))[1])
			else:
				raise NoArticleMatched('No Article Matched with level of %d' % user_level )

			
			
			read_record = user_in_repo.read_article(min_level_article)
			session.commit()
			session.close()
			# user information check
			return read_record
        
##########################################Additional part###############################################
```



- **Run screenshot**

![](https://ftp.bmp.ovh/imgs/2021/06/eefb64448fd84b3a.png)

## Explanation

In this code modification, we mainly rewrite the function `read` to implement a service layer for a user to read an article.

In the function `read`, we first judge whether the account information is correct. 

After identity verification, we judge and calculate the user’s English level. If the number of new words added by the user is more than three, we select the first three most difficult words to get the average difficulty which represents the user’s English proficiency. If the number of newly added words is less than three, then directly get their average difficulty. 

After obtaining the user's English level, we screened out the English articles whose article difficulty is higher than the user's level. After screening all acceptable articles, we select the article with the lowest difficulty and present it to user.

## Discussions

- **About Exceptions**

The function `read(user, user repo, article repo, session)` raises an `UnknownUser` exception if user does not have a correct user name or a correct password, or raises a `NoArticleMatched` exception if no article in the article repository, i.e. 

- **About Difficulty Level**

When providing article reading service for each user, EnglishPal needs to select articles with appropriate difficulty.
An article’s difficulty level, La, must match a user’s vocabulary level, Lu, iff La > Lu.
An article’s difficulty level is recorded in the level field in the database table articles. A user’s vocabulary level is defined as the average value of top n most difficult words in the user’s list of new words, where n is either 3 or the number of new words belonging to that user in the table newwords, whichever is smaller.

## Q&A

**Question**

- Does your function `read` in `services.py` follow the Single Responsibility Principle (SRP) principle? Why or why not?

**Answer**

- Yes. In the function `read` , we only implement one function-to select the article closest to the user's level. If the article is not successfully obtained, an exception is thrown. This design conforms to the single responsibility principle and will not change the result due to changes in a certain part.

## Read the Docs

[Check this link](https://lab1-report.readthedocs.io/en/latest/Lab_3_Report.html)

## References

[1] [How to Write a Computer Science Lab Report](https://thehackpost.com/a-brief-guide-how-to-write-a-computer-science-lab-report.html)

[2] [SQLAlchemy](https://pypi.org/project/SQLAlchemy/)

[3] [SQLAlchemy-ORM](https://pypi.org/project/sqlalchemy-orm/)