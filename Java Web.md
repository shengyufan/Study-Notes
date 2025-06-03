[toc]
<div STYLE="page-break-after:always;"></div>

# 前后端分离开发

1.   需求分析：首先我们需要阅读需求文档，分析需求，理解需求

2.   接口定义：查询接口文档中关于需求的接口的定义，包括地址，参数，响应数据类型等等
3.   前后台并行开发：各自按照接口文档进行开发，实现需求
4.   测试：前后台开发完了，各自按照接口文档进行测试
5.   前后段联调测试：前段工程请求后端工程，测试功能

# 前端开发

前端的代码是通过浏览器转化（解析和渲染）成用户看到的网页

浏览器中对代码进行解析和渲染的部分，称为浏览器**内核**。不同的内核对于相同前端代码解析的效果可能不一致

## Web 标准

Web 标准也称为网页标准，由一系列的标准组成，大部分由W3C（ World Wide Web Consortium，万维网联盟）负责制定。由三个组成部分：

-   HTML：负责网页的**结构**（页面元素和内容）
-   CSS：负责网页的表现（页面元素的外观、位置等页面**样式**，如：颜色、大小等）
-   JavaScript：负责网页的**行为**（交互效果）

## HTML & CSS

HTML （HyperText Markup Language），超文本标记语言

-   超文本：超越了文本的限制，比普通文本更强大。除了文字信息，还可以定义图片、音频、视频等内容
-   标记语言：由标签构成的语言。HTML 标签都是预定义好的。HTML 代码直接在浏览器中运行，HTML 标签由浏览器解析

CSS（Cascading Style Sheet），层叠样式表，用于控制页面的样式（表现）

HTML中的标签特点

-   HTML标签不区分大小写

-   HTML标签的属性值，采用单引号、双引号都可以
-   HTML语法相对比较松散 (建议大家编写HTML标签的时候尽量严谨一些)

### HTML 常见标签

**img 标签**：设置图片

-   `src` - 图片资源路径，可采用本地绝对路径，本地相对路径和网络绝对路径
-   `width/height`：设置高度/宽度（百分比为占父元素的比例），如果只设置一个会进行等比例缩放

**h1 - 6 标签**：设置标题

**hr 标签**：设置水平分割线

**span 标签**：无语义的布局标签，但可以分割同一行中的不同部分（组合行内元素）

**div 标签**：无语义的布局标签，一行只能显示一个 div 标签，并且其可以设置高度和宽度

**a 标签**：设置超链接

-   `href` - 指定资源的 URL

-   `target` - 指定在何处打开资源链接

    -   `_self`： 默认值，在当前页面打开

    -   `_blank`：在空白页面打开

**video 标签**：设置视频

-   `src`： 规定视频的 URL
-   `controls`： 显示播放控件，加载控件之后才能播放
-   `width`： 播放器的宽度
-   `height`： 播放器的高度

**audio 标签**：设置音频

-   `src`： 规定音频的 URL
-   `controls`： 显示播放控件

**br 标签**：设置换行

**p 标签**：设置段落

**b/strong 标签**：设置文本加粗，strong 标签还具有强调语义

**table 标签**：设置整个表格

-   `border`：规定表格边框的宽度
-   `width`：规定表格的宽度
-   `cellspacing`：规定单元之间的空间

**tr 标签**：表格的行

**td 标签**：表格的单元格，如果是表头，可以替换为 **th 标签**

**form 标签**：设置表单，必须含有 `name` 属性

-   `action`：规定表单提交时，向何处发送表单数据，表单提交的URL。如果不指定提交到当前页面
-   `method`：规定用于发送表单数据的方式，常见为： GET、POST
    -   GET：表单数据是拼接在url后面的， 如： xxxxxxxxxxx?username=Tom&age=12，url中能携带的表单数据大小是有限制的
    -   POST： 表单数据是在请求体（消息体）中携带的，大小没有限制

**Input 标签**：定义表单项，通过 `type` 属性控制输入形式

| `type` 取值                  | 描述                                 |
| ---------------------------- | ------------------------------------ |
| text                         | 默认值，定义单行的输入字段           |
| password                     | 定义密码字段                         |
| radio                        | 定义单选按钮（同一组 `name` 相同）   |
| checkbox                     | 定义复选框                           |
| file                         | 定义文件上传按钮                     |
| date / time / datetime-local | 定义日期 / 时间 / 日期时间           |
| number                       | 定义数字输入框                       |
| email                        | 定义邮件输入框                       |
| hidden                       | 定义隐藏域                           |
| submit / reset / button      | 定义提交按钮 / 重置按钮 / 可点击按钮 |

**select 标签**：定义下拉列表

**textarea 标签**：定义文本域

**script 标签**：引入 JavaScript 代码

### CSS 样式

行内样式：在标签内使用 `style` 属性，属性值是 CSS 属性键值对

```html
<h1 style="xxx:xxx;">中国新闻网</h1>
```

内嵌样式：定义 `<style>` 标签，在标签内部定义 CSS 样式

```html
<style> h1 {...} </style>
```

外联样式：定义 `<link>` 标签，通过 `href` 属性引入外部 CSS 文件

```html
<link rel="stylesheet" href="css/news.css">
```

### CSS 选择器

元素（标签）选择器：

-   选择器的名字必须是标签的名字
-   作用：选择器中的样式会作用于所有同名的标签上

id 选择器：

-   选择器的名字前面需要加上 `#`，并且在 HTML 标签中指定 id
-   作用：选择器中的样式会作用于指定 id 的标签上（id 是**唯一**的）

类选择器：

-   选择器的名字前面需要加上 `.`，并且在 HTML 标签中指定类 class
-   作用：选择器中的样式会作用于所有 class 的属性值和该名字一样的标签上，可以是多个

优先级：id 选择器 > 类选择器 > 元素选择器

### 页面布局

盒子：页面中所有的元素（标签），都可以看做是一个盒子，由盒子将页面中的元素包含在一个矩形区域内，通过盒子的视角更方便的进行页面布局

盒子模型有多个区域组成

-   内容区域（content）
-   内边距区域（padding）
-   边框区域（border）
-   外边距区域（margin）

## JavaScript

跨平台、面向对象的脚本语言，用于控制网页行为，使网页可交互

### 引入方式

1.   内部脚本：将 JS 代码定义在 HTML 页面中
     -   一般将脚本置于 body 底部，可改善显示速度
2.   外部脚本：JS 代码定义在外部 JS 文件中，然后引入到 HTML 页面中

### 变量

JS 是一门弱类型语言，变量可以存放不同类型的值

| 关键字 | 解释                                                         |
| ------ | ------------------------------------------------------------ |
| var    | 早期 ECMAScript 5 中用于变量声明的关键字，作用域为全局变量，可重复定义 |
| let    | ECMAScript 6 中新增的用于变量声明的关键字，作用域为代码块，不可重复定义 |
| const  | 用于声明常量的关键字，常量一旦声明，不能修改                 |

### 运算符

-   算术运算符：＋，一，*，1，%，++，一
-   ﻿﻿赋值运算符：=，+=，-=，*=，/=，%=
-   ﻿﻿比较运算符：＞，<，>=，<=，=，==，===
-   ﻿﻿逻辑运算符：&&，II，！
-   三元运算符：条件表达式？true_value:false_value

注意 == 和 === 是不一样的

-   ==：只比较值是否相等，不区分数据类型，哪怕类型不一致，==也会自动转换类型进行值得比较
-   ===：不光比较值，还要比较类型，如果类型不一致，直接返回false

### 类型转换

-   字符串类型转为数字：将字符串字面值转为数字。如果字面值不是数字，则转为NaN
-   ﻿﻿其他类型转为boolean：
    -   ﻿Number: 0和 NaN为false，其他均转为true
    -   ﻿String：空定符串为false，其他均转为true
    -   Null 和 undefined：均转为false

### 函数与对象

通过 `function` 关键字进行定义。形参和返回值不需要声明类型

JS 对象分为三类：基本对象、BOM 对象（浏览器对象）和 DOM 对象（文档对象）

JS 中的数组类似于 Java 中的集合，长度可变，存放数据类型可变

JSON 对象中 key 必须使用引号并且是双引号标记，value 可以是任意数据类型

BOM 提供了5个对象

