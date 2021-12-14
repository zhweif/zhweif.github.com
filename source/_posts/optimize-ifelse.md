---
title: 优化Java中if-else的几种方式
date: 2021-12-14 09:15:41
author: 锋哥
tags:
- Java
categories:
- Java
thumbnail:
blogexcerpt: 有时候代码中会有很多的if-else逻辑，不仅看起来丑陋，维护起来也比较麻烦。这里介绍几种可以优化if-else方法。
---

### 一 提前return,去除不必要的else

- 如果if-else代码块包含return语句，可以考虑通过提前return，把多余else干掉，使代码更加优雅

```java
public class Test {
    public void doAction(Object condition) {
        // 优化前
        if(condition){
            //doSomething
        }else{
            return ;
        }

        // 优化后
        if (!condition){
            return ;
        }
        //doSomething    
    }
    
}
```
### 二 使用条件三目运算符

- 使用条件三目运算符可以简化某些if-else,使代码更加简洁，更具有可读性。

```java
public class Test {
    public void doAction() {
        // 优化前
        int  price ;
        if(condition){
            price = 80;
        }else{
            price = 100;
        }

        //优化后
        int price = condition ? 80 : 100;
    }
}
   
```

### 三 使用枚举

- 在某些时候，使用枚举也可以优化if-else逻辑分支，按个人理解，它也可以看做一种表驱动方法。

```java
    // 优化前
    public class Test {
        public void doAction() {
            String OrderStatusDes;
            if(orderStatus==0){
                OrderStatusDes ="订单未支付";
            }else if(OrderStatus==1){
                OrderStatusDes ="订单已支付";
            }else if(OrderStatus==2){
                OrderStatusDes ="已发货";
            }
        }
    }   
    
    
    // 优化后
    // 1.定义枚举类
    public enum OrderStatusEnum {
        UN_PAID(0,"订单未支付"),PAIDED(1,"订单已支付"),SENDED(2,"已发货"),;
    
        private int index;
        private String desc;

        public int getIndex() {
            return index;
        }

        public String getDesc() {
            return desc;
        }

        OrderStatusEnum(int index, String desc){
            this.index = index;
            this.desc =desc;
        }

        public static OrderStatusEnum of(int orderStatus) {
            for (OrderStatusEnum temp : OrderStatusEnum.values()) {
                if (temp.getIndex() == orderStatus) {
                    return temp;
                }
            }
            return null;
        }
    }
    // 2.优化前的if else 可简化为一句
    public class Test {
        public void doAction() {
            String OrderStatusDes = OrderStatusEnum.of(orderStatus).getDesc();
        }
    } 
```
### 四 合并条件表达式

- 如果有一系列条件返回一样的结果，可以将它们合并为一个条件表达式，让逻辑更加清晰。

```java
public class Test {
    // 优化前
    double getVipDiscount() {
        if(age<18){
            return 0.8;
        }
        if("深圳".equals(city)){
            return 0.8;
        }
        if(isStudent){
            return 0.8;
        }
        //do somethig
    }

    // 优化后
    double getVipDiscount(){
        if(age<18|| "深圳".equals(city)||isStudent){
            return 0.8;
        }
        //doSomthing
    }   
}
```

### 五 使用Optional

- 有时候if-else比较多，是因为非空判断导致的，这时候你可以使用java8的Optional进行优化。

```java
public class Test {
    
    String str = "jay@huaxiao";
    public void doAction() {
        // 优化前
        if (str != null) {
            System.out.println(str);
        } else {
            System.out.println("Null");
        }

        // 优化后
        Optional<String> strOptional = Optional.of("jay@huaxiao");
        strOptional.ifPresentOrElse(System.out::println, () -> System.out.println("Null"));
    }
}
```

### 六 表驱动法

- 表驱动法，又称之为表驱动、表驱动方法。
  表驱动方法是一种使你可以在表中查找信息，而不必用很多的逻辑语句（if或Case）来把它们找出来的方法。以下的demo，把map抽象成表，在map中查找信息，而省去不必要的逻辑语句。

```java
public class Test {
    // 优化前
    public void doAction(int param){
        if (param.equals(value1)) {
            doAction1(someParams);
        } else if (param.equals(value2)) {
            doAction2(someParams);
        } else if (param.equals(value3)) {
            doAction3(someParams);
        }
        // ...
    }
}

public class Test {
    // 优化后
    Map<Integer, Function> actionMappings = new HashMap<>();
    public void init (){
        // 初始化
        actionMappings.put(value1, (someParams) -> { doAction1(someParams);});
        actionMappings.put(value2, (someParams) -> { doAction2(someParams);});
        actionMappings.put(value3, (someParams) -> { doAction3(someParams);});
    }
    
    public void doAction(int param){
        // 省略多余逻辑语句
        actionMappings.get(param).apply(someParams);    
    }
}
```

