# Sleep 和 Wait

当调用sleep方法后，当前线程进入阻塞状态。目的是让出CPU给其他线程运行的机会。但是由于sleep方法不会释放锁对象，所以在一个同步代码块中调用这个方法后，线程虽然休眠了，但其他线程无法访问它的锁对象。这是因为sleep方法拥有CPU的执行权，它可以自动醒来无需唤醒。而当sleep()结束指定休眠时间后，这个线程不一定立即执行，因为此时其他线程可能正在运行。

wait方法是Object类里的方法，当一个线程执行到wait()方法时，它就进入到一个和该对象相关的等待池中，同时释放了锁对象，等待期间可以调用里面的同步方法，其他线程可以访问，等待时不拥有CPU的执行权，否则其他线程无法获取执行权。当一个线程执行了wait方法后，必须调用notify或者notifyAll方法才能唤醒，而且是随机唤醒，若是被其他线程抢到了CPU执行权，该线程会继续进入等待状态。由于锁对象可以时任意对象，所以wait方法必须定义在Object类中，因为Obeject类是所有类的基类。

## 锁

sleep 不会释放锁。

wait 会释放锁。

## 传入参数

sleep()方法必须传入参数，参数就是休眠时间，时间到了就会自动醒来。

wait()方法可以传入参数也可以不传入参数，传入参数就是在参数结束的时间后开始等待，不穿如参数就是直接等待。

## 是否需要捕获异常

sleep方法必须要捕获异常，而wait方法不需要捕获异常。

## 作用范围

wait、notify和notifyAll方法只能在同步方法或者同步代码块中使用

sleep方法可以在任何地方使用。

## 调用者的区别

首先为什么wait、notify和notifyAll方法要和synchronized关键字一起使用？

因为wait方法是使一个线程进入等待状态，并且释放其所持有的锁对象，notify方法是通知等待该锁对象的线程重新获得锁对象，然而如果没有获得锁对象，wait方法和notify方法都是没有意义的，因此必须先获得锁对象再对锁对象进行进一步操作于是才要把wait方法和notify方法写到同步方法和同步代码块中了。



sleep方法是让某个线程暂停运行一段时间，其控制范围是由当前线程决定，运行的主动权是由当前线程来控制（拥有CPU的执行权）。