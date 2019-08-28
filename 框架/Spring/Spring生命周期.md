Spring中bean的生命周期：

实例化

填充属性

调用各种aware

- 调用BeanNameAware的setBeanName()

- 调用BeanFactoryAware的setBeanFactory()
- 调用ApplicationContextAware的setApplicationContext()

调用BeanPostProcessor的预初始化方法postProcessBeforeInitialization()

调用InitializingBean的afterPropertiesSet()方法

调用自定义的初始化方法

调用BeanPostProcessor的预初始化后方法postProcessAfterInitialization()



使用bean



容器关闭

DisposableBean的destroy方法

调用自定义销毁方法