### 七 优化逻辑结构，让正常流程走主干

```java
    public class Test {
        // 优化前
        public double getAdjustedCapital(){
            if(capital <= 0.0 ){
                return 0.0;
            }
            if(intRate > 0 && duration >0){
                return (income / duration) * ADJ_FACTOR;
            }
            return 0.0;
        }
    
        // 优化后
        public double getAdjustedCapital(){
            if(capital <= 0.0 ){
                return 0.0;
            }
            if(intRate <= 0 || duration <= 0){
                return 0.0;
            }
    
            return (income / duration) * ADJ_FACTOR;
        }
    }
```

### 八 策略模式+工厂方法

将if里面的逻辑拆成一个个策略方法,使用工厂方法获取对应的策略

#### 实现策略模式的方法

可以直接使用常规的策略模式,配合spring注解也可以非常方便的实现  
使用注解的方式实现:  
   例如：根据从移动端产生的订单和pc端产生的订单分别做不同的处理。定义一个公共的订单处理的interface，不同来源的订单各自实现自己的处理逻辑，外部只需要调用公共的订单处理接口即可达到不同的目的
  
   1. 自定义订单处理方式的注解

   ```java
   @Target(ElementType.TYPE)
   @Retention(RetentionPolicy.RUNTIME)
   @Documented
   @Service
   public @interface OrderHandlerType {
       /**
       * 订单来源
       */
       String source();

   }
   ```

   2. 创建策略公共接口并实现不同的策略

   ```java
       public interface OrderHandler {
           /**
            * 订单处理
            */
           void handle(String source);
       }
               
               
       @OrderHandlerType(source = "mobile")
       @Service
       public class MobileOrderHandlerImpl implements OrderHandler {
           /**
            * 移动端订单处理
            */
           @Override
           public void handle(String source) {
               // do something ...
               System.out.println("source : " + source);
           }
       }
                   
       @OrderHandlerType(source = "pc")
       @Service
       public class PcOrderHandlerImpl implements OrderHandler {
           /**
            * pc订单处理
            */
           @Override
           public void handle(String source) {
               // do something ...
               System.out.println("source : " + source);
           }
       }
   ```
         
   3. 订单处理接口
         
   ```java
      public class OrderServiceImpl {
          // 将订单的来源作为key,handler作为value存起来
          private Map<String, OrderHandler> map = new HashMap<>();
              
          /**
           * 注入策略实现方法
           */
          @Autowired
          public void setMap(List<OrderHandler> orderHandlerList) {
              map = orderHandlerList.stream().collect(Collectors.toMap(orderHandler ->
                              AnnotationUtils.findAnnotation(orderHandler.getClass(), OrderHandlerType.class).source,
                      v -> v, (v1, v2) -> v1));
          }
              
          public void order(String source) {
              // do something ...
              // 获取订单处理策略
              OrderHandler orderHandler = this.map.get(source);
              if (orderHandler == null) {
                  // do something ...
              }
              // 执行订单处理策略
              orderHandler.handle(source);
              // do something ...
          }
      }
   ```

如果具体的策略实现方法里面的逻辑只有一个条件，比如以上的根据订单的来源做不同的处理，那么上面这种实现方式就可以，如果需要根据多个条件组合成的结果才能能确定不同策略实现的逻辑，那就需要对上面的实现方式做一些修改。  
    
   例如： 需要根据订单的来源和支付方式的组合条件做不同的处理逻辑  
   思路： 将存放策略的map修改为以OrderHandlerType作为key,OrderHandler作为value，这样的话，无论需求怎样变更，只需要扩展OrderHandlerType和OrderHandler就可以，不会影响到代码的主要流程，扩展性极高

1. 修改注解 OrderHandlerType 增加支付方式
  
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Service
public @interface OrderHandlerType {
    /**
     * 订单来源
     */
    String source();
    
    /**
     * 支付方式
     */
    String paymentMethod();
}
```
        
2. 现在存放策略的map的key的类型是注解OrderHandlerType，它是一个interface，为了能够从策略map中get到需要实现它
          
```java
public class OrderHandlerTypeImpl implements OrderHandlerType {
    
