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
默认的，在layout中定义了需要渲染的 Section，那么在 View 中就必须实现，除非像上面一样，设置第二个参数为 `false`，表示该 Section 是可选的。  
且 View 中只能定义已经在 Layout 中指定渲染的 Section