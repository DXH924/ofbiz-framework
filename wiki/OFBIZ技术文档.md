# OFBIZ技术文档

OFBIZ技术文档是最优先，最全面的文档。包含了技术文档和开发协调信息

https://cwiki.apache.org/confluence/display/OFBIZ/Technical+Documentation#TechnicalDocumentation-GettingStarted

## 一、OFBIZ背景信息

### 1.1 核心概览

#### 1.1.1 框架配置

这部分文档描述了OBFIZ框架的框架配置。文档解释了OFBIZ框架中的每一个可用的properties文件的作用。OFBiz应用程序使用的properties文件有不同选项的示例，位于OFBiz/commonapp/etc中。

* cache.properties
  * 包含了UtilCache类使用的缓存设置。创建缓存时，可以传入默认缓存参数，但是，如果给定缓存名称的缓存参数存在于该文件中，则将从cache.properties中加载这些参数
* debug.properties
  * 包含打开或关闭OFBIZ调试线程的选项，还可以作为log4J属性文件
* jndiservers.xml
  * 这是一个简单的XML文件，在一个jndi配置标记中包含许多jndi服务器标记。每个jndi服务器标记对应于一个命名的jndi服务器配置，该配置将在OFBiz框架的各个位置使用，尤其是在实体和服务引擎中。
  * jndi服务器标签用于配置jndi服务器，这些服务器将用于JTA对象、JDBC对象、JMS对象以及实体引擎、服务引擎和其他框架或应用程序组件使用的其他jndi资源。
* localdtds.properties
  * 包含DTD名称和文件名。类路径上可用于该名称的dtd文件。这用于完成DTD的本地加载，而不是在加载和验证XML文件时总是通过internet获取它们。此文件由UtilXml类使用
* security.properties
  * 包含与安全相关的设置，例如密码的最小长度。当前文件中唯一的属性是password.length.min，指定用户登录密码中的最小字符数
* serverstats.properties
  *  含用于控制服务器命中统计信息的收集和持久性的设置 
* url.properties
  *  url.properties 文件用于为当前服务器的 URL 不足的情况配置特殊的服务器 URL。这方面的示例包括自动跳转到站点的安全或非安全区域以及引用不同服务器或不同服务器场中的内容的设置

#### 1.1.2 实体引擎

https://cwiki.apache.org/confluence/display/OFBIZ/Entity+Engine+Guide

OFBIZ实体引擎是一组用于建模和管理实体特定数据的工具和模式。在这种情况下，实体是由一组字段和一组与其他实体的关系定义的数据。此定义来自关系数据库管理系统的标准实体-关系建模概念。实体引擎的目标是简化实体数据在企业范围内的使用。这包括实体相关功能的定义、维护、质量保证和开发。 

实体引擎的主要目标是在事务应用程序的尽可能多的区域中消除对实体特定持久性代码的需求 。

为了实现尽可能少的实体特定代码，所有值对象都是通用的，使用映射按名称存储实体实例的字段值。字段的 get 和 set 方法接受一个带有 fieldName 的字符串，用于验证该字段是否是实体的一部分，然后根据需要获取或设置一个值。使用实体引擎和应用程序之间的合同减少了这种灵活性的危险；这包含在一个特殊的 XML 文件中。 

实体定义不是编写实体特定的代码，而是从 XML 文件中读取并由实体引擎用于在应用程序和数据源（无论是数据库还是其他一些源）之间强制执行一组规则。这些 XML 实体定义指定了所有实体的名称及其字段以及它们对应的数据库表和列。它们还用于为每个字段指定类型，然后在给定数据源的字段类型文件中查找该类型以查找 Java 和 SQL 数据类型。实体之间的关系也在此 XML 文件中定义。通过指定相关表、关系类型（一个或多个）和关系的键映射来定义关系。也可以为成为其名称一部分的关系赋予标题，以将其与与该特定相关实体的其他关系区分开来 

使用实体引擎作为抽象层，可以轻松创建和修改实体特定代码。可以以各种方式部署使用实体引擎 API 与实体交互的代码，以便可以以不同方式完成实体持久性，而无需更改与更高级别的实体交互的代码。这种抽象的有用性的一个例子是，通过更改配置文件，使用实体引擎编写的应用程序可以从直接通过 JDBC 访问数据库切换到使用 EJB 服务器和实体 Bean 来实现持久性。相同的代码还可以用于自定义数据源，例如通过 HTTP 的遗留系统或通过在同一框架内进行一些自定义编码的消息传递服务 

#### 1.1.3 实体引擎配置

https://cwiki.apache.org/confluence/display/OFBIZ/Entity+Engine+Configuration+Guide

#### 1.1.4 服务引擎

服务是独立的逻辑片段，它们放在一起处理许多不同类型的业务需求。服务可以有许多不同的类型：工作流、规则、Java、SOAP、Groovy 等。Java 类型的服务很像一个静态方法的事件，但是使用服务框架，我们不限于基于 Web应用程序。服务要求输入参数在 Map 中，并且结果也在 Map 中返回。这很好，因为 Map 可以通过 HTTP (SOAP) 进行序列化和存储或传递。 

服务通过服务定义，并分配给特定的服务引擎。每个服务引擎负责以适当的方式调用定义的服务。由于服务不依赖于基于 Web 的应用程序，这允许服务在没有可用响应对象时运行。这允许将服务安排在特定时间运行，以通过 Job Scheduler 在后台运行 。

服务可以调用其他服务。因此，将小型服务链接在一起以完成更大的任务可以更轻松地重用现有服务。 

在组件中声明的服务（ofbiz-component.xml 文件中的服务资源）可以从 OFBiz 中的任何地方访问，甚至可以使用导出功能在外部访问。虽然这种可能性并不常用（没有示例 OOTB），但也可以创建特定于应用程序的服务：仅限于在该应用程序中可用。为此，您将服务定义和实现文件放在 WEB-INF 目录下。您还可以通过在部署上下文中使用相同的名称来覆盖服务（首先是框架，然后是主题，然后是应用程序，然后是特殊用途，然后是热部署）。这很方便，但如果不需要，请小心... 

在 Web 应用程序中使用时，Web 事件可以使用服务，这允许事件保持较小并重用服务框架中的现有逻辑。此外，服务可以定义为“可导出”，这意味着它们可以被外部各方访问。目前有一个 SOAP EventHandler 允许通过 SOAP 提供服务。将来可能会将其他形式的远程调用添加到框架中 

#### 1.1.5 服务引擎配置

https://cwiki.apache.org/confluence/display/OFBIZ/Service+Engine+Configuration+Guide

