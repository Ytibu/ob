## Bean(IOC/DI)

### 实例化
- 构造方法
- 静态工厂
- 实例工厂
	- FactoryBean

此时对象**内部属性均为默认值**（如引用为`null`，int为`0`），还未注入依赖。

### 属性注入
- setter注入
- 构造器注入
- 自动装配
- 集合注入

将依赖的**其他Bean或基本值**注入进来。此刻对象才真正拥有了我们配置的数据。

###  初始化
- XML指定的`init-method`
- 实现`InitializingBean`接口的`afterPropertiesSet()`
- `@PostConstruct`方法  

通常用于**检查必要属性是否注入**，或**开启连接、加载缓存**等。

### 销毁
- XML指定的`destroy-method`
- 实现`DisposableBean`接口的`destroy()`
- 包括`@PreDestroy`

用于**释放数据库连接、关闭文件流**等资源清理。

### 其他
- 第三方Bean管理
- 加载properties文件 

### 容器
- 创建容器：类路径加载配置文件、文件系统路径加载配置文件、加载多个配置文件
- 获取Bean：使用bean名称获取、使用bean名称获取·并指定类型、使用bean类型获取
- 容器类层次结构：
- BeanFactory：

### 使用注解来定义bean

### 纯注解开发

## Aspects(AOP)


## spring事务
