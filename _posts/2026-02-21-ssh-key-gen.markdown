---
title:  "[Linux] SSH 기반 GPU 클라우드의 인스턴스 접속"
layout: post
---
#### Lambda Labs에 등록할 키 생성

{% highlight ruby %}
ssh-keygen -t rsa -C "xxx@gmail.com"
{% endhighlight %}

Public SSH Key 저장 위치: ~/.ssh/id_rsa.pub 

{% highlight ruby %}
cat ~/.ssh/id_rsa.pub
# ssh-rsa xxx= xxx@gmail.com
{% endhighlight %}

#### [Lambda Labs 대시보드: SSH Keys → 공개키 붙여넣기](https://cloud.lambda.ai/ssh-keys) 

Private SSH Key 저장 위치: ~/.ssh/id_rsa

#### GPU 인스턴스 생성 후 SSH Login

{% highlight ruby %}
ssh ubuntu@instance.ip
{% endhighlight %}