-   Window：浏览器窗口对象
-   Navigator：浏览器对象
-   Screen：屏幕对象
-   History：历史记录对象
-   Location：地址栏对象

DOM 将标记语言的各个组成部分封装为对应的对象，JS 通过 DOM 对 HTML进行操作

-   Document：整个文档对象
-   Element：元素对象
-   Attribute：属性对象
-   Text：文本对象
-   Comment：注释对象

### 事件

HTML 事件是发生在 HTML 元素上的事情，当事件触发时，可以自动的完成对应的功能，这被称为事件监听

**绑定方式**

1.   通过 HTML 标签中的事件属性进行绑定
2.   通过 DOM 中 Element 元素的事件属性进行绑定

**常见事件**

| 事件属性名  | 说明                     |
| ----------- | ------------------------ |
| onclick     | 鼠标单击事件             |
| onblur      | 元素失去焦点             |
| onfocus     | 元素获得焦点             |
| onload      | 某个页面或图像被完成加载 |
| onsubmit    | 当表单提交时触发该事件   |
| onmouseover | 鼠标被移到某元素之上     |
| onmouseout  | 鼠标从某元素移开         |

## Vue

MVVM 开发思想（Model-View-ViewModel），实现数据的双向绑定

-   Model：数据模型，特指前端中通过请求从后台获取的数据
-   View：视图，用于展示数据的页面，可以理解成我们的 HTML+CSS 搭建的页面，但是没有数据
-   ViewModel：数据绑定到视图，负责将数据（Model）通过 JavaScript 的 DOM 技术，将数据展示到视图（View）上

Vue.js 是一套构建用户界面的 渐进式框架。与其他重量级框架不同的是，Vue 采用自底向上增量开发的设计。Vue 的核心库只关注视图层，并且非常容易学习，非常容易与其它库或已有项目整合。Vue.js 的目标是通过尽可能简单的 API 实现响应的数据绑定和组合的视图组件

### Vue 指令

| 指令      | 作用                                                         |
| --------- | ------------------------------------------------------------ |
| v-bind    | 为 HTML 标签绑定属性值，如设置 href、CSS 样式等              |
| v-model   | 在表单元素上创建双向数据绑定                                 |
| v-on      | 为 HTML 标签绑定事件                                         |
| v-if      | 条件性地渲染某元素，判定为 true 时渲染，否则不渲染           |
| v-else    | v-if 的“否则”块，在 v-if 判定为 false 时渲染                 |
| v-else-if | v-if 的“否则如果”块，增加多条件判断逻辑                      |
| v-show    | 根据条件展示某元素，区别在于其会渲染所有元素，通过设置 display 是否展示 |
| v-for     | 列表渲染，遍历数组元素或对象属性                             |

### Vue 生命周期

Vue 对象从创建到销毁的过程。Vue 的生命周期包含8个阶段：每触发一个生命周期事件，会自动执行一个生命周期方法，这些生命周期方法也被称为钩子方法

| 状态          | 阶段周期   |
| ------------- | ---------- |
| beforeCreate  | 创建前     |
| created       | 创建后     |
| beforeMount   | 挂载前     |
| mounted       | 挂载完成， 用于页面初始化自动的 Ajax 请求后台数据|
| beforeUpdate  | 更新前     |
| updated       | 更新后     |
| beforeDestroy | 销毁前     |
| destroyed     | 销毁后     |

![vue_lifecycle](assets/vue_lifecycle.png)

### Element 组件库

组件：组成网页的部件，如超链接、按钮、图片、表格、表单、分页条等

使用步骤：1）下载安装组件库；2）在入口 JS 文件中引入组件库；3）在 `src/views` 目录下创建组件文件；4）从官方文档中找到相应组件，复制源码

### Vue 路由

前端路由：URL 中的 hash（#号之后的内容）与组件之间的对应关系

需要使用官方提供的 Vue Router

-   VueRouter：路由器类，根据路由请求在路由视图中动态渲染选中的组
-   `<router-link>`：请求链接组件，浏览器会解析成 `<a>`
-   `<router-view>`：动态视图组件，用来渲染展示与路由路径对应的组件

首先 VueRouter 根据我们配置的 URL 的 hash 片段和路由的组件关系去维护一张路由表；然后我们页面提供一个 `<router-link>` 组件,用户点击，发出路由请求；接着我们的 VueRouter 根据路由请求，在路由表中找到对应的vue组件；最后 VueRouter 会切换`<router-view>` 中的组件，从而进行视图的更新

## Ajax

Asynchronous JavaScript And XML，异步的 JavaScript 和 XML。其作用包含

-   与服务器进行**数据交换**：通过 Ajax 可以给服务器发送请求，并获取服务器响应的数据
-   **异步交互**：可以在不重新加载整个页面的情况下，与服务器交换数据并更新部分网页的技术，如：搜索联想、用户名是否可用的校验等等

原生 Ajax 使用步骤

1.   创建 XMLHttpRequest 对象，用于和服务器交换数据
2.   调用对象的 `open()` 方法设置请求的参数信息，例如请求地址，请求方式。然后调用 `send()` 方法向服务器发送请求
3.   通过绑定事件的方式，来获取服务器响应的数据

### Axios

Axios 是对原生的 Ajax 进行封装，简化书写

使用 Axios 只需要引入文件，然后就可以使用 Axios 发送请求，并获取响应结果

`axios()` 发送异步请求的，小括号中使用 JS 的 JSON 对象传递请求相关的参数：

-   method 属性：用来设置请求方式的。取值为 get 或者 post
-   url 属性：用来书写请求的资源路径。如果是 get 请求，需要将请求参数拼接到路径的后面，格式为： url?参数名=参数值&参数名2=参数值2
-   data 属性：作为请求体被发送的数据。也就是说如果是 post 请求的话，数据需要作为 data 属性的值

`then()` 需要传递一个匿名函数。我们将 then() 中传递的匿名函数称为**回调函数**，意思是该匿名函数在发送请求时不会被调用，而是在成功响应后调用的函数。而该回调函数中的 resp 参数是对响应的数据进行封装的对象，通过 resp.data 可以获取到响应的数据

## Nginx

一款轻量级的 Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器。其特点是占有内存少，并发能力强

Nginx 服务器的默认端口号是80。如果端口被占用，我们需要通过 conf/nginx.conf 配置文件来修改端口号

# 后端开发

## Maven

Apache 旗下的一个开源项目，是一款用于管理和构建 Java 项目的工具

Maven 作用包含

-   依赖管理
    -   方便快捷的管理项目依赖的资源（jar包），避免版本冲突问题
    -   在 Maven 项目的 `pom.xml` 添加配置
-   统一项目结构
    -   提供标准、统一的项目结构（针对于使用不同开发工具的情况）
-   项目构建
    -   提供标准跨平台的自动化项目构建方式

### Maven 仓库

仓库：用于存储资源，管理各种 jar 包

Maven 仓库分为

-   本地仓库：自己计算机上的一个目录（用来存储jar包）
-   中央仓库：由Maven团队维护的全球唯一的。仓库地址： https://repo1.maven.org/maven2/
-   远程仓库（私服）：一般由公司团队搭建的私有仓库

优先级：本地仓库 > 远程仓库 > 中央仓库

### 依赖管理

1.  ﻿﻿在 pom.xml 中编写 `<dependencies>` 标签
2.  ﻿﻿﻿在`dependencies>` 标签中 使用 `<dependency>` 引入坐标
3.  ﻿﻿﻿定义坐标的 groupld，artifactld 和 version
4.  ﻿﻿点击刷新按钮，引入最新加入的坐标

Maven 的依赖具有传递性，所以会自动把所依赖的其他jar包也一起导入，**依赖传递**可以分为：

1.   直接依赖：在当前项目中通过依赖配置建立的依赖关系
2.   间接依赖：被依赖的资源如果依赖其他资源，当前项目间接依赖其他资源

可以通过**排除依赖**来主动断开依赖的资源（被排除的资源无需指定版本）

### 依赖范围

在项目中导入依赖的 jar 包后，默认情况下，可以在任何地方使用

通过 `<scope></scope>` 设置作用范围

1.   主程序范围有效（main文件夹范围内）

2.   测试程序范围有效（test文件夹范围内）
3.   是否参与打包运行（package指令范围内）

