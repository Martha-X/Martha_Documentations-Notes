# IDEA的常用设置

## 设置主题

> Settings→Appearance&Behavior→Appearance
>
> + Theme

## 鼠标滚轮调节字体大小

> Settings→Editor→General
>
> + 勾选Change font size with Ctrl+Mouse Wheel

## 设置自动导包功能

> Settings→Editor→General→Auto Import
>
> + 勾选Add unambiguous imports on the fly
> + 勾选Optimize imports on the fly

## 显示行号以及方法之间的分隔符

> Settings→Editor→General→Appearance
>
> + 勾选show line numbers
> + 勾选show method separators

## 设置多行显示tabs的操作

> Settings→Editor→General→Editor Tabs
>
> + 勾选Multiple rows

## 设置注释字体的颜色

> Settings→Editor→Color Scheme→Language Defaults

## 修改字符集UTF-8

> Settings→Editor→File Encodings
>
> + UTF-8
> + UTF-8
> + UTF-8

## 设置自动编译

> Settings→Build，Execution，Deployment→Complier
>
> + 勾选Build Project Automatically
> + 勾选Complie independent modules in parallel
>
> Ctrl + Alt  + Shift + /,点击Registry
>
> + 勾选compiler.automake.allow.when.app.running
>
> Run→Edit Configurations...
>
> + Modify Options，Add  Before Launch Task
> + 把锤子build叉掉就可以了

## Java模板自定义

> File→Settings→Editor→Live Templates
>
> + 点击 + 创建模板组
> + 详情见MarthaTemplate

**编译后的字节码文件路径查**

> File→Project Structure→Project compiler output   复制路径以打开

**反编译字节码文件**

> 将.class文件复制进IDEA projects 文件夹目录下，打开IDEA即可

**Java类注释模板设置**

> File→Settings→Editor→File and Code Templates→Class将以下复制到类public class ${NAME} {}上方即可

```java
/**
 * @Description: 
 * @ClassName: ${NAME}
 * @Author: ${USER}
 * @Date: ${DATE} ${TIME} 
 * @Version: 1.0
 */
```

**Java方法注释模板设置**

> File→Settings→Editor→Live Templates点击右边 + 号，选择Live Template，在Abbreviation 填入/**，Template Text填下面的

```java
/** 
 * @Description $index$
 * @Param $param$
 * @Return $return$
 * @Author $user$
 * @Date $date$ $time$
 */
