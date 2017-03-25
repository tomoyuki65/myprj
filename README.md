# myprj
It is my project.

# railsapp v1
 1:Githubからクローン
 2:railsappディレクトリに移動
   cd ./railsapp
 3:Railsアプリケーションを生成
   docker-compose run web rails new . --force --database=mysql --skip-bundle
 4:Gemfileの以下をリコメントする。
   gem 'therubyracer', platforms: :ruby
 5:MySQLへの接続情報をconfig/database.ymlで設定。
   以下の通り修正
     password: password
     host: db
 6:ビルドを実行
   docker-compose build
 7:Dockerコンテナ実行
   docker-compose up
 8:ターミナルの別タブでデータベース作成
   docker-compose run web rake db:create
 9:localhost:3000にアクセスし、ブラウザ確認
