# OFBIZ服务引擎

## 介绍

* 服务可以调用其他服务，因此将一些小的服务串联起来完成大型任务使得复用现有服务更加容易
* 在ofbiz-component.xml文件中声明的服务可以从OFBIZ的任何位置访问，甚至可以使用导出功能从外部访问
* 在web应用程序中使用时，web事件可以调用服务，这允许事件保持较小，并重用服务框架中的现有逻辑
* 服务可以被标记为"exportable"以供第三方调用

## Service Dispatcher

* 服务调度器将服务分派到相应的服务引擎，然后在相应的地方调用服务
* 每个Entity Delegator只有一个Service Dispatcher
* 一个Local Dispatcher和一个应用程序关联，但应用程序永远不会直接和ServiceDispatcher交互
* LocalDispatcher包含了一个调用服务的API，它们由ServiceDispatcher路由

## Dispatcher Context

* dispatcher context由local dispatcher在实例化时创建，是运行时的dispatcher context
* dispatcher context包含了为每个调度器处理服务所需的信息
* context上下文包含对每个服务定义文件的引用、应用于调用的类加载器、对委托者及其分派器的引用以及用户定义属性的“包”。该上下文在调用时传递给每个服务，并由调度器用于确定服务的模型

## Service Engine

* Service Engine是服务实际调用的地方，每一个服务有一个在其定义中分配name的engine
* engine name通过serviceengine.xml映射，并在调用时由GenericEngineFactory实例化

## Job Scheduler

* scheduler是一个多线程组件，其中一个线程用于作业管理/调度，另一个线程用于调用每一个服务
* 当一个作业计划运行时，scheduler会调用与该作业关联的service dispatcher以在其自己的线程中调用该服务
* 这将防止长时间或者耗时的作业减慢队列中其他作业的运行速度
* scheduler的最佳用法是使用异步服务调用；当一个异步服务被调用时，它被传递到job scheduler以便排队运行

## Service Definition

* service在service definition文件中定义

* 定义service时需要使用唯一的name，与特定的服务引擎关联，并且输入和输出参数都是显式定义的，例如：

  ```xml
  <service name="userLogin" engine="java" location="org.ofbiz.commonapp.security.login.LoginServices" invoke="userLogin">
      <description>Authenticate a username/password; create a UserLogin object</description>
      <attribute name="login.username" type="String" mode="IN"/>
      <attribute name="login.password" type="String" mode="IN"/>
      <attribute name="userLogin" type="org.ofbiz.entity.GenericValue" mode="OUT" optional="true"/>
  </service>
  ```

* service中的元素

  | 属性名                  | 必需 | 描述                                                         | 默认值        |
  | ----------------------- | ---- | ------------------------------------------------------------ | ------------- |
  | name                    | Y    | service唯一的name                                            |               |
  | engine                  | Y    | engine的name（在serviceengine.xml中定义）                    |               |
  | location                | N    | service class的包位置                                        |               |
  | invoke                  | N    | 调用的service的方法名                                        |               |
  | auth                    | N    | service是否需要权限                                          | true          |
  | debug                   | N    | 调用此服务时是否启用详细调试                                 | true          |
  | default-entity-name     | N    | 用于自动属性的默认实体                                       |               |
  | export                  | N    | service是否被允许通过SOAP/HTTP/JMS访问                       | false         |
  | max-retry               | N    | service重试次数                                              | -1（无限制）  |
  | require-new-transaction | N    | service需要新事务                                            | true          |
  | semaphore               | N    | 定义并发调用service时的处理方式：none-对该服务的多个调用可能会同时运行；wait-当service运行时，阻塞任何后续调用到队列里；fail-当service运行时，丢弃任何后续调用 | none          |
  | semaphore-wait-seconds  | N    | 当semaphore="wait"时，丢弃后续调用前的等待时间（second）     | 300           |
  | semaphore-sleep         | N    | 当semaphore="wait"时，检查后续调用是否可以被运行的时间间隔（millisecond） | 500           |
  | transaction-timeout     | N    | 重写默认事务的超时时间，只在启用事务时生效                   | 0（系统默认） |
  | use-transcation         | N    | 为此service创建事务（如果尚未创建事务）                      | true          |
  | validate                | N    | 是否验证下面找到的用于名称和类型匹配的属性                   | true          |

* implements中的元素
  
  * service：需要实现的service的name，所有属性都是被继承的
* attribute element
  * name：属性名
  * type：属性字段类型（String, java.util.Date, etc）
  * mode：输入或输出参数还是都有（IN/OUT/INOUT）
  * optional：参数是否可选（true/false），默认false