本文档描述了服务引擎的配置。 它首先介绍一般概念，然后遍历 serviceengine.xml 文件的每个部分并解释可用元素及其用法。 用于 OFBiz 应用程序的 serviceengine.xml 文件包含许多不同选项的示例，位于 ofbiz/commonapp/etc/serviceengine.xml。 服务引擎的配置是通过一个名为 serviceengine.xml 的简单 XML 文件完成的，该文件必须存在于类路径的某个位置 

#### 1.1.6 数据文件

数据文件库是用于读取和写入“plat”文件的工具。它允许您使用类似于实体引擎 GenericValue 对象的通用对象以更自然的方式处理平面文件数据。

该设计与实体引擎非常相似，其中有一个 XML 配置文件用于指定平面文件的格式和结构，还有一个通用 API 用于处理文件中的记录。一个主要区别是数据文件记录是分层的，一条记录可以有各种子记录。描述符文件也以这种方式构造，以便每个记录类型都可以有一个父记录类型。 读取文件时，该库会将各种格式和样式的字符串转换为数字、日期等。它还支持常见的平面文件格式，例如零填充数字、隐含的固定小数点数字和不带分隔符的日期。 

#### 1.1.7 Mini语言

https://cwiki.apache.org/confluence/display/OFBIZ/Mini+Language+-+minilang+-+simple-method+-+Reference

#### 1.1.8 Control Servlet

https://cwiki.apache.org/confluence/display/OFBIZ/Control+Servlet+Guide

OFBIZ的controller是一组类，用于管理OFBIZ框架设计的Web应用程序的呈现。 控制器旨在通过与实体委托者保持恒定状态来与实体引擎一起工作，以及通过与本地服务调度程序保持状态来与服务框架一起工作。 控制器的目标是提供一种将表示逻辑与实际显示分离的干净机制。

该控制器利用了几种 J2EE 表示层设计模式。 上下文安全过滤器仿照装饰过滤器模式。 CSF 在上下文根中运行，能够限制对 JSP 模板的直接访问以及为将来的服务（例如调试、日志记录和压缩）打开大门。 Control Servlet 是在 Front Controller 模式之后建模的。 这个 Servlet 是 Web 应用程序的请求处理开始的地方。 它使用处理安全和事件的辅助类，然后分派到定义的视图。 视图是 JSP 模板，与复合视图模式中描述的模板非常相似。 这些模板使用帮助文件来限制在实际显示页面中找到的逻辑数量。 这些帮助文件是执行特定视图组逻辑的 Java 类。 此过程基于 View Helper 模式 

* Context Security Filter
  *  /WEB-INF/web.xml 中定义的上下文安全过滤器 (CSF) 用于限制对 Web 应用程序文件的访问。将来它可能用于调试和/或记录请求。这是对应用程序的所有 Web 请求的起点。默认情况下，所有路径都被拒绝，并且只有明确定义的路径才允许直接访问。通过在“允许列表”中设置 Servlet 的挂载点，CSF 被设置为允许对控制 Servlet 的所有请求。允许列表定义为名为 allowedPaths 的 init-param。该值是由冒号分隔的单个路径字符串。示例 webapps 允许 '/control:/index.html:/index.jsp:/default.html:/default.jsp:/images' 路径。路径可以是目录名（从 webapp 的根目录开始）或特定文件的路径。 
  * 示例：'/images' 将允许直接访问 /images 目录中的所有文件。 
  * 示例：“/site-pages/contactus.html”将只允许直接访问 /site-pages 目录中的 contactus.html 文件。
  * 示例：'/site-pages/info/*' 将只允许直接访问子目录 'info' 中的文件。 
  * 当对受保护路径发出直接请求时，过滤器将执行以下两项操作之一。一，过滤器可以将用户重定向到 web.xml 中定义的页面。这是通过将 init-param redirectPath 设置为格式正确的 URL 来定义的。二、过滤器会抛出一个服务器错误，这个错误可以由 init-param errorCode 定义。仅当未定义重定向时才会引发错误。如果没有定义 errorCode，过滤器将抛出 404 服务器错误。  
  
* Control Servlet
  *  Control Servlet 是所有请求处理的核心。通过上下文安全过滤器的有效请求在此处开始处理。当收到请求时，Control Servlet 首先为帮助类设置一个环境。此环境包括（但不限于）设置初始会话对象并存储有关初始请求的有用信息以及设置对实体委托者、服务调度程序和安全处理程序的引用以供帮助程序类使用。然后将请求传递给请求处理程序进行处理。请求处理程序处理请求并在完成后返回到 ControlServlet 
  * 首次加载 Control Servlet 时，它将创建 Web 应用程序使用的对象并将它们存储在应用程序上下文 (ServletContext) 中。可以通过访问上下文中的属性或通过 JSP <useBean> 标记找到这些对象。这些对象包括：Entity Delegator、Security 对象、Service Dispatcher 和 Request Handler 
  
* Request Handler
  * Request Handler 使用 RequestManager 帮助类来收集在 XML 配置文件中定义的请求映射列表。配置文件位于 /WEB-INF/controller.xml 中以用于适当的上下文。该映射由一个请求 URI 和一个可选的 VIEW 名称组成。视图名称也映射到配置文件中。请求 URI 也可以与事件相关联。事件用于处理与 Web 相关的逻辑，方法是通过实体委托器直接与实体引擎一起工作，或者通过服务调度程序调用服务来处理逻辑。 
  * 当通过请求处理程序接收到请求时，首先在请求映射中查找它；如果未找到映射，则返回“未知请求错误”。成功查找后，将调用安全处理程序来验证当前请求是否需要身份验证，以及发出请求的用户是否经过正确身份验证。如果当前用户未通过身份验证，则请求处理程序将请求重定向到正确的登录表单。在成功认证后或者如果不需要认证，请求处理程序然后查找请求的定义事件。如果找到事件，则将请求传递给 EventHandler 进行处理。事件处理完成后，将为此请求查找默认视图，除非 EventHandler 请求特定视图。然后在视图映射中检查视图，并将值传递给定义的 ViewHandler 以进行调度。如果视图类型为 none，则期望事件自己处理响应；如果视图类型是 url，则 RequestHandler 会重定向到指定的 URL，并且不会调用 ViewHandler。
  
* Event Handler

   事件处理程序在 XML 配置文件中定义，例如：  

  ```xml
  <handler name="java" type="request" class="org.ofbiz.webapp.event.JavaEventHandler"/>
  ```

  与上面的请求映射一样，登录事件是使用 java类型定义的。 事件类型使用处理程序定义映射到事件处理程序。 可以设计自定义事件处理程序，并可以像上面的示例那样实现。  

   Java事件通过定位事件的路径（包和类名）来处理； 然后，通过使用反射 API，事件处理程序调用定义的方法。 一个 String 对象返回到请求处理程序，该请求处理程序映射到请求定义的响应元素 

