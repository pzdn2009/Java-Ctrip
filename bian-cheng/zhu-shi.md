# 注释

## JAVADOC TAGS

| Tag | Introduced in JDK/SDK | 解释 |
| :--- | :--- | :--- |
| **@author** | 1.0 | 作者 |
| {@code} | 1.5 | 等价于Code包裹的&lt;code&gt;{@literal}&lt;/code&gt;，直接写代码 |
| {@docRoot} | 1.3 | 文档的相对路径Root |
| @deprecated | 1.0 | Deprecated警告 |
| **@exception** | 1.0 | @throw的同义词 |
| {@inheritDoc} | 1.4 |  |
| {@link} | 1.2 | 内链 |
| {@linkplain} | 1.4 |  |
| {@literal} | 1.5 | 纯文本 |
| **@param** | 1.0 | 参数 |
| **@return** | 1.0 | 返回值 |
| @see | 1.0 | See Also |
| @serial | 1.2 |  |
| @serialData | 1.2 |  |
| @serialField | 1.2 |  |
| @since | 1.1 | 版本 |
| @throws | 1.2 | 异常 |
| {@value} | 1.4 |  |
| **@version** | 1.0 | 版本 |

## 位置

### 基础注释

* 类、接口
* 构造函数
* 方法
* 全局变量
* 字段/属性

备注：简单的代码做简单注释，注释内容不大于10个字即可，另外，持久化对象或VO对象的getter、setter方法不需加注释。

### 特殊必加注释

* 典型算法必须有注释。
* 在代码不明晰处必须有注释。
* 在代码修改处加上修改标识的注释。
* 在循环和逻辑分支组成的代码中加注释。
* 为他人提供的接口必须加详细注释。

## IDEA

使用 /\*\* + Enter则可以快速添加。

