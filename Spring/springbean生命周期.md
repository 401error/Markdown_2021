#### 1.容器启动

* xml文件->生成beanDefinition对象，这个对象包含实例这个bean的一些信息，比如单例还是多例，是构造器注入还是set。



#### 2.容器启动后

* 生成beanfactoryPostProcessor，这个对象可以改变BeanDefinition里的信息。

* **创建实例**，利用beanDefinition创建实例，此时实例属性无值。

* **属性注入**然后注入属性，如果此bean实现了一些Aware的接口，还会调用相应的方法

  * Aware 接口是用来让bean来获得，加载自己的beanfactory，或者classload等等

  * ```java
    public interface BeanFactoryAware extends Aware {
    	// 会调用提供拥有bean实例的工厂IOC容器，调用的时机是:
    	// 在调用方法populateBean填充bean属性之后，
    	// 调用方法InitializingBean#afterPropertiesSet，init-method前
    	// 传入的参数beanFactory肯定是直接可用的，绝对不会为null
    	void setBeanFactory(BeanFactory beanFactory) throws BeansException;
    
    }
    ```

  * 

* **初始化init**实例化beanPostProcessor对象，这个对象可以帮助我们再实例化了bean之后，扩展这个对象，会对这个对象做一些处理，==例如生成此对象的代理对象==

  

![img](C:\Users\ZHW\Desktop\笔记\IMG\181453414212066.png)

<img src="C:\Users\ZHW\Desktop\笔记\IMG\181454040628981.png" alt="img"  />



#### spring循环依赖

* Spring首先从一级缓存singletonObjects中获取。如果获取不到，并且对象正在创建中，就再从二级缓存earlySingletonObjects中获取。如果还是获取不到且允许singletonFactories通过getObject()获取，就从三级缓存singletonFactory.getObject()(三级缓存)获取，如果获取到了则，把此示例放到二级缓存。
* 当一个对象执行了构造器进行实例化后，就会提前曝光，然后放到三级缓存singletonFactor里。