* View Handler

  定义和Event Handler类似

  ```xml
  <handler name="region" type="view" class="org.ofbiz.webapp.view.RegionViewHandler"/>
  ```

  视图处理程序处理呈现用户将看到的下一页。 默认视图处理程序通过分派到视图定义中定义的页面来支持标准 html/jsp 页面。 区域和速度等其他视图处理程序使用特殊逻辑将页面呈现给用户。 通过实现 ViewHandler 接口并在 controller.xml 文件中设置定义，可以轻松创建自定义视图处理程序，如上例所示。  

#### 1.1.9 模块依赖

* 组件集合， 目前OFBiz中有少量组件集（从最低级别到最高级别）：  

  * framework

  * applications

  * plugins

  这些集合中的组件之间的依赖关系只能在列表中列出。 例如插件中的组件可以依赖应用程序中的组件，但应用程序中的组件不能依赖插件中的组件 
  
* framework

  framework是提供基本应用程序和扩展组件使用的基本通用特性的底层数据结构和实用程序。 这些更高级别的组件将对框架组件有很多依赖。 框架中不应该依赖于 OFBiz 的其他部分，但有一些已经悄悄进入。它们在图中显示为灰色箭头。 在添加任何新的依赖项之前，应该对邮件列表进行完整的讨论。 实际上，应该避免它们，甚至欢迎删除现有的。 仅框架分发是朝着这个方向迈出的第一步 

* base applications

  * 如图所示，基本应用程序组件相互依赖。之所以需要这些依赖关系，是因为组件是根据高级业务概念组织的，并且在现实世界中，业务流程往往会在其中的许多组件之间跳转。用技术术语来说，这会导致从一个组件中的实体到另一个组件中的实体的外键，以及从一个组件中的服务到另一个组件中的服务的服务调用，以及其他类似的效果。https://cwiki.apache.org/confluence/display/OFBIZ/Component+and+Component+Set+Dependencies
  * 虽然组件可以在基本应用程序集中相互依赖，但它们应该遵循已建立的依赖关系优先级。在下图中，您可以看到现有依赖项和组件依赖项优先级从页面顶部向下（换句话说，页面上较高的组件依赖于页面上较低的组件） 
  *  每当某些数据结构或功能可能进入多个组件或可能依赖于多个组件中的元素时，设计应确保较低级别的组件不依赖于较高级别的组件 

* plugins

  * 插件集中的组件不应相互依赖。 如果这个集合中的组件需要依赖另一个集合中的某些东西，那么应该将某些东西放入applications中最合适的组件中，并且两个插件组件都可以依赖于应用程序组件。 目前，在演示数据方面，我们至少依赖于电子商务组件的应用程序组件 

### 1.2 数据模型

#### 1.2.1 数据模型图

详见OFBizDatamodelBook_TOC_20171001.pdf

#### 1.2.2 通用实体概览

有一些文件与这些实体的定义一起出现。 这些文件位于基本 OFBiz 组件的数据模型组件中的“entitydef”文件夹中。 对于特殊用途的组件，实体定义位于特定组件的“entitydef”文件夹中 

* 可扩展性模式
  * 您会在 OFBiz 数据模型中看到一种经常重复的模式，这需要一些解释。 此模式用于使数据模型更加灵活，并消除大量原本可能必须存在的表。 它有助于概念化遵循此模式的实体的使用。 它还为使用此模式的实体提供了一种运行时可扩展性方法 
  * EntityType： EntityType 用于描述一个实体。 如果在给定 EntityType 实例的 parentTypeId 字段中指定了特性，则给定 EntityType 可以从父 EntityType 继承特性。 如果表与与 entityTypeId 字段值同名的给定 EntityType 实例相关联，则 hasTable 字段应具有值“Y”，否则应具有值“N”。 为 EntityType 实例的简短描述提供了描述字段。  
  * EntityAttribute： EntityAttribute 用于存储给定实体实例的名称-值对属性实例。 可以使用属性代替实体表上的列，尤其是当该属性不适用于所有类型的实体时。 属性也可以临时用于适用于给定实体实例的任何名称-值对信息。 如果许多属性适用于给定的 EntityType，最好创建一个单独的实体来保存这些属性，并通过将实体命名为与 entityTypeId 相同并将 hasTable 字段设置为“Y”来使该实体与 EntityType 实例相关联 EntityType 实例。 这将比重复查询给定实体实例的属性集合更快 
  * EntityTypeAttr： EntityTypeAttr 用于指定对应于给定 entityTypeId 的 Entity 实例应该存在哪些属性。 列出了 entityTypeId 和 name 字段组合来指定这一点。 例如，entityTypeId 为“BOX”的 EntityType 应始终具有名为“HEIGHT”、“WIDTH”、“DEPTH”和“COLOR”的属性，因此每个属性都将存在一个 EntityTypeAttr 实例。 对于某些实体，此模式存在变体，但此描述通常适用于此数据模型。 请注意，数据模型的默认类型种子数据在各种 *Data.xml 文件中，通常在每个组件的数据目录中 
  * 已弃用的实体： 以“Old”为前缀的实体已被弃用。通过显式指定相应的表名（参见实体定义中的表名属性），底层表名保持不变。  在显着更改实体时，尤其是更改主键时，请始终弃用现有实体并创建新实体，以便现有数据库的升级路径成为可能。为了促进这一点，除了弃用旧实体（以“旧”为前缀）和定义新实体之外，您应该始终创建一个服务来将数据从旧实体移动到新实体。该服务不必自己确定何时运行（这很难弄清楚并且通常不可靠），并且通常应该手动运行。 弃用实体时，请确保搜索对该实体的所有引用并将其更改为使用新实体。在流程结束时，对已弃用实体的唯一引用，当然还有“旧”前缀，应该是迁移服务。 当更改字段名称，或弃用和替换不需要弃用整个实体的字段时，请遵循相同的模式将旧字段留在那里：在字段名称中添加“旧”前缀，首先更改原始字段字母为大写，并指定字段的列名，使其与原始字段名相同。例如，如果您要将“uomId”字段更改为新名称，则将字段名称更改为“oldUomId”并指定“UOM_ID”列名。就像替换和实体时一样，确保编写一个服务来将数据从旧字段移动到新字段 
* 包结构
  * common：包含一般用途的实体，并且可以被许多其他包中的实体使用
  * party：
  * product： 包含与 Product 实体相关的实体。有许多类型的产品，以及与产品或产品相关联的大量数据，并且这些数据在此包中定义。产品可以是商品或服务，每一种都可以有不同的子类型。一些对产品定义相当重要的相关数据包括功能和类别。 Category 包允许组织产品，而 Feature 包描述了可用于定价或参数化产品搜索的产品详细信息。 产品包还包含有关序列化和非序列化库存项目的信息，以及库存在仓库、商店或其他设施中的存储。有一个用于产品成本估算的包，另一个用于产品价格计算。此外，还有一个产品供应商相关信息包，用于跟踪产品可能来自何处，并对同一产品的不同供应商进行评级和优先排序。  
  * security： 包含与控制对使用数据模型的应用程序的各个部分的访问相关的实体。 Login 包有一个 UserLogin 实体，它包含系统上所有用户的用户名和密码，并且可选地与 Party 实体实例相关。 Login 包还有一个实体，它保存登录到系统的历史记录。 

