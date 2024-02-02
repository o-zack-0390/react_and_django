コンテナ起動のメモ。

利用者は手順14,15,16のみ実行。


## 環境構築する最初の人だけ打つコマンド

### 1. 下記実行
    $ docker-compose build


### 2. 下記実行
    $ docker-compose run --rm react sh -c 'npx create-react-app /AI_Novel_Typing/frontend --template typescript'


### 3. index.htmlに下記追記 (formanticUIを使用可能にする)
    <script src="https://cdn.jsdelivr.net/npm/jquery@3.6.3/dist/jquery.min.js"></script>
    <link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/npm/fomantic-ui@2.9.2/dist/semantic.min.css">


### 4. 下記実行
    $ docker-compose run django django-admin.py startproject django_project .


### 5. settings.pyに下記追記

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql',
            'NAME': 'mydatabase',    # MySQLデータベースの名前
            'USER': 'myuser',        # MySQLデータベースのユーザー
            'PASSWORD': 'secret',    # MySQLデータベースのパスワード
            'HOST': 'mysql',         # MySQLデータベースのホスト
            'PORT': '3306',          # MySQLデータベースのポート
        }
    }


### 6. 下記実行
    $ docker-compose up -d


### 7. 下記実行
    $ docker exec -it test_ai_novel_typing-django-1 /bin/bash


### 8. 下記実行
    $ python manage.py startapp django_apps


### 9. settings.pyに下記追記

    INSTALLED_APPS = [
        'django_apps.apps.DjangoAppsConfig',
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
    ]


### 10. models.pyに下記追記 (ランキングテーブルの構成が決まってないので仮のテーブルを作ってみる)

    class LivedoorNewscorpus(models.Model):
        label    = models.CharField(max_length=50)
        name     = models.CharField(max_length=50, primary_key=True)
        sentence = models.TextField()


### 11. 下記実行
    $ python manage.py makemigrations django_apps


### 12. 下記実行 (手順9で定義したテーブルが作成される。Adminerで確認可能)
    $ python manage.py migrate


### 13. GitHubにプロジェクトをpush


## 全員が打つコマンド

### 14. 下記実行 (データベース起動)
    $ docker-compose up -d mysql

### 15. 下記実行 (他のコンテナ起動)
    $ docker-compose up -d

### 16. 下記実行(アプリケーションを停止するときに実行)
    $ docker-compose down


### ※ 注意点1
 
    手順13だけ実行するとmysqlの起動タイミングによってはDjango側でDB接続エラーになる。
    必ずデータベースを先に起動してからDjangoコンテナを起動する。

    (上記のコマンドでは 12.mysql起動 → 13.他のコンテナ全て起動)


### ※ 注意点2

    「ai_novel_typing_django_run_d72126c1c39e」というコンテナは起動させない。
    一つだけ起動してないコンテナがあるが、Djangoプロジェクトを作成するためのコンテナ(手順4)
    何回も起動すると同じプロジェクトが複数作成されるかもしれないので、エラーになる可能性が高くなる。