```

> 填完后，选择应用场景，点击define（没定义过是define）或change，选中Java的全部场景，ok
>
> 最后点击Edit Variables，设置各参数获取的方法【如下】，设置完后  ok  apply即可

| Name   | Expression         |
| ------ | ------------------ |
| user   | user()             |
| date   | date()             |
| time   | time()             |
| param  | methodParameters() |
| return | methodReturnType() |

# 快捷键

》 以下快捷键为keymap = Windows，但有些自行修改过，需要导入之前导出的Settings

|   常用快捷键   |          作用          |         keymap解释          |
| :------------: | :--------------------: | :-------------------------: |
|   Ctrl Click   |    跳到该方法实现体    | Go to Declaration or Usages |
| Ctrl Alt Click | 跳到该方法声明或调用处 |   Go to implementation(s)   |
|     Ctrl D     |      删除所选中行      |         Delete Line         |
|  Ctrl Alt  ↓   |    向下复制所选中行    | Duplicate Line Or Selection |
|     Ctrl Z     |        撤销操作        |            Undo             |
|     Ctrl Y     |        重做操作        |            Redo             |

刚开始使用IDEA时一直都不熟悉，利用空闲的时间整理了一下常用的IDEA快捷键。

1、Ctrl 快捷键 介绍

Ctrl + F 在当前文件进行文本查找

Ctrl + R 在当前文件进行文本替换

Ctrl + Z 撤销

Ctrl + Y 删除[光标](https://so.csdn.net/so/search?q=光标&spm=1001.2101.3001.7020)所在行 或 删除选中的行

Ctrl + X 剪切光标所在行 或 剪切选择内容

Ctrl + C 复制光标所在行 或 复制选择内容

Ctrl + D 复制光标所在行 或 复制选择内容，并把复制内容插入光标位置下面

**Ctrl + W 递进式选择代码块。可选中光标所在的单词或段落，连续按会在原有选中的基础上再扩展选中范围**

Ctrl + E 显示最近打开的文件记录列表

**Ctrl + N 根据输入的 类名 查找类文件**

Ctrl + G 在当前文件跳转到指定行处

Ctrl + J 插入自定义动态代码模板

Ctrl + P 方法参数提示显示

Ctrl + Q 光标所在的变量 / 类名 / 方法名等上面（也可以在提示补充的时候按），显示文档内容

Ctrl + U 前往当前光标所在的方法的父类的方法 / 接口定义

Ctrl + B 进入光标所在的方法/变量的接口或是定义出，等效于 Ctrl + 左键单击

Ctrl + K 版本控制提交项目，需要此项目有加入到版本控制才可用

Ctrl + T 版本控制更新项目，需要此项目有加入到版本控制才可用

Ctrl + H 显示当前类的层次结构

Ctrl + O 选择可重写的方法

Ctrl + I 选择可继承的方法

**Ctrl + + 展开代码**

**Ctrl + - 折叠代码**

Ctrl + / 注释光标所在行代码，会根据当前不同文件类型使用不同的注释符号

**Ctrl + [ 移动光标到当前所在代码的花括号开始位置**

**Ctrl + ] 移动光标到当前所在代码的花括号结束位置**

Ctrl + F1 在光标所在的错误代码出显示错误信息

Ctrl + F3 调转到所选中的词的下一个引用位置

Ctrl + F4 关闭当前编辑文件

Ctrl + F8 在 Debug 模式下，设置光标当前行为断点，如果当前已经是断点则去掉断点

Ctrl + F9 执行 Make Project 操作

Ctrl + F11 选中文件 / 文件夹，使用助记符设定 / 取消书签

Ctrl + F12 弹出当前文件结构层，可以在弹出的层上直接输入，进行筛选

Ctrl + Tab 编辑窗口切换，如果在切换的过程又加按上delete，则是关闭对应选中的窗口

Ctrl + Enter 智能分隔行

Ctrl + End 跳到文件尾

Ctrl + Home 跳到文件头

Ctrl + Space 基础代码补全，默认在 Windows 系统上被输入法占用，需要进行修改

Ctrl + Delete 删除光标后面的单词或是中文句

Ctrl + BackSpace 删除光标前面的单词或是中文句

Ctrl + 1,2,3…9 定位到对应数值的书签位置

Ctrl + 左键单击 在打开的文件标题上，弹出该文件路径

Ctrl + 光标定位 按 Ctrl 不要松开，会显示光标所在的类信息摘要

Ctrl + 左方向键 光标跳转到当前单词 / 中文句的左侧开头位置

Ctrl + 右方向键 光标跳转到当前单词 / 中文句的右侧开头位置

Ctrl + 前方向键 等效于鼠标滚轮向前效果

Ctrl + 后方向键 等效于鼠标滚轮向后效果

2、Alt 快捷键 介绍

Alt + ` 显示版本控制常用操作菜单弹出层

Alt + Q 弹出一个提示，显示当前类的声明 / 上下文信息

Alt + F1 显示当前文件选择目标弹出层，弹出层中有很多目标可以进行选择

Alt + F2 对于前面页面，显示各类浏览器打开目标选择弹出层

Alt + F3 选中文本，逐个往下查找相同文本，并高亮显示

Alt + F7 查找光标所在的方法 / 变量 / 类被调用的地方

Alt + F8 在 Debug 的状态下，选中对象，弹出可输入计算表达式调试框，查看该输入内容的调试结果

Alt + Home 定位 / 显示到当前文件的 Navigation Bar

Alt + Enter IntelliJ IDEA 根据光标所在问题，提供快速修复选择，光标放在的位置不同提示的结果也不同

Alt + Insert 代码自动生成，如生成对象的 set / get 方法，构造函数，toString() 等

**Alt + 左方向键 按左方向切换当前已打开的文件视图**

**Alt + 右方向键 按右方向切换当前已打开的文件视图**

**Alt + 前方向键 当前光标跳转到当前文件的前一个方法名位置**

Alt + 后方向键 当前光标跳转到当前文件的后一个方法名位置

