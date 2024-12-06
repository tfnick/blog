---
share: "true"
toc: "true"
date: 2024-12-06
title: 关于循环依赖的答疑
description: 描述
author: 飞天
tags:
  - 循环依赖
  - 架构设计
---

## 问题：能否举个循环依赖的例子

假设，某个单体应用模块如下：

```
project
├── bpm-module
│   ├── bpm-module-api      # 定义 BPM 的通用接口
│   └── bpm-module-impl     # BPM 的具体实现，依赖 bpm-module-api 和 biz-module-api
├── biz-module
│   ├── biz-module-api      # 定义业务模块的通用接口
│   └── biz-module-impl     # 业务模块的具体实现，依赖 bpm-module-api 和 biz-module-api

```

### 依赖关系说明

1. **bpm-module-api**：
    
    - 定义流程相关的接口（如 `ProcessService` 和回调接口 `BizCallbackInterface`）。
    - 该模块不依赖任何其他模块。
2. **bpm-module-impl**：
    
    - 实现 `bpm-module-api` 中定义的流程管理功能。
    - 依赖 **biz-module-api** 来调用业务模块提供的回调接口。
3. **biz-module-api**：
    
    - 定义业务模块相关的接口（如 `BusinessService`）。
    - 该模块不依赖任何其他模块。
4. **biz-module-impl**：
    
    - 实现 `biz-module-api` 中定义的业务逻辑功能。
    - 依赖 **bpm-module-api** 来调用流程相关的功能。

通过这种设计，两个模块的实现层互相依赖，但依赖的仅仅是对方的 `API` 层，而不是具体实现。

### 示例代码

#### bpm-module-api: 定义流程管理功能

```java
public interface ProcessService {
    void createProcessInstance();
}

public interface BizCallbackInterface {
    void onProcessCompleted(String processId);
}

```

bpm-module-impl: 实现流程管理功能
```java
@Service
public class ProcessServiceImpl implements ProcessService {
    private final BizCallbackInterface bizCallback;

    public ProcessServiceImpl(BizCallbackInterface bizCallback) {
        this.bizCallback = bizCallback;
    }

    @Override
    public void createProcessInstance() {
        // 创建流程实例逻辑
        String processId = "123";
        // 回调业务模块
        bizCallback.onProcessCompleted(processId);
    }
}

```

biz-module-api: 定义业务模块功能

```java
public interface BusinessService {
    void startBusinessProcess();
}

```

biz-module-impl: 实现业务逻辑

```java
@Service
public class BusinessServiceImpl implements BusinessService {
    private final ProcessService processService;

    public BusinessServiceImpl(ProcessService processService) {
        this.processService = processService;
    }

    @Override
    public void startBusinessProcess() {
        // 调用 BPM 模块的功能
        processService.createProcessInstance();
    }
}

```

### 依赖配置示意

1. **bpm-module-impl** 的 `pom.xml`：

```xml
<dependencies>
    <dependency>
        <groupId>com.example.project</groupId>
        <artifactId>bpm-module-api</artifactId>
    </dependency>
    <dependency>
        <groupId>com.example.project</groupId>
        <artifactId>biz-module-api</artifactId>
    </dependency>
</dependencies>

```

2. **biz-module-impl** 的 `pom.xml`：

```xml
<dependencies>
    <dependency>
        <groupId>com.example.project</groupId>
        <artifactId>biz-module-api</artifactId>
    </dependency>
    <dependency>
        <groupId>com.example.project</groupId>
        <artifactId>bpm-module-api</artifactId>
    </dependency>
</dependencies>

```

### 优势

1. **模块解耦**：实现层只依赖 API 层，避免了直接的循环依赖。
2. **职责分离**：API 层只定义接口，逻辑集中在实现层。
3. **可扩展性强**：未来可以独立替换实现模块，而不会影响其他模块。
4. **方便测试**：API 层可以使用 mock 测试，避免测试时加载整个模块。

通过这种方式，你的双向依赖问题就转化为通过 `API` 层的单向依赖，设计更加清晰、合理。

## 问题：但是实际上，bpm-module-impl 依赖biz-module-api ，biz-module-impl依赖bpm-module-api，这不还是双向依赖吗？

