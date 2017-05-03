FROM ruby:2.4.1
MAINTAINER Tomoyuki

ENV APP_ROOT /usr/src/app

WORKDIR $APP_ROOT

RUN apt-get update && \
    apt-get install -y nodejs \
                       postgresql-client \
                       --no-install-recommends && \
    rm -rf /var/lib/apt/lists/*

COPY ./Gemfile $APP_ROOT

RUN \
  echo 'gem: --no-document' >> ~/.gemrc && \
  cp ~/.gemrc /etc/gemrc && \
  chmod uog+r /etc/gemrc && \
  bundle config --global build.nokogiri --use-system-libraries && \
  bundle config --global jobs 4 && \
  bundle install && \
  rm -rf ~/.gem

COPY . $APP_ROOT

ENV APP_ROOT /usr/src/app
