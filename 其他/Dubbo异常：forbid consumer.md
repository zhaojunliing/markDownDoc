# Dubbo异常：forbid consumer
id: dubbo-forbid-whitelist
title: Dubbo异常：forbid consumer
date: 2016-06-14 21:56:17
tags: [DUBBO]
---

<section style="padding: 2rem 4rem;background:#333; text-align:center;">
<div style="color:#fff;font-size:3.25rem; font-weight:bold;">DUBBO</div>
</section>

使用Dubbo开发过程中，经常会遇到forbid consumer whitelist/blacklist的错误.  
日志提示当前调用者被禁止访问某个服务，请检查下注册中心访问列表，还有黑名单和白名单。  
实际情况是我并根本没有对服务做白名单和黑名单机制.<!-- more -->
``` log
com.alibaba.dubbo.rpc.RpcException: Forbid consumer 10.159.98.101 access service com.ai.ecs.order.api.OrderService from registry 10.154.73.105:2181 use dubbo version 2.5.3, Please check registry access list (whitelist/blacklist).
```

根据异常栈，抛出这个异常的代码在RegistryDirectory的第579行，如下：
``` java
public List<Invoker<T>> doList(Invocation invocation) {
 if (forbidden ) {
     throw new RpcException(RpcException.FORBIDDEN_EXCEPTION , ” Forbid consumer “ +  NetUtils. getLocalHost() + ” access service “ +        getInterface().getName() + ” from registry “ + getUrl().getAddress() + ” use dubbo version “ + Version.getVersion() + “, Please check registry access list (whitelist/blacklist).”);
 }
```

forbidden变量默认为false，那么什么时候变成true了呢？  
看RegistryDirectory的这段代码：
``` java
private void refreshInvoker(List<URL> invokerUrls){
   if (invokerUrls != null && invokerUrls.size() == 1 && invokerUrls.get(0) != null && Constants.EMPTY_PROTOCOL .equals(invokerUrls.get(0).getProtocol())) {
            this.forbidden = true; // 禁止访问
            this.methodInvokerMap = null; // 置空列表
            destroyAllInvokers(); // 关闭所有Invoker
   }
```  
如果invokerUrls的size为1，并且url的协议头是Constants.EMPTY_PROTOCOL时，则设置forbidden为true，Constants.EMPTY_PROTOCOL的值是empty。
 
refreshInvoker方法什么时候被调用呢？当某个服务的provider有变化时就会被调用，例如zookeeper上某个服务的provider目录里的内容发生变化，则zk监听器会被触发，由于provider的数量会发生变化，例如有一个新的provider启动了，有一个provider下线了，所以必须刷新本地的对provider的连接，具体逻辑就在refreshInvoker方法里。

当zookeeper初次订阅或者订阅的信息有变更时，都会触发toUrlsChanged方法，看看这个方法内部都做了什么，完整代码如下：  
``` java
private List<URL> toUrlsWithEmpty(URL consumer, String path, List<String> providers) {
        List<URL> urls = toUrlsWithoutEmpty(consumer, providers);
        if (urls == null || urls.isEmpty()) {
        	int i = path.lastIndexOf('/');
        	String category = i < 0 ? path : path.substring(i + 1);
        	URL empty = consumer.setProtocol(Constants.EMPTY_PROTOCOL).addParameter(Constants.CATEGORY_KEY, category);
            urls.add(empty);
        }
        return urls;
    }
```  

可见如果toUrlsWithoutEmpty的结果是空或者size为0，则强制返回一个protocol为empty的url，看来源头就在这里了。传入的List<String> providers实际上就是最新的服务提供者信息，当某个服务没有任何provider时，providers就变为一个size为0的List了，导致返回一个协议头为empty的url，进而导致forbidden为true，屏蔽了consumer调用。