| scope值         | 主程序 | 测试程序 | 打包（运行） | 范例        |
| --------------- | ------ | -------- | ------------ | ----------- |
| compile（默认） | Y      | Y        | Y            | log4j       |
| test            | -      | Y        | -            | junit       |
| provided        | Y      | Y        | -            | servlet-api |
| runtime         | -      | Y        | Y            | JDBC 驱动   |

### 生命周期

Maven 有**三套**相互独立的生命周期

-   clean：清理工作
-   default：核心工作，如：编译、测试、打包、安装、部署等
-   site：生成报告、发布站点等

其中主要阶段包含

-   clean：移除上一次构建生成的文件
-   compile：编译项目源代码
-   test：使用合适的单元测试框架运行测试(junit)
-   package：将编译后的文件打包，如：jar、war等
-   install：安装项目到本地仓库

在同一套生命周期中，执行某个阶段时，前面的所有阶段都会自动运行

### Maven 进阶

#### 分模块设计

将项目按照功能/结构拆分成若干个子模块，方便项目的管理维护、拓展，也方便模块键的相互调用、资源共享

通常需要将业务代码与通用组件分离，业务代码也可以根据业务模块分组

#### 继承

描述两个工程间的关系，与java中的继承相似，子工程可以继承父工程中的配置信息，常见于依赖关系的继承，用于简化依赖配置，统一管理依赖

Maven打包方式：

-   jar：普通模块打包，springboot项目基本都是jar包（内嵌tomcat运行）

-   war：普通web程序打包，需要部署在外部的tomcat服务器中运行

-   pom：父工程或聚合工程，该模块不写代码，仅进行依赖管理

#### 版本锁定

子工程中除了继承父工程的依赖，还可以配置自己的依赖。多个子工程对于同一个依赖应该使用相同版本，因此需要使用 Maven 提供的版本锁定功能

在 Maven 中，可以在父工程的 pom 文件中通过 `<dependencyManagement>` 来统一管理依赖版本

```xml
<!--父工程-->
<dependencyManagement>
    <dependencies>
        <!--JWT令牌-->
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
            <version>0.9.1</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

注意：这个标签只能进行统一管理依赖版本，并不会将这个依赖直接引入进来子工程依然需要引入依赖，但不需要规定版本号

```xml
<!--子工程-->
<dependencies>
    <!--JWT令牌-->
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt</artifactId>
    </dependency>
</dependencies>
```

在父工程中还可以通过自定义属性及属性引用的形式，将依赖的版本号进行集中管理维护

```xml
<properties>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
    <lombok.version>1.18.24</lombok.version>
    <jjwt.version>0.9.1</jjwt.version>
    <aliyun.oss.version>3.15.1</aliyun.oss.version>
    <jaxb.version>2.3.1</jaxb.version>
    <activation.version>1.1.1</activation.version>
    <jaxb.runtime.version>2.3.3</jaxb.runtime.version>
</properties>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>${lombok.version}</version>
</dependency>
```

#### 聚合

分模块后，对其中模块进行打包时，需要将所有相关的模块先打包，才能成功打包该模块（操作繁琐）

将多个模块组织成一个整体，同时进行项目的构建。因此需要使用聚合工程（有且仅有一个 pom 文件，通常使用父工程）快速构建模块项目

在 Maven 中可以在聚合工程中通过 `<moudules>` 设置当前聚合工程所包含的子模块的名称

```xml
<!--聚合其他模块-->
<modules>
    <module>../tlias-pojo</module>
    <module>../tlias-utils</module>
    <module>../tlias-web-management</module>
