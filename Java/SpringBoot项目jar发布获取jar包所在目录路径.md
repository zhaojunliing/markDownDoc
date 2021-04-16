```java
		//第一种
        File path = new File(ResourceUtils.getURL("classpath:").getPath());
        if(!path.exists()) path = new File("");
        System.out.println(path.getAbsolutePath());
        //第二种
        System.out.println(System.getProperty("user.dir"));
        //第三种
        String path1 = ClassUtils.getDefaultClassLoader().getResource("").getPath();
        System.out.println(URLDecoder.decode(path1, "utf-8"));
        //第四种
        String path2 = ResourceUtils.getURL("classpath:").getPath();
        System.out.println(path2);
        //第五种
        ApplicationHome h = new ApplicationHome(getClass());
        File jarF = h.getSource();
        System.out.println(jarF.getParentFile().toString());

```



这几种获取项目目录的方式中，其中：

第一种、第二种得到的结果一致

第三种、第四种得到的结果一致

但是，不同的运行方式，他们的结果是有偏差的。下面我们来看下不同情况下依次打印的路径是什么。

先看下直接在idea中运行main方法启动项目时候，打印的路径，显示如下：





https://blog.csdn.net/liangcha007/article/details/88526181