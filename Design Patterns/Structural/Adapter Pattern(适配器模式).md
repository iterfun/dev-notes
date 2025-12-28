## 定义

将一个类的接口转换成客户期望的另一个接口 -> 使得原本由于接口不兼容而不能一起工作的类可以一起工作。

我不需要修改现有代码，只需要创建一个适配器类，调用：

```java
adapter.doSomething();
```

## 为什么我迟早会用到适配器模式

假设有两种现有的系统接口，我们需要让它们兼容工作：

```java
class OldSystem {
    void oldMethod() {
        // 旧方法
    }
}

class NewSystem {
    void newMethod() {
        // 新方法
    }
}
```

场景：我们想在同一流程里调用 OldSystem 和 NewSystem，而它们的接口不兼容。传统的做法是修改两个系统的代码，但如果这两个系统是第三方提供的，就不能这么做。这时就可以用适配器模式，将旧系统接口转化为新的接口，便于统一调用。

## 不用适配器模式会怎样

如果没有适配器模式，可能会遇到以下问题：

- 必须修改现有代码，增加不必要的耦合
- 不同的系统接口不能兼容使用
- 增加新功能时，代码变得臃肿且不易维护
- 难以与第三方系统进行集成

## 反例（没有适配器模式）

假设我们有一个需要同时处理两种接口的业务代码：

```java
public void process() {
    OldSystem oldSystem = new OldSystem();
    NewSystem newSystem = new NewSystem();

    // 直接调用旧接口
    oldSystem.oldMethod();
    // 直接调用新接口
    newSystem.newMethod();
}
```

问题：

- OldSystem 和 NewSystem 接口不兼容
- 代码重复且难以维护
- 随着系统增多，代码愈加混乱

## 正例：适配器模式的标准实现

### 创建适配器类：将旧系统的接口适配为新系统的接口

```java
public class OldSystemAdapter {
    private OldSystem oldSystem;

    public OldSystemAdapter(OldSystem oldSystem) {
        this.oldSystem = oldSystem;
    }

    public void newMethod() {
        oldSystem.oldMethod();
    }
}
```

### 客户端通过适配器访问旧系统接口

```java
public void process() {
    OldSystem oldSystem = new OldSystem();
    OldSystemAdapter adapter = new OldSystemAdapter(oldSystem);

    // 通过适配器调用新方法
    adapter.newMethod();
}
```

> 适配器的职责：将旧接口转换为新接口，保证接口兼容。

## 工程实践中的适配器设计原则

1️⃣ 适配器是接口适配工具，目的是让旧接口能用，而不是改善它

2️⃣ 避免修改现有类（违背开闭原则）

3️⃣ 每个适配器只处理一个接口的适配（单一职责）

## 常见误区与反模式

### ❌ 把适配器当作“万能转换器”

```java
public class UniversalAdapter {
    public void convertOldToNew(OldSystem oldSystem) {
        oldSystem.oldMethod();
        // 其他各种转换
    }
}
```

这样做会导致以下问题：

- 职责不单一 ->变得难以理解和维护
- 违反开闭原则（新需求的出现应该通过扩展适配器而非修改现有适配器来实现）

如果我有一个 OldSystem 和 NewSystem，分别为两种接口提供适配，那么就应该为它们分别写适配器，而不是把所有接口的适配都放在一个 UniversalAdapter 中。

### ❌ 把适配器做成“内联修改”

```java
public void process() {
    OldSystem oldSystem = new OldSystem();
    oldSystem.oldMethod();  // 直接修改接口
}
```

> 不要直接修改接口，而是通过适配器来解耦