#### 1.2.3 数据文件



## 二、最佳实践指南

本文档介绍了与 OFBIZ 项目相关的开发和架构的最佳实践

### 2.1 通用概念

#### 2.1.1  降低代码复杂性、冗余和大小  

在 OFBiz 框架的各个地方使用的最佳实践是动态 API，例如实体和服务引擎，以及特殊用途的语言，例如工作流和规则引擎以及 MiniLang 库 

动态 API 模式的特点是具有简单操作的通用 API，这些操作根据配置和域定义文件表现不同。这些通常是 XML 文件。这是代码生成的替代方法，用于生成代码的输入或域描述文件通常可以不加改变地用于驱动动态 API，从而减少代码和更多动态的临时控制 

与 Java 等通用过程语言相比，专用语言用于以一种语言或使用更适合特定需求的工具来创建逻辑。这减少了代码，因为在与需要解决的问题密切匹配的上下文中描述您想要的内容比使用通用语言更容易。无需编写大量代码即可表达高级概念 

#### 2.1.2  通用与专用工件（Generic Versus Special Purpose Artifacts）

在框架的所有部分中，最佳实践是首先创建一个通用或通用的工件，如果它不存在的话。对于组织内特定角色或用于特殊目的的任何事物，最佳实践是使用 OFBiz 中存在的许多覆盖机制从通用的工件中派生出尽可能少的冗余代码的工件。在大多数情况下，这可以在不更改开源项目的任何基本代码的情况下完成，从而随着时间的推移更容易维护 

### 2.2 数据层

在数据层中使用的最佳实践工具是 OFBiz 实体引擎。对于大多数应用程序，实体引擎将优雅地完成 99% 的数据库交互需求。在实体引擎不够用的少数情况下，我建议为您的查询或其他命令使用自定义 JDBC 代码。这将是有时需要的次优实践之一 

使用实体引擎时，使用内联字符串引用实体和字段名称。这使得阅读和维护代码变得更加容易。如果你需要准备一个大的 Map 或 EntityCondition 来传递给一个 EE 方法，通常在单独的行上执行它，或者在实际的 EE 方法调用之前在不同的单独行上执行它

根据应用程序的需要使用简单的规范化数据模型。通常，数据模型可以直接由使用实体的功能需求驱动。这通常会产生一个高度规范化的数据模型，这将使您的生活更加轻松。当您需要组合数据进行报告时，可以使用视图实体功能来完成数据的连接、分组和汇总。 

当可以使用更具描述性的复合键时，请始终使用主键并避免使用通用的顺序主键。始终使用关系定义来记录实体如何一起使用，以便更轻松地获取相关数据，通过外键约束字段，并通过基于自动外键的索引来提高性能 

### 2.3 逻辑层

用于调用逻辑的最佳工具是 OFBiz 服务引擎。几乎所有的业务逻辑都应该作为服务来实现，以提高可重用性并促进基于组件的开发。

尽管服务非常灵活，但在某些情况下服务模型不合适，即使对于业务逻辑也是如此。在某些情况下，直接调用脚本或 Java 方法是必要的，在这些情况下，使用服务模型没有意义，也不应该使用。  

服务引擎通过提供多种方式来使用作为服务实现的逻辑来减少代码大小。您可以同步、异步或按计划调用您的逻辑。当你调用一个服务时，你不需要知道它的位置或它是如何实现的。这使得有效利用远程服务和使用不同语言编写的服务变得容易。能够通过服务引擎透明地调用不同语言的逻辑对于有效使用特殊用途语言很重要。 

在定义服务时，尽量使用接口服务或其他技术使它们尽可能简单。每当服务基于实体时，请务必使用 auto-attributes 标记从实体字段定义创建属性。此外，不要使用属性标记重新定义属性，而是使用覆盖标记并仅指定要更改的信息。 

始终使用最简单、最合适的语言或工具来实施您的服务。您可以使用许多不同的语言实现服务，包括 Java、Groovy、Beanshell 或任何符合 BSF 的脚本语言（Jython、Jacl、JavaScript 等）、OFBiz 工作流引擎流程、OFBiz MiniLang 简单方法和其他各种语言。通过编写简单的适配器可以支持其他语言。 

大多数服务都围绕数据映射和处理，简单方法是实现它们的最佳方式。使用简单方法编写服务是编写服务的主要最佳实践。在某些情况下，简单方法脚本过于严格或繁琐，在这些情况下应该使用更通用的语言。对于这种情况，推荐的两个次要最佳实践工具是 Groovy 和 Java，首选 Groovy 是因为它具有灵活性/可扩展性特性、无需重新编译来测试更改以及各种其他好处。 

始终通过服务引擎调用远程逻辑。您可以通过各种机制调用远程服务，包括 HTTP、SOAP、JMS 等。您还可以通过创建一个简单的适配器来添加远程服务调用机制。 

大多数时候，您希望让服务引擎自动将您的服务调用包装在事务中，以便整个事情成功，或者整个事情失败。请注意，如果您在现有事务中调用服务，它将识别当前事务并使用它，而不是尝试创建另一个事务。  

### 2.4 持久层

始终将输入处理逻辑、查看数据准备逻辑和查看演示模板分开。 这将使得不仅在 Web 应用程序中重用逻辑变得容易，而且对于独立的胖客户端应用程序也是如此。 在调试或探索组件如何工作时，它还将使组织代码和查找特定功能变得更加容易。 对于三个独立的部分中的每一个，都有特殊的最佳实践工具可供使用 

#### 2.4.1 输入处理逻辑

输入处理逻辑应始终与 controller.xml 文件中的请求相关联，而不应与视图相关联。输入处理逻辑通常应作为服务实现并通过服务事件处理程序调用，该服务事件处理程序将自动从请求参数或属性中提取数据并将其从字符串转换为服务定义中定义的对象类型。这使得仅使用服务定义就可以轻松指定您关心处理的参数，并让框架为您准备好它们 

在各种情况下，输入处理逻辑无法实现为服务。对于与请求相关联的逻辑，还有各种其他类型的事件处理程序，它们为您提供更多的请求上下文，并且不像服务那样与环境无关。一个例子是接收上传的数据。另一个很好的例子是在将参数传递给服务进行处理之前对参数进行特殊的预处理和验证。请注意，您始终可以从这些自定义事件中调用服务，并且应尽可能在服务中实现通用逻辑 

