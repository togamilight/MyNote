# 入门

* 在命令行使用以下命令可新建 Web 应用：
  `dotnet new webapp -o projName`
  * `-o projName` 参数表示使用应用的源文件创建名为 projName 的目录
* 信任 HTTPS 开发证书：
  `dotnet dev-certs https --trust`
* 在应用目录下运行以下代码可启动应用：
  `dotnet watch run`

# Web 应用教程

## Razor 页面

### 入门

在 Visual Studio 中新建 Web 应用（ASP.NET Core 3.1）

#### 项目文件

* **Pages 文件夹**：包含 Razor 页面和支持文件，每个页面包含一对文件：
  * `.cshtml` 文件，包含使用 Razor 语法的 C# 代码的 HTML 标记
  * `.cshtml.cs` 文件，包含处理页面事件的 C# 代码
  支持文件以下划线开头，如 `_Layout.cshtml` 可配置所有页面的通用 UI 元素
* **wwwroot 文件夹**：包含静态文件，如 html, js, css
* **appSettings.json**：包含配置数据，如连接字符串
* **Program.cs**：程序入口
* `Startup.cs`：包含配置应用行为的代码

### 添加模型

添加一个模型，并用基架工具生成其增删查改页面，在这过程中同时生成了**数据上下文**类

#### 初始迁移

在 NuGet 包管理器控制台（PMC）中输入：
```
Add-Migration InitialCreate
Update-Database
```
`migrations` 命令生成用于创建初始数据库架构的代码；该架构基于在 `DbContext` 中指定的模型，`InitialCreate` 参数用于为迁移命名，可使用任意名称  
`update` 命令在尚未应用的迁移中运行 `Up` 方法，这种情况下，将在用于创建数据库的 `Migrations/<time-stamp>_InitialCreate.cs` 文件中运行 Up 方法

当模型类更改时，可使用这两个命令来更新数据库，`Update-Database` 表示保留原有数据并更新

#### 数据上下文的依赖注入

ASP.NET Core 通过依赖关系注入进行生成，各种服务在应用程序启动期间通过依赖关系注入进行注册，需要这些服务的组件通过构造方法提供相应的服务

### 基架

#### 增删查改页面

Razor 页面派生自 `PageModel` 类，一般命名为 `XXModel`，该类的构造方法用依赖关系注入将 `DbContext` 添加到页；  
向页面发起请求时，`OnGet/OnGetAsync` 方法被调用，以初始化页面状态，`OnGet` 可返回 `void/IActionResult`，`OnGetAsync` 对应的是 `Task/Task<IActionResult>`

Razor 可以从 HTML 转换为 C# 或 Razor 特定标记。 当 @ 符号后跟 Razor 保留关键字时，它会转换为 Razor 特定标记，否则会转换为 C#

##### @page 指令

`@page` 指令将文件转化为一个 MVC 操作，这意味着它可以处理请求，必须是页面上第一个 Razor 指令

##### @model 指令

```
@model XXX.XXModel
```
`@model` 指令指定传递给 Razor 页面的模型类型 

##### 布局页

布局模板允许 HTML 容器具有如下布局：
* 在一个位置指定
* 应用于站点中多个页面

使用了布局页的页面的内容将渲染到布局页的 `@RenderBody()` 行

布局页在 `Pages/_ViewStart.cshtml` 文件中配置：`@{ Layout = "_Layout"}`  
针对所有 Razor 文件将布局文件设置为 `Pages/Shared/_Layout.cshtml`

##### ViewData 和布局

`PageModel` 基类包含 `ViewData` 字典属性，用于将数据传递到某个视图

##### Create 页面模型 PageModel

`OnGet` 方法初始化页面所需的任何状态，返回 `Page()`，`Page()` 方法创建用于呈现 HTML 页面的 `PageResult` 对象

使用 `[BindProperty(SupportsGet = true)]` 特性来将属性加入**模型绑定**（要支持 Get 需设置），当 Create 表单发布表单值时，ASP.NET Core 运行时将发布的值绑定到属性模型  

当页面发布表单数据时，运行 `OnPostAsync` 方法，此时可捕获到模型校验状态：`ModelState.IsValid`

##### Create Razor 页面

Visual Studio 会将**标记帮助程序**加粗显示，比如 `<form method="post">`，表单标记帮助程序会自动包含防伪令牌

基架引擎为模型的每个字段（ID 除外）创建 Razor 标记：  
* **验证标记帮助程序**显示验证错误
  * `<div asp-validation-summary 和 <span asp-validation-for`
* **标签标记帮助程序**生成标签描述和 Title 属性的 for 特性 
  * `<label asp-for="Movie.Title" class="control-label"></label>`
* **输入标记帮助程序**使用 `DataAnnotations` 属性并在客户端生成 jQuery 验证所需的 HTML 属性
  * `<input asp-for="Movie.Title" class="form-control">`

### 数据库

#### 数据上下文的依赖注入

ASP.NET Core 使用 EntityFramework Core 进行数据库连接  

ASP.NET Core 通过依赖关系注入进行生成，各种服务在应用程序启动期间通过依赖关系注入进行注册，需要这些服务的组件通过构造方法提供相应的服务。

