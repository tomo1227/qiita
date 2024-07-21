---
title: django入門
tags:
  - Python
  - Django
private: true
updated_at: '2022-01-03T05:34:27+09:00'
id: cd68f98c7db4fe62c6ac
organization_url_name: null
slide: false
ignorePublish: false
---
#概要

Djangoの公式チュートリアルをしていく

# 開発環境

```terminal
% conda create -n django python anaconda
% conda activate django
(django) % conda install django
```

Djangoが入ってる確認する。

```python
>>> import django
>>> print(django.get_version())
4.0
```

下のコマンドでもバージョンを確認できる

```terminal
python -m django --version
4.0
```


# Hello Django

まず、プロジェクトを作成する。

```terminal
(django) % django-admin startproject mysite
(django) % tree mysite
mysite
├── manage.py   Djangoプロジェクトに対する様々な操作を行うためのコマンドラインユーティリティ
└── mysite     プロジェクト名
    ├── __init__.py  このディレクトリがPythonパッケージであることをPythonに知らせるための空のファイル
    ├── asgi.py  プロジェクトを提供するASGI互換Web サーバーのエントリポイント
    ├── settings.py  Django プロジェクトの設定ファイル
    ├── urls.py     DjangoプロジェクトのURL宣言(目次)
    └── wsgi.py     プロジェクトをサーブするためのWSGI互換Webサーバーとのエントリーポイント
```

```terminal
(django) mysite % python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
January 02, 2022 - 18:10:56
Django version 4.0, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

http://127.0.0.1:8000/ にアクセスすると

![スクリーンショット 2022-01-03 3.12.45.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/cc11ff73-0314-9271-589c-415fbf0342b3.png)

となる。

デフォルトではサーバーのポートは*8000*だが例えば*8080*にしたいときは以下のようにする。

```terminal
% python manage.py runserver 8080
```

## Pollsアプリケーションの作成

```terminal
(django) mysite % python manage.py startapp polls
```

すると***mysite***の中に***polls***ディレクトリが作成される。

```
(django) mysite % ls
db.sqlite3  manage.py*  mysite/     polls/
(django) mysite % tree polls
polls
├── __init__.py
├── admin.py
├── apps.py
├── migrations
│   └── __init__.py
├── models.py
├── tests.py
└── views.py

1 directory, 7 files
```

##pollのviewを作る

```python:polls/views.py
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

ビューを呼ぶためにURLを対応付けしなければならない。そのために**URLconf**を作る。



pollsディレクトリに**URLconf**を作るには以下のような***urls.py***というファイルを作る。

```python:polls/urls.py
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

次にルートのURLconfに**polls.urls**モジュールの記述を反映させる。
***mysite/urls.py***を以下のように書く。


```python:mysite/urls.py
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```


そしてhttp://localhost:8000/polls/ にアクセスすると

```terminal
(django) mysite % python manage.py runserver  
```


![スクリーンショット 2022-01-03 3.55.20.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/ae9f8913-98ab-5331-f1ad-b20fcf98d39f.png)

と表示される。


#next app

##Databaseの設定

***mysite/settings.py***を開いてタイムゾーンを変更する

```python:mysite/settings.py
TIME_ZONE = 'Asia/Tokyo'
```

アプリケーションは最低1つデータベースのテーブルを使うので、使い始まる前にデータベースにテーブルを以下のコマンドで作る。

```terminal
(django)  mysite % python manage.py migrate
```

##モデルの作成

pollアプリケーションではQuestionとChoiceの2つのモデルを作る。

```python:polls/models.py
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

##モデルを有効にする

まず、***mysite/settings.py*** を編集する。

アプリケーションをプロジェクトに含めるには、構成クラスへの参照を INSTALLED_APPS 設定に追加する必要がある。PollsConfig クラスは、***polls/apps.py*** にある。

```python:mysite/settings.py
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```
これでdjangoはpollsアプリケーションが含まれていることを認識できる。

そして以下のように、**makemigrations** を実行することで、Djangoにモデルに変更があったこと(この場合、新しいものを作成した)を伝え、そして変更をマイグレーションの形で保存することができる。

```terminal
(django) mysite % python manage.py makemigrations polls
Migrations for 'polls':
  polls/migrations/0001_initial.py
    - Create model Question
    - Create model Choice
```

**sqlmigrate** コマンドはマイグレーションの名前を引数にとってSQLを返す。