**Alt + 1**,2,3…9 显示对应数值的选项卡，其中 1 是 Project **用得最多**

3、Shift 快捷键 介绍

Shift + F1 如果有外部文档可以连接外部文档

**Shift + F2 跳转到上一个高亮错误 或 警告位置**

Shift + F3 在查找模式下，查找匹配上一个

Shift + F4 对当前打开的文件，使用新Windows窗口打开，旧窗口保留

Shift + F6 对文件 / 文件夹 重命名

**Shift + F7 在 Debug 模式下，智能步入。断点所在行上有多个方法调用，会弹出进入哪个方法**

Shift + F8 在 Debug 模式下，跳出，表现出来的效果跟 F9 一样

Shift + F9 等效于点击工具栏的 Debug 按钮

Shift + F10 等效于点击工具栏的 Run 按钮

Shift + F11 弹出书签显示层

Shift + Tab 取消缩进

Shift + ESC 隐藏当前 或 最后一个激活的工具窗口

Shift + End 选中光标到当前行尾位置

Shift + Home 选中光标到当前行头位置

Shift + Enter 开始新一行。光标所在行下空出一行，光标定位到新行位置

Shift + 左键单击 在打开的文件名上按此快捷键，可以关闭当前打开文件

Shift + 滚轮前后滚动 当前文件的横向滚动轴滚动

4、Ctrl + Alt 快捷键 介绍

**Ctrl + Alt + L 格式化代码，可以对当前文件和整个包目录使用**

Ctrl + Alt + O 优化导入的类，可以对当前文件和整个包目录使用

**Ctrl + Alt + I 光标所在行 或 选中部分进行自动代码缩进，有点类似格式化**

Ctrl + Alt + T 对选中的代码弹出环绕选项弹出层

Ctrl + Alt + J 弹出模板选择窗口，讲选定的代码加入动态模板中

**Ctrl + Alt + H 调用层次**

Ctrl + Alt + B 在某个调用的方法名上使用会跳到具体的实现处，可以跳过接口

Ctrl + Alt + V 快速引进变量

Ctrl + Alt + Y 同步、刷新

Ctrl + Alt + S 打开 IntelliJ IDEA 系统设置

Ctrl + Alt + F7 显示使用的地方。寻找被该类或是变量被调用的地方，用弹出框的方式找出来

Ctrl + Alt + F11 切换全屏模式

Ctrl + Alt + Enter 光标所在行上空出一行，光标定位到新行

Ctrl + Alt + Home 弹出跟当前文件有关联的文件弹出层

Ctrl + Alt + Space 类名自动完成

**Ctrl + Alt + 左方向键 退回到上一个操作的地方**

**Ctrl + Alt + 右方向键 前进到上一个操作的地方**

Ctrl + Alt + 前方向键 在查找模式下，跳到上个查找的文件

Ctrl + Alt + 后方向键 在查找模式下，跳到下个查找的文件

5、Ctrl + Shift 快捷键 介绍

Ctrl + Shift + F 根据输入内容查找整个项目 或 指定目录内文件

Ctrl + Shift + R 根据输入内容替换对应内容，范围为整个项目 或 指定目录内文件

Ctrl + Shift + J 自动将下一行合并到当前行末尾

**Ctrl + Shift + Z 取消撤销**

Ctrl + Shift + W 递进式取消选择代码块。可选中光标所在的单词或段落，连续按会在原有选中的基础上再扩展取消选中范围

Ctrl + Shift + N 通过文件名定位 / 打开文件 / 目录，打开目录需要在输入的内容后面多加一个正斜杠

Ctrl + Shift + U 对选中的代码进行大 / 小写轮流转换

Ctrl + Shift + T 对当前类生成单元测试类，如果已经存在的单元测试类则可以进行选择

Ctrl + Shift + C 复制当前文件磁盘路径到剪贴板（我的是注释代码了）

Ctrl + Shift + V 弹出缓存的最近拷贝的内容管理器弹出层

Ctrl + Shift + E 显示最近修改的文件列表的弹出层

Ctrl + Shift + H 显示方法层次结构

Ctrl + Shift + B 跳转到类型声明处

Ctrl + Shift + I 快速查看光标所在的方法 或 类的定义

