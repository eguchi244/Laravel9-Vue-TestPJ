# Laravel9_Vue.jsテンプレート

## 概要
LaravelとVue.jsをDockerで導入したテンプレートです。  
下記のZennの記事によって構築されました。具体的な内容は記事を参照してください。   
[LaravelとVue.jsをDockerで導入してみよう!](https://zenn.dev/eguchi244_dev/articles/laravel-vue-docker-introduction-20230828)

## 目的＆内容
下記のシステム構成でのテンプレートに使用することを目的にしています。    
この README は Github からローカル環境に構築するための手順です。

## システム構成
【システム構成】
* フレームワーク ： Laravel Framework 9.x.x
* フロントエンドビルドツール ： LaravelMix
* データベース ： MYSQL 5.7.36
* DB管理ツール ： phpMyAdmin latest
* PHP ： PHP 8.0.x
* Nginx ： Nginx latest
* Node.js ： node 14.18-alpine
* mail：mailhog latest

【ディレクトリ構成】
```
laravel9-vue-build-template（ルートディレクトリ）
├── docker-compose.yml
├── docker 
│   ├── php 
│   │   ├── Dockerfile 
│   │   └── php.ini 
│   └── nginx 
│       └── default.conf 
├── phpMyAdmin
└── src 
    └──  LaravelVueProject（Laravelのプロジェクトディレクトリ）
```

## 前提条件
PCに下記がインストールと設定がされていること。

* Linux/Unix開発環境
   * Windows ： WSL2（Ubuntu）, Curl
   * Mac ： HomeBrew
* Docker, Docker-compose
* composer
* npm 

## 環境構築
任意の作業フォルダ内で下記を実施してください。

1. GitHubよりプロジェクトをダウンロードする
```
$ git clone https://github.com/eguchi244/laravel9-vue-build-template.git
$ cd laravel9-vue-build-template
```
2. Dockerをビルドする
```
$ docker-compose up -d
```
3. Laravel(PHP)のセットアップをする
```
# PHPコンテナにログインする
$ docker-compose exec php bash
root@~# cd LaravelVueProject
# Composer（PHPパッケージ）をインストールしてVenderフォルダを作成する
root@~# composer install
# .env.example ファイルをコピーして .env ファイルを生成する
root@~# cp .env.example .env
# キージェネレートする
# これを行わないと起動時に「No application encryption key has been specified.」エラーが表示される
root@~# php artisan key:generate
# インストールの確認をする
root@~# php artisan --version
# 念の為に権限を与える
# PermissionDeniedエラーがでる場合の対処方法
root@~ # chown www-data ./ -R
# PHPコンテナからログアウトする
root@~# exit
```
4. ポートが競合する場合は 必要に応じて .env ファイルを編集する
- ポートが競合する場合は下記のような書き方で競合しないポートに書き換えてください
```
APP_URL=http://localhost:8880
APP_PORT=8880
```
- .env を書き換えてる場合は再度ターミナルでキージェネレートする
```
# PHPコンテナにログインする
$ docker-compose exec php bash
root@~# cd LaravelVueProject
# キージェネレートする
root@~# php artisan key:generate
# PHPコンテナからログアウトする
root@~# exit
```
5. JSモジュールのセットアップをする
```
# nodeコンテナにログインする
$ docker-compose exec node /bin/sh
root@~# cd LaravelVueProject
# 各種loaderをインストールするために npm をビルドする
root@~# npm install && npm run dev
# インストールされたJSモジュールを全部消す
root@~# rm -rf node_modules
# インストールされたJSモジュールのバージョン情報を消す
root@~# rm -rf package-lock.json
# npmのキャッシュをクリアする
root@~# npm cache clear --force
# npm(jsモジュール)を再インストールして実行する
root@~# npm install && npm run dev
# nodeコンテナからログアウトする
root@~# exit
```

6. migrationを実行する
```
# PHPコンテナにログインする
$ docker-compose exec php bash
root@~# cd LaravelVueProject
# migrationを実行する
root@~# php artisan migrate:fresh
# PHPコンテナからログアウトする
root@~# exit
```
7. データーベースを確認する
```
# DBコンテナにログインする
$ docker-compose exec db bash
# MySQLにログインする(.envでアカウントを確認する)
root@~# mysql -u <ユーザー名> -p --default-character-set=utf8mb4
Enter password: <DBのパスワードを入力> 
# DBを確認する
mysql> show databases;
# DBを選択する
mysql> use mysql_test_db;
# テーブルを確認する
mysql> SHOW TABLES;
# MySQLからログアウトする
mysql>quit
# DBコンテナからログアウトする
root@~# exit
```

## 動作確認
1. ブラウザでLaravelのデフォルトページの表示を確認する  
ブラウザに [http://localhost/](http://localhost/) でアクセスして表示されればOKです。
2. phpMyAdmin（DB管理ツール）　の表示を確認する  
ブラウザに [http://localhost:8080](http://localhost:8080) でアクセスして表示されればOKです。
3. mailhog（メーリングライブラリ）　の表示を確認する  
ブラウザに [http://localhost:8025](http://localhost:8025) でアクセスして表示されればOKです。

以上です。