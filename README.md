# Email template and component based on thymeleaf

## Intro

HTML邮件和普通的HTML网页不同，各个邮件客户端对html的标签、样式等支持均不相同。因此的邮件格式兼容性一直是个繁琐的事情。

> 了解更多[HTML Email的编写指南](http://www.ruanyifeng.com/blog/2013/06/html_email.html)

因此对于非EDM(例如简单的通知类邮件) 将所有邮件客户端都支持的HTML特性进行组件封装和构建模板，能极大减少处理邮件兼容性的时间。

*Features*

- 响应式布局
- 支持多个组件
- 无需关心兼容性
- 多种预置表格配色

## Usage

### 引入pom

需要引入thymeleaf。除此之外，因为thymeleaf本身不支持「模板」的概念，因此还需引入第三方实现的dialect来支持「模板」

```xml
<!--https://ultraq.github.io/thymeleaf-layout-dialect/-->
<dependency>
    <groupId>nz.net.ultraq.thymeleaf</groupId>
    <artifactId>thymeleaf-layout-dialect</artifactId>
    <version>3.2.0</version>
</dependency>
```

### 引入模板和组件

邮件的模板和组件组件在`src/`目录下，将所有内容复制到你项目的`resources/templates/`目录下，以`base.html`为基础修改邮件内容

### 邮件编写须知

1. 整个邮件可以理解为一个大的table，因此只能出现和table相关的标签`tr`, `td`, `th`等，还有一些功能标签`img`, `a`。绝对不允许出现`div`等标签，一切从简。
2. 不要使用背景图片，因为邮件默认不会加载外部图片，或者可以满足即便未展示背景图片也不影响正文的展示。

### 组件说明

#### 组件的层级规范

组件必须按照规定的层级使用

```yaml
# 其中body和tr是html标签，而带括号的为提供的组件
body:
  - <space>
  - <row>:
      - <space>
      - <table>
      - tr
  - <footer>
```

#### <row>

`<row>`可以理解为html中的`<p>`，body中划分成多个`<row>`区域块

```html
<row layout:replace="~{components/row}"> <th:block layout:fragment="row_content">
    content
</th:block> </row>
```

#### <space>

`<space>`就是空行，用于撑开`<row>`之间的间隔

单行

```html
<space layout:replace="~{components/space}"></space>
```

多行

```html
<space layout:replace="~{components/space :: space(line_num=2)}"></space>
```

#### <table>

`<table>`就是对html table的封装，支持了一些预设样式['table-style-1', 'table-style-2']（也可以在`components/mail_template.html`里自建）

```html
<table layout:replace="~{components/table :: table(width='100%', style='table-style-2')}">
    <tr layout:fragment="header">
        <th>字段1</th>
    </tr>

    <th:block layout:fragment="datas">
    <tr>
        <td>值1</td>
    </tr>
    <tr>
        <td>值2</td>
    </tr>
    </th:block>
</table>
```

#### <footer>

通用页脚，也可举一反三增加通用icon，通用页头

```html
<footer layout:replace="~{components/footer}"></footer>
```

### 全局变量说明

支持多种全局变量，可以在`base.html`搜索`G_`开头的变量，其中`G_debug_mode`为开启调试，会展示各个box的边框用于查看布局。

## Reference

*编写指南*

- https://emailframe.work/
- https://blog.csdn.net/lovearforever/article/details/80854731
- https://juejin.cn/post/6875633621382725645

*模板*

- https://github.com/leemunroe/responsive-html-email-template
- http://htmlemailboilerplate.com/

*测试和校验*

- https://htmlemail.io/inline
- https://validator.w3.org/#validate_by_input

*背景图片*

- https://www.emailonacid.com/blog/article/email-development/html-background-images-in-email/#setting-up-your-tables
