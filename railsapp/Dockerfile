FROM ruby:2.4.1
MAINTAINER Tomoyuki
ENV LANG C.UTF-8

# myswl-client,nginx,vim をインストール
RUN apt-get update && apt-get install -y build-essential libpq-dev nodejs  mysql-client vim nginx

# nginx に unicornの設定ファイルをコピー
ADD ./container/nginx/unicorn.conf /etc/nginx/conf.d/unicorn.conf

# Gemfile bundle install
WORKDIR /tmp
ADD Gemfile Gemfile
ADD Gemfile.lock Gemfile.lock
RUN bundle install

ENV APP_HOME /railsapp
RUN mkdir -p $APP_HOME
WORKDIR $APP_HOME
ADD . $APP_HOME
