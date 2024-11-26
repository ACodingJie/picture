> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/zsx_xiaoxin/article/details/123898171)

[CompletableFuture](https://so.csdn.net/so/search?q=CompletableFuture&spm=1001.2101.3001.7020) 是 jdk8 的新特性。CompletableFuture 实现了 CompletionStage 接口和 Future 接口，前者是对后者的一个扩展，增加了异步会点、流式处理、多个 Future 组合处理的能力，使 Java 在处理多任务的协同工作时更加顺畅便利。

### 一、创建异步任务

#### 1. supplyAsync

supplyAsync 是创建带有返回值的异步任务。它有如下两个方法，一个是使用默认线程池（[ForkJoinPool](https://so.csdn.net/so/search?q=ForkJoinPool&spm=1001.2101.3001.7020).commonPool()）的方法，一个是带有自定义线程池的重载方法

```
// 带返回值异步请求，默认线程池
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier)
 
// 带返回值的异步请求，可以自定义线程池
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier, Executor executor)
```

测试代码：

```
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<String> cf = CompletableFuture.supplyAsync(() -> {
            System.out.println("do something....");
            return "result";
        });
 
        //等待任务执行完成
        System.out.println("结果->" + cf.get());
}
 
 
public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 自定义线程池
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        CompletableFuture<String> cf = CompletableFuture.supplyAsync(() -> {
            System.out.println("do something....");
            return "result";
        }, executorService);
 
        //等待子任务执行完成
        System.out.println("结果->" + cf.get());
}
```

 测试结果：

![](https://i-blog.csdnimg.cn/blog_migrate/0a955eaf62bd64e9f3027dc4b9cb98b5.png)

####  2. runAsync

runAsync 是创建没有返回值的异步任务。它有如下两个方法，一个是使用默认线程池（ForkJoinPool.commonPool()）的方法，一个是带有自定义线程池的重载方法

```
// 不带返回值的异步请求，默认线程池
public static CompletableFuture<Void> runAsync(Runnable runnable)
 
// 不带返回值的异步请求，可以自定义线程池
public static CompletableFuture<Void> runAsync(Runnable runnable, Executor executor)
```

测试代码：

```
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Void> cf = CompletableFuture.runAsync(() -> {
            System.out.println("do something....");
        });
 
        //等待任务执行完成
        System.out.println("结果->" + cf.get());
}
 
 
public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 自定义线程池
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        CompletableFuture<Void> cf = CompletableFuture.runAsync(() -> {
            System.out.println("do something....");
        }, executorService);
 
        //等待任务执行完成
        System.out.println("结果->" + cf.get());
}
```

测试结果：

![](https://i-blog.csdnimg.cn/blog_migrate/abd63c71ba5d45a34c697cf3e9afccce.png)

#### 3. 获取任务结果的方法

```
// 如果完成则返回结果，否则就抛出具体的异常
public T get() throws InterruptedException, ExecutionException 
 
// 最大时间等待返回结果，否则就抛出具体异常
public T get(long timeout, TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException
 
// 完成时返回结果值，否则抛出unchecked异常。为了更好地符合通用函数形式的使用，如果完成此 CompletableFuture所涉及的计算引发异常，则此方法将引发unchecked异常并将底层异常作为其原因
public T join()
 
// 如果完成则返回结果值（或抛出任何遇到的异常），否则返回给定的 valueIfAbsent。
public T getNow(T valueIfAbsent)
 
// 如果任务没有完成，返回的值设置为给定值
public boolean complete(T value)
 
// 如果任务没有完成，就抛出给定异常
public boolean completeExceptionally(Throwable ex)
```

###  二、异步回调处理

#### 1.thenApply 和 thenApplyAsync

 thenApply 表示某个任务执行完成后执行的动作，即回调方法，会将该任务的执行结果即方法返回值作为入参传递到回调方法中，带有返回值。

测试代码：

```
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> cf1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf1 do something....");
            return 1;
        });
 
        CompletableFuture<Integer> cf2 = cf1.thenApplyAsync((result) -> {
            System.out.println(Thread.currentThread() + " cf2 do something....");
            result += 2;
            return result;
        });
        //等待任务1执行完成
        System.out.println("cf1结果->" + cf1.get());
        //等待任务2执行完成
        System.out.println("cf2结果->" + cf2.get());
}
 
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> cf1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf1 do something....");
            return 1;
        });
 
        CompletableFuture<Integer> cf2 = cf1.thenApply((result) -> {
            System.out.println(Thread.currentThread() + " cf2 do something....");
            result += 2;
            return result;
        });
        //等待任务1执行完成
        System.out.println("cf1结果->" + cf1.get());
        //等待任务2执行完成
        System.out.println("cf2结果->" + cf2.get());
}
```

测试结果：

![](https://i-blog.csdnimg.cn/blog_migrate/fba074ef4f72045e4900eb7139fc8310.png)   ![](https://i-blog.csdnimg.cn/blog_migrate/8a16aba9e7711e78b853b7dc440bcedb.png)

从上面代码和测试结果我们发现 thenApply 和 thenApplyAsync 区别在于，使用 thenApply 方法时子任务与父任务使用的是同一个线程，而 thenApplyAsync 在子任务中是另起一个线程执行任务，并且 thenApplyAsync 可以自定义线程池，默认的使用 ForkJoinPool.commonPool() 线程池。

#### 2.thenAccept 和 thenAcceptAsync

 thenAccep 表示某个任务执行完成后执行的动作，即回调方法，会将该任务的执行结果即方法返回值作为入参传递到回调方法中，无返回值。

测试代码

```
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> cf1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf1 do something....");
            return 1;
        });
 
        CompletableFuture<Void> cf2 = cf1.thenAccept((result) -> {
            System.out.println(Thread.currentThread() + " cf2 do something....");
        });
 
        //等待任务1执行完成
        System.out.println("cf1结果->" + cf1.get());
        //等待任务2执行完成
        System.out.println("cf2结果->" + cf2.get());
}
 
 
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> cf1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf1 do something....");
            return 1;
        });
 
        CompletableFuture<Void> cf2 = cf1.thenAcceptAsync((result) -> {
            System.out.println(Thread.currentThread() + " cf2 do something....");
        });
 
        //等待任务1执行完成
        System.out.println("cf1结果->" + cf1.get());
        //等待任务2执行完成
        System.out.println("cf2结果->" + cf2.get());
}
```

测试结果：

![](https://i-blog.csdnimg.cn/blog_migrate/236413c46a466b38ed06a8280a602636.png) ![](https://i-blog.csdnimg.cn/blog_migrate/573a5b030116e018d5eda444a5cf3409.png)从上面代码和测试结果我们发现 thenAccep 和 thenAccepAsync 区别在于，使用 thenAccep 方法时子任务与父任务使用的是同一个线程，而 thenAccepAsync 在子任务中可能是另起一个线程执行任务，并且 thenAccepAsync 可以自定义线程池，默认的使用 ForkJoinPool.commonPool() 线程池。

#### 2.thenRun 和 thenRunAsync

 thenRun 表示某个任务执行完成后执行的动作，即回调方法，无入参，无返回值。

测试代码：

```
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> cf1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf1 do something....");
            return 1;
        });
 
        CompletableFuture<Void> cf2 = cf1.thenRun(() -> {
            System.out.println(Thread.currentThread() + " cf2 do something....");
        });
 
        //等待任务1执行完成
        System.out.println("cf1结果->" + cf1.get());
        //等待任务2执行完成
        System.out.println("cf2结果->" + cf2.get());
}
 
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> cf1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf1 do something....");
            return 1;
        });
 
        CompletableFuture<Void> cf2 = cf1.thenRunAsync(() -> {
            System.out.println(Thread.currentThread() + " cf2 do something....");
        });
 
        //等待任务1执行完成
        System.out.println("cf1结果->" + cf1.get());
        //等待任务2执行完成
        System.out.println("cf2结果->" + cf2.get());
}
```

 测试结果：

![](https://i-blog.csdnimg.cn/blog_migrate/5603e62a804bdd970208310013ead4e9.png)

![](https://i-blog.csdnimg.cn/blog_migrate/cae8b343468641071dced960ed73fd5d.png)

从上面代码和测试结果我们发现 thenRun 和 thenRunAsync 区别在于，使用 thenRun 方法时子任务与父任务使用的是同一个线程，而 thenRunAsync 在子任务中可能是另起一个线程执行任务，并且 thenRunAsync 可以自定义线程池，默认的使用 ForkJoinPool.commonPool() 线程池。

#### 3.whenComplete 和 whenCompleteAsync

 whenComplete 是当某个任务执行完成后执行的回调方法，会将执行结果或者执行期间抛出的[异常](https://edu.csdn.net/cloud/pm_summit?utm_source=blogglc)传递给回调方法，如果是正常执行则异常为 null，回调方法对应的 CompletableFuture 的 result 和该任务一致，如果该任务正常执行，则 get 方法返回执行结果，如果是执行异常，则 get 方法抛出异常。

测试代码：

```
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> cf1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf1 do something....");
            int a = 1/0;
            return 1;
        });
 
        CompletableFuture<Integer> cf2 = cf1.whenComplete((result, e) -> {
            System.out.println("上个任务结果：" + result);
            System.out.println("上个任务抛出异常：" + e);
            System.out.println(Thread.currentThread() + " cf2 do something....");
        });
 
//        //等待任务1执行完成
//        System.out.println("cf1结果->" + cf1.get());
//        //等待任务2执行完成
        System.out.println("cf2结果->" + cf2.get());
    }
```

测试结果：

![](https://i-blog.csdnimg.cn/blog_migrate/28f62c4ea311930247ce67363e6ae4ec.png) 

 whenCompleteAsync 和 whenComplete 区别也是 whenCompleteAsync 可能会另起一个线程执行任务，并且 thenRunAsync 可以自定义线程池，默认的使用 ForkJoinPool.commonPool() 线程池。

#### 4.handle 和 handleAsync

 跟 whenComplete 基本一致，区别在于 handle 的回调方法有返回值。

测试代码：

```
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> cf1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf1 do something....");
            // int a = 1/0;
            return 1;
        });
 
        CompletableFuture<Integer> cf2 = cf1.handle((result, e) -> {
            System.out.println(Thread.currentThread() + " cf2 do something....");
            System.out.println("上个任务结果：" + result);
            System.out.println("上个任务抛出异常：" + e);
            return result+2;
        });
 
        //等待任务2执行完成
        System.out.println("cf2结果->" + cf2.get());
}
```

测试结果 ：

![](https://i-blog.csdnimg.cn/blog_migrate/4a7b6a9bd4f6781f9c79dd1e059b352f.png)

三、多任务组合处理 
----------

#### 1.thenCombine、thenAcceptBoth 和 runAfterBoth

这三个方法都是将两个 CompletableFuture 组合起来处理，只有两个任务都正常完成时，才进行下阶段任务。

区别：thenCombine 会将两个任务的执行结果作为所提供[函数](https://edu.csdn.net/cloud/pm_summit?utm_source=blogglc)的参数，且该方法有返回值；thenAcceptBoth 同样将两个任务的执行结果作为方法入参，但是无返回值；runAfterBoth 没有入参，也没有返回值。注意两个任务中只要有一个执行异常，则将该异常信息作为指定任务的执行结果。

测试代码：

```
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> cf1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf1 do something....");
            return 1;
        });
 
        CompletableFuture<Integer> cf2 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf2 do something....");
            return 2;
        });
 
        CompletableFuture<Integer> cf3 = cf1.thenCombine(cf2, (a, b) -> {
            System.out.println(Thread.currentThread() + " cf3 do something....");
            return a + b;
        });
 
        System.out.println("cf3结果->" + cf3.get());
}
 
 public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> cf1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf1 do something....");
            return 1;
        });
 
        CompletableFuture<Integer> cf2 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf2 do something....");
            return 2;
        });
        
        CompletableFuture<Void> cf3 = cf1.thenAcceptBoth(cf2, (a, b) -> {
            System.out.println(Thread.currentThread() + " cf3 do something....");
            System.out.println(a + b);
        });
 
        System.out.println("cf3结果->" + cf3.get());
}
 
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> cf1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf1 do something....");
            return 1;
        });
 
        CompletableFuture<Integer> cf2 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread() + " cf2 do something....");
            return 2;
        });
 
        CompletableFuture<Void> cf3 = cf1.runAfterBoth(cf2, () -> {
            System.out.println(Thread.currentThread() + " cf3 do something....");
        });
 
        System.out.println("cf3结果->" + cf3.get());
}
```

测试结果：

![](https://i-blog.csdnimg.cn/blog_migrate/6804634240f2da4bca23bf7c4e83115d.png) 

![](https://i-blog.csdnimg.cn/blog_migrate/a1bb536f39901174c19b6880623d4fcb.png)

#### ![](https://i-blog.csdnimg.cn/blog_migrate/c7f5665287372adca7dbb850f465f41b.png) 2.applyToEither、acceptEither 和 runAfterEither

这三个方法和上面一样也是将两个 CompletableFuture 组合起来处理，当有一个任务正常完成时，就会进行下阶段任务。

区别：applyToEither 会将已经完成任务的执行结果作为所提供函数的参数，且该方法有返回值；acceptEither 同样将已经完成任务的执行结果作为方法入参，但是无返回值；runAfterEither 没有入参，也没有返回值。

测试代码：

```
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<String> cf1 = CompletableFuture.supplyAsync(() -> {
            try {
                System.out.println(Thread.currentThread() + " cf1 do something....");
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "cf1 任务完成";
        });
 
        CompletableFuture<String> cf2 = CompletableFuture.supplyAsync(() -> {
            try {
                System.out.println(Thread.currentThread() + " cf2 do something....");
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "cf2 任务完成";
        });
 
        CompletableFuture<String> cf3 = cf1.applyToEither(cf2, (result) -> {
            System.out.println("接收到" + result);
            System.out.println(Thread.currentThread() + " cf3 do something....");
            return "cf3 任务完成";
        });
 
        System.out.println("cf3结果->" + cf3.get());
}
 
 
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<String> cf1 = CompletableFuture.supplyAsync(() -> {
            try {
                System.out.println(Thread.currentThread() + " cf1 do something....");
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "cf1 任务完成";
        });
 
        CompletableFuture<String> cf2 = CompletableFuture.supplyAsync(() -> {
            try {
                System.out.println(Thread.currentThread() + " cf2 do something....");
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "cf2 任务完成";
        });
 
        CompletableFuture<Void> cf3 = cf1.acceptEither(cf2, (result) -> {
            System.out.println("接收到" + result);
            System.out.println(Thread.currentThread() + " cf3 do something....");
        });
 
        System.out.println("cf3结果->" + cf3.get());
}
 
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<String> cf1 = CompletableFuture.supplyAsync(() -> {
            try {
                System.out.println(Thread.currentThread() + " cf1 do something....");
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("cf1 任务完成");
            return "cf1 任务完成";
        });
 
        CompletableFuture<String> cf2 = CompletableFuture.supplyAsync(() -> {
            try {
                System.out.println(Thread.currentThread() + " cf2 do something....");
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("cf2 任务完成");
            return "cf2 任务完成";
        });
 
        CompletableFuture<Void> cf3 = cf1.runAfterEither(cf2, () -> {
            System.out.println(Thread.currentThread() + " cf3 do something....");
            System.out.println("cf3 任务完成");
        });
 
        System.out.println("cf3结果->" + cf3.get());
}
```

测试结果： 

![](https://i-blog.csdnimg.cn/blog_migrate/6ace4e6d98c02a8a1a41958db848538d.png)

![](https://i-blog.csdnimg.cn/blog_migrate/9023dbe1ec5f0c4e4b6a48332383ffff.png)从上面可以看出 cf1 任务完成需要 2 秒，cf2 任务完成需要 5 秒，使用 applyToEither 组合两个任务时，只要有其中一个任务完成时，就会执行 cf3 任务，显然 cf1 任务先完成了并且将自己任务的结果传值给了 cf3 任务，cf3 任务中打印了接收到 cf1 任务完成，接着完成自己的任务，并返回 cf3 任务完成；acceptEither 和 runAfterEither 类似，acceptEither 会将 cf1 任务的结果作为 cf3 任务的入参，但 cf3 任务完成时并无返回值；runAfterEither 不会将 cf1 任务的结果作为 cf3 任务的入参，它是没有任务入参，执行完自己的任务后也并无返回值。

#### 3.allOf / anyOf 

allOf：CompletableFuture 是多个任务都执行完成后才会执行，只有有一个任务执行异常，则返回的 CompletableFuture 执行 get 方法时会抛出异常，如果都是正常执行，则 get 返回 null。

anyOf ：CompletableFuture 是多个任务只要有一个任务执行完成，则返回的 CompletableFuture 执行 get 方法时会抛出异常，如果都是正常执行，则 get 返回执行完成任务的结果。

测试代码：

```
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<String> cf1 = CompletableFuture.supplyAsync(() -> {
            try {
                System.out.println(Thread.currentThread() + " cf1 do something....");
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("cf1 任务完成");
            return "cf1 任务完成";
        });
 
        CompletableFuture<String> cf2 = CompletableFuture.supplyAsync(() -> {
            try {
                System.out.println(Thread.currentThread() + " cf2 do something....");
                int a = 1/0;
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("cf2 任务完成");
            return "cf2 任务完成";
        });
 
        CompletableFuture<String> cf3 = CompletableFuture.supplyAsync(() -> {
            try {
                System.out.println(Thread.currentThread() + " cf2 do something....");
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("cf3 任务完成");
            return "cf3 任务完成";
        });
 
        CompletableFuture<Void> cfAll = CompletableFuture.allOf(cf1, cf2, cf3);
        System.out.println("cfAll结果->" + cfAll.get());
}
 
 
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<String> cf1 = CompletableFuture.supplyAsync(() -> {
            try {
                System.out.println(Thread.currentThread() + " cf1 do something....");
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("cf1 任务完成");
            return "cf1 任务完成";
        });
 
        CompletableFuture<String> cf2 = CompletableFuture.supplyAsync(() -> {
            try {
                System.out.println(Thread.currentThread() + " cf2 do something....");
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("cf2 任务完成");
            return "cf2 任务完成";
        });
 
        CompletableFuture<String> cf3 = CompletableFuture.supplyAsync(() -> {
            try {
                System.out.println(Thread.currentThread() + " cf2 do something....");
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("cf3 任务完成");
            return "cf3 任务完成";
        });
 
        CompletableFuture<Object> cfAll = CompletableFuture.anyOf(cf1, cf2, cf3);
        System.out.println("cfAll结果->" + cfAll.get());
}
```

测试结果：

![](https://i-blog.csdnimg.cn/blog_migrate/7e2deb7dbc61f5d04b21f4b8a13b3a72.png)

 ![](https://i-blog.csdnimg.cn/blog_migrate/9f7b5e598afea78ebfe4d8933490ef58.png)