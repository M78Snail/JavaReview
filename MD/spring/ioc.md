## SpringIOC原理？自己实现IOC要怎么做，哪些步骤？

IOC 也就是“控制反转”了，不过更流行的叫法是“依赖注入”（DI - Dependency Injection）。听起来挺高深，其实实现起来并不复杂。下面就看看如何来实现这个轻量级 IOC 框架。

从实例出发，先看看以下 Action 代码。

```
@Bean
public class ProductAction extends BaseAction {
    @Inject
    private ProductService productService;

    @Request("GET:/product/{id}")
    public Result getProductById(long productId) {
        if (productId == 0) {
            return new Result(ERROR_PARAM);
        }
        Product product = productService.getProduct(productId);
        if (product != null) {
            return new Result(OK, product);
        } else {
            return new Result(ERROR_DATA);
        }
    }
}
```

以上使用了两个自定义注解：@Bean 与 @Inject。

在 ProductAction 类上标注了 @Bean 注解，表示该类会交给“容器”处理，以便加入依赖注入框架。

在 produceService 字段上标注了 @Inject 注解，表示该字段将会被注入进来，而无需 new ProductServiceImpl()，实际上 new 这件事情不是我们做的，而是框架做的，也就是说控制权正好反过来了，所以“依赖注入（DI）”也称作“控制反转（IoC）”。

那么，应该如何实现依赖注入框架呢？首先还是看看下面的 BeanHelper 类吧。

```
public class BeanHelper {
    private static final Map<Class<?>, Object> beanMap = new HashMap<Class<?>, Object>();

    static {
        try {
            // 获取并遍历所有的 Bean（带有 @Bean 注解的类）
            List<Class<?>> beanClassList = ClassHelper.getClassListByAnnotation(Bean.class);
            for (Class<?> beanClass : beanClassList) {
                // 创建 Bean 实例
                Object beanInstance = beanClass.newInstance();
                // 将 Bean 实例放入 Bean Map 中（键为 Bean 类，值为 Bean 实例）
                beanMap.put(beanClass, beanInstance);
            }

            // 遍历 Bean Map
            for (Map.Entry<Class<?>, Object> beanEntry : beanMap.entrySet()) {
                // 获取 Bean 类与 Bean 实例
                Class<?> beanClass = beanEntry.getKey();
                Object beanInstance = beanEntry.getValue();
                // 获取 Bean 类中所有的字段（不包括父类中的方法）
                Field[] beanFields = beanClass.getDeclaredFields();
                if (ArrayUtil.isNotEmpty(beanFields)) {
                    // 遍历所有的 Bean 字段
                    for (Field beanField : beanFields) {
                        // 判断当前 Bean 字段是否带有 @Inject 注解
                        if (beanField.isAnnotationPresent(Inject.class)) {
                            // 获取 Bean 字段对应的接口
                            Class<?> interfaceClass = beanField.getType();
                            // 获取该接口所有的实现类
                            List<Class<?>> implementClassList = ClassHelper.getClassListByInterface(interfaceClass);
                            if (CollectionUtil.isNotEmpty(implementClassList)) {
                                // 获取第一个实现类
                                Class<?> implementClass = implementClassList.get(0);
                                // 从 Bean Map 中获取该实现类对应的实现类实例
                                Object implementInstance = beanMap.get(implementClass);
                                // 设置该 Bean 字段的值
                                beanField.setAccessible(true); // 必须使该字段可访问
                                beanField.set(beanInstance, implementInstance);
                            }
                        }
                    }
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static Map<Class<?>, Object> getBeanMap() {
        return beanMap;
    }

    @SuppressWarnings("unchecked")
    public static <T> T getBean(Class<T> cls) {
        return (T) beanMap.get(cls);
    }
}
```

其实很简单，依赖注入其实分为两个步骤：

1. 通过反射创建实例；
2. 获取需要注入的接口实现类并将其赋值给该接口。以上代码中的两个 for 循环就是干这两件事情的。 

依赖注入框架实现完毕！

如果一个接口存在两个实现类，应该获取哪一个实现类呢？我之前的做法是，只获取第一个实现类。而 Spring 的做法是，直接报错，应用都起不来。