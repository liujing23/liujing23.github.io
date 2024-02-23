---
title: spring AOP和代理模式
date: 2023-11-17 18:15:24
tags:
---

# 一.代理模式

## 1.定义

对一个对象提供一个代理对象，使用代理对象控制对原对象的引用



![结构类图](https://raw.githubusercontent.com/liujing23/FigureBed/main/blog/img/20240223182254.png)



RealSubject类实现Subject接口，Proxy类实现Subject接口，并定义RealSubject对象作为成员变量

客户端不直接依赖代理类



## 2.分类

- 静态代理：通过代码编译生成代理类
- 动态代理：运行时动态生成代理类



## 3.实现

1）jdk的动态代理

自定义Proxy类，实现InvocationHandle接口，重写invoke方法

```
public interface Subject {
    public void rent();

    public void hello(String str);
}
public class SubjectImpl implements Subject{
    @Override
    public void rent() {
        System.out.println("I want to rent my house");
    }

    @Override
    public void hello(String str) {
        System.out.println("hello: " + str);
    }
}
public class DynamicProxy implements InvocationHandler {
    // 这个就是要代理的真实对象
    private Object subject;

    // 构造方法，给要代理的真实对象赋初值
    public DynamicProxy(Object subject) {
        this.subject = subject;
    }

    @Override
    public Object invoke(Object object, Method method, Object[] args) throws Throwable {
        // 在代理真实对象前可以添加一些自己的操作
        System.out.println("before rent house");
        System.out.println("Method:" + method);

        // 当代理对象调用真实对象的方法时，其会自动的跳转到代理对象关联的handler对象的invoke方法来进行调用
        Object result = method.invoke(subject, args);

        // 在代理真实对象后也可以添加一些自己的操作
        System.out.println("after rent house");

        return result;
    }

}
public class Client {
    public static void main(String[] args) {
        //    要代理的真实对象
        Subject realSubject = new SubjectImpl();
        //   要代理哪个真实对象，就将该对象传进去，最后是通过该真实对象来调用其方法的
        InvocationHandler handler = new DynamicProxy(realSubject);
        /*
         * 通过Proxy的newProxyInstance方法来创建我们的代理对象，来看看其三个参数
         * 第一个参数 handler.getClass().getClassLoader() ，这里使用handler这个类的ClassLoader对象来加载我们的代理对象
         * 第二个参数realSubject.getClass().getInterfaces()，这里为代理对象提供的接口是真实对象所实行的接口，
         * 表示要代理的是该真实对象，这样就能调用这组接口中的方法了
         * 第三个参数handler， 这里将这个代理对象关联到了上方的 InvocationHandler 这个对象上
         */
        Subject subject = (Subject) Proxy.newProxyInstance(handler.getClass().getClassLoader(), realSubject.getClass().getInterfaces(), handler);
        System.out.println(subject.getClass().getName());
        subject.rent();
        subject.hello("world");

    }
```

自定义工厂类，返回代理对象



