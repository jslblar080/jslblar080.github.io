---
title:  "[Linear Algebra] 다른 eigenvalue로 선형독립을 이루는 eigenvector"
layout: post
---

서로 다른 eigenvalue(`λj≠λi`)를 가진 `n`개의 eigenvector가 `선형종속`을 이룬다고 가정하자.
{% highlight ruby %}
`c1*v1 + ... + cn*vn = 0 (cj≠0)` ①
{% endhighlight %}
A@vj = λj*vj를 만족하는 행렬 A를 ①의 좌측에 곱하면 ②가 얻어지고
{% highlight ruby %}
`c1*A@v1 + ... + cn*A@vn = 0`
<->
`c1*λ1*v1 + ... + cn*λn*vn = 0` ②
{% endhighlight %}
①에 λi (i는 1에서 n사이의 임의의 값)를 곱하면 ③이 얻어지고
{% highlight ruby %}
`c1*λi*v1 + ... + cn*λi*vn = 0` ③
{% endhighlight %}
②에서 ③을 빼면
{% highlight ruby %}
`c1*(λ1-λi)*v1 + ... + cn*(λn-λi)*vn = 0 (cj*(λj-λi)≠0)`
{% endhighlight %}
(n-1)개의 eigenvector에 대해서도 선형종속을 이루고 이 과정을 반복하면,
단일한 eigenvector가 선형종속을 이룬다(혹은 영 벡터가 된다)는 모순이 생긴다.