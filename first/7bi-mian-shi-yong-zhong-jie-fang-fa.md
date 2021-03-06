# 7:避免使用终结方法

终结方法（finalizer）通常是不可预测的，也是很危险的，一般情况下是不必要的。

## 为什么要避免使用

### 1

终结方法的缺点在于不能保证会被及时的执行。从一个对象变得不可达开始，到它的终结方法被执行，所花费的这段时间是任意长的。

> 所以，注重时间的任务不应该由终结方法来完成，例如，用终结方法来关闭已打开的文件，这是很严重的错误。


### 2

及时的执行终结方法正是垃圾回收算法的一个主要功能，不同的 jvm 的实现方式有所不同，所以在你自己的电脑上的 jvm 可能运行的很好，但是在其他人的 jvm 下可能差别会很大。

### 3

java 语言规范不保证终结方法会被及时的执行，甚至根本就不保证它们会被执行。当一个程序终结的时候，某些已经无法访问的对象上的终结方法却根本没有执行，这是完全有可能的。

### 4

不要被 System.gc System.runFinalization 这两个所诱惑，它们却是增加了终结方法被执行的机会，但是它们也并不能保证终结方法一定会被执行。唯一可以保证终结方法被执行的方法是 System.runFinalizersOnExit 以及 Runtime.runFinalizersOnExit， 然而它们都有致命的缺陷，已经被废弃了。

### 5

如果未被捕捉的异常在终结过程中被抛出来，那么这种异常可以被忽略，并且该对象的终结过程也会终止。未被捕获的异常会使对象处于破坏的状态，如果另一个线程企图使用这种被破坏的对象，则可能发生任何不确定的行为。

### 6 

使用终结方法，会有一个严重的性能损失。

## 如何正确的终止

如果一个类中封装的资源（例如文件或者线程）确实需要终止，应该怎么做才能不用编写终结方法呢？

只需要提供一个显示的终结方法，并要求客户端在每个实例不再有用的时候显示额调用合格方法。

> 该类必须记录下自己是否已经被终止了。

比如 javaio 中，各个流的 close() 方法。

显示d额终止方法通常与 try-finally 结构结合起来使用。

## 终结方法有什么好处

### 1

当对象的所有者忘记调用前面段落中建议d 显示终止方法时，终结方法可以充当“安全网”

> 迟一点释放资源总比不释放好。

### 2

第二个用途与本地对等体有关。

> 本地对等体是一个本地对象，普通对象通过本地方法委托给一个本地对象，因为本地对等体b㐊一个普通d额对象，所以垃圾回收器不会知道他，当它的 java 对等体被回收的时候，他不会被回收。

在本地对等体并不拥有关键资源的情况下，终结方法是”回收“它的最好选择。如果本地对等体有需要及时回收的资源，那么该类就应该提供合适的显示终止方法。


## 总结

总之，除非是作为安全网，或者是为了终止非关键d额本地资源，不要使用终结方法。



