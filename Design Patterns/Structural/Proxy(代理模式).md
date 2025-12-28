## 定义

为其他对象提供一种替代品，以便控制对这个对象的访问。

简单来说，代理模式通过“代理”对象来控制对真实对象的访问，而不直接访问真实对象。

```java
proxy.doSomething();
```

## 为什么我迟早会用到代理模式

在某些情况下，直接访问真实对象不方便或不可能，这时可以使用代理模式来控制访问。

假设有一个 RealSubject 类，它的某些功能需要被控制：

```java
class RealSubject {
    void request() {
        // 实际操作
    }
}
```

但我们不希望每次都直接访问 RealSubject，例如：

- 控制访问权限：只有在某些条件下才能访问 RealSubject。
- 延迟加载：直到需要时才加载 RealSubject。
- 添加附加功能：例如，日志、缓存、权限检查等。

这时，我们可以通过代理来间接访问 RealSubject。

## 不用代理模式会怎样

如果没有代理模式，可能会面临以下问题：

强耦合：业务逻辑和实现逻辑紧密绑定，不便于管理和扩展。

重复代码：如果每次访问 RealSubject 都需要执行一些附加功能（如权限检查、缓存等），代码就会重复且难以维护。

无法控制访问：如果不使用代理，我们可能无法在访问 RealSubject 时加入额外的控制逻辑（如延迟加载或访问限制）。

## 反例（没有代理模式）

假设我们需要在多个地方访问 RealSubject，并在每次访问时添加额外逻辑：

```java
public class Client {
    public void process() {
        RealSubject realSubject = new RealSubject();

        // 访问时做权限检查
        if (hasPermission()) {
            realSubject.request();
        }

        // 访问时做日志记录
        logRequest();
    }
}
```

问题：

- 强耦合：业务代码与权限检查、日志记录直接耦合，难以维护。
- 重复代码：每次访问 RealSubject 都需要写相似的检查和日志代码。

## 正例：代理模式的标准实现

创建代理类：通过代理控制对真实对象的访问

```java
public class Proxy implements Subject {
    private RealSubject realSubject;

    @Override
    public void request() {
        if (realSubject == null) {
            realSubject = new RealSubject();
        }
        
        // 代理可以做附加功能
        logRequest();
        realSubject.request();
    }

    private void logRequest() {
        // 记录日志
    }
}
```

客户端通过代理访问真实对象

```java
public class Client {
    public void process() {
        Subject proxy = new Proxy();
        proxy.request();
    }
}
```

> 代理的职责：通过代理控制对 RealSubject 的访问，可以附加一些额外的逻辑，如权限检查、延迟加载、日志记录等。

## 工程实践中的代理设计原则

1️⃣ 代理只负责控制访问

代理的职责是控制对真实对象的访问，不应承担额外的业务逻辑。避免把代理做得太复杂，否则就违背了设计模式的初衷。

2️⃣ 代理和真实对象应该遵循相同的接口

在代理模式中，代理类和真实对象通常实现相同的接口。这样，客户端就可以无缝地通过代理来操作真实对象，无需知道其背后的实现。

3️⃣ 使用代理来增强功能

代理模式不仅仅是为了控制访问权限，还可以用来为真实对象增加一些额外功能，如日志记录、缓存、延迟加载等。

## 常见误区与反模式

### ❌ 把代理当作“万能控制器”

```java
public class Proxy {
    public void doSomething() {
        // 附加功能和业务逻辑混在一起
        checkPermission();
        logRequest();
        realSubject.request();
    }
}
```

### ❌ 把代理做成“无所不能”的代理类

```java
public class UniversalProxy {
    public void handleRequest() {
        // 多种功能（权限检查、日志、延迟加载等）混在一起
    }
}
```

### ❌ 代理不应该与具体实现耦合

```java
public class Proxy implements Subject {
    private RealSubject realSubject = new RealSubject();

    @Override
    public void request() {
        // 无法灵活切换代理目标
        realSubject.request();
    }
}
```

## 进阶：真实项目中的高级用法

### ✅ 代理模式与缓存

在许多系统中，代理模式常用于缓存，通过代理对象来缓存请求结果，避免每次都去访问真实对象。

### ✅ 代理模式与延迟加载

代理模式也可以用于延迟加载，即在需要时才创建真实对象，而不是一开始就加载它。