- # 1. 类与对象
- ## 1.1 对象复制工具MapStruct
### 简介
- 在我们日常开发的分层结构的应用程序中，为了各层之间互相解耦，一般都会定义不同的对象用来在不同层之间传递数据，因此，就有了各种 `XXXDTO`、`XXXVO`、`XXXBO` 等基于数据库对象派生出来的对象，当在不同层之间传输数据时，不可避免地经常需要将这些对象进行相互转换。
- 此时一般处理两种处理方式：① 直接使用 `Setter` 和 `Getter` 方法转换、② 使用一些工具类进行转换（e.g. `BeanUtil.copyProperties`）。第一种方式如果对象属性比较多时，需要写很多的 `Getter/Setter` 代码。第二种方式看起来虽然比第一种方式要简单很多，但是因为其使用了反射，性能不太好，而且在使用中也有很多陷阱。而今天要介绍的主角 [MapStruct](https://links.jianshu.com/go?to=https%3A%2F%2Fmapstruct.org) 在不影响性能的情况下，同时解决了这两种方式存在的缺点。
### 特点
### 使用步骤
-
- ```java
  @Mappings({
      @Mapping(source = "quantity", target = "volume", defaultValue = "0"),
      @Mapping(source = "salesQtyInUom", target = "sapVolume"),
      @Mapping(source = "materialShortText", target = "materialText"),
      @Mapping(target = "qty", expression = "java(com.lpht.baxi.sell.order.service.impl.ITbSmOrderCalculateServiceImpl.getTbSmOrderQtyForStatic(sapOrderShop.getUnitPrice(), sapOrderShop.getSalesQtyInUom(), sapOrderShop.getMaterialShortText()))")
    })
  TbSmOrderInformationVO toTbSmOrderInformationVO(SapSplitOrderBo sapOrderShop);
  ```
- # 2. 异常
- # 3. 导入导出发邮件
-
- # 4. 单测覆盖率
- # 5. Stream
- # 6. 验证 or 生成工具
- # 7. 日期和时间
- # 8. 正则表达式
- # 9. 配置
- # 10. Mybatis
- # 11. 反射
- # 12. 集合
- # 12.1 List
- ## a. 求交集
- 两个 list 求交集, 一种方式是手动遍历, 然后判断是否 contains, 然后添加到结果 list 中。这里介绍另外一个方法
  直接调用 list1.retainAll(list2), 调用完成后, list1 中不在 list2 的元素都会被剔除, 此时 list1 就是交集。
- List.retainAll
  collapsed:: true
	- ```java
	  /**
	   * retain
	   * 保留
	   */
	  @Test
	  public void testRetain() {
	      List<String> list1 = new ArrayList<>();
	      list1.add("03");
	      list1.add("02");
	      list1.add("01");
	  
	      List<String> list2 = new ArrayList<>();
	      list2.add("02");
	      list2.add("03");
	  
	      // ⭐️ 🧣list1 只保留在 lists2 中的元素
	      list1.retainAll(list2);
	  
	      System.out.println(list1);
	  }
	  ```
-
- ## b. 获取集合的前几个元素
- List.subList
  collapsed:: true
	- ```java
	  List.subList(start,end);
	  //⭐️ start：起始元素的下标 
	  //⭐️ end：结束元素的下标
	  ```
-
- # 13. 锁
  collapsed:: true
	- ## 13.1 分布式锁
	- ```java
	  /**
	   * @Title: RedisLockUtil.java
	   * @Package com.wxb.common.redis
	   * @Description:
	   * @author tansl
	   * @date 2020年12月9日
	   * @version V1.0
	   */
	  package com.wxb.common.redis;
	  
	  import com.wxb.common.utils.spring.SpringUtils;
	  import lombok.extern.slf4j.Slf4j;
	  import org.springframework.data.redis.core.RedisTemplate;
	  import org.springframework.data.redis.core.script.DefaultRedisScript;
	  import org.springframework.data.redis.core.script.RedisScript;
	  
	  import java.util.Collections;
	  import java.util.concurrent.TimeUnit;
	  
	  /**
	   * @ClassName: RedisLockUtil
	   * @Description:
	   * @author tansl
	   * @date 2020年12月9日
	   * @version V1.0
	   */
	  @Slf4j
	  public class RedisLockUtil {
	  
	      /**
	       * 饿汉式 单例
	       */
	      private static RedisTemplate<String, Object> redisTemplate = SpringUtils.getBean("redisTemplate");
	  
	      public final static String LOCK = "A_LOCK";
	  
	      private RedisLockUtil() {
	  
	      }
	  
	      /**
	       * 加锁标志
	       */
	      public static final String REDIS_LOCK = "REDIS_LOCK:";
	      private static final Long SUCCESS = 1L;
	      private static long LOCK_TIMEOUT = 60L; // 获取锁的超时时间，60秒
	  
	      private static long EXPIRE_TIME = 5 * 60L; // 最长锁定时间，5分钟
	  
	      /**
	       * 加锁 应该以： lock(); try { doSomething(); } finally { unlock()； } 的方式调用
	       *
	       * @param value 要锁的数据主键
	       * @return
	       */
	      public static boolean lock(String value) {
	  
	          return tryLock(getKey(value), value, -1);
	      }
	  
	      /**
	       *
	       * @Title: lock
	       * @Description: redis 分布式锁
	       * @param value 要锁定的数据主键
	       * @param timeout 等待时间
	       *  @return boolean @throws
	       */
	      public static boolean lock(String value, long timeout) {
	  
	          return tryLock(getKey(value), value, timeout);
	      }
	  
	      /**
	       * 尝试获取锁 立即返回
	       * @param key
	       * @param value
	       * @param timeout
	       * @return
	       */
	      public static boolean lock(String key, String value, long timeout) {
	          return redisTemplate.opsForValue().setIfAbsent(key, value, timeout, TimeUnit.SECONDS);
	      }
	  
	      /**
	       * 释放锁
	       * @param key
	       * @param value
	       * @return
	       */
	      public static boolean releaseLock(String key, String value) {
	          String lua = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
	          DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>(lua, Long.class);
	          Long result = redisTemplate.execute(redisScript, Collections.singletonList(key), value);
	          return 1L == result;
	      }
	  
	      /**
	       *
	       * @Title: getKey
	       * @Description: 通用key的生成方法
	       * @param value
	       * @return     * String
	       * @throws
	       */
	      public static String getKey(String value) {
	  
	          return REDIS_LOCK + value;
	      }
	  
	      /**
	       * 加锁，无阻塞
	       *
	       * @param
	       * @param
	       * @return
	       */
	      private static Boolean tryLock(String key, String value, long timeout) {
	          boolean ret = false;
	          Long start = System.currentTimeMillis();
	          if (timeout == -1) {
	              timeout = LOCK_TIMEOUT;
	          }
	          try {
	              while (true) {
	                  // SET命令返回OK ，则证明获取锁成功
	                  ret = redisTemplate.opsForValue().setIfAbsent(key, value, EXPIRE_TIME, TimeUnit.SECONDS);
	                  if (ret) {
	                      return true;
	                  }
	                  Thread.sleep(100);// 延迟100毫秒
	                  // 否则循环等待，在timeout时间内仍未获取到锁，则获取失败
	                  long end = System.currentTimeMillis() - start;
	                  if (end >= timeout) {
	                      return false;
	                  }
	  
	              }
	          } catch (InterruptedException e) {
	              e.printStackTrace();
	              log.error("RedisLockUtil 获取锁失败:{}", e.getMessage());
	              Thread.currentThread().interrupt();
	          }
	  
	          return ret;
	      }
	  
	      /**
	       * 请调用单个参数的方法
	       * key：键
	       */
	      public static Boolean unlock(String key) {
	          try {
	              Boolean result = redisTemplate.delete(key);
	              log.info("RedisLockUtil unlock result:{}", result);
	              if (result) {
	                  return Boolean.TRUE;
	              }
	          } catch (Exception e) {
	              log.error("RedisLockUtil unlock failed. e:{}", e);
	              return Boolean.FALSE;
	          }
	          return Boolean.FALSE;
	      }
	  }
	  ```
-