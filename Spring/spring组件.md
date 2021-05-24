

# spring注册组件



#### @Configuaratin（一般是跟bean一起使用）

* 标在类上，表示一个这个类是配置类，这个配置类所起的作用等同于spring的xml
* springboot2.x版本里，configuaration有full配置和lite 配置



#### @Bean

* @Bean 用在方法上，告诉Spring容器，你可以从下面这个方法中拿到一个Bean，这个Bean就是方法的返回值
* 拿到的bean的id默认为方法名。



#### @Import

* 把类名的方式导入容器。



#### @Conditional