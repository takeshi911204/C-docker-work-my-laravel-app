# リポジトリのFORK作成手順
gitのリポジトリをforkして作成したプロジェクトの運用手順

## Server環境構築

1. .envのPROJECT_NAMEを指定してください。  
過去に利用したことのあるPROJECT_NAMEの場合、同じコンテナがDockerに存在する可能性があります。  
その場合はDockerの古いContainers, Images, Volumesをそれぞれ順に削除する必要があります。

2. .envのMYSQL_DATABASEを指定してください。

3. ターミナルで以下のコマンドを実行し、Containersが全て起動することを確認してください。

```
docker-compose up -d
```

docker-<PROJECT_NAME>  
├─ <PROJECT_NAME>-php  
├─ <PROJECT_NAME>-nginx  
├─ <PROJECT_NAME>-mysql  
└─ <PROJECT_NAME>-pma  

## Nuxt.js環境構築手順
Nuxt.jsを構築する場合は以下の手順を実行してください。

1. docker-compose.ymlの[Nuxt Setup1]のコメントアウトを外してください。

2. ターミナルで以下のコマンドを実行します。

```
docker-compose run nuxt npm init nuxt-app www.<PROJECT_NAME>
```

Nuxt環境作成時のコマンド設定は、開発したい設定にしてください。  
以下は例です。

```
✨  Generating Nuxt.js project in www.sample
? Project name: www.sample
? Programming language: JavaScript 
? Package manager: Npm
? UI framework: (Use arrow keys)
❯ None
? UI framework: None
? Nuxt.js modules: Axios - Promise based HTTP client
? Linting tools: ESLint
? Testing framework: None
? Rendering mode: Universal (SSR / SSG)
? Deployment target: Server (Node.js hosting)
? Development tools: (Press <space> to select, <a> to toggle all, <i> to invert selection)
? Continuous integration: None
? Version control system: Git
```

3. 実行が終わるとNuxt用ContainersとImagesが作成されますが、どちらも削除してください。

4. docker-compose.ymlの[Nuxt Setup1]をコメントアウトし、[Nuxt Setup2]のコメントアウトを外してください。

5. 再度Nuxt用Dockerを起動します。

```
docker-compose up -d
```

6. http://localhost:3000 にアクセスして、起動の確認をしてください。  
起動には若干時間がかかります。

## Laravel環境構築手順

1. phpのContainersを起動してください。

```
docker-compose start php
```

2. 以下のコマンドで、phpのコンテナに入ります。

```
docker-compose exec php bash
```

3. Laravel作成用コマンドを実行します。

```
composer create-project laravel/laravel 'api.<PROJECT_NAME>'
```

4. storageディレクトリに書き込み権限を追加します

```
chmod 777 -R api.<PROJECT_NAME>/storage/
```

5. /api/api.<PROJECT_NAME>/.envの環境ファイルを変更します。  
Server環境構築で変更した.envと同じ値を指定してください。

```
DB_HOST=mysql # MySQLのコンテナ名
DB_DATABASE=<MYSQL_DATABASE> # .envと同じMYSQL_DATABASE
DB_PASSWORD=%DBz0x9c8v7 # .envを同じMYSQL_PASSWORD
```

6. DB接続の確認をします。

```
cd api.<PROJECT_NAME>
php artisan migrate
```

7. 実行が成功すればDBにusersテーブルが作成されます。  
確認がとれたらphpコンテナから抜け出します。

```
exit
```

8. /server/nginx/default.confの、DocumentRootを変更します。

```
#root /var/www/vhost; # コメントアウト
root /var/www/vhost/api.<PROJECT_NAME>/public;
```

9. 再度Dockerをrestartします。

```
docker-compose restart
```

10. http://localhost:8080 にアクセスして、起動の確認をしてください。  

## ディレクトリ構成
<PROJECT_NAME>  
├─ api/  
│   └─ api.<PROJECT_NAME> # BACKEND API用ディレクトリ  
├─ server  
│   ├─ mysql  
│   ├─ nginx  
│   └─ php  
└─ www  
　  └─ www.<PROJECT_NAME> # FRONT用ディレクトリ  

# 開発用URL

## APIサービス
DocumentRoot: /var/www/vhost/api.project_name/public/  
URL: http://localhost:8080

## WWWサービス
DocumentRoot: /var/www/vhost/www.project_name/  
URL: http://localhost:3000

## phpMyAdminサービス
URL: http://localhost:4040


# 環境設定
## server環境

php 8.0  
環境設定ファイル: /server/php/php.ini

Nginx 1.20  
環境設定ファイル: /server/nginx/default.conf

MySQL 8.0  
環境設定ファイル: /server/mysql/my.cnf

## api環境
Laravel v8.81.0

## www環境
NuxtJs v2.15.8

# Docker
## コンテナ

|概要|サービス名|コンテナ名|備考|
| ---- | ---- | ---- | ---- |
| Nginx service | nginx | myapp-nginx | |
| php service | php | myapp-php |laravel用コマンドの実行はphpコンテナ上で実行します|
| MySQL service | mysql | myapp-mysql |
| phpMyAdmin service | phpmyadmin | myapp-pma |
| Nuxt.js service | nuxt | myapp-nuxt | 

## command
| 概要 | command | 備考 |
| ---- | ---- | ---- |
| 全コンテナ起動 | docker-compose up -d | |
| 全コンテナ再起動 | docker-compose restart | |
| 各コンテナ起動 | docker-compose up <コンテナ名> | |
| 各コンテナ移動 | docker-compose exec <サービス名> bash | php, mysqlのみ |