</modules>
```

#### 继承与聚合对比

作用

- 聚合用于快速构建项目
- 继承用于简化依赖配置、统一管理依赖

相同点：

-   聚合与继承的 pom.xml 文件打包方式均为 pom，通常将两种关系制作到同一个 pom 文件中
-   聚合与继承均属于设计型模块，并无实际的模块内容

不同点：

-   聚合是在聚合工程中配置关系，聚合可以感知到参与聚合的模块有哪些

-   继承是在子模块中配置关系，父模块无法感知哪些子模块继承了自己

### 私服

一种特殊的远程仓库，它是架设在局域网内的仓库服务，用来代理位于外部的中央仓库，用于解决团队内部的资源共享与资源同步问题

私服配置

-   在 Maven 的配置文件中配置访问私服的用户名、密码
-   在 Maven 的配置文件中配置连接私服的地址（URL 地址）
-   在项目的 pom.xml 文件中配置上传资源的位置（URL 地址），使用 distributionManagement 标签

私服仓库

-   RELEASE：存储自己开发的 RELEASE 发布版本的资源
-   SNAPSHOT：存储自己开发的 SNAPSHOT 发布版本的资源
-   Central：存储的是从中央仓库下载下来的依赖

## Tomcat

Tomcat 是一个 轻量级 Web 服务器，对 HTTP 协议的操作进行**封装**，使得程序员不必直接对协议进行操作（不用程序员自己写代码去解析 HTTP 协议规则)，让Web开发更加便捷。主要功能是"提供网上信息浏览服务"

由于 Tomcat 只支持 Servlet/JSP 少量 JavaEE 规范，所以是一个开源免费的轻量级Web服务器

Spring Boot 内置了 Tomcat，用于处理 HTTP 请求

## 请求参数

使用 Postman 进行模拟前端测试，可发送不同的请求（GET、POST等）

### 简单参数

```java
@RestController
public class RequestController {
    // http://localhost:8080/simpleParam?name=Tom&age=10
    // 第1个请求参数： name=Tom 参数名:name，参数值:Tom
    // 第2个请求参数： age=10 参数名:age , 参数值:10
    // springboot方式
  	// 形参名和请求参数名保持一致
    @RequestMapping("/simpleParam")
    public String simpleParam(String name , Integer age ){
        System.out.println(name+" : "+age);
        return "OK";
		}
}
```

如果形参名与请求参数名不一致时，运行不会报错，但无法正确接收参数。可使用 `@RequestParam` 实现映射

```java
@RestController
public class RequestController {
    // http://localhost:8080/simpleParam?name=Tom&age=20
    // 请求参数名：name
    // springboot方式
    @RequestMapping("/simpleParam")
    public String simpleParam(@RequestParam("name") String username, Integer age ){
        System.out.println(username+" : "+age);
        return "OK";
    }
}
```

注意：`@RequestParam` 中的 required 属性默认为 true，代表该请求参数必须传递，如果不传递将报错

### 实体参数

在使用简单参数做为数据传递方式时，前端传递了多少个请求参数，后端 controller 方法中的形参就要书写多少个。如果请求参数比较多，通过上述的方式一个参数一个参数的接收，会比较繁琐

此时，我们可以考虑将请求参数封装到一个实体类对象中。 要想完成数据封装，需要遵守如下规则：请求参数名与实体类的属性名相同（简单实体）或请求参数名与形参对象属性名相同，按照对象层次结构关系即可接收嵌套实体类属性参数（复杂实体）

### 数组集合参数

数组参数：请求参数名与形参数组名称相同且请求参数为多个，定义数组类型形参即可接收参数

```java
@RestController
public class RequestController {
    // 数组参数
  	// 形参名与请求参数名保持一致
    @RequestMapping("/arrayParam")
    public String arrayParam(String[] hobby){
        System.out.println(Arrays.toString(hobby));
        return "OK";
    }
}
```

集合参数：请求参数名与形参集合对象名相同且请求参数为多个，`@RequestParam` 绑定参数关系

```java
@RestController
public class RequestController {
    // 集合参数
  	// 形参名与请求参数名保持一致
    @RequestMapping("/listParam")
    public String listParam(@RequestParam List<String> hobby){
        System.out.println(hobby);
        return "OK";
    }
}
```

### 日期参数

对于日期类型的参数在进行封装的时候，需要通过 `@DateTimeFormat` 注解，以及其 pattern 属性来设置日期的格式

```java
@RestController
public class RequestController {
    //日期时间参数
    @RequestMapping("/dateParam")
    public String dateParam(@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") LocalDateTime updateTime){
        System.out.println(updateTime);
        return "OK";
    }
}
```

### JSON 参数

服务端 Controller 方法接收 JSON 格式数据：

-   传递 JSON 格式的参数，在 Controller 中会使用实体类进行封装
-   封装规则：JSON 数据键名与形参对象属性名相同，定义 POJO 类型形参即可接收参数。需要使用 `@RequestBody` 标识将 JSON 数据映射到形参的实体类对象中

```java
@RestController
public class RequestController {
    //JSON参数
    @RequestMapping("/jsonParam")
    public String jsonParam(@RequestBody User user){
        System.out.println(user);
        return "OK";
    }
}
```

### 路径参数

通过请求 URL 直接传递参数，使用 `{[id]}` 标识路径参数，需要使用 `@PathVariable` 获取路径参数

```java
@RestController
public class RequestController {
    //路径参数
    @RequestMapping("/path/{id}")
    public String pathParam(@PathVariable Integer id){
        System.out.println(id);
        return "OK";
    }
}
```

可以同时传递多个路径参数

```java
@RestController
public class RequestController {
    //路径参数
    @RequestMapping("/path/{id}/{name}")
    public String pathParam2(@PathVariable Integer id, @PathVariable String name){
        System.out.println(id + " : " + name);
        return "OK";
    }
}
```

## 响应数据

Controller 方法中的 return 的结果返回给请求端，使用了 `@ReponseBody` 注解，将方法返回值直接响应给请求端，如果返回值类型是实体对象/集合，将会转换为 JSON 格式后在响应给浏览器

### 统一返回结果

项目中 Controller 方法将成千上万，使用传统方式将造成整个项目难以维护，因为每个 Controller 返回值不一致

因此需要统一返回结果。统一的返回结果使用类来描述，在这个结果中包含：

-   响应状态码：当前请求是成功，还是失败
-   状态码信息：给页面的提示信息
-   返回的数据：给前端响应的数据（字符串、对象、集合）

## 分层解耦

### 三层架构

代码可按照三层架构分开实现

-   Controller：控制层。接收前端发送的请求，对请求进行处理，并响应数据
-   Service：业务逻辑层。处理具体的业务逻辑
-   Dao：数据访问层（Data Access Object），也称为持久层。负责数据访问操作，包括数据的增、删、改、查

程序执行流程

-   前端发起的请求，由 Controller 层接收（Controller 响应数据给前端）
-   Controller 层调用 Service 层来进行逻辑处理
-   Serivce 层调用 Dao 层（逻辑处理过程中需要用到的一些数据要从 Dao 层获取）
-   Dao 层操作文件中的数据，并将数据会返回给 Service 层
-   Service 层处理数据之后将其返回给 Controller 层

### 控制反转（IOC）与依赖注入（DI）

软件开发涉及到的两个概念：内聚和耦合

-   内聚：软件中各个功能模块内部的功能联系
-   耦合：衡量软件中各个层/模块之间的依赖、关联的程度。

软件设计原则：**高内聚低耦合**

-   高内聚指的是：一个模块中各个元素之间的联系的紧密程度，如果各个元素(语句、程序段)之间的联系程度越高，则内聚性越高，即 "高内聚"
-   低耦合指的是：软件中各个层、模块之间的依赖关联程序越低越好

**控制反转**： Inversion Of Control，简称 IOC。对象的创建控制权由程序自身转移到外部（容器），这种思想称为控制反转

-   对象的创建权由程序员主动创建转移到容器(由容器创建、管理对象)。这个容器称为：IOC 容器或 Spring 容器
-   加入 `@Component` 注解表明将当前类交给 IOC 容器管理，成为 IOC 容器中的 Bean

**依赖注入**： Dependency Injection，简称DI。容器为应用程序提供运行时，所依赖的资源，称之为依赖注入

-   程序运行时需要某个资源，容器就会为其提供这个资源
-   加入 `@Autowired` 注解表明运行时，IOC 容器会提供该类型的 Bean 对象，并赋值给该变量

IOC容器中创建、管理的对象，称之为 **Bean 对象**

#### 控制反转

为了更好的标识 Web 应用程序开发当中，Bean 对象到底归属于哪一层，又提供了 `@Component` 的衍生注解：

-   `@Controller` （标注在控制层类上）
-   `@Service` （标注在业务层类上）
-   `@Repository` （标注在数据访问层类上）

注意：使用四大注解声明的 Bean，要想生效，还需要被组件扫描注解 `@ComponentScan` 扫描。 `@ComponentScan` 注解虽然没有显式配置，但是实际上已经包含在了引导类声明注解 `@SpringBootApplication`  中， 默认扫描的范围是 Spring Boot 启动类所在包及其子包

#### 依赖注入

`@Autowired` 注解，默认是按照类型进行自动装配的（去IOC容器中找某个类型的对象，然后完成注入操作）

当存在多个相同类型的 Bean，程序会报错。解决这个问题需要添加额外的注解

-   `@Primary` ：确定默认的实现
-   `@Qualifier` ：在 value 属性中指定注入的bean的名称
-   `@Resource` ：通过 name 属性指定要注入的bean的名称

`@Autowired` 与 `@Resource` 的区别

-   `@Autowired` 是spring框架提供的注解，而 `@Resource` 是JDK提供的注解
-   `@Autowired` 默认是按照类型注入，而 `@Resource` 是按照名称注入

## MyBatis

持久层框架，用于简化 JDBC 开发

-   持久层：指的是就是数据访问层(dao)，是用来操作数据库的
-   框架：是一个半成品软件，是一套可重用的、通用的、软件基础代码模型。在框架的基础上进行软件开发更加高效、规范、通用、可拓展

可以在 IDEA 中配置与 MySQL 数据库的连接，保证 SQL 语句的正确性

### JDBC

Java DataBase Connectivity（JDBC）使用 Java 语言操作关系型数据库的一套 API

-   Sun 公司官方定义的一套操作所有关系型数据库的规范，即接口
-   各个数据库厂商去实现这套接口，提供数据库驱动 jar 包
-   我们可以使用这套接口（JDBC）编程，真正执行的代码是驱动 jar 包中的实现类

### 数据库连接池

数据库连接池是个容器，负责分配、管理数据库连接（Connection），允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个

-   程序在启动时，会在数据库连接池（容器）中，创建一定数量的 Connection 对象
-   客户端在执行 SQL 时，先从连接池中获取一个 Connection 对象，然后在执行 SQL 语句，SQL 语句执行完之后，释放 Connection 时就会把 Connection 对象归还给连接池（Connection 对象可以复用）
-   释放空闲时间超过最大空闲时间的连接，来避免因为没有释放连接而引起的数据库连接遗漏

### Lombok

Lombok 是一个实用的 Java 类库，通过注解的形式自动生成构造器、getter/setter、equals、hashcode、toString等方法，并可以自动化生成日志变量，简化java开发、提高效率

| 注解                | 作用                                                         |
| ------------------- | ------------------------------------------------------------ |
| @Getter/@Setter     | 为所有属性提供 getter/setter 方法                            |
| @ToString           | 自动生成易读的 `toString()` 方法                             |
| @EqualsAndHashCode  | 根据非静态字段自动重写 `equals()` 和 `hashCode()` 方法       |
| @Data               | 综合注解，包含 `@Getter`、`@Setter`、`@ToString`、`@EqualsAndHashCode` |
| @NoArgsConstructor  | 为类生成无参构造方法                                         |
| @AllArgsConstructor | 为类生成全参数构造方法（不包括 static 字段）                 |

引入依赖

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
```

### 预编译 SQL

Mybatis 中使用预编译 SQL 语句，即输入参数并不会直接拼接在 SQL 语句之后

使用预编译的优势

-   性能更高：预编译SQL，编译一次之后会将编译后的SQL语句缓存起来，后面再次执行这条语句时，不会再次编译（只是输入的参数不同）
-   更安全（防止SQL注入）：将敏感字进行转义，保障SQL的安全性
    -   SQL 注入：是通过操作输入的数据来修改事先定义好的 SQL 语句，以达到执行代码对服务器进行攻击的方法

Mybatis 中提供两种参数占位符

-   `#{...}`：执行 SQL，会将 `#{…}` 替换为`?`，生成预编译 SQL，会自动设置参数值（用于参数传递）
-   `${...}` ：拼接 SQL，直接将参数拼接在 SQL 语句中，存在 SQL 注入问题（用于对表名、列表进行动态设置）

### XML 映射文件

Mybatis 除使用注解，还可以使用XML 配置映射语句，适合于实现**复杂**的 SQL 功能

