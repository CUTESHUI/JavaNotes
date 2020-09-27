## RabbitMQ

```
Last login: Tue Sep 15 23:51:33 on ttys001
➜  /Users/mac git:(master)
✗ brew install rabbitmq
Updating Homebrew...
^C==> Installing dependencies for rabbitmq: jpeg, libpng, libtiff, wxmac and erlang
==> Installing rabbitmq dependency: jpeg
==> Downloading https://homebrew.bintray.com/bottles/jpeg-9d.catalina.bottle.tar
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/8f7b82a952fb3937889c7
######################################################################## 100.0%
==> Pouring jpeg-9d.catalina.bottle.tar.gz
🍺  /usr/local/Cellar/jpeg/9d: 21 files, 775.2KB
==> Installing rabbitmq dependency: libpng
==> Downloading https://homebrew.bintray.com/bottles/libpng-1.6.37.catalina.bott
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/c8e74da602c21f978cd7e
######################################################################## 100.0%
==> Pouring libpng-1.6.37.catalina.bottle.tar.gz
🍺  /usr/local/Cellar/libpng/1.6.37: 27 files, 1.2MB
==> Installing rabbitmq dependency: libtiff
==> Downloading https://homebrew.bintray.com/bottles/libtiff-4.1.0.catalina.bott
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/449bd9123e73e4c4eab85
######################################################################## 100.0%
==> Pouring libtiff-4.1.0.catalina.bottle.tar.gz
🍺  /usr/local/Cellar/libtiff/4.1.0: 247 files, 3.7MB
==> Installing rabbitmq dependency: wxmac
==> Downloading https://homebrew.bintray.com/bottles/wxmac-3.0.4_2.catalina.bott
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/763d404a9adfadf2fa209
######################################################################## 100.0%
==> Pouring wxmac-3.0.4_2.catalina.bottle.tar.gz
🍺  /usr/local/Cellar/wxmac/3.0.4_2: 810 files, 22.9MB
==> Installing rabbitmq dependency: erlang
==> Downloading https://homebrew.bintray.com/bottles/erlang-22.3.catalina.bottle
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/aeb18f87b3059b6256aba
######################################################################## 100.0%
==> Pouring erlang-22.3.catalina.bottle.tar.gz
==> Caveats
Man pages can be found in:
  /usr/local/opt/erlang/lib/erlang/man

Access them with `erl -man`, or add this directory to MANPATH.
==> Summary
🍺  /usr/local/Cellar/erlang/22.3: 5,787 files, 281.7MB
==> Installing rabbitmq
==> Downloading https://github.com/rabbitmq/rabbitmq-server/releases/download/v3
==> Downloading from https://github-production-release-asset-2e65be.s3.amazonaws
######################################################################## 100.0%
==> /usr/bin/unzip -qq -j /usr/local/Cellar/rabbitmq/3.8.3/plugins/rabbitmq_mana
==> Caveats
Management Plugin enabled by default at http://localhost:15672

Bash completion has been installed to:
  /usr/local/etc/bash_completion.d

To have launchd start rabbitmq now and restart at login:
  brew services start rabbitmq
Or, if you don't want/need a background service you can just run:
  rabbitmq-server
==> Summary
🍺  /usr/local/Cellar/rabbitmq/3.8.3: 281 files, 20.4MB, built in 38 seconds
==> `brew cleanup` has not been run in 30 days, running now...
==> Caveats
==> erlang
Man pages can be found in:
  /usr/local/opt/erlang/lib/erlang/man

Access them with `erl -man`, or add this directory to MANPATH.
==> rabbitmq
Management Plugin enabled by default at http://localhost:15672

Bash completion has been installed to:
  /usr/local/etc/bash_completion.d

To have launchd start rabbitmq now and restart at login:
  brew services start rabbitmq
Or, if you don't want/need a background service you can just run:
  rabbitmq-server
➜  /Users/mac git:(master)
✗
```

- 启动
  - cd /usr/local/Cellar/rabbitmq/3.8.3/sbin
  - sudo ./rabbitmq-server
  - http://localhost:15672
  - 账号/密码：guest / guest
- 关闭
  - cd /usr/local/Cellar/rabbitmq/3.8.3/sbin 
  - sudo ./rabbitmqctl stop
- 启用rabbitmq management插件
  - sudo sbin/rabbitmq-plugins enable rabbitmq_management
- 配置文件
  - /usr/local/etc/rabbitmq
  - enabled_plugins、rabbitmq-env.conf
- 日志
  - /usr/local/var/log/rabbitmq

---





