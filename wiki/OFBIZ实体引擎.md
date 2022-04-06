# OFBIZ实体引擎
## 一、实体引擎设计目的及思路
* 目的：在事务应用程序的尽可能多的区域中消除对实体特定持久性代码的需求
* 思路：所有值对象都是通用的（generic），使用映射（map）按名称来储存实体实例的字段值。字段的 get 和 set 方法接受一个带有 fieldName 的字符串，用于验证该字段是实体的一部分，然后根据需要获取或设置一个值

## 二、实体定义方式
* 实体定义在XML文件中，而非使用特定代码定义
* 这些XML实体定义指定了所有实体的名称及其字段和它们对应的数据库的表和列
* 它们还用于为每个字段指定类型，然后在给定数据源的字段类型文件中查找该类型以查找Java和SQL数据类型
* 实体间的关系同样在XML文件中定义
* 通过指定相关表来定义一个关系

## 三、实体模型
* OBFIZ实体引擎通过2个XML文件来运作，一个是实体模型XML，另一个是模型字段类型XML
* 之所以要把实体定义分成上述两部分，是因为字段类型的定义在不同的数据库中可能是不同的
* OFBIZ的主要实体模型在framework/common/entitydef下定义
* MySQL字段类型模型XML文件路径为framework/entity/fieldtype/fieldtypemysql.xml
* 实体定义实例
```xml
  <entity title="Sample Entity"
            copyright="Copyright (c) 2001 John Doe Enterprises"
            author="John Doe" version="1.0"
            package-name="org.ofbiz.commonapp.sample"
            entity-name="SampleEntity"
            table-name="SAMPLE_ENTITY">
      <field name="primaryKeyFieldOne" col-name="PRIMARY_KEY_FIELD_ONE" type="id"></field>
      <field name="primaryKeyFieldTwo" type="id"></field>
      <field name="fieldOne" type="long-varchar"></field>
      <field name="fieldTwo" type="long-varchar"></field>
      <field name="foreignKeyOne" type="id"></field>
      <prim-key field="primaryKeyFieldOne" />
      <prim-key field="primaryKeyFieldTwo" />
      <relation type="one" rel-entity-name="OtherSampleEntity">
        <key-map field-name="foreignKeyOne" rel-field-name="primaryKeyOne" />
      </relation>
      <relation type="one" title="Self" rel-entity-name="SampleEntity">
        <key-map field-name="primaryKeyFieldOne" />
        <key-map field-name="primaryKeyFieldTwo" />
      </relation>
      <relation type="many" title="AllOne" rel-entity-name="SampleEntity">
        <key-map field-name="primaryKeyFieldOne" />
      </relation>
    </entity>
```
* "package-name"用于组织实体，指定代码位置
* "type"对应fieldtype*.xml中的Java字段和数据库字段类型映射
* "col-name"和"table-name"元素是可选的
* 表明和列名用大写字母书写，并用下划线分隔
* 实体和字段名称用Java约定编写，例如实体SampleEntity对应SAMPLE_ENTITY
* 可以使用多个指定主键字段名称的 <prim-key> 标记来指定多个主键列

## 四、实体间关系
* 根据关系的基数及其是否需要外键，每个关系都是"one", "one-nofk"或"many"类型
* 如果类型是"many"，"key-map"字段元素就不会被相关实体的主键限制
* 外键和外键的索引在实体引擎中会被自动创建，只会在"one"关系中生效

## 五、实体元素

* entity

  | 属性名       | 必需 | 描述                                                         |
  | ------------ | ---- | ------------------------------------------------------------ |
  | entity-name  | Y    | 使用实体引擎 Java API 和实体引擎中的各种其他位置时引用的实体名称 |
  | package-name | Y    | 实体所在包名                                                 |
  | table-name   | N    | 实体对应的数据库表名，如果未指定则由实体名获取               |
  | dependent-on | N    | 用于指定父实体或依赖的实体，可用于指定分层实体结构           |
  | enable-lock  | N    | 指定是否应该对此实体使用乐观锁                               |
  | never-cache  | N    | 默认false，不允许对实体进行缓存                              |
  | title        | N    | 实体标题                                                     |
  | copyright    | N    | 版权                                                         |
  | author       | N    | 作者                                                         |
  | version      | N    | 版本                                                         |

  | 子元素名    | 数量   | 描述           |
  | ----------- | ------ | -------------- |
  | description | 0 or 1 | 用于描述实体   |
  | field       | >=1    | 声明实体字段   |
  | prim-key    | 不限   | 声明实体主键   |
  | relation    | 不限   | 声明实体间关系 |

  

