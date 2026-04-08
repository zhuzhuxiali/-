# QPS Counter

统计各接口QPS计数
使用一个双向循环链表结构




```Java
	//AtomicInteger是原子整数类，在多线程环境下线程安全，不会出现错误（普通int在多线程会出问题）
	static AtomicInteger qpsCount = 100; //线程安全
    static volatile long lastSenconds =  System.currentTimeMillis()/1000;
    
    //1 计数器 
    public static boolean tryAcquire() {

        long current = System.currentTimeMillis()/1000;
        if(current == lastSenconds){
            
            if (qpsCount-- > 0) {//CAS api
                return true;
            } else {
                //限流
                return false;
            }
            
        } else{//下一个时间窗口
            lastSenconds = current;
            qpsCount = 100;
            return true;
        }   
    }
```







