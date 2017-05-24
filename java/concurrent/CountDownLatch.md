
###CountDownLatch是一个同步工具类，它允许一个或多个线程一直等待，直到其他线程的操作执行完后再执行

```
public class CountDownLatchDemo {      
    public static void main(String[] args) throws InterruptedException {  
        //构造器中的计数值（count）实际上就是闭锁需要等待的线程数量。
        //这个值只能被设置一次，而且CountDownLatch没有提供任何机制去重新设置这个计数值。
        CountDownLatch latch=new CountDownLatch(2);
        List<Worker> workers = new ArrayList<>(2);
        workers.add(new Worker(latch));
        workers.add(new Worker(latch));
        //Start service checkers using executor framework
        Executor executor = Executors.newFixedThreadPool(workers.size());
        for(final Worker v : workers)
        {
            executor.execute(v);
        }
        // 阻塞直到latch值变成0
        latch.await();
        System.out.println("all work done at "+sdf.format(new Date()));  
    }  
      
      
    static class Worker extends Thread{  
       
        CountDownLatch latch;  
        public Worker(CountDownLatch latch){  
             this.latch=latch;  
        }  
        public void run(){
          try{
            doWork();//do sth
          }finally{
            latch.countDown();// finish work countdown
          }            
        }  
          
        private void doWork(){  
           // ...business code
        }  
    }  
      
       
} 
