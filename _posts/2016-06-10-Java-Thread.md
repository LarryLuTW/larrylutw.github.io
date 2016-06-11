---
layout: post
title: '[Java] 執行緒(Thread) 入門'
keywords: Java, 線程, 執行緒, 多工, parallel, 平行, Thread, Runnable
published: true
---

### 在開始之前先來談談 Program, Process, Thread 的不同<br>

- ### Program(程式)：<br>
寫出來的程式<br>
簡單來說就是那堆程式碼<br>
他們以資料的形式被存放在硬碟中<br>
還沒有跑起來<br>

- ### Process(程序)：<br>
跑起來的程式<br>
寫出來的 Program 可以讓他同時跑在很多地方<br>
這樣就可以產生很多 Process<br>

- ### Thread(執行緒)：<br>
一個 Process 裡面會有至少一個 Thread<br>
在 Java 中預設只有 main 一個<br>
可以建立很多 Thread 讓他們同時運作<br>

<img src="http://www.javatpoint.com/images/multithreading.JPG" height="360">

---

## 單執行緒程式(single threading)

平常的程式如果沒有建其他 Thread 的話都是單執行緒<br>
在 Java 中就是 main<br>
從 main 的頭開始跑<br>
main 跑完了整個程式就結束<br>
整個程式就只有 main 所在的那個 Thread<br>
我們通常把 main 所在的那個 Thread 稱為 main thread<br>

## 多執行緒程式(multithreading)

當你的程式有很多 thread 的時候<br>
系統會把它排進一個 Queue<br>
然後每個 thread 就會排隊輪流執行<br>
優先權高的可能會執行比較久<br>
但優先權低的也會執行到<br>
只是可能會一直被插隊<br>
<br>
<img src="http://ppt.cc/03mFV" height="250">

---

## 如何產生 Thread

Java 的 Thread 被定義在 java.lang.Thread<br>
Thread 的 Constructor 有兩種：<br>

1. Thread()
2. Thread(Runnable)

這篇只介紹比較簡單的第一種<br>

---

## 進入點

使用 class Thread 產生的 thread 物件<br>
會從物件裡的 run() 開始執行<br>
執行完了那個 thread 就結束<br>
就像 main() 一樣<br>
<br>
先來看一段簡單的 code<br>

```java
// Example.java

// 一個新的 Thread class
// 進入點為 run()
class MyThread extends Thread {
    public void run(){
        System.out.println("Hello World in MyThread");
    }
}

public class Example1 {
    public static void main(String[] args){
        Thread t1 = new MyThread();   // 建一個新的 Thread t1
        t1.start();                   // 讓 t1 開始執行
        System.out.println("Hello World in main Thread");
    }
}

/*
 *
 * 輸出結果：
 * Hello World in main Thread
 * Hello World in MyThread
 *
 */
```

有些人的輸出結果可能會跟我不一樣<br>
因為有多個 thread 同時在跑時系統會自動分配<br>
就像範例中 t1 跟 main thread 都要輸出<br>
系統就會幫他們排優先順序<br>
不過通常是 main thread 最大除非有調整優先度<br>
<br>
來看另外一個範例<br>

```java
// Example2.java

class MyThread extends Thread {
    private String x;

    public MyThread(int x){
        // turn to string
        this.x = String.valueOf(x);
    }

    public void run(){
        System.out.println("Hello I'm " + x);
    }
}

public class Example2 {
    public static void main(String[] args){
        Thread t1 = new MyThread(1);
        Thread t2 = new MyThread(2);
        Thread t3 = new MyThread(3);
        Thread t4 = new MyThread(4);
        Thread t5 = new MyThread(5);
        
        // 新增 5 個 thread

        t1.start();
        t2.start();
        t3.start();
        t4.start();
        t5.start();
    }
}

/*
 *
 * 輸出結果：
 * Hello I'm 1
 * Hello I'm 5
 * Hello I'm 3
 * Hello I'm 4
 * Hello I'm 2
 *
 */
```

這個範例有 5 個 thread<br>
系統會自己排誰先執行<br>
所以每次輸出都會不太一樣<br>

---

## 優先權

可以用`Thread.setPriority(int)`來設定優先權<br>
`priority`可以設定 1 ~ 10<br>
預設值是 5<br>
數字越大優先權越高<br>
用上面的範例稍作修改<br>

