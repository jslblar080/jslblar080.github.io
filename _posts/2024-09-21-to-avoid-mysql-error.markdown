---
title:  "[MySQL] [InnoDB] Unable to lock ./ibdata1 error: 35 무한반복 피하기"
layout: post
---
`mysqld` 명령어를 실행하려는 도중 다음과 같은 오류가 계속 반복되는 것을 확인할 수 있었다.

{% highlight ruby %}
2024-09-20T16:10:28.478443Z 0 [System] [MY-010116] [Server] /usr/local/Cellar/mysql@8.0/8.0.39_3/bin/mysqld (mysqld 8.0.39) starting as process 23125
2024-09-20T16:10:28.484682Z 0 [Warning] [MY-010159] [Server] Setting lower_case_table_names=2 because file system for /usr/local/var/mysql/ is case insensitive
2024-09-20T16:10:28.499945Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2024-09-20T16:10:28.516815Z 1 [ERROR] [MY-012574] [InnoDB] Unable to lock ./ibdata1 error: 35
2024-09-20T16:10:29.520349Z 1 [ERROR] [MY-012574] [InnoDB] Unable to lock ./ibdata1 error: 35
2024-09-20T16:10:30.521216Z 1 [ERROR] [MY-012574] [InnoDB] Unable to lock ./ibdata1 error: 35
2024-09-20T16:10:31.522640Z 1 [ERROR] [MY-012574] [InnoDB] Unable to lock ./ibdata1 error: 35
2024-09-20T16:10:32.523130Z 1 [ERROR] [MY-012574] [InnoDB] Unable to lock ./ibdata1 error: 35
2024-09-20T16:10:33.524017Z 1 [ERROR] [MY-012574] [InnoDB] Unable to lock ./ibdata1 error: 35
{% endhighlight %}

크게 2가지 사항을 점검하고 해결할 수 있었다.

{% highlight ruby %}
# 1. 데몬으로 실행되고 있는 MySQL 종료
brew services stop mysql # 특정 버전의 경우: brew services stop mysql@8.0

# 2. mysqld 프로세스를 모두 종료
killall mysqld
{% endhighlight %}

{% highlight ruby %}
2024-09-20T16:31:35.482591Z 0 [System] [MY-010116] [Server] /usr/local/Cellar/mysql@8.0/8.0.39_3/bin/mysqld (mysqld 8.0.39) starting as process 28697
2024-09-20T16:31:35.494736Z 0 [Warning] [MY-010159] [Server] Setting lower_case_table_names=2 because file system for /usr/local/var/mysql/ is case insensitive
2024-09-20T16:31:35.516001Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2024-09-20T16:31:35.710324Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2024-09-20T16:31:36.068445Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2024-09-20T16:31:36.068497Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2024-09-20T16:31:36.105633Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '127.0.0.1' port: 33060, socket: /tmp/mysqlx.sock
2024-09-20T16:31:36.105685Z 0 [System] [MY-010931] [Server] /usr/local/Cellar/mysql@8.0/8.0.39_3/bin/mysqld: ready for connections. Version: '8.0.39'  socket: '/tmp/mysql.sock'  port: 3306  Homebrew.
{% endhighlight %}