从模块间的依赖路径来看，你的描述确实构成了**逻辑上的双向依赖**，但这里的双向依赖并不是直接的实现层依赖，而是通过 **API 层** 间接连接的。这样的设计实际上是可以接受的，因为：

1. **解耦了实现细节**：
    
    - `bpm-module-impl` 和 `biz-module-impl` 并没有直接互相依赖，它们只依赖对方的 **API 层**，这使得每个模块的实现细节对对方透明。
    - API 层仅定义契约，不包含实现逻辑。
2. **解决了循环依赖的技术问题**：
    
    - 编译时，`bpm-module-impl` 和 `biz-module-impl` 的依赖路径是清晰且无环的。
    - 部署时，两个模块通过 Spring 的依赖注入（或其他 DI 框架）协作，避免了实例化循环。
3. **模块职责分离**：
    
    - API 层职责是定义接口和契约，**实现层才是真正依赖链的终端**。只要 API 不发生频繁变更，模块间的耦合是稳定的。

### 深入分析与改进策略

虽然这种设计在一定程度上是合理的，但仍然可能引入一定的复杂性，尤其是在以下情况下：

- **模块边界不清晰**：模块职责定义模糊，可能导致过多的跨模块调用。
- **扩展性受限**：将来如果希望拆分为微服务架构，双向依赖可能成为一个阻碍。

如果你希望进一步优化，可以考虑以下几种改进策略：

#### 1. **通过事件解耦**

使用事件驱动模型，消除模块间的直接调用关系。

- **场景**：
    
    - `bpm-module-impl` 创建流程实例后，发布事件。
    - `biz-module-impl` 监听事件并执行相应的业务逻辑。
- **优点**：
    
    - 模块间的直接依赖被削弱为松散耦合的事件关系。
    - 便于扩展和演化为分布式系统。
- **示例代码**：
    
    **bpm-module-api** 定义事件：

```java
public class ProcessCreatedEvent {
    private final String processId;

    public ProcessCreatedEvent(String processId) {
        this.processId = processId;
    }

    public String getProcessId() {
        return processId;
    }
}

```

**bpm-module-impl** 发布事件：

```java
@Service
public class ProcessServiceImpl implements ProcessService {
    private final ApplicationEventPublisher eventPublisher;

    public ProcessServiceImpl(ApplicationEventPublisher eventPublisher) {
        this.eventPublisher = eventPublisher;
    }

    @Override
    public void createProcessInstance() {
        String processId = "123";
        eventPublisher.publishEvent(new ProcessCreatedEvent(processId));
    }
}

```

**biz-module-impl** 监听事件：

```java
@Component
public class ProcessEventListener {
    @EventListener
    public void handleProcessCreated(ProcessCreatedEvent event) {
        System.out.println("Handling process created: " + event.getProcessId());
    }
}

```

#### 2. **使用中间层进行协调**

引入一个独立的 `coordinator-module`，作为 `bpm-module` 和 `biz-module` 的调度者。

- **设计思路**：
    
    - `coordinator-module` 依赖两个模块的 API。
    - 两个模块只与 `coordinator-module` 交互，避免直接调用对方的 API。
- **优点**：
    
    - 模块边界更加清晰，避免模块间的直接依赖。
    - 中间层可以集中处理跨模块的逻辑。
- **项目结构**：
```
project
├── bpm-module
│   ├── bpm-module-api
│   └── bpm-module-impl
├── biz-module
│   ├── biz-module-api
│   └── biz-module-impl
└── coordinator-module

```

#### 3. **重构模块职责**

如果模块间调用是频繁且高度耦合的，可以考虑重新划分模块边界：

- 将共同关注的功能合并到一个模块。
- 根据职责明确性将模块拆分为新的粒度。

### 总结

你当前的设计（`bpm-module-impl` 依赖 `biz-module-api`，`biz-module-impl` 依赖 `bpm-module-api`）在单体应用中是合理的，尤其是模块间通过 API 解耦时。不过，如果期望更高的灵活性和扩展性，建议探索事件驱动模型或中间协调模块的设计，以减少模块间的逻辑耦合。