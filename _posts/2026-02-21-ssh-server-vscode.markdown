---
title:  "[Linux] SSH 기반 GPU 클라우드의 인스턴스 생성 후 VS Code에서 접속"
layout: post
---
#### Lambda Labs에 등록할 키 생성

{% highlight ruby %}
ssh-keygen -t ed25519 -C "my@email.com"
{% endhighlight %}

Public SSH Key 저장 위치: ~/.ssh/id_ed25519.pub 

{% highlight ruby %}
cat ~/.ssh/id_ed25519.pub
# ssh-ed25519 xxx= my@email.com
{% endhighlight %}

#### [Lambda Labs 대시보드: SSH Keys → 공개키 붙여넣기](https://cloud.lambda.ai/ssh-keys) 

Private SSH Key 저장 위치: ~/.ssh/id_ed25519

#### GPU 인스턴스 생성 (임시 확인: ssh ubuntu@instance.ip → exit)

#### VS Code → Extensions → 'Remote - SSH' 설치
#### → Ctrl + Shift + P → Remote-SSH: Connect to Host → Configure SSH Hosts
#### → ~/.ssh/config

{% highlight ruby %}
# Read more about SSH config files: https://linux.die.net/man/5/ssh_config
Host lambda-gpu
    HostName instance.ip
    User ubuntu
    IdentityFile ~/.ssh/id_ed25519
{% endhighlight %}

#### 지정 Host로 SSH Login
{% highlight ruby %}
ssh lambda-gpu
{% endhighlight %}

#### → Ctrl + Shift + P → Remote-SSH: Connect to Host → lambda-gpu
#### (→ Select the platform of the remote host "lambda-gpu" → Linux)
#### → Open Folder → /home/ubuntu/ (OK)