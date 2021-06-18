# Markdown不常用的语法

- [Markdown不常用的语法](#markdown不常用的语法)
  - [任务清单](#任务清单)
  - [HTML元素](#html元素)
  - [公式](#公式)
  - [Mermaid图](#mermaid图)
    - [横向流程图](#横向流程图)
    - [竖向流程图](#竖向流程图)
    - [标准流程图](#标准流程图)
    - [横向标准流程图](#横向标准流程图)
    - [UML时序图](#uml时序图)
    - [UML时序图源码复杂样例](#uml时序图源码复杂样例)
    - [UML标准时序图样例](#uml标准时序图样例)
    - [甘特图样例](#甘特图样例)
    - [类图](#类图)
    - [状态迁移图](#状态迁移图)
    - [饼图](#饼图)
    - [Git log图（虽然官网有示例，但是有问题，也没有对应文档）](#git-log图虽然官网有示例但是有问题也没有对应文档)
    - [整体关系图](#整体关系图)
    - [行程图](#行程图)

## 任务清单
```
- [x] 支持 @提到某人、#引用、[链接]()、**格式化** 和 <del>标签</del> 等语法
- [x] 需要使用列表语法来激活（无序或有序列表均可）
- [x] 这是一个已完成项目
- [ ] 这是一个未完成项目
```
效果
- [x] 支持 @提到某人、#引用、[链接]()、**格式化** 和 <del>标签</del> 等语法
- [x] 需要使用列表语法来激活（无序或有序列表均可）
- [x] 这是一个已完成项目
- [ ] 这是一个未完成项目

## HTML元素
`使用 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Del</kbd> 重启电脑`
效果
使用 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Del</kbd> 重启电脑

## 公式
当你需要在编辑器中插入数学公式时，可以使用两个美元符 $$ 包裹 TeX 或 LaTeX 格式的数学公式来实现。提交后，问答和文章页会根据需要加载 Mathjax 对数学公式进行渲染。
```latex
$$ 
xy \text{ with arrows:} \quad \overleftarrow{xy} \; \mid \; \overleftrightarrow{xy} \; \mid \; \overrightarrow{xy} 
$$

$$ \int_0^1 {x^2} \,{\rm d}x $$
```
效果
$$ 
xy \text{ with arrows:} \quad \overleftarrow{xy} \; \mid \; \overleftrightarrow{xy} \; \mid \; \overrightarrow{xy} 
$$

$$ \int_0^1 {x^2} \,{\rm d}x $$

## Mermaid图
官网示例：[https://mermaid-js.github.io/mermaid/#/](https://mermaid-js.github.io/mermaid/#/)
### 横向流程图
mermaid语法: [https://cloud.tencent.com/developer/article/1334691](https://cloud.tencent.com/developer/article/1334691)
```mermaid
graph LR
    A[方形] -->B(圆角)
    B --> C{条件a}
    C -->|a=1| D[结果1]
    C -->|a=2| E[结果2]
    F[横向流程图]
```

### 竖向流程图
```mermaid
graph TD
    A[方形] --> B(圆角)
    B --> C{条件a}
    C --> |a=1| D[结果1]
    C --> |a=2| E[结果2]
    F[竖向流程图]
```

### 标准流程图
flow语法：[https://www.jianshu.com/p/f28c94cf1204](https://www.jianshu.com/p/f28c94cf1204)
```flow
st=>start: 开始框
op=>operation: 处理框
cond=>condition: 判断框(是或否?)
sub1=>subroutine: 子流程
io=>inputoutput: 输入输出框
e=>end: 结束框

st->op->cond
cond(yes)->io->e
cond(no)->sub1(right)->op
```

### 横向标准流程图
```flow
st=>start: 开始框
op=>operation: 处理框
cond=>condition: 判断框(是或否?)
sub1=>subroutine: 子流程
io=>inputoutput: 输入输出框
e=>end: 结束框

st(right)->op->cond
cond(yes)->io(bottom)->e
cond(no)->sub1(top)->op
```

### UML时序图
```sequence
对象A->对象B: 对象B你好吗?（请求）
Note right of 对象B: 对象B的描述
Note left of 对象A: 对象A的描述(提示)
对象B-->对象A: 我很好(响应)
对象A->对象B: 你真的好吗？
```

### UML时序图源码复杂样例
```sequence
Title: 标题：复杂使用
对象A->对象B: 对象B你好吗?（请求）
Note right of 对象B: 对象B的描述
Note left of 对象A: 对象A的描述(提示)
对象B-->对象A: 我很好(响应)
对象B->小三: 你好吗
小三-->>对象A: 对象B找我了
对象A->对象B: 你真的好吗？
Note over 小三,对象B: 我们是朋友
participant C
Note right of C: 没人陪我玩
```

### UML标准时序图样例
```mermaid
%% 时序图例子,-> 直线，-->虚线，->>实线箭头
  sequenceDiagram
    participant 张三
    participant 李四
    张三->王五: 王五你好吗？
    loop 健康检查
        王五->王五: 与疾病战斗
    end
    Note right of 王五: 合理 食物 <br/>看医生...
    李四-->>张三: 很好!
    王五->李四: 你怎么样?
    李四-->王五: 很好!
```

### 甘特图样例
```mermaid
gantt
    %% this is a comment
    dateFormat  YYYY-MM-DD
    title 软件开发甘特图

    section 设计
    需求                      :done,    des1, 2014-01-06,2014-01-08
    原型                      :active,  des2, 2014-01-09, 3d
    UI设计                    :         des3, after des2, 5d
    未来任务                   :         des4, after des3, 5d
    
    section 开发
    学习准备理解需求            :crit, done, 2014-01-06,24h
    设计框架                   :crit, done, after des2, 2d
    开发                      :crit, active, 3d
    未来任务                   :crit, 5d
    耍                        :2d

    section 测试
    功能测试                   :active, a1, after des3, 3d
    压力测试                   :after a1  , 20h
    测试报告                   : 48h
```

### 类图
```mermaid
classDiagram
    Animal <|-- Duck
    Animal <|-- Fish
    Animal <|-- Zebra
    Animal : +int age
    Animal : +String gender
    Animal : +isMammal()
    Animal : +mate()
    Duck : +String beakColor
    Duck : +swim()
    Duck : +quack()
    Fish : -int sizeInFeet
    Fish : -canEat()
    Zebra : +bool is_wild
    Zebra : +run()
```

### 状态迁移图
```mermaid
stateDiagram-v2
    [*] --> Active

    state Active {
        [*] --> NumLockOff
        NumLockOff --> NumLockOn : EvNumLockPressed
        NumLockOn --> NumLockOff : EvNumLockPressed
        --
        [*] --> CapsLockOff
        CapsLockOff --> CapsLockOn : EvCapsLockPressed
        CapsLockOn --> CapsLockOff : EvCapsLockPressed
        --
        [*] --> ScrollLockOff
        ScrollLockOff --> ScrollLockOn : EvCapsLockPressed
        ScrollLockOn --> ScrollLockOff : EvCapsLockPressed
    }
```

### 饼图
```mermaid
pie
    title 数学成绩饼图
    "90分以上" : 15
    "80-90分" : 21
    "70-80分" : 12
    "60-70分" : 8
    "60分以下" : 3
```

### Git log图（虽然官网有示例，但是有问题，也没有对应文档）
```mermaid
gitGraph:
options
{
    "nodeSpacing": 150,
    "nodeRadius": 10
}
end
commit
branch newbranch
checkout newbranch
commit
commit
checkout master
commit
commit
merge newbranch
```

### 整体关系图
```mermaid
erDiagram
    CUSTOMER ||--o{ ORDER : places
    ORDER ||--|{ LINE-ITEM : contains
    CUSTOMER }|..|{ DELIVERY-ADDRESS : uses
```

### 行程图
```mermaid
journey
    title My working day
    section Go to work
      Make tea: 5: Me
      Go upstairs: 3: Me
      Do work: 1: Me, Cat
    section Go home
      Go downstairs: 5: Me
      Sit down: 5: Me
```