    /**
     * 订单来源
     */
    private final String source;
    
    /**
     * 支付方式
     */
    private final String paymentMethod;
    
    public OrderHandlerTypeImpl(String source, String paymentMethod) {
        this.source = source;
        this.paymentMethod = paymentMethod;
    }
    
    /**
     * 订单来源
     */
    @Override
    public String source() {
        return source;
    }
    
    /**
     * 支付方式
     */
    @Override
    public String paymentMethod() {
        return paymentMethod;
    }
    
    /**
     * Returns the annotation type of this annotation.
     *
     * @return the annotation type of this annotation
     */
    @Override
    public Class<? extends Annotation> annotationType() {
        return OrderHandlerType.class;
    }
    
    /**
     * 一定重写hashcode和equals方法，否则策略map就不能get出来相应的策略，
     *
     * 策略map中的key的类型是annotation,也是一个interface，在运行时jdk动态代理会生成其代理
     *
     * 为了能使自己的实现类和jdk的代理形同，需要重写hashcode和equals方法
     *
     * 重写可参照AnnotationInvocationHandler 的 private Boolean equalsImpl(Object var1); 和 private int hashCodeImpl()；
     *
     */    
    @Override
    public int hashCode() {
        int hashCode = 0;
        hashCode += (127 * "source".hashCode()) ^ source.hashCode();
        hashCode += (127 * "paymentMethod".hashCode()) ^ paymentMethod.hashCode();
        return hashCode;
    }
    
    
    @Override
    public boolean equals(Object obj) {
        if (!(obj instanceof OrderHandlerType)) {
            return false;
        }
        OrderHandlerType other = (OrderHandlerType) obj;
        return source.equals(other.source()) && paymentMethod.equals(other.paymentMethod());
    }
}
```
        
3. 修改OrderHandeler
        
```java
       public interface OrderHandler {
           /**
            * 订单处理
            */
           void handle(String source, String paymentMethod);
       }
             
       @OrderHandlerType(source = "mobile", paymentMethod = "wx")
       @Service
       public class MobileOrderHandlerImpl implements OrderHandler {
             
             
           /**
            * 订单处理:移动端微信支付
            */
           @Override
           public void handle(String source, String paymentMethod) {
               // do something ...
               System.out.println("source : " + source + ", paymentMethod : " + paymentMethod);
           }
       }
             
       @OrderHandlerType(source = "mobile", paymentMethod = "ali")
       @Service
       public class MobileOrderHandlerImpl implements OrderHandler {
             
           /**
            * 订单处理:移动端支付宝支付
            */
           @Override
           public void handle(String source, String paymentMethod) {
               // do something ...
               System.out.println("source : " + source + ", paymentMethod : " + paymentMethod);
           }
       }
             
       @OrderHandlerType(source = "pc", paymentMethod = "ali")
       @Service
       public class PcOrderHandlerImpl implements OrderHandler {
           /**
            * 订单处理:pc端支付宝支付
            */
           @Override
           public void handle(String source, String paymentMethod) {
               // do something ...
               System.out.println("source : " + source + ", paymentMethod : " + paymentMethod);
           }
       }
             
       @OrderHandlerType(source = "pc", paymentMethod = "wx")
       @Service
       public class PcOrderHandlerImpl implements OrderHandler {
           /**
            * 订单处理:pc端微信支付
            */
           @Override
           public void handle(String source, String paymentMethod) {
               // do something ...
               System.out.println("source : " + source + ", paymentMethod : " + paymentMethod);
           }
       }
       
```

4. 订单处理接口
        
```java
public class OrderServiceImpl {
    // 将OrderHandlerType作为key,handler作为value存起来
    private Map<OrderHandlerType, OrderHandler> map = new HashMap<>();
    
    /**
     * 注入策略实现方法
     */
    @Autowired
    public void setMap(List<OrderHandler> orderHandlerList) {
        map = orderHandlerList.stream().collect(Collectors.toMap(orderHandler ->
                        AnnotationUtils.findAnnotation(orderHandler.getClass(), OrderHandlerType.class),
                v -> v, (v1, v2) -> v1));
    }
    
    public void order(String source,String paymentMethod) {
        // do something ...
        // 获取订单处理策略
        OrderHandler orderHandler = this.map.get(new OrderHandlerTypeImpl(source, paymentMethod));
        if (orderHandler == null) {
            // do something ...
        }
        // 执行订单处理策略
        orderHandler.handle(source);
        // do something ...
    }
}
```