1.   XML 映射文件的名称与 Mapper 接口名称一致，并且将 XML 映射文件和 Mapper 接口放置在相同包下（同包同名）
2.   XML 映射文件的 namespace 属性为 Mapper 接口全限定名一致
3.   XML 映射文件中 SQL 语句的 id 与 Mapper 接口中的方法名一致，并保持返回类型一致

### 动态 SQL

当通过硬编码传递参数的话，如果只传递了其中一部分参数，其余参数为 null 的情况下，无法得到正确解。因此需要使 SQL 语句会随着用户的输入或外部条件的变化而变化，即动态 SQL

#### `<where>`

只会在子元素有内容的情况下才插入 where 子句，而且会自动去除子句的开头的 AND 或 OR

#### `<set>`

在 update 语句中动态插入 set 关键字，并会删掉额外的逗号

#### `<if>`

用于判断条件是否成立。使用 test 属性进行条件判断，如果条件为 true，则拼接 SQL

```xml
<if test="条件表达式">
		要拼接的sql语句
</if>
```

#### `<foreach>`

遍历集合中的所有元素

```xml
<foreach collection="集合名称" item="集合遍历出来的元素/项" separator="每一次遍历使用的分隔符"open="遍历开始前拼接的片段" close="遍历结束后拼接的片段">
</foreach>
```

#### `<sql>` 与 `<include>`

`<sql>` 标签用于定义可重复的 SQL 片段

`<include>` 标签通过属性 refid 指定包含的 SQL 片段

## Spring Boot

Spring 框架提供很多实用功能，如：依赖注入、事务管理、Web 开发支持、数据访问、消息服务等

但直接基于 Spring Framework 进行开发，存在配置繁琐、入门难度大等问题。Spring Boot 可以帮助开发者非常快速的构建应用程序，具有简化开发、提高效率量大特点

### REST 开发规范

REST（Representational State Transfer），表述性状态转换，它是一种软件架构风格

-   通过 URL 定位要操作的资源
-   通过 HTTP 动词（请求方式）来描述具体的操作

开发流程

1.   查看页面原型明确需求
     -   根据页面原型和需求，进行表结构设计、编写接口文档(已提供)
2.   阅读接口文档
3.   思路分析
4.   功能接口开发
     -   开发后台的业务功能，一个业务功能，我们称为一个接口
5.   功能接口测试
     -   功能开发完毕后，先通过Postman进行功能接口测试，测试通过后，再和前端进行联调测试
6.   前后端联调测试
     -   和前端开发人员开发好的前端工程一起测试

### 请求路径简化

在 Spring 当中为了简化请求路径的定义，可以把公共的请求路径，直接抽取到类上，在类上加一个注解 `@RequestMapping`，并指定请求路径

### 分页查询

当总表记录数过多时，往往需要分页展示查询结果，因此需要根据查询页数和每页显示的记录数（前端提供）计算出当前查询页应该显示的数据。另外还需要返回总记录数等结果，因此可以将返回结果封装到一个新的类中

`@RequestParam` 注解中的 defaultValue 可以设置默认值

可以使用 `PageHelper` 插件（需要使用 Spring 版本）简化分页查询的代码

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.4.6</version>
</dependency>
```

### 文件上传

将本地图片、视频、音频等文件上传到服务器，供其他用户浏览或下载的过程

前端程序上传文件需要通过 form 标签实现。上传文件的原始form表单，要求表单必须具备以下三点（上传文件页面三要素）：1）表单必须有 file 域，用于选择要上传的文件；2）表单提交方式必须为 POST；3）表单的编码类型 enctype 必须要设置为：multipart/form-data

### 文件存储

文件上传时在服务端会产生一个临时文件，请求响应完成之后，这个临时文件被自动删除，并没有进行保存，因此需要将接收文件存储到特定位置

#### 本地存储

使用 MultipartFile 类提供的 API 方法，把临时文件转存到本地磁盘目录下

-   使用 `getOriginalFilename()` 方法获得原始文件名
-   使用 `UUID.randomUUID()` 创建唯一的文件名
-   使用 `transferTo()` 方法将文件存储在本地磁盘中

Spring Boot 默认单个上传文件最大为 1M，需要配置 application.properties 支持大文件的上传

```properties
#配置单个文件最大上传大小
spring.servlet.multipart.max-file-size=10MB
#配置单个请求最大上传大小(一次请求可以上传多个文件)
spring.servlet.multipart.max-request-size=100MB
```

#### 云存储

使用云服务通常分别三步

-   准备工作（注册、开通相关服务等）
-   参考官方 SDK 编写程序
-   集成使用

### 配置优化

目前云存储采用硬编码配置，但会存在两个主要问题

1.   如果这些参数发生变化了，就必须在源程序代码中改动这些参数，然后需要重新进行代码的编译，将Java代码编译成class字节码文件再重新运行程序。（比较繁琐）

2.   如果我们开发的是一个真实的企业级项目， Java类可能会有很多，如果将这些参数分散的定义在各个Java类当中，我们要修改一个参数值，我们就需要在众多的Java代码当中来定位到对应的位置，再来修改参数，修改完毕之后再重新编译再运行。（参数配置过于分散，是不方便集中的管理和维护）

因此需要对配置采用优化措施

#### 项目配置文件

在 application.properties 文件中添加相关的配置

```properties
#自定义的阿里云OSS配置信息
aliyun.oss.endpoint=https://oss-cn-hangzhou.aliyuncs.com
aliyun.oss.accessKeyId=LTAI4GCH1vX6DKqJWxd6nEuW
aliyun.oss.accessKeySecret=yBshYweHOpqDuhCArrVHwIiBKpyqSL
aliyun.oss.bucketName=web-tlias
```

在 Java 文件中采用 `@Value` 注解（用于外部配置的属性注入）

```java
@Value("${aliyun.oss.endpoint}")
private String endpoint;

@Value("${aliyun.oss.accessKeyId}")
private String accessKeyId;

@Value("${aliyun.oss.accessKeySecret}")
private String accessKeySecret;

@Value("${aliyun.oss.bucketName}")
private String bucketName;
```

#### yml 配置文件

```yaml
aliyun:
  oss:
    endpoint: https://oss-cn-hangzhou.aliyuncs.com
    accessKeyId: LTAI4GCH1vX6DKqJWxd6nEuW
    accessKeySecret: yBshYweHOpqDuhCArrVHwIiBKpyqSL
    bucketName: web-397
```

yml 格式的数据具有多个特点

1.   容易阅读
2.   容易与脚本语言交互
3.   以数据为核心，重数据轻格式

其基本语法

-   大小写敏感
-   **数值前边必须有空格**，作为分隔符
-   使用缩进表示层级关系，缩进时，不允许使用Tab键，只能用空格（idea中会自动将Tab转换为空格）
-   缩进的空格数目不重要，只要相同层级的元素左侧对齐即可
-   `#` 表示注释，从这个字符一直到行尾，都会被解析器忽略

#### `@ConfigurationProperties` 注解

对于多个外部属性，都通过 `@Value` 注入会造成开发降低效率。因此 Spring 提供了简化方式

1.   需要创建一个实现类，且实体类中的属性名和配置文件当中 key 的名字必须要一致
2.   需要将实体类交给 Spring 的 IOC 容器管理，成为 IOC 容器当中的 bean 对象
3.   在实体类上添加 `@ConfigurationProperties` 注解，并通过 prefix 属性来指定配置参数项的前缀

```java
import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

/*阿里云OSS相关配置*/
@Data
@Component
@ConfigurationProperties(prefix = "aliyun.oss")
public class AliOSSProperties {
    //区域
    private String endpoint;
    //身份ID
    private String accessKeyId ;
    //身份密钥
    private String accessKeySecret ;
    //存储空间
    private String bucketName;
}
```

```java
// 使用配置示例
// 注入配置参数实体类对象
@Autowired
private AliOSSProperties aliOSSProperties;

String endpoint = aliOSSProperties.getEndpoint();
String accessKeyId = aliOSSProperties.getAccessKeyId() ;
String accessKeySecret = aliOSSProperties.getAccessKeySecret() ;
String bucketName = aliOSSProperties.getBucketName();
```

### 登录校验

在服务器端接收到浏览器发送过来的请求之后，首先要对请求进行校验。先要校验一下用户登录了没有，如果用户已经登录了，就直接执行对应的业务操作就可以了；如果用户没有登录，此时就不允许他执行相关的业务操作，直接给前端响应一个错误的结果，最终跳转到登录页面，要求他登录成功之后，再来访问对应的数据