* field

  | 属性名   | 必需 | 描述                                         |
  | -------- | ---- | -------------------------------------------- |
  | name     | Y    | 用于生成Java代码的字段名                     |
  | type     | Y    | 字段类型，决定字段的Java类型和数据库字段类型 |
  | col-name | N    | 相应数据库列的名称                           |

  | 子元素名 | 数量 | 描述                                                         |
  | -------- | ---- | ------------------------------------------------------------ |
  | validate | 不限 | 每个 validate 元素都有一个名为 name 的属性，它指定要调用的验证方法的名称。这些方法不会在所有实体引擎操作中调用，仅用于通用用户界面，如 WebTools 中的实体数据维护页面 |

  

* prim-key

  | 属性名 | 必需 | 描述   |
  | ------ | ---- | ------ |
  | field  | Y    | 主键名 |

  

* relation

  | 属性名          | 必需 | 描述                                                         |
  | --------------- | ---- | ------------------------------------------------------------ |
  | type            | Y    | 指定关系的类型，包括关系的基数（在一个方向上）以及是否应为基数一关系创建外键。必须是“one”、“one-nofk”或“many” |
  | rel-entity-name | Y    | 相关实体的名称。关系从该实体到相关实体                       |
  | title           | N    | 可能希望与单个实体有多个关系，所以此属性允许您指定一个标题，该标题将附加到 rel-entity-name 以构成关系的名称。如果未指定，则仅将 rel-entity-name 用作关系名称 |
  | fk-name         | N    | 外键名称可以从关系名称自动创建，但不建议这样做，原因有两个：许多数据库的外键和索引名称的最大大小非常小（如 18 个字符），并且许多数据库要求外键名称对于整个数据库来说是唯一的，而不仅仅是 FK 来自的表 |

  | 子元素名 | 数量 | 描述                                                         |
  | -------- | ---- | ------------------------------------------------------------ |
  | key-map  | >=1  | 键映射用于指定该实体中的一个字段，该字段对应于相关实体中的一个字段。该元素有两个属性：field-name 和 rel-field-name。这些用于指定该实体上的字段名称以及相关实体上的字段的相应名称 |

## 六、视图实体模型

## 七、实体引擎API

* org.apache.ofbiz.entity中定义了和实体引擎实体数据交互的API
* 对用户来说只需要理解3个类
  * GenericDelegator：经常以实例名"delegator"使用，主要用来对GenericValue对象进行创建、查询、存储和其他操作
  * GenericValue：一旦GenericValue对象呗创建，它就会包含一个创建它的delegator的引用。通过这个引用就可以知道是如何存储、删除并且执行其他操作的，而无需程序调用委托者本身的方法
  * GenericPK
* 应该使用GenericDelegator的makeValue和makePK方法来构造GenericValue和GenericPK，这些创建一个对象而不持久化它，并允许您添加到它，稍后使用它们自己的 create 或 store 方法创建或存储它，或者在delegator对象上调用 create 或 store 方法
* Creating, Storing and Removing
  * 向数据库中创建（插入）数据，要使用GenericValue或GenericDelegator对象的create方法
  * 向数据库中保存（更新）数据，要使用GenericValue或GenericDelegator对象的store方法
  * GenericDelegator有一个storeAll方法用于批量存储多个实体，这个方法持有多个GenericValue实例并且在一个事务中存储它们。实际上，说它存储它们是不正确的。它检查它们是否存在，如果存在则更新，如果不存在则插入。通过允许您根据对该实体存在的先验知识指定是否应该插入或更新，这可能会在未来进行优化以提高速度。请注意，这是与 store 方法不同的行为，后者只是进行更新
  * 通过delegator或者GenericValue的remove方法删除实体
* Finding
  * 可以使用 findByPrimaryKey 方法从数据库中检索值实例，或者可以使用 findAll 或 findByAnd 方法检索集合