```java
// Example2.java

class MyThread extends Thread {
    private String x;

    public MyThread(int x){
        // turn to string
        this.x = String.valueOf(x);
    }

    public void run(){
        System.out.println("Hello I'm " + x);
    }
}

public class Example2 {
    public static void main(String[] args){
        Thread t1 = new MyThread(1);
        Thread t2 = new MyThread(2);
        Thread t3 = new MyThread(3);
        Thread t4 = new MyThread(4);
        Thread t5 = new MyThread(5);

        t1.setPriority(1);
        t2.setPriority(2);
        t3.setPriority(3);
        t4.setPriority(4);
        t5.setPriority(5);

        t1.start();
        t2.start();
        t3.start();
        t4.start();
        t5.start();
    }
}

/*
 *
 * 輸出結果：
 * Hello I'm 1
 * Hello I'm 5
 * Hello I'm 4
 * Hello I'm 3
 * Hello I'm 2
 *
 */
```

有比較照順序一點XD<br>
不過可能因為是 t1 先 start<br>
所以 t1 還是排在前面<br>
<br>
優先權低不代表一定比較晚執行<br>
只是代表他們同時競爭相同資源時<br>
會先給優先權大的<br>
譬如說同時要輸出或是存取某個檔案時<br>

---

## 其他改變優先權的方法

接下來這邊會介紹 sleep, yield, join<br>
用來等待其他 thread 或是先讓其他 thread 執行<br>

### sleep

顧名思義就是睡覺<br>
先睡一下等別人<br>

```java
// Sleep.java

class MyThread extends Thread {
    public void run(){
        try {
            sleep(2000);
            // 因為 sleep 有可能被中斷
            // 所以要 catch InterruptedException
            // 不然過不了編譯
        } catch(InterruptedException e){

        }

        System.out.println("I'm t1");
    }
}

public class Sleep {
    public static void main(String[] args){
        Thread t1 = new MyThread();
        t1.start();
        System.out.println("I'm main thread");
    }
}

/*
 *
 * 輸出結果：
 * I'm main thread
 * I'm t1
 *
 */
```

上面的 code 會先跑進 t1<br>
但是因為 t1 sleep 兩秒<br>
所以會先出現`I'm main thread`<br>
然後過一下子才出現`I'm t1`<br>

---

### join

等某個 thread 結束之後才繼續執行<br>

```java
// Join.java

class MyThread extends Thread {
    public void run(){
        try {
            sleep(2000);
        } catch(InterruptedException e){

        }
        System.out.println("I'm t1");
    }
}

public class Join {
    public static void main(String[] args){
        Thread t1 = new MyThread();
        t1.start();

        try {
            t1.join();
            // 呼叫 t1.join() 的 thread 要等 t1 跑完
            // 所以 main thread 要等 t1 跑完才會跑下面
        } catch(InterruptedException e){

        }

        System.out.println("I'm main thread");
    }
}

/*
 *
 * 輸出結果：
 * I'm t1
 * I'm main thread
 *
 */
```

因為 t1 要先 sleep 兩秒<br>
main thread 又在等 t1 完成<br>
所以整個程式會先閒置兩秒<br>
然後再依序輸出`I'm t1`, `I'm main thread`<br>

---

### yield

輪到自己的時候先放棄<br>
等下次輪到自己再繼續<br>

```java
class MyThread extends Thread {
    public void run(){
        try {
            sleep(2000);
        } catch(InterruptedException e){

        }
        System.out.println("I'm t1");
    }
}

public class Sleep {
    public static void main(String[] args){
        Thread t1 = new MyThread();
        t1.start();

        Thread.yield();
        // main thread 執行 yield 先讓別人跑
        // 但因為 t1 還在 sleep
        // 所以又換回 main thread 執行

        System.out.println("I'm main thread");
    }
}

/*
 *
 * 輸出結果：
 * I'm main thread
 * I'm t1
 *
 */
```

這個例子是 main thread yield 之後大家都不想執行<br>
所以又換 main thread 執行<br>
反之如果`yield()`之後別人想跑<br>
那就要先讓他跑<br>
等等才會輪回來自己<br>

---

## 總結
這邊只提到一些比較常見的用法<br>
之後會寫一些比較難的還有怎麼應用<br>

Thread 是個滿好用的東西<br>
但是要把它寫好真的也滿難的<br>
什麼時候誰要等誰<br>
然後不能同時讀檔案寫檔案<br>
要考慮很多事情<br>