在给定事件结果字符串的情况下，始终让 Control Servlet 配置处理有关对请求采取适当响应的决策。在大多数情况下，响应将是视图的生成，但有时将请求链接在一起以实现逻辑重用或更高级的流控制是有意义的 

#### 2.4.2 视图数据准备逻辑

视图数据准备逻辑应始终与其准备数据的视图模板相关联。这应该通过屏幕定义 XML 文件中的 OFBiz 屏幕小部件通过指定脚本操作来完成。当一个屏幕被分成多个模板或屏幕时，数据准备操作应该只与它准备数据的单个小屏幕相关联。这使得移动模板和内容片段并在许多地方重用它们变得更加容易 

视图数据准备逻辑应指定为 XML 屏幕定义中的操作，或在必要时以动态脚本语言（如 Groovy、JavaScript、BeanShell、Jython 或 Jacl）实现，以便于动态修改用户界面。通用数据检索应实现为应从这些动态操作脚本调用的服务。这使得在多个页面和其他类型的用户界面中共享和重用此功能变得更加容易 

OFBiz 中数据准备的最佳实践是使用以下工具之一： 

*  用于简单的数据检索：屏幕操作部分中的 entity* 标签 
*  用于更复杂的数据准备和操作：简单方法或 Groovy 
*  为了更大的数据准备和更多的可重用性：实现服务并调用屏幕操作标签 

在视图操作中准备数据时，您应该通过将数据放在“上下文”对象中来使数据对视图模板可用。如果模板语言支持，则上下文对象中的所有属性都将在模板的上下文中可用。 

虽然在 OFBiz 中不建议使用 JSP，但它是受支持的。请注意，当使用 JSP 作为视图模板时，您不能使用 Screen Widget，因此操作工具将不可用。我们对 JSP 的建议是在页面顶部放置一个用于准备数据的 scriptlet。在这种情况下，尝试调用工作服务或工作 Java 方法来完成大部分工作并尽可能多地保留页面之外的逻辑 

#### 2.4.3 视图演示模板

我们推荐用于 HTML 和其他文本生成的最佳实践模板引擎是 FreeMarker。它类似于 Jakarta 的 Velocity，但更加灵活，并且可以很好地与其他 OFBiz Core Framework 工具配合使用。我们强烈建议不要直接运行 FreeMarker 模板，而是使用 OFBiz 屏幕小部件，以便操作可以与屏幕相关联，并且可以使用通用模板进行装饰。我们将在下面描述如何最好地使用它 

视图演示模板应始终保持尽可能简单，并且应在运行时使用装饰模式添加常见内容，例如页眉、页脚、侧边栏等。用于装饰每个页面的模板文件在屏幕定义 XML 文件中指定 

始终使用最符合您需求的视图生成工具。 FreeMarker 是生成文本输出的推荐工具，但在很多情况下其他工具更合适。如果您想使用其他文本生成工具，例如 Velocity 或 XSLT，我们建议您通过 Screen Widget 这样做，特别是如果您希望对其进行修饰并运行操作以准备模板 

当您想要显示类似视图的报告时，我们建议使用 Screen Widget 和 FreeMarker 来生成 XSL:FO XML 文件，并使用 Control Servlet 配置中的 Screen Widget FOP 视图处理程序将其转换为 PDF 以发送到客户端.这种方法允许您对这些报告使用与应用程序中的其他视图相同的工具，并提供基本上无限的灵活性。作为替代方案，您可以使用其他更传统的报告工具，例如 Jasper Reports 或 DataVision，并通过 Control Servlet 配置文件 (controller.xml) 中的视图映射安装这些报告 

如果您有频繁重复的 UI 模式，例如表单、查询数据显示、选项卡或菜单栏、扩展树视图等，我们目前建议使用 XML 文件来描述 UI 模式，然后将其转换为 HTML 或其他输出使用该特定 XML 格式的渲染引擎，或使用 XSLT/FreeMarker/etc 将其转换为所需的输出 

对于大多数表单，表示它们的最佳方式是使用 OFBiz 表单小部件，它接受 XML 中的通用表单定义，可用于多种用户界面类型，并利用基于 OFBiz 的代码中的现有资产，例如服务和实体定义。这导致代码更小且更易于维护 

当使用 FreeMarker 不可行或不实用时，我们建议使用另一种动态模板语言，例如 Velocity。当这也不可能或不可行时，我们建议使用 JSP。但是，请注意，当使用 JSP 时，您无法利用操作或装饰模板，因为您无法通过 JPublish 运行它。这要归功于 JSP 规范中的限制。即使您不能使用装饰器模式，您也可以将复合视图模式与 OFBiz 区域框架一起使用。区域在 region.xml 文件中指定。请注意，这些不像 Screen Widget 复合视图那样易于使用，并且它们不支持操作。但是 Regions 框架确实提供了很大的灵活性，并且在很多情况下都非常有用 

## 三、使用服务流处理器

服务流处理程序允许服务在接收来自客户端的请求时使用原始流。 InputStream 和 OutputStream 是唯一需要的参数。 开发使用流处理程序的服务时，实现该服务的最佳方式是使用静态 java 方法和 java 服务引擎。 

### 3.1 服务定义

 服务应始终实现 serviceStreamInterface，它提供了两个 IN 参数 inputStream 和 outputStream。 与往常一样，您可以在必要时覆盖这些参数。 唯一的 OUT 参数是 contentType。 这主要用于告诉 HttpServletResponse 对象响应类型是什么 

 **services.xml** 

```xml
<services>
    <!-- Routing service to handle all incoming messages and send them to the appropriate processing service -->
    <service name="oagisMessageHandler" engine="java" transaction-timeout="300"
        location="org.ofbiz.oagis.OagisServices" invoke="oagisMessageHandler" auth="false">
        <implements service="serviceStreamInterface"/>
        <implements service="oagisMessageIdOutInterface"/>
        <attribute name="isErrorRetry" type="Boolean" mode="IN" optional="true"/>
        <override name="outputStream" optional="true"/>
    </service>
 
    <!-- the interface found in framework/service/servicedef/services.xml -->
    <service name="serviceStreamInterface" engine="interface">
        <description>Inteface to describe services call with streams</description>
        <attribute name="inputStream" type="java.io.InputStream" mode="IN"/>
        <attribute name="outputStream" type="java.io.OutputStream" mode="IN"/>
        <attribute name="contentType" type="String" mode="OUT"/>
    </service>
</services>
```

### 3.2 服务实现

#### 3.2.1 读取InputStream

 服务实现简单； 就像任何其他 Java 服务一样。 唯一不同的是主要参数inputStream。 使用一行代码 

```java
InputStream in = (InputStream) context.get("inputStream");
```

#### 3.2.2 输出OutputStream

 OutputStream 又被写回客户端。 这是一个从调用者引用的 IN 参数。 只需从上下文中拉出 outputStream  