#### 会话跟踪

浏览器与服务器之间的一次连接就称为一次会话。同一个浏览器在未关闭之前请求了多次服务器，这多次请求是属于同一个会话，有多个浏览器客户端和服务器建立了连接时，就会有多个会话

会话跟踪：一种维护浏览器状态的方法，服务器需要识别多次请求是否来自于同一浏览器，以便在同一次会话的多次请求间共享数据

**Cookie**

Cookie 是客户端会话跟踪技术，它是存储在客户端浏览器的

使用 Cookie 来跟踪会话，在浏览器第一次发起请求来请求服务器的时候，服务器端来设置一个 Cookie，服务器端在给客户端在响应数据的时候，会自动的将 cookie 响应给浏览器，浏览器接收到响应回来的 Cookie 之后，会自动的将 Cookie 的值存储在浏览器本地。接下来在后续的每一次请求当中，都会将浏览器本地所存储的 Cookie 自动地携带到服务端

优点：HTTP协议中支持的技术（像 Set-Cookie 响应头的解析以及 Cookie 请求头数据的携带，都是浏览器自动进行的，是无需我们手动操作的）

缺点：1）移动端APP（Android、IOS）中无法使用 Cookie；2）不安全，用户可以自己禁用 Cookie；3）Cookie不能跨域

**Session**

Session 是服务器端会话跟踪技术，他是存储在服务器端的

浏览器在第一次请求服务器的时候，会话对象是不存在的，这个时候服务器会自动的创建一个会话对象Session 。而每一个会话对象 Session ，它都有一个ID。接下来，服务器端在给浏览器响应数据的时候，它会将 Session 的 ID 通过 Cookie 响应给浏览器。其实在响应头当中增加了一个 Set-Cookie 响应头。这个 Set-Cookie 响应头对应的值是否为 Cookie，浏览器会自动识别这个响应头，然后自动将 Cookie 存储在浏览器本地。在后续的每一次请求当中，都会将 Cookie 的数据获取出来，并且携带到服务端。接下来服务器拿到 JSESSIONID 这个 Cookie 的值，也就是 Session 的 ID。拿到 ID 之后，就会从众多的 Session 当中来找到当前请求对应的会话对象 Session

优点：Session 是存储在服务端的，较为安全

缺点：1）服务器集群环境下无法直接使用Session；2）移动端APP（Android、IOS）中无法使用Cookie；3）用户可以自己禁用Cookie；4）Cookie不能跨域

**令牌技术**

在请求登录接口的时候，如果登录成功，我就可以生成一个令牌，令牌就是用户的合法身份凭证。接下来我在响应数据的时候，我就可以直接将令牌响应给前端。接下来我们在前端程序当中接收到令牌之后，就需要将这个令牌存储起来。这个存储可以存储在 Cookie 当中，也可以存储在其他的存储空间（如 Local Storage）当中。接下来，在后续的每一次请求当中，都需要将令牌携带到服务端。携带到服务端之后，接下来我们就需要来校验令牌的有效性。如果令牌是有效的，就说明用户已经执行了登录操作，如果令牌是无效的，就说明用户之前并未执行登录操作

优点：1）支持PC端、移动端；2）解决集群环境下的认证问题；3）减轻服务器的存储压力（无需在服务器端存储）

缺点：需要自己实现（包含生成、传递与校验）

#### JWT 令牌

定义了一种简洁的、自包含的格式，用于在通信双方以 JSON 数据格式安全的传输信息。由于数字签名的存在，这些信息是可靠的

JWT 令牌由三个部分组成，三个部分之间使用英文的点来分割

-   第一部分：Header（头）， 记录令牌类型、签名算法等。 例如：{"alg":"HS256","type":"JWT"}
-   第二部分：Payload（有效载荷），携带一些自定义信息、默认信息等。 例如：{"id":"1","username":"Tom"}
-   第三部分：Signature（签名），防止Token被篡改、确保安全性。将header、payload，并加入指定秘钥，通过指定签名算法计算而来

生成 JWT 令牌时，会对 JSON 格式的数据进行一次 base64 编码（基于64个可打印的字符来表示二进制数据的编码方式）

```xml
<!-- 引入 JWT 依赖 -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
```

```java
public void genJwt(){
    Map<String,Object> claims = new HashMap<>();
    claims.put("id",1);
    claims.put("username","Tom");
    String jwt = Jwts.builder()
                    .signWith(SignatureAlgorithm.HS256, "itheima") //签名算法
                    .setClaims(claims) //自定义内容(载荷)
                    .setExpiration(new Date(System.currentTimeMillis() + 24 * 3600 * 1000)) // 设置24小时有效期
                    .compact();
    System.out.println(jwt);
}
```

令牌解析后可以看到 id 和过期时间，如果在解析过程中没有报错，说明令牌正确

```java
public void parseJwt(){
    Claims claims = Jwts.parser()
                        .setSigningKey("itheima")//指定签名密钥（必须保证和生成令牌时使用相同的签名密钥）
      									.parseClaimsJws("eyJhbGciOiJIUzI1NiJ9.eyJpZCI6MSwiZXhwIjoxNjcyNzI5NzMwfQ.fHi0Ub8npbyt71UqLXDdLyipptLgxBUg_mSuGJtXtBk")
                        .getBody();
    System.out.println(claims);
}
```

#### Filter 过滤器

过滤器可以把对资源的请求拦截下来，从而实现一些特殊的功能

使用了过滤器之后，要想访问 Web 服务器上的资源，必须先经过滤器，过滤器处理完毕之后，才可以访问对应的资源

过滤器一般完成一些通用的操作，比如：登录校验、统一编码处理、敏感字符处理等

使用过滤器

1.   定义过滤器：定义一个类，实现 Filter 接口，并重写其所有方法
2.   配置过滤器：Filter类上加 `@WebFilter` 注解，配置拦截资源的路径。引导类上加`@ServletComponentScan` 开启Servlet组件支持

`@WebFilter` 注解中可使用多种拦截路径

| 拦截路径  | 含义说明                                                     |
| --------- | ------------------------------------------------------------ |
| `/login`  | 只拦截访问 `/login` 这个**具体路径**的请求                   |
| `/emps/*` | 拦截 `/emps` **目录**下的所有资源，比如 `/emps/1`、`/emps/edit` |
| `/*`      | 拦截所有路径，等同于**全局拦截**                             |

在一个 Web 应用程序当中，可以配置多个过滤器，多个过滤器就形成了一个过滤器链。执行优先级是按时过滤器**类名**的自动排序确定的，类名排名越靠前，优先级越高

#### Interceptor 拦截器

一种动态拦截方法调用的机制，类似于过滤器。拦截器由 Spring 提供，用来动态拦截控制器方法的执行

其作用在于拦截请求，在指定方法调用前后，根据业务需要执行预先设定的代码

使用拦截器

1.   定义拦截器：定义一个类，实现 HandlerInterceptor 借口，重写其方法，并将对象加入 IOC 容器中
2.   注册配置拦截器：实现 WebMvcConfigure r接口，并重写 addInterceptors 方法

配置拦截器时，不仅可以调用 `addPathPatterns()` 指定要拦截哪些资源，还可以调用 `excludePathPatterns()` 方法指定哪些资源不需要拦截

| 拦截路径    | 含义说明                      | 匹配示例                           | 不匹配示例             |
| ----------- | ----------------------------- | ---------------------------------- | ---------------------- |
| `/*`        | 匹配一级路径                  | `/depts`、`/emps`、`/login`        | `/depts/1`、`/emps/1`  |
| `/**`       | 匹配任意级路径                | `/depts`、`/depts/1`、`/depts/1/2` | 无（几乎全匹配）       |
| `/depts/*`  | 匹配 `/depts/` 下一级路径     | `/depts/1`、`/depts/add`           | `/depts/1/2`、`/depts` |
| `/depts/**` | 匹配 `/depts/` 下任意层级路径 | `/depts`、`/depts/1`、`/depts/1/2` | `/emps/1`              |

#### 过滤器与拦截器

![filter_and_interceptor](assets/filter_and_interceptor.png)

接口规范不同：过滤器需要实现 Filter 接口，而拦截器需要实现 HandlerInterceptor 接口

