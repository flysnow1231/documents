# 通过canal把binlog同步至rocketmq，如何保证【消息顺序==binglog顺序】+ 多broker负载均衡？     
### **    【前提条件】  **  
**一个 queue 不能跨 broker；  
要想既有顺序又有多 broker 负载均衡，  
就必须让“顺序范围”缩小到某个 sharding key，  
再把不同 key 打散到多个 queue，而这些 queue 分布在多个 broker 上。     
queue是rocket的顺序单位**

**【单表数据顺序性+多broker负载均衡】**   
以订单表为例，单表的数据顺序性实质是：同一订单id的写sql在同一个queue，同一个订单顺序  
shardingKey -> queue  ：按订单id hash
queueIndex = hash(orderId) % 6  
orderId=1001 -> hash -> Queue2 -> BrokerB  
orderId=1002 -> hash -> Queue5 -> BrokerC  
orderId=1003 -> hash -> Queue1 -> BrokerA  
orderId=1004 -> hash -> Queue2 -> BrokerB    
这就实现了：
同一个 orderId 永远进同一个 queue  
所以同一个 orderId 的消息有序  
不同 orderId 会分散到不同 queue  
这些 queue 分布在多个 broker  
所以 broker 压力可以被打散  

**【多表数据顺序性 + 多broker负载均衡】**  
按表名hash，  
order_table -> Queue0 -> BrokerA
user_table  -> Queue1 -> BrokerB

问题：  
同表顺序有了  
但热点表会打爆单 queue / 单 broker