```java
OutputStream out = (OutputStream) context.get("outputStream");
```

 然后像往常一样写入流。 内容类型由调用者在流关闭之前设置。 

 注意：不要关闭服务中的 OutputStream； 这由调用者处理，调用者通常是 ServiceStreamHandler.java。  

代码示例：

 **OagisServices.java** 

```java
public static Map oagisMessageHandler(DispatchContext ctx, Map context) {
    GenericDelegator delegator = ctx.getDelegator();
    LocalDispatcher dispatcher = ctx.getDispatcher();
    InputStream in = (InputStream) context.get("inputStream");
    List errorList = FastList.newInstance();
    Boolean isErrorRetry = (Boolean) context.get("isErrorRetry");
 
    GenericValue userLogin = null;
    try {
        userLogin = delegator.findByPrimaryKey("UserLogin", UtilMisc.toMap("userLoginId", "system"));   
    } catch (GenericEntityException e) {
        String errMsg = "Error Getting UserLogin with userLoginId system: "+e.toString();
        Debug.logError(e, errMsg, module);
    }
     
    Document doc = null;
    String xmlText = null;
    try {
        BufferedReader br = new BufferedReader(new InputStreamReader(in, "UTF-8"));
        StringBuffer xmlTextBuf = new StringBuffer();
        String currentLine = null;
        while ((currentLine = br.readLine()) != null) {
            xmlTextBuf.append(currentLine);
            xmlTextBuf.append('\n');
        }
        xmlText = xmlTextBuf.toString();
         
        // DEJ20070804 adding this temporarily for debugging, should be changed to verbose at some point in the future
        Debug.logWarning("Received OAGIS XML message, here is the text: \n" + xmlText, module);
 
        ByteArrayInputStream bis = new ByteArrayInputStream(xmlText.getBytes("UTF-8"));
        doc = UtilXml.readXmlDocument(bis, true, "OagisMessage");
    } catch (SAXException e) {
        String errMsg = "XML Error parsing the Received Message [" + e.toString() +
            "]; The text received we could not parse is: [" + xmlText + "]";
        errorList.add(UtilMisc.toMap("description", errMsg, "reasonCode", "SAXException"));
        Debug.logError(e, errMsg, module);
    } catch (ParserConfigurationException e) {
        String errMsg = "Parser Configuration Error parsing the Received Message: " + e.toString();
        errorList.add(UtilMisc.toMap("description", errMsg, "reasonCode", "ParserConfigurationException"));
        Debug.logError(e, errMsg, module);
    } catch (IOException e) {
        String errMsg = "IO Error parsing the Received Message: " + e.toString();
        errorList.add(UtilMisc.toMap("description", errMsg, "reasonCode", "IOException"));
        Debug.logError(e, errMsg, module);
    }
 
    if (UtilValidate.isNotEmpty(errorList)) {
        return ServiceUtil.returnError("Unable to parse received message");
    }
 
    Element rootElement = doc.getDocumentElement();
    rootElement.normalize();
    Element controlAreaElement = UtilXml.firstChildElement(rootElement, "os:CNTROLAREA");
    Element bsrElement = UtilXml.firstChildElement(controlAreaElement, "os:BSR");
    String bsrVerb = UtilXml.childElementValue(bsrElement, "of:VERB");
    String bsrNoun = UtilXml.childElementValue(bsrElement, "of:NOUN");
     
    Element senderElement = UtilXml.firstChildElement(controlAreaElement, "os:SENDER");
    String logicalId = UtilXml.childElementValue(senderElement, "of:LOGICALID");
    String component = UtilXml.childElementValue(senderElement, "of:COMPONENT");
    String task = UtilXml.childElementValue(senderElement, "of:TASK");
    String referenceId = UtilXml.childElementValue(senderElement, "of:REFERENCEID");
     
    if (UtilValidate.isEmpty(bsrVerb) || UtilValidate.isEmpty(bsrNoun)) {
        return ServiceUtil.returnError("Was able to receive and parse the XML message, but BSR->NOUN [" + bsrNoun +
            "] and/or BSR->VERB [" + bsrVerb + "] are empty");
    }
     
    GenericValue oagisMessageInfo = null;
    Map oagisMessageInfoKey = UtilMisc.toMap("logicalId", logicalId, "component", component, "task", task, "referenceId", referenceId);
    try {
        oagisMessageInfo = delegator.findByPrimaryKey("OagisMessageInfo", oagisMessageInfoKey);
    } catch (GenericEntityException e) {
        String errMsg = "Error Getting Entity OagisMessageInfo: " + e.toString();
        Debug.logError(e, errMsg, module);
    }
     
    Map messageProcessContext = UtilMisc.toMap("document", doc, "userLogin", userLogin);
     
    // call async, no additional results to return: Map subServiceResult = FastMap.newInstance();
    if (UtilValidate.isNotEmpty(oagisMessageInfo)) {
        if (Boolean.TRUE.equals(isErrorRetry) || "OAGMP_SYS_ERROR".equals(oagisMessageInfo.getString("processingStatusId"))) {
            // there was an error last time, tell the service this is a retry
            messageProcessContext.put("isErrorRetry", Boolean.TRUE);
        } else {
            String responseMsg = "Message already received with ID: " + oagisMessageInfoKey;
            Debug.logError(responseMsg, module);
 
            List errorMapList = UtilMisc.toList(UtilMisc.toMap("reasonCode", "MessageAlreadyReceived", "description", responseMsg));
 
            Map sendConfirmBodCtx = FastMap.newInstance();
            sendConfirmBodCtx.put("logicalId", logicalId);
            sendConfirmBodCtx.put("component", component);
            sendConfirmBodCtx.put("task", task);
            sendConfirmBodCtx.put("referenceId", referenceId);
            sendConfirmBodCtx.put("errorMapList", errorMapList);
            sendConfirmBodCtx.put("userLogin", userLogin);
 
            try {
                // run async because this will send a message back to the other server and may take some time, and/or fail
                dispatcher.runAsync("oagisSendConfirmBod", sendConfirmBodCtx, null, true, 60, true);
            } catch (GenericServiceException e) {
                String errMsg = "Error sending Confirm BOD: " + e.toString();
                Debug.logError(e, errMsg, module);
            }
            Map result = ServiceUtil.returnSuccess(responseMsg);
            result.put("contentType", "text/plain");
            return result;
        }
    }
     
    Debug.logInfo("Processing OAGIS message with verb [" + bsrVerb + "] and noun [" +
        bsrNoun + "] with context: " + messageProcessContext, module);
     
    if (bsrVerb.equalsIgnoreCase("CONFIRM") && bsrNoun.equalsIgnoreCase("BOD")) {
        try {
            // subServiceResult = dispatcher.runSync("oagisReceiveConfirmBod", messageProcessContext);
            dispatcher.runAsync("oagisReceiveConfirmBod", messageProcessContext, true);
        } catch (GenericServiceException e) {
            String errMsg = "Error running service oagisReceiveConfirmBod: " + e.toString();
            errorList.add(UtilMisc.toMap("description", errMsg, "reasonCode", "GenericServiceException"));
            Debug.logError(e, errMsg, module);
        }
    } else if (bsrVerb.equalsIgnoreCase("SHOW") && bsrNoun.equalsIgnoreCase("SHIPMENT")) {
        try {
            //subServiceResult = dispatcher.runSync("oagisReceiveShowShipment", messageProcessContext);
            // DEJ20070808 changed to run asynchronously and persisted so that if it fails it will retry;
            // for transaction deadlock and other reasons
            dispatcher.runAsync("oagisReceiveShowShipment", messageProcessContext, true);
        } catch (GenericServiceException e) {
            String errMsg = "Error running service oagisReceiveShowShipment: " + e.toString();
            errorList.add(UtilMisc.toMap("description", errMsg, "reasonCode", "GenericServiceException"));
            Debug.logError(e, errMsg, module);
        }
    } else if (bsrVerb.equalsIgnoreCase("SYNC") && bsrNoun.equalsIgnoreCase("INVENTORY")) {
        try {
            //subServiceResult = dispatcher.runSync("oagisReceiveSyncInventory", messageProcessContext);
            // DEJ20070808 changed to run asynchronously and persisted so that if it fails it will retry;
            // for transaction deadlock and other reasons
            dispatcher.runAsync("oagisReceiveSyncInventory", messageProcessContext, true);
        } catch (GenericServiceException e) {
            String errMsg = "Error running service oagisReceiveSyncInventory: " + e.toString();
            errorList.add(UtilMisc.toMap("description", errMsg, "reasonCode", "GenericServiceException"));
            Debug.logError(e, errMsg, module);
        }
    } else if (bsrVerb.equalsIgnoreCase("ACKNOWLEDGE") && bsrNoun.equalsIgnoreCase("DELIVERY")) {
        Element dataAreaElement = UtilXml.firstChildElement(rootElement, "ns:DATAAREA");
        Element ackDeliveryElement = UtilXml.firstChildElement(dataAreaElement, "ns:ACKNOWLEDGE_DELIVERY");
        Element receiptlnElement = UtilXml.firstChildElement(ackDeliveryElement, "ns:RECEIPTLN");
        Element docRefElement = UtilXml.firstChildElement(receiptlnElement, "os:DOCUMNTREF");
        String docType = docRefElement != null ? UtilXml.childElementValue(docRefElement, "of:DOCTYPE") : null;
        String disposition = UtilXml.childElementValue(receiptlnElement, "of:DISPOSITN");
         
        if ("PO".equals(docType)) {
            try {
                //subServiceResult = dispatcher.runSync("oagisReceiveAcknowledgeDeliveryPo", messageProcessContext);
                dispatcher.runAsync("oagisReceiveAcknowledgeDeliveryPo", messageProcessContext, true);
            } catch (GenericServiceException e) {
                String errMsg = "Error running service oagisReceiveAcknowledgeDeliveryPo: " + e.toString();
                errorList.add(UtilMisc.toMap("description", errMsg, "reasonCode", "GenericServiceException"));
                Debug.logError(e, errMsg, module);
            }
        } else if ("RMA".equals(docType)) {
            try {
                //subServiceResult = dispatcher.runSync("oagisReceiveAcknowledgeDeliveryRma", messageProcessContext);
                dispatcher.runAsync("oagisReceiveAcknowledgeDeliveryRma", messageProcessContext, true);
            } catch (GenericServiceException e) {
                String errMsg = "Error running service oagisReceiveAcknowledgeDeliveryRma: " + e.toString();
                errorList.add(UtilMisc.toMap("description", errMsg, "reasonCode", "GenericServiceException"));
                Debug.logError(e, errMsg, module);
            }
        } else if (UtilValidate.isEmpty(docType) && ("NotAvailableTOAvailable".equals(disposition) ||
                   "AvailableTONotAvailable".equals(disposition))) {
            try {
                dispatcher.runAsync("oagisReceiveAcknowledgeDeliveryStatus", messageProcessContext, true);
            } catch (GenericServiceException e) {
                String errMsg = "Error running service oagisReceiveAcknowledgeDeliveryStatus: " + e.toString();
                errorList.add(UtilMisc.toMap("description", errMsg, "reasonCode", "GenericServiceException"));
                Debug.logError(e, errMsg, module);
            }
        } else {
            return ServiceUtil.returnError("For Acknowledge Delivery message could not determine if it is for a PO " +
                "or RMA or Status Change. DOCTYPE from message is [" + docType + "], DISPOSITN is [" + disposition + "]");
        }
    } else {
        String errMsg = "Unknown Message Type Received, verb/noun combination not supported: verb=[" + bsrVerb + "], noun=[" + bsrNoun + "]";
        Debug.logError(errMsg, module);
        return ServiceUtil.returnError(errMsg);
    }
     
    Map result = ServiceUtil.returnSuccess();
    result.put("contentType", "text/plain");
 
    /* no sub-service error processing to be done here, all handled in the sub-services:
    result.putAll(subServiceResult);
    List errorMapList = (List) subServiceResult.get("errorMapList");
    if (UtilValidate.isNotEmpty(errorList)) {
        Iterator errListItr = errorList.iterator();
        while (errListItr.hasNext()) {
            Map errorMap = (Map) errListItr.next();
            errorMapList.add(UtilMisc.toMap("description", errorMap.get("description"), "reasonCode", errorMap.get("reasonCode")));
        }
        result.put("errorMapList", errorMapList);
    }
    */
     
    return result;
}
```

