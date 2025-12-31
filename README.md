# 停车场费率计算核心模块
Parking Lot Rate Calculation Core Module


## 一、项目简介
本项目是**停车场管理系统的核心计费组件**，专注于实现**不同用户类型、不同时段下的停车费用自动化计算**，解决人工计费易出错、规则变更成本高的问题，为停车场业务系统提供基础计费能力。


## 二、核心功能
通过3个核心类实现完整的计费逻辑：

### 1. 用户类型管理（`CarParkKind` 枚举）
- 定义4类用户：
  - `STAFF`：员工
  - `STUDENT`：学生
  - `MANAGEMENT`：管理层
  - `VISITOR`：访客（内置规则：访客免费）


### 2. 时段管理（`Period` 类）
- **时段定义**：支持创建“正常时段”“优惠时段”（如`new Period(8, 18)`表示8:00-18:00）
- **合法性校验**：
  - 起始时间需小于结束时间
  - 时间范围限制在0-24小时内
- **工具方法**：
  - `isIn(int hour)`：判断某小时是否在此时段内
  - `overlaps(Period period)`：判断此时段与另一时段是否重叠
  - `occurrences(List<Period> list)`：计算此时段与时段列表的重叠小时数


### 3. 费率计算（`Rate` 类）
- **费率校验**：
  - 正常费率需大于优惠费率
  - 费率范围限制在0-10元/小时
- **时段校验**：
  - 正常时段与优惠时段不可重叠
  - 时段列表内部不可重叠
- **费用计算**：
  - 自动拆分停车时段中的“正常时长”和“优惠时长”
  - 结合用户类型计算最终费用（访客直接返回0元）


## 三、技术栈
| 类型         | 技术/工具                |
|--------------|--------------------------|
| 开发语言     | Java                     |
| 测试框架     | JUnit 5                  |
| 构建工具     | Maven                    |
| 开发工具     | IntelliJ IDEA            |


## 四、项目结构
```
parking-lot-calculator/
├── src/
│   ├── main/
│   │   └── java/
│   │       └── org/
│   │           └── example/
│   │               ├── CarParkKind.java   # 用户类型枚举
│   │               ├── Period.java        # 时段管理类
│   │               ├── Rate.java          # 费率计算类
│   │               └── Main.java          # 功能演示主类
│   └── test/
│       └── java/
│           └── org/
│               └── example/
│                   ├── PeriodTest.java   # Period类测试用例
│                   └── RateTest.java     # Rate类测试用例
├── pom.xml                                # Maven依赖配置
└── README.md                              # 项目说明文档
```


## 五、快速使用示例
在`Main.java`中运行以下代码，可快速演示计费功能：

```java
package org.example;

import java.math.BigDecimal;
import java.util.ArrayList;

public class Main {
    public static void main(String[] args) {
        // 1. 定义时段（正常8:00-18:00，优惠18:00-22:00）
        ArrayList<Period> normalPeriods = new ArrayList<>();
        normalPeriods.add(new Period(8, 18));
        ArrayList<Period> reducedPeriods = new ArrayList<>();
        reducedPeriods.add(new Period(18, 22));

        // 2. 定义员工费率（正常5元/小时，优惠3元/小时）
        Rate staffRate = new Rate(
            CarParkKind.STAFF,
            reducedPeriods,
            normalPeriods,
            new BigDecimal("5.0"),
            new BigDecimal("3.0")
        );

        // 3. 模拟停车时段（10:00-20:00）
        Period parkingPeriod = new Period(10, 20);

        // 4. 计算并输出费用
        BigDecimal cost = staffRate.calculate(parkingPeriod);
        System.out.println("员工停车费用：" + cost + "元"); // 输出：46.0元
    }
}
```


## 六、测试说明
已编写**37个JUnit测试用例**，覆盖所有核心场景：
- `PeriodTest`：验证时段合法性、时段重叠、小时归属等逻辑
- `RateTest`：验证费率合法性、不同用户/时段的费用计算等逻辑


### 运行测试
在IntelliJ IDEA中操作：
1. 定位到`src/test/java/org/example`包
2. 右键包 → 选择`Run 'Tests in org.example'`
3. 所有测试将自动执行（执行时间约53毫秒）


## 七、敏捷开发实践启示
本项目是敏捷开发思想的微型实践案例，核心启示包括：
1. **迭代式开发**：先交付“用户-时段-费率”核心功能（MVP），再扩展上层业务
2. **测试驱动（TDD）**：先编写测试用例定义功能预期，再实现业务代码，保障质量
3. **快速反馈**：一键运行所有测试，53毫秒完成37个用例验证，及时发现问题
4. **灵活扩展**：组件化设计支持快速新增用户类型、时段规则，响应需求变化
5. **简单设计**：类职责单一、依赖清晰，降低维护成本
