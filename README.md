# woo-utils
我自己的常用工具包。使用jdk1.8。所有第三方依赖，都需要使用者自行引入对应依赖，详见文档或pom.xml文件中scope为provided的依赖。

```xml
<dependency>
    <groupId>com.pugwoo</groupId>
    <artifactId>woo-utils</artifactId>
    <version>0.5.7</version>
</dependency>
```

## collect

还在为排序写Comparator，疑惑应该返回-1还是1吗？实际上，最友好的排序写法，应该是像SQL语句一样，直接指定order by一个可以比较大小的值。collect中的SortingUtils就可以这样来用。

而且，你还不用担心List中烦人的null值，你只需要指定null排在最前面还是最后面就可以了。

而且，排序还支持按多个值来排序，例如先按年龄排，再按收入排。

# Redis

为什么选择Redis？在主流分布式系统中，常用Redis作为分布式缓存，来存放登录态等信息。由于Redis的持久化特性和广泛被使用的运维基础，因此Redis会和MySQL一样的地位一样成为首选必选的存储方案。

我倾向于维护尽可能少的服务器。Zookeeper作为分布式的配置和分布式事务管理也是非常不错的方案，但没有办法充当redis的功能。而Redis则可以基本实现Zookeeper的特性。此外Redis是C程序，在Linux上少开一个Java进程将节省不少内存空间。因此作为服务器，我倾向于使用Redis代替Zookeeper。从各大云服务提供商产品看，redis都是弹性缓存的首选方案，也有云服务提供，反之zookeeper则没有，从这一点看，也符合我两年前的思路。

从运维角度考虑，我倾向redis上存放的内容都使用字符串明文，Object转换成json，byte[]转换成base64。虽然性能会有些损耗，但是对开发和运维都足够友好。在安全性方面，明文和二进制序列化的安全程度本质上一样的，我们应该通过密码和运维物理隔离的方式来保证线上数据的安全。

在这个项目中，我使用Redis实现了关于分布式事务非常常用的两种功能：

1. 限制分布式系统在指定的单位时间内，对某个业务的请求次数。参见limit.
2. 分布式锁。参见transaction。
3. 分布式自增id器。

使用方式：

如果是简单程序，请new一个RedisHelperImpl对象，设置上服务器的ip和端口、密码，即可使用；如果需要存储Object转换的，需要再设置上IRedisObjectConverter的实现对象。如果项目使用了Spring，那么推荐像配置一个jdbcTemplate那样配置一个RedisHelper，在代码中直接注入即可。