#### 3.2.3 配置controller入口

*  确保将 ServiceStreamHandler 设置为事件处理程序之一 
*  配置您的事件（挂载点）以使用此处理程序 

 **controller.xml** 

```xml
<handler name="stream" type="request" class="org.ofbiz.webapp.event.ServiceStreamHandler"/>
...
<request-map uri="oagisMessageHandler">
    <security https="true" auth="false" cert="true"/>
    <event type="stream" invoke="oagisMessageHandler"/>
    <response name="success" type="none"/>
</request-map>
```

## 四、Control Servlet

该控制器利用了几种 J2EE 表示层设计模式。上下文安全过滤器仿照装饰过滤器模式。 CSF 在上下文根中运行，能够限制对 JSP 模板的直接访问以及为将来的服务（例如调试、日志记录和压缩）打开大门。 Control Servlet 是在 Front Controller 模式之后建模的。这个 Servlet 是 Web 应用程序的请求处理开始的地方。它使用处理安全和事件的辅助类，然后分派到定义的视图。视图是 JSP 模板，与复合视图模式中描述的模板非常相似。这些模板使用帮助文件来限制在实际显示页面中找到的逻辑数量。这些帮助文件是执行特定视图组逻辑的 Java 类。此过程基于 View Helper 模式。 

如上所述，Controller 的主要目的是将逻辑与显示分离。 这是通过以下方式完成的：