拦截范围不同：过滤器 Filter 会拦截所有的资源，而 Interceptor 只会拦截 Spring 环境中的资源

### 异常处理

当出现异常时，如果没有任何处理，响应回来的数据是一个 JSON 格式的数据。但这种 JSON 格式的数据不符合开发规范，所以前端无法解析出响应的 JSON 数据

最简单的处理方案为在所有 Controller 的所有方法中进行 `try…catch` 处理，但代码臃肿（不推荐）

#### 全局异常处理器

1.   定义一个类，在类上加上一个注解 `@RestControllerAdvice`，加上这个注解就代表我们定义了一个全局异常处理器
2.   在全局异常处理器当中，需要定义一个方法来捕获异常，在这个方法上需要加上注解 `@ExceptionHandler`。通过 `@ExceptionHandler` 注解当中的value属性来指定我们要捕获的是哪一类型的异常

`@RestControllerAdvice` = `@ControllerAdvice` + `@ResponseBody`

### 事务管理

事务是一组操作的集合，它是一个不可分割的工作单位。事务会把所有的操作作为一个整体，一起向数据库提交或者是撤销操作请求。所以这组操作要么同时成功，要么同时失败

Spring 框架中提供了 `@Transactional` 注解，其作用是在当前这个方法执行开始之前来开启事务，方法执行完毕之后提交事务。如果在这个方法执行的过程当中出现了异常，就会进行事务的回滚操作

`@Transactional` 注解书写位置：

-   方法：当前方法交给 Spring 进行事务管理
-   类：当前类中所有的方法都交由 Spring 进行事务管理
-   接口：接口下所有的实现类当中所有的方法都交给 Spring 进行事务管理

可以在 application.yml 配置文件中开启事务管理日志，这样就可以在控制看到和事务相关的日志信息

```yaml
#spring事务管理日志
logging:
  level:
    org.springframework.jdbc.support.JdbcTransactionManager: debug
```

#### 进阶

**rollbackFor 属性**

默认情况下，只有出现 RuntimeException（运行时异常）才会回滚事务。假如我们想让所有的异常都回滚，需要来配置 `@Transactional` 注解当中的 rollbackFor 属性指定出现何种异常类型回滚事务

**propagation 属性**

配置事务的传播行为（当一个事务方法被另一个事务方法调用时，这个事务方法应该如何进行事务控制）

| 属性值          | 含义说明                                                     |
| --------------- | ------------------------------------------------------------ |
| `REQUIRED`      | 【默认值】需要事务，有事务则加入，无事务则创建新事务         |
| `REQUIRES_NEW`  | 总是创建新事务，若当前已有事务则挂起，重新创建一个新事务     |
| `SUPPORTS`      | 支持事务，有事务就加入，没有事务就以非事务方式运行           |
| `NOT_SUPPORTED` | 不支持事务，总是以非事务方式运行，若当前存在事务，则挂起当前事务 |
| `MANDATORY`     | 必须在事务中运行，如果当前没有事务，就抛出异常               |
| `NEVER`         | 必须在非事务中运行，如果当前存在事务，就抛出异常             |
| ...             | ...                                                          |

### AOP 面向切面编程

AOP 可以做到在不改动原始方法的基础上，针对特定的方法进行功能的增强，即在程序运行期间在不修改源代码的基础上对已有方法进行增强（无侵入性: 解耦）

动态代理技术是 AOP 思想最主流的实现方式。而 Spring 的 AOP 是 Spring 框架的高级技术，旨在管理 bean 对象的过程中底层使用**动态代理**机制，对特定的方法进行编程(功能增强)

AOP的优势：1）减少重复代码；2）提高开发效率；3）维护方便

实现 AOP 可分为两步

1.   导入依赖：在 pom.xml 中导入 AOP 的依赖

     ```xml
     <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-aop</artifactId>
     </dependency>
     ```

2.   编写 AOP 程序：针对于特定方法根据业务需要进行编程

     ```java
     @Component
     @Aspect // 当前类为切面类
     @Slf4j
     public class TimeAspect {
         @Around("execution(* com.itheima.service.*.*(..))") // 切入点表达式
         public Object recordTime(ProceedingJoinPoint pjp) throws Throwable {
             // 记录方法执行开始时间
             long begin = System.currentTimeMillis();
             // 执行原始方法
             Object result = pjp.proceed();
             // 记录方法执行结束时间
             long end = System.currentTimeMillis();
             // 计算方法执行耗时
             log.info(pjp.getSignature() + "执行耗时: {}毫秒", end - begin);
             return result;
         }
     }
     ```

#### 概念

**连接点 JoinPoint**：可以被AOP控制的方法（暗含方法执行时的相关信息）

**通知 Advice**：指哪些重复的逻辑，也就是共性功能（最终体现为一个方法）

**切入点 PointCut**：匹配连接点的条件，通知仅会在切入点方法执行时被应用

**切面 Aspect**：描述通知与切入点的对应关系（通知 + 切入点）

**目标对象 Target**：通知所应用的对象

### AOP 进阶

#### 通知类型

`@Around`：环绕通知，此注解标注的通知方法在目标方法前、后都被执行

`@Before`：前置通知，此注解标注的通知方法在目标方法前被执行

`@After`：后置通知，此注解标注的通知方法在目标方法后被执行，无论是否有异常都会执行

`@AfterReturning`：返回后通知，此注解标注的通知方法在目标方法后被执行，有异常不会执行

`@AfterThrowing`：异常后通知，此注解标注的通知方法发生异常后执行

注意事项

-   `@Around` 环绕通知需要自己调用 `ProceedingJoinPoint.proceed()` 来让原始方法执行，其他通知不需要考虑目标方法执行
-   `@Around` 环绕通知方法的返回值，必须指定为 Object，来接收原始方法的返回值，否则原始方法执行完毕，是获取不到返回值的。

当存在大量重复的切入点表达式，可以定义一个方法使用 `PointCut` 注解抽取表达式

```java
// 切入点方法（公共的切入点表达式）
@Pointcut("execution(* com.itheima.service.*.*(..))")
private void pt(){}
```

如果该方法修饰符为 `public` ，其他外部切面类也可以使用这个方法，复用表达式

#### 通知顺序

默认按照切面类的类名字母排序：

-   目标方法前的通知方法：字母排名靠前的先执行
-   目标方法后的通知方法：字母排名靠前的后执行

修改通知顺序有两种方式

-   修改切面类的类名（这种方式非常繁琐、而且不便管理）
-   使用 Spring 提供的 `@Order` 注解（前置通知：数字小先执行；后置通知：数字小后执行）

#### 切入点表达式

用来决定项目中的哪些方法需要加入通知

`execution` 切入点表达式

-   根据我们所指定的方法的描述信息来匹配切入点方法，这种方式也是最为常用的一种方式
-   如果我们要匹配的切入点方法的方法名不规则，或者有一些比较特殊的需求，通过 `execution` 切入点表达式描述比较繁琐

`@annotation` 切入点表达式

-   基于注解的方式来匹配切入点方法。这种方式虽然多一步操作，我们需要自定义一个注解，但是相对来比较灵活。我们需要匹配哪个方法，就在方法上加上对应的注解就可以了

##### `execution(...)`

根据方法签名来匹配

```java
// 语法
// 访问修饰符：可省略（比如: public、protected）
// 包名.类名： 可省略
// throws 异常：可省略（注意是方法上声明抛出的异常，不是实际抛出的异常）
execution(访问修饰符 返回值 包名.类名.方法名(方法参数) throws 异常)
```

可使用通配符描述切入点

-   `*`：单个独立的任意符号，可以通配任意返回值、包名、类名、方法名、任意类型的一个参数，也可以通配包、类、方法名的一部分
-   `..` ：多个连续的任意符号，可以通配任意层级的包，或任意类型、任意个数的参数

可以使用 且（`&&`）、或（`||`）、非（`!`） 来组合比较复杂的切入点表达式

书写建议

-   所有业务方法名在命名时尽量规范，方便切入点表达式快速匹配。如：查询类方法都是 find 开头，更新类方法都是 update 开头
-   描述切入点方法通常基于**接口**描述，而不是直接描述实现类，增强拓展性
-   在满足业务需要的前提下，尽量缩小切入点的匹配范围。如：包名匹配尽量不使用 ` ..`，使用 `*` 匹配单个包

