# myprj
It is my project.

# railsapp v1
-------------------
-- 概要
-------------------
DockerによるRails開発環境
構成[Rails + Unicorn + Nginx + MySQL]

-------------------
-- 環境構築手順
-------------------
 1.Githubからクローン
 2.railsappディレクトリに移動
   cd ./railsapp
 3.Railsアプリケーションを生成
   docker-compose run web rails new . --force --database=mysql --skip-bundle
 4.Gemfileの以下をリコメントする。
   gem 'therubyracer', platforms: :ruby
 5.gem「unicorn」を最終行に追加
   gem 'unicorn'
 6:MySQLへの接続情報をconfig/database.ymlで設定。
   以下の通り修正
     password: password
     host: db
 7.フォルダ「railsapp」直下のファイル「unicorn.rb」をフォルダ「config」に移動
 8:ビルドを実行
   docker-compose build
 9:Dockerコンテナ実行
   docker-compose up
 10:ターミナルの別タブでデータベース作成
   docker-compose run web rake db:create
 11:ブラウザで「http://127.0.0.1」にアクセスして確認

-------------------
-- サービスの停止と再開
-------------------
 1.起動中のサービスを停止
 docker-compose stop
 2.停止中のサービスを再起動
 docker-compose restart

-------------------
-- ファイルを修正した場合（jemファイルなど）
-------------------
1.再ビルド
docker-compose build
2.コンテナを作成実行（バックグラウンド実行の場合）
docker-compose up -d

-------------------
-- 全て再構築する場合
-------------------
 1.docker-composeのコンテナを全て削除
 docker-compose down --rmi all
 2.フォルダ「tmp」のファイル「unicorn.pid」を削除
 3.再ビルド
 docker-compose build

-------------------
-- その他コマンドなど
-------------------
 -- docker-composeで起動中コンテナ確認
 docker-compose ps
 -- docker-composeで存在する全てのコンテナ確認
 docker-compose ps -a
 -- バックグラウンドで起動
 docker-compose up -d
 -- docker-composeを一時停止
 docker-compose stop
 -- docker-composeのコンテナを全て削除
 docker-compose down --rmi all
 -- 対象の起動中コンテナにログイン
 docker exec -it [コンテナID or コンテナ名] /bin/bash

# heroku v1
-------------------
-- 概要
-------------------
Heroku用のDockerによるRails環境

-------------------
-- 環境構築手順
-------------------
 1.Githubからクローン
 2.herokuディレクトリに移動
   cd ./heroku
 3.Dockerfileをビルドし、イメージ「heroku-rails:1.0」を作成
   docker build -t heroku-rails:1.0 .
 4.Railsプロジェクトの作成
   docker run -it -v `pwd`:/usr/src/app -w /usr/src/app heroku-rails:1.0 rails new .
     ※途中でGemfileの上書きを確認されるので、「Y」を入力後にEnter。
 5.Gemfileの修正
   ①以下をリコメントする。
     gem 'therubyracer', platforms: :ruby
   ②PostgreSQLを使用するため、以下を追加する。
     gem 'pg'
 6.Dockerfileを再ビルド
   docker build -t heroku-rails:1.0 .
 7.実行用Webイメージ作成
   フォルダ直下のファイル「Dockerfile-nextfile」のファイル名を
   「Dockerfile」に修正し、既存のDockerfileを上書き後、
   ビルドしてイメージ「heroku-rails/do:1.0」を作成する。
   docker build -t heroku-rails/do:1.0 .
 8.Herokuにデプロイ
   ①サインアップ
     https://api.heroku.com/signup からユーザー登録する。
   ②Heroku Toolbeltのインストール
     https://devcenter.heroku.com/articles/heroku-command-line#download-and-install
     からHerokuのCommand Lineをインストールする。
     例：MacOS Homebrewの場合「brew install heroku」
   ③Heroku Container Registryプラグインのインストール
     heroku plugins:install heroku-container-registry
   ④Heroku アプリケーションの作成
     (a)初回のみherokuにログイン。メールアドレスとパスワードを入力する。
       heroku container:login
     (b)herokuにアプリケーションを作成する。
        ※以下の「app-name」は、任意のアプリケーション名に修正する。
          アプリケーション名がない場合は、自動で付与される。
        heroku create app-name
   ⑤HerokuへDockerコンテナのデプロイ
     heroku container:push web
   ⑥以下アドレスにアクセスして確認
     Railsの初期画面が表示されればOK
     ※以下の「app-name」は、上記④で修正したアプリケーション名に修正
     https://app-name.herokuapp.com/
 9.Heroku上のアプリをproductionモードに変更
   ①Heroku上の環境変数を確認（空であることを確認）
     heroku config --app app-name
   ②Railsを本番環境で動かすための設定を追加
     ※以下の「app-name」は、上記④で修正したアプリケーション名に修正
     heroku config:add RAILS_ENV=production --app app-name
     heroku config:add RACK_ENV=production --app app-name
     heroku config:add RAILS_SERVE_STATIC_FILES=enabled --app app-name
     heroku config:add RAILS_LOG_TO_STDOUT=enabled --app app-name
     heroku config:add LANG=en_US.UTF-8 --app app-name
   ③追加した環境変数を確認
     heroku config --app app-name
   ④最後にセッションキー等のシードに使われるSECRET_KEY_BASEを追加
     ※以下の「app-name」は、上記④で修正したアプリケーション名に修正
       また、秘密キーのため、他人に共有しないこと！
     heroku config:add SECRET_KEY_BASE=$(docker run -v `pwd`:/usr/src/app -w /usr/src/app -it heroku-rails/do:1.0 bundle exec rake secret) --app app-name
 10.データベースの準備
   ①フォルダ「config」直下の「database.yml」を修正
     HerokuではDATABASE_URLという環境変数に接続先情報を格納し、
     PGの修正をすることなく接続先のDBになっていている。
     (a)「default: &default」の以下をコメントアウト
         adapter: sqlite3
         pool: 5
         timeout: 5000
     (b)「default: &default」に以下を追加
        url: <%= ENV['DATABASE_URL'] %>
   ②docker-composeを立ち上げて確認
     docker-compose up
     open http://localhost:3000
   ③helloアクションをApplicationコントローラーに追加
     RailsのデフォルトのページはHeroku上ではうまく表示されない仕様のため、
     helloアクションをApplicationコントローラーに追加する。
     フォルダ「app/controllers」にあるファイル「application_controller.rb」
     を開き、以下を追加する。
       def hello
         render html: "hello, world!"
       end
   ④ルートルーティングを設定
     フォルダ「config」のファイル「routes.rb」にある、
     「Rails.application.routes.draw do」直下に、
     「root 'application#hello'」を追加する。
   ⑤HerokuにPostgreSQLの追加
     ブラウザからHerokuの管理画面を開き、タブ「Resouces」の
     「Add-ons」の検索バーで「heroku-postgresql」と入力。
     ボタン「Provision」を押下し、追加が完了。
     ※heroku configで環境変数「DATABASE_URL」の確認が可能
   ⑥HerokuへDockerコンテナのデプロイ
     heroku container:push web
   ⑦Herokuにテーブルの作成
     heroku run rake db:migrate
     heroku restart
   ⑧以下アドレスにアクセスして再確認
     画面に「hello, world!」が表示されればOK
     ※以下の「app-name」は、上記④で修正したアプリケーション名に修正
     https://app-name.herokuapp.com/
