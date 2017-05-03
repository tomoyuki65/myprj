FROM heroku-rails:1.0
MAINTAINER Tomoyuki

ENV APP_ROOT /usr/src/app
COPY . $APP_ROOT

EXPOSE  3000
CMD ["rails", "server", "-b", "0.0.0.0"]