Ctrl + Shift + A 查找动作 / 设置

Ctrl + Shift + / 代码块注释

Ctrl + Shift + [ 选中从光标所在位置到它的顶部中括号位置

Ctrl + Shift + ] 选中从光标所在位置到它的底部中括号位置

**Ctrl + Shift + + 展开所有代码**

**Ctrl + Shift + - 折叠所有代码**

Ctrl + Shift + F7 高亮显示所有该选中文本，按Esc高亮消失

Ctrl + Shift + F8 在 Debug 模式下，指定断点进入条件

Ctrl + Shift + F9 编译选中的文件 / 包 / Module

**Ctrl + Shift + F12 编辑器最大化**

Ctrl + Shift + Space 智能代码提示

Ctrl + Shift + Enter 自动结束代码，行末自动添加分号

Ctrl + Shift + Backspace 退回到上次修改的地方

Ctrl + Shift + 1,2,3…9 快速添加指定数值的书签

Ctrl + Shift + 左方向键 在代码文件上，光标跳转到当前单词 / 中文句的左侧开头位置，同时选中该单词 / 中文句

Ctrl + Shift + 右方向键 在代码文件上，光标跳转到当前单词 / 中文句的右侧开头位置，同时选中该单词 / 中文句

Ctrl + Shift + 左方向键 在光标焦点是在工具选项卡上，缩小选项卡区域

Ctrl + Shift + 右方向键 在光标焦点是在工具选项卡上，扩大选项卡区域

Ctrl + Shift + 前方向键 光标放在方法名上，将方法移动到上一个方法前面，调整方法排序

Ctrl + Shift + 后方向键 光标放在方法名上，将方法移动到下一个方法前面，调整方法排序

6、Alt + Shift 快捷键 介绍

Alt + Shift + N 选择 / 添加 task

Alt + Shift + F 显示添加到收藏夹弹出层

Alt + Shift + C 查看最近操作项目的变化情况列表

Alt + Shift + F 添加到收藏夹

Alt + Shift + I 查看项目当前文件

Alt + Shift + F7 在 Debug 模式下，下一步，进入当前方法体内，如果方法体还有方法，则会进入该内嵌的方法中，依此循环进入

Alt + Shift + F9 弹出 Debug 的可选择菜单

Alt + Shift + F10 弹出 Run 的可选择菜单

Alt + Shift + 左键双击 选择被双击的单词 / 中文句，按住不放，可以同时选择其他单词 / 中文句

Alt + Shift + 前方向键 移动光标所在行向上移动

Alt + Shift + 后方向键 移动光标所在行向下移动

7、Ctrl + Shift + Alt 快捷键 介绍

Ctrl + Shift + Alt + V 无格式黏贴

Ctrl + Shift + Alt + N 前往指定的变量 / 方法

Ctrl + Shift + Alt + S 打开当前项目设置

Ctrl + Shift + Alt + C 复制参考信息

8、其他快捷键 介绍

F2 跳转到下一个高亮错误 或 警告位置

F3 在查找模式下，定位到下一个匹配处

F4 编辑源

F7 在 Debug 模式下，进入下一步，如果当前行断点是一个方法，则进入当前方法体内，如果该方法体还有方法，则不会进入该内嵌的方法中

F8 在 Debug 模式下，进入下一步，如果当前行断点是一个方法，则不进入当前方法体内

F9 在 Debug 模式下，恢复程序运行，但是如果该断点下面代码还有断点则停在下一个断点上

F11 添加书签

F12 回到前一个工具窗口

Tab 缩进

# IDEA导出/导入Settings

IDEA导入/导出live templates或者其他设置

### 导出

在菜单栏选择 File | Manage IDE Settings | Export Settings

在打开的导出弹窗中，选择需要导出的项目，如果我们只需要导出Live templates，那就只选择Live templates即可，然后选择一个需要导出的位置并设置一个存储的文件名（默认是settings.zip）

点击OK进行导出，导出的文件可以导入到其他IDEA中进行使用

### 导入

在菜单栏选择 File | Manage IDE Settings | Import Settings

选择之前导出的配置文件，点击OK

在弹出的窗口中选择需要导入的项目，例如Live templates 然后点击OK

重启IDEA使新配置生效