```terminal
(django) mysite % python manage.py sqlmigrate polls 0001
BEGIN;
--
-- Create model Question
--
CREATE TABLE "polls_question" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "question_text" varchar(200) NOT NULL, "pub_date" datetime NOT NULL);
--
-- Create model Choice
--
CREATE TABLE "polls_choice" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "choice_text" varchar(200) NOT NULL, "votes" integer NOT NULL, "question_id" bigint NOT NULL REFERENCES "polls_question" ("id") DEFERRABLE INITIALLY DEFERRED);
CREATE INDEX "polls_choice_question_id_c5b4b260" ON "polls_choice" ("question_id");
COMMIT;
```

migrate を再度実行し、 モデルのテーブルをデータベースに作成する。

```terminal
(django) mysite % python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Applying polls.0001_initial... OK
```
##APIを動かしてみる

```terminal
(django) mysite % python manage.py shell
```

```python
>>> from polls.models import Choice, Question

>>> Question.objects.all()
>>> <QuerySet []>

>>> from django.utils import timezone

>>> q = Question(question_text="What's new?", pub_date=timezone.now())

>>> q.save()

>>> q.id
 1

>>> q.question_text
 "What's new?"

>>> q.pub_date
 datetime.datetime(2022, 1, 2, 19, 50, 16, 500279, tzinfo=datetime.timezone.utc)

>>> q.question_text = "What's up?"

>>> q.save()

>>> Question.objects.all()
 <QuerySet [<Question: Question object (1)>]>
```

ここで `<QuerySet [<Question: Question object (1)>]>`という答えは分かりにくいので、**polls/models.py** を変更する。

```python:polls/models.py
import datetime
from django.db import models
from django.utils import timezone


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

    def __str__(self):
        return self.question_text
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
    def __str__(self):
        return self.choice_text
```

もう一度`python manage.py shell `を実行する。

```python
>>> from polls.models import Choice, Question

>>> Question.objects.all()
 <QuerySet [<Question: What's up?>]>

>>> Question.objects.filter(id=1)
 <QuerySet [<Question: What's up?>]>

>>> Question.objects.filter(question_text__startswith='What')
 <QuerySet [<Question: What's up?>]>

>>> from django.utils import timezone

>>> current_year = timezone.now().year

>>> Question.objects.get(pub_date__year=current_year)
 <Question: What's up?>

>>> Question.objects.get(id=2)
・・・                  
DoesNotExist: Question matching query does not exist.

>>> Question.objects.get(pk=1)
 <Question: What's up?>

>>> q = Question.objects.get(pk=1)

>>> q.was_published_recently()
 True

>>> q = Question.objects.get(pk=1)

>>> q.choice_set.all()
 <QuerySet []>

>>> q.choice_set.create(choice_text='Not much', votes=0)
 <Choice: Not much>

>>> q.choice_set.create(choice_text='The sky', votes=0)
 <Choice: The sky>

>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)

>>> c.question
 <Question: What's up?>

>>> q.choice_set.all()
 <QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

>>> q.choice_set.count()
 3

>>> Choice.objects.filter(question__pub_date__year=current_year)
 <QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')

>>> c.delete()
 (1, {'polls.Choice': 1})
```

## Django Admin

```terminal
(django) mysite % python manage.py createsuperuser
Username : admin
Email address: admin@example.com

Password: doraemon
Password (again): doraemon

Bypass password validation and create user anyway? [y/N]: y
Superuser created successfully.

(django) mysite % python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
January 03, 2022 - 05:19:20
Django version 4.0, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

http://localhost:8000/admin にログインすると

![スクリーンショット 2022-01-03 5.23.11.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/1e608fe3-4ec2-7f4a-47ed-2439c9e095e9.png)

**LANGUAGE_CODE** を設定すると、与えられた言語でログイン画面が表示されるようになる。
ログインすると

![スクリーンショット 2022-01-03 5.25.00.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/832bf443-7ff1-b63c-0517-ef731843b50d.png)

**admin** のインデックスページに **poll**が表示されていないので、***polls/admin.py*** を編集する。

```python:polls/admin.py
from django.contrib import admin

from .models import Question

admin.site.register(Question)
```

再びログインすると、

![スクリーンショット 2022-01-03 5.28.55.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/0104ca04-fc30-379f-cc9e-0bdeaa16d193.png)

とpollが表示される。
Questionをクリックしてみると、

![スクリーンショット 2022-01-03 5.29.49.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/42c6b986-92dd-a6db-06df-8ea0881adc4d.png)

**"What's UP?" question** も表示される。

