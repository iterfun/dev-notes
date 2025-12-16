## 1. 定义

为子系统中的一组接口提供一个统一的高层接口 -> 让多个子系统可以更容易地 work together。

我不需要知道系统内部怎么运作，只需要：

```java
facade.doSomething();
```

## 2. 为什么你迟早会用到门面模式

在业务系统早期，代码往往是这样的：

```
controller -> serviceA
controller -> serviceB
controller -> serviceC
```

随着业务增长，很快会变成：

```
controller -> serviceA
           -> serviceB
           -> serviceC
           -> serviceD
           -> serviceE
```

并且开始伴随这些需要：调用顺序不能乱，某些 service 失败要回滚，新需求要改多个 Controller
-> 架构缺一层的问题，这层通常就是 Facad(门面)

门面模式的核心不是“封装”，而是简化使用成本。子系统依然存在，复杂度没有消失，只是被集中管理。

## 3. 不用门面模式会怎样

如果没有 Facade，通常会出现这些问题：

1. Controller 知道所有内部细节
2. 顺序耦合严重（顺序一错就出 bug）
3. 任何流程改动都要改 Controller
4. 同一套流程无法复用

## 4. 反例（没有 Facade）

```java
@PostMapping("/order")
public void placeOrder() {
    inventoryService.checkStock();
    paymentService.pay();
    orderService.create();
    notificationService.send();
}
```

问题：

- Controller 直接编排流程
- 对多个 Service 强依赖
- 流程无法集中维护

## 5. 正例：门面模式的标准实现

### Facade：负责组织流程

```java
@Component
public class OrderFacade {

    @Autowired
    private InventoryService inventoryService;

    @Autowired
    private PaymentService paymentService;

    @Autowired
    private OrderService orderService;

    @Autowired
    private NotificationService notificationService;

    public void placeOrder() {
        inventoryService.checkStock();
        paymentService.pay();
        orderService.create();
        notificationService.send();
    }
}
```

> **Facade 的职责**：组织流程 / 编排调用顺序

### Controller：只负责接收请求

```java
@PostMapping("/order")
public void placeOrder() {
    orderFacade.placeOrder();
}
```

> **Controller 的职责**：参数校验 + 请求转发

## 6. 工程实践中的 Facade 设计原则

### 1️⃣ Facade 按“用例”划分，而不是按“领域”

❌ 错误：

```
UserFacade
OrderFacade
PaymentFacade
```

✅ 正确：

```
PlaceOrderFacade
RefundOrderFacade
CancelOrderFacade
```

> Facade = Use Case（用例）

### 2️⃣ Facade 不要写复杂业务规则

❌ 错误：

```java
if (user.isVip() && order.amount > 1000) {
    ...
}
```

✅ 正确：

```java
vipDiscountService.apply(order);
```

-> Facade 是导演，不是演员

### 3️⃣ 一个 Controller 尽量只依赖一个 Facade

如果你看到：

```java
@Autowired OrderFacade
@Autowired PaymentFacade
```

-> 就要警惕 ⚠️

## 7. Spring 项目中的典型分层

```
Controller
   ↓
Facade（Application Layer）
   ↓
Domain Service
   ↓
Repository
```

> Facade 非常适合作为 **应用层 / 用例层**

## 8. 常见误区与反模式

### ❌ Facade 写成“万能类”

```java
public class SystemFacade {
    // 5000 行代码
}
```

解决方案：

- 拆分 Facade
- 按业务用例拆

### ❌ Facade 直接操作数据库

```java
jdbcTemplate.update(...)
```

> **Facade 不应该感知基础设施细节**

## 9. 进阶：真实项目中的高级用法

### ✅ Facade + 事务边界

```java
@Transactional
public void placeOrder() {
    ...
}
```

> **Facade 是最合理的事务边界**

### ✅ Facade + 编排（Orchestration）

在微服务中，Facade 常用于：

- 调用多个 RPC
- 处理失败策略
- 返回统一结果

### ✅ Facade 是重构利器

重构第一步：

> **先抽 Facade，不改内部实现**

## 10. 总结：什么时候该用，什么时候别用

**该用的时候**：

- 一个用例需要多个 Service 协作
- 调用顺序复杂
- Controller 开始变胖

**别用的时候**：

- 只有单一 Service 调用