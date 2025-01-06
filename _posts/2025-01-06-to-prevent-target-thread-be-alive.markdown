---
title:  "[Java] 제한 시간 내에 완료되지 않은 타깃 스레드를 먼저 완료하고 호출한 스레드 진행하기"
layout: post
---
타깃 스레드의 `join` 메소드에 `timeout`을 설정하였더니 다음과 같이 제한 시간을 초과하자 main 스레드가 진행되고 이후에 타깃 스레드의 작업이 완료되는 것을 확인하였다.

{% highlight ruby %}
import java.util.logging.Logger;

public class BeforeApplication {

    private static final Logger log = Logger.getLogger(BeforeApplication.class.getName());
    private static final long TIMEOUT_MS = 3000;

    public static void main(String[] args) {

        Thread workerThread = new Thread(() -> {
            try {
                log.info(Thread.currentThread().getName() + "가 작업을 시작했습니다.");
                Thread.sleep(5000);
                log.info(Thread.currentThread().getName() + "가 작업을 성공적으로 완료했습니다.");
            } catch (InterruptedException e) {
                log.warning(Thread.currentThread().getName() + "가 InterruptedException을 만났습니다.");
                Thread.currentThread().interrupt();
            }
        }, "WorkerThread");

        workerThread.start();

        try {
            workerThread.join(TIMEOUT_MS);
        } catch (InterruptedException e) {
            log.warning(Thread.currentThread().getName() + "가 InterruptedException을 만났습니다.");
        }

        log.info(Thread.currentThread().getName() + "이 실행되고 있습니다.");
    }
}
{% endhighlight %}

{% highlight ruby %}
정보: WorkerThread가 작업을 시작했습니다.
정보: main이 실행되고 있습니다.
정보: WorkerThread가 작업을 성공적으로 완료했습니다.
{% endhighlight %}

내가 원했던 로직은 제한 시간을 설정하고, 타깃 스레드가 작업을 완료하지 못했더라도 호출한 main 스레드를 먼저 진행시키지 않는 것이었다.
이 부분을 해결하기 위해 타깃 스레드가 isAlive일 때 고의적으로 `interrupt`를 하고 곧이어 `join`하는 `ThreadTimeoutManager`라는 유틸리티 클래스를 작성하였다.
이 경우 catch문에 `InterruptedException` 처리하는 것을 염두에 두어야 한다.

{% highlight ruby %}
package util;

import java.util.logging.Logger;

public class ThreadTimeoutManager {

    private static final Logger log = Logger.getLogger(ThreadTimeoutManager.class.getName());

    public static void handle(Thread targetThread, long timeout) {

        try {
            targetThread.join(timeout);
            if (targetThread.isAlive()) {
                log.warning(">>> " + targetThread.getName() + " did not finish within " + timeout + "ms. Interrupting from " + Thread.currentThread().getName() + "...");
                targetThread.interrupt();
                targetThread.join(); // necessary to prevent target thread to be alive
            }
        } catch (InterruptedException e) {
            log.warning(">>> " + Thread.currentThread().getName() + " interrupted while waiting for " + targetThread.getName() + ".");
            Thread.currentThread().interrupt();
        }
    }
}
{% endhighlight %}

{% highlight ruby %}
import util.ThreadTimeoutManager;

import java.util.logging.Logger;

public class AfterApplication {

    private static final Logger log = Logger.getLogger(AfterApplication.class.getName());
    private static final long TIMEOUT_MS = 3000; // 3000 vs 6000

    public static void main(String[] args) {

        Thread workerThread = new Thread(() -> {
            try {
                log.info(Thread.currentThread().getName() + "가 작업을 시작했습니다.");
                Thread.sleep(5000);
                log.info(Thread.currentThread().getName() + "가 작업을 성공적으로 완료했습니다.");
            } catch (InterruptedException e) {
                log.warning(Thread.currentThread().getName() + "가 InterruptedException을 만났습니다.");
                Thread.currentThread().interrupt();
            }
        }, "WorkerThread");

        workerThread.start();

        ThreadTimeoutManager.handle(workerThread, TIMEOUT_MS);

        if (workerThread.isAlive()) {
            log.warning(workerThread.getName() + "는 아직도 살아있습니다. 나쁜 메시지!");
        } else {
            log.info(workerThread.getName() + "는 더 이상 살아있지 않습니다. 좋은 메시지!");
        }

        log.info(Thread.currentThread().getName() + "이 실행되고 있습니다.");
    }
}
{% endhighlight %}

{% highlight ruby %}
# TIMEOUT_MS = 3000 (제한 시간을 초과한 경우)
정보: WorkerThread가 작업을 시작했습니다.
경고: >>> WorkerThread did not finish within 3000ms. Interrupting from main...
경고: WorkerThread가 InterruptedException을 만났습니다.
정보: WorkerThread는 더 이상 살아있지 않습니다. 좋은 메시지!
정보: main이 실행되고 있습니다.

# TIMEOUT_MS = 6000 (제한 시간을 초과하지 않은 경우)
정보: WorkerThread가 작업을 시작했습니다.
정보: WorkerThread가 작업을 성공적으로 완료했습니다.
정보: WorkerThread는 더 이상 살아있지 않습니다. 좋은 메시지!
정보: main이 실행되고 있습니다.
{% endhighlight %}

만약에 `ThreadTimeoutManager`에서 try문 안의 if문에서 `interrupt()`만하고 `join()`을 생략하면 다음과 같이 `InterruptedException` 처리를 뒤늦게 하게 된다.

{% highlight ruby %}
# TIMEOUT_MS = 3000 (제한 시간을 초과한 경우)
정보: WorkerThread가 작업을 시작했습니다.
경고: >>> WorkerThread did not finish within 3000ms. Interrupting from main...
경고: WorkerThread는 아직도 살아있습니다. 나쁜 메시지!
경고: WorkerThread가 InterruptedException을 만났습니다.
정보: main이 실행되고 있습니다.
{% endhighlight %}