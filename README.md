# myprj
It is my project.

# railsapp v1
-------------------
-- 環境構築手順
-------------------
 1:Githubからクローン
 2:railsappディレクトリに移動
   cd ./railsapp
 3:Railsアプリケーションを生成
   docker-compose run web rails new . --force --database=mysql --skip-bundle
 4:Gemfileの以下をリコメントする。
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
 11:ブラウザで「http://0.0.0.0」にアクセスして確認

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