* 使用过滤器保护 Web 应用程序中的 JSP 模板     
* 使用 Servlet 管理应用程序流     
* 使用事件（命令）和视图助手进行表示逻辑  

### 4.1 Context Security Filter（CSF）

/WEB-INF/web.xml 中定义的上下文安全过滤器 (CSF) 用于限制对 Web 应用程序文件的访问。将来它可能用于调试和/或记录请求。这是对应用程序的所有 Web 请求的起点。默认情况下，所有路径都被拒绝，并且只有明确定义的路径才允许直接访问。通过在“允许列表（allow list）”中设置 Servlet 的挂载点，CSF 被设置为允许对控制 Servlet 的所有请求。允许列表定义为名为 allowedPaths 的 init-param。该值是由冒号分隔的单个路径字符串。示例 webapps 允许 '/control:/index.html:/index.jsp:/default.html:/default.jsp:/images' 路径。路径可以是目录名（从 webapp 的根目录开始）或特定文件的路径。

示例：

*  '/images' 将允许直接访问 /images 目录中的所有文件 
*  “/site-pages/contactus.html”将只允许直接访问 /site-pages 目录中的 contactus.html 文件 
*  '/site-pages/info/*' 将只允许直接访问子目录 'info' 中的文件 

当对受保护路径发出直接请求时，过滤器将执行以下两项操作之一。一，过滤器可以将用户重定向到 web.xml 中定义的页面。这是通过将 init-param redirectPath 设置为格式正确的 URL 来定义的。二、过滤器会抛出一个服务器错误，这个错误可以由 init-param errorCode 定义。仅当未定义重定向时才会引发错误。如果没有定义 errorCode，过滤器将抛出 404 服务器错误。  

配置示例：

```xml
<filter> 
  <filter-name>ContextSecurityFilter</filter-name>
  <display-name>ContextSecurityFilter</display-name> 
  <filter-class>org.ofbiz.webapp.control.ContextSecurityFilter</filter-class>    
  <init-param> 
    <param-name>allowedPaths</param-name> 
    <param-value>/control:/index.html:/index.jsp:/default.html:/default.jsp:/images</param-value>    
  </init-param> 
  <init-param> 
    <param-name>errorCode</param-name>    
    <param-value>403</param-value> 
  </init-param> 
</filter>    
<filter-mapping> 
  <filter-name>ContextSecurityFilter</filter-name>    
  <url-pattern>/*</url-pattern> 
</filter-mapping> 

```

### 4.2 Control Servlet

Control Servlet 是所有请求处理的核心。通过上下文安全过滤器的有效请求在此处开始处理。当收到请求时，Control Servlet 首先为帮助类（helper classes）设置一个环境。此环境包括（但不限于）设置初始会话对象并存储有关初始请求的有用信息以及设置对实体委托者、服务调度程序和安全处理程序的引用以供帮助程序类使用。然后将请求传递给请求处理程序进行处理。请求处理程序处理请求并在完成后返回到 ControlServlet。 

首次加载 Control Servlet 时，它将创建 Web 应用程序使用的对象并将它们存储在应用程序上下文 (ServletContext) 中。可以通过访问上下文中的属性或通过 JSP <useBean> 标记找到这些对象。这些对象包括：Entity Delegator、Security 对象、Service Dispatcher 和 Request Handler。  



### 4.3 Request Handler

Request Handler 使用 RequestManager 帮助类来收集在 XML 配置文件中定义的请求映射列表。配置文件位于 /WEB-INF/controller.xml 中以用于适当的上下文。该映射由一个请求 URI 和一个可选的 VIEW 名称组成。视图名称也映射到配置文件中。请求 URI 也可以与事件相关联。事件用于处理与 Web 相关的逻辑，方法是通过 Entity Delegator 直接使用 Entity Engine 或调用服务以通过 Service Dispatcher 处理逻辑。 

当通过请求处理程序接收到请求时，首先在请求映射中查找它；如果未找到映射，则返回“未知请求错误”。成功查找后，将调用安全处理程序以验证当前请求是否需要身份验证，以及发出请求的用户是否已正确验证。如果当前用户未通过身份验证，则请求处理程序将请求重定向到正确的登录表单。在成功验证或不需要验证后，请求处理程序会查找请求的已定义事件。如果找到事件，则将请求传递给 EventHandler 进行处理。事件处理完成后，将为此请求查找默认视图，除非 EventHandler 请求特定视图。然后在视图映射中检查视图，并将值传递给定义的 ViewHandler 以进行调度。如果视图类型为 none，则期望事件自己处理响应；如果视图类型是 url，则 RequestHandler 会重定向到指定的 URL，并且不会调用 ViewHandler。  

request handler配置示例

```xml
 <request-map uri="checkLogin" edit="false">
    <description>Verify a user is logged in.</description>
    <security https="false" auth="false"/>
    <event type="java" path="org.ofbiz.commonapp.security.login.LoginEvents" invoke="checkLogin" />
    <response name="success" type="view" value="name" />
    <response name="error" type="view" value="login" />
  </request-map>
```

在上述映射中，事件返回码成功将加载名为 name的视图。 如果成功是用请求类型定义的，那么它将 [***] 而不是显示视图，而是调用另一个请求。 这称为请求链。 视图映射定义如下： 

```xml
  <view-map name="login" page="/login.jsp"/>
```

### 4.4 Event Handler

event handler在xml配置文件里定义，例如：

```xml
 <handler name="java" type="request" class="org.ofbiz.webapp.event.JavaEventHandler"/>
```

与上面的请求映射一样，登录事件是使用 java类型定义的。 事件类型使用处理程序定义映射到事件处理程序。 可以设计自定义事件处理程序，并可以像上面的示例那样实现。

 Java事件通过定位事件的路径（包和类名）来处理； 然后，通过使用反射 API，事件处理程序调用定义的方法。 一个 String 对象返回到请求处理程序，该请求处理程序映射到请求定义的响应元素。 

### 4.5 View Handler

视图处理程序的定义就像事件处理程序一样，除了类型是视图 

```xml
 <handler name="region" type="view" class="org.ofbiz.webapp.view.RegionViewHandler"/>
```

视图处理程序处理呈现用户将看到的下一页。 默认视图处理程序通过分派到视图定义中定义的页面来支持标准 html/jsp 页面。 区域和速度等其他视图处理程序使用特殊逻辑将页面呈现给用户。 

通过实现 ViewHandler 接口并在 controller.xml 文件中设置定义，可以轻松创建自定义视图处理程序，如上例所示。  