基架工具自动创建了 `DbContext` 并将其注册到依赖关系注入容器，代码添加在 `Startup.cs` 中：
```CSharp
public void ConfigureServices(IServiceColletion services){
    ...
    services.AddDbContext<MyDbContext>(options => 
        options.UseSqlServer(Configuration.GetConnectionString("MyDbContext")));
}
```

DbContext 的调用方式：
```
var context = new MyDbContext(
    serviceProvider.GetRequiredService
      <DbContextOptions<MyDbContext>>()
  );
```

ASP.NET Core 配置系统会读取 `ConnectionString`，本地开发时，从 `appsetting.json` 文件获取，将应用部署到服务器时，可使用环境变量将连接字符串设置为实际的数据库服务器

### 页面

#### 路由模板

在页面开头的 `@page` 命令处可配置该页面的路由模板，如：`@page "{id:int?}"`

### 验证

#### DataAnnotations

`DataAnnotations` 命名空间提供内置验证特性，应用于类或属性：
* `Required` 和 `MinimumLength` 特性表示属性必须有值，但可只输入空格
  * 值类型必填不需要 `Required`
* `RegularExpression` 特性使用正则表达式验证
* `Range` 特性限定值的范围
* `StringLength` 特性限制字符串长度，参数 `MinimumLength` 可指定最小长度

注意：
* `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`，`Range` 不合适用于 `DateTime` 类型，jQuery 验证不支持
#### Razor 页面的验证 UI

自动创建的 Create/Edit 页面自动选取了和模型类的属性上一致的验证规则，使用 jQuery 验证？验证错误时显示提示

`DataAnnotations` 可能改变数据库表结构（不为空，长度等），修改后要在 Nuget PMC 用命令更新数据库

#### 服务器端验证

在浏览器中禁用 JS 后，出错表单会提交到服务器，服务器端使用 `ModelState.IsValid` 判断验证情况，错误时直接返回 `Page()`，页面会有错误提示

#### DataType 特性

`DataType` 特性仅提供相关提示来帮助视图引擎设置数据格式，用于指定比数据库内部类型更具体的数据类型；  
应用程序还可通过 `DataType` 特性自动提供类型特定的功能，例如，可为 `DataType.EmailAddress` 创建 `<a href="mailto:EmailAddress">` 链接，为 `DataType.Date` 提供日期选择器；  
`DataType` 特性提供 `data-` 属性给 HTML5 浏览器使用，本身不提供任何验证  

`DisplayFormat ` 特性用于指定数据显示格式：`[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]`  
`ApplyFormatInEditMode` 参数设置在编辑页面是否需要以该格式显示

可单独使用 `DisplayFormat` 特性，但通常建议使用 `DataType` 特性，它传达数据的语义而不是如何在屏幕上呈现数据，并提供 `DisplayFormat` 不具备的优势：
* 浏览器可启用 HTML5 功能（例如显示日历控件、区域设置适用的货币符号、电子邮件链接等）
* 默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。
* 借助 `DataType` 特性，ASP.NET Core 框架可选择适当的字段模板来呈现数据。 单独使用时，`DisplayFormat` 特性将使用字符串模板。

## MVC

模型-视图-控制器 (MVC) 体系结构模式将应用分成 3 个主要组件：模型 (M)、视图 (V) 和控制器 (C) 
* **模型**：表示应用数据的类，模型类使用验证逻辑来对该数据强制实施业务规则，通常，模型对象检索模型状态并将其存储在数据库中，业务逻辑位于模型中
* **视图**：显示应用用户界面的组件，此 UI 通常会显示模型数据
* **控制器**：处理浏览器请求的类，它们检索模型数据并调用返回响应的视图模板即处理并响应用户输入和交互
UI 逻辑位于视图中，输入逻辑位于控制器中，业务逻辑位于模型中

### 添加控制器

控制器中的每个 `public` 方法均可作为 HTTP 终结点调用  
MVC 默认路由规则：`/[Controller]/[ActionName]/[Parameters]`

### 添加视图

MVC 的视图是 cshtml 文件，默认布局是在 Views/Shared/_Layout.cshtml 文件中实现的（Views/_ViewStart.cshtml 文件中的 `@{ Layout = "_Layout"; }` 设定的），使用了布局页的页面的内容将渲染到布局页的 `@RenderBody()` 行  
使用动态类型对象 `ViewData` 可将数据传递到视图

### 添加模型

安装 EF 命令：`Install-Package Microsoft.EntityFrameworkCore.SqlServer`

添加模型类、DbContext、向 DI 容器注册 DbContext 服务，添加数据库连接字符串，初始迁移，创建基架页面等操作与 Razor 应用类似

Controller 在构造方法中使用依赖关系注入来注入 DbContext，供 Controller 内的方法调用

#### 强类型

MVC 还提供将强类型模型对象传递给视图的功能，相比 ViewData，此强类型方法启用编译时代码检查  
需要在 View 文件中加入 `@model MyModel`，在 Action 方法返回 `return View(myModel)`，这样在 View 中可以用 `@Model` 的方式调用该 Model