* 例如在上述的userLogin中，指定了两个IN参数：login.username和login.password。需要的参数在调用服务前会被测试，如果参数不匹配name或类型，服务就不会被调用。那些不一定被发送到service的参数应该被定义为optional。当service被调用后，OUT参数也会被测试，且只会测试必需的参数。但是如果传递了一个未定义为可选或必需的参数，则会导致服务失败。此service不需要OUT参数，因此只返回结果

## Automatic Entity Maintenance Service Definition

* OFBIZ能够自动定义简单的CRUD操作，对于许多实体来说

* 将engine属性设置为"entity-auto"，并将invoke属性设置为"create"，"update"或者"delete"，例如：

  ```xml
  <service name="createInvoiceContactMech" engine="entity-auto" invoke="create" default-entity-name="InvoiceContactMech">
      <description>Create a ContactMech for an invoice</description>
      <permission-service service-name="acctgInvoicePermissionCheck" main-action="CREATE"/>
      <auto-attributes include="pk" mode="IN" optional="false"/>
  </service>
  ```

* "entity-auto"engine可以实现下述的CREATE操作：
  * a single OUT primary key for primary auto-sequencing
  * a single INOUT primary key for primary auto-sequencing with optional override
  * a multi-part primary key with all parts part IN except the last which is  OUT only (the missing primary key is a sub-sequence mainly for entity  association
  * all primary key fields IN for a manually specified primary key

* 对于任何更复杂的情况，为您自己的服务编写代码，而不是依赖自动定义的服务

## Interfaces

* 已实现接口服务引擎以帮助定义共享许多相同参数的服务。接口服务不能被调用，而是其他服务继承自定义的服务。每个接口服务都将使用接口引擎定义，例如

  ```xml
  <service name="testInterface" engine="interface" location="" invoke="">
      <description>A test interface service</description>
      <attribute name="partyId" type="String" mode="IN"/>
      <attribute name="partyTypeId" type="String" mode="IN"/>
      <attribute name="userLoginId" type="org.ofbiz.entity.GenericValue" mode="OUT" optional="true"/>
  </service>
  ```

* 接下来实现接口：

  ```xml
  <service name="testExample1" engine="simple" location="org/ofbiz/commonapp/common/SomeTestFile.xml" invoke="testExample1">
      <description>A test service which implements testInterface</description>
      <implements service="testInterface"/>
  </service>
  ```

* testExample1 服务将具有与 testInterface 服务完全相同的必需和可选属性。任何实现 testInterface 的服务也将继承参数/属性。如果需要，可以通过将属性标记与实施标记一起添加到特定服务中来添加其他属性。您还可以通过在 implements 标记之后重新定义属性来覆盖属性

## ECA（Event Condition Action）

* ECA很像一个触发器，当一个服务被调用时，将执行查找以查看是否为此事件定义了任何ECA

* 事件包括身份验证之前、IN 参数验证之前、实际服务调用之前、OUT 参数验证之前、事务提交之前或服务返回之前

* 接下来评估 ECA 定义中的每个条件，如果所有条件返回为真，则执行每个操作

* 动作只是一个服务，必须定义它以使用服务上下文中已经存在的参数。每个 ECA 可以定义的条件或操作的数量没有限制

  ```xml
  <service-eca>
      <eca service="testScv" event="commit">
          <condition field-name="message" operator="equals" value="12345"/>
          <action service="testBsh" mode="sync"/>
      </eca>
  </service-eca>
  ```

  **请注意，您可以使用 set 操作来重命名触发服务的参数或插入值，例如**

  ```xml
  <eca service="setCustRequestStatus" event="commit">
      <condition field-name="oldStatusId" operator="not-equals" value="CRQ_ACCEPTED"/>
      <condition field-name="statusId" operator="equals" value="CRQ_ACCEPTED"/>
      <set field-name="bodyParameters.custRequestId" env-name="custRequestId"/>
      <set field-name="bodyParameters.custRequestName" env-name="custRequestName"/>
      <set field-name="partyIdTo" env-name="fromPartyId"/>
      <set field-name="emailTemplateSettingId" value="CUST_REQ_ACCEPTED"/>
      <action service="sendMailFromTemplateSetting" mode="sync" />
  </eca>
  ```

* ECA标签

  | 属性名       | 必需 | 描述                                                         |
  | ------------ | ---- | ------------------------------------------------------------ |
  | service      | Y    | ECA绑定的服务名                                              |
  | event        | Y    | 此 ECA 将运行的事件可以是（之前）：auth、in-validate、out-validate、invoke、commit 或 return |
  | run-on-error | N    | ECA在服务出错时是否继续运行，默认false                       |

* Contidion标签

  | 属性名     | 必需 | 描述                                                         |
  | ---------- | ---- | ------------------------------------------------------------ |
  | map-name   | N    | 包含要验证的字段将来自的映射的服务上下文字段的名称。如果未指定，则字段名称将被视为服务上下文字段名称（环境名称） |
  | field-name | Y    | 将被比较的映射字段的名称                                     |
  | operator   | Y    | 指定比较运算符必须是下列之一：less、greater、less-equals、greater-equals、equals、not-equals 或 contains |
  | value      | Y    | 该字段将与之比较的值。必须是字符串，但可以转换为其他类型     |
  | type       | N    | 用于比较的数据类型。必须是以下之一：String、Double、Float、Long、Integer、Date、Time 或 Timestamp。如果未指定类型，则默认为 String |
  | format     | N    | 将 String 对象转换为其他数据类型时使用的格式说明符，主要是 Date、Time 和 Timestamp |

* Condition-field 标签

  | 属性名        | 必需 | 描述                                                         |
  | ------------- | ---- | ------------------------------------------------------------ |
  | map-name      | N    | 包含要验证的字段将来自的映射的服务上下文字段的名称。如果未指定，则字段名称将被视为方法环境字段名称（环境名称） |
  | field-name    | Y    | 将被比较的映射字段的名称                                     |
  | operator      | Y    | 指定比较运算符必须是下列之一：less、greater、less-equals、greater-equals、equals、not-equals 或 contains |
  | to-map-name   | N    | 包含要比较的字段将来自的映射的服务上下文字段的名称。如果留空将默认为映射名称，如果映射名称也未指定，则默认为方法环境。 |
  | to-field-name | N    | 将与主字段进行比较的 to-map 字段的名称。如果留空将默认为字段名称 |
  | type          | N    | 用于比较的数据类型。必须是以下之一：String、Double、Float、Long、Integer、Date、Time 或 Timestamp。如果未指定类型，则默认为 String |
  | format        | N    | 将 String 对象转换为其他数据类型时使用的格式说明符，主要是 Date、Time 和 Timestamp |

  

* Action标签

  | 属性名            | 必需 | 描述                                                         |
  | ----------------- | ---- | ------------------------------------------------------------ |
  | service           | N    | 将被调用的服务名                                             |
  | mode              | Y    | 将被调用服务的模式，可以是同步或者异步的。请注意，即使设置为 true，异步操作也不会更新上下文 |
  | result-to-context | N    | action-service的结果是否应该更新main service的context，默认true |
  | result-to-result  | N    | action-service的结果是否应该更新main service的result，如果为true，则操作服务的消息将附加到主服务的消息中，默认false |
  | ignore-error      | N    | 忽略由action-service引起的任何错误，如果为 true，则错误将导致原始服务失败。默认为true |
  | persist           | N    | action-service store/run，仅在mode为async时有效，默认为false |

## Service Groups

* 服务组是一组在调用初始服务时应该运行的服务。您使用组服务引擎定义服务，并包括组中所有服务所需的所有参数/属性。组服务不需要 location 属性，invoke 属性定义要运行的组的名称。当这个服务被调用时，组被调用并且组中定义的服务被调用

* 组定义非常简单，它包含一个组元素以及一个或多个服务元素。 group 元素包含一个 name 属性和一个 mode 属性，用于定义组的功能。 service 元素与 ECA 中的 action 元素非常相似，不同之处在于 result-to-context 的默认值

  ```xml
  <service-group>
      <group name="testGroup" send-mode="all">
          <invoke name="testScv" mode="sync"/>
          <invoke name="testBsh" mode="sync"/>
      </group>
  </service-group>
  ```

  

* Group tag

| **Attribute Name** | **Required?** | **Description**                                              |
| ------------------ | ------------- | ------------------------------------------------------------ |
| name               | Y             | The name of the service this action will invoke.             |
| send-mode          | N             | The mode in which the service(s) should be invoked. The options are: none,  all, first-available, random, or round-robin. The default is all. |

* Invoke tag

| **Attribute Name** | **Required?** | **Description**                                              |
| ------------------ | ------------- | ------------------------------------------------------------ |
| name               | N             | The name of the service this action will invoke.             |
| mode               | Y             | The mode in which this service should be invoked. Can be sync or async.  Note that async actions will not update the context even when set to  true. |
| result-to-context  | N             | Should the results                                           |