##### `@annotation(...)`

根据注解匹配

实现方式

1.   自定义注解

     ```java
     @Target(ElementType.METHOD)
     @Retention(RetentionPolicy.RUNTIME)
     public @interface MyLog {}
     ```

2.   业务类中为连接点的方法上添加自定义注解

     ```java
     @MyLog //自定义注解（表示：当前方法属于目标方法）
     public void delete(Integer id) {
         //1. 删除部门
         deptMapper.delete(id);
     }
     ```

3.   切面类中使用 `@annotation` 注解

     ```java
     @Before("@annotation(com.itheima.anno.MyLog)")
     public void before(){
     		log.info("MyAspect6 -> before ...");
     }
     ```

#### 连接点

Spring 中用 JoinPoint 抽象了连接点，用它可以获得方法执行时的相关信息，如目标类名、方法名、方法参数等

-   对于 `@Around`通知，获取连接点信息只能使用 ProceedingJoinPoint 类型
-   对于其他四种通知，获取连接点信息只能使用 JoinPoint（ProceedingJoinPoint的父类型）

### 配置优先级

Spring Boot 除支持三种配置文件外，为了增强程序的扩展性，还支持两种常见的方式

-   Java系统属性配置 （格式： -Dkey=value）

    ```java
    -Dserver.port=9000
    ```

-   命令行参数 （格式：--key=value）

    ```java
    --server.port=9000
    ```

配置优先级为 命令行参数 > 系统属性参数 > properties参数 > yml参数 > yaml参数

### Bean 管理

#### 获取 Bean

`@Lazy` 注解表明延迟加载，即第一次使用 Bean 对象时，才会创建 Bean 对象并交给 IOC 容器管理

Spring 容器中提供了一些方法，可以主动从 IOC 容器中获取到 Bean 对象

-   根据 name 获取 Bean

    ```java
    Object getBean(String name)
    ```

-   根据类型获取 Bean

    ```java
    <T> T getBean(Class<T> requiredType)
    ```

-   根据 name 获取 Bean，并进行类型转换

    ```java
    <T> T getBean(String name, Class<T> requiredType)
    ```

```java
@SpringBootTest
class SpringbootWebConfig2ApplicationTests {
    @Autowired
    private ApplicationContext applicationContext; // IOC容器对象
  
    // 获取bean对象
    @Test
    public void testGetBean(){
        // 根据bean的名称获取
        DeptController bean1 = (DeptController) applicationContext.getBean("deptController");
        System.out.println(bean1);
      
        // 根据bean的类型获取
        DeptController bean2 = applicationContext.getBean(DeptController.class);
        System.out.println(bean2);
      
        // 根据bean的名称 及 类型获取
        DeptController bean3 = applicationContext.getBean("deptController", DeptController.class);
        System.out.println(bean3);
    }
}
```

#### Bean 作用域

默认情况下，IOC 中的 Bean 对象是单例，即 IOC 容器中只有是一个实例

Spring 中支持五种作用域，可使用 `@Scope` 注解来进行配置作用域

| 作用域（Scope） | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| `singleton`     | 容器中该名称的 Bean 始终只有一个实例（单例），**默认作用域** |
| `prototype`     | 每次从容器中获取 Bean 时都会创建一个新的实例（非单例）       |
| `request`       | 每个 HTTP 请求创建一个 Bean 实例（仅用于 Web 环境）          |
| `session`       | 每个 HTTP 会话创建一个 Bean 实例（仅用于 Web 环境）          |
| `application`   | 整个 Web 应用范围内共享一个 Bean 实例（对应 ServletContext 级别，Web 环境） |

#### 第三方 Bean

如果要管理的 Bean 对象来自于第三方（不是自定义的），是无法用 `@Component` 及衍生注解声明Bean的，就需要用到 `@Bean` 注解

实现方式

1.   在启动类中添加 `@Bean` 标识的方法

     ```java
     @SpringBootApplication
     public class SpringbootWebConfig2Application {
         public static void main(String[] args) {
             SpringApplication.run(SpringbootWebConfig2Application.class, args);
         }
       
         //声明第三方bean
         @Bean //将当前方法的返回值对象交给IOC容器管理, 成为IOC容器bean
         public SAXReader saxReader(){
             return new SAXReader();
         }
     }
     ```

2.   在配置类中添加 `@Bean` 标识的方法

     ```java
     @Configuration //配置类 (在配置类当中对第三方bean进行集中的配置管理)
     public class CommonConfig {
         // 声明第三方 Bean
       	// 将当前方法的返回值对象交给 IOC 容器管理, 成为 IOC 容器 Bean
         // 通过@Bean注解的name/value属性指定bean名称, 如果未指定, 默认是方法名
       	// 第三方 Bean 需要依赖其它 Bean 对象，直接在 Bean 定义方法中设置形参即可，容器会根据类型自动装配
         @Bean
         public SAXReader reader(DeptService deptService){
             System.out.println(deptService);
             return new SAXReader();
         }
     }
     ```

### Spring Boot 原理

#### 起步依赖

使用 Spring 框架进行 Web 程序的开发，此时我们就需要引入 Web 程序开发所需要的一些依赖

使用 Spring Boot 只需要引入一个起步依赖即可

起步依赖的原理就是 Maven 的依赖传递

#### 自动配置

当 Spring 容器启动后，一些配置类、Bean 对象就自动存入到了 IOC 容器中，不需要我们手动去声明，从而简化了开发，省去了繁琐的配置操作

默认情况下，`@SpringBootApplication` 注解只会扫描启动类所在包及其子包，第三方依赖中的 Bean 对象无法加载到 IOC 容器中。为了这个问题，有两种方案

1.   使用 `@ComponentScan` 注解，声明扫描范围

     ```java
     @ComponentScan({"com.itheima","com.example"}) //指定要扫描的包
     ```

2.   使用 `@Import` 导入普通类，配置类和 ImportSelector 接口实现类

     ```java
     @Import(TokenParser.class) // 普通类
     @Import(HeaderConfig.class) // 配置类
     @Import(MyImportSelector.class) // 接口实现类
     ```

     ```java
     public class MyImportSelector implements ImportSelector {
         public String[] selectImports(AnnotationMetadata importingClassMetadata) {
             //返回值字符串数组（数组中封装了全限定名称的类）
             return new String[]{"com.example.HeaderConfig"};
         }
     }
     ```

3.   使用自定义 `@EnableXxxxx` 注解，在其中封装 `@Import` 注解

     ```java
     @Retention(RetentionPolicy.RUNTIME)
     @Target(ElementType.TYPE)
     @Import(MyImportSelector.class) // 指定要导入哪些bean对象或配置类
     public @interface EnableHeaderConfig {}
     ```

     ```java
     @EnableHeaderConfig // 使用第三方依赖提供的Enable开头的注解
     @SpringBootApplication
     public class SpringbootWebConfig2Application {
         public static void main(String[] args) {
             SpringApplication.run(SpringbootWebConfig2Application.class, args);
         }
     }
     ```

自动配置原理源码入口就是 `@SpringBootApplication` 注解，在这个注解中封装了3个注解，分别是

-   `@SpringBootConfiguration`：声明当前类是一个配置类
-   `@ComponentScan`：进行组件扫描（SpringBoot中默认扫描的是启动类所在的当前包及其子包）
-   `@EnableAutoConfiguration`：封装了@Import注解（Import注解中指定了一个ImportSelector接口的实现类）
    -   在实现类重写的 `selectImports()` 方法，读取当前项目下所有依赖jar包中META-INF/spring.factories、META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports两个文件里面定义的配置类（配置类中定义了`@Bean` 注解标识的方法）

#### `@Conditional` 注解

按照一定的条件进行判断，在满足给定条件后才会注册对应的 Bean 对象到 Spring 的 IOC 容器中，可用于方法和类

`@Conditional` 本身是一个父注解，派生出大量的子注解：

-   `@ConditionalOnClass`：判断环境中有对应字节码文件，才注册 Bean 到 IOC 容器。
-   `@ConditionalOnMissingBean`：判断环境中没有对应的 Bean（类型或名称），才注册 Bean 到 IOC 容器
-   `@ConditionalOnProperty`：判断配置文件中有对应属性和值，才注册 Bean 到 IOC 容器

