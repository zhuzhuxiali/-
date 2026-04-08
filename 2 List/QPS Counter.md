# QPS Counter

统计各接口QPS计数
使用一个双向循环链表结构




```Java
	//AtomicInteger是原子整数类，在多线程环境下线程安全，不会出现错误（普通int在多线程会出问题）
	static AtomicInteger qpsCount = 100; //线程安全，1秒内有100次可请求额度
    //volatile是内存可见性修饰符，保证多线程下lastSenconds的修改能立刻被所有线程看到，不会出现缓存不一致
    static volatile long lastSenconds =  System.currentTimeMillis()/1000;//把当前时间转成秒数，判断是否进入下一个1秒窗口
    
    //1 计数器 
    public static boolean tryAcquire() {

        //获取当前时间的秒数
        long current = System.currentTimeMillis()/1000;
        //当前秒数==上一个记录的秒数，则还在同一个1秒窗口内
        if(current == lastSenconds){

			//同一个窗口内：检查还有没有剩余额度
			//qpsCount先和0比大小，再减1（后置--的优先级）
            if (qpsCount-- > 0) {//CAS api：原子性减1，同时判断是否>0
                return true;//还有额度：放行请求
            } else {
                //限流：额度用完了，拒绝请求
                return false;
            }

        } else{//下一个时间窗口
            lastSenconds = current;//更新时间窗口
            qpsCount = 100;//重置额度为100，新窗口重新计数
            return true;//新窗口第一个请求，直接放行
        }   
    }
```







