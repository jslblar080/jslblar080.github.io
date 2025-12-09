---
title:  "[Linear Algebra] X=AB의 Column/Row space"
layout: post
---
행렬 A와 B의 곱은 A 열벡터와 B 행벡터의 outer product의 합으로 표현된다.
{% highlight ruby %}
`X=A@B=Σa@b.T (a: 열벡터, b.T: 행벡터)`
{% endhighlight %}
[C (Column space): 열벡터의 덧셈과 스칼라곱에 대해 닫혀있는 선형공간]

[R (Row space): 행벡터의 덧셈과 스칼라곱에 대해 닫혀있는 선형공간]

X는 A 열벡터 또는 B 행벡터의 선형조합에서 기인하므로 다음과 같은 관계를 가진다.

{% highlight ruby %}
`C(X) ⊆ C(A)`
`R(X) ⊆ R(B)`
{% endhighlight %}

[rank: 행렬에서 선형독립을 이루는 열벡터 혹은 행벡터의 최대 갯수]

아래의 예시와 같이 X의 rank가 A의 것과 같거나 B의 것과 같다면 포함관계에서 등호관계로 바뀐다.

{% highlight ruby %}
`C(A@A.T) = C(A)`
`R(A.T@A) = R(A)`
{% endhighlight %}