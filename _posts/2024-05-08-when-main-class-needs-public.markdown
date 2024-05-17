---
title:  "[Java] main 클래스에서 public 접근 제어자가 필요한 예시"
layout: post
---
접근 제어자 중 `default`는 `public`과 다르게 해당 `package` 외부에서 접근하는 것을 제한합니다. `main() 메서드가 속한 클래스`의 경우, Java SE 프로그램의 진입점이기도 하고 다른 객체에서 사용하는 것을 고려하지 않기 때문에 일반적으로 `public` 접근 제어자를 생략해도 문제가 되지 않습니다.

하지만 다음과 같은 상황이라면 필요한 경우가 아닐까 하는 의문이 들었습니다. 

`main 클래스가 package 외부의 클래스와 교대로 상호작용 (Alternating Pattern)`

하단부의 코드는 [여기][main-class-without-public]에 작성했습니다.

`cycle/FeedBack1.java` (FeedBack2 및 FeedBack3도 같은 구조)

{% highlight ruby %}
public class FeedBack1 {

    private static int counter = 0;

    public static void response1(int total) {
        if (counter < total) {
            System.out.println(++counter + ": cycle 패키지의 response1() 메서드입니다.");
        } else return;

        WithPublic.main(WithPublic.strArgs);
    }
}
{% endhighlight %}

`main/WithPublic.java`

{% highlight ruby %}
public class WithPublic {
    
    public static String[] strArgs;

    public static void main(String[] args) {
        System.out.println("main 패키지의 main() 메서드입니다.");
        FeedBack1.response1(1);
        FeedBack2.response2(2);
        FeedBack3.response3(3);
    }
}
# 일반적으로 생각하는 결과
#main 패키지의 main() 메서드입니다.
#1: cycle 패키지의 response1() 메서드입니다.
#1: cycle 패키지의 response2() 메서드입니다.
#2: cycle 패키지의 response2() 메서드입니다.
#1: cycle 패키지의 response3() 메서드입니다.
#2: cycle 패키지의 response3() 메서드입니다.
#3: cycle 패키지의 response3() 메서드입니다.
{% endhighlight %}

main package 외부에서 static main() 메서드를 호출하므로 실제 실행결과는 다음과 같습니다.

{% highlight ruby %}
#main 패키지의 main() 메서드입니다.
#1: cycle 패키지의 response1() 메서드입니다.
#main 패키지의 main() 메서드입니다.
#1: cycle 패키지의 response2() 메서드입니다.
#main 패키지의 main() 메서드입니다.
#2: cycle 패키지의 response2() 메서드입니다.
#main 패키지의 main() 메서드입니다.
#1: cycle 패키지의 response3() 메서드입니다.
#main 패키지의 main() 메서드입니다.
#2: cycle 패키지의 response3() 메서드입니다.
#main 패키지의 main() 메서드입니다.
#3: cycle 패키지의 response3() 메서드입니다.
#main 패키지의 main() 메서드입니다.
{% endhighlight %}

[main-class-without-public]: https://github.com/jslblar080/main-class-without-public/tree/main/src/com/github/jslblar080