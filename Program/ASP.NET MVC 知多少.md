https://www.jianshu.com/p/5f6156cacc76

# 一、 基本概念

## MVC

MVC是一种**软件设计模式**，强迫关注分离，域模型和控制器逻辑与UI是松耦合关系。
![image](https://upload-images.jianshu.io/upload_images/2799767-8b86b0c7bf49d728.png)

* **Model**：模型代表一系列类用来描述业务逻辑，比如业务模型以及数据访问操作，或者数据模型。同时也定义了对数据如何进行处理的业务规则。
* **View**：视图代表的是UI部分。它主要的职责是展现从controller接受到数据或模型。
* **Controller**：控制器职责在于处理传入的请求。它接受用户通过视图的输入，然后对用户输入的数据模型进行处理，最终通过视图将结果渲染给用户。通常来讲，控制器在视图和模型之间扮演着桥梁（协调者）的角色。

## 领域驱动设计开发

Domain-Driven Design (DDD)定义了一系列的准则和模式从而时开发者针对不同的领域采取合适的设计方案以开发出优美的系统。DDD既不是一项技术也不是一套方法论。DDD主要以下五大部分组成：
* **Entity**（实体）：具有唯一标志的对象，比如用户。
* **Value Object**（值对象）：不具有唯一标志的对象，比如枚举。一个值对象也可以是一个实体视情况而定。
* **Aggregate**（聚合）： 它通过定义对象之间清晰的所属关系和边界来实现领域模型的内聚，并避免了错综复杂的难以维护的对象关系网的形成。聚合定义了一组具有内聚关系的相关对象的集合，我们把聚合看作是一个修改数据的单元。
* **Service**（服务）：服务是在应用程序中用来处理业务逻辑的。
* **Repository**（仓储）：仓储的作用是数据的存储读取，即封装数据持久化框架。它不关心使用何种数据库。仓储也不是我们常说的Data Access Layer（数据操作层），但是为了安全因素，仓储会引用一个位置进行存储。仓储的主要职责是处理聚合的和持久化相关的任务（ADD、UPDATE、DELETE、GET）

## MVP

![image](https://upload-images.jianshu.io/upload_images/2799767-1b5385173f41b78f.png?imageMogr2/auto-orient/)

MVP 与 MVC 相似，只是将 MVC 的 Controller 替换成 Presenter。
* **Presenter**：职责在于处理视图上的 UI 行为事件。通过 View 接收用户输入，然后通过 Model 处理用户数据，然后将结果传回 View。不像 View和 Controller，View 和 Presenter 之间完全解耦，是通过接口进行交互。同时它也不处理和接收传入的请求。

MVP 模式通常用在 Asp.NET Web Form、Windows Form 应用程序

MVP 模式的关键点：
1. 用户与 View 直接交互.
2. View 与 Presenter 是1对1关系，一个 View 对应一个 Presenter
3. View 与 Model 不发生联系，都通过 Presenter 传递。
4. 各部分之间的通信，都是双向的。
5. View 非常薄，不部署任何业务逻辑，称为"被动视图"（Passive View），即没有任何主动性，而 Presenter 非常厚，所有逻辑都部署在那里。

## MVVM

![image](https://upload-images.jianshu.io/upload_images/2799767-8d84c29c3d326964.png?imageMogr2/auto-orient/)

MVVM 是指 Model-View-ViewModel。MVVM 支持在 View 与 ViewModel 之间进行双向数据绑定。通过视图模型的状态就能够自动的传播改动到 View。通常来说，ViewModel 是通过观察者模式将 ViewModel 的改动通知到 View。

* **View Model**：ViewModel 的职责是暴露方法、命令以及属性去维护 View 的状态，操纵 Model 作为 View 执行的结果，以及触发 View 上自身的事件。

MVVM 模式的关键点：
1. 用户与 View 直接交互。
2. View 与 ViewModel 是1对多关系，一个 View 可以对应多个 ViewModel。
3. View 保存一个对 ViewModel 的引用，但是 ViewModel 对于 View 一无所知。
4. View 和 ViewModel 之间的数据绑定是双向的。

Angular 就是使用这种模式。

## ASP.NET的MVC模式

![image](https://upload-images.jianshu.io/upload_images/2799767-4950a08a9d9e0aba.png?imageMogr2/auto-orient/)

### Model

ASP.NET MVC 中的Model可以分解成几个不同的层：

* **Objects or ViewModel or Presentation Layer**：这一层包含的简单对象或复杂的对象用来进行特定的强类型 View 的展示。这些对象用来从 Controller 传递数据到强类型的 View，反之亦然。这些对象对应的类通过数据注解指定定的验证规则。通常，这些类拥有你想要展示到对应 View/Page 的属性。
* **Business Layer**：主要用来实现业务逻辑和数据验证。同时通过数据访问层（DAL）将数据持久化到数据库。这一层被Controller直接调用去处理用户输入并将结果返回到View
* **Data Access Layer**：提供对象去访问和操作数据库。通常这一层主要使用一些ORM框架

### View
View就是展示从Controller传递的数据。将Model进行转换以在View的UI上进行展示。

### Controller
响应Http请求并根据传入的请求内容决定由哪个具体的Action去处理。它通过View接收用户输入，然后通过Model的帮助去处理用户数据并将结果返回给View.

## ASP.NET MVC 相对于 Web Forms 的优势

* **关注分离**：MVC 设计模式将 Asp.net MVC 应用程序分成三个部分，View、Controller、Model。更容易地去处理程序的复杂性问题。
* **测试驱动开发**：更好的支持测试驱动开发。
* **扩展性好**：MVC 支持可插拔、可扩展。因此更容易进行替换和自定义。
* **对应用程序行为的完全控制**：MVC 不使用 View State，且不依赖于 Server。促使程序员可以更好的控制应用的行为同时减少对 Server 请求的带宽。
* **强大 ASP.NET 特性支持**：MVC 框架是基于 Asp.net 设计的，因此可以使用ASP.NET 包含大部分功能，比如认证、授权，权限和角色控制、缓存、Session 等。
* **路由机制**：MVC 框架提供了一个强大的 URL 路由机制，以便我们构建易理解易搜索的 URLS。这个路由机制提高了应用程序的可访问性，同时利于搜索引擎优化。

## 三层架构与MVC模式

![image](https://upload-images.jianshu.io/upload_images/2799767-d2a97e07c960cabe.png?imageMogr2/auto-orient/)

三层架构分为：展现层、业务逻辑层、数据访问层，与 MVC 模式并不冲突，MVC 模式一般用于展现层

# 二、 MVC的管道及路由机制

## ViewModel

ViewModel 是一个包含将在强类型视图中展示的字段的类。它是用来将数据从 Controller 传递到强类型视图中。

* ViewModel 包含在视图中呈现的字段。(LabelFor, EditorFor, DisplayFor helpers)
* ViewModel 可以通过数据注解指定特定的验证规则。
* ViewModel 可以包含多个来自不同数据模型或数据源的实体或对象。

## ASP.NET MVC 管道(pipeline)

![image](https://upload-images.jianshu.io/upload_images/2799767-990fcc7367daeffa.png?imageMogr2/auto-orient/)

1. **Routing（路由）**：路由是管道的第一步，是一种模式匹配系统，去路由表中注册的 Url 中匹配传入的请求。在代码中主要是 `System.Web.Routing.UrlRoutingModule` 在做匹配的工作，路由表对应的是 `System.Web.Routing.RouteTable`。
2. **Controller Initialization（初始化控制器）**：MvcHandler 使用 `ProcessRequest` 方法开始对 ASP.NET MVC 管道进行实时处理。这个方法使用工厂类 `IControllerFactory` 的实例（默认是`System.Web.Mvc.DefaultControllerFactory`）去创建对应的 Controller。
3. **Action Execution （Action执行）**：该环节按以下顺序执行:

    * Controller 通过传递选择的 Action 方法详情调用它自己的 `InvokeAction()` 方法。这一步是由 `IActionInvoker` 处理。

    * **Model Binder（模型绑定器，默认是`System.Web.Mvc.DefaultModelBinder`）** 取回传入的 Http 请求的数据，然后进行数据转换，数据验证（比如 required、数据格式等）。同时还需要将数据映射到 Action 方法对应的参数上。

    * **Authentication Filter（认证过滤器）** 是在 ASP.NET MVC5 中引入的，它先于Authorization Filter（授权过滤器）执行。它主要用来对用户认证。认证过滤器处理请求中的用户凭证并返回相应的主体。在 ASP.NET MVC5 之前，使用 Authorization Filter （授权过滤器）对用户进行认证和授权。 Authenticate Attribute（认证特性）默认是被用来进行认证. 可以通过实现 `IAuthenticationFilter` 接口来创建自定义的 Authentication Filter（认证过滤器）

    * **Authorization Filter（授权过滤器）** 用来对已认证的用户执行授权操作。例如。基于角色的授权。Authorize Attribute（授权特性默认用来执行授权操作）。可以通过实现 `IAuthorizationFilter` 接口来创建自定义的 Authentication Filter（授权过滤器）。

    * **Action Filters （Action过滤器）**：`IActionFilter` 接口提供了两个方法  `OnActionExecuting`、`OnActionExecuted` 分别在 Action 之前和之后执行。通过实现 `IActionFilter` 接口来自定义 Action 过滤器。

    * Action 执行后, 通过 Model(Business Model or Data Model) 去处理用户输入并准备对应的 Action Result。

4. **Result Execution （返回执行结果阶段）**：该阶段主要包含以下步骤:

    * **Result Filters（结果过滤器）**：`IResultFilter` 接口提供两个方法  `OnResultExecuting` 、`OnResultExecuted` 分别对应在 ActionResult 之前和之后执行。可以通过实现 `IResultFilter` 接口来自定义结果过滤器。
    * **Action Result** 是 BLL 或者 DAL 对用户输入执行相应的操作后的返回结果。Action Result 的类型可以是 `ViewResult, PartialViewResult, RedirectToRouteResult, RedirectResult, ContentResult, JsonResult, FileResult, EmptyResult`。这些返回类型可以分为两类，即 ViewResult 类型和 NonViewResult 类型。ViewResult 类型主要用于返回并渲染html页面到浏览器。NonViewResult 仅仅返回数据，比如文本、二进制、json 格式数据。

5. **View Initialization and Rendering （视图初始化及渲染）**：实际是第4阶段的一部分，要点：
    * **ViewResult 类型**，比如 View、Partial View 都是实现了 `System.Web.Mvc.IView` 接口并由相应的视图引擎进行渲染。
    * 这一过程主要由视图引擎的 `System.Web.Mvc.IViewEngine` 接口负责。默认 ASP.NET MVC 提供了 WebForm、Razor 两种视图引擎。可以通过实现 `IViewEngine` 创建自定义的视图引擎并注册自定义视图引擎到 ASP.NET MVC 应用程序。
    * **Html Helpers** 主要用来创建 HTML 输入控件，基于路由创建链接，创建Ajax 表带等等。HtmlHelpers 是 HtmlHelper的扩展类并可以很好的进行进一步扩展。 在复杂的情形中，可以渲染一个有前端验证机制的JavaScript或jQuery验证。

## 路由机制

路由是一种模式匹配系统，用来监视传入的请求并决定如何处理请求。在运行时，路由引擎使用**路由表**去匹配传入的请求的 Url，根据路由表定义的 Url 格式与传入的Url格式进行匹配。可以在 `Application_Start` 事件中注册一个或多个 Url 格式到路由表中。

当路由引擎在路由表中找到一个与传入的 Url 请求匹配的路由记录，路由引擎会转发请求到对应的 Controller、Action 中。如果没有匹配的记录，则返回 404

当 MVC 应用程序第一次启动时，global.asax 类中的 `Application_Start()` 方法调用 `RegisterRoutes()` 方法。`RegisterRoutes()` 方法负责创建了路由表
### 定义路由

在 App_Start 文件夹下的 `RegisterRoutes.RegisterRoutes` 方法中配置，如下：
```CSharp
public class RouteConfig{
    public static void RegisterRoutes(RouteCollection routes){
        routes.IgnoreRoute("{resource}.axd/{*pathInfo}");

        routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional },
            //还可以用正则表达式约束参数
            new { id = @"\d+" }
        );
    }
}
```
* 特殊的路由定义应该配置在最上面，因为路由表是从上往下匹配，一旦匹配到，就返回。
* 路由名称必须是唯一的

### 特性路由

ASP.NET MVC5 和 WEB API 2 支持一种新路由方式，叫做 **Attribute Routing（特性路由）**，用特性来定义路由，能够更好的控制URLs，支持直接在 Action 和 Controller 上定义路由，使用了特性路由的 Controller 和 Action 将不再适用全局路由。

#### 启用特性路由

在 App_Start 文件夹下的 `RegisterRoutes.RegisterRoutes` 方法中添加 `routes.MapMvcAttributeRoutes();` 即可，要添加在全局路由定义之前，否则无法匹配到。

#### 使用特性路由

特性路由可以用于 Controller 或 Action, 如下:
```CSharp
[RouteArea("Admin")]        //可选，Area
[RoutePrefix("MyHome")]     //可选，前缀，即 Controller
[Route("{action=Index}")]   //可配置默认值，这里不能用 controller 参数
public class HomeController : Controller{
    public ActionResult Index(){}   //route: /Admin/MyHome
    public ActionResult Login(){}   //route: /Admin/MyHome/Login

    [Route("Num")]
    public ActionResult GetNum(){} // route: /Admin/MyHome/Num, 如果没有控制器路由则是 /Num
    [Route("~/Tel")]
    public ActionResult GetTel(){} // route: /Tel
}
```

### 与 URL 重写的区别

路由和Url重写都可以用来定义出SEO友好型的URLS。但是它们的实现方式是十分不同的，主要区别在：
* **URL重写**注重将一个URL映射到另一个URL。 而**路由**注重将一个URL映射到一个资源。
* **URL重写**重写你的旧的URL到一个新的URL。而**路由**只是将URL映射到它对应的原始路由。

# 三、视图引擎与HtmlHelper

## ASP.NET MVC 重要命名空间

* **System.Web.Mvc**：支持 ASP.NET WEB 应用程序的 MVC 模式，主要包括：Controllers, Controller Factories, Action Results, Views, Patial Views, Model Binders
* **System.Web.Mvc.Ajax**：支持 Ajax 脚本以及 Ajax 选项设置
* **Syetem.Web.Mvc.Html**：帮忙渲染 HTML 控件，主要用来支撑 Forms, Input Controls, Links, Patial Views, Validation

## 视图引擎

视图引擎是 MVC 的子系统，拥有自身的语义标记，负责转换服务器模板为 HTML 标记并渲染呈现到浏览器。有 Web Forms(.aspx，旧) 和 Razor(.cshtml，MVC3 引入) 两种，还有 Spark，NHaml 等第三方的。

### 组成部分

* **ViewEngine**：实现自 `IViewEngine` 接口，负责定位视图模板的位置
* **View**：实现自 `IView` 接口，负责从当前上下文合并数据与模板并转换为输出的 HTML 标记
* **模板解析引擎 (Template Parsing Engine)**：解析模板和编译视图为可执行代码

### 自定义视图引擎

可以通过实现 `ViewEngine` 接口或继承 `VirtualPathProviderViewEngine` 抽象类来实现自定义视图引擎

```CSharp
public class CustomViewEngine : VirtualPathProviderViewEngine {
    public CustomViewEngine() {
        //定义视图和分部视图的本地路径
        this.ViewLocationFormats =
            new string[] { "~/Views/{1}/{0}.html", "~/Views/Shared/{0}.html" };
        this.PartialViewLocationFormats =
            new string[] { "~/Views/{1}/{0}.html", "~/Views/Shared/{0}.html" };
    }

    protected override IView CreatePartialView(ControllerContext controllerContext, string partialPath) {
        var physicalPath = controllerContext.HttpContext.Server.MapPath(partialPath);
        return new CustomView(physicalPath);
    }

    protected override IView CreateView(ControllerContext controllerContext, string viewPath, string masterPath) {
        var physicalpath = controllerContext.HttpContext.Server.MapPath(viewPath);
        return new CustomView(physicalpath);
    }
}

public class CustomView : IView {
    private string viewPhysicalPath;

    public CustomView(string ViewPhysicalPath) {
        this.viewPhysicalPath = ViewPhysicalPath;
    }

    public void Render(ViewContext viewContext, TextWriter writer) {
        //获取视图原始模板
        string rawContent = File.ReadAllText(this.viewPhysicalPath);
        //根据ViewData渲染模板
        string parsedContent = Parse(rawContent, viewContext.ViewData);
        writer.Write(parsedContent);
    }

    public string Parse(string content, ViewDataDictionary viewData) {
        return Regex.Replace(content, "\\{(.+)\\}", m => GetMatch(m, viewData));
    }

    public virtual string GetMatch(Match m, ViewDataDictionary viewData) {
        if (m.Success) {
            string key = m.Result("$1");
            if (viewData.ContainsKey(key)) {
                return viewData[key].ToString();
            }
        }
        return string.Empty;
    }
}
```

#### 注册自定义视图引擎

在 global.asax.cs 文件的 `Application_Start()` 方法注册自定义视图引擎，告诉 ASP.NET MVC 使用自定义视图引擎替换默认的视图引擎
```CSharp
protected void Application_Start(){
    ...
    ViewEngines.Engines.Add(new CustomViewEngine());
    ...
}
```

#### 删除默认视图引擎

```CSharp
protected void Application_Start(){
    //移除所有视图引擎包括 Webform 和 Razor
    ViewEngines.Engines.Clear();
}
```

### Razor 与 WebForm 引擎的区别

| Razor | WebForm |
|-------|---------|
| MVC3 后引入 | 最初的 MVC 版本就引入 |
| 位于 System.Web.Razor 命名空间 | 位于 System.Web.Mvc.WebFormViewEngine 命名空间 |
| 状态管理技术（View State、Session） | 没有自动的状态管理 |
| 基于文件路径的路由 | 基于路由的 Urls |
| 统一的文件后缀 .cshtml（C#） | 视图后缀为 .aspx，分部视图或编辑模板为 .ascx |
| View 与业务逻辑分离 | View 与业务逻辑紧耦合（.aspx，.aspx.cs） |
| @Html.ActionLink("SignUp", "SignUp") | <%: Html.ActionLink("SignUp", "SignUp") %> |
| 默认支持阻止 XSS 攻击 | 不支持 |
| 不能通过拖拽控件进行布局 | 可以 |
| 支持 TDD | 不支持 TDD |
* TDD：测试驱动开发

## HTML Helpers

主要有三种：
* **Inline Html Helpers**：通过 `@helper` 标签创建的帮助类，只能在同一个 View 中使用 
    ```CSharp
    @helper ListingItems(string [] items){
        <ol>
        @foreach(string item in items){
            <li>@item</li>
        }
        </ol>
    }

    @ListingItems(new string[]{"C", "C++", "C#"})
    ```
* **Built-In Html Helpers**：是针对 `HtmlHelper` 的扩展方法，主要分为三类：
    * **Standard Html Helpers**：用于渲染常见的 Html 元素：
  
| Razor | HTML |
|-------|------|
| `@Html.TextBox("tb1", "val")` | `<input id="tb1" name="tb1" type="text" value="val" />` |
| `@Html.Password("pw1", "val")` | `<input id="pw1" name="pw1" type="password" value="val" />` |
| `@Html.Hidden("hd1", "val")` | `<input id="hd1" name="hd1" type="hidden" value="val" />` |
| `@Html.CheckBox("cb1", false)` | `<input id="cb1" name="cb1" type="checkbox" value="true" />` <br/> `<input name="myCheckbox" type="hidden" value="false" />` ? |
| `@Html.RadioButton("rb1", "val", true)` | `<input id="rb1" name="rb1" type="radio" value="val" />` |
| `Html.TextArea("ta1", "val", 5, 15, null)` | `<textarea cols="15" rows="5" id="ta1" name="ta1">val</textarea>` |
| `@Html.DropDownList("ddl1", new SelectList( new []{"Male", "Female"} ))` | `<select id="ddl1" name="ddl1"><option>Male</option><option>Female</option></select>` |
| `@Html.ListBox("lb1", new MultiSelectList( new []{"Male", "Female"} ))` | `<select id="lb1" name="lb1" multiple="multiple"><option>Male</option><option>Female</option></select>` |

* 
  * **Strongly Typed Html Helpers**：基于 model 属性创建的强类型 Html 元素，主要通过 Lambda 表达式来创建。就是将上一种方法的 id/name 参数部分换成 Lambda 表达式， 返回 model 的某个属性，生成的 Html 的 id/name 是属性名称， value 是属性的值
  * **Templated Html Helpers**：自动根据 model 类的属性类型特性去呈现适当的 Html 元素。比如，model 的属性使用 `[DataType(DataType.Password)]` 特性，当使用模板Html帮助类时，自动呈现为密码类型的文本框
    * Display \ DisplayFor：根据指定的 model 属性和基于 model 属性的数据类型和元数据选择一个合适的html标签去渲染**只读状态的视图**。
    * Editor \ EditorFor：根据指定的 model 属性和基于 model 属性的数据类型和元数据选择一个合适的html标签去渲染**编辑状态的视图**。
* **Custom Html Helpers**：可以通过扩展 `HtmlHelper` 类或在工具类中创建静态方法来创建自定义 Html Helper
    ```CSharp
    public static class CustomHelper{
        public static MvcHtmlString SubmitButton(this HtmlHelper helper, string buttonText){
            string str = "<input type=\"submit\" value=\""+ buttonText +"\" />";
            return new MvcHtmlString(str);
        }

        public static MvcHtmlString TextBoxFor<TModel, TValue>(this HtmlHelper<TModel> htmlHelper, Expression<Func<TModel, TValue>>expression, bool isReadOnly){
            MvcHtmlString html = default(MvcHtmlString);
            if(isReadOnly){
                html = System.Web.Mvc.Html.InputExtension.TextBoxFor(helper, expression, new { @class = "readOnly", @readonly = "read-only" });
            }else{
                html = System.Web.Mvc.Html.InputExtensions.TextBoxFor(helper, expression);
            }
            return html;
        }
    }
    ```

## Url Helpers

Url Helpers 基于路由配置帮助我们去渲染 HTML 链接或生成 URL，如：
|Razor|HTML|
|-----|----|
| `@Url.Content("~/Files/xx.pdf")` | `/Files/xx.pdf` |
| `@Html.ActionLink("About Us", "About", "Home")` | `<a href="/Home/About">About Us</a>` |
| `@Html.ActionLink("About Me", "About", "Home", "http", "www.xxx.com", null, null, null)` | `<a href="http://www.xxx.com/Home/About">About Me</a>` |
| `@Url.Action("About", "Home")` | `/Home/About` |

## Validation Summary

**Validation Summary** 用来显示 ModelState 字典中所有验证错误信息（未经排序）。`@Html.ValidationSummary(arg)` 接收一个 bool 参数，当为 **true** 只显示 **Model-Level** 错误，当为 **false** 显示 **Model-Level** 和 **Property-Level** 错误。  
可以在 **Controller** 使用 `ModelState.AddModelError("propertyName", "errorMsg");` 手动添加错误信息，第一个参数为 `""` 则是 **Property-Level** 错误。

## Ajax Helpers

用来创建启用 Ajax 进行异步加载的元素比如 Ajax Form，Ajax 链接。 Ajax Helpers 是 `System.Web.Mvc` 命名空间中 `AjaxHelper` 类的扩展方法。

```CSharp
@Ajax.ActionLink("LoadInfo", "GetInfo", new AjaxOptions{UpdateTargetId="InfoContainer", HttpMethod="GET"})
//Html
<a data-ajax="true" data-ajax-method="GET" data-ajax-mode="replace" 
data-ajax-update="#InfoContainer" href="/Home/GetInfo">LoadInfo</a>
```

### 非侵入Ajax (Unobtrusive Ajax)

 ASP.NET MVC 提供了基于 jQuery 的非侵入 Ajax。非侵入式 Ajax 意味着通过使用帮助类方法去定义 Ajax 功能而不是通过在 View 中添加 js 代码块。

### AjaxHelper 配置选项

`AjaxOptions` 类定义的属性允许你在 Ajax 请求的生命周期中的不同阶段指定对应的回调方法。(https://www.jianshu.com/p/4ccb941357b6)

### 跨域Ajax (Cross Domain Ajax)

默认来说，浏览器只允许 Ajax 调用你自己服务器上托管的当前 WEB 应用的站点。这个限制帮助阻止了许多安全问题（比如 XSS 攻击）。但是有些时候我们需要与额外的 API 交互，我们的 WEB 应用就必须支持 JSONP 请求或 CORS（跨域资源分享）。ASP.NET MVC 默认不支持 JSONP 和 CORS，需要做一些编码和配置。

# 四、布局页 Layout

**布局页**用来使 Views 保持一致的外观体验。  
使用布局页：在 View 的顶部直接声明 `@{Layout = "~/Views/Shared/XXXLayout.cshtml"}`  

## Section

通过 Section 可以在 Layout 中指定占用一块内容区域。可以在 View 中按以下方式定义:
```CSharp
//在 View 中定义
@section header{ <h1>Header</h1> }
//在 Layout 中渲染
@RenderSection("header", required: false)
```
默认的，在 Layout 中定义了需要渲染的 Section，那么在 View 中就必须实现，除非像上面一样，设置第二个参数为 `false`，表示该 Section 是可选的。  
且 View 中只能定义已经在 Layout 中指定渲染的 Section

## @RenderBody()

`@RenderBody()` 在 Layout 页中调用，将使用了该 Layout 的子页面的内容渲染到页面中，每个 Layout 只能有一个 `@RenderBody()`

## @RenderPage(url)

同样在 Layout 页中调用，渲染 URL 的页面到 Layout 中，可以有多个。

## @Styles.Render(url) / @Scripts.Render(url)

打包压缩 JS/CSS 文件，提高网络加载速度和页面解析速度，url 代表的文件在 BundleConfig.cs 中配置
```CSharp
public class BundleConfig{
    public static void RegisterBundles(BundleCollection bundles){
        bundles.Add(new ScriptBundle("~/bundles/jqueryval")
            .Include("~/Scripts/jquery.unobtrusive*", "~/Scripts/jquery.validate*"));
        bundles.Add(new StyleBundle("~/Content/themes/base/css").Include(
            "~/Content/themes/base/jquery.ui.core.css", "~/Content/themes/base/jquery.ui.resizable.css"));
    }
}
```
当关闭优化时，`Styles.Render` 和 `Scripts.Render` 会为 StyleBundle（ScriptBundle）中的定义的每一个 CSS（Script）生成一个 Style（Script）标签。当开启优化时，`Styles.Render` 和 `Scripts.Render` 生成唯一的 Style和 Script标签，其中带有版本戳的 URL 代表整个捆绑的 CSS 和 Script。

### 启用捆绑优化

通过在 Global.asax.cs 文件中修改 BundleTable 的 EnableOptimizations 属性来打开和关闭捆绑优化。（默认应该是打开的）
```CSharp
protected void Application_Start(){
    System.Web.Optimization.BundleTable.EnableOptimizations = false;
}
```

## _ViewStart.cshtml

_ViewStart.cshtml 中的代码会在同文件夹下（包括子文件夹）的所有 View 的代码之前执行，可以用来为一系列 View 添加相同的设置操作。默认的，Views 文件夹下有一个 _ViewStart.cshtml，为所有 View 设定同一个布局。
```CSharp
@{ Layout = "~/Views/Shared/Layout.cshtml"; }
```

## App_Start 文件夹

App_Start 文件夹是从 MVC4 引入的，包含以下配置文件，比如 BundleConfig.cs, FilterConfig.cs, RouteConfig.cs, WebApiConfig.cs。所有的设置都是在 Global.asax.cs 文件的 Application_Start 方法中被注册。

* **BundleConfig.cs**：用来为 CSS 和 JS 文件创建和注册捆绑。默认已经包含了对 jQuery, jQueryUI, jQuery Validation, Modernizr, Site CSS 的捆绑。
* **FilterConfig.cs**：用来注册全局的 MVC 过滤器，比如 error filters, actions filters 等。默认包含 HandleErrorAttribute 过滤器。
* **RouteConfig.cs**：用来注册不同的路由模式，默认仅注册一个名为 Default 的路由。
* **WebApiConfig.cs**：用来注册不同的 WEB API 路由，也可用来设置额外的 WEB API 配置选项。

## 返回 View 的方式

* **return View()**：生成指定的视图的 HTML 并发送到浏览器。相当于 ASP.NET WebForm 中的 Server.Transfer()。是转发，浏览器显示的地址不会改变。
* **return RedirectToAction()**：跳转到指定的 Action 执行而不是直接提供 HTML。浏览器将收到跳转通知并重新发送一个指定 Action 的新请求，即重定向。这个类似于 ASP.NET WebForm 中的 `Response.Redirect()`。而且， `RedirectToAction` 会根据路由表构造了一个跳转 URL 到指定的 Action/Controller，会使浏览器收到 302 重定向状态码。
* **return Redirect()**：跳转到指定的 URL 而不是直接提供 HTML。类似上一个，只是你需要自己构造完整的URL去进行重定向。浏览器同样会收到 302 重定向状态码。
* **return RedirectToRoute()**：在路由表中查找指定的路由，然后重定向到路由中定义的 Controller/Action。这也是重定向。

# 五、 页面传值方式；HTTP 请求与 Action 的映射

## 页面传值

### ViewData

* ViewData 是 Controller 的属性，是 ViewDataDictionary 类的一个实例。
* ViewData 用来从 Controller 中传值到相对应的 View 中。
* 生命周期仅存在于当前此次请求。
* 如果发生重定向，那么值将会被清空。
* 从 ViewData 中取值时需要进行**类型转换**和 **Null Check** 以避免异常。

### ViewBag

* ViewBag 是 Controller 的一个动态属性（dynamic），是基于C# 4.0的动态语言的特性。
* 是对 ViewData 的一次包装，也是用来从 Controller 中传值到相对应的 View 中。
* 生命周期仅存在于当前此次请求。
* 如果发生重定向，那么值将会被清空。
* 从 ViewBag 中取值时不需要进行类型转换。

### TempData 

https://www.jianshu.com/p/eb7a301bc536

* TempData 是 Controller 的属性，是 TempDataDictionary 类的示例，存储于Session中。
* TempData 用来进行跨页面请求传值。
* TempData 被请求后生命周期即结束。
* 从 TempData 中取值时需要进行**类型转换**和 **Null Check** 以避免异常。
* 主要用来存储一次性数据信息，比如 error messages, validation messages。

#### 工作流程

1. TempData 保存在 Session 中，Controller 每次执行请求时，会从 Session 中一次获取所有 TempData 数据，保存在单独的内部数据字典中，而后从 Session 中清除。  
2. 然后在 Action 执行过程中，通过 Key 从字典中获取对应的 Value，每访问一次后对应的 Key-Value 就会从字典中删除，因此 TempData 数据最多只能经过一次 Controller 传递，并且每个元素最多只能访问一次。  
3. Action 执行完时，新的和未使用的 TempData 重新保存到 Session 中（即将内存中的 TempData 保存到 Session 中）。

* 如果想把数据保留到下一次请求，可以调用 `TempData.Keep()` 或 `TempData.Keep("key")`。或者，使用 `TempData.Peek("key")` 进行读取

### Session

* ASP.NET MVC 中 Session 是 Controller 中的一个属性，Session 是 HttpSessionStateBase 类型。
* Session 保存数据直到用户会话结束（默认 Session 过期时间为 20mins）。
* Session 对所有的请求都有效，不仅仅是单一的跳转。
* 从 Session 中取值时需要进行**类型转换**和 **Null Check** 以避免异常。

#### 控制 Session

不过 Session 里有没有数据，ASP.NET MVC 都必须为所有的 Controller 管理  Session State，存储在服务器内存中，会影响性能，可以使用特性 `[SessionState(SessionStateBehavior)]` 来控制某个 Controller 中 Session 的行为：
* **Default**：默认行为
* **Disabled**：完全关闭
* **ReadOnly**：只读
* **Required**：完全可读写的

由于 TempData 存储于 Session 中，如果关闭了 Session，将无法使用 TempData

## Action

### ActionResult

* **ViewResult**：`View()` ，用来呈现指定或默认的视图
* **PartialViewResult**：`PartialView()`，用来呈现指定或默认的分部视图
* **RedirectResult**：`Redirect()`，发起一个 HTTP 301 或 302 到指定 URL 的跳转（重定向）
* **RedirectToRouteResult**：`RedirectToAction(), RedirectToActionPermanent(), RedirectToRoute(), RedirectToRoutePermanent()`，发起一个 HTTP 301 或 302 到指定 Action 或者路由的跳转
* **ContentResult**：`Content()`，呈现指定的文本
* **JsonResult**：`Json()`，呈现序列化的 JSON 格式数据
* **JavaScriptResult**：`JavaScript()`，呈现一段 JavaScript 代码，一般仅用于 Ajax 请求的场景
* **FileResult**：`File()`，呈现文件内容
* **EmptyResult**：`new EmptyResult()`，空结果
* **HttpNotFoundResult**：`HttpNotFound()`，返回一个 HTTP 404 状态码
* **HttpUnauthorizedResult**：`new HttpUnauthorizedResult()`，返回一个 HTTP 401 状态码（未认证），用来要求用户登录以完成认证
* **HttpStatusCodeResult**：`new HttpStatusCodeResult()`，返回指定的 HTTP 状态码

### Action 相关特性

* `[NonAction]`：防止一个公共方法被暴露为 Action
* `[ActionName("Name")]`：为一个 Action 方法改名
* `[HttpGet]`：为 Action 限制访问方式（GET, POST, PUT, DELETE）

### 判断 Action 被访问方式

* `Request.HttpMethod`：判断是被哪种 HTTP 请求调用
* `Request.IsAjaxRequest()`：是否为 Ajax 请求

# 六、 模型验证、前端的优化技术

## Data Annotations

Data Annotations（数据注解）是指 `System.ComponentModel.DataAnnotations` 命名空间下的一系列特性，适用于  ASP.NET 项目以及 Entity Framework 模型，为 Model 类或属性定义规则进行数据验证和显示合适的提示信息给终端客户。  

常见特性如下：
* **DataType**：为属性指定数据类型
* **DisplayName**：为属性指定显示名称
* **DisplayFormat**：为属性指定显示格式
* **Required**：限制属性为必须的
* **RegularExpression**：用正则表达式验证属性值
* **Range**：限制属性的取值区间（数字）
* **StringLength**：限制字符串长度
* **MaxLength**：限制字符串最大长度
* **Bind**：添加参数或表单数据到 Model 时，指定字段会被添加到或排除
* **ScaffoldColumn**：隐藏表单编辑界面的指定字段

## 服务器端验证

可以使用 Data Annotations 对模型属性进行注解，然后通过 `Controller.ModelState` 进行判断：
```CSharp
public ActionResult DoSomething(MyModel model){
    if (!ModelState.IsValid) {
        string msg = "";
        foreach (var fieldState in ModelState) {
            foreach (var error in fieldState.Value.Errors) {
                msg += error.ErrorMessage + "\n";
            }
        }
        throw new Exception(msg);
    }

    //也可以自己添加
    ModelState.AddModelError("UserName", "Please enter your name");
}
```

## 关闭和启用客户端验证

可以通过设置 ClientValidationEnabled & UnobtrusiveJavaScriptEnabled 在应用程序级别启用和关闭客户端验证。开启客户端验证，两个属性都必须为true。
```xml
<add key="ClientValidationEnabled" value="true" />
<add key="UnobtrusiveJavaScriptEnabled" value="true" />
```
修改 Global.asax 中的 `Application_Start()` 事件也可以启用关闭客户端验证：
```CSharp
protected void Application_Start(){
    HtmlHelper.ClientValidationEnabled = true;
    HtmlHelper.UnobtrusiveJavaScriptEnabled = true;
}
```
可以为某一 View 启用及关闭客户端验证。通过在 View 中的 Razor 代码块中指定。View 中的设置将覆盖应用程序级别的设置。
```CSharp
@using MvcApp.Models
@{ HtmlHelper.ClientValidationEnabled = false; }
```

## Bundling（捆绑）和 Minification （微小）

ASP.NET MVC 提供捆绑和微小技术来减少对服务器的请求次数以及减少请求的 CSS 和 JavaScript 的大小，从而加快页面加载时间。

一个 Bundle 是逻辑上的一组文件，仅通过一次 HTTP 请求就完成加载。所有的捆绑都是在 BundleConfig.cs 文件中创建。
```CSharp
public class BundleConfig
{
 public static void RegisterBundles(BundleCollection bundles)
 {
 bundles.Add(new StyleBundle("~/Content/css")
    .Include("~/Content/site.min.css", "~/Content/mystyle.min.css"));
 bundles.Add(new ScriptBundle("~/bundles/jqueryval")
    .Include("~/Scripts/jquery-1.7.1.min.js", "~/Scripts/jquery.validate.min.js"); 
 }
}
```

Minification 是一项用来移除 JavaScript 和 CSS 文件中不必要的字符（比如空格，换号符，制表符）和注释来减小文件大小来加快网页加载速度。有很多种工具进行微小（其中 JSMin 和 YUI 是最流行的两款工具）。

## Bundling（捆绑）与浏览器缓存

浏览器缓存资源是基于 URL 的，当页面请求一个资源，浏览器首先检查缓存中是否存在资源与请求的 URL 匹配，若有则直接使用缓存。  
因此，当你改变 JS 和 CSS 文件时，它可能不会被浏览器加载，这时必须强制刷新浏览器（清除或禁用缓存）  
但使用 Bundling 时，会自动为每个 Bundle 添加一个 HashCode 作为 URL 的查询参数，所以当改变文件时，HashCode就会改变，浏览器一直能获得最新的 CSS 和 JS

# 七、 PartialView、Area

PartialView 分部视图相当于一个可重用的视图模块，最好是创建在 Shared 文件夹并以 `_` 为前缀命名。

## 获取 PartialView

### 在 Controller 中

`return PartialView(name)`

### 在 View 中

* **Html.RenderPartial(name)**：结果直接写入 Http 响应流，即使用与当前页面、模板相同的 TextWriter 对象，速度比较快。
    * 被调用的分部视图可以直接使用父视图的强类型 Model
    * 返回类型为 void，不能直接调用，而应该放在 Razor 语句块中：`@{ Html.RenderPartial(name); }`
* **Html.RenderAction(actionName)**：与上面类似，只是调用 Action 来得到分部视图
* **Html.Partial(name)**：返回 Html 编码的字符串（MvcHtmlString），速度较慢，但可以在返回后通过修改字符串的内容来修改视图内容
* **Html.Action**：与上面类似，只是调用 Action 来得到 MvcHtmlString

两种不调用 Action 而直接获取分部视图的方法都可以传入 Model 和 ViewData

### ChildAction

ChildAction 通过在 Action 方法上添加 `[ChildActionOnly]` 特性来创建，常用来生成分部视图，只能在 View 中通过 `Html.RenderAction()` 或 `@Html.Action()` 来调用，拥有独立于父视图的 MVC 生命周期

## Area

Area 用来分离模块，每个模块拥有各自的 Models、Views、Controllers。

### 注册Area

在 Global.asax 的 Application_Start 方法中注册
```CSharp
protected void Appication_Start(){
    AreaRegistration.RegisterAllAreas();
}
```
必须在最开始注册 Area，以便注册的 Settings、Filters、Routes 能作用于 Area

# 八、过滤器

ASP.NET MVC 通过特性的方式来给 Controller 或 Action 添加过滤器，在 Action 执行前或后执行一些操作。自定义过滤器需要继承 `FilterAttribute` 或实现 `IMvcFilter`。

## ASP.NET MVC 中的过滤器

* **AuthenticationFilter**：认证过滤器，实现 `IAuthenticationFilter` 即可创建自定义认证过滤器
    ```CSharp
    public interface IAuthenticationFilter{
        void OnAuthentication(AuthenticationContext filterContext);
        void OnAuthenticationChallenge(AuthenticationChallengeContext filterContext);
    }
    ```
* **Authorization Filter**：授权过滤器，实现 `IAuthorizationFilter` 接口或继承 `AuthorizeAttribute` 类并重写虚方法
    ```CSharp
    public interface IAuthorizationFilter{
        void OnAuthorization(AuthorizationContext filterContext);
    }

    //AuthorizeAttribute 中的虚方法
    protected virtual bool AuthorizeCore(HttpContextBase httpContext);
    protected virtual void HandleUnauthorizedRequest(AuthorizationContext filterContext);
    public virtual void OnAuthorization(AuthorizationContext filterContext);
    protected virtual HttpValidationStatus OnCacheAuthorization(HttpContextBase httpContext);
    ```
* **ActionFilter**：操作过滤器，在 Action 执行之前和之后执行，实现 `IActionFilter` 接口
    ```CSharp
    public interface IActionFilter{
        void OnActionExecuting(ActionExecutingContext filterContext);
        void OnActionExecuted(ActionExecutedContext filterContext);
    }
    ```
* **ResultFilter**：结果过滤器，在 Action 生成结果之前和之后执行，在 ActionFilter 之后调用。实现 `IResultFilter` 接口
    ```CSharp
    public interface IResultFilter{
        void OnResultExecuted(ResultExecutedContext filterContext);
        void OnResultExecuting(ResultExecutingContext filterContext);
    }
    ```
* **ExceptionFilter**：异常过滤器，实现 `IExceptionFilter` 接口
    ```CSharp
    public interface IExceptionFilter{
        void OnException(ExceptionContext filterContext);
    }
    ```
    默认的异常过滤器是 `HandleErrorAttribute`，处理后返回 /Views/Shared 文件夹下的 Error 视图

## 过滤器执行顺序

1. AuthenticationFilter
2. AuthorizationFilter
3. ActionFilter
4. ResultFilter

## 配置过滤器

过滤器的作用范围分为三个级别：

1. Global
   在 Global.asax.cs 文件的 `Application_Start` 方法中注册
    ```CSharp
    protected void Application_Start(){
        FilterConfig.RegisterGlobalFilters(filter);
    }
    ```
2. Controller
   添加特性到 Controller 上
3. Action
   添加特性到 Action 上

## 表单认证和授权工作流程

表单认证在 IIS 认证完成之后发生，在 Web.config 文件中的 forms 节点进行配置。默认配置如下：
```xml
<system.web>
<authentication mode="Forms">
<forms loginUrl="login.cshtml" protection="All" timeout="30" name=".ASPXAUTH" path="/" requireSSL="false" slidingExpiration="true" defaultUrl="default.cshtml" cookieless="UseDeviceProfile" enableCrossAppRedirects="false"/>
</authentication>
</system.web>
```

用户通过登录页面发送登录数据，服务器从数据库中获取数据校验登录数据是否正确，登录成功时设置 Cookie 并重定向到首页，当 `SetAuthCookie()` 或 `RedirectFromLoginPage()` 被调用时 `FormsAuthentication` 类自动创建认证 Cookie。认证 Cookie 中包含一个已经加密和签名的 `FormsAuthenticationTicket` 对象的字符串。  
可以指定 Cookie 的名称、版本、目录路径、生效日期、失效日期、是否永久等属性来创建 `FormsAuthenticationTicket` 对象：
```CSharp
var ticket = new FormsAuthenticationTicket(1, "userName", DateTime.Now, DateTime.Now.AddMinutes(30), false, string.Empty, FormsAuthentication.FormsCookiePath);
```
然后就可以使用 `FormsAuthentication` 类的 `Encrypt` 方法来加密ticket。
`string encryptedTicket = FormsAuthentication.Encrypt(ticket);`

## 自定义表单认证和授权
(这部分讲得不是很清楚，待以后补充)

### 认证

一个用户上下文中有一个 Principal，代表了用户的身份（Identity）和角色（Role），用户通过身份进行认证，通过给用户分配角色进行授权。

ASP.NET 提供了 `IPrincipal` 和 `IIdentity` 接口来表示用户的身份和角色，这两个接口绑定到 `HttpContext` 对象和当前线程，通过实现这两个接口来自定义表单认证和授权：
```CSharp
public class CustomPrincipal : IPrincipal{
    public IIdentity Identity {get; private set;}
    public bool IsInRole(string role){
        if(roles.Any(r => role.Contains(r))){
            return true;
        }
        return false;
    }
    public CustomPrincipal(string Username){
        this.Identity = new GenericIdentity(Username);
    }
    public int UserId {get; set;}
    public string UserName {get; set;}
    public string[] roles {get; set;}
}
```
可以把 `CustomPrincipal` 对象放入 `Thread.CurrentPrincipal` 属性和 `HttpContext.User` 属性来完成自定义的认证和授权流程。  
如果 `CustomPrincipal.Identity.IsAuthenticated` 返回 true 则表示认证成功

### 授权

通过继承 `AuthorizeAttribute` 类并重写 `OnAuthorization()` 方法来自定义授权过滤器：
```CSharp
public class CustomAuthorizeAttribute: AuthorizeAttribute {
    protected virtual CustomPrincipal CurrentUser {
        get {
            return HttpContext.Current.User as CustomPrincipal;
        }
    }
    public override void OnAuthorization(AuthorizationContext filterContext) {
        if (filterContext.HttpContext.Request.IsAuthenticated) {
            if (!String.IsNullOrEmpty(Roles)) {
                if (!CurrentUser.IsInRole(Roles)) {
                    filterContext.Result = new RedirectToRouteResult(new RouteValueDictionary(new {
                        controller = "Error",
                        action = "AccessDenied"
                    }));
                }
            }
            if (!String.IsNullOrEmpty(Users)) {
                if (!Users.Contains(CurrentUser.UserId.ToString())) {
                    filterContext.Result = new RedirectToRouteResult(new RouteValueDictionary(new {
                        controller = "Error",
                        action = "AccessDenied"
                    }));
                    // base.OnAuthorization(filterContext); //returns to login url
                }
            }
        }
    }
}
```

## 允许输入 HTML 字符

ASP.NET MVC 默认不允许用户去提交 HTML，避免跨域脚本（XSS）攻击。  
在 Action 或 Controller 使用 `[ValidateInput(false)]` 特性可以启用或禁止输入校验。但这样会是所有参数都不进行校验。
可以在 Model 的某个属性使用 `[AllowHtml]` 特性来允许单个